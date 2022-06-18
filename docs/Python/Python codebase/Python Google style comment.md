---
layout: default
title: Python Google style comment
date: 2020-06-18
categorires: Python
grand_parent: Python
parent: Python codebase
Created by: ECHO
Tags: python, 规范
---


# Python Google style comment

<small>Created: June 17, 2022</small>  
<small>Tags: #python, #规范</small>

作用：确保对模块, 函数, 方法和行内注释使用正确的风格


## **文档字符串**

Python有一种独一无二的的注释方式: 使用文档字符串. 文档字符串是包, 模块, 类或函数里的第一个语句. 这些字符串可以通过对象的 `__doc__` 成员被自动提取, 并且被 `pydoc` 所用. (你可以在你的模块上运行pydoc试一把, 看看它长什么样). 我们对文档字符串的惯例是使用三重双引号 `”””`( PEP-257 ). 一个文档字符串应该这样组织: 首先是一行以句号, 问号或惊叹号结尾的概述(或者该文档字符串单纯只有一行). 接着是一个空行. 接着是文档字符串剩下的部分, 它应该与文档字符串的第一行的第一个引号对齐. 下面有更多文档字符串的格式化规范. 

## **模块**

每个文件应该包含一个许可样板. 根据项目使用的许可(例如, Apache 2.0, BSD, LGPL, GPL), 选择合适的样板. 其开头应是对模块内容和用法的描述.

```python
A one-line summary of the module or program, terminated by a period.

Leave one blank line. The rest of this docstring should 
contain an overall description of the module or program.  
Optionally, it may also contain a brief description of exported 
classes and functions and/or use examples.

Typical usage example:

foo = ClassFoo()bar = foo.FunctionBar()
```

## **函数和方法**

下文所指的函数,包括函数, 方法, 以及生成器.

一个函数必须要有文档字符串, 除非它满足以下条件:

1. 外部不可见
2. 非常短小
3. 简单明了

文档字符串应该包含函数做什么, 以及输入和输出的详细描述. 通常, 不应该描述”怎么做”, 除非是一些复杂的算法. 文档字符串应该提供足够的信息, 当别人编写代码调用该函数时, 他不需要看一行代码, 只要看文档字符串就可以了. 对于复杂的代码, 在代码旁边加注释会比使用文档字符串更有意义. 覆盖基类的子类方法应有一个类似 `See base class` 的简单注释来指引读者到基类方法的文档注释.若重载的子类方法和基类方法有很大不同,那么注释中应该指明这些信息.

关于函数的几个方面应该在特定的小节中进行描述记录， 这几个方面如下文所述. 每节应该以一个标题行开始. 标题行以冒号结尾. 除标题行外, 节的其他内容应被缩进2个空格.

**Args:**列出每个参数的名字, 并在名字后使用一个冒号和一个空格, 分隔对该参数的描述.如果描述太长超过了单行80字符,使用2或者4个空格的悬挂缩进(与文件其他部分保持一致). 描述应该包括所需的类型和含义. 如果一个函数接受*foo(可变长度参数列表)或者**bar (任意关键字参数), 应该详细列出*foo和**bar.**Returns: (或者 Yields: 用于生成器)**描述返回值的类型和语义. 如果函数返回None, 这一部分可以省略.**Raises:**列出与接口有关的所有异常. 

```python
**def** fetch_smalltable_rows(table_handle: smalltable.Table,
                        keys: Sequence[Union[bytes, str]],
                        require_all_keys: bool = **False**,
) -> Mapping[bytes, Tuple[str]]:
*"""Fetches rows from a Smalltable.    
Retrieves rows pertaining to the given keys from the Table instance represented by table_handle.  
String keys will be UTF-8 encoded.    
Args:        
		table_handle: An open smalltable.Table instance.        
		keys: A sequence of strings representing the key of each table row to fetch.  
					String keys will be UTF-8 encoded.        
		require_all_keys: Optional; If require_all_keys is True only rows with values set for all keys will be returned.    
Returns: 
		A dict mapping keys to the corresponding table row data fetched. 
		Each row is represented as a tuple of strings. For example:        
		
		{b'Serak': ('Rigel VII', 'Preparer'),        
		b'Zim': ('Irk', 'Invader'),        
		b'Lrrr': ('Omicron Persei 8', 'Emperor')}    
		    
		Returned keys are always bytes.  If a key from the keys argument is 
		missing from the dictionary, then that row was not found in the        
		table (and require_all_keys must have been False).    
Raises: 
		IOError: An error occurred accessing the smalltable.    """*
```

在 `Args:` 上进行换行也是可以的:

```python
**def** fetch_smalltable_rows(table_handle: smalltable.Table,
                        keys: Sequence[Union[bytes, str]],
                        require_all_keys: bool = **False**,
) -> Mapping[bytes, Tuple[str]]:
*"""Fetches rows from a Smalltable.    
Retrieves rows pertaining to the given keys from the Table instance
represented by table_handle.  String keys will be UTF-8 encoded.    

Args:    
table_handle: 
		An open small table.Table instance.   
keys: 
		A sequence of strings representing the key of each table row to fetch. String keys will be UTF-8 encoded.    
require_all_keys: 
		Optional; If require_all_keys is True only rows with values set for all keys will be returned.    

Returns:    
A dict mapping keys to the corresponding table row data    
fetched. Each row is represented as a tuple of strings. 

For example:    
{b'Serak': ('Rigel VII', 'Preparer'),    
b'Zim': ('Irk', 'Invader'),    
b'Lrrr': ('Omicron Persei 8', 'Emperor')}    
Returned keys are always bytes.  If a key from the keys argument is 
missing from the dictionary, then that row was not found in the 
table (and require_all_keys must have been False).    

Raises:    
IOError: An error occurred accessing the smalltable. 
"""*
```

## **类**

类应该在其定义下有一个用于描述该类的文档字符串. 如果你的类有公共属性(Attributes), 那么文档中应该有一个属性(Attributes)段. 并且应该遵守和函数参数相同的格式.

```python
**class** **SampleClass**(object):
    *"""Summary of class here.    
		
		Longer class information....    
		Longer class information....  
  
		Attributes:        
				likes_spam: A boolean indicating if we like SPAM or not.        
				eggs: An integer count of the eggs we have laid.    
		"""*
		
		**def** __init__(self, likes_spam=**False**):
        *"""Inits SampleClass with blah."""*
				self.likes_spam = likes_spam
        self.eggs = 0

    **def** public_method(self):
        *"""Performs operation blah."""*
```

## **块注释和行注释**

最需要写注释的是代码中那些技巧性的部分. 如果你在下次 代码审查 的时候必须解释一下, 那么你应该现在就给它写注释. 对于复杂的操作, 应该在其操作开始前写上若干行注释. 对于不是一目了然的代码, 应在其行尾添加注释.

```python
*# We use a weighted dictionary search to find out where i is in
# the array.  We extrapolate position based on the largest num
# in the array and the array size and then do binary search to
# get the exact number.*

**if** i & (i-1) == 0:   *# True if i is 0 or a power of 2.*
```

为了提高可读性, 注释应该至少离开代码2个空格.

另一方面, 绝不要描述代码. 假设阅读代码的人比你更懂Python, 他只是不知道你的代码要做什么.

```python
*# BAD COMMENT: Now go through the b array and make sure whenever i occurs
# the next element is i+1*
```

转自：

[Python风格规范](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_style_rules/#section-8)