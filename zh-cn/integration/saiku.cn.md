> Kyligence公司基于Saiku进行二次开发，提供定制版对KAP友好的访问页面

### 安装
通过Kyligence公司获得定制版的saiku安装包 saiku-server-knalyzer-{version}.zip,由于Saiku是基于Mondrian转换SQL，因此我们需要获得对应KAP版本的Mondrian包。对应Mondrian包可在 [github](/Kyligence/kylin-mondrian/blob/master/build/mondrian-kylin-1.0.jar) 上获得。
解压saiku安装包，把mondrian-kylin对应的jar包拷到 saiku-server/tomcat/webapps/saiku/WEB-INF/lib 目录下。

通过saiku-server 目录下的 start-saiku.sh启动saiku，默认端口为8080, 初始默认帐号密码为 admin/admin。可通过http://host:8080访问页面。

![](/images/integration/saiku/saiku_login.png)

### Saiku中的Schema
KAP中的每一Cube都对应saiku中的一个schema文件，因此我们需要创建一个schema文件。
mondrian的schema有各种语法。这里提供官方的[文档链接](http://mondrian.pentaho.com/schema.html)供参考。

下面以KAP发布的样例Cube讲解schema的结构。其中包含了对联合主键，层级(Hierarchy)维度的支持，供参考。

```
 <?xml version="1.0" encoding="UTF-8"?>
<Schema name="sale_cal_dt" metamodelVersion="4.0">
   <PhysicalSchema>

      <!-- fact table -->
      <Table name="KYLIN_SALES">
      </Table>

      <!-- KYLIN_CAL_DT -->
      <Table name="KYLIN_CAL_DT">
      </Table>
      <!-- KYLIN_CATEGORY_GROUPINGS -->
      <Table name="KYLIN_CATEGORY_GROUPINGS">
      </Table>  

   </PhysicalSchema>
   <Cube name="kylin_sale_cal_dt" defaultMeasure="MIN(PRICE)">
      <Dimensions>
         <Dimension name="KYLIN_SALES" table="KYLIN_SALES" key="PART_DT">
            <Attributes>
               <Attribute name="LSTG_FORMAT_NAME" keyColumn="LSTG_FORMAT_NAME" hasHierarchy="true" />
               <Attribute name="PART_DT" keyColumn="PART_DT" hasHierarchy="false" />
               <Attribute name="SELLER_ID" keyColumn="SELLER_ID" hasHierarchy="true" />
               <Attribute name="LEAF_CATEG_ID" keyColumn="LEAF_CATEG_ID" hasHierarchy="true" />
               <Attribute name="LSTG_SITE_ID" keyColumn="LSTG_SITE_ID" hasHierarchy="true" />               
            </Attributes>
         </Dimension>
         <Dimension name="KYLIN_CAL_DT" table="KYLIN_CAL_DT"  key="CAL_DT">
            <Attributes>
               <Attribute name="WEEK_BEG_DT" keyColumn="WEEK_BEG_DT" hasHierarchy="true" />
               <Attribute name="CAL_DT" keyColumn="CAL_DT" hasHierarchy="false" />
            </Attributes>
         </Dimension>

         <!--  -->
        <Dimension name='KYLIN_CATEGORY_GROUPINGS' table='KYLIN_CATEGORY_GROUPINGS' key="UPD_USER">
               <Attributes>
                   <Attribute name='UPD_USER' hasHierarchy='true'>
                       <key>
                           <Column name="LEAF_CATEG_ID"/>
                           <Column name="SITE_ID"/>
                       </key>    
                       <Name>
                           <Column name='UPD_USER'/>
                       </Name>
                   </Attribute>

                    <Attribute name='USER_DEFINED_FIELD1' hasHierarchy='true'>
                         <key>
                             <Column name='USER_DEFINED_FIELD1'/>
                         </key>
                     </Attribute>

                   <Attribute name='USER_DEFINED_FIELD3' hasHierarchy='true'>
                         <key>
                             <Column name='USER_DEFINED_FIELD3'/>
                         </key>
                     </Attribute>

                   <Attribute name='UPD_DATE' hasHierarchy='true'>
                         <key>
                             <Column name='UPD_DATE'/>
                         </key>
                     </Attribute>

                   <Attribute name='META_CATEG_NAME' hasHierarchy='false'>
                         <key>
                            <Column name='META_CATEG_NAME'/>
                         </key>
                     </Attribute>

                   <Attribute name='CATEG_LVL2_NAME' hasHierarchy='false'>
                         <key>
                             <Column name='META_CATEG_NAME'/>
                             <Column name='CATEG_LVL2_NAME'/>
                         </key>
                         <Name>
                              <Column name='CATEG_LVL2_NAME'/>
                         </Name>
                     </Attribute>

                   <Attribute name='CATEG_LVL3_NAME' hasHierarchy='false'>
                        <key>
                             <Column name='META_CATEG_NAME'/>
                             <Column name='CATEG_LVL2_NAME'/>
                             <Column name='CATEG_LVL3_NAME'/>
                         </key>                         
                         <Name>
                             <Column name='CATEG_LVL3_NAME'/>
                         </Name>
                     </Attribute> 

               </Attributes>

               <Hierarchies>
                  <Hierarchy name='META_CATEG_NAME_Hierarchy' hasAll='true'>
                    <Level attribute="META_CATEG_NAME"/>
                    <Level attribute="CATEG_LVL2_NAME"/>
                    <Level attribute="CATEG_LVL3_NAME"/>
                  </Hierarchy>
               </Hierarchies>
        </Dimension>


      </Dimensions>
      <MeasureGroups>
         <MeasureGroup name="measures" table="KYLIN_SALES">
            <Measures>
               <Measure name="COUNT(*)" aggregator="count" formatString="#,###" />
               <Measure name="MAX(PRICE)" column="PRICE" aggregator="max" formatString="#,###.00" />
               <Measure name="MIN(PRICE)" column="PRICE" aggregator="min" formatString="#,###.00" />
               <Measure name="SUM(PRICE)" column="PRICE" aggregator="sum" formatString="#,###.00" />
            </Measures>
            <DimensionLinks>
               <FactLink dimension="KYLIN_SALES" />

               <ForeignKeyLink foreignKeyColumn="PART_DT" dimension="KYLIN_CAL_DT" />

               <ForeignKeyLink dimension='KYLIN_CATEGORY_GROUPINGS' attribute='UPD_USER'>
                     <ForeignKey>
                         <Column name="LEAF_CATEG_ID"/>
                         <Column name="LSTG_SITE_ID"/>
                     </ForeignKey>
               </ForeignKeyLink>

            </DimensionLinks>
         </MeasureGroup>
      </MeasureGroups>
   </Cube>
</Schema>

```



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



