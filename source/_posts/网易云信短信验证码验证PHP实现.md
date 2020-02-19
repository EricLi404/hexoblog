---
title: 网易云信短信验证码验证PHP实现
date: 2016-09-11
tags: [PHP,code]
description: 本文给出了使用网易云信相关付费接口实现发送短信验证码的 PHP 实现方式
---



网易云信5分一条，阿里大于4分5一条，但是网易云信自带验证系统，阿里大于需要自己指定验证码，自己做验证，但是，，，，阿里大于可以自己定义签名，而网易云信只能使用默认的【云短信】，而且阿里大于的短信模板审核很快，，，网易云信妈的，。，，审核了两天，这还是我联系了客服妹子，要不，，，好了。。。不黑网易了。。。接下来上代码。



```
<?php
/**
 * Created by PhpStorm.
 * User: EricLi
 * Date: 2016/9/10
 * Time: 9:20
 */

//Auth 类，直接引入就行

class Auth
{

//将你注册的 key和 secret 定义好。

    const APP_KEY = 'your key';
    const APP_SECRET = 'tour secret';
//发送验证码函数，传入手机号即可
    public function SendSmsCode($mobile = ""){
        $appKey = self::APP_KEY;
        $appSecret = self::APP_SECRET;
        $nonce = '100';
        $curTime = time();
        $checkSum = sha1($appSecret . $nonce . $curTime);
        $data  = array(
            'mobile'=> $mobile,
        );
        $data = http_build_query($data);
        $opts = array (
            'http' => array(
                'method' => 'POST',
                'header' => array(
                    'Content-Type:application/x-www-form-urlencoded;charset=utf-8',
                    "AppKey:$appKey",
                    "Nonce:$nonce",
                    "CurTime:$curTime",
                    "CheckSum:$checkSum"
                ),
                'content' =>  $data
            ),
        );
        $context = stream_context_create($opts);
        $html = file_get_contents("https://api.netease.im/sms/sendcode.action", false, $context);
        echo $html;
    }
//验证码校验函数，传入手机号，以及该手机号反馈给你的验证码，
    public function CheckSmsYzm($mobile = "",$Code=""){
        $appKey = self::APP_KEY;
        $appSecret = self::APP_SECRET;
        $nonce = '100';
        $curTime = time();
        $checkSum = sha1($appSecret . $nonce . $curTime);
        $data  = array(
            'mobile'=> $mobile,
            'code' => $Code,
        );
        $data = http_build_query($data);
        $opts = array (
            'http' => array(
                'method' => 'POST',
                'header' => array(
                    'Content-Type:application/x-www-form-urlencoded;charset=utf-8',
                    "AppKey:$appKey",
                    "Nonce:$nonce",
                    "CurTime:$curTime",
                    "CheckSum:$checkSum"
                ),
                'content' =>  $data
            ),
        );
        $context = stream_context_create($opts);
        $html = file_get_contents("https://api.netease.im/sms/verifycode.action", false, $context);
    }
}
```