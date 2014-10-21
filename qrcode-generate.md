Title: 论二维码的原理及生成
Date: 2014-02-17 16:56
Author: neoyin
Category: 技术流
Tags: QRcode, 二维码
Slug: qrcode-generate

<div style="display: none;">
[hollister t shirt](http://www.daydayask.com/hollister-pas-cher/)

</div>
</p>
大家肯定都用过微信中的扫一扫功能。我们先来全面了解一下二维码

二维码的起源
------------

二维码的起源是日本，原本是Denso
Wave公司为了追踪汽车零部件而设计的一种条码.今日QR码已广泛使用在各行各业.

二维码的特点
------------

1.  **存储信息量**
    传统的条形码只能处理20位左右的信息量，与此相比，QR码可处理条形码的几十倍到几百倍的信息量。另外，QR码还可以支持所有类型的数据。（如：数字、英文字母、日文字母、汉字、符号、二进制、控制码等）。一个QR码最多可以处理7089字(仅用数字时)的巨大信息量。
2.  **在小空间内打印**
    QR码使用纵向和横向两个方向处理数据，如果是相同的信息量，QR码所占空间为条形码的十分之一左右。(还支持Micro
    QR码，可以在更小空间内处理数据。)
3.  **有效的表现各种字母**
    QR码是日本国产的二维码，因此非常适合处理日文字母和汉字。QR码字集规格定义是按照日本标准“JIS第一级和第二级的汉字”制定的，因此在日语处理方面，每一个全角字母和汉字都用13比特的数据处理，效率较高，与其他二维码相比，可以多存储20%以上的信息。
4.  **纠错功能**
    QR码具备“纠错功能”，即使部分编码变脏或破损，也可以恢复数据。数据恢复以码字为单位※1，最多可以纠错约30%※2。
5.  **360度任一方向读取**
    QR码从360°任一方向均可快速读取。其奥秘就在于QR码中的3处定位图案，可以帮助QR码不受背景样式的影响，实现快速稳定的读取。
6.  **支持数据合并功能**
    QR码可以将数据分割为多个编码，最多支持16个QR码。使用这一功能，还可以在狭长区域内打印QR码。另外，也可以把多个分割编码合并为单个数据。

![二维码](http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=66197a630b24ab18e416e13205c197f0/d1160924ab18972be0789f8fe4cd7b899e510a30.jpg?referer=7eef5c76718da9771738b21b76ef&x=.jpg)

-   在上图21\*21的矩阵中，黑白的区域在QR码规范中被指定为固定的位置，称为寻像图形（finder
    pattern）和定位图形(timing
    pattern)。寻像图形和定位图形用来帮助解码程序确定图形中具体符号的坐标。
-   黄色的区域用来保存被编码的数据内容以及纠错信息码。
-   蓝色的区域，用来标识纠错的级别（也就是Level L到Level H)和所谓的"Mask
    pattern",这个区域被称为“格式化信息”（format information）。

<!--more-->

二维码的生成原理
----------------

酷壳中的一篇文章写得不错，可以去看一下:[http://coolshell.cn/articles/10590.html](http://coolshell.cn/articles/10590.html)

JAVA生成二维码
--------------

JAVA中的开源类库([Zxing](https://github.com/zxing/zxing))用于解析多种格式的条形码和二维码.
Maven中可以引用

    <dependency>
      <groupId>com.google.zxing</groupId>
      <artifactId>javase</artifactId>
      <version>2.2</version>
    </dependency>



    public class QRcodeTest {

        public static void main(String[] args) {
            String myCodeText = "http://www.l99.com/media";
            String filePath = "/home/neoyin/temp/bak/test.png";
            int size = 125;
            String fileType = "png";
            File myFile = new File(filePath);
            try {
                Hashtable<EncodeHintType, ErrorCorrectionLevel> hintMap = new Hashtable<EncodeHintType, ErrorCorrectionLevel>();
                hintMap.put(EncodeHintType.ERROR_CORRECTION, ErrorCorrectionLevel.L);
                QRCodeWriter qrCodeWriter = new QRCodeWriter();
                BitMatrix byteMatrix = qrCodeWriter.encode(myCodeText,BarcodeFormat.QR_CODE, size, size, hintMap);
                int CrunchifyWidth = byteMatrix.getWidth();
                BufferedImage image = new BufferedImage(CrunchifyWidth, CrunchifyWidth,
                        BufferedImage.TYPE_INT_RGB);
                image.createGraphics();

                Graphics2D graphics = (Graphics2D) image.getGraphics();
                graphics.setColor(Color.WHITE);
                graphics.fillRect(0, 0, CrunchifyWidth, CrunchifyWidth);
                graphics.setColor(Color.BLACK);

                for (int i = 0; i < CrunchifyWidth; i++) {
                    for (int j = 0; j < CrunchifyWidth; j++) {
                        if (byteMatrix.get(i, j)) {
                            graphics.fillRect(i, j, 1, 1);
                        }
                    }
                }
                ImageIO.write(image, fileType, myFile);
            } catch (WriterException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }
            System.out.println("\n\nYou have successfully created QR Code.");
        }    
    }

[二维码百度百科](http://www.baike.com/wiki/%E4%BA%8C%E7%BB%B4%E7%A0%81)

[二维码wiki](http://zh.wikipedia.org/wiki/QR%E7%A0%81)

[为程序员写的Reed-Solomon码解释](http://www.felix021.com/blog/read.php?2116)
