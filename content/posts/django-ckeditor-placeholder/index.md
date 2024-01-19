---
title: "django-ckeditor设置placeholder属性"
date: 2021-03-06 18:45:00
lastmod: 2022-09-26 09:52:25
tags: ["django", "web"]
---

新博客换了很多编辑器，最终选择了ckeditor，原因是开源免费，一直维护，且用户量多，本站用的是django-ckeditor，内置的CKEditor 4.14.1。 设置过程中，在forms进行如下设置没法显示placeholder属性

```python

class Form(forms.ModelForm):      

class Meta:

           ...           

    widgets = {                

        'body': forms.Textarea(attrs={'placeholder': 'blabla'})           

}

```

但没什么卵用，并且在settings.py里直接设置也不行。

ckeditor自带一个placeholder占位符插件，所以搜索出来都是这个。

浏览官方文档发现，用[Configuration Helper](https://ckeditor.com/cke4/addon/confighelper "Configuration Helper")插件就可以了,下载后解压放到`/Lib/site-packages/ckeditor/static/ckeditor/ckeditor/plugins` 然后在settings.py里直接添加ckeditor设置就行了

###附上评论区的ckeditor配置

```python

    CKEDITOR_CONFIGS = {

    'default': {

        height': 100,

        'width': 'auto',

        'language': 'zh - cn ',

        'CKEDITOR_RESTRICT_BY_USER ': True,

        'toolbar': 'Custom ',

        'placeholder ': '不有佳咏， 何伸雅怀？',

        ...

        }

    }

```