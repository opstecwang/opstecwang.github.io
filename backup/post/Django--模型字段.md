---
title: "Django | 模型字段"
tags: ["Django"]
categories: ["编程语言"]
date: 2021-06-09T10:50:48+08:00
toc: true
draft: false
---

定义模型的基本要素：模型字段、重写函数__str__和Meta选项

## 模型字段
`AutoField` 自增长类型，数据表的字段为整数，长度为11位。  
`BigAutoField` 自增长类型，数据表的字段为bigint，长度为20位。  
`CharField` 字符类型，适用于中小长度的字符串   
`BooleanField` 布尔类型，一个真／假（true/false）字段。   
`CommaaSeparatedIntegerField` 一个用逗号分隔的整数类型。和 CharField 中一样， maxlength 参数是必需的。   
`DateField` 日期字段   
`DateTimeField` 时间日期字段，额外选项同DateField  
`EmailField` 一个能检查值是否是有效的电子邮件地址的 CharField 。不接受 maxlength 参数，它的 maxlength 被自动设置为75。   
`FileField` 文件上传字段，必须参数upload_to一个本地的文件系统路径，被附加到你的 MEDIA_ROOT 设置后面，这决定了 get_<fieldname>_url() 辅助函数的输出   
`FilePathField` 文件系统中某个目录下的文件名   
`FloatField` 浮点数，   
`ImageField` 同FileField要验证上传的对象是一个有效的图片   
`IntegerField` 整数   
`IPAddressField` IP地址，以字符串格式表示（例如： "24.124.1.30" ）。   
`NullBooleanField` 就像一个 BooleanField ，但它支持 None /Null   
`PhoneNumberField` 它是一个 CharField ，并且会检查值是否是一个合法的美式电话格式，如（XXX-XXX-XXXX） 
`PositiveIntegerField` 正整数  
`SlugField` 嵌条是报纸业的术语。 嵌条 就是一段内容的简短标签，这段内容只能包含字母、数字、下划线或连字符,通常用于URL中  
`SmallIntegerField` 和 IntegerField 类似，但是只允许在一个数据库相关的范围内的数值（通常是-32,768到+32,767）  
`TextField` 不限长度的文字长度  
`TimeField` 时分秒的时间显示。它接受的可指定参数与 DateField 和 DateTimeField 相同  
`URLField` 存储URL的字段  
`USStateField` 美国州名称缩写，两个字母  
`XMLField` 它就是一个 TextField ，只不过要检查值是匹配指定schema的合法XML。  
  
  
## 通用的模型字段参数
`verbose_name` 默认为 None，在Admin站点管理设置字段的显示名称  
`primary_key` 默认为 False，若为True，则将字段设置成主键  
`max_length` 默认为 None，设置字段的最大长度  
`unique` 默认为 False，若为True，则设置字段的唯一属性  
`blank` 默认为 False，若为True，则字段允许为空值，数据库将存储空字符串  
`null` 默认为 False，若为True，则字段允许为空值，数据库表现为NULL  
`db_index` 默认为 False，若为True，则以此字段来创建数据库索引  
`default` 默认为NOT_PROVIDED对象，设置字段的默认值。  
`editable` 默认为 True，允许字段可编辑，用于设置Admin的新增数据的字段  
`serialize` 默认为 True，允许字段序列化，可将数据转化为JSON格式  
`unique_for_date` 默认为 None，设置日期字段的唯一性  
`unique_for_month` 默认为 None，设置日期字段月份的唯一性  
`unique_for_year` 默认为 None，设置日期字段年份的唯一性  
`choices` 默认为空列表，设置字段的可选值  
`help_text` 默认为空字符串，用于设置表单的提示信息  
`db_column` 默认为 None，设置数据表的列名称，若不设置，则将字段名作为数据表的列名  
`db_tablespace` 默认为 None，如果字段已创建索引，那么数据库的表空间名称将作为该字段的索引名。注意：部分数据库不支持表空间  
`auto_created` 默认为 False，若为True，则自动创建字段，用于一对一的关系模型  
`validators` 默认为空列表，设置字段内容的验证函数  
`error_messages` 默认为 None，设置错误提示  
  
  
## Meta选项的 19 个属性
`abstract` 
`app_label` 
`db_table` 
`db_teblespace`
`get_latest_by`
`managed`
`order_with_respect_to`
`ordering`
`permissions`  
`proxy` 
`required_db_features` 
`required_db_vendor` 
`select_on_save` 
`indexes` 
`unique_together` 
`verbose_name` 
`verbose_name_plural` 
`label` 
`label_lower` 















