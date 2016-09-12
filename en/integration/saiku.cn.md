> Kyligence公司基于Saiku进行二次开发，提供定制版对KAP友好的访问页面

### 安装
通过Kyligence公司获得定制版的saiku安装包 KyAnalyzer-{version}.zip,由于Saiku是基于Mondrian转换SQL，因此我们需要获得对应KAP版本的Mondrian包。对应Mondrian包可在 [github](https://github.com/Kyligence/kylin-mondrian/blob/master/build/mondrian-kylin-1.0.jar) 上获得。
解压saiku安装包，把mondrian-kylin对应的jar包拷到 saiku-server/tomcat/webapps/saiku/WEB-INF/lib 目录下。

通过saiku-server 目录下的 start-saiku.sh启动saiku，默认端口为8080, 初始默认帐号密码为 admin/admin。可通过 http://host:8080 访问页面。

![](/images/integration/saiku/saiku_login.png)

### Saiku中的Schema
KAP中的每一Cube都对应saiku中的一个schema文件，因此我们需要创建一个schema文件。
mondrian的schema有各种语法。这里提供官方的[文档链接](http://mondrian.pentaho.com/schema.html)供参考。
请参见下一节了解如何创建Schema文件

### 创建schema及数据源


点击导航栏第一排中间含'A'的图标，进入创建数据源页面。点击添加schema (Add Schema), 在该页面上传本地编辑好的schema文件并命名。
点击添加数据源(Add Data Source)按钮，进入创建页面，为saiku创建数据源, saiku访问KAP数据时需要对应的URL，帐号密码等信息。。

* Name － 自定义名称
* Connection Type － 选默认'Mondrian'
* Url - jdbc访问Url
* Schema - 为KAP中Cube建的schema文件
* Jdbc Driver - 驱动类，保留默认值
* Username - 访问KAP用户名 
* Password - 访问KAP密码

![](/images/integration/saiku/saiku_datasource.png)

### 查询页
点击导航栏的新建查询按钮，点击刷新按钮获取最新的数据，在'选择多维数据' 下拉框中选中要查询的Cube，

![](/images/integration/saiku/saiku_query_1.png)

左侧列出维度和度量， 选择自己要查询的数据自由查询。saiku支持多种展现形式，表格，格式图表都可以。

#### 表格

![](/images/integration/saiku/saiku_query_table.png)

#### 图形化展示

![](/images/integration/saiku/saiku_query_chart.png)

![](/images/integration/saiku/saiku_query_linechart.png)



