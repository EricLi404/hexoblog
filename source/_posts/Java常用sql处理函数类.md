---
title: Java常用sql处理函数类
date: 2016-04-15
tags: [Java,code,database]
description: 本文记录了笔者学习Java过程中常用到的一个sql处理函数类。
---


java常用sql处理函数类

使用jdbc操作

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.HashMap;


//封装了数据库的增删改查操作

public class Sqlutil {
	/**
	 * 连接数据库 
	 * @param url
	 * @param user
	 * @param passwd
	 * @return
	 */
	public static Connection connect(String url, String user, String passwd) {
		Connection conn = null;
		try {
			Class.forName("com.mysql.jdbc.Driver");
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		try {
			conn = DriverManager.getConnection(url, user, passwd);
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return conn;
	}
	
/**
 *   查一条
 * @param conn
 * @param sql
 * @return
 */
	public static HashMap<String, String> fetchOne(Connection conn, String sql) {
		Statement stat = null;
		ResultSet res = null;
		HashMap<String, String> map = new HashMap<>();
		try {
			stat = conn.createStatement();
			res = stat.executeQuery(sql);
			int col = res.getMetaData().getColumnCount();
			res.next();
			for (int i = 1; i < col; i++) {
				map.put(res.getMetaData().getColumnName(i), res.getString(i));
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return map;
	}
	
	/**
	 *   查多条
	 * @param conn
	 * @param sql
	 * @return
	 */
	public static HashMap<Integer, HashMap<String, String>> fetchAll(Connection conn, String sql) {
		Statement stat = null;
		ResultSet res = null;
		HashMap<Integer,HashMap<String, String>> map = new HashMap<>();
		try {
			stat = conn.createStatement();
			res = stat.executeQuery(sql);
			int col = res.getMetaData().getColumnCount();
			int j = 0;
			while (res.next()) {
				HashMap<String, String> tmap = new HashMap<>();
				for (int i = 1; i < col; i++) {
					tmap.put(res.getMetaData().getColumnName(i), res.getString(i));
				}
				map.put(j, tmap);
				j++;
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return map;
	}
	
	/**
	 *    增删改  
	 * @param conn 
	 * @param sql
	 * @return
	 */
	public static int update(Connection conn, String sql) {
		Statement stat;
		int res = 0;
		try {
			stat = conn.createStatement();
			res = stat.executeUpdate(sql);
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return res;
	}
	
}

```