---
title: Excel通过VBA+Outlook发送邮件
date: 2023-09-14
tags:
- VBA
categories:
---

## 实现步骤
1. 将需要实现该功能的Excel表另存为xlsm格式，仅该格式支持宏代码
2. 点击开发工具-插入-表单控件（按钮）
3. 在弹出的对话框中，点击新建
4. 在打开的Mircosoft Visual Basic For Application中编辑实现功能的代码
5. 保存后，点击该按钮即可实现对应功能

## 实现代码
```vb
Sub Test()
    Dim Mail As Outlook.Application
    Set Mail = New Outlook.Application
    Dim objMail As Outlook.MailItem
    Set objMail = Mail.CreateItem(olMailItem)
    Dim oRng As Range
    Dim oCell As Range
    Dim dtToday As Date
    Dim dtDiff As Integer
    Dim mtCells As New Collection
    
    '获取当前日期
    dtToday = Date  
    
    '指定日期所在的列范围
    Set oRng = Worksheets("Sheet1").Range("A2", "A6")
    
    '循环遍历，与当前时间进行比较，3天内到期的物料将发送邮件提醒
    For Each oCell In oRng
        dtDiff = DateDiff("d", dtToday, oCell.Value)
        If dtDiff <= 3 And dtDiff >= 0 Then
            Dim rng As Range
            Set rng = Cells(Right(oCell.Address, 1), "B") '通过日期所在行来获取物料所在的单元格
            sMsg = "物料 " & rng.Value & " " & "过期时间 " & oCell.Value & " " & "还有 " & dtDiff & "天！"
            mtCells.Add (sMsg)  '定义的集合，将循环的所有sMsg信息全部存储其中
        End If
    Next
    
    '遍历循环集合内的值，并将所有值拼接至result中
    Dim result As String
    Dim i As Long
    For i = 1 To mtCells.Count
        result = result & mtCells(i) & vbCrLf & vbCrLf
    Next i
    
    '发送邮件
    With objMail
        .Subject = "My Test Mail"             '主题
        .To = "1402271195@qq.com"             '收件人，多人使用分号隔开
        '.CC = "xxxxx@hotmail.com"            '抄送
        '.BCC = "xxxxx@sina.cn"               '密送
        '.BodyFormat = olFormatHTML           '邮件格式
        .Body = result                        '正文
        '.Attachments.Add "D:\RunLog.txt"     '附件
        .Display                              '预览显示，不发送
        '.Send                                '执行发送
    End With
    
    'MsgBox "发送成功！" & vbCrLf & "共用时：" & Timer - t & "秒", vbInformation + vbOKOnly
End Sub
```