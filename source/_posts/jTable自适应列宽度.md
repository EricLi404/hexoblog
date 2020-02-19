---
title: jTable自适应列宽度
date: 2016-06-12
tags: [Java,code]
description: 本文记录了笔者JavaSE课程学习中解决的一个问题。
---


使用：把要设置自适应宽度的jtable传入既可。
为了显示效果，每个列宽我还额外加了10px，如果想修改这个值，可以修改`column.setWidth(width + myTable.getIntercellSpacing().width + 10);`



	
	/**
	 * 使jtable自适应列宽度
	 * @param myTable
	 */
	public static void FitTableColumns(JTable myTable) {
		JTableHeader header = myTable.getTableHeader();
		int rowCount = myTable.getRowCount();

		Enumeration columns = myTable.getColumnModel().getColumns();
		while (columns.hasMoreElements()) {
			TableColumn column = (TableColumn) columns.nextElement();
			int col = header.getColumnModel().getColumnIndex(column.getIdentifier());
			int width = (int) myTable.getTableHeader().getDefaultRenderer()
					.getTableCellRendererComponent(myTable, column.getIdentifier(), false, false, -1, col)
					.getPreferredSize().getWidth();
			for (int row = 0; row < rowCount; row++) {
				int preferedWidth = (int) myTable.getCellRenderer(row, col)
						.getTableCellRendererComponent(myTable, myTable.getValueAt(row, col), false, false, row, col)
						.getPreferredSize().getWidth();
				width = Math.max(width, preferedWidth);
			}
			header.setResizingColumn(column);
			column.setWidth(width + myTable.getIntercellSpacing().width + 10);
		}
	}