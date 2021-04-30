---
layout:     post
title:      抓取‘httpswww.cspea.com.cnlist’信息系统实现方案（实现翻页）
subtitle:   抓取‘httpswww.cspea.com.cnlist’信息系统实现方案（实现翻页）
date:       2021-04-26
author:     HLKT
header-img: https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/image-20210427164526187.png
catalog: true
tags:
    - Java
    - MySQL
---

# 抓取‘https://www.cspea.com.cn/list’信息系统实现方案（实现翻页）

> 开发环境 ： IDEA 2020.1   MySQL-5.6.50-winx64

[TOC]

## 网站信息分析

### 网页直观布局

![image-20210427164526187](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/image-20210427164526187.png)



通过网站信息可知：抓取信息为以上几部分

        ### 网页数据流监控

**下图红圈部分为第一页的数据请求返回包**

![image-20210427164725346](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/image-20210427164725346.png)

**也可通过WireShark进行抓包，获取Post请求信息**

![WireShark抓包](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/WireShark%E6%8A%93%E5%8C%85.png)

**从第二页开始，每次滚动翻页，返回的仅有一个包（偶尔会夹杂图片资源包），且其中含有所需数据**

![image-20210427164916772](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/image-20210427164916772.png)

* 下图为所需数据

![image-20210427165023358](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/image-20210427165023358.png)

## 网站信息抓取实现

### 获取请求头、请求参数

* 由网站流量监控可获取以上信息

  ![](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/image-20210427165148470.png)

### 通过Apache-Http.jar包实现信息的请求与获取

~~~java
//定义uri
            String uri = "https://www.cspea.com.cn/proxy/projectInterface/project/searchIndex";
            //需要传入的参数
            Map<String, String> map = new HashMap<String, String>();
            map.put("filter_projectText", "");
            map.put("filter_projectClassifyCode", "C02");
            map.put("filter_tagCode", "");
            map.put("filter_industryCode", "");
            map.put("filter_industryCodeTwo", "");
            map.put("filter_projectType", "");
            map.put("filter_tradInstitutionId", "");
            map.put("filter_zone", "");
            map.put("filter_groupZone", "");
            map.put("filter_minPrice", "");
            map.put("filter_maxPrice", "");
            map.put("filter_minTradeValue", "");
            map.put("filter_maxTradeValue", "");
            map.put("filter_minPercent", "");
            map.put("filter_maxPercent", "");
            map.put("filter_startDate", "");
            map.put("filter_endDate", "");
            map.put("filter_startTradeDate", "");
            map.put("filter_endTradeDate", "");
            map.put("filter_startPreDate", "");
            map.put("filter_endPreDate", "");
            map.put("filter_businessStatus", "A02,A03");
            map.put("filter_isGz", "");
            map.put("filter_isHot", "");
            map.put("filter_publishDateSort", "desc");
            map.put("filter_projectPriceSort", "");
            map.put("filter_tradeValueSort", "");
            map.put("filter_startExpireDate", "2021-04-21");
            map.put("filter_endExpireDate", "2099-01-01");
            map.put("pageIndex", temp);
            map.put("pageSize", "30");   //单页最大查询有数量限制， 为30 故放入循环多次抓取
            map.put("sysCode", "1");

            String encoding = "utf-8";
            //创建默认的httpclient
            CloseableHttpClient httpClient = HttpClients.createDefault();
            //创建post请求对象
            HttpPost httpPost = new HttpPost(uri);
            //装填请求参数
            List<NameValuePair> list = new ArrayList<NameValuePair>();
            for (Map.Entry<String, String> entry : map.entrySet()) {
                list.add(new BasicNameValuePair(entry.getKey(), entry.getValue()));
            }
            //设置参数到请求对象中
            httpPost.setEntity(new UrlEncodedFormEntity(list, encoding));

            //设置header信息
            //指定报文头【Content-type】、【User-Agent】
            httpPost.setHeader("Content-type", "application/x-www-form-urlencoded");
            httpPost.setHeader("User-Agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.114 Safari/537.36 Edg/89.0.774.76)");
            //执行请求操作，并拿到结果（同步阻塞）
            CloseableHttpResponse response = httpClient.execute(httpPost);
            //获取所有的请求头信息
            Header[] allHeaders = response.getAllHeaders();
            for (Header allHeader : allHeaders) {
                System.out.println(allHeader.toString());
            }
            //获取结果实体
            HttpEntity entity = response.getEntity();
~~~

### 通过对返回数据格式的分析，对数据进行解析

* 返回json格式如下，可知头部和尾部 "]" 皆为多余信息，应去除，否则影响Json的转换
* 所需信息仅为data中的
* data中多条json数据，通过**正则表达式**进行拆分
* 第10页开始，因为 pageIndex 由一位变为两位，故字符串裁剪需要多增一位

**返回数据格式**

![返回json格式](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/%E8%BF%94%E5%9B%9Ejson%E6%A0%BC%E5%BC%8F.png)

~~~java
//服务器返回json截取，只保留信息
                if (i >= 10) {
                    //第10页起返回json与其他页不同，中间多了一个空格，在此做特殊处理
                    ListInfo = StringEscapeUtils.unescapeJava(responseEntity.substring(47, responseEntity.length() - 66));
                }
                else {
                    ListInfo = StringEscapeUtils.unescapeJava(responseEntity.substring(47, responseEntity.length() - 65));
                }

                String regEx = "},";
                Pattern pattern = Pattern.compile(regEx);
                Matcher matcher = pattern.matcher(ListInfo);

                String ListInfoNeat[] = pattern.split(ListInfo);

                //json信息数组逐条分割
                if (ListInfoNeat.length > 0) {
                    int count = 0;
                    while (count < ListInfoNeat.length) {
                        if (matcher.find()) {
                            ListInfoNeat[count] += "}";
                        }
                        count++;
                    }
                }
~~~

### 开始与截至日期为时间戳格式，需要进行转换处理

~~~JAVA
//转换工具函数
/**
     * 时间戳转换成日期格式字符串
     *
     * @param seconds   精确到秒的字符串
     * @param formatStr
     * @return
     */
    public static String timeStamp2Date(String seconds, String format) {
        if (seconds == null || seconds.isEmpty() || seconds.equals("null")) {
            return "";
        }
        if (format == null || format.isEmpty()) {
            format = "yyyy-MM-dd HH:mm:ss";
        }
        SimpleDateFormat sdf = new SimpleDateFormat(format);
        return sdf.format(new Date(Long.valueOf(seconds + "000")));
    }

    /**
     * 取得当前时间戳（精确到秒）
     *
     * @return
     */
    public static String timeStamp() {
        long time = System.currentTimeMillis();
        String t = String.valueOf(time / 1000);
        return t;
    }
}
~~~



### 数据存储入MySQL数据库

* 通过JSON.parseObject将解析后的字符串转为JSON对象
* 再通过JSONObject.getString() 获取对用键值对

~~~java
for (String s : ListInfoNeat) {
                    JSONObject infoJson = JSON.parseObject(s);
                    //System.out.println(infoJson);
                    String projectName = infoJson.getString("projectName");
                    //System.out.println(projectType);
                    //System.out.println(infoJson.getClass().toString());
                    String sellPercent = URLEncoder.encode(infoJson.getString("sellPercent"), "utf-8");
                    String projectCode = infoJson.getString("projectCode");
                    String zoneName = infoJson.getString("zoneName");
                    String projectPrice = infoJson.getString("projectPrice");
                    String tradInstitutionName = infoJson.getString("tradInstitutionName");

                    String timeStampPublishDate = infoJson.getString("publishDate");
                    String publishDate = timeStamp2Date
                            (timeStampPublishDate.substring(0, timeStampPublishDate.length() - 3)
                                    , "yyyy-MM-dd HH:mm:ss");
                    String timeStampExpireDate = infoJson.getString("expireDate");
                    String expireDate = timeStamp2Date
                            (timeStampExpireDate.substring(0, timeStampExpireDate.length() - 3)
                                    , "yyyy-MM-dd HH:mm:ss");
                    //System.out.println(expireDate);

                    //数据插入数据库
                    pstmt.setString(1, projectName);
                    pstmt.setString(2, projectCode);
                    pstmt.setString(3, projectPrice);
                    pstmt.setString(4, sellPercent);
                    pstmt.setString(5, zoneName);
                    pstmt.setString(6, publishDate);
                    pstmt.setString(7, expireDate);
                    pstmt.setString(8, tradInstitutionName);
                    pstmt.executeUpdate();

                    System.out.println("1 row affected");

                }
~~~

### 数据入库截图

![入库结果](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/%E5%85%A5%E5%BA%93%E7%BB%93%E6%9E%9C.png)



## 翻页查询实现

* 由返回数据中 “totalData”即可获得总数据条数
* 再有for循环实现pageIndex的递增，即可实现翻页查询

**总条数**

![总条数](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/%E6%80%BB%E6%9D%A1%E6%95%B0.png)

**网页最后一条数据**

![网页最后一条数据](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/%E7%BD%91%E9%A1%B5%E6%9C%80%E5%90%8E%E4%B8%80%E6%9D%A1%E6%95%B0%E6%8D%AE.png)

**数据库最后一条数据**

![数据库最后一条数据](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img/%E6%95%B0%E6%8D%AE%E5%BA%93%E6%9C%80%E5%90%8E%E4%B8%80%E6%9D%A1%E6%95%B0%E6%8D%AE.png)
