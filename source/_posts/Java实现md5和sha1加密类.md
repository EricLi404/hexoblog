---
title: Java实现md5和sha1加密类
date: 2016-04-14
tags: [Java,code]
description: 本文记录了笔者学习Java过程中常用到的一个md5和sha1加密类。
---

md5为32位大写字母格式，
sha1为40位大写字母格式，

```

import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;


/**
 * Created by leif on 2016/3/30 0030.
 */
public class utility {
	/**
	 *   实现32位大写字母格式的md5加密
	 * @param m
	 * @return
	 */
	public static String md5(String m) {
		MessageDigest md5;
		char hexDigits[] = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F' };
		String mi = new String();
		try {
			md5 = MessageDigest.getInstance("MD5");
			md5.update(m.getBytes());
			byte[] bb = md5.digest();
			int j = 0;
			char[] ll = new char[bb.length * 2];
			for (int i = 0; i < bb.length; i++) {
				byte nm = bb[i];
				ll[j++] = hexDigits[nm >>> 4 & 0xf];
				ll[j++] = hexDigits[nm & 0xf];
			}
			mi = new String(ll);
		} catch (NoSuchAlgorithmException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return mi;
	}
	/**
	 *   实现40位大写字母格式的SHA加密
	 * @param m
	 * @return
	 */
	public static String sha(String m) {
		MessageDigest md5;
		char hexDigits[] = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F' };
		String mi = new String();
		try {
			md5 = MessageDigest.getInstance("SHA");
			md5.update(m.getBytes());
			byte[] bb = md5.digest();
			int j = 0;
			char[] ll = new char[bb.length * 2];
			for (int i = 0; i < bb.length; i++) {
				byte nm = bb[i];
				ll[j++] = hexDigits[nm >>> 4 & 0xf];
				ll[j++] = hexDigits[nm & 0xf];
			}
			mi = new String(ll);
		} catch (NoSuchAlgorithmException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return mi;
	}
	//测试
	public static void main(String[] args) {
		String m = new String("admin"); 
		System.out.println(sha(md5(m)));
	}
}

```