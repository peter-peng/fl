Title: 打造分布式文件系统--Java排序算法复习
Date: 2013-11-04 16:26
Author: neoyin
Category: 技术流
Tags: hadoop
Slug: hadoop-sort

我们知道Hadoop中关键点在[HDFS](http://www.floatinglife.cn/hadoop-hdfs)
和 [Mapreduce](http://www.floatinglife.cn/hadoop-mapreduce)
,也通过Hadoop中简单实例[WordCount](http://www.floatinglife.cn/hadoop-mapreduce)
了解其基本流程.

我们需要再深入的了解一下Hadoop中Mapreduce的核心过程,其最主要的核心是Shuffle和Sort.这一章我们还是先复习一下Java的排序算法.

排序是程序开发中一种非常常见的操作，对一组任意的数据元素（或记录）经过排序操作后，就可以把他们变成一组按关键字排序的有序队列。

对一个排序算法来说，一般从下面3个方面来衡量算法的优劣：

1.  时间复杂度：它主要是分析关键字的比较次数和记录的移动次数。
2.  空间复杂度：分析排序算法中需要多少辅助内存。
3.  稳定性：若两个记录A和B的关键字值相等，但是排序后A,B的先后次序保持不变，则称这种排序算法是稳定的；反之，就是不稳定的。

就现有的排序算法来看，排序大致可分为内部排序和外部排序。如果整个排序过程不需要借助外部存储器（如磁盘等），所有排序操作都是在内存中完成，这种排序就被称为内部排序。

如果参与排序的数据元素非常多，数据量非常大，计算无法把整个排序过程放在内存中完成，必须借助于外部存储器（如磁盘），这种排序就被称为外部排序。

外部排序最常用算噶是多路归并排序，即将原文件分解称多个能够一次性装入内存的部分，分别把每一部分调入内存完成排序，接下来再对多个有序的子文件进行归并排序。

就常用的内部排序算法来说，可以分为以下几类：

-   选择排序（直接选择排序，堆排序）
-   交换排序（冒泡排序，快速排序）
-   插入排序（直接插入排序，折半插入排序，Shell排序）
-   归并排序
-   桶式排序
-   基数排序

<!--more-->

**直接选择排序**

直接选择排序的基本操作就是每一趟从待排序的数据元素中选出最小（或最大）的一个元素，顺序放在已排好序的数列的最后，直到全部待排序的数据元素排完，它需要经过n-1趟比较。算法不稳定，O(1)的额外的空间，比较的时间复杂度为O(n\^2)，交换的时间复杂度为O(n)，并不是自适应的。在大多数情况下都不推荐使用。只有在希望减少交换次数的情况下可以用。

基本思想

n个记录的文件的直接选择排序可经过n-1趟直接选择排序得到有序结果：

①初始状态：无序区为R[1..n]，有序区为空。

②第1趟排序

在无序区R[1..n]中选出关键字最小的记录R[k]，将它与无序区的第1个记录R[1]交换，使R[1..1]和R[2..n]分别变为记录个数增加1个的新有序区和记录个数减少1个的新无序区。

……

③第i趟排序

第i趟排序开始时，当前有序区和无序区分别为R[1..i-1]和R(1≤i≤n-1)。该趟排序从当前无序区中选出关键字最小的记录
R[k]，将它与无序区的第1个记录R交换，使R[1..i]和R分别变为记录个数增加1个的新有序区和记录个数减少1个的新无序区。

这样，n个记录的文件的直接选择排序可经过n-1趟直接选择排序得到有序结果。

    public class SelectSortTest {

        public static void main(String[] args) {
            int[] data = new int[] { 5, 3, 6, 2, 1, 9, 4, 8, 7 };
            print(data);
            selectSort(data);
            print(data);
        }

        public static void swap(int[] data, int i, int j) {
            if (i == j) {
                return;
            }
                    int temp = data[i];
                                        data[i] = data[j];
                                        data[j] = temp;
            //data[i] = data[i] + data[j];
            //data[j] = data[i] - data[j];
            //data[i] = data[i] - data[j];
        }

        public static void selectSort(int[] data) {
            for (int i = 0; i < data.length - 1; i++) {
                int minIndex = i; // 记录最小值的索引
                for (int j = i + 1; j < data.length; j++) {
                    if (data[j] < data[minIndex]) {
                        minIndex = j; // 若后面的元素值小于最小值,将j赋值给minIndex
                    }
                }
                if (minIndex != i) {
                    swap(data, i, minIndex);
                    print(data);
                }
            }
        }

        public static void print(int[] data) {
            for (int i = 0; i < data.length; i++) {
                System.out.print(data[i] + "\t");
            }
            System.out.println();
        }
    }

**堆排序**

> **堆积排序**(Heapsort)是指利用堆积树（堆）这种资料结构所设计的一种排序算法，可以利用数组的特点快速定位指定索引的元素。堆排序是不稳定的排序方法，辅助空间为O(1)，
> 最坏时间复杂度为O(nlog2n) ，堆排序的堆序的平均性能较接近于最坏性能。

堆排序利用了大根堆(或小根堆)堆顶记录的关键字最大(或最小)这一特征，使得在当前无序区中选取最大(或最小)关键字的记录变得简单。

**![image](http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=354d15dbad51f3dec7b2b961a4d58122/fcfaaf51f3deb48f240f7ccff21f3a292df578b9.jpg?referer=9815cd3e97cad1c889acc917b857&x=.jpg)**

（1）用大根堆排序的基本思想

① 先将初始文件R[1..n]建成一个大根堆,此堆为初始的无序区

②
再将关键字最大的记录R[1](即堆顶)和无序区的最后一个记录R[n]交换，由此得到新的无序区R[1..n-1]和有序区R[n]，且满足R[1..n-1].keys≤R[n].key

③由于交换后新的根R[1]可能违反堆性质，故应将当前无序区R[1..n-1]调整为堆。然后再次将R[1..n-1]中关键字最大的记录R[1]和该区间的最后一个记录R[n-1]交换，由此得到新的无序区R[1..n-2]和有序区R[n-1..n]，且仍满足关系R[1..n-2].keys≤R[n-1..n].keys，同样要将R[1..n-2]调整为堆。

……

直到无序区只有一个元素为止。

（2）大根堆排序算法的基本操作：

① 初始化操作：将R[1..n]构造为初始堆；

②
每一趟排序的基本操作：将当前无序区的堆顶记录R[1]和该区间的最后一个记录交换，然后将新的无序区调整为堆(亦称重建堆)。

注意：

①只需做n-1趟排序，选出较大的n-1个关键字即可以使得文件递增有序。

②用小根堆排序与利用大根堆类似，只不过其排序结果是递减有序的。堆排序和直接选择排序相反：在任何时刻堆排序中无序区总是在有序区之前，且有序区是在原向量的尾部由后往前逐步扩大至整个向量为止。

    public class HeapSortTest {

        public static void main(String[] args) {
            int[] data5 = new int[] { 5, 3, 6, 2, 1, 9, 4, 8, 7 };
            print(data5);
            heapSort(data5);
            System.out.println("排序后的数组：");
            print(data5);
        }

        public static void swap(int[] data, int i, int j) {
            if (i == j) {
                return;
            }
            data[i] = data[i] + data[j];
            data[j] = data[i] - data[j];
            data[i] = data[i] - data[j];
        }

        public static void heapSort(int[] data) {
            for (int i = 0; i < data.length; i++) {          createMaxdHeap(data, data.length - 1 - i);          swap(data, 0, data.length - 1 - i);             print(data);        }   }   public static void createMaxdHeap(int[] data, int lastIndex) {      for (int i = (lastIndex - 1) / 2; i >= 0; i--) {
                // 保存当前正在判断的节点
                int k = i;
                // 若当前节点的子节点存在
                while (2 * k + 1 <= lastIndex) {
                    // biggerIndex总是记录较大节点的值,先赋值为当前判断节点的左子节点
                    int biggerIndex = 2 * k + 1;
                    if (biggerIndex < lastIndex) {
                        // 若右子节点存在，否则此时biggerIndex应该等于 lastIndex
                        if (data[biggerIndex] < data[biggerIndex + 1]) {
                            // 若右子节点值比左子节点值大，则biggerIndex记录的是右子节点的值
                            biggerIndex++;
                        }
                    }
                    if (data[k] < data[biggerIndex]) {
                        // 若当前节点值比子节点最大值小，则交换2者得值，交换后将biggerIndex值赋值给k
                        swap(data, k, biggerIndex);
                        k = biggerIndex;
                    } else {
                        break;
                    }
                }
            }
        }

        public static void print(int[] data) {
            for (int i = 0; i < data.length; i++) {
                System.out.print(data[i] + "\t");
            }
            System.out.println();
        }

    }     ---

---

[http://www.cnblogs.com/kemaswill/archive/2012/10/05/2712080.html](http://www.cnblogs.com/kemaswill/archive/2012/10/05/2712080.html)

[http://blog.csdn.net/apei830/article/details/6584645](http://blog.csdn.net/apei830/article/details/6584645)

[http://bbs.csdn.net/topics/360221833](http://bbs.csdn.net/topics/360221833)

**冒泡排序**

**冒泡排序**是计算机的一种排序方法，它的时间复杂度为O（n\^2），虽然不及堆排序、快速排序的O（nlogn，底数为2），但是有两个优点：

1.“编程复杂度”很低，很容易写出代码；

2.具有稳定性，这里的稳定性是指原序列中相同元素的相对顺序仍然保持到排序后的序列，而堆排序、快速排序均不具有稳定性。

不过，一路、二路归并排序、不平衡二叉树排序的速度均比冒泡排序快，且具有稳定性，但速度不及堆排序、快速排序。冒泡排序是经过n-1趟子排序完成的，第i趟子排序从第1个数至第n-i个数，若第i个数比后一个数大（则升序，小则降序）则交换两数。

冒泡排序算法稳定，O(1)的额外的空间，比较和交换的时间复杂度都是O(n\^2)，自适应，对于已基本排序的算法，时间复杂度为O(n)。冒泡算法的许多性质和插入算法相似，但对于系统开销高一点点。

排序过程

设想被排序的数组R［1..N］垂直竖立，将每个数据元素看作有重量的气泡，根据轻气泡不能在重气泡之下的原则，从下往上扫描数组R，凡扫描到违反本原则的轻气泡，就使其向上"漂浮"，如此反复进行，直至最后任何两个气泡都是轻者在上，重者在下为止。

    public class BubbleSortTest {

        public static void main(String[] args) {
            int[] data5 = new int[] { 5, 3, 6, 2, 1, 9, 4, 8, 7};
            print(data5);
            bubbleSort(data5);
            System.out.println("排序后的数组：");
            print(data5);
        }

        public static void swap(int[] data, int i, int j) {
            if (i == j) {
                return;
            }
            data[i] = data[i] + data[j];
            data[j] = data[i] - data[j];
            data[i] = data[i] - data[j];
        }

        public static void bubbleSort(int[] data) {
            for (int i = 0; i < data.length - 1; i++) {
                // 记录某趟是否发生交换，若为false表示数组已处于有序状态
                boolean isSorted = false;
                for (int j = 0; j < data.length - i - 1; j++) {              if (data[j] > data[j + 1]) {
                        swap(data, j, j + 1);
                        isSorted = true;
                        print(data);
                    }
                }
                if (!isSorted) {
                    // 若数组已处于有序状态，结束循环
                    break;
                }
            }
        }

        public static void print(int[] data) {
            for (int i = 0; i < data.length; i++) {
                System.out.print(data[i] + "\t");
            }
            System.out.println();
        }

    }

**快速排序**

快速排序是一个速度非常快的交换排序算法，它的基本思路很简单，从待排的数据序列中任取一个数据（如第一个数据）作为分界值，所有比它小的数据元素放到左边，所有比它大的数据元素放到它的右边。经过这样一趟下来，该序列形成左右两个子序列，左边序列中的数据元素的值都比分界值小，右边序列中数据元素的值都比分界值大。  

接下来对左右两个子序列进行递归排序，对两个子序列重新选择中心元素并依此规则调整，直到每个元素子表的元素只剩下一个元素，排序完成。

思路：  

1.定义一个i变量，i变量从左边第一个索引开始，找大于分界值的元素的索引，并用i来记录它。  

2.定义一个j变量，j变量从右边第一个索引开始，找小于分界值的元素的索引，并用j来记录它。  
3.如果i<j，交换i,j两个索引处的元素。  

重复执行以上1,2,3步，直到i\>=j，可以判断j左边的数据元素都小于分界值，j右边的数据元素都大于分界值，最后将分界值和j索引处的元素交换即可。

时间复杂度  
最好情况（每次总是选到中间值作枢轴）T(n)=O(nlogn)  
最坏情况（每次总是选到最小或最大元素作枢轴）  
做n-1趟，每趟比较n-i次，总的比较次数最大：[O(n²)]  
平均时间复杂度为：：T(n)=O(nlogn)

    package sort;

    public class QuickSortTest {

        public static void main(String[] args) {
            int[] data = new int[] { 5, 3, 6, 2, 1, 9, 4, 8, 7 };
            print(data);
            quickSort(data, 0, data.length - 1);
            System.out.println("排序后的数组：");
            print(data);
        }

        public static void swap(int[] data, int i, int j) {
            if (i == j) {
                return;
            }
            data[i] = data[i] + data[j];
            data[j] = data[i] - data[j];
            data[i] = data[i] - data[j];
        }

        public static void quickSort(int[] data, int start, int end) {
            if (start >= end)
                return;
            //以起始索引为分界点
            int pivot = data[start];
            int i = start + 1;
            int j = end;
            while (true) {
                while (i <= end && data[i] <= pivot) {                i++;            }           while (j > start && data[j] >= pivot) {
                    j--;
                }
                if (i < j) {
                    swap(data, i, j);
                } else {
                    break;
                }
            }
            //交换 j和分界点的值
            swap(data, start, j);
            print(data);
            //递归左子序列
            quickSort(data, start, j - 1);
            //递归右子序列
            quickSort(data, j + 1, end);
        }

        public static void print(int[] data) {
            for (int i = 0; i < data.length; i++) {
                System.out.print(data[i] + "\t");
            }
            System.out.println();
        }

    }

[http://baike.baidu.com/view/19016.htm](http://baike.baidu.com/view/19016.htm)

**直接插入排序**

**直接插入排序**的基本操作就是将待排序的数据元素按其关键字值的大小插入到前面的有序序列中。

直接插入的时间效率并不高，如果在最坏的情况下，所有元素的比较次数总和为（0+1+...+n-1）=O(n\^2)。其他情况下也要考虑移动元素的次数，故时间复杂度为O(n\^2)

直接插入空间效率很好，只需要1个缓存数据单元，也就是说空间复杂度为O(1).

直接插入排序是稳定的。

直接插入排序在数据已有一定顺序的情况下，效率较好。但如果数据无规则，则需要移动大量的数据，其效率就与冒泡排序法和选择排序法一样差了。

算法描述

对一个有n个元素的数据序列，排序需要进行n-1趟插入操作：

第1趟插入，将第2个元素插入前面的有序子序列--此时前面只有一个元素，当然是有序的。

第2趟插入，将第3个元素插入前面的有序子序列，前面2个元素是有序的。

第n-1趟插入，将第n个元素插入前面的有序子序列，前面n-1个元素是有序的。

    package sort;

    public class InsertSortTest {
        public static int count = 0;

        public static void main(String[] args) {

            int[] data = new int[] { 5, 3, 6, 2, 1, 9, 4, 8, 7 };
            print(data);
            insertSort(data);
            print(data);

        }

        public static void insertSort(int[] data) {
            for (int i = 1; i < data.length; i++) {
                // 缓存i处的元素值
                int tmp = data[i];
                if (data[i] < data[i - 1]) {                 int j = i - 1;              // 整体后移一格               while (j >= 0 && data[j] > tmp) {
                        data[j + 1] = data[j];
                        j--;
                    }
                    // 最后将tmp插入合适的位置
                    data[j + 1] = tmp;
                    print(data);
                }
            }
        }

        public static void print(int[] data) {
            for (int i = 0; i < data.length; i++) {
                System.out.print(data[i] + "\t");
            }
            System.out.println();
        }

    }

**折半插入排序**

折半插入排序法，又称二分插入排序法，是直接插入排序法的改良版，也需要执行i-1趟插入，不同之处在于，第i趟插入，先找出第i+1个元素应该插入的的位置，假定前i个数据是已经处于有序状态。

    package sort;

    public class BinaryInsertSortTest {
        public static int count = 0;

        public static void main(String[] args) {

            int[] data = new int[] { 5, 3, 6, 2, 1, 9, 4, 8, 7 };
            print(data);
            binaryInsertSort(data);
            print(data);

        }

        public static void binaryInsertSort(int[] data) {
            for (int i = 1; i < data.length; i++) {
                if (data[i] < data[i - 1]) {
                    // 缓存i处的元素值
                    int tmp = data[i];
                    // 记录搜索范围的左边界
                    int low = 0;
                    // 记录搜索范围的右边界
                    int high = i - 1;
                    while (low <= high) {
                        // 记录中间位置
                        int mid = (low + high) / 2;
                        // 比较中间位置数据和i处数据大小，以缩小搜索范围
                        if (data[mid] < tmp) {                       low = mid + 1;                  } else {                        high = mid - 1;                     }               }               //将low~i处数据整体向后移动1位                 for (int j = i; j > low; j--) {
                        data[j] = data[j - 1];
                    }
                    data[low] = tmp;
                    print(data);
                }
            }
        }

        public static void print(int[] data) {
            for (int i = 0; i < data.length; i++) {
                System.out.print(data[i] + "\t");
            }
            System.out.println();
        }

    }

**希尔排序(Shell排序)**

**希尔排序**(缩小增量法)
属于插入类排序,由Shell提出，希尔排序对直接插入排序进行了简单的改进：它通过加大插入排序中元素之间的间隔，并在这些有间隔的元素中进行插入排序，从而使数据项大跨度地移动，当这些数据项排过一趟序之后，希尔排序算法减小数据项的间隔再进行排序，依次进行下去，进行这些排序时的数据项之间的间隔被称为增量，习惯上用字母h来表示这个增量。

常用的h序列由Knuth提出，该序列从1开始，通过如下公式产生：

h = 3 \* h +1

反过来程序需要反向计算h序列，应该使用

h=(h-1)/3

    package sort;

    public class ShellSortTest {
        public static int count = 0;

        public static void main(String[] args) {

            int[] data = new int[] { 5, 3, 6, 2, 1, 9, 4, 8, 7 };
            print(data);
            shellSort(data);
            print(data);

        }

        public static void shellSort(int[] data) {
            // 计算出最大的h值
            int h = 1;
            while (h <= data.length / 3) {           h = h * 3 + 1;      }       while (h > 0) {
                for (int i = h; i < data.length; i += h) {
                    if (data[i] < data[i - h]) {                     int tmp = data[i];                  int j = i - h;                  while (j >= 0 && data[j] > tmp) {
                            data[j + h] = data[j];
                            j -= h;
                        }
                        data[j + h] = tmp;
                        print(data);
                    }
                }
                // 计算出下一个h值
                h = (h - 1) / 3;
            }
        }

        public static void print(int[] data) {
            for (int i = 0; i < data.length; i++) {
                System.out.print(data[i] + "\t");
            }
            System.out.println();
        }

    }

**归并排序**

**归并排序**（Merge）是将两个（或两个以上）有序表合并成一个新的有序表，即把待排序序列分为若干个子序列，每个子序列是有序的。然后再把有序子序列合并为整体有序序列。

归并排序是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide
and Conquer）的一个非常典型的应用。
将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为2-路归并。

归并排序算法稳定，数组需要O(n)的额外空间，链表需要O(log(n))的额外空间，时间复杂度为O(nlog(n))，算法不是自适应的，不需要对数据的随机读取。

**工作原理：**

1、申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列

2、设定两个指针，最初位置分别为两个已经排序序列的起始位置

3、比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置

4、重复步骤3直到某一指针达到序列尾

5、将另一序列剩下的所有元素直接复制到合并序列尾

    public class MergeSortTest {

        public static void main(String[] args) {
            int[] data = new int[] { 5, 3, 6, 2, 1, 9, 4, 8, 7 };
            print(data);
            mergeSort(data);
            System.out.println("排序后的数组：");
            print(data);
        }

        public static void mergeSort(int[] data) {
            sort(data, 0, data.length - 1);
        }

        public static void sort(int[] data, int left, int right) {
            if (left >= right)
                return;
            // 找出中间索引
            int center = (left + right) / 2;
            // 对左边数组进行递归
            sort(data, left, center);
            // 对右边数组进行递归
            sort(data, center + 1, right);
            // 合并
            merge(data, left, center, right);
            print(data);
        }

        /**
         * 将两个数组进行归并，归并前面2个数组已有序，归并后依然有序
         * 
         * @param data
         *            数组对象
         * @param left
         *            左数组的第一个元素的索引
         * @param center
         *            左数组的最后一个元素的索引，center+1是右数组第一个元素的索引
         * @param right
         *            右数组最后一个元素的索引
         */
        public static void merge(int[] data, int left, int center, int right) {
            // 临时数组
            int[] tmpArr = new int[data.length];
            // 右数组第一个元素索引
            int mid = center + 1;
            // third 记录临时数组的索引
            int third = left;
            // 缓存左数组第一个元素的索引
            int tmp = left;
            while (left <= center && mid <= right) {
                // 从两个数组中取出最小的放入临时数组
                if (data[left] <= data[mid]) {
                    tmpArr[third++] = data[left++];
                } else {
                    tmpArr[third++] = data[mid++];
                }
            }
            // 剩余部分依次放入临时数组（实际上两个while只会执行其中一个）
            while (mid <= right) {
                tmpArr[third++] = data[mid++];
            }
            while (left <= center) {
                tmpArr[third++] = data[left++];
            }
            // 将临时数组中的内容拷贝回原数组中
            // （原left-right范围的内容被复制回原数组）
            while (tmp <= right) {
                data[tmp] = tmpArr[tmp++];
            }
        }

        public static void print(int[] data) {
            for (int i = 0; i < data.length; i++) {
                System.out.print(data[i] + "\t");
            }
            System.out.println();
        }

    }

**桶式排序**

桶式排序不再是一种基于比较的排序方法，它是一种比较巧妙的排序方式，但这种排序方式需要待排序的序列满足以下两个特征：

待排序列所有的值处于一个可枚举的范围之类；

待排序列所在的这个可枚举的范围不应该太大，否则排序开销太大。

排序的具体步骤如下：

（1）对于这个可枚举范围构建一个buckets数组，用于记录“落入”每个桶中元素的个数；

（2）将（1）中得到的buckets数组重新进行计算，按如下公式重新计算：

buckets[i] = buckets[i] +buckets[i-1] (其中1<=i<buckets.length);

桶式排序是一种非常优秀的排序算法，时间效率极高，它只要通过2轮遍历：第1轮遍历待排数据，统计每个待排数据“落入”各桶中的个数，第2轮遍历buckets用于重新计算buckets中元素的值，2轮遍历后就可以得到每个待排数据在有序序列中的位置，然后将各个数据项依次放入指定位置即可。

桶式排序的空间开销较大，它需要两个数组，第1个buckets数组用于记录“落入”各桶中元素的个数，进而保存各元素在有序序列中的位置，第2个数组用于缓存待排数据。

桶式排序是稳定的。

如果待排序数据的范围在0\~k之间，那么它的时间复杂度是O(k+n)的

桶式排序算法速度很快，因为它的时间复杂度是O(k+n)，而基于交换的排序时间上限是n*lg*
n。

但是它的限制多，比如它只能排整形数组。而且当k较大，而数组长度n较小，即k\>\>n时，辅助数组C[k+1]的空间消耗较大。

当数组为整形，且k和n接近时,
可以用此方法排序。（有的文章也称这种排序算法为“计数排序”）

    public class BucketSortTest {
        public static int count = 0;

        public static void main(String[] args) {

            int[] data = new int[] { 5, 3, 6, 2, 1, 9, 4, 8, 7 };
            print(data);
            bucketSort(data, 0, 10);
            print(data);

        }

        public static void bucketSort(int[] data, int min, int max) {
            // 缓存数组
            int[] tmp = new int[data.length];
            // buckets用于记录待排序元素的信息
            // buckets数组定义了max-min个桶
            int[] buckets = new int[max - min];
            // 计算每个元素在序列出现的次数
            for (int i = 0; i < data.length; i++) {
                buckets[data[i] - min]++;
            }
            // 计算“落入”各桶内的元素在有序序列中的位置
            for (int i = 1; i < max - min; i++) {            buckets[i] = buckets[i] + buckets[i - 1];       }       // 将data中的元素完全复制到tmp数组中         System.arraycopy(data, 0, tmp, 0, data.length);         // 根据buckets数组中的信息将待排序列的各元素放入相应位置       for (int k = data.length - 1; k >= 0; k--) {
                data[--buckets[tmp[k] - min]] = tmp[k];
            }
        }

        public static void print(int[] data) {
            for (int i = 0; i < data.length; i++) {
                System.out.print(data[i] + "\t");
            }
            System.out.println();
        }

    }

**基数排序**

基数排序已经不再是一种常规的排序方式，它更多地像一种排序方法的应用，基数排序必须依赖于另外的排序方法。基数排序的总体思路就是将待排序数据拆分成多个关键字进行排序，也就是说，基数排序的实质是多关键字排序。

多关键字排序的思路是将待排数据里德排序关键字拆分成多个排序关键字；第1个排序关键字，第2个排序关键字，第3个排序关键字......然后，根据子关键字对待排序数据进行排序。

多关键字排序时有两种解决方案：

最高位优先法（MSD）(Most Significant Digit first)

最低位优先法（LSD）(Least Significant Digit first)

例如，对如下数据序列进行排序。

192,221,12,23

可以观察到它的每个数据至多只有3位，因此可以将每个数据拆分成3个关键字：百位（高位）、十位、个位（低位）。

如果按照习惯思维，会先比较百位，百位大的数据大，百位相同的再比较十位，十位大的数据大；最后再比较个位。人得习惯思维是最高位优先方式。

如果按照人得思维方式，计算机实现起来有一定的困难，当开始比较十位时，程序还需要判断它们的百位是否相同--这就认为地增加了难度，计算机通常会选择最低位优先法。

基数排序方法对任一子关键字排序时必须借助于另一种排序方法，而且这种排序方法必须是稳定的。

对于多关键字拆分出来的子关键字，它们一定位于0-9这个可枚举的范围内，这个范围不大，因此用桶式排序效率非常好。

对于多关键字排序来说，程序将待排数据拆分成多个子关键字后，对子关键字排序既可以使用桶式排序，也可以使用任何一种稳定的排序方法。

    import java.util.Arrays;

    public class MultiKeyRadixSortTest {

        public static void main(String[] args) {
            int[] data = new int[] { 1100, 192, 221, 12, 23 };
            print(data);
            radixSort(data, 10, 4);
            System.out.println("排序后的数组：");
            print(data);
        }

        public static void radixSort(int[] data, int radix, int d) {
            // 缓存数组
            int[] tmp = new int[data.length];
            // buckets用于记录待排序元素的信息
            // buckets数组定义了max-min个桶
            int[] buckets = new int[radix];

            for (int i = 0, rate = 1; i < d; i++) {

                // 重置count数组，开始统计下一个关键字
                Arrays.fill(buckets, 0);
                // 将data中的元素完全复制到tmp数组中
                System.arraycopy(data, 0, tmp, 0, data.length);

                // 计算每个待排序数据的子关键字
                for (int j = 0; j < data.length; j++) {
                    int subKey = (tmp[j] / rate) % radix;
                    buckets[subKey]++;
                }

                for (int j = 1; j < radix; j++) {                buckets[j] = buckets[j] + buckets[j - 1];           }           // 按子关键字对指定的数据进行排序          for (int m = data.length - 1; m >= 0; m--) {
                    int subKey = (tmp[m] / rate) % radix;
                    data[--buckets[subKey]] = tmp[m];
                }
                rate *= radix;
            }

        }

        public static void print(int[] data) {
            for (int i = 0; i < data.length; i++) {
                System.out.print(data[i] + "\t");
            }
            System.out.println();
        }

    }


