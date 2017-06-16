****
#供应商信息管理
###1.模块说明
####供应商信息管理模块主要对各大教材供应商的信息进行保存，涵盖供应商的名称，法人代表，机构代码，具体地址，联系人，联系电话等信息。管理页面可以对各供应商信息进行修改和删除，可以根据输入的供应商名称或者练联系人姓名进行查询.![](/assets/2017-06-16_131538.png)
###2.数据库BOOK_SUPPLIER表结构

|  字段名|字段类型|说明|
| :---|:---|:---|
| NUMBER(6) | ID | 供应商ID|
| VARCHAR2(100 BYTE) | SUPPLIER_NAME | 供应商名|
| VARCHAR2(50 BYTE) | SUPPLIER_ORPORATE | 法人代表|
| VARCHAR2(50 BYTE) | ORGANIZE_CODE | 组织机构代码|
| VARCHAR2(200 BYTE) | SUPPLIER_ADDRESS | 供应商地址|
| VARCHAR2(20 BYTE) | CONTRACT_USER | 联系人|
| VARCHAR2(20 BYTE) | CONTRACT_PHONE | 联系电话|
| VARCHAR2(20 BYTE) | OPER_USER | 操作人|
| DATE | OPER_DATE | 操作时间|
| NUMBER(1) NULL | ISDEL | 0:正常 1:删除|
###3.页面操作流程
####1. 点击新增按钮,在弹出窗口填写供应商信息![](/assets/2017-06-16_131949.png)填写完毕点击提交.
####2. 选中一条记录,点击修改按钮,在弹出的窗口中对供应商信息进行修改.![](/assets/2017-06-16_132331.png)
####3. 选中一条记录,点击删除按钮进行删除 ![](/assets/2017-06-16_133250.png)
####4. 点击选择按钮,选择按照供应商名或者联系人,在搜索框中输入相应的信息进行搜索![](/assets/2017-06-16_133500.png)









