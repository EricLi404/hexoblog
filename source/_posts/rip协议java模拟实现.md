---
title: rip协议Java模拟实现
date: 2016-07-09
tags: [Java,code]
description: 本文记录了笔者大二下学期的计算机网络原理课程设计所做内容。
---

> 大二下学期的计算机网络原理课程设计


----------


### 已同步github
url:[https://github.com/EricLi404/Java-Demos/tree/master/Rip-Demo](https://github.com/EricLi404/Java-Demos/tree/master/Rip-Demo)
------
## 一、设计题目：距离矢量路由选择算法的模拟实现

## 二、设计要求
（1）掌握距离矢量路由选择选择算法的工作原理。
（2）采用C、Java等编程语言实现距离矢量路由选择选择算法。
（3）算法输入：
带权图G，如图1所示。
![这里写图片描述](http://img.blog.csdn.net/20160709003245099)
（4）算法输出：
 - 某个顶点v（路由器）在第i次周期后的整个路由表。
 - 路由表的结构<目的网络，跳数，下一跳>

## 三、设计思路
距离矢量路由选择选择算法有多种，本设计采用的是比较经典的RIP协议，根据设计要求，只要求输出路由器在第i次周期后的整个路由表，为突出设计要求，本设计简化了RIP协议，与RIPv1，RIPv2和RIPng相比，本设计精简了水平分割、毒性逆转和抑制计时功能，本设计采用手动触发更新，即：手动输入循环周期i次，然后执行i次更新，并输出结果。
系统中涉及到三个对象，network，router，routertable，分别表示网络空间，路由器，路由表。其中，系统一共具有1个网络，网络中包括至少一个路由器，每个路由器拥有一个路由表。
系统执行大致分为三个阶段：
阶段一，系统读取配置文件./config/network.properties，通过其中的配置项来初始化网络空间，建立路由表，建立邻接关系。
阶段二，系统提示用户输入周期i，用户将输入反馈给系统。
阶段三，系统根据用户输入，执行响应轮次的路由表更新，输出结果。
配置文件书写规则：
e.g. 

```
routernum=6     //路由器数量
networknum=7     //网络数量
0=0              //左侧表示路由器编号，右侧表示网络编号
1=0,1,3,4        //路由器和网络号都从0开始计数
2=1,2			//右侧为该路由器连接的网络，中间用英文","隔开
3=3,6
4=3
5=4,5

```

## 四、源码

```
//Index.java   主类

package rip;

import java.util.Scanner;

public class Index {
	public static void main(String[] args){
		Network network = new Network();
		network.initNetwork();
		network.setnearRouter();
		System.out.print("请输入循环轮次:");
		Scanner in = new Scanner(System.in);
		int i = in.nextInt();
		in.close();
		for(int j = 0; j < i; j++){
			System.out.println("=========================循环轮次"+(j+1)+"===========================");
			network.changeAllRouter();
		}
	}

}

```

```
//Network.java 网络空间类

package rip;

import java.util.HashMap;

public class Network {
	private  int   routernum;
	private  int   networknum;
	public Router[] routers;
	
	
	
	public int getRouternum() {
		return routernum;
	}

	public void setRouternum(int routernum) {
		this.routernum = routernum;
	}

	public int getNetworknum() {
		return networknum;
	}

	public void setNetworknum(int networknum) {
		this.networknum = networknum;
	}

	public Router[] getRouters() {
		return routers;
	}

	public void setRouters(Router[] routers) {
		this.routers = routers;
	}

	public Network() {
		super();
	}
	
	public void initNetwork(){
		HashMap<String, String[]> netconf = Util.readNetworkConfig();
		setRouternum(Integer.parseInt(netconf.get("routernum")[0]));
		setNetworknum(Integer.parseInt(netconf.get("networknum")[0]));
		//TODO 建立各路由器以及路由表       !important
		Router routerssss[] = new Router[routernum];
		for (int i = 0; i < routernum; i++) {
			HashMap<Integer, String[]> list = new HashMap<>();
			String[] strings = netconf.get(String.valueOf(i));
			for(int j = 0; j < strings.length;j++){
				String[] temp = {strings[j],"1", String.valueOf(i)};
				list.put(j, temp);
			}
			RouterTable routerTable = new RouterTable(list, i);
			Router router = new Router(i, routerTable);
			routerssss[i] = router;
		}
		setRouters(routerssss);
	}
	
	public void setnearRouter(){
		for (int i = 0; i < routers.length; i++) {
			String str1 = Util.intArray2string(routers[i].getNearNetwork());
			String str3 = "";
			for(int j = 0; j < routers.length; j++){
				String str2 = Util.intArray2string(routers[j].getNearNetwork());
				for(int k = 0; k < str2.length(); k++){
					if (str1.contains(str2.substring(k, k+1))) {
						str3 += j;
						break;
					}
				}
			}
			if (str3.length() > 0) {
				int p[] = new int[str3.length()];
				for (int j = 0; j < str3.length(); j++) {
					p[j] = Integer.parseInt(str3.substring(j, j+1));
				}
				routers[i].setNearRouter(p);
			}
		}
	}
	
	public void changeAllRouter(){
		//TODO for 循环，每个路由器获取其临近路由器的路由表，然后更新自己的路由表
		for(int i = 0; i < routers.length; i++){
//			System.out.println("外循环********************路由器"+i+"开始获取路由表");
			for(int j = 0; j < routers[i].getNearRouter().length; j++){
//				System.out.println("内循环********************路由器了获取路由表"+j);
				routers[i].changeRouterTable(routers[routers[i].getNearRouter()[j]].getRouterTable());
			}
			routers[i].echoRoutertable();
		}
	}

}

```

```
// Router.java 路由器类

package rip;

import java.util.HashMap;

public class Router {
	private  final int routerId;
	private RouterTable routerTable;
	private int[] nearRouter;
	private int[] nearNetwork;



	public RouterTable getRouterTable() {
		return routerTable;
	}
	public void setRouterTable(RouterTable routerTable) {
		this.routerTable = routerTable;
	}
	public void changeRouterTable(RouterTable otherRouterTable){
		// 规则：
				// 1.  传入的为其临近路由器的路由表
				// 2. 解析路由表
				// 3. 如果有自己路由表里有的网络，检查跳数是否为15，不为15进行以下操作
				// 4. 如果“跳数+1” 小于 本路由表对应条目的跳数，则修改记录
				// 5. 修改记录 “网络号(不变)”，“跳数+1”,“下一跳路由(该路由条目的来源路由)” 
				// 6. else如果有本自己路由表里没有的网路，检查跳数是否为15，不为15进行以下操作
				// 7. 添加记录 “网络号”，“跳数+1”,“下一跳路由(该路由条目的来源路由)”
		
		
		//将自己的路由表里 所有的网络号建立一个String，所有的跳数建立一个String，
		//遍历外来路由表的各个网络号，判断是否存在相同的值
		//如果存在 判断跳数（1.是否不等于15，2，是否小于自己对应的跳数+1）
		//          如果满足，修改路由表对应数据
		//			 如果不满足。do nothing
		//如果不存在，判断跳数是否不等于15，
		//            如果满足，添加该路由条目
				
			HashMap<Integer, String[] > otherList = otherRouterTable.getList();
			HashMap<Integer, String[] > selfList = routerTable.getList();
			String otherNetnum = "";
			String otherTiaonum = "";
			String selfNetnum = "";
			String selfTiaonum = "";
			String selfShouldMod = "";
			String otherShouldMod = "";
			String shouldAdd = "";
			for(int i = 0 ; i < otherList.size(); i++){
				otherNetnum += otherList.get(i)[0];
				otherTiaonum += otherList.get(i)[1];
			}
			for(int i = 0; i < selfList.size(); i++){
				selfNetnum += selfList.get(i)[0];
				selfTiaonum += selfList.get(i)[1];
			}
			for(int i = 0; i < otherNetnum.length(); i++){
//				System.out.println("第"+i+"循环检验========================");
				int res = selfNetnum.indexOf(otherNetnum.substring(i,i+1));
				int p = Integer.parseInt(otherTiaonum.substring(i, i+1));
				if (res != -1) {
					int q = Integer.parseInt(selfTiaonum.substring(res, res+1));
					if (p < 15) {
						if ((p+1) < q ) {
							//TODO 修改路由表对应数据
//							System.out.println("premod======="+selfNetnum.substring(res, res+1)+"--------"+otherNetnum.substring(i,i+1));
							selfShouldMod += String.valueOf(res);
							otherShouldMod += String.valueOf(i);
						}
					}
				}else if (res == -1) {
					if (p < 15) {
						//TODO 添加该条目
//						System.out.println("preadd====="+otherNetnum.substring(i,i+1));
						shouldAdd += String.valueOf(i);
					}
				}else {
					System.err.println("core change err");
				}
			}
			if (selfShouldMod.length() > 0) {
				for(int i = 0; i < selfShouldMod.length(); i++){
//					System.out.println("mod");
					selfList.remove(selfShouldMod.substring(i,i+1));
					String newChange[] = {
								otherList.get(Integer.parseInt(otherShouldMod.substring(i, i+1)))[0],
								String.valueOf(Integer.parseInt(otherList.get(Integer.parseInt(otherShouldMod.substring(i,i+1)))[1])+1),
								String.valueOf(otherRouterTable.getRouterID())
							}; 
					selfList.put(Integer.parseInt(selfShouldMod.substring(i,i+1)), newChange);
				}
			}
			if (shouldAdd.length() > 0) {
//				System.out.println("1111111111111self.size================="+selfList.size());
				int len = selfList.size();
				for(int i = 0; i < shouldAdd.length(); i++){
//					System.out.println("add");
					String newChange[] = {
							otherList.get(Integer.parseInt(shouldAdd.substring(i, i+1)))[0],
							String.valueOf(Integer.parseInt(otherList.get(Integer.parseInt(shouldAdd.substring(i,i+1)))[1])+1),
							String.valueOf(otherRouterTable.getRouterID())
						}; 
					selfList.put(len+i, newChange);
//					System.out.println("self.size================="+selfList.size());
				}
			}
			routerTable.setList(selfList);
			setRouterTable(routerTable);
	}
	public int[] getNearRouter() {
		return nearRouter;
	}
	
	

	public void setNearRouter(int[] nearRouter) {
		this.nearRouter = nearRouter;
	}

	public int[] getNearNetwork() {
		return nearNetwork;
	}

	public void setNearNetwork(int[] nearNetwork) {
		this.nearNetwork = nearNetwork;
	}

	public int getRouterId() {
		return routerId;
	}
	
	public void echoRoutertable(){
		RouterTable rtTables = getRouterTable(); 
		HashMap<Integer, String[]> list = rtTables.getList();
		System.out.println("*******路由器 "+getRouterTable().getRouterID()+" 路由表******");
		for (int i = 0; i < list.size(); i++) {
			String[] pStrings = list.get(i);
			System.out.println("网络："+pStrings[0]+"  |   "+"跳数："+pStrings[1]+"   |   "+"下一跳路由器： "+pStrings[2]);
		}
	}


	public Router(int routerId, RouterTable routerTable) {
		super();
		this.routerId = routerId;
		this.routerTable = routerTable;
		//TODO 记录临近的网络
		int[] p = new int[routerTable.getList().size()];
		for(int i = 0; i < routerTable.getList().size(); i++){
			p[i] = Integer.parseInt(routerTable.getList().get(i)[0]);
		}
		this.nearNetwork = p;
	}
	
	

}

```

```
// RouterTable.java  路由表类

package rip;

import java.util.HashMap;

public class RouterTable {
	private HashMap<Integer, String[] > list;
	private int routerID;
	
	public HashMap<Integer, String[]> getList() {
		return list;
	}

	public void setList(HashMap<Integer, String[]> list) {
		this.list = list;
	}

	public int getRouterID() {
		return routerID;
	}

	public void setRouterID(int routerID) {
		this.routerID = routerID;
	}
	
	

	public RouterTable(HashMap<Integer, String[]> list, int routerID) {
		super();
		this.list = list;
		this.routerID = routerID;
	}
}

```

```
// Util.java  通用函数类
package rip;

import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.Enumeration;
import java.util.HashMap;
import java.util.Properties;



public class Util {
	private final static String propertiesUrl = "./config/network.properties";
	

/**
 * 读取网络配置信息
 * ###网络配置信息相关文档见readme.md
 * @return hashmap
 */
	public static HashMap<String, String[]> readNetworkConfig(){
		HashMap<String, String[]> netconf = new HashMap<>();
		Properties Networkconfig = new Properties();
		try {
			Networkconfig.load(new FileInputStream(propertiesUrl));
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		 @SuppressWarnings("rawtypes")
		Enumeration enumm = Networkconfig.propertyNames();
		          while(enumm.hasMoreElements()) {
		              String strKey = (String) enumm.nextElement();
		              String strValue = Networkconfig.getProperty(strKey);
		              String[] strValueArray = strValue.split(",");
		              netconf.put(strKey, strValueArray);
		       }
		          return netconf;
	}
	
	/**
	 * 将int[]数组转为string
	 * @param intarr int[]
	 * @return string
	 */
	public static String intArray2string(int intarr[]){
		String string = "";
		for(int i = 0; i < intarr.length; i++){
			string += String.valueOf(intarr[i]);
		}
		return string;
	}
	
	public static String callCmd(String cmd) throws IOException, InterruptedException {
                // 使用Runtime来执行command，生成Process对象
                Process process = Runtime.getRuntime().exec(new String[] { "/bin/sh", "-c", cmd });
                // int exitCode = process.waitFor();
                // 取得命令结果的输出流
                InputStream is = process.getInputStream();
                // 用一个读输出流类去读
                InputStreamReader isr = new InputStreamReader(is);
                // 用缓冲器读行
                BufferedReader br = new BufferedReader(isr);
                String line = null;
                StringBuilder sb = new StringBuilder();
                while ((line = br.readLine()) != null) {
                    System.out.println(line);
                    sb.append(line);
                }
                is.close();
                isr.close();
                br.close();
                return sb.toString();
	}
	
         
}

```

 