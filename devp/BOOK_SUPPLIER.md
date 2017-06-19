****

#图书供应商

###1.获取页面分页信息

#### 请求url | /textbook/supplier/queryListForPage

#### 请求方法 POST

#### 请求参数 

|  参数|参数值|说明|
| :---|:---|:---|
|start|0|开始数据行|
|limit|10|每页显示行数|
|field|supplierName/isdel/contractUser|查询条件|

#### 返回值

| 返回值|说明|
| :---|:---|
|ExtGridReturn|包含分页信息数据|

#### 代码

```java


/** 查询所有并分页 */
	@RequestMapping(value = "/queryListForPage", method = RequestMethod.POST)
	@ResponseBody
	public Object queryListForPage(ExtPager pager, HttpServletRequest request,
                                   HttpSession session, String field, String value, String isdel, String supplierName){
			try {
				  Criteria criteria= new Criteria();
				  /** 设置分页信息 */
				  if (pager.getLimit() != null && pager.getStart() != null) {
						criteria.setStart(pager.getStart());
						criteria.setLimit(pager.getLimit());
						criteria.setOracleStart(pager.getStart());
						criteria.setOracleEnd(pager.getStart() + pager.getLimit());
					}
				 
				  if (StringUtils.isNotEmpty(value)) {
						if (field.equals("supplierName")) {
							criteria.put("supplierName", value);
						}
						if (field.equals("contractUser")) {
							criteria.put("contractUser", value);
						}
					}
				  
				  if (StringUtils.isNotBlank(isdel)) {
					  criteria.put("isdel", isdel);
				}
					
				  if (StringUtils.isNotBlank(supplierName)) {
					  criteria.put("supplierName", supplierName);
				}
				  List<BookSupplier> list=bookSupplierService.queryListForPage(criteria);
				  int total=bookSupplierService.getTotalCount(criteria);
				  return new ExtGridReturn(total, list);
			} catch (Exception e) {
				e.printStackTrace();
				return new ExceptionReturn(e);
			}	
		
	}

```

###1.新增

#### 请求url | /textbook/supplier/insert

#### 请求方法 POST

#### 请求参数 

|  参数|说明|
| :---|:---|
|BookSupplier|插入数据信息|

#### 返回值

| 返回值|说明|
| :---|:---|
|ExtReturn|是否新增成功说明信息|

####代码

```java

/** 添加 */
	@RequestMapping(value = "/insert", method = RequestMethod.POST)
	@ResponseBody
	public ExtReturn insert(BookSupplier bookSupplier, HttpServletRequest request, HttpSession session) {
		ExtReturn result = null;
		try {
			/** 获取当前用户 */
			BaseUsers user = (BaseUsers)session.getAttribute(
 					WebConstants.CURRENT_USER);
			bookSupplier.setOperUser(user);
			 int insert=bookSupplierService.insert(bookSupplier);
			 if (insert>0) {
					result=new ExtReturn(true,"添加成功");
				}else {
					result=new ExtReturn(false,"添加失败");
				};
				return result;
		} catch (Exception e) {
			result = new ExtReturn(e);
			LOGGER.error("添加信息出错", e);
			return result;
		}
	}

```

###删除(删除供应商 修改供应商状态0:正常1：删除不显示)
#### 请求url | /textbook/supplier/delete

|  参数|说明|
| :---|:---|
|BookSupplier  |供应商对象|
#### 返回值

| 返回值|说明|
| :---|:---|
|ExtReturn|是否删除成功说明信息|

####代码

```java

 /** 删除 */
	@RequestMapping(value = "/delete", method = RequestMethod.POST)
	@ResponseBody
	public ExtReturn delete(BookSupplier  bookSupplier) {
		ExtReturn result = null;
		try {
			int delete = bookSupplierService.updateIsdel(bookSupplier);
			if (delete == 1) {
				result = new ExtReturn(true, "删除成功");
			} else {
				result = new ExtReturn(false, " 删除失败");
			}
			return result;
		} catch (Exception e) {
			LOGGER.error("删除信息出错", e);
			result = new ExtReturn(e);
			return result;
		}
	}

```

###修改

|  参数|说明|
| :---|:---|
|BookSupplier |页面传递的修改后的供应商信息|

#### 返回值

| 返回值|说明|
| :---|:---|
|ExtReturn|是否修改成功说明信息|

####代码

```java

public ExtReturn update(BookSupplier bookSupplier, HttpServletRequest request) {
		ExtReturn result = null;
		try {
			
			int update = bookSupplierService.update(bookSupplier);
			if (update == 1) {
				result = new ExtReturn(true, "修改成功");
			} else {
				result = new ExtReturn(false, "修改失败");
			}
			return result;
		} catch (Exception e) {
			LOGGER.error("修改信息出错", e);
			result = new ExtReturn(e);
			return result;
		}
	}
 

```
####数据库BOOK_SUPPLIER表结构
| 字段名|字段类型|说明|
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


