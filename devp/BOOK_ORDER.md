****

#订单管理模块

###1.获取页面分页信息

#### 请求url | /textbook/order/queryListForPage

#### 请求方法 POST

#### 请求参数 

|  参数|参数值|说明|

| :---|:---|:---|

|start|0|开始数据行|

|limit|10|每页显示行数|

|field|orderName/orderNo/signed|查询条件|

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
								   HttpSession session, String signed, String orderNo, String orderName) {
		try {
			Criteria criteria = new Criteria();
			/** 设置分页信息 */
			if (pager.getLimit() != null && pager.getStart() != null) {
				criteria.setStart(pager.getStart());
				criteria.setLimit(pager.getLimit());
				criteria.setOracleStart(pager.getStart());
				criteria.setOracleEnd(pager.getStart() + pager.getLimit());
			}
			if (StringUtils.isNotBlank(signed)) {
				criteria.put("signed", signed);
			}
			if (StringUtils.isNotBlank(orderNo)) {
				criteria.put("orderNo", orderNo);
			}
			if (StringUtils.isNotBlank(orderName)) {
				criteria.put("orderName", orderName);
			}
			List<BookOrder> list = bookOrdertService.queryListForPage(criteria);
			int total = bookOrdertService.getTotalCount(criteria);
			return new ExtGridReturn(total, list);
		} catch (Exception e) {
			e.printStackTrace();
			return new ExceptionReturn(e);
		}

	}



```

###1.新增

#### 请求url | /textbook/detail/queryListForPage/insert

#### 请求方法 POST

#### 请求参数 

|  参数|说明|

| :---|:---|

|BookOrder |插入数据信息|

#### 返回值

| 返回值|说明|

| :---|:---|

|ExtReturn|是否新增成功说明信息|

####代码

```java

/** 添加 */
	@RequestMapping(value = "/insert", method = RequestMethod.POST)
	@ResponseBody
	public ExtReturn insert(BookOrder bookOrder, String [] bookIdArray,
							String[] orderCountArray, String[] priceArray, HttpSession session) {
		ExtReturn result = null;
		try {
			/** 获取当前用户 */
			BaseUsers user = (BaseUsers) session
					.getAttribute(WebConstants.CURRENT_USER);
			String orderNum = getOrderNum(user);
			bookOrder.setOperUser(user);
			bookOrder.setOrderNo(orderNum);
			List<BookOrderList> list = new ArrayList<BookOrderList>();
			for (int i = 0; i < orderCountArray.length; i++) {
				BookDetail book= new BookDetail();
				BookOrderList temp = new BookOrderList();
				book.setId(Integer.parseInt(bookIdArray[i]));
				temp.setBookPrice(Double.parseDouble(priceArray[i]));
				temp.setOrderCount(Integer.parseInt(orderCountArray[i]));
				temp.setOrderNo(orderNum);
				temp.setBook(book);
				list.add(temp);
			}
			bookOrder.setList(list);
			int insert = bookOrdertService.insert(bookOrder);
			if (insert > 0) {
				result = new ExtReturn(true, "添加成功");
			} else {
				result = new ExtReturn(false, "添加失败");
			}			
			return result;
		} catch (Exception e) {
			result = new ExtReturn(e);
			LOGGER.error("添加信息出错", e);
			return result;
		}
	}


```

###删除

|  参数|说明|

| :---|:---|

|id|图书id|

#### 返回值

| 返回值|说明|

| :---|:---|

|ExtReturn|是否删除成功说明信息|

####代码

```java

  /**

     * 删除

     */

    @RequestMapping(value = "/delete", method = RequestMethod.POST)

    @ResponseBody

    public ExtReturn delete(int id) {

        ExtReturn result = null;

        try {

            int delete = bookDeatilService.delete(id);

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

|BookDetail |页面传递的修改后的图书信息|

#### 返回值

| 返回值|说明|

| :---|:---|

|ExtReturn|是否修改成功说明信息|

####代码

```java

/**

     * 修改

     */

    @RequestMapping(value = "/update", method = RequestMethod.POST)

    @ResponseBody

    public ExtReturn update(BookDetail bookDetail, HttpServletRequest request) {

        ExtReturn result = null;

        try {

            int update = bookDeatilService.update(bookDetail);

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

###查询图书树数据

|  参数|说明|

| :---|:---|

|bookId|页面传递图书id|

#### 返回值

| 返回值|说明|

| :---|:---|

|ArrayList<Tree>|图书树数据|

####代码

```java

   /**

     * 图书树

     *

     */

    @RequestMapping(value = "/getBookTree", method = RequestMethod.POST)

    @ResponseBody

    public Object getUserTree(String bookId) {

        try {

            List<Tree> treeList = new ArrayList<Tree>();

            Tree tree = null;

            Criteria criteria = new Criteria();

            if (FormatUtil.isNotEmpty(bookId)) {

                criteria.put("bookId", bookId);

            }

            List<BookDetail> list = bookDeatilService.queryListForPage(criteria);

            for (BookDetail bookDetail : list) {

                String id = String.valueOf(bookDetail.getId());

                tree = new Tree();

                tree.setId(id);

                tree.setText(bookDetail.getBookName());

                tree.setLeaf(true);

                treeList.add(tree);

            }

            return treeList;

        } catch (Exception e) {

            LOGGER.error("分页获取信息出错", e);

            return new ExceptionReturn(e);

        }

    }

 

```

###数据库BOOK_DETAIL表结构

|  字段名|字段类型|说明|

| :---|:---|:---|

|ID | NUMBER(6) | ID||BOOK_NAME | VARCHAR2(50 BYTE) | 图书名称|

|AUTHOR | VARCHAR2(50 BYTE) | 作者|

|PUBLISHER | VARCHAR2(200 BYTE) | 出版社|

|PUBLISH_DATE | DATE | 出版日期|

|BOOK_BRIEF | VARCHAR2(500 BYTE) | 图书简介|

|OPER_USER | VARCHAR2(20 BYTE) | 操作人|

|OPER_DATE | DATE | 操作时间|

|TOTAL_COUNT | NUMBER(6) | 当前总册数|

|BOOK_ISBN | VARCHAR2(20 BYTE) | ISBN号码|

