---
title: jTable导出到excel
date: 2016-06-11
tags: [Java,code]
description: 本文记录了笔者JavaSE课程学习中解决的一个问题。
---

实现将jtable导出到excel中，直接传入jtable和文件url就行。


```
/**
	 * 导出jtable的model到excel
	 * @param table 要导出的jtable
	 * @param file 要导出到的file
	 * @throws IOException IO异常
	 */
	public static void exportTable(JTable table, File file) throws IOException {
		try {
			OutputStream out = new FileOutputStream(file);
			TableModel model = table.getModel();
			WritableWorkbook wwb = Workbook.createWorkbook(out);
			// 创建字表，并写入数据
			WritableSheet ws = wwb.createSheet("中文", 0);
			// 添加标题
			for (int i = 0; i < model.getColumnCount(); i++) {
				jxl.write.Label labelN = new jxl.write.Label(i, 0, model.getColumnName(i));
				try {
					ws.addCell(labelN);
				} catch (RowsExceededException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				} catch (WriteException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			// 添加列
			for (int i = 0; i < model.getColumnCount(); i++) {
				for (int j = 1; j <= model.getRowCount(); j++) {
					jxl.write.Label labelN = new jxl.write.Label(i, j, model.getValueAt(j - 1, i).toString());
					try {
						ws.addCell(labelN);
					} catch (RowsExceededException e) {
						e.printStackTrace();
					} catch (WriteException e) {
						e.printStackTrace();
					}
				}
			}
			wwb.write();
			try {
				wwb.close();
			} catch (WriteException e) {
				e.printStackTrace();
			}
		} catch (FileNotFoundException e) {
			JOptionPane.showMessageDialog(null, "导入数据前请关闭工作表");
		}
	}
```