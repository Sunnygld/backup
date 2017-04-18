---
title: AS使用记录
date: 2017-03-08
tags: AS
categories: android
---



## Activities

- main-java-new activities

- res- new layoutfolder - new layout

- AS讲究逻辑与视图分离,最好每一个活动对应一个布局

- 每一个活动都应该在androidmanifest.xml中进行注册

<!--more -->

## main activities

````JAVA
<activity android:name=".FirstActivity"
	android:label="This is FirstActivity">
  	<intent-filter>
    	<action android:name="android.intent.action.MAIN"/>
      <category android:name="android.action.category.LAUNCHER"/>
  	</intent-filter>
````

## Button

````JAVA
<Button
    android:id="@+id/button_1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="Button 1"
    />
````

## Toast


````JAVA
 Button button1=(Button)findViewById(R.id.button_1);
        button1.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                Toast.makeText(FirstActivity.this,"You clicked Button 1",Toast.LENGTH_SHORT).show();
            }
        });
````

## Menu

````JAVA
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/add_item"
        android:title="Add"/>
    <item
        android:id="@+id/remove_item"
        android:title="Remove"/>
</menu>
````

## Menu显示与反馈

````JAVA
@Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch(item.getItemId()){
            case R.id.add_item:
                Toast.makeText(this,"You clicked Add",Toast.LENGTH_SHORT).show();
                break;
            case R.id.remove_item:
                Toast.makeText(this,"You clicked Remove",Toast.LENGTH_SHORT).show();
                break;
            default:
        }
        return true;
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.main,menu);
        return true;
    }
````

## intent


````JAVA
//Intent intent = new Intent(FirstActivity.this,SecondActivity.class);
//startActivity(intent);

Intent intent = new Intent("com.example.activitytest.ACTION_START");
intent.addCategory("com.example.activitytest.MY_CATEGORY");
startActivity(intent);
````