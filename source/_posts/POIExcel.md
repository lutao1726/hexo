---
title: SSM中使用POI实现excel的导入导出
date: 2017-08-27 09:00:32
categories: Java教程
tags: [SSM, Java, Mysql, Excel]

---
![Mou_icon](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1503914324302&di=8b3cc54efb7123cc827f49409e784cc2&imgtype=jpg&src=http%3A%2F%2Fimg4.imgtn.bdimg.com%2Fit%2Fu%3D3327704561%2C61497253%26fm%3D214%26gp%3D0.jpg)
<!-- more -->

环境:导入POI对应的包
---------------------
环境：
> Spring+SpringMVC+Mybatis

POI对应的包
``` xml
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>3.14</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml-schemas</artifactId>
    <version>3.14</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>3.14</version>
</dependency>
```
ExcelBean数据封装
------------------------
ExcelBean.java：
```java

/**
 * Created by LT on 2017-08-23.
 */
public class ExcelBean implements  java.io.Serializable{
    private String headTextName; //列头（标题）名
    private String propertyName; //对应字段名
    private Integer cols; //合并单元格数
    private XSSFCellStyle cellStyle;
    public ExcelBean(){
    }
    public ExcelBean(String headTextName, String propertyName){
        this.headTextName = headTextName;
        this.propertyName = propertyName;
    }
    public ExcelBean(String headTextName, String propertyName, Integer cols) {
        super();
        this.headTextName = headTextName;
        this.propertyName = propertyName;
        this.cols = cols;
    }
    public String getHeadTextName() {
        return headTextName;
    }

    public void setHeadTextName(String headTextName) {
        this.headTextName = headTextName;
    }

    public String getPropertyName() {
        return propertyName;
    }
}    
```
导入导出工具类
--------------------------
ExcelUtil.java
```java
/**
 * Created by LT on 2017-08-23.
 */
public class ExcelUtil {
    private final static String excel2003L =".xls";    //2003- 版本的excel
    private final static String excel2007U =".xlsx";   //2007+ 版本的excel
    /**
     * Excel导入
     */
    public static  List<List<Object>> getBankListByExcel(InputStream in, String fileName) throws Exception{
        List<List<Object>> list = null;
        //创建Excel工作薄
        Workbook work = getWorkbook(in,fileName);
        if(null == work){
            throw new Exception("创建Excel工作薄为空！");
        }
        Sheet sheet = null;
        Row row = null;
        Cell cell = null;
        list = new ArrayList<List<Object>>();
        //遍历Excel中所有的sheet
        for (int i = 0; i < work.getNumberOfSheets(); i++) {
            sheet = work.getSheetAt(i);
            if(sheet==null){continue;}
            //遍历当前sheet中的所有行
            //包涵头部，所以要小于等于最后一列数,这里也可以在初始值加上头部行数，以便跳过头部
            for (int j = sheet.getFirstRowNum(); j <= sheet.getLastRowNum(); j++) {
                //读取一行
                row = sheet.getRow(j);
                //去掉空行和表头
                if(row==null||row.getFirstCellNum()==j){continue;}
                //遍历所有的列
                List<Object> li = new ArrayList<Object>();
                for (int y = row.getFirstCellNum(); y < row.getLastCellNum(); y++) {
                    cell = row.getCell(y);
                    li.add(getCellValue(cell));
                }
                list.add(li);
            }
        }
        return list;
    }
    /**
     * 描述：根据文件后缀，自适应上传文件的版本
     */
    public static  Workbook getWorkbook(InputStream inStr,String fileName) throws Exception{
        Workbook wb = null;
        String fileType = fileName.substring(fileName.lastIndexOf("."));
        if(excel2003L.equals(fileType)){
            wb = new HSSFWorkbook(inStr);  //2003-
        }else if(excel2007U.equals(fileType)){
            wb = new XSSFWorkbook(inStr);  //2007+
        }else{
            throw new Exception("解析的文件格式有误！");
        }
        return wb;
    }
    /**
     * 描述：对表格中数值进行格式化
     */
    public static  Object getCellValue(Cell cell){
        Object value = null;
        DecimalFormat df = new DecimalFormat("0");  //格式化字符类型的数字
        SimpleDateFormat sdf = new SimpleDateFormat("yyy-MM-dd");  //日期格式化
        DecimalFormat df2 = new DecimalFormat("0.00");  //格式化数字
        switch (cell.getCellType()) {
            case Cell.CELL_TYPE_STRING:
                value = cell.getRichStringCellValue().getString();
                break;
            case Cell.CELL_TYPE_NUMERIC:
                if("General".equals(cell.getCellStyle().getDataFormatString())){
                    value = df.format(cell.getNumericCellValue());
                }else if("m/d/yy".equals(cell.getCellStyle().getDataFormatString())){
                    value = sdf.format(cell.getDateCellValue());
                }else{
                    value = df2.format(cell.getNumericCellValue());
                }
                break;
            case Cell.CELL_TYPE_BOOLEAN:
                value = cell.getBooleanCellValue();
                break;
            case Cell.CELL_TYPE_BLANK:
                value = "";
                break;
            default:
                break;
        }
        return value;
    }
    /**
     * 导入Excel表结束
     * 导出Excel表开始
     * @param sheetName 工作簿名称
     * @param clazz  数据源model类型
     * @param objs   excel标题列以及对应model字段名
     * @param map  标题列行数以及cell字体样式
     */
    public static XSSFWorkbook createExcelFile(Class clazz, List objs, Map<Integer, List<ExcelBean>> map, String sheetName) throws
            IllegalArgumentException,IllegalAccessException,InvocationTargetException,
            ClassNotFoundException, IntrospectionException, ParseException {
        // 创建新的Excel工作簿
        XSSFWorkbook workbook = new XSSFWorkbook();
        // 在Excel工作簿中建一工作表，其名为缺省值, 也可以指定Sheet名称
        XSSFSheet sheet = workbook.createSheet(sheetName);
        // 以下为excel的字体样式以及excel的标题与内容的创建，下面会具体分析;
        createFont(workbook); //字体样式
        createTableHeader(sheet, map); //创建标题（头）
        createTableRows(sheet, map, objs, clazz); //创建内容
        return workbook;
    }
    private static XSSFCellStyle fontStyle;
    private static XSSFCellStyle fontStyle2;
    public static void createFont(XSSFWorkbook workbook) {
        // 表头
        fontStyle = workbook.createCellStyle();
        XSSFFont font1 = workbook.createFont();
        //font1.setBoldweight(XSSFFont.BOLDWEIGHT_BOLD);
        font1.setFontName("黑体");
        font1.setFontHeightInPoints((short) 11);// 设置字体大小
        fontStyle.setFont(font1);
        fontStyle.setBorderBottom(XSSFCellStyle.BORDER_THIN); // 下边框
        fontStyle.setBorderLeft(XSSFCellStyle.BORDER_THIN);// 左边框
        fontStyle.setBorderTop(XSSFCellStyle.BORDER_THIN);// 上边框
        fontStyle.setBorderRight(XSSFCellStyle.BORDER_THIN);// 右边框
        fontStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER); // 居中
        // 内容
        fontStyle2=workbook.createCellStyle();
        XSSFFont font2 = workbook.createFont();
        font2.setFontName("宋体");
        font2.setFontHeightInPoints((short) 12);// 设置字体大小
        fontStyle2.setFont(font2);
        fontStyle2.setBorderBottom(XSSFCellStyle.BORDER_THIN); // 下边框
        fontStyle2.setBorderLeft(XSSFCellStyle.BORDER_THIN);// 左边框
        fontStyle2.setBorderTop(XSSFCellStyle.BORDER_THIN);// 上边框
        fontStyle2.setBorderRight(XSSFCellStyle.BORDER_THIN);// 右边框
        fontStyle2.setAlignment(XSSFCellStyle.ALIGN_RIGHT); // 居中
    }
    /**
     * 根据ExcelMapping 生成列头（多行列头）
     *
     * @param sheet 工作簿
     * @param map 每行每个单元格对应的列头信息
     */
    public static final void createTableHeader(XSSFSheet sheet, Map<Integer, List<ExcelBean>> map) {
        int startIndex=0;//cell起始位置
        int endIndex=0;//cell终止位置
        for (Map.Entry<Integer, List<ExcelBean>> entry : map.entrySet()) {
            XSSFRow row = sheet.createRow(entry.getKey());
            List<ExcelBean> excels = entry.getValue();
            for (int x = 0; x < excels.size(); x++) {
                //合并单元格
                if(excels.get(x).getCols()>1){
                    if(x==0){
                        endIndex+=excels.get(x).getCols()-1;
                        CellRangeAddress range=new CellRangeAddress(0,0,startIndex,endIndex);
                        sheet.addMergedRegion(range);
                        startIndex+=excels.get(x).getCols();
                    }else{
                        endIndex+=excels.get(x).getCols();
                        CellRangeAddress range=new CellRangeAddress(0,0,startIndex,endIndex);
                        sheet.addMergedRegion(range);
                        startIndex+=excels.get(x).getCols();
                    }
                    XSSFCell cell = row.createCell(startIndex-excels.get(x).getCols());
                    cell.setCellValue(excels.get(x).getHeadTextName());// 设置内容
                    if (excels.get(x).getCellStyle() != null) {
                        cell.setCellStyle(excels.get(x).getCellStyle());// 设置格式
                    }
                    cell.setCellStyle(fontStyle);
                }else{
                    XSSFCell cell = row.createCell(x);
                    cell.setCellValue(excels.get(x).getHeadTextName());// 设置内容
                    if (excels.get(x).getCellStyle() != null) {
                        cell.setCellStyle(excels.get(x).getCellStyle());// 设置格式
                    }
                    cell.setCellStyle(fontStyle);
                }
            }
        }
    }
    public static void createTableRows(XSSFSheet sheet, Map<Integer, List<ExcelBean>> map, List objs, Class clazz)
            throws IllegalArgumentException, IllegalAccessException, InvocationTargetException, IntrospectionException,
            ClassNotFoundException, ParseException {
        int rowindex = map.size();
        int maxKey = 0;
        List<ExcelBean> ems = new ArrayList<ExcelBean>();
        for (Map.Entry<Integer, List<ExcelBean>> entry : map.entrySet()) {
            if (entry.getKey() > maxKey) {
                maxKey = entry.getKey();
            }
        }
        ems = map.get(maxKey);
        List<Integer> widths = new ArrayList<Integer>(ems.size());
        for (Object obj : objs) {
            XSSFRow row = sheet.createRow(rowindex);
            for (int i = 0; i < ems.size(); i++) {
                ExcelBean em = (ExcelBean) ems.get(i);
                // 获得get方法
                PropertyDescriptor pd = new PropertyDescriptor(em.getPropertyName(), clazz);
                Method getMethod = pd.getReadMethod();
                Object rtn = getMethod.invoke(obj);
                String value = "";
                // 如果是日期类型进行转换
                if (rtn != null) {
                    if (rtn instanceof Date) {
                        value = DateUtils.dateToString((Date)rtn);
                    } else if(rtn instanceof BigDecimal){
                        NumberFormat nf = new DecimalFormat("#,##0.00");
                        value=nf.format((BigDecimal)rtn).toString();
                    } else if((rtn instanceof Integer) && (Integer.valueOf(rtn.toString())<0 )){
                        value="--";
                    }else {
                        value = rtn.toString();
                    }
                }
                XSSFCell cell = row.createCell(i);
                cell.setCellValue(value);
                cell.setCellType(XSSFCell.CELL_TYPE_STRING);
                cell.setCellStyle(fontStyle2);
                // 获得最大列宽
                int width = value.getBytes().length * 300;
                // 还未设置，设置当前
                if (widths.size() <= i) {
                    widths.add(width);
                    continue;
                }
                // 比原来大，更新数据
                if (width > widths.get(i)) {
                    widths.set(i, width);
                }
            }
            rowindex++;
        }
        // 设置列宽
        for (int index = 0; index < widths.size(); index++) {
            Integer width = widths.get(index);
            width = width < 2500 ? 2500 : width + 300;
            width = width > 10000 ? 10000 + 300 : width + 300;
            sheet.setColumnWidth(index, width);
        }
    }
}/**
 * Created by LT on 2017-08-23.
 */
public class ExcelUtil {
    private final static String excel2003L =".xls";    //2003- 版本的excel
    private final static String excel2007U =".xlsx";   //2007+ 版本的excel
    /**
     * Excel导入
     */
    public static  List<List<Object>> getBankListByExcel(InputStream in, String fileName) throws Exception{
        List<List<Object>> list = null;
        //创建Excel工作薄
        Workbook work = getWorkbook(in,fileName);
        if(null == work){
            throw new Exception("创建Excel工作薄为空！");
        }
        Sheet sheet = null;
        Row row = null;
        Cell cell = null;
        list = new ArrayList<List<Object>>();
        //遍历Excel中所有的sheet
        for (int i = 0; i < work.getNumberOfSheets(); i++) {
            sheet = work.getSheetAt(i);
            if(sheet==null){continue;}
            //遍历当前sheet中的所有行
            //包涵头部，所以要小于等于最后一列数,这里也可以在初始值加上头部行数，以便跳过头部
            for (int j = sheet.getFirstRowNum(); j <= sheet.getLastRowNum(); j++) {
                //读取一行
                row = sheet.getRow(j);
                //去掉空行和表头
                if(row==null||row.getFirstCellNum()==j){continue;}
                //遍历所有的列
                List<Object> li = new ArrayList<Object>();
                for (int y = row.getFirstCellNum(); y < row.getLastCellNum(); y++) {
                    cell = row.getCell(y);
                    li.add(getCellValue(cell));
                }
                list.add(li);
            }
        }
        return list;
    }
    /**
     * 描述：根据文件后缀，自适应上传文件的版本
     */
    public static  Workbook getWorkbook(InputStream inStr,String fileName) throws Exception{
        Workbook wb = null;
        String fileType = fileName.substring(fileName.lastIndexOf("."));
        if(excel2003L.equals(fileType)){
            wb = new HSSFWorkbook(inStr);  //2003-
        }else if(excel2007U.equals(fileType)){
            wb = new XSSFWorkbook(inStr);  //2007+
        }else{
            throw new Exception("解析的文件格式有误！");
        }
        return wb;
    }
    /**
     * 描述：对表格中数值进行格式化
     */
    public static  Object getCellValue(Cell cell){
        Object value = null;
        DecimalFormat df = new DecimalFormat("0");  //格式化字符类型的数字
        SimpleDateFormat sdf = new SimpleDateFormat("yyy-MM-dd");  //日期格式化
        DecimalFormat df2 = new DecimalFormat("0.00");  //格式化数字
        switch (cell.getCellType()) {
            case Cell.CELL_TYPE_STRING:
                value = cell.getRichStringCellValue().getString();
                break;
            case Cell.CELL_TYPE_NUMERIC:
                if("General".equals(cell.getCellStyle().getDataFormatString())){
                    value = df.format(cell.getNumericCellValue());
                }else if("m/d/yy".equals(cell.getCellStyle().getDataFormatString())){
                    value = sdf.format(cell.getDateCellValue());
                }else{
                    value = df2.format(cell.getNumericCellValue());
                }
                break;
            case Cell.CELL_TYPE_BOOLEAN:
                value = cell.getBooleanCellValue();
                break;
            case Cell.CELL_TYPE_BLANK:
                value = "";
                break;
            default:
                break;
        }
        return value;
    }
    /**
     * 导入Excel表结束
     * 导出Excel表开始
     * @param sheetName 工作簿名称
     * @param clazz  数据源model类型
     * @param objs   excel标题列以及对应model字段名
     * @param map  标题列行数以及cell字体样式
     */
    public static XSSFWorkbook createExcelFile(Class clazz, List objs, Map<Integer, List<ExcelBean>> map, String sheetName) throws
            IllegalArgumentException,IllegalAccessException,InvocationTargetException,
            ClassNotFoundException, IntrospectionException, ParseException {
        // 创建新的Excel工作簿
        XSSFWorkbook workbook = new XSSFWorkbook();
        // 在Excel工作簿中建一工作表，其名为缺省值, 也可以指定Sheet名称
        XSSFSheet sheet = workbook.createSheet(sheetName);
        // 以下为excel的字体样式以及excel的标题与内容的创建，下面会具体分析;
        createFont(workbook); //字体样式
        createTableHeader(sheet, map); //创建标题（头）
        createTableRows(sheet, map, objs, clazz); //创建内容
        return workbook;
    }
    private static XSSFCellStyle fontStyle;
    private static XSSFCellStyle fontStyle2;
    public static void createFont(XSSFWorkbook workbook) {
        // 表头
        fontStyle = workbook.createCellStyle();
        XSSFFont font1 = workbook.createFont();
        //font1.setBoldweight(XSSFFont.BOLDWEIGHT_BOLD);
        font1.setFontName("黑体");
        font1.setFontHeightInPoints((short) 11);// 设置字体大小
        fontStyle.setFont(font1);
        fontStyle.setBorderBottom(XSSFCellStyle.BORDER_THIN); // 下边框
        fontStyle.setBorderLeft(XSSFCellStyle.BORDER_THIN);// 左边框
        fontStyle.setBorderTop(XSSFCellStyle.BORDER_THIN);// 上边框
        fontStyle.setBorderRight(XSSFCellStyle.BORDER_THIN);// 右边框
        fontStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER); // 居中
        // 内容
        fontStyle2=workbook.createCellStyle();
        XSSFFont font2 = workbook.createFont();
        font2.setFontName("宋体");
        font2.setFontHeightInPoints((short) 12);// 设置字体大小
        fontStyle2.setFont(font2);
        fontStyle2.setBorderBottom(XSSFCellStyle.BORDER_THIN); // 下边框
        fontStyle2.setBorderLeft(XSSFCellStyle.BORDER_THIN);// 左边框
        fontStyle2.setBorderTop(XSSFCellStyle.BORDER_THIN);// 上边框
        fontStyle2.setBorderRight(XSSFCellStyle.BORDER_THIN);// 右边框
        fontStyle2.setAlignment(XSSFCellStyle.ALIGN_RIGHT); // 居中
    }
    /**
     * 根据ExcelMapping 生成列头（多行列头）
     *
     * @param sheet 工作簿
     * @param map 每行每个单元格对应的列头信息
     */
    public static final void createTableHeader(XSSFSheet sheet, Map<Integer, List<ExcelBean>> map) {
        int startIndex=0;//cell起始位置
        int endIndex=0;//cell终止位置
        for (Map.Entry<Integer, List<ExcelBean>> entry : map.entrySet()) {
            XSSFRow row = sheet.createRow(entry.getKey());
            List<ExcelBean> excels = entry.getValue();
            for (int x = 0; x < excels.size(); x++) {
                //合并单元格
                if(excels.get(x).getCols()>1){
                    if(x==0){
                        endIndex+=excels.get(x).getCols()-1;
                        CellRangeAddress range=new CellRangeAddress(0,0,startIndex,endIndex);
                        sheet.addMergedRegion(range);
                        startIndex+=excels.get(x).getCols();
                    }else{
                        endIndex+=excels.get(x).getCols();
                        CellRangeAddress range=new CellRangeAddress(0,0,startIndex,endIndex);
                        sheet.addMergedRegion(range);
                        startIndex+=excels.get(x).getCols();
                    }
                    XSSFCell cell = row.createCell(startIndex-excels.get(x).getCols());
                    cell.setCellValue(excels.get(x).getHeadTextName());// 设置内容
                    if (excels.get(x).getCellStyle() != null) {
                        cell.setCellStyle(excels.get(x).getCellStyle());// 设置格式
                    }
                    cell.setCellStyle(fontStyle);
                }else{
                    XSSFCell cell = row.createCell(x);
                    cell.setCellValue(excels.get(x).getHeadTextName());// 设置内容
                    if (excels.get(x).getCellStyle() != null) {
                        cell.setCellStyle(excels.get(x).getCellStyle());// 设置格式
                    }
                    cell.setCellStyle(fontStyle);
                }
            }
        }
    }
    public static void createTableRows(XSSFSheet sheet, Map<Integer, List<ExcelBean>> map, List objs, Class clazz)
            throws IllegalArgumentException, IllegalAccessException, InvocationTargetException, IntrospectionException,
            ClassNotFoundException, ParseException {
        int rowindex = map.size();
        int maxKey = 0;
        List<ExcelBean> ems = new ArrayList<ExcelBean>();
        for (Map.Entry<Integer, List<ExcelBean>> entry : map.entrySet()) {
            if (entry.getKey() > maxKey) {
                maxKey = entry.getKey();
            }
        }
        ems = map.get(maxKey);
        List<Integer> widths = new ArrayList<Integer>(ems.size());
        for (Object obj : objs) {
            XSSFRow row = sheet.createRow(rowindex);
            for (int i = 0; i < ems.size(); i++) {
                ExcelBean em = (ExcelBean) ems.get(i);
                // 获得get方法
                PropertyDescriptor pd = new PropertyDescriptor(em.getPropertyName(), clazz);
                Method getMethod = pd.getReadMethod();
                Object rtn = getMethod.invoke(obj);
                String value = "";
                // 如果是日期类型进行转换
                if (rtn != null) {
                    if (rtn instanceof Date) {
                        value = DateUtils.dateToString((Date)rtn);
                    } else if(rtn instanceof BigDecimal){
                        NumberFormat nf = new DecimalFormat("#,##0.00");
                        value=nf.format((BigDecimal)rtn).toString();
                    } else if((rtn instanceof Integer) && (Integer.valueOf(rtn.toString())<0 )){
                        value="--";
                    }else {
                        value = rtn.toString();
                    }
                }
                XSSFCell cell = row.createCell(i);
                cell.setCellValue(value);
                cell.setCellType(XSSFCell.CELL_TYPE_STRING);
                cell.setCellStyle(fontStyle2);
                // 获得最大列宽
                int width = value.getBytes().length * 300;
                // 还未设置，设置当前
                if (widths.size() <= i) {
                    widths.add(width);
                    continue;
                }
                // 比原来大，更新数据
                if (width > widths.get(i)) {
                    widths.set(i, width);
                }
            }
            rowindex++;
        }
        // 设置列宽
        for (int index = 0; index < widths.size(); index++) {
            Integer width = widths.get(index);
            width = width < 2500 ? 2500 : width + 300;
            width = width > 10000 ? 10000 + 300 : width + 300;
            sheet.setColumnWidth(index, width);
        }
    }
}
```
Excel表导出
---------------------------
ExcelController.java
```java
/**
 * 上传excel并将内容导入数据库中
 *
 * @return
 */
@RequestMapping(value = "/import")
@Permission("login")

public Object importExcel(MultipartFile file, HttpServletRequest request) throws Exception {
    Map<String, Object> map = new HashMap<String, Object>();
    try {
        if (request.getSession().getAttribute("userName") == null || request.getSession().getAttribute("userName").toString().isEmpty()) {
            map.put("code", "20000");
            map.put("mes", "请先登录再进行操作！！！");
            return map;
        }
        System.out.println(file.getOriginalFilename());
        InputStream in = file.getInputStream();
        List<List<Object>> listob = ExcelUtil.getBankListByExcel(in, file.getOriginalFilename());
        List<Inventory> inventoryList = new ArrayList<Inventory>();
        String createBy = request.getSession().getAttribute("userName").toString();
        //遍历listob数据，把数据放到List中
        for (int i = 0; i < listob.size(); i++) {
            List<Object> ob = listob.get(i);
            Inventory inventory = new Inventory();
            //通过遍历实现把每一列封装成一个model中，再把所有的model用List集合装载
            inventory.setCompany(String.valueOf(ob.get(0)).trim());
            inventory.setArea(String.valueOf(ob.get(1)).trim());
            inventory.setWarehouse(String.valueOf(ob.get(2)).trim());
            inventory.setWarehouseName(String.valueOf(ob.get(3)).trim());
            inventory.setStoreAttributes(String.valueOf(ob.get(4)).trim());
            inventory.setMaterialBig(String.valueOf(ob.get(5)).trim());
            inventory.setMaterialMid(String.valueOf(ob.get(6)).trim());
            inventory.setMaterialSmall(String.valueOf(ob.get(7)).trim());
            inventory.setMaterialModel(String.valueOf(ob.get(8)).trim());
            inventory.setMaterialCode(String.valueOf(ob.get(9)).trim());
            inventory.setMaterialTips(String.valueOf(ob.get(10)).trim());
            inventory.setServiceAttribute(String.valueOf(ob.get(11)).trim());
            inventory.setPlanner(String.valueOf(ob.get(12)).trim());
            inventory.setSales(String.valueOf(ob.get(13)).trim());
            inventory.setEndingCount(String.valueOf(ob.get(14)).trim());
            inventory.setTransferin(String.valueOf(ob.get(15)).trim());
            inventory.setInventory(String.valueOf(ob.get(16)).trim());
            inventory.setCreateTime(new Date());
            inventory.setCreateBy(createBy);
            inventoryList.add(inventory);
        }
        //批量插入
        inventoryService.insertInfoBatch(inventoryList);
    } catch (Exception e) {
        LogUtil.error("ExcelController-----importExcel:" + e.getMessage());
        map.put("code", "30000");
        map.put("mes", "上传异常");
        return map;
    }
    map.put("code", "10000");
    map.put("mes", "上传成功");
    map.put("url","user/crud");
    LogUtil.info("ExcelController-----importExcel:" + map.toString());
    return map;
}
```
Excel表导出
-----------------------
ExcelController.java
```java
/**
 * 将数据库中的数据导出为excel
 *
 * @return
 */
@RequestMapping("/output")
@Permission("login")
@ResponseBody

public Object outputExcel(HttpServletRequest request, HttpServletResponse response) {

    response.reset(); //清除buffer缓存
    Map<String, Object> map = new HashMap<String, Object>(), TempMap = new HashMap<String, Object>();
    System.out.println("startDate:"+request.getParameter("startDate"));
    System.out.println("endDate:"+request.getParameter("endDate"));
    try {
        if (request.getSession().getAttribute("userName") == null || request.getSession().getAttribute("userName").toString().isEmpty()) {
            map.put("code", "20000");
            map.put("mes", "请先登录再进行操作！！！");
            return map;
        }
        String fileName = DateUtils.getCurrentDate() + "~";
        if (request.getParameter("startDate") != null&& !"".equals(request.getParameter("startDate"))) {
            TempMap.put("startDate", request.getParameter("startDate"));

            fileName = DateUtils.formatString(request.getParameter("startDate"))+ "~";
        }
        if (request.getParameter("endDate") != null&&!"".equals(request.getParameter("endDate"))) {
            TempMap.put("endDate", request.getParameter("endDate"));
            fileName =fileName+ DateUtils.formatString(request.getParameter("endDate"));
        } else {
            fileName = fileName + DateUtils.dateToString(new Date());
        }

        // 指定下载的文件名
        response.setHeader("Content-Disposition", "attachment;filename=" + fileName + ".xlsx");
        response.setContentType("application/vnd.ms-excel;charset=UTF-8");
        response.setHeader("Pragma", "no-cache");
        response.setHeader("Cache-Control", "no-cache");
        response.setDateHeader("Expires", 0);
        List<Inventory> list = inventoryService.getList(TempMap);
        List<ExcelBean> excel = new ArrayList<ExcelBean>();
        Map<Integer, List<ExcelBean>> mapExcel = new LinkedHashMap<Integer, List<ExcelBean>>();
        XSSFWorkbook xssfWorkbook = null;
        //设置标题栏
        excel.add(new ExcelBean("行政公司", "company", 0));
        excel.add(new ExcelBean("区域", "area", 0));
        excel.add(new ExcelBean("门店-仓库", "warehouse", 0));
        excel.add(new ExcelBean("门店-仓库名称", "warehouseName", 0));
        excel.add(new ExcelBean("门店属性", "storeAttributes", 0));
        excel.add(new ExcelBean("物料大类", "materialBig", 0));
        excel.add(new ExcelBean("物料中类(手机制式)", "materialMid", 0));
        excel.add(new ExcelBean("物料小类", "materialSmall", 0));
        excel.add(new ExcelBean("物料型号", "materialModel", 0));
        excel.add(new ExcelBean("物料编码", "materialCode", 0));
        excel.add(new ExcelBean("物料说明", "materialTips", 0));
        excel.add(new ExcelBean("业务属性", "serviceAttribute", 0));
        excel.add(new ExcelBean("计划员", "planner", 0));
        excel.add(new ExcelBean("销量", "sales", 0));
        excel.add(new ExcelBean("期末数量", "endingCount", 0));
        excel.add(new ExcelBean("调拨在途", "transferin", 0));
        excel.add(new ExcelBean("库存", "inventory", 0));
        mapExcel.put(0, excel);
        String sheetName = fileName + "天翼库存表";
        xssfWorkbook = ExcelUtil.createExcelFile(Inventory.class, list, mapExcel, sheetName);
        OutputStream output;
        try {
            output = response.getOutputStream();
            BufferedOutputStream bufferedOutPut = new BufferedOutputStream(output);
            bufferedOutPut.flush();
            xssfWorkbook.write(bufferedOutPut);
            bufferedOutPut.close();
        } catch (IOException e) {
            LogUtil.error("ExcelController-----outputExcel:" + e.getMessage());
            e.printStackTrace();
            map.put("code", "30000");
            map.put("mes", "导出异常");
            return map;
        }
    } catch (Exception e) {
        LogUtil.error("ExcelController-----outputExcel:" + e.getMessage());
        map.put("code", "30000");
        map.put("mes", "导出异常");
        return map;
    }
    map.put("code", "10000");
    map.put("mes", "导出成功");
    LogUtil.info("ExcelController-----outputExcel:" + map.toString());
    return map;
}
```
mapper.xml配置
------------------------
InventoryMapping.xml
```xml
<insert id="insertInfoBatch" parameterType="java.util.List">
    insert into inventory (
    company, area,warehouse, warehouseName, storeAttributes,materialBig,
    materialMid, materialSmall, materialModel, materialCode, materialTips,serviceAttribute,
    planner , sales, endingCount, transferin, inventory, createTime, createBy
    )
    values
    <foreach collection="list" item="item" index="index" separator=",">
        (
        #{item.company}, #{item.area}, #{item.warehouse},#{item.warehouseName}, #{item.storeAttributes}, #{item.materialBig},
        #{item.materialMid},#{item.materialSmall}, #{item.materialModel},#{item.materialCode}, #{item.materialTips}, #{item.serviceAttribute},
        #{item.planner}, #{item.sales}, #{item.endingCount},#{item.transferin}, #{item.inventory}, #{item.createTime}, #{item.createBy}
        )
    </foreach>
</insert>
```

`为了方便所以将service层的代码写到了Controller里了`

项目效果图
---------------------------------
