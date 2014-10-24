Title: android中的ListView在超过屏幕后，会使选择数据混乱的解决方法
Date: 2013-12-01 00:06
Author: fuqiang
Category: 技术流
Tags: android
Slug: android-listview-issue

看了一下几个月以前写的一个android应用，后来捣鼓除了这个解决ListView的选择数据混乱的问题，由于sdk出了些问题，因此无法运行程序贴出图片，只能贴上代码，以后再待用了。

    package com.asms.adapter;

    import java.util.ArrayList;
    import java.util.HashMap;

    import android.app.Activity;
    import android.app.Dialog;
    import android.content.ContentValues;
    import android.content.Context;
    import android.content.DialogInterface;
    import android.content.Intent;
    import android.util.Log;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.View.OnClickListener;
    import android.view.ViewGroup;
    import android.widget.BaseAdapter;
    import android.widget.CheckBox;
    import android.widget.CompoundButton;
    import android.widget.CompoundButton.OnCheckedChangeListener;
    import android.widget.RadioButton;
    import android.widget.TextView;

    import com.asms.MainActivity;
    import com.asms.R;
    import com.asms.activity.SettingTargetEmailActivity;
    import com.asms.activity.UpdateTargetEmailActivity;
    import com.asms.db.DBManager;
    import com.asms.dialog.SelfSettingDialog;
    import com.asms.utils.Constant;

    /**
    * ListView的自定义布局类
    * @author Administrator
    *
    */
    public class ListViewAdapter extends BaseAdapter{

    public static final int e_id = 1000;
    public static final int c_id = 100;
    private Context context = null;
    private ArrayList<ContentValues> cvs = null;
    private LayoutInflater layoutInflater = null;
    private HashMap<Integer, Boolean> statusMap = new HashMap<Integer, Boolean>();
    private HashMap<Integer, Boolean> removeMap = new HashMap<Integer, Boolean>();
    private View convertView = null;
    public ListViewAdapter(Context context, ArrayList<ContentValues> cvs) {
    this.context = context;
    this.cvs = cvs;
    this.layoutInflater = LayoutInflater.from(context);
    }

    @Override
    public int getCount() {
    return cvs.size();
    }

    @Override
    public Object getItem(int position) {
    return cvs.get(position);
    }

    @Override
    public long getItemId(int position) {
    return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
    ViewHolder vh = null;
    if(convertView == null) {
    Log.v("ListViewAdapter", ""+position);
    vh = new ViewHolder();
    convertView = layoutInflater.inflate(R.layout.listviewadapter, null);
    vh.emailShow = (TextView) convertView.findViewById(R.id.emailShowView);
    vh.statusShow = (RadioButton) convertView.findViewById(R.id.changeStatus);

    vh.removeCb = (CheckBox) convertView.findViewById(R.id.removeEmailChk);

    convertView.setTag(vh);
    } else {
    vh = (ViewHolder) convertView.getTag();
    }
    //给选择框添加事件
    vh.emailShow.setOnClickListener(new EmailShowListener());
    vh.emailShow.setId(e_id+position); //必须具有不同的id，不然会被覆盖掉

    vh.statusShow.setId(position);
    vh.removeCb.setId(c_id + position);
    vh.statusShow.setOnCheckedChangeListener(new RadioGroupListener());
    vh.removeCb.setOnCheckedChangeListener(new CheckBoxListener());

    String email = cvs.get(position).getAsString("email");
    if(email.length() > 20) {
    email = email.substring(0, 20) + "...";
    }
    vh.emailShow.setText(email);
    int status = cvs.get(position).getAsInteger("status").intValue();
    //在每一次下拉列表时，都会执行下面的判断
    if((status == 0 && statusMap.size() <= 0) || statusMap.containsKey(position)) {
    statusMap.put(position, true);
    vh.statusShow.setChecked(true);
    } else {
    vh.statusShow.setChecked(false);
    }
    if(removeMap.containsKey(c_id + position)) {
    vh.removeCb.setChecked(removeMap.get(c_id + position));
    } else {
    vh.removeCb.setChecked(false);
    }
    this.convertView = convertView;
    return convertView;
    }

    class ViewHolder {
    public TextView emailShow;
    public RadioButton statusShow;
    public CheckBox removeCb;
    }

    class CheckBoxListener implements android.widget.CompoundButton.OnCheckedChangeListener {
    @Override
    public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
    int id = buttonView.getId();
    if(!isChecked) {
    removeMap.remove(id);
    return;
    }
    removeMap.put(id, isChecked);

    Log.v("ListViewAdapter", removeMap.toString());
    }
    }

    class RadioGroupListener implements OnCheckedChangeListener {
    public RadioGroupListener(){}
    @Override
    public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
    int id = buttonView.getId();
    if(!isChecked) {
    return;
    }
    int tempId = -1;
    if(statusMap.size() > 0) {
    tempId = statusMap.entrySet().iterator().next().getKey();
    RadioButton rb = (RadioButton) ((Activity) context).findViewById(tempId);
    if(rb != null)
    rb.setChecked(false);
    }
    statusMap.remove(tempId);
    statusMap.put(id, isChecked);
    DBManager db = new DBManager(context);
    ContentValues cv = new ContentValues();
    cv.put("status", 0);
    db.update(Constant.MAIN_TABLE, cv, " where id = ?", cvs.get(id).getAsString("id"));
    cv.put("status", 1);
    db.update(Constant.MAIN_TABLE, cv, " where id != ?", cvs.get(id).getAsString("id"));
    Log.v("ListViewAdapter", statusMap.toString() + ", " + id);
    }
    }

    class EmailShowListener implements OnClickListener {

    @Override
    public void onClick(View v) {
    int id = cvs.get(v.getId() - e_id).getAsInteger("id");
    DBManager db = new DBManager(context);
    String sql = "select e.*, t.name, t.host_name,t.send_host_name from main_table e left join email_type t on t.id = e.email_type where t.status = 0 and e.id=?";
    ArrayList<ContentValues> cvs = db.rawQuery(sql, "" + id);
    if(cvs.size() > 0) {
    ContentValues cv = cvs.get(0);
    SelfSettingDialog.Builder sb = new SelfSettingDialog.Builder(context);
    sb.setTitle(cv.getAsString("email"));
    StringBuffer temp = new StringBuffer();
    temp.append("名称：").append(cv.getAsString("name")).append("\n").append("发送协议：").append(cv.getAsString("host_name")).append("\n");
    temp.append("接收协议：").append(cv.getAsString("send_host_name")).append("\n");
    temp.append("创建时间：").append(cv.getAsString("create_time"));
    sb.setMessage(temp.toString());
    sb.setPostiveButton(R.string.ok, new PostiveButtonListener());
    sb.setNegativeButton(R.string.update, new NegativeButtonListener());
    Dialog dialog = sb.create();
    dialog.show();
    }
    }

    }

    public void setSelectAll(boolean isChecked) {
    int length = cvs.size();
    for(int i = 0; i < length; i++) {
    removeMap.put(c_id + i, isChecked);
    }
    }

    public HashMap<Integer, Boolean> getSelectedRemove() {
    return removeMap;
    }

    public void setSelectedRomve(HashMap<Integer, Boolean> removes) {
    this.removeMap = removes;
    }

    public void setStatus(HashMap<Integer, Boolean> statusMap) {
    this.statusMap = statusMap;
    }

    public HashMap<Integer, Boolean> getStatusSelected() {
    return statusMap;
    }

    public ArrayList<ContentValues> getCvs() {
    return cvs;
    }

    class PostiveButtonListener implements DialogInterface.OnClickListener {

    @Override
    public void onClick(DialogInterface dialog, int which) {
    dialog.dismiss();
    }

    }

    class NegativeButtonListener implements DialogInterface.OnClickListener {

    @Override
    public void onClick(DialogInterface dialog, int which) {
    Intent intent = new Intent(context,UpdateTargetEmailActivity.class);
    ((Activity)context).overridePendingTransition(android.R.anim.slide_in_left, android.R.anim.slide_out_right);
    MainActivity.tabHost.addTab(MainActivity.buildTabSpec("home2",-1, R.drawable.receivebox, intent,context.getResources()));
    //((Activity)context).startActivityForResult(intent, 1);
    }

    }
    }
