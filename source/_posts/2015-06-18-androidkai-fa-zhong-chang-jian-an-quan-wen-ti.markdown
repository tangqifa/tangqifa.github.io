---
layout: post
title: "Android开发中常见安全问题"
date: 2015-06-18 17:43:58 +0800
comments: true
categories: android 
---
> 由于公司是做互联网金融的，所以对APP客户端的安全性要求较高，本文针对中国金融认证中心（简称CFCA）对我们的APP检测过程中存在的问题和解决方案梳理如下：

* 存在问题：Activity劫持情况，恶意应用可以劫持合法应用   
  整改方案：当程序启动后，判断当前置顶的应用是否为自身应用，若不是，则弹出Toast窗口提示用户注意   
  关键代码：
  <pre>
  //获取当前顶层应用包名
      public static String getTopPackageName(Context context) {
        ActivityManager am = (ActivityManager)context.getSystemService(context.ACTIVITY_SERVICE);
        List<ActivityManager.RunningTaskInfo> taskInfo = am.getRunningTasks(1);
        ComponentName componentInfo = taskInfo.get(0).topActivity;
        return componentInfo.getPackageName();
    }
  </pre>
  
* 存在问题：未检查是否root   
  整改方案：判断系统状态，发现root、越狱后提示用户风险   
  关键代码：
  <pre>
  //判断是否root
    public static boolean isRoot() {
        boolean root = false;
        try {
            if ((!new File("/system/bin/su").exists())
                    && (!new File("/system/xbin/su").exists())) {
                root = false;
            } else {
                root = true;
            }
        } catch (Exception e) {
        }
        return root;
    }
  </pre>
  
* 存在问题：软件未进行加壳加固   
  整改方案：对软件进行加壳加固，主要考虑第三方，比如爱加密
  
  
* 存在问题：客户端未能验证自身合法性，服务器也未能验证客户端合法性   
  整改方案：在程序启动以及运行过程中，对程序自身的签名进行校验，而校验方式一般是通过对应用的签名的验证，比如对签名做MD5或者sha1   
  关键代码：
   <pre>
  //获取签名的MD5
 public static  String getCertMsg(Context context, String packageName) {
        String certMsg = "";
        if (TextUtils.isEmpty(packageName)) {
            return certMsg;
        }
        PackageInfo pis;
        try {
            pis = context.getPackageManager().getPackageInfo(packageName, PackageManager.GET_SIGNATURES);
            Signature[] sigs = pis.signatures;    //签名
            CertificateFactory certFactory = CertificateFactory.getInstance("X.509");
            //获取证书
            X509Certificate cert = (X509Certificate) certFactory.generateCertificate(
                    new ByteArrayInputStream(sigs[0].toByteArray()));
            //获取证书发行者   可根据证书发行者来判断该应用是否被二次打包（被破解的应用重新打包后，签名与原包一定不同，据此可以判断出该应用是否被人做过改动）
            certMsg = MD5Utils.getMD5(cert.getSubjectDN().toString());
            LogHelperDebug.d(TAG,"SubjectDN MD5: " + certMsg);
        } catch (CertificateException e) {
            LogHelperDebug.e("CertificateException", e.getMessage());
        } catch (Exception e) {
            LogHelperDebug.e("Exception: ", e.getMessage());
        }
        return certMsg;
    }
  </pre>
  
  
* 存在问题：未对口令复杂度进行验证   
  整改方案：建议要求口令复杂度，至少两种字符,主要利用正则表达式验证   
  关键代码：
  <pre>
      public static boolean checkPassword(String value) {
        return value.matches("^(?=.*[0-9])(?=.*[a-zA-Z])(?=\\S+$).{8,}$");
    }
  </pre>
  
  
* 存在问题：口令连续尝试次数没有限制   
  整改方案：增加连续尝试错误次数，建议5次
  
  
* 存在问题：未设置会话超时时间   
  整改方案：增加超时时间，未操作一段时间后，再次操作需要重新登录,设置token超时机制
  
  
* 存在问题：网络传输中未对数据进行加密,比如密码等   
  整改方案：对传输的数据包进行整体加密，比如采用AES加密等
  
  
* 存在问题：密码输入调用系统键盘，未采用自主编写安全键盘，且无法进行按键随即布局，无法防止截屏记录   
  整改方案：采用自主编写的安全键盘进行敏感信息的输入，同时做到按键布局随机，取消按键气泡等按下状态显示效果。同时按下的按键做到内存中加密存储，参考我的开源项目[AndroidSecurityKeyboard](https://github.com/tangqifa/AndroidSecurityKeyboard)
  
* 存在问题：数据传输MAC校验   
  整改方案：增加MAC校验，例如：HMAC，对整体url做哈希，并加入特定的前缀
  
* 存在问题：软件更新通过HTTP方式，未采取安全传输方式    
  整改方案：采用HTTPS
