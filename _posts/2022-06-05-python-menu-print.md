---
title: Python menu print
author: unitaryorz
date: 2022-06-05 20:10:00 +0800
categories: [Python]
tags: [python]
render_with_liquid: false
---

针对打印菜单的需求，编写一些函数方法来方便菜单的打印，有很多种方式可以实现，能力有限，这种仅供参考，可以根据实际情况进行更改

## 菜单名格式化

```
>>> name_print('Menu')

------------------Menu------------------
```

使用`format（）`格式化函数对菜单名进行**居中**和左右两边进行**填充**。根据Format Specifications（格式化规范），`^`为字符串居中，后面跟的数字为居中时左右两边**填充的数量**。以下分别使用`40`和`100`的**填充数**,分别代表`Menu`在总数40个和100个字符中处于居中（总数包含Menu四个字符）

```
>>> print("{:^40}".format("Menu1"))
                  Menu1
>>> print("{:^100}".format("Menu2"))
                                                Menu2
```

在`^`默认用空格进行填充，左边可加入自定义的字符

```
>>> print("{:1^8}".format("Menu")) //总数为8的字符串中居中，以1填充
11Menu11
>>> print("{:1^9}".format("Menu")) //总数为9的字符串中居中，以1填充
11Menu111
>>> print("{:-^40}".format("Menu")) //以-填充
------------------Menu------------------
>>> print("{:*^40}".format("Menu")) //以*填充
******************Menu******************
```

定义一个函数，专门打印菜单名，加入`sleep`避免打印过快

```python
def name_print(name):
    print("\n{:-^40}\n".format(name))
    time.sleep(0.5)
```

## 菜单目录生成

简单演示下效果，首先这有定义的几个功能

```python
def one():
    print("Start one")


def two():
    print("Start two")


def three():
    print("Start three")

def quit():
    exit()

def invalid():
	#启动不存在的功能时打印
    print("INVALID CHOICE!!!!!")
```

列表中的元祖分别对应菜单的**功能名**和**功能对应的函数**，传入一个列表给`menu_print()`，自动生成一个菜单并根据选择跳转到相应的功能

```
>>> selection = [('One',one),('Two',two),('Three',three)]
>>> menu_print(selection)
1.One
2.Two
3.Three
4.Quit

Make A Choice: 1
Start one
```

定义一个字典`menu`，自动生成id值，添加为字典的**key(键)**

```python
selection = [('One',one),('Two',two),('Three',three)] #one，two，three都为需要使用的函数名，代表所定义的函数
menu = {}

# Add id + selection
for i in range(len(selection)):
    id = str(i + 1)
    menu.update({id: selection[i]}) 

#menu为{'1': ('One', <function one at 0x102082af0>), '2': ('Two', <function two at 0x102082b80>), '3': ('Three', <function three at 0x102082c10>)}
```

在菜单项末尾都加一个退出功能

```python
menu.update({str(i + 2): ("Quit", quit)})
```

打印菜单目录，`sorted()`可以把字典中的`key (键) `变为一个独立的列表，即id值的列表，然后用`key`选出对于的功能名，比如`One`，最终组合成`1.One`

```python
for key in sorted(menu.keys()):
    print(key + "." + menu[key][0])
```

接受一个id值的选择，使用`get()`来根据id值提取出字典中对应的值，比如`('One',one)`,然后用`[1]`选择第二个值`one`，加上`()`，即可调用one函数`one()`

`[None, invalid]` 为设置`get()`的默认值，字典中未找到对应的键时返回`invalid`

```python
    ans = input("\nMake A Choice: ")
    menu.get(ans, [None, invalid])[1]()
```

定义成一个函数

```python
def menu_print(selection):
    menu = {}

    # Add id + selection
    for i in range(len(selection)):
        id = str(i + 1)
        menu.update({id: selection[i]})

    # Add “Quit” selection
    menu.update({str(i + 2): ("Quit", quit)})

    # print(menu)
    for key in sorted(menu.keys()):
        print(key + "." + menu[key][0])

    ans = input("\nMake A Choice: ")
    menu.get(ans, [None, invalid])[1]()
```

小的Tips，给`input()`设置默认值，在后面加`or`，空的字符为`False`，然后会返回`or`后面的值

```python
def one():
    name = input('Your name: ') or 'Tom'
	print(name)
```

## 效果展示

菜单结构如下

```python
main
├── Menu1
│   ├── One
│   └── Two
└── Menu2
    └── Three
```

定义对应的函数

```python
def one():
    print("Start one")
    name = input("Your name: ") or "Tom"
    print(name)
    menu1()

def two():
    print("Start two")
    menu1()

def three():
    print("Start three")
    menu2()

def quit():
    exit()


def invalid():
    print("INVALID CHOICE!!!!!")
    time.sleep(1)
    main()


def menu1():
    name_print("Menu1")
    selection = [("One", one), ("Two", two), ("Back", main)]
    menu_print(selection)


def menu2():
    name_print("Menu2")
    selection = [("Three", three), ("Back", main)]
    menu_print(selection)


def main():
    selection = [("Menu1", menu1), ("Menu2", menu2),]
    menu_print(selection)


main()
```

效果如下

```
------------------Main------------------

1.Menu1
2.Menu2
3.Quit

Make A Choice: 1

-----------------Menu1------------------

1.One
2.Two
3.Back
4.Quit

Make A Choice: 1

------------------One-------------------

Start one
Your name: 11
11

-----------------Menu1------------------

1.One
2.Two
3.Back
4.Quit

Make A Choice: 3

------------------Main------------------

1.Menu1
2.Menu2
3.Quit

Make A Choice: 2

-----------------Menu2------------------

1.Three
2.Back
3.Quit

Make A Choice: 1

-----------------Three------------------

Start three

-----------------Menu2------------------

1.Three
2.Back
3.Quit

Make A Choice: 3
```

完整代码

```python
import time


def name_print(name):
    print("\n{:-^40}\n".format(name))
    time.sleep(0.5)


def menu_print(selection):
    menu = {}

    # Add id + selection
    for i in range(len(selection)):
        id = str(i + 1)
        menu.update({id: selection[i]})

    # Add “Quit” selection
    menu.update({str(i + 2): ("Quit", quit)})

    # print(menu)
    for key in sorted(menu.keys()):
        print(key + "." + menu[key][0])

    ans = input("\nMake A Choice: ")
    menu.get(ans, [None, invalid])[1]()


def one():
    name_print("One")
    print("Start one")
    name = input("Your name: ") or "Tom"
    print(name)
    menu1()


def two():
    name_print("Two")
    print("Start two")
    menu1()


def three():
    name_print("Three")
    print("Start three")
    menu2()


def quit():
    exit()


def invalid():
    print("INVALID CHOICE!!!!!")
    time.sleep(1)
    main()


def menu1():
    name_print("Menu1")
    selection = [("One", one), ("Two", two), ("Back", main)]
    menu_print(selection)


def menu2():
    name_print("Menu2")
    selection = [("Three", three), ("Back", main)]
    menu_print(selection)


def main():
    name_print("Main")
    selection = [
        ("Menu1", menu1),
        ("Menu2", menu2),
    ]
    menu_print(selection)


main()
```

