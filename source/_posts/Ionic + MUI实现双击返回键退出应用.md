---
title: Ionic + MUI实现双击返回键退出应用
date: 2015-08-26
tags: code
description: 本文记录了笔者在使用 ionic 和 MUI 开发跨平台 移动app 时实现的双击返回键退出应用。
---

> 使用ionic+MUI联合开发app，鉴于ionic单方面实现的双击返回键过于复杂，所以想出了使用ionic与html5+API一起使用实现双击返回键退出应用，单击返回键返回上层的功能。


```
$scope.listenbackbutton = function() {
	document.addEventListener("plusready", onPlusReady, false);
		function onPlusReady() {
			plus.key.addEventListener("backbutton", function() {
				if ($location.path() == '/tab/index') {
					if (plus.os.name == "Android") {
						$ionicLoading.show({
							template: "再次点击返回键退出应用"
						});
							$timeout(function() {
							$ionicLoading.hide();
						plus.key.removeEventListener("backbutton", function() {});
							return;
							}, 1000);
						plus.key.addEventListener("backbutton", function() {
							plus.runtime.quit();
						});
				} else {
					outSet("此平台不支持直接退出程序，请按Home键切换应用");
					}
				} else {
					$ionicHistory.goBack();
			}
		});
	}
}
```