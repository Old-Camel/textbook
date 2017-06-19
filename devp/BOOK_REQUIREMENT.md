****



#教材需求管理



###1.获取页面分页信息



#### 请求url | /textbook/storage/queryListForPage



#### 请求方法 POST



#### 请求参数



| 参数|参数值|说明|
| :---|:---|:---|
|start|0|开始数据行|
|limit|20|每页显示行数|
|field|user/signed/deptName/isAdmin/realName|查询条件|



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
								   HttpSession session, String signed, String deptName, String isAdmin, String field, String value){
			try {
				  Criteria criteria= new Criteria();
				  BaseUsers user = (BaseUsers)session.getAttribute(
		 					WebConstants.CURRENT_USER);
				  /** 设置分页信息 */
				  if (pager.getLimit() != null && pager.getStart() != null) {
						criteria.setStart(pager.getStart());
						criteria.setLimit(pager.getLimit());
						criteria.setOracleStart(pager.getStart());
						criteria.setOracleEnd(pager.getStart() + pager.getLimit());
					}
				  if (user!=null) {
						criteria.put("user", user.getAccount());
					}
				  if (StringUtils.isNotEmpty(signed)) {
						criteria.put("signed", signed);
					}
				  if (StringUtils.isNotEmpty(deptName)) {
						criteria.put("deptName", deptName);
					}
				  if (StringUtils.isNotEmpty(isAdmin)) {
						criteria.put("isAdmin", isAdmin);
					}
				  
				  if (StringUtils.isNotEmpty(value)) {
						if (field.equals("deptName")) {
							criteria.put("deptName", value);
						}
						if (field.equals("realName")) {
							criteria.put("realName", value);
						}
					}
				  List<BookRequirement> list=bookRequirementService.queryListForPage(criteria);
				  int total=bookRequirementService.getTotalCount(criteria);
				  return new ExtGridReturn(total, list);
			} catch (Exception e) {
				e.printStackTrace();
				return new ExceptionReturn(e);
			}	
		
	}








```



###1.新增



#### 请求url | /textbook/supplier/queryListForPage/insert



#### 请求方法 POST



#### 请求参数



| 参数|说明|

| :---|:---|

|BookSupplier|插入数据信息|



#### 返回值



| 返回值|说明|
| :---|:---|
|ExtReturn|是否新增成功说明信息|



####代码



```java



添加 */
	@RequestMapping(value = "/insert", method = RequestMethod.POST)
	@ResponseBody
	public ExtReturn insert(BookRequirement bookRequirement,
							String [] bookIdArray, String [] applyCountArray, HttpSession session) {
		ExtReturn result = null;
		try {
			List<BookRequirementList> requirementList=new ArrayList<BookRequirementList>();
			
			/** 获取当前用户 */
			BaseUsers user = (BaseUsers)session.getAttribute(
 					WebConstants.CURRENT_USER);
			bookRequirement.setApplyUser(user);
			for(int i=0;i<bookIdArray.length;i++){
				BookRequirementList temp = new BookRequirementList();
				BookDetail book=new BookDetail();
				book.setId(Integer.parseInt(bookIdArray[i]));
				temp.setApplyCount(Integer.parseInt(applyCountArray[i]));
				temp.setBook(book);
				requirementList.add(temp);
			}
			bookRequirement.setRequirementList(requirementList);
			 int insert=bookRequirementService.insert(bookRequirement);
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



| 参数|说明|

| :---|:---|

|BookSupplier |供应商对象|

#### 返回值



| 返回值|说明|

| :---|:---|

|ExtReturn|是否删除成功说明信息|



####代码



```java

/** 删除 */
	@RequestMapping(value = "/delete", method = RequestMethod.POST)
	@ResponseBody
	public ExtReturn delete(int id) {
		ExtReturn result = null;
		try {
			
			int delete = bookRequirementService.delete(id);
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



| 参数|说明|
| :---|:---|
|BookSupplier |页面传递的修改后的供应商信息|



#### 返回值



| 返回值|说明|
| :---|:---|
|ExtReturn|是否修改成功说明信息|



####代码



```java



	/** 修改 */
	@RequestMapping(value = "/update", method = RequestMethod.POST)
	@ResponseBody
	public ExtReturn update(BookRequirement bookRequirement, 
			 String [] bookIdArray,String [] applyCountArray) {
		ExtReturn result = null;
		try {
			List<BookRequirementList> requirementList=new ArrayList<BookRequirementList>();
			if(bookIdArray.length!=0){
				for(int i=0;i<bookIdArray.length;i++){
					BookRequirementList temp = new BookRequirementList();
					BookDetail book=new BookDetail();
					book.setId(Integer.parseInt(bookIdArray[i]));
					temp.setApplyCount(Integer.parseInt(applyCountArray[i]));
					temp.setBook(book);
					requirementList.add(temp);
				}
				bookRequirement.setRequirementList(requirementList);
			}
			int update = bookRequirementService.update(bookRequirement);
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
###审核
| 参数|说明|
| :---|:---|
|status/id|申请状态/申请id|
#### 返回值
| 返回值|说明|
| :---|:---|
|ExtReturn|是否审核成功说明信息|

```java
/** 审核 */

	@RequestMapping(value = "/examine", method = RequestMethod.POST)
	@ResponseBody
	public ExtReturn audit(BookRequirement bookRequirement, HttpServletRequest request,int status,HttpSession session,int id) {
		ExtReturn result = null;
		try {
			bookRequirement.setStatus(status);
			bookRequirement.setId(id);
			
			/** 获取当前用户 */
			BaseUsers user = (BaseUsers)session.getAttribute(
 					WebConstants.CURRENT_USER);
			bookRequirement.setCheckUser(user);
			int update = bookRequirementService.examine(bookRequirement);
			if (update ==1) {
				result = new ExtReturn(true, " 操作成功");
			} else {
				result = new ExtReturn(false, "操作失败");
			}
			return result;
		} catch (Exception e) {
			LOGGER.error("修改信息出错", e);
			result = new ExtReturn(e);
			return result;
		}

	}
```
###提交
| 参数|说明|
| :---|:---|
|status/id|申请状态/申请id|
#### 返回值
| 返回值|说明|
| :---|:---|
|ExtReturn|是否审核成功说明信息|

```java

/**提交按钮**/
	@RequestMapping(value = "/sub", method = RequestMethod.POST)
	@ResponseBody
	public ExtReturn sub(BookRequirement bookRequirement, HttpServletRequest request,int status,HttpSession session,int id) {
		ExtReturn result = null;
		try {
			bookRequirement.setStatus(status);
			bookRequirement.setId(id);
		int update = bookRequirementService.sub(bookRequirement);
			if (update ==1) {
				result = new ExtReturn(true, " 操作成功");
			} else {
				result = new ExtReturn(false, "操作失败");
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






