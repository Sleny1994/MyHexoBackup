---
title: Get Epicor Customizations by SQL
date: 2023-06-19
tags: 
- ERP
- Epicor
- SQL
categories: 
- ERP
---

## 获取自定义的BAQ
```sql
select Company ,QueryID ,AuthorID ,Description ,DisplayPhrase ,IsGlobal
,IsShared ,SystemFlag ,Version ,Updatable ,SystemFlag 
from Ice.QueryHdr 
where SystemFlag = 0 and IsShared = 1 
order by QueryID, AuthorID
```

## 获取自定义的BPM
```sql
-- 待验证
select Company,
case when Source = 'BO' then 'BPM' 
     when Source = 'DB' then 'DataDirectives'
     when Source = 'DQ' then 'BAQMethod' 
else 'Other' end Source,
DirectiveGroup ,BpMethodCode ,DirectiveType ,Name ,[Order] ,
case when IsEnabled = 1 then 'InUse' else 'Obsolete' end [Status] ,Body 
from Ice.BpDirective 
where Thumbnail<>'' 
order by DirectiveGroup desc,BpMethodCode,[Order]
```

## 获取自定义的Dashboard
```sql
select Company ,ProductID ,DefinitionID ,Description ,DashBdVersion ,DataBaseVersion
,LastDeployedBy ,LastDeployedDate ,LastUpdatedBy ,LastUpdated
,DashboardSchema ,DashboardAssembly ,HasDashboardAssembly 
from Ice.DashBdDef 
where SystemFlag = 0 
order by DefinitionID
```

## 获取自定义的Customization Form
```sql
select Company ,ProductID ,TypeCode ,Key1 as CustIDOrName ,Description
,Key2 as AppForm ,LastUpdatedBy ,LastUpdated ,SysCharacter04 as [Status]
,CommentText
from Ice.XXXDef 
where SystemFlag = 0 and TypeCode in ('Customization') and Key1 <> 'CustomContextMenu'
Order by Key1
```

## 获取自定义的Quick Search
```sql
select Company ,QuickSearchID ,Description ,ExportID ,LikeDataFieldTableID ,LikeDataFieldName
,ReturnFieldTableID ,ReturnFieldName, CallFrom ,UserID ,Version ,IsShared ,BaseDefault
 from Ice.QuickSearch 
 where SystemFlag = 0 
 order by QuickSearchID
```

## 获取自定义的Posting Rules
```sql
select b.Company ,b.ACTTypeUID ,a.ACTRevisionUID ,b.DisplayName ,b.DetailedDescription
       ,a.RevisionCode ,a.Description ,a.RevisionStatus
       ,a.SendToReviewJournal ,a.IsDefault ,a.RType
     from Erp.ACTRevision as a
left join Erp.ACTType as b 
on a.ACTTypeUID = b.ACTTypeUID
where a.RevisionStatus = 'Active' and a.RevisionCode <> 'Base Std'
```

## 获取自定义的BAQ & WhereUsed
```sql
select a.Company ,a.QueryID ,a.AuthorID ,a.Description ,a.DisplayPhrase ,a.IsGlobal
       ,a.IsShared ,a.SystemFlag ,a.Version ,a.Updatable ,a.SystemFlag
       ,b.Description as WhereUsedQuickSearch
       ,c.ReportID as WhereUsedRptID
       ,c.Description as WhereUsedRptDesc
       ,c.SSRSReportName as SSRSReportName
       ,d.DefinitionID as WhereUsedDashBd
from Ice.QueryHdr as a
left join Ice.QuickSearch as b 
on a.QueryID = b.ExportID  
left join Ice.BAQReport as c 
on a.QueryID = c.BAQRptID
left join Ice.DashBdBAQ as d 
on a.QueryID = d.QueryID
where a.SystemFlag = 0 and a.IsShared = 1 
order by a.QueryID, a.AuthorID
```

## 获取UD Field
```sql
select SystemCode ,DataTableID ,DBTableName ,FieldName ,Seq ,Description ,DataType 
,UDRequired ,UDReadOnly ,FieldFormat
from Ice.ZDataField 
where DataTableID in (
    select DataTableID 
    from Ice.ZDataTable 
    where TableType='UD') 
    and FieldName not in ('UD_SysRevID','ForeignSysRowID')
```

[本文转载自CSDN][1]: https://blog.csdn.net/qq_28756875/article/details/110448110 