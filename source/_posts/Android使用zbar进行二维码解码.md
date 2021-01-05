---
title: Android使用zbar进行二维码解码
date: 2020-04-27 13:48:51
updated: 2020-04-27 13:48:51
tags:
    - android
    - zbar
categories: frontend
---
> 在项目中常常需要扫描二维码的功能。这就需要一种可以压缩图片，并快速解码的方法。
>
> zbar恰恰是你的最佳选择。他自带扫描二维码的页面不需要你手动去调用相机页面以及编写AutoFocus的逻辑，且速度极快。

## 1. 安装zbar

引入jar包和so文件

![865264-20180827092923347-193222905](https://blogic-1256965470.cos.ap-shanghai.myqcloud.com/blog/865264-20180827092923347-193222905.png)

如果jar文件和so文件放在libs下，需在app build.gradle的android标签中加入如下代码并`Sync Now`

```java
sourceSets {
    main {
        jniLibs.srcDirs = ['libs']
    }
}
```



将zbar包copy到项目

![865264-20180827093028766-726566162](https://blogic-1256965470.cos.ap-shanghai.myqcloud.com/blog/865264-20180827093028766-726566162.png)

 这里包名不一样肯定会报错，clean project并改掉包名就好。



导入相关资源文件

 drawable、drawable-hdpi、drawable-xhdpi和layout 

![865264-20180827190106756-1049638413](https://blogic-1256965470.cos.ap-shanghai.myqcloud.com/blog/865264-20180827190106756-1049638413.png)



raw文件和values文件 

![865264-20180827190145954-817553189](https://blogic-1256965470.cos.ap-shanghai.myqcloud.com/blog/865264-20180827190145954-817553189.png)

注：values中相关资源不要直接替换，否则会覆盖之前的，需要打开文件将内容加到自己项目对应文件中。 



AndroidManifest.xml加入相关权限和扫描的Activity

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera" />
<uses-feature android:name="android.hardware.camera.autofocus" />
<activity android:name=".zbar.CaptureActivity" />
```

## 2. 使用zbar

调用扫描界面 获取扫描结果

在需要打开扫描界面的地方直接跳转至CaptureActivity（使用startActivityForResult）

```java
/**
 * 跳转到扫码界面扫码
 */
private void goScan(){
    Intent intent = new Intent(MainActivity.this, CaptureActivity.class);
    startActivityForResult(intent, REQUEST_CODE_SCAN);
}
```

在onActivityResult的回调中即可获取扫描内容，如下

```java
  @Override
  protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    switch (requestCode) {
        case REQUEST_CODE_SCAN:
            // 扫描二维码回传
            if (resultCode == RESULT_OK) {
               if (data != null) {
                    //获取扫描结果
                    Bundle bundle = data.getExtras();
                    String result = bundle.getString(CaptureActivity.EXTRA_STRING);
                }
            }
            break;
        default:
            break;
    }
}
```



**动态权限申请**

由于扫描需要调用相机，打开摄像头属于敏感权限，所以需要进行权限的动态申请，如下

```java
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.get_code:
            //动态权限申请
            if (ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.CAMERA) != PackageManager.PERMISSION_GRANTED) {
                ActivityCompat.requestPermissions(MainActivity.this, new String[]{Manifest.permission.CAMERA}, 1);
            } else {
                goScan();
            }
            break;
    }
    return true;
}
// ...
@Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        switch (requestCode) {
            case 1:
                if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    goScan();
                } else {
                    Toast.makeText(this, "你拒绝了权限申请，可能无法打开相机扫码哟！", Toast.LENGTH_SHORT).show();
                }
                break;
            default:
        }
    }
```

## 3. 我遇到的问题

在电脑上进行模拟器测试的时候，二维码扫描一切正常，但使用真机进行测试的时候，报错说找不到`libZBarDecoder.so`文件。

貌似是版本兼容的问题，可以再`build.gradle`中添加如下代码解决：

```java
android {
    // ...
    splits {
        abi {
            enable true
            reset()
            include 'x86', 'x86_64', 'armeabi-v7a', 'armeabi'
            universalApk false
        }
    }
}
```