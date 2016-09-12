## 查询页概览

KAP的分析页面即为查询页面，点击后左边列出来所有可以查询的表，这些表是Cube 构建好以后才会显示出来，右边给出了输入框，在这输入SQL, 下方显示结果。下面是不同标识对应的解释。
* FACT - 事实表
* LOOKUP - 维度表
* D - 维度列
* M - 度量列
* D&M - 该列既是维度又是度量
* PK - 主键
* FK - 外键

![]( /images/gui/web/1 query_list_table.png)

## SQL查询
* 提供一个输入框输入SQL, 点击提交即可查询结果。在右下角有一个limit字段，如果SQL中没有limit字段，这里默认会拼接上limit 50000，如果SQL中有limit 那么以SQL中为准。假如用户想去掉limit限制，那么SQL中不要加limit，右下角的limit输入框也改为0。

![]( /images/gui/web//2 query_input_sql.png)


* 如果SQL去掉了所有的limit限制，后台最多也只会返回一百万条数据，如果超过了一百万条，前台会显示一个 "显示所有记录" 字样的按钮，点击后即可获取所有结果集。当查询运行成功后，它将呈现一个成功指标与被访问的cube名字。同时它将会呈现这个查询在后台引擎运行了多久（不包括从KAP服务器到浏览器的网络通信）

![]( /images/gui/web//3 query_list_show_all.png)

> **支持的浏览器**
> 
> Windows: Google Chrome, FireFox
> 
> Mac: Google Chrome, FireFox, Safari



> **查询限制**
> 
> 1. 仅支持SELECT查询
> 
> 2. 为了避免从服务器到客户端产生巨大的网络流量，扫描范围阀值被设置为1,000,000。
> 
> 3. SQL在cube中无法找到的数据将不会重定向到Hive

## 保存查询
与用户账号关联，你将能够从不同的浏览器甚至机器上获取已保存的查询。在结果区域点击保存图标，将会弹出名称和描述来保存当前查询。

![]( /images/gui/web//4 query_list_save_query.png)

## 查询历史
   仅保存当前用户在当前浏览器中的查询历史，这将需要启用cookie，并且如果你清理浏览器缓存将会丢失数据。点击“Query History”标签，你可以直接重新提交其中的任何一条并再次运行。

![]( /images/gui/web//5 query_list_history.png)

