---
title: NPOI 自適應欄寬
categories: C#
tags: 
    - C#
date: 2023-05-16T21:11:57+08:00
---

``` csharp
/// <summary>
/// 自動適應列寬
/// </summary>
/// <param name="sheet">需要自適應列寬的sheet表</param>
/// <param name="columnCount">起始列數</param>
public static void AutoFitColumnWidth(ISheet sheet, int columnCount, int startRowIndex = 0)
{
    //列寬自適應，只對英文和數字有效
    for (int ci = 0; ci < columnCount; ci++)
    {
        sheet.AutoSizeColumn(ci);
    }
    //獲取當前列的寬度，然後對比本列的長度，取最大值
    for (int colIndex = 0; colIndex < columnCount; colIndex++)
    {
        int columnWidth = sheet.GetColumnWidth(colIndex) / 256;
        for (int rowNum = startRowIndex; rowNum < sheet.LastRowNum; rowNum++)
        {
            IRow currentRow;

            //當前行未被使用過
            if (sheet.GetRow(rowNum) == null)
            {
                currentRow = sheet.CreateRow(rowNum);
            }
            else
            {
                currentRow = sheet.GetRow(rowNum);
            }

            if (currentRow.GetCell(colIndex) != null)
            {
                ICell currentCell = currentRow.GetCell(colIndex);
                int length = Encoding.Default.GetBytes(currentCell.ToString()).Length;
                if (columnWidth < length)
                {
                    columnWidth = length;
                }
            }

            if (columnWidth > 255)
            {
                columnWidth = 255;
                break;
            }
        }

        sheet.SetColumnWidth(colIndex, columnWidth * 256);
    }
}
```