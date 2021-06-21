---
title: "Django | 表单字段"
tags: ["Django"]
categories: ["编程语言"]
date: 2021-06-10T09:50:48+08:00
toc: true
draft: false
---

## 类BaseForm常用的属性和方法
`data` 默认值为None，以字典形式表示，字典的键值为表单字段，代码将数据绑定到对应的表单字段。  
`auto_id` 默认值为id_%s，以字符串格式化表示，若设置HTML元素控件的id属性，比如表单字段job，则元素控件id属性为id_job，%s代表表单字段名称。  
`prefix` 默认值为None，以字符串表示，设置表单的控件属性name和id的属性值，如果一个网页里使用多个相同的表单，那么设置该属性可以区分每个表单。  
`initial` 默认值为None，以字典的形式表示，在表单的实例化过程中设置初始化值。  
`label_suffix` 若参数桌子为None，则默认为冒号，以表单字段job为例，其HTML控件含有label标签（<label for="id_job">职位:</label>），其中label标签里的冒号由参数label_suffix设置。    
`field_order` 默认值为None，则以表单字段定义的先后顺序进行排列，作妖自定义排序，则将每个表单字段按先后顺序放置在列表里，并把列表作为该参数的值。  
`use_required_attribute` 默认值为None（或为True），为表单字段所对应的HTML控件设置required属性，该控件为必选项，数据不能为空，若设为False，则HTML控件为可填项。  
`errors()` 验证表单的数据是否存在异常，若存在异常，则获取异常信息，异常信息可以设为字典或JSON格式。  
`is_valid()` 验证表单数据是否存在异常，若存在，则返回False，否则返回True。  
`as_table()` 将表单字段以HTML的\<table\>标签生成网页表单。  
`as_ul()` 将表单字段以HTML的\<ul\>标签生成网页表单。  
`as_p()` 将表单字段以HTML的\<p\>标签生成网页表单。  
`has_changed()` 对比用于提交的表单数据与表单初始化数据是否发生变化。  

## 26个表单字段，及每个字段说明
`CharField` 文本框，参数max_length和min_length分别设置文本长度  
`IntegerField` 数值框，参数max_value设置最大值，min_value设置最小值。  
`FloatField` 数值框，验证数据是否为浮点数  
`DecimalField` 数值框，验证是否有小数点  
`DateField` 文本框，日期格式  
`TimeField` 文本框，时间格式  
`DateTimeField` 文本框，日期时间格式  
`DurationField` 文本框，时间段  
`RegexField` 文本框，正则表达式匹配  
`EmailField` 文本框，邮箱地址  
`FileField` 文件上传控件  
`ImageField` 文件上传控件  
`FilePathField` 文件选择控件  
`URLField` 文本框，路由地址  
`BooleanField` 复选框，True或False  
`NullBooleanField` 复选框，Null、True或False  
`ChoiceField`  下拉框，参数choices以元组形式表示，用于设置下拉框的选项列表  
`TypedChoiceField`  下拉框  
`MultipleChoiceField` 下拉框，验证数据是否在下拉框的选项列表  
`TypedMultipleChoiceField`  下拉框  
`ComboField` 文本框，为表单字段设置验证功能  
`MultiValueField` 文本框，将多个表单字段合并成一个新的字段  
`SplitDateTimeField` 文本框，日期时间格式  
`GenericIPAddressField` 文本框，验证数据是否是有效的IP地址  
`SlugField` 文本框，验证数据师傅只包含字母、数字、下划线及连接符
`UUIDField` 文本框，验证数据是否为UUID格式  


## 字段的通用参数
`required` 输入的数据是否为空，默认值为True  
`widget` 设置HTML控件的样式  
`label` 用于生成label标签的网页内容  
`initial` 设置表单字段的初始值  
`help_text` 设置帮助提示信息  
`error_messaages` 设置错误信息，以字典形式表示，比如{`required`: `不能为空`, 'invalid': '格式错误'}  
`show_hidden_initial` 参数值为True/False，是否在当前控件后面再加一个隐藏的且具有默认值的控件（可用于检验两次输入的值是否一致）  
`validators` 自定义数据验证规则。以列表格式表示，列表元素为函数名  
`localize` 参数值为True/False，设置本地化，不同时区自动显示当地时间  
`disabled` 参数值为True/False，HTML控件是否可以编辑  
`label_suffix` 设置label的后缀内容  




