****

#入库

###1.获取页面分页信息

#### 请求url | /textbook/storage/queryListForPage

#### 请求方法 POST

#### 请求参数

| 参数|参数值|说明|
| :---|:---|:---|
|start|0|开始数据行|
|limit|20|每页显示行数|
|field|orderNo/orderName/startDate/realName|查询条件|
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
								   HttpSession session, String field, String value , Date todate, Date startDate){
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
						if (field.equals("orderNo")) {
							criteria.put("orderNo", value);
						}
						if (field.equals("orderName")) {
							criteria.put("orderName", value);
						}
				  
				  }
				  
				  
				  
			      
				 
				 
				  if (startDate!=null) {
					  /**时间加一天**/
					  Calendar rightNow = Calendar.getInstance();
				        rightNow.setTime(startDate);
				        rightNow.add(Calendar.DAY_OF_YEAR,1);//日期加1天
				        Date dt1=rightNow.getTime();
				        String  da= dFormat.format(dt1);
				        Date end = dFormat.parse(da);
				        
						criteria.put("today", startDate);
						criteria.put("end", end);
					}
				 
				  List<BookStorage> list=bookStorageService.queryListForPage(criteria);
				  int total=bookStorageService.getTotalCount(criteria);
				  return new ExtGridReturn(total, list);
			} catch (Exception e) {
				e.printStackTrace();
				return new ExceptionReturn(e);
			}	
		
	}
	

```

###1.新增

#### 请求url | /textbook/storage/insert

#### 请求方法 POST

#### 请求参数

| 参数|说明
| :---|:---|
|BookStorage |插入数据信息|

#### 返回值

| 返回值|说明|
| :---|:---|
|ExtReturn|是否新增成功说明信息|

####代码

```java

/** 添加 */
	@RequestMapping(value = "/insert", method = RequestMethod.POST)
	@ResponseBody
	public ExtReturn insert(BookStorage bookStorage, HttpServletRequest request, HttpSession session) {
		ExtReturn result = null;
		try {
			
			
			/** 获取当前用户 */
			BaseUsers user = (BaseUsers)session.getAttribute(
 					WebConstants.CURRENT_USER);
			bookStorage.setOperUser(user);
			
			 int insert=bookStorageService.insert(bookStorage);
			 if (insert>0) {
					
				 BookDetail bookDetail=bookDeatilService.queryById(bookStorage.getBook().getId());
					int totalCount=bookDetail.getTotalCount()+bookStorage.getStorageCount();
					BookDetail bDetail=new BookDetail();
					bDetail.setId(bookStorage.getBook().getId());
					bDetail.setTotalCount(totalCount);
					int update=bookDeatilService.update(bDetail);
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
#### 请求url | /textbook/storage/delete

| 参数|说明|
| :---|:---|
|id||

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
			int delete = bookStorageService.delete(id);
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
#### 请求url | /textbook/storage/update

| 参数|说明|
| :---|:---|
|BookStorage |页面传递的修改后的入库信息|

#### 返回值

| 返回值|说明|
| :---|:---|
|ExtReturn|是否修改成功说明信息|

####代码

```java

/** 修改 */
	@RequestMapping(value = "/update", method = RequestMethod.POST)
	@ResponseBody
	public ExtReturn update(BookStorage bookStorage, HttpServletRequest request) {
		ExtReturn result = null;
		try {
			
			int update = bookStorageService.update(bookStorage);
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

	

 ###数据库BOOK_STORAGE表结构

| 字段名|字段类型|说明|
| :---|:---|:---|
|ID           |   NUMBER(6)          |     入库编号|
|ORDER_NO     |   VARCHAR2(100 BYTE) |       订单编号|
|BOOK_ID      |   NUMBER(6)          |       图书ID|
|STORAGE_COUNT|   NUMBER(6)          |     入库数量|
|STORAGE_DATE |   DATE               |     入库时间|
|OPER_USER    |   VARCHAR2(50 BYTE)  |        操作人|


