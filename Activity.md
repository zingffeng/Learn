### Activity和Activity之间数据传递
#### 将数据传递到下一个Activity页面的方法有两种 (FirstActivity.java -> SecondActivity.java)
1. 第一种
+ 将数据存入FirstActivity的Intent中
    ```
        String data = "This is FirstActivity data!";
        Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
        intent.putExtra("extra_data", data);
        startActivity(intent);
    ```
+ 再从SecondActivity页面取出数据
    ```
        Intent intent = getIntent();
        String data = intent.getStringExtra("extra_data");
        Log.d("SecondActivity", data);
    ```
    
2. 第二种
+ 将数据先存入Bundle中，再将Bundle存入FirstActivity的Intent中  
    ```
        String data = "This is FirstActivity data!";
        Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
        Bundle bundle = new Bundle();
        bundle.putString("extra_data", data);
        intent.putExtra("Message", bundle);
        startActivity(intent);
    ```
+ 再从SecondActivity页面取出数据
    ```
        Intent intent = getIntent();
        Bundle bundle = intent.getBundleExtra("Message");
        String data = bundle.getString("extra_data");
        Log.d("SecondActivity", data);
    ```
    
#### 将数据传递到上一个Activity页面的方法（SecondActivity.java -> FirstActivity.java)
+ 需要在FirstActivity中更换启动活动的方法
    ```
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
            //更换原来的startActivity()，而是使用startActivityForResult();
            startActivityForResult(intent, 1);   
        }
        
        //重写onActivityResult()方法
        @Override
        protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
            switch (requestCode){
                case 1:
                    if (resultCode == RESULT_OK){
                        String returnData = data.getStringExtra("data_return");
                        Log.d("FirstActivityQAQ", returnData);
                    }
                break;
            default:
        }
    }  
    ```
   
+ 在SecondActivity.java的代码
    ```
        Intent intent = new Intent();
        intent.putExtra("data_return", "This is SecondActivity data!");
        setResult(RESULT_OK, intent);
        finish();
    ```

------
### 活动的生命周期

#### 返回栈 
> Android是使用任务（Task）来管理活动的，一个任务就是一组存放在栈里的活动的集合，这个栈也被称为返回栈

#### 活动状态   
活动的生命周期中最多有4种状态
1. 运行状态         
    当一个活动位于返回栈的栈顶时，这时活动就处于运行状态。
2. 暂停状态         
    当一个活动不再处于栈顶位置时，但是仍然可见，这时活动就进入暂停状态（比如对话框形式的活动只会占用屏幕中间的部分区域，但是你很快就会在后面看到这种活动），处于暂停状态的活动仍然是完全存活的。
3. 停止状态         
    当一个活动不再处于栈顶位置，并且完全不可见的时候，就进入了停止状态。这种状态，系统仍然会为这种活动保存相应的状态和成员变量，但是当系统内存不足的时候，处于停止状态的活动有可能会被系统收回。
4. 销毁状态         
    当一个活动从返回栈中移除后就会变成销毁状态。系统最倾向于回收处于这状态的活动，这样就可以保证手机内存的充足。
    
#### 活动的生存期
Activity类中定义了7个回调方法，覆盖了活动生命周期的每一个环节。
+ **onCreate()**        
    首次创建 Activity 时调用。 您应该在此方法中执行所有正常的静态设置 — 创建视图、将数据绑定到列表等等。 系统向此方法传递一个 Bundle 对象，其中包含 Activity 的上一状态，不过前提是捕获了该状态。始终后接 onStart()。
+ **onRestart()**       
    在 Activity 已停止并即将再次启动前调用。始终后接 onStart()。
+ **onStart()**         
    在 Activity 即将对用户可见之前调用。如果 Activity 转入前台，则后接 onResume()，如果 Activity 转入隐藏状态，则后接 onStop()。
+ **onResume()**        
    在 Activity 即将开始与用户进行交互之前调用。 此时，Activity 处于 Activity 堆栈的顶层，并具有用户输入焦点。始终后接 onPause()。
+ **onPause()**         
    当系统即将开始继续另一个 Activity 时调用。 此方法通常用于确认对持久性数据的未保存更改、停止动画以及其他可能消耗 CPU 的内容，诸如此类。 它应该非常迅速地执行所需操作，因为它返回后，下一个 Activity 才能继续执行。
如果 Activity 返回前台，则后接 onResume()，如果 Activity 转入对用户不可见状态，则后接 onStop()。
+ **onStop()**          
    在 Activity 对用户不再可见时调用。如果 Activity 被销毁，或另一个 Activity（一个现有 Activity 或新 Activity）继续执行并将其覆盖，就可能发生这种情况。
    如果 Activity 恢复与用户的交互，则后接 onRestart()，如果 Activity 被销毁，则后接 onDestroy()。
+ **onDestory()**           
    在 Activity 被销毁前调用。这是 Activity 将收到的最后调用。 当 Activity 结束（有人对 Activity 调用了 finish()），或系统为节省空间而暂时销毁该 Activity 实例时，可能会调用它。 您可以通过 isFinishing() 方法区分这两种情形。

![my-logo.png](https://github.com/zingffeng/Learn/blob/master/images/activity_lifecycle.png)        

#### 活动的启动模式        
> 启动模式有四种，分别是 **standard、singleTop、singleTask和singleInstance**
##### 使用方法：在AndroidManifest.xml文件中通过给<Activity>标签指定android:launchMode属性来选择启动模式。       
+ **standard模式**        
    这是活动默认的启动模式。每当启动一个新活动，他就会在返回栈中入栈，并处于栈顶的位置。对于standard模式的活动，系统不会在乎这个活动是否已经在返回栈中存在，每次启动都会创建该活动的一个新的实例。
+ **singleTop模式**       
    这种模式的活动，在启动活动时会判断该活动是否位于栈顶，如果位于栈顶不会重新实例活动，但是当启动SecondActivity活动后，重新启动FirstActivity,还是会创建新的实例。
+ **singleTask模式**      
    这种模式很好的解决了重复创建栈顶活动的问题。每次启动给活动时系统首先会在返回栈中检查是否存在该活动的实例，如果发现已经存在则直接使用该实例，并把在这个活动之上的所有活动统统出栈，如果没有发现就会创建一个新的活动实例。
+ **singleInstance模式**     
    在这种模式下会有一个单独的返回栈来管理这个活动，不管是哪个应用程序来访问这个程序，都共用的同一个返回栈。
