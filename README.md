# Eas_Dep
EAS开发常用代码
author：frh
date： 2015-04-15
email： frh0792@qq.com
前台操作
保存校验
覆盖EditUI的verifyInput方法

校验表头字段

使用对象（推荐）

  if(editData.getNumber == null){ //如果编码为空
      MsgBox.showWarning(this,“编码不能为空”);//跳出对话框警告
      abort();//停止保存动作
  }
使用控件

  if(txtNumber.getText() == null){ //如果编码为空,一般都getValue
      MsgBox.showWarning(this,“编码不能为空”);//跳出对话框警告
      abort();//停止保存动作
  }
校验表体字段，循环，例如：表头实体Bill 表体叫 BillEntry,通过连接属性Entry连接

使用对象 迭代器模式(推荐)

  for (Iterator iterator = editData.getEntry().iterator(); iterator.hasNext();) {
      BillEntryInfo entry = (BillEntryInfo) iterator.next();
      if(entry.getSeq()==null){
          MsgBox.showWarning(this, "seq不能为空！");
      }
  }
使用控件

  for (int i=0; i<kdtEntry.getRowCount();i++){
      Integer seq = (Integer)kdtEntry.getRow(i).getCell("seq").getValue();
      if(seq==null){
          MsgBox.showWarning(this, "seq不能为空！");
      }
  }
监听操作
覆盖EditUI中的initListener方法

监听表格编辑事件 常用方法如下

单元格编辑

  kdtEntry.addKDTEditListener(new KDTEditAdapter() {
      @Override
      public void editStopped(KDTEditEvent e) {
          //编辑结束：一般用来触发取数、计算等操作
      }
      @Override
      public void editStarting(KDTEditEvent e) {
          //编辑开始：一般用来设置单元格中过滤
      }
  });
单元格新增行、插入行、删除行按钮事件

  //插入行
  kdtEntry_detailPanel.addInsertListener(new IDetailPanelListener() {
      public void afterEvent(DetailPanelEvent e) throws Exception {
          //插入之后
      }

      public void beforeEvent(DetailPanelEvent e) throws Exception {
          //插入之前
      }
  });
  //添加行
  kdtEntry_detailPanel.addAddListener(new IDetailPanelListener() {

      public void afterEvent(DetailPanelEvent e) throws Exception {
          //添加之后
      }

      public void beforeEvent(DetailPanelEvent e) throws Exception {
          //添加之前
      }
  });
  kdtEntry_detailPanel.addRemoveListener(new IDetailPanelListener() {

      public void afterEvent(DetailPanelEvent e) throws Exception {
          //删除之后
      }

      public void beforeEvent(DetailPanelEvent e) throws Exception {
          //删除之前
      }
  });
监听下拉框选择事件

监听

      comboBox.addItemListener(new ItemListener() {  

          public void itemStateChanged(ItemEvent e) {  
              comboBox_itemStateChanged(e);  
          }  
      });  
** 事件处理 ** 一定要如下代码，判断是否是SELECTED状态，要不会执行两次

      protected void comboBox_itemStateChanged(ItemEvent e) {   
          if (e.getStateChange() == ItemEvent.SELECTED  
                  && e.getSource() == comboBox) {  
              //System.out.println("comboBox");  
              //业务逻辑写此处
          }  
         }  
F7选择事件

选择之前

  prmtbizType.addSelectorListener(new SelectorListener(){
      public void willShow(SelectorEvent arg0) {
          // F7控件选择之前，
          //用途1：用来设置过滤条件
          //用途2：用来设置跳出的自定义F7界面
      }});
选择之后

  prmtbizType.addDataChangeListener(new DataChangeListener() {
      public void dataChanged(DataChangeEvent e) {
          //用来触发取数、计算等各种业务逻辑
      }
  });
日期控件

 pkBizDate.addDataChangeListener(new DataChangeListener() {

     public void dataChanged(DataChangeEvent arg0) {
         //日期改变逻辑
     }

 });
新增默认值
覆盖EditUI中createNewData方法

设置对象的值就可以了，例子如下

 protected com.kingdee.bos.dao.IObjectValue createNewData() {
     com.kingdee.eas.shine.sale.RTLPOrderInfo objectValue = new com.kingdee.eas.shine.sale.RTLPOrderInfo();//新增的对象
     objectValue.setCreator((com.kingdee.eas.base.permission.UserInfo) (com.kingdee.eas.common.client.SysContext.getSysContext().getCurrentUser()));//设置单据创建人为当前登录人
     objectValue.setBillStatus(BillStatusEnum.ADDNEW);//单据状态为新增
     objectValue.setInitBill(true);//设置初始化字段为是
     objectValue.setBizDate(new Date());//设置业务日期为当前日期
     return objectValue;
 }
如果默认值设置不起作用，则检查抽象类中的方法applyDefaultValue,如果有如下设置，则createNewData方法无论如何设置initBill字段都为True

 protected void applyDefaultValue(IObjectValue vo) {        
     vo.put("initBill",Boolean.TRUE);   
 } 
复制新增字段默认值
覆盖setFieldsNull方法，示例方法如下：

protected void setFieldsNull(com.kingdee.bos.dao.AbstractObjectValue arg0) {
        super.setFieldsNull(arg0);
        SaleCNoteInfo info = (SaleCNoteInfo) arg0;//强制转换对象为当前单据类型
        info.setNumber(null);//编码复制的时候情况
        info.setBillStatus(BillStatusEnum.ADDNEW);//单据设置为新增状态
        info.setAuditor(null);//审核人设置为空
        info.setCreator(SysContext.getSysContext().getCurrentUserInfo());//创建人设置为当前登录用户
}
初始化控件
一般初始化控件已经在抽象类中发布好了，但是有些还是需要初始化。比如人为将表格中的一列变为F7

final KDBizPromptBox kdtEntry_material_PromptBox = new KDBizPromptBox();
kdtEntry_material_PromptBox.setQueryInfo("com.kingdee.eas.basedata.master.material.app.F7MaterialBaseInfoQuery");
kdtEntry_material_PromptBox.setVisible(true);
kdtEntry_material_PromptBox.setEditable(true);
kdtEntry_material_PromptBox.setDisplayFormat("$number$");
kdtEntry_material_PromptBox.setEditFormat("$number$");
kdtEntry_material_PromptBox.setCommitFormat("$number$");
KDTDefaultCellEditor kdtEntry_material_CellEditor = new KDTDefaultCellEditor(kdtEntry_material_PromptBox);
this.kdtEntry.getColumn("material").setEditor(kdtEntry_material_CellEditor);
ObjectValueRender kdtEntry_material_OVR = new ObjectValueRender();
kdtEntry_material_OVR.setFormat(new BizDataFormat("$number$"));
this.kdtEntry.getColumn("material").setRenderer(kdtEntry_material_OVR);    
EditUI中方法总结
常用方法

initListener 监听初始化
loadFields 从数据库加载数据到界面
onLoad 界面加载后
onShow 界面显示后
getSelectors 需要从数据库取出来的数据
createNewData 新增默认值设置
storeFields 保存前调用，将控件上的数据同步到对象中
verifyInput 保存前校验
setFieldsNull复制单据字段默认值
常用按钮，可通过toolbar找到，不一一列举了

actionPrint_actionPerformed 打印
actionPrintPreview_actionPerformed 打印预览
actionEdit_actionPerformed 编辑
actionRemove_actionPerformed 删除
actionAddNew_actionPerformed 新增
actionCopy_actionPerformed 复制
F7控件设置过滤
设置F7过滤
监听F7的选择事件willShow

 prmtsaCustomer.addSelectorListener(new SelectorListener() {

     public void willShow(SelectorEvent e) {

         prmtsaCustomer_willShow(e);
     }

 });
设置过滤条件

 protected void prmtsaCustomer_willShow(SelectorEvent e) {
     Object obj = this.prmtogCustomer.getData();
     CustomerInfo ogCustomer = null;
     if (obj != null) {
         ogCustomer = (CustomerInfo) obj;
     }
     EntityViewInfo lpOrderDetailView = new EntityViewInfo();
     FilterInfo filter = new FilterInfo();
     filter.getFilterItems().add(new FilterItemInfo("currency.id", "xxxxxx"));
     prmtsaCustomer.setEntityViewInfo(view);
     prmtsaCustomer.getQueryAgent().resetRuntimeEntityView();
     System.out.println();
 }
设置表格F7过滤
监听表格editStarting事件

 this.kdtLPV.addKDTEditListener(new com.kingdee.bos.ctrl.kdf.table.event.KDTEditAdapter() {
         public void editStarting(com.kingdee.bos.ctrl.kdf.table.event.KDTEditEvent e) {
             try {
                 kdtLPV_editStarting(e);
             } catch(Exception exc) {
                 handUIException(exc);
             }
         }
     });
设置过滤条件

 protected void kdtLPV_editStarting(KDTEditEvent e) throws Exception {
     int rowIndex = e.getRowIndex();
     int colIndex = e.getColIndex();
     if (colIndex == kdtLPV.getColumnIndex("lpOrderDetail")) {
         EntityViewInfo lpOrderDetailView = new EntityViewInfo();
         FilterInfo filter = new FilterInfo();
         filter.getFilterItems().add(new FilterItemInfo("currency.id", "xxxxxx"));
         KDBizPromptBox prmtLPOrderdetail = (KDBizPromptBox) kdtLPV.getColumn("lpOrderDetail").getEditor().getComponent();
         prmtLPOrderdetail.setEntityViewInfo(view);
         prmtLPOrderdetail.getQueryAgent().resetRuntimeEntityView();
     }
 }
数据库对象操作
新增
new一个对象，设置对象的只，调用对象的addNew方法即可

BillStatusLogInfo billLog = new BillStatusLogInfo();//new一个对象
billLog.setOldStatus(oldStatus);//设置属性
billLog.setNewStatus(newStatus);
billLog.setCreateTime(new Timestamp(System.currentTimeMillis()));
billLog.setCreator(ContextUtil.getCurrentUserInfo(ctx));
billLog.setBosType(model.getBOSType().toString());
billLog.setBillId(billId);
billLog.setBillNumber(info.getNumber());
//调用对象插入数据库
BillStatusLogFactory.getLocalInstance(ctx).addnew(billLog);//后台调用，在ControllerBean中
BillStatusLogFactory.getRemoteInstance().addnew(billLog);//前台台调用，在UI中
查询
通过ID查找，返回指定对象
//定义一个selector 查找需要的列
SelectorItemCollection selector = new SelectorItemCollection();
selector.add(new SelectorItemInfo("auditor"));
selector.add(new SelectorItemInfo("auditTime"));
selector.add(new SelectorItemInfo("billStatus"));
//后台调用，在ControllerBean中
XYBillBaseInfo billBaseInfo = XYBillBaseFactory.getLocalInstance(ctx).getXYBillBaseInfo(pk,selector);
//前台台调用，在UI中
XYBillBaseInfo billBaseInfo = XYBillBaseFactory.getRemoteInstance().getXYBillBaseInfo(pk,selector);
以上代码相当于如下SQL语句：

select fauditorid,fauditTime,fbillStatus
  from T_XYBillBase
 where fid = 'id'
通过其他字段查找，返回数据集
EntityViewInfo evi = new EntityViewInfo();
SelectorItemCollection selector = evi.getSelector();
selector.add("number");
FilterInfo filter = new FilterInfo();
filter.getFilterItems().add(new FilterItemInfo("status", "2"));
//默认等于不用写，效果和下一句意义一样
//filter.getFilterItems().add(new FilterItemInfo("status", "2", CompareType.EQUALS));
filter.getFilterItems().add(new FilterItemInfo("age", "20", CompareType.LESS));
//前端UI中调用
ProduceDetailFactory.getRemoteInstance().getProduceDetailCollection(evi);
//后端在ControllerBean中调用
ProduceDetailFactory.getLocalInstance(ctx).getProduceDetailCollection(evi);
简单写法OQL，不太推荐

String oql = "select number where status=2 and age<20";
ProduceDetailFactory.getRemoteInstance().getProduceDetailCollection(oql);
相当于如下SQL"

select fnumber
  from T_ProduceDetail
 where fstatus = 2
   and fage < 20 //如果20是String，则自动加上 '20'
其他方法updatePartial，delete不一一介绍，每个对象均有这几个方法

写SQL语句
只支持T-SQL函数和语法

前端只能写Select语句
String sql = "select now()";//查询当前日期，T-SQL语法
//如果是要用oracle语法 则前面加上/*dialect*/
//String sql = "/*dialect*/select sysdate from dual";
IRowSet rs = SQLExecutorFactory.getRemoteInstance(sql.toString()).executeSQL();  
后端CRUD都可以
查询如下

String sql = "select now()";    
IRowSet rs = com.kingdee.eas.util.app.DbUtil.executeQuery(ctx, sql.toString());
更新删除添加

String sql = "insert|update|delete";    
com.kingdee.eas.util.app.DbUtil.execute(ctx, sql.toString());
也可以使用占位符

String sql = "select * from T where fnumber = ?";    
Object[] param = new Object[1];//几个问号就几个参数
param[0] = "123";
//建议用集合
// List param = new ArrayList();
// param.add("123");
// Object[] o = param.toArray();
IRowSet rs = com.kingdee.eas.util.app.DbUtil.executeQuery(ctx, sql.toString(),param);
附录
CompareType说明

CompareType.EQUALS = ("=");
CompareType.GREATER = (">");
CompareType.LESS = ("<");
CompareType.GREATER_EQUALS = (">=");
CompareType.LESS_EQUALS = ("<=");
CompareType.NOTEQUALS = ("<>");
CompareType.LIKE = ("like");
CompareType.NOTLIKE = ("not like");
CompareType.INCLUDE = ("in");
CompareType.NOTINCLUDE = ("not in");
CompareType.INNER = ("inner");
CompareType.NOTINNER = ("not inner");
CompareType.IS = ("is");
CompareType.ISNOT = ("is not");
CompareType.EXISTS = ("exists");
CompareType.NOTEXISTS = ("not exists");
CompareType.EMPTY = IS;
CompareType.NOTEMPTY = ISNOT;
