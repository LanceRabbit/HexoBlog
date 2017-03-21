---
layout: post
title: '[Android] Drag and dorp ListView'
date: 2015-04-05 01:02
comments: true
categories: Android
tag: 
	- ListView
	- Drag and drop
---
因為客製化的需求，第一次嘗試在Settings 中添加新的功能
其中一項為 ListView 的物件可以拖拉排序

參考的為 Music 中的 TrackBrowserActivity.java 與  TouchInterceptor.java

首先先自訂一個Layout for activity
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
    <com.android.alucard.DragListView
       android:id="@+id/drag_list"
       android:layout_width="fill_parent"
       android:layout_height="fill_parent"
       android:cacheColorHint="#00000000"/>
</LinearLayout>
```

因為要實現能drag and drop 的效果，使用原生的ImageView or ListView 無法實現
所以寫了一個新的View (繼承ListView) 並在Layout中使用他.

以下是 DragListView 的實現

```java
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.Color;
import android.graphics.PixelFormat;
import android.util.AttributeSet;
import android.view.Gravity;
import android.view.MotionEvent;
import android.view.View;
import android.view.ViewConfiguration;
import android.view.ViewGroup;
import android.view.WindowManager;
import android.widget.AdapterView;
import android.widget.ImageView;
import android.widget.ListView;

public class DragListView extends ListView {

    private ImageView dragImageView;
    private int dragSrcPosition;
    private int dragPosition;

    private int dragPoint;
    private int dragOffset;
    private Context mContext;

    private WindowManager windowManager;
    private WindowManager.LayoutParams windowParams;

    private int scaledTouchSlop;
    private int upScrollBounce;
    private int downScrollBounce;

    private DropListener mDropListener;


    public DragListView(Context context, AttributeSet attrs) {
        super(context, attrs);
        mContext = context;
        scaledTouchSlop = ViewConfiguration.get(context).getScaledTouchSlop();
    }

    public interface DropListener {
        void drop(int dragPosition ,int dropPosition);
    }

    public void setDropListener(DropListener l) {
        mDropListener = l;
    }
    //
    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        if(ev.getAction() == MotionEvent.ACTION_DOWN){
            int x = (int) ev.getX();
            int y = (int) ev.getY();

            dragSrcPosition = dragPosition = pointToPosition(x, y);
            if(dragPosition == AdapterView.INVALID_POSITION){
                return super.onInterceptTouchEvent(ev);
            }
            ViewGroup itemView = (ViewGroup) getChildAt(dragPosition - getFirstVisiblePosition());
            dragPoint = y - itemView.getTop();
            dragOffset = (int) (ev.getRawY() - y);

            View dragger = itemView.findViewById(R.id.drag_list_item_image);
            if(dragger !=null && x > dragger.getLeft() - 20){

                upScrollBounce = Math.min(y-scaledTouchSlop, getHeight()/3);
                downScrollBounce = Math.max(y+scaledTouchSlop, getHeight()*2/3);

                itemView.setDrawingCacheEnabled(true);
                Bitmap bm = Bitmap.createBitmap(itemView.getDrawingCache());
                bm.setHasAlpha(true);
                startDrag(bm, y);
            }
            return false;
         }
         return super.onInterceptTouchEvent(ev);
    }

    /**
     *
     */
    @Override
    public boolean onTouchEvent(MotionEvent ev) {
        if(dragImageView != null && dragPosition != INVALID_POSITION){
            int action = ev.getAction();
            switch(action){
                case MotionEvent.ACTION_UP:
                    int upY = (int)ev.getY();
                    stopDrag();
                    onDrop(upY);
                    break;
                case MotionEvent.ACTION_MOVE:
                    int moveY = (int)ev.getY();
                    onDrag(moveY);
                    break;
                default:break;
            }
            return true;
        }
        //
        return super.onTouchEvent(ev);
    }

    /**
     *
     * @param bm
     * @param y
     * 在拖曳的時候，被拖曳的image會放大120%
     */
    public void startDrag(Bitmap bm ,int y){
        stopDrag();
        float disreHeight =  bm.getHeight() * (float) 1.2;
        float disreWidth = bm.getWidth()  * (float) 1.2;
        windowParams = new WindowManager.LayoutParams();
        windowParams.gravity = Gravity.TOP;
        windowParams.x = 0;
        windowParams.y = y - dragPoint + dragOffset;
        windowParams.width = (int) disreWidth;
        windowParams.height = (int )disreHeight;
        windowParams.flags = WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE
                            | WindowManager.LayoutParams.FLAG_NOT_TOUCHABLE
                            | WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON
                            | WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN;
        windowParams.format = PixelFormat.TRANSLUCENT;
        windowParams.alpha=0.6f;
        windowParams.windowAnimations = 0;

        ImageView imageView = new ImageView(getContext());
        imageView.setBackgroundColor(Color.WHITE);
        imageView.setImageBitmap(bm);
        imageView.setScaleType(ImageView.ScaleType.FIT_XY);
        windowManager = (WindowManager)getContext().getSystemService("window");
        windowManager.addView(imageView, windowParams);
        dragImageView = imageView;
    }

    /**
     *
     */
    public void stopDrag(){
        if(dragImageView != null){
            windowManager.removeView(dragImageView);
            dragImageView = null;
        }
    }

    /**
      * 因為需求只要求可以上下移動，故只取縱向的數據
      *
      */
    public void onDrag(int y){
        if(dragImageView != null){
            windowParams.alpha = 0.8f;
            windowParams.y = y - dragPoint + dragOffset;
            windowManager.updateViewLayout(dragImageView, windowParams);
        }
        //
        int tempPosition = pointToPosition(0, y);
        if(tempPosition != INVALID_POSITION){
            dragPosition = tempPosition;
        }

        int scrollHeight = 0;
        if( y < upScrollBounce){
            scrollHeight = 8;
        }else if(y > downScrollBounce){
            scrollHeight = -8;
        }

        if(scrollHeight!=0){
            setSelectionFromTop(dragPosition, getChildAt(dragPosition - getFirstVisiblePosition()).getTop() + scrollHeight);
        }
    }

    /**
      * 因為需求只要求可以上下移動，故只取縱向的數據
      *
      */
    public void onDrop(int y){
        int tempPosition = pointToPosition(0, y);
        if(tempPosition != INVALID_POSITION){
            dragPosition = tempPosition;
        }
        if(y < getChildAt(1).getTop()){
            dragPosition = 0;
        }else if(y > getChildAt(getChildCount()-1).getBottom()){
            dragPosition = getAdapter().getCount()-1;
        }
        if(dragPosition >= 0 && dragPosition < getAdapter().getCount()){
            mDropListener.drop(dragSrcPosition, dragPosition);
        }

    }
}
```

在Activity中 調用 Adapter, 並且倒入到剛剛的View中.
Activity:
```java
import android.content.Intent;
import android.view.View;
import android.view.ViewGroup;


import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import android.widget.WrapperListAdapter;
import android.content.Context;
import android.provider.Settings;


import android.app.Fragment;
import android.view.LayoutInflater;
import android.os.Bundle;

import android.util.Log;


public class LockFuncSettings extends Fragment {

    private final String TAG = "LockFuncSettings";
    private Context mContext;
    private View mlistview;
    private List<String> mFunctionList;

    private DragListAdapter adapter;
    private DragListView dragListView;
    private final String KEY_LOCK_FUNC_SETTING = "lockscrren_func_setting_pref";

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mContext = getActivity();

        mFunctionList = new ArrayList<String>();
        loadFuncList();

    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
            Bundle savedInstanceState) {
        mlistview = inflater.inflate(R.layout.drag_list_activity, null);
        dragListView = (DragListView) mlistview.findViewById(R.id.drag_list);
        return mlistview;
    }

    private void loadFuncList() {
        String list = Settings.System.getString(mContext.getContentResolver(), Settings.System.LOCKSCREEN_FUNC_LIST);
        for (String s : list.split(" ")) {
            mFunctionList.add(s);
        }

        mFunctionList.add(5, "12");
    }
    @Override
    public void onResume() {
        adapter = new DragListAdapter(mContext, mFunctionList);
        dragListView.setDropListener(mDropListener);
        dragListView.setAdapter(adapter);
        super.onResume();
    }

    @Override
    public void onPause() {
        super.onPause();
    }

    private DragListView.DropListener mDropListener =
            new DragListView.DropListener() {
                public void drop(int dragPosition ,int dropPosition) {
                    if (adapter != null) {
                        String dragItem = (String) adapter.getItem(dragPosition);
                        Log.d("LockFuncSettings", "dragItem = " + dragItem);
                        adapter.remove(dragItem);
                        adapter.insert(dragItem, dropPosition);
                        //---------------------------------------------------------
                        adapter.remove("12");
                        String result = "";
                        for(int i = 0; i < 12; i++) {
                            result = result + (String) adapter.getItem(i) + " ";
                        }
                        Log.d(TAG, "result = " + result);
                        Settings.System.putString(mContext.getContentResolver(),
                                Settings.System.LOCKSCREEN_FUNC_LIST, result.trim());
                        adapter.insert("12", 5);
                        adapter.notifyDataSetChanged();
                        mContext.sendBroadcast(new Intent("android.intent.action.FUNC_SETTINGS_CHANGED"));
                    }
                }
            };

}
```

Adapter:
```java
import java.util.ArrayList;
import java.util.List;

import android.content.Context;
import android.graphics.Color;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ArrayAdapter;
import android.widget.TextView;
import android.widget.ImageView;
import android.provider.Settings;
import android.util.Log;

public class DragListAdapter extends ArrayAdapter<String> {

    public List<String> groupKey = new ArrayList<String>();
    public DragListAdapter(Context context, List<String> objects) {
        super(context, 0, objects);
        groupKey.add(getContext().getString(R.string.lock_settings_wont_show));
    }


    @Override
        public boolean isEnabled(int position) {
            if(groupKey.contains(getItem(position))){
                //
                return false;
            }
            return super.isEnabled(position);
        }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {

        View view = convertView;
        if(position == 5) {
            view = LayoutInflater.from(getContext()).inflate(R.layout.drag_list_item_tag, null);
        }else{
            view = LayoutInflater.from(getContext()).inflate(R.layout.drag_list_item, null);
        }

        TextView textView = (TextView)view.findViewById(R.id.drag_list_item_text);
        ImageView imageView = (ImageView) view.findViewById(R.id.drag_list_item_icon);
        int id = Integer.parseInt(getItem(position));
        boolean isBlue = (position < 5)? true:false;

        switch (id) {
            case Settings.System.FUNC_CALCULATOR:
                imageView.setImageResource(isBlue? R.drawable.list_calculator_blue:R.drawable.list_calculator_grey);
                textView.setText(R.string.func_calculator);
                break;
            case Settings.System.FUNC_PLAYLIST:
                imageView.setImageResource(isBlue? R.drawable.list_playlist_blue:R.drawable.list_playlist_grey);
                textView.setText(R.string.func_playlist);
                break;
            case Settings.System.FUNC_SELFIE:
                imageView.setImageResource(isBlue? R.drawable.list_selfie_blue:R.drawable.list_selfie_grey);
                textView.setText(R.string.func_selfie);
                break;
            case Settings.System.FUNC_CONTACT:
                imageView.setImageResource(isBlue? R.drawable.list_contact_blue:R.drawable.list_contact_grey);
                textView.setText(R.string.func_contact);
                break;
            case Settings.System.FUNC_QR_CODE:
                imageView.setImageResource(isBlue? R.drawable.list_qr_blue:R.drawable.list_qr_grey);
                textView.setText(R.string.func_qr_code);
                break;
            case Settings.System.FUNC_MESSAGE:
                imageView.setImageResource(isBlue? R.drawable.list_mes_blue:R.drawable.list_mes_grey);
                textView.setText(R.string.func_message);
                break;
            case Settings.System.FUNC_EMAIL:
                imageView.setImageResource(isBlue? R.drawable.list_email_blue:R.drawable.list_email_grey);
                textView.setText(R.string.func_email);
                break;
            case Settings.System.FUNC_AGENDA:
                imageView.setImageResource(isBlue? R.drawable.list_event_blue:R.drawable.list_event_grey);
                textView.setText(R.string.func_agenda);
                break;
            case Settings.System.FUNC_RECORD:
                imageView.setImageResource(isBlue? R.drawable.list_record_blue:R.drawable.list_record_grey);
                textView.setText(R.string.func_record);
                break;
            case Settings.System.FUNC_HOME:
                imageView.setImageResource(isBlue? R.drawable.list_naigation_blue:R.drawable.list_naigation_grey);
                textView.setText(R.string.func_home);
                break;
            case Settings.System.FUNC_ALARM:
                imageView.setImageResource(isBlue? R.drawable.list_alarm_blue:R.drawable.list_alarm_grey);
                textView.setText(R.string.func_alarm);
                break;
            case Settings.System.FUNC_NOTE:
                imageView.setImageResource(isBlue? R.drawable.list_note_blue:R.drawable.list_note_grey);
                textView.setText(R.string.func_note);
                break;
            default:
                imageView.setImageResource(R.drawable.list_arrow);
                textView.setText(R.string.lock_settings_wont_show);
        }
        if (!isBlue) {
            view.setBackgroundColor(Color.parseColor("#e5e5e5"));
            textView.setTextColor(Color.parseColor("#a0a0a0"));
        }
        return view;
    }
}
```
在DragListView中的物件，有兩個xml

第一個是可以拖拉物件的layout 定義
請注意，一定要用 *RelativeLayout*
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <ImageView android:id="@+id/drag_list_item_icon"
        android:layout_centerVertical="true"
        android:layout_width="wrap_content"
        android:layout_marginStart="@dimen/search_result_item_image_margin_start"
        android:layout_marginEnd="@dimen/search_result_item_image_margin_end"
        android:layout_height="@dimen/drag_item_normal_height"/>
    <TextView
       android:id="@+id/drag_list_item_text"
       android:layout_width="wrap_content"
       android:layout_height="@dimen/drag_item_normal_height"
       android:layout_marginLeft="75dp"
       android:layout_alignParentStart="true"
       android:textSize="16sp"
       android:gravity="center_vertical"/>
    <ImageView android:id="@+id/drag_list_item_image"
       android:src="@drawable/list_menu"
       android:layout_marginRight="15dp"
       android:layout_alignParentRight="true"
       android:layout_centerVertical="true"
       android:layout_width="wrap_content"
       android:layout_height="@dimen/drag_item_normal_height"/>
</RelativeLayout>
```

第二個為不可拖拉的物件
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:background="#a0a0a0"
    android:padding="5dip"
    android:paddingLeft="10dip">
    <ImageView android:id="@+id/drag_list_item_icon"
        android:layout_centerVertical="true"
        android:layout_width="wrap_content"
        android:layout_marginStart="@dimen/search_result_item_image_margin_start"
        android:layout_marginEnd="@dimen/search_result_item_image_margin_end"
        android:layout_height="@dimen/drag_item_normal_height"/>
    <TextView
       android:id="@+id/drag_list_item_text"
       android:layout_width="wrap_content"
       android:layout_height="20dip"
       android:textColor="#ffffff"
       android:textSize="14sp"
       android:layout_marginStart="@dimen/settings_side_margin"
       android:layout_marginEnd="@dimen/settings_side_margin"
       android:gravity="center_vertical"/>
</LinearLayout>
```

