### 起因
今天公司测试人员在测试环境中测试网站新功能时, 突然在浏览器中弹出警告框, 如下图所示: ![](https://note.youdao.com/yws/public/resource/340ba176925ee287a13ec8321f3181f3/xmlnote/C3BC37974EDD473EB18E176BE989FB71/20900)        
### 排查过程
问题非常诡异, 因为在这个问题出现前后, 没有人更新过应用. 在应用源码中全文搜索`alert`和`aa`字符串, 没有发现能够弹出该警告框的代码块. 在仔细排查了源码没有找到疑点后, 重新对问题的出现过程进行分析, 发现有时弹出一次, 有时弹出两次, 并且全都是在按条件查询后弹出, 于是尝试确定警告框出现次数, 使用筛选功能进行条件查询, 发现弹窗的次数与后台返回的数据数量相关, 于是把怀疑聚焦在后端传入的数据中. 发现后端传入的数据中某个字段的值中包含`<script>alert('aa')</script`字符串, 于是确定问题起源. ![](https://note.youdao.com/yws/public/resource/340ba176925ee287a13ec8321f3181f3/xmlnote/A9BE3746D4744F59B3162674317B3326/20899)
### 根本原因
在`jquery-1.8.2`中的`html: function( value ) {...}`方法的参数`value`就是经过处理后的包含微博正文字段等所有表格展示内容的html字符串, 然后在该方法里面对该字符串进行解析, 此时会执行其中的`<script>`代码片段      
例如
```html
value = "<table class="datagrid-btable" cellspacing="0" cellpadding="0" border="0"><tbody><tr id="datagrid-row-r1-2-0" datagrid-row-index="0" class="datagrid-row  " ><td field="uid_nick"  ><div style="text-align:center;white-space:normal;height:auto;" class="datagrid-cell datagrid-cell-c1-uid_nick">_笺罗</div></td><div style="text-align:center;white-space:normal;height:auto;" class="datagrid-cell datagrid-cell-c1-weibo_content">CMS原发-博文-维系类-带图0710，ADD，sum，000，；‘’“”：？,;''"":?...（@！!#2333#*/^&~+-（2333二三）<br /> &nbsp; <script>alert('aa');</script>，2-0，1.[偷乐]</div></td><td field="exposure_rate"  ><div style="text-align:center;white-space:normal;height:auto;" class="datagrid-cell datagrid-cell-c1-exposure_rate">6</div></td>></tr></tbody></table>"
```
此时会执行`datagrid-cell datagrid-cell-c1-weibo_content  `类中的`<script>`字符串
### 事后总结
1. 代码编写方面
   1. 在后台对需要插入数据库的字符串进行检查或转义. 虽然不是本人写的插入功能, 但也要引以为戒
   2. 在前端对后台传入的字符串进行转义
2. bug定位方面
   1. 定位每次出现的具体状况有细微差别的bug时, 应找到bug的出现规律, 有助于发现bug原因