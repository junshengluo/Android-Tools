﻿Android安全退出应用程序
===

1. 杀死进程。    
    这种方法是没有效果的只能杀死当前的`Activity`无法关闭程序，在1.5的时候有用，谷歌设计的时候规定程序不能自杀`android.os.Process.killProcess(android.os.Process.myPid())`.                                 
2. 终止当前正在运行的Java虚拟机，导致程序终止.     
    这种方法也是没有效果的，因为`Android`用的是`dalvik`虚拟机
    `System.exit(0);`
3. 强制关闭与该包有关联的一切执行     
    这种方法只能杀死别人，无法杀死自己     
    ```java
    ActivityManager manager = (ActivityManager) getSystemService(Context.ACTIVITY_SERVICE);    
    manager.restartPackage(getPackageName());
    同时需要申请权限
    <uses-permission android:name="android.permission.RESTART_PACKAGES" />
    ```

既然上面介绍的三种方法都没有效果，那么怎么才能退出应用程序呢？        
就是自定义一个`Application`,在该`Application`中去定义一个`List<Activity>`的集合来记录中每一个开启的`Activity`，在退出的时候去遍历这个`List<Activity>`集合，然后挨个的进行`mActivity.finish()`方法，这要求在每开启一个`Activity`的时候都加入到`List`集合中，并且在`Activity`退出的时候从`List`集合中将其移除。       
```java
public class Activity01 extends Activity {
	
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		MyApp myApp = (MyApp) getApplication();
		myApp.activies.add(this);
	}
	
	@Override
	protected void onDestroy() {
		super.onDestroy();
		MyApp myApp = (MyApp) getApplication();
		myApp.activies.remove(this);
	}
	
	public void click1(View view){
		Intent intent = new Intent(this,Activity01.class);
		startActivity(intent);
	}
	public void click2(View view){
		Intent intent = new Intent(this,Activity02.class);
		startActivity(intent);
	}
	
	public void exit(View view){
		MyApp myApp = (MyApp) getApplication();
		 for(Activity ac : myApp.activies){
			 ac.finish();
		 }
	}
}

public class Activity02 extends Activity {

	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main2);
		MyApp myApp = (MyApp) getApplication();
		myApp.activies.add(this);
	}
	
	public void click1(View view) {
		Intent intent = new Intent(this, Activity01.class);
		startActivity(intent);
	}
	
	public void click2(View view) {
		Intent intent = new Intent(this, Activity02.class);
		startActivity(intent);
	}
	
	public void exit(View view) {
		MyApp myApp = (MyApp) getApplication();
		for (Activity ac : myApp.activies) {
			ac.finish();
		}
	}

	@Override
	protected void onDestroy() {
		super.onDestroy();
		MyApp myApp = (MyApp) getApplication();
		myApp.activies.remove(this);
	}
}

public class MyApp extends Application {
	//存放当前应用程序里面打开的所有的activity
	public List<Activity> activies;
	@Override
	public void onCreate() {
		activies = new ArrayList<Activity>();
		super.onCreate();
	} 
}
```

---

- 邮箱 ：charon.chui@gmail.com  
- Good Luck! 
