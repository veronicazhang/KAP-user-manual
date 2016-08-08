

> 下面以KAP发布的样例Cube讲解schema的结构。其中包含了对联合主键，层级(Hierarchy)维度等的支持，供参考。

首先文件格式为xml格式。分为两大块， PhysicalSchema 和 Cube。
PhysicalSchema 定义了表信息， 在里面指明有哪些表即可。
````
<Table name="KYLIN_SALES">
</Table>
````

表定义好后，我们需要定义表中的维度，度量，表与表之间的连接。这些都在<Cube>标签下定义。
#### 维度
* Dimension 
 在<Cube>下定义一个<Dimensions>标签， <Dimensions>下定义多个<Dimension>，每一个<Dimension>标签对应一个表,注意Dimension标签有一个key属性，该属性指明了该表的主键，在关联查询时会作为join的条件。如果是联合主键的情况，则不在这里定义，在度量标签中定义。
 key属性对应关联一个维度，所以<Attributes> 标签下需要定义一个<Attribute>属性与之关联。

* 联合主键
 如果表与表之间是通过多个字段关联，可以在某个Attribute中定义一个key属性，里面指明该表与事实表join的字段。将Dimension的key定义为Attribute的name。如KYLIN_CATEGORY_GROUPINGS 指明key为UPD_USER与对应名为UPD_USER的Attribute关联，该Attribute指明了两个字段，LEAF_CATEG_ID和SITE_ID，那么涉及该维度的查询时，会将事实表的联合主键和这两个字段关联。事实表的主键在下面的度量中会说明。

* Hierarchy
  关于Hierarchy可以参考标签Attribute对应的META_CATEG_NAME、CATEG_LVL2_NAME、CATEG_LVL3_NAME的设置，注意Hierarchy结构的列每一个子元素定义Attribute时都会同时定义它的父元素，最终Hierarchies标签会统一列出这几个Attribute。

#### 度量
  * MeasureGroup
    KAP中度量默认都在事实表上，所以这里需要通过table属性关联对应的表。
    对应的Measure标签，说明对应的列(可选)和类型(min,max,sum,avg,count等），以及对应的数值类型。
  * DimensionLinks
    在该标签下说明每一个Dimension与事实表是如何关联的，如果维度本身在事实表上，使用FactLink标签，否则通过ForeignKeyLink，对应foreignKeyColumn指向事实表的关联字段。如果是联合主键，在ForeignKeyLink下定义ForeignKey
    
下面是一个完整的示例，对应的是KAP提供的样例Cube 'kylin_sales_cube'
```
 <?xml version="1.0" encoding="UTF-8"?>
<Schema name="kylin_sale_hierarchy_name" metamodelVersion="4.0">
   <PhysicalSchema>
      <!-- FACT TABLE -->
      <Table name="KYLIN_SALES">
      </Table>

      <!-- KYLIN_CAL_DT -->
      <Table name="KYLIN_CAL_DT">
      </Table>
      <!-- KYLIN_CATEGORY_GROUPINGS -->
      <Table name="KYLIN_CATEGORY_GROUPINGS">
      </Table>  
   </PhysicalSchema>

   <Cube name="kylin_sale_hierarchy" defaultMeasure="MIN(PRICE)">
      <Dimensions>
         <Dimension name="KYLIN_SALES" table="KYLIN_SALES" key="PART_DT">
            <Attributes>
               <Attribute name="LSTG_FORMAT_NAME" keyColumn="LSTG_FORMAT_NAME"  />
               <Attribute name="PART_DT" keyColumn="PART_DT"  />
               <Attribute name="SELLER_ID" keyColumn="SELLER_ID"  />
               <Attribute name="LEAF_CATEG_ID" keyColumn="LEAF_CATEG_ID"  />
               <Attribute name="LSTG_SITE_ID" keyColumn="LSTG_SITE_ID"  />               
            </Attributes>
         </Dimension>
         <Dimension name="KYLIN_CAL_DT" table="KYLIN_CAL_DT"  key="CAL_DT">
            <Attributes>
               <Attribute name="WEEK_BEG_DT" keyColumn="WEEK_BEG_DT"  />
               <Attribute name="CAL_DT" keyColumn="CAL_DT"/>
            </Attributes>
         </Dimension>

         <!--  -->
        <Dimension name='KYLIN_CATEGORY_GROUPINGS' table='KYLIN_CATEGORY_GROUPINGS' key="UPD_USER">
               <Attributes>
                   <Attribute name='UPD_USER' >
                       <key>
                           <Column name="LEAF_CATEG_ID"/>
                           <Column name="SITE_ID"/>
                       </key>    
                       <Name>
                           <Column name='UPD_USER'/>
                       </Name>
                   </Attribute>

                    <Attribute name='USER_DEFINED_FIELD1' >
                         <key>
                             <Column name='USER_DEFINED_FIELD1'/>
                         </key>
                     </Attribute>

                   <Attribute name='USER_DEFINED_FIELD3' >
                         <key>
                             <Column name='USER_DEFINED_FIELD3'/>
                         </key>
                     </Attribute>

                   <Attribute name='UPD_DATE' >
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


