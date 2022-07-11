---
layout: post
title:  "管家婆工贸T系列22.0不允许负库存但是还有负库存问题"
color:  blue
width:   3
height:  1
date:   2022-07-10
categories: 工贸 ERP
excerpt_separator: <!--more-->
---
由于业务流程需要在入库前做销售单(草稿,并未审核过账),并且收款(实际为预收款)
<!--more-->
或者在生产完工前做领料单(草稿,并未审核过账),假设实际库存为0,允许负虚拟库存保存草稿.
接着在完工入库或者报溢入库后(可能是第二天),审核过账前一张销售的或者领料单,这时候是有
实际库存的,所以能过账成功,但是会造成库存明细表查询销售单或者领料单对应日期的结存库存
为负数,可能会影响成本的计算.所以需要用下面代码修复,原理为自动修改过账的销售的和领料单
单据日期/索引,并删除原来旧单据,对明细和其他无影响.
### 在数据库 企业管理器里 选择对应的数据库执行

{% highlight ruby %}

--gmT9top22 单据重写修复vchcode不是最新问题

-- 判断要创建的存储过程名是否存在
if exists (select * from dbo.sysobjects where id = object_id(N'[dbo].[_reovrridebill]') and OBJECTPROPERTY(id, N'IsProcedure') = 1) drop procedure [dbo]._reovrridebill

 
--
 
 

 
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER OFF
GO

create  PROCEDURE [dbo]._reovrridebill  
(  
	-- 重写 单据,修改vchcode为最大
	-- 单据type in 11,172
	@Vchcode Numeric(18,0) 
	,
	@Vchtype	int
	

)
AS 
	SET NOCOUNT ON
	if(@Vchtype not in (11,172))
	return
	CREATE TABLE #Tmp_dlyndx_new( Vchcode numeric(10, 0) NOT NULL, DATE nvarchar(10) NULL, NUMBER nvarchar(60) NULL, VchType int NOT NULL, summary nvarchar(256) NULL, btypeid nvarchar(25) NULL, etypeid nvarchar(25) NULL, ktypeid nvarchar(25) NULL, ktypeid2 nvarchar(25) NULL, ifcheck nvarchar(50) NULL, period int NOT NULL, RedWord nvarchar(1) NULL, RedOld nvarchar(1) NULL, InputNo nvarchar(100) NULL, draft int NOT NULL, Total numeric(18, 2) NOT NULL, GatheringDate nvarchar(10) NULL, projectid nvarchar(25) NULL, ETypeID2 nvarchar(25) NULL, SourceVchcode int NOT NULL, SourceVchType int NOT NULL, BillTotal numeric(18, 2) NOT NULL, WtypeID nvarchar(50) NULL, Compact nvarchar(100) NULL, UserDefined01 nvarchar(50) NULL, UserDefined02 nvarchar(50) NULL, UserDefined03 nvarchar(50) NULL, MatchQty numeric(18, 4) NOT NULL, AuditLeveal smallint NOT NULL, BTypeID2 nvarchar(25) NULL, ProjectId2 nvarchar(50) NULL, Pubufts timestamp NULL, SaveTime datetime NULL, DifAType int NOT NULL, Appoint tinyint NOT NULL, Acc int NOT NULL, NoteCode nvarchar(50) NOT NULL, isPreARAP tinyint NOT NULL, InvoiceType tinyint NOT NULL, CancelType tinyint NOT NULL, SettleType tinyint NOT NULL, NormCost tinyint NOT NULL, UserDefined04 nvarchar(50) NOT NULL, UserDefined05 nvarchar(50) NOT NULL, UserDefined06 nvarchar(50) NOT NULL, UserDefined07 nvarchar(50) NOT NULL, UserDefined08 nvarchar(50) NOT NULL, UserDefined09 nvarchar(50) NOT NULL, UserDefined10 nvarchar(50) NOT NULL, UserDefined11 nvarchar(10) NOT NULL, UserDefined12 nvarchar(10) NOT NULL, UserDefined13 nvarchar(10) NOT NULL, UserDefined14 numeric(18, 8) NOT NULL, UserDefined15 numeric(18, 8) NOT NULL, UserDefined16 numeric(18, 8) NOT NULL, SettleBtypeId nvarchar(50) NOT NULL, IsCheckOut bit NOT NULL, ModifyPerson nvarchar(100) NOT NULL, ModifyDate datetime NULL, LastYear tinyint NOT NULL, BuildType int NOT NULL, BuildNo int NOT NULL, IsAutoBuild tinyint NOT NULL, InstockBackSign tinyint NOT NULL, AuditDate datetime NULL, mtypeid nvarchar(50) NOT NULL, BillType int NOT NULL, IsRejected bit NOT NULL, ParVchtype int NOT NULL, DataCheck nvarchar(256) NULL) ON [PRIMARY]
-- 可无
CREATE TABLE dbo.#Tmp_T_CW_IntfDlyndx_new( Vchcode int NOT NULL, ModeNo tinyint NOT NULL, OriginVchcode int NOT NULL, Date nvarchar(10) NULL, VchType int NOT NULL, summary nvarchar(256) NULL, counttype int NOT NULL, VoucherNo int NOT NULL, period int NOT NULL, RedWord nvarchar(1) NULL, RedOld nvarchar(1) NULL, checke nvarchar(25) NULL, accounte nvarchar(25) NULL, InputNo nvarchar(100) NULL, draft tinyint NOT NULL, attach int NOT NULL, Total numeric(18, 2) NOT NULL, casherid nvarchar(25) NULL, cashfloweid nvarchar(25) NULL, cashflowlock int NOT NULL, CheckError nvarchar(25) NULL, WorkSign nvarchar(1) NULL, CWVchcode int NOT NULL, AutoOrNot nvarchar(1) NULL) ON [PRIMARY] 
	Declare @newvchcode int
  


set @newvchcode = (select MAX(vchcode)+1 from dlyndx)
if (@Vchcode = @newvchcode -1)
return
INSERT INTO #Tmp_dlyndx_new(Vchcode, DATE, NUMBER, VchType, summary , btypeid, etypeid, ktypeid, ktypeid2, ifcheck , period, RedWord, RedOld, InputNo, draft , Total, GatheringDate, projectid, ETypeID2, SourceVchcode , SourceVchType, BillTotal, WtypeID, Compact, UserDefined01 , UserDefined02, UserDefined03, MatchQty, AuditLeveal, BTypeID2 , ProjectId2, Pubufts, SaveTime, DifAType, Appoint , Acc, NoteCode, isPreARAP, InvoiceType, CancelType , SettleType, NormCost, UserDefined04, UserDefined05, UserDefined06 , UserDefined07, UserDefined08, UserDefined09, UserDefined10, UserDefined11 , UserDefined12, UserDefined13, UserDefined14, UserDefined15, UserDefined16 , SettleBtypeId, IsCheckOut, ModifyPerson, ModifyDate, LastYear , BuildType, BuildNo, IsAutoBuild, InstockBackSign, AuditDate , mtypeid, BillType, IsRejected, ParVchtype, DataCheck) SELECT Vchcode, DATE, NUMBER, VchType, summary , btypeid, etypeid, ktypeid, ktypeid2, ifcheck , period, RedWord, RedOld, InputNo, draft , Total, GatheringDate, projectid, ETypeID2, SourceVchcode , SourceVchType, BillTotal, WtypeID, Compact, UserDefined01 , UserDefined02, UserDefined03, MatchQty, AuditLeveal, BTypeID2 , ProjectId2, NULL, SaveTime, DifAType, Appoint , Acc, NoteCode, isPreARAP, InvoiceType, CancelType , SettleType, NormCost, UserDefined04, UserDefined05, UserDefined06 , UserDefined07, UserDefined08, UserDefined09, UserDefined10, UserDefined11 , UserDefined12, UserDefined13, UserDefined14, UserDefined15, UserDefined16 , SettleBtypeId, IsCheckOut, ModifyPerson, ModifyDate, LastYear , BuildType, BuildNo, IsAutoBuild, InstockBackSign, AuditDate , mtypeid, BillType, IsRejected, ParVchtype, DataCheck FROM dlyndx WHERE Vchcode = @Vchcode
update #Tmp_dlyndx_new set Vchcode = @newvchcode where Vchcode = @Vchcode
delete dlyndx where Vchcode = @Vchcode 



SET IDENTITY_Insert dlyndx ON 
INSERT INTO dlyndx(Vchcode, DATE, NUMBER, VchType, summary , btypeid, etypeid, ktypeid, ktypeid2, ifcheck , period, RedWord, RedOld, InputNo, draft , Total, GatheringDate, projectid, ETypeID2, SourceVchcode , SourceVchType, BillTotal, WtypeID, Compact, UserDefined01 , UserDefined02, UserDefined03, MatchQty, AuditLeveal, BTypeID2 , ProjectId2, Pubufts, SaveTime, DifAType, Appoint , Acc, NoteCode, isPreARAP, InvoiceType, CancelType , SettleType, NormCost, UserDefined04, UserDefined05, UserDefined06 , UserDefined07, UserDefined08, UserDefined09, UserDefined10, UserDefined11 , UserDefined12, UserDefined13, UserDefined14, UserDefined15, UserDefined16 , SettleBtypeId, IsCheckOut, ModifyPerson, ModifyDate, LastYear , BuildType, BuildNo, IsAutoBuild, InstockBackSign, AuditDate , mtypeid, BillType, IsRejected, ParVchtype, DataCheck) SELECT Vchcode, DATE, NUMBER, VchType, summary , btypeid, etypeid, ktypeid, ktypeid2, ifcheck , period, RedWord, RedOld, InputNo, draft , Total, GatheringDate, projectid, ETypeID2, SourceVchcode , SourceVchType, BillTotal, WtypeID, Compact, UserDefined01 , UserDefined02, UserDefined03, MatchQty, AuditLeveal, BTypeID2 , ProjectId2, NULL, SaveTime, DifAType, Appoint , Acc, NoteCode, isPreARAP, InvoiceType, CancelType , SettleType, NormCost, UserDefined04, UserDefined05, UserDefined06 , UserDefined07, UserDefined08, UserDefined09, UserDefined10, UserDefined11 , UserDefined12, UserDefined13, UserDefined14, UserDefined15, UserDefined16 , SettleBtypeId, IsCheckOut, ModifyPerson, ModifyDate, LastYear , BuildType, BuildNo, IsAutoBuild, InstockBackSign, AuditDate , mtypeid, BillType, IsRejected, ParVchtype, DataCheck FROM #Tmp_dlyndx_new WHERE Vchcode = @newVchcode
SET IDENTITY_Insert dlyndx Off
update dlyndx set DATE = CONVERT(varchar(100), AuditDate, 23) where vchcode = @newvchcode
update [DlyA] set vchcode = @newvchcode where vchcode = @Vchcode
update [DlySale] set Vchcode = @newvchcode where Vchcode =@Vchcode
update DlySale set date = (select CONVERT(varchar(100), AuditDate, 23) from Dlyndx where Dlyndx.vchcode = DlySale.vchcode) where DlySale.vchcode = @newvchcode
update DlyA set date = (select CONVERT(varchar(100), AuditDate, 23) from Dlyndx where Dlyndx.vchcode = DlyA.vchcode) where DlyA.vchcode = @newvchcode
update AuditProcess set Vchcode = @newvchcode where Vchcode = @Vchcode
update BillAuditProcessNdx set Vchcode = @newvchcode where Vchcode = @Vchcode
update BillAuditDetailRecord set Vchcode = @newvchcode where Vchcode = @Vchcode
update T_OA_NotReadMessage set Vchcode = @newvchcode where Vchcode = @Vchcode
update Dlyother set Vchcode = @newvchcode where Vchcode = @Vchcode
update T_SC_LLDDYRWD set Vchcode = @newvchcode where Vchcode = @Vchcode
update T_CW_IntfDlyndx set OriginVchcode = @newvchcode where OriginVchcode = @Vchcode

update ARSettles set Vchcode = @newvchcode where Vchcode = @Vchcode

update ARSettle set Vchcode = @newvchcode where Vchcode = @Vchcode

 -- select * from T_FIFO_OutList_Stock
drop table #Tmp_dlyndx_new
drop table #Tmp_T_CW_IntfDlyndx_new --
 
GO








GO
 SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
ALTER PROCEDURE [dbo].[P_BS_OA_SendSysMessage]
(
	@Receiver	nvarchar(100), --接收人
	@Content	nvarchar(500),--内容
	@Vchcode	int,			
	@Vchtype	int,
	@IsCallDll	INT,
	@Type	    TINYINT, --0 系统(15.8后无用)  1.普通消息  2.审核消息  3.预警消息  4.报警消息
	@CreateTime	datetime out
)  as
  SET NOCOUNT ON
set @CreateTime = Getdate()
insert into dbo.T_OA_NotReadMessage (
	CreateTime,
	Sender,
	Receiver,
	Content,
	[Type],	
	vchcode,
	vchtype
) VALUES ( 
	@CreateTime,
	'系统', --系统
	@Receiver,
	@Content,
	@Type,	 
	@Vchcode,
	@Vchtype ) 
if @@Error<>0 goto Error1
IF @IsCallDll = 1
BEGIN
	DECLARE @MsgXML  NVARCHAR(MAX)
		Select @MsgXML = '<Root><Record Sender = "系统" Receiver = "'+@Receiver+'" Content = "' + @Content + '" Type = "'+ CAST(@Type AS NVARCHAR) +'" ' +
				'vchcode = "' + CAST(@Vchcode AS NVARCHAR) +'" vchtype = "' + CAST(@Vchtype AS NVARCHAR) + '"/></Root>'
	Declare @PlugInProcName nVarchar(50)
	Declare my_cursor Cursor For Select ProcName From T_Inf_PlugIn_MessageToDll
	Open my_cursor
	Fetch Next From my_cursor Into @PlugInProcName
	While @@Fetch_Status = 0
	Begin
		Exec @PlugInProcName @MsgXML, NULL
		
		Fetch Next From my_cursor Into @PlugInProcName
	End
	Close my_cursor
	Deallocate my_cursor	
END

exec _reovrridebill @vchcode,@Vchtype -- fix
return SCOPE_IDENTITY()
Error1:
	return -1

{% endhighlight %}


