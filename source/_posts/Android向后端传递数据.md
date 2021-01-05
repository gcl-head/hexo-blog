---
title: Android向后端传递数据
date: 2020-04-13 18:46:12
updated: 2020-04-13 18:46:12
tags:
    - android
categories: frontend
---
>  安卓开发中，网络交互是必不可少的。而安卓向后端发送请求的方式有很多种，本篇讲述我使用Http Connection来与后端交互的具体方法和代码。

## 1. 请求网络权限

在AndroidManifest.xml中添加权限

```xml
<!--允许联网 -->
<uses-permission android:name="android.permission.INTERNET" />
<!--获取GSM（2g）、WCDMA（联通3g）等网络状态的信息  -->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<!--获取wifi网络状态的信息 -->
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<!--保持CPU 运转，屏幕和键盘灯有可能是关闭的,用于文件上传和下载 -->
<uses-permission android:name="android.permission.WAKE_LOCK" />
<!--获取sd卡写的权限，用于文件上传和下载-->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<application
	<!--...-->
	android:usesCleartextTraffic="true"
	<!--...-->
</application>
```

在MainActivity.java中增加如下代码便可在主线程使用联网功能。在练手的时候可以使用，之后建议

将联网部分单独放在子线程中进行，提高app的流畅度。

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    // 允许联网非多进程
    if (android.os.Build.VERSION.SDK_INT > 9) {
        StrictMode.ThreadPolicy policy = new 		StrictMode.ThreadPolicy.Builder().permitAll().build();
        StrictMode.setThreadPolicy(policy);
    }
    // ...
}
```

## 2. 编写网络请求函数

下面是我封装的网络请求类

```java
public class HttpConnect {
    public static final String href = "http://***"; // 请求服务器地址
    private static String session_value = ""; // session id
    public static String sendByGet(String href){
        //get的方式提交就是url拼接的方式
        String path = href;
        try {
            URL url = new URL(path);
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
            connection.setConnectTimeout(5000);
            connection.setRequestMethod("GET");
            connection.setRequestProperty("Cookie", session_value); // 更新session
            //获得结果码
            int responseCode = connection.getResponseCode();
            if(responseCode ==200){
                //请求成功 获得返回的流
                if (connection.getHeaderField("Set-Cookie")!=null)
                    session_value = connection.getHeaderField("Set-Cookie"); // 维护session
                InputStream is = connection.getInputStream();
                ByteArrayOutputStream baos = new ByteArrayOutputStream();
                int len = -1;
                byte[] buffer = new byte[1024];
                while ((len = is.read(buffer)) != -1) {
                    baos.write(buffer, 0, len);
                }
                is.close();

                //这样就得到服务器返回的数据了
                return baos.toString();
            }else {
                //请求失败
                return null;
            }
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (ProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
    public static String sendByPost(String href, String text){
        String path = href;
        try {
            URL url = new URL(path);
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
            connection.setConnectTimeout(5000);
            connection.setRequestMethod("POST");
            connection.setRequestProperty("Cookie", session_value); // 更新session

            //至少要设置的两个请求头
            connection.setRequestProperty("Content-Type","application/x-www-form-urlencoded");
//            connection.setRequestProperty("Content-Length", data.length()+"");

            //post的方式提交实际上是留的方式提交给服务器
            connection.setDoOutput(true);
            OutputStream outputStream = connection.getOutputStream();
            outputStream.write(text.getBytes());

            //获得结果码
            int responseCode = connection.getResponseCode();
//            System.out.println(1);
//            System.out.println(responseCode);
            if(responseCode ==200){
                //请求成功
                if (connection.getHeaderField("Set-Cookie")!=null)
                    session_value = connection.getHeaderField("Set-Cookie"); // 维护session
                InputStream is = connection.getInputStream();
                ByteArrayOutputStream baos = new ByteArrayOutputStream();
                int len = -1;
                byte[] buffer = new byte[1024];
                while ((len = is.read(buffer)) != -1) {
                    baos.write(buffer, 0, len);
                }
                is.close();

                //这样就得到服务器返回的数据了
                return baos.toString();
            }else {
                //请求失败
                return null;
            }
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (ProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

可以看到，除了基础的设定请求时间，请求头等。如果返回的数据字段有*Set-Cookie*则还要根据该字段的值来更新类中存放cookie的变量，从而让每次请求都带有每个用户独有的信息方便服务器鉴别用户等，而不用每次都放在数据字段中带来带去。

## 3. 使用网络请求类

有了上述的网络请求类，我们只需要调用`HttpConnect.sendByGet()`或者`HttpConnect.sendByPost()`就可以进行http请求了。

```java
// 登录验证...'
new Thread(new Runnable() {
    @Override
    public void run() {
        String login_data_text = HttpConnect.sendByPost(HttpConnect.href+"/android_login/",                                                       "username="+number+"&password="+password);
        // json转码
        Login_data login_data = JSON.parseObject(login_data_text, Login_data.class); 
        if (login_data.getRe().equals("Fail") ) {
            // 关闭进度条
            Message msg = new Message();
            msg.what = 1;
            handler.sendMessage(msg);
            //
            return;
        }
        // 创建user_data文件存储用户登录账号方便下次自动登录
        SharedPreferences.Editor editor = getSharedPreferences("user_data", MODE_PRIVATE).edit();
        editor.putString("user_number", number);
        editor.putString("user_password", password);
        editor.apply();
        Intent intent = new Intent(LoginActivity.this, MainActivity.class);
        intent.putExtra("user_name", login_data.getName());
        intent.putExtra("user_number", login_data.getNumber());

        startActivity(intent);
        // 关闭进度条
        Message msg = new Message();
        msg.what = 0;
        handler.sendMessage(msg);
        //
        finish();
    }
}).start();
```

可以看到在上述代码中，一旦涉及到对于控件的处理，我就会向`Handler`发送` Message`。这是因为在安卓中，子线程不能对UI进行操作，UI的操作必须始终在主线程中进行。这样可以防止多线程同时对UI进行操作而产生错误的情况。因此在子线程中必须通过`Handler`来操作控件。

具体做法就是新建一个`Handler`，然后根据传入`Handler`的`Message`的值来对相应控件进行操作，比如对于上述登录进程的Handler是这样定义的。

```java
Handler handler = new Handler(new Handler.Callback() {
    @Override
    public boolean handleMessage(@NonNull Message message) {
        ProgressBar progressBar = findViewById(R.id.loading);
        if (message.what==0) {
            Toast toast=Toast.makeText(getApplication(),"登录成功！",Toast.LENGTH_SHORT);
            toast.show();
            progressBar.setVisibility(View.GONE);
        } else if (message.what==1) {
            Toast toast=Toast.makeText(getApplication(),"用户名或密码错误",Toast.LENGTH_SHORT);
            toast.show();
            progressBar.setVisibility(View.GONE);
        }
        return false;
    }
});
```