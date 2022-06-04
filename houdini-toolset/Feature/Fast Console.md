# 简介
快速命令工具让使用者能快速地搜索特定命令并执行。一些典型的命令如下：
+ 创建节点
+ 显示选中节点的几何属性
+ 打开cookbook
# 使用方法
用户在Network Editor，也就是节点编辑器面板，按下Ctrl+空格键即可唤出快速命令搜索框，按照关键词搜索对应的命令，搜索后按下回车即可执行命令，按下Esc即可退出搜索窗口。
# 自定义命令
快速命令工具中的命令是可高度自定义的。用户目前可通过两种形式扩展命令。
## 自定义节点创建命令
Houdini的Tab菜单可搜索并创建节点，而自定义节点创建命令的意义在于——快速命令工具中的每一个命令都可以包含一个别名，例如可以给“Point Wrangle”节点设置“pw”的别名，因此直接输入pw即可搜索到“Point Wrangle”命令。
对于节点的创建命令，我们提供了一个方便快捷的自定义方式——用户可以在DolagPlugin/scripts/console/console_items.json文件中添加自己的节点创建命令。
如果我们要创建一个节点命令，可以在此json文件的列表尾部添加键值对，其中“node_name”表示此节点的名称(在houdini中显示节点信息窗口的右上角会显示节点名称)，“item_name”表示在快速命令列表中显示和搜索的名称，“alias”表示此命令的别名，而“important”决定了此命令是否重要，重要节点会优先显示。
下列代码展示了如何添加一个subdivide节点的快捷命令。

```
[
  {
    ...
  },
  {
    ...
  },
  {
    ...
  },
  {
    "item_name": "Subdivide",
    "alias": "sd",
    "important": true,
    "node_name": "subdivide"
  }
]
```
## 自定义任何命令
快速命令工具提供了一个能完全自定义任何命令的框架。在此之前先谈一下原理。
快速命令工具中的每一个命令都是ConsoleItem类的实例，快速命令工具会读取每一个ConsoleItem实例的命令名称、别名、重要与否，在执行命令时调用实例的run函数，其中ConsoleItem类的定义在DolagPlugin/scripts/console/ConsoleItem.py中。
因此想要添加自定义命令就需要先创建一个ConsoleItem实例。
ConsoleItem类初始化函数签名如下：
```
ConsoleItem(item_name, callback, alias=tuple(), important=False)
```
item_name为此命令在快速命令中显示和搜索的名称，“alias”表示此命令的别名，而“important”决定了此命令是否重要，重要节点会优先显示。
最重要的是回调函数参数callback，此函数的签名如下：
```
callback(context)
```
context参数是快速命令框架传入的ConsoleContext类型的参数，可以视为一个字典来访问其值。其键和值如下：
|  键   | 值  |
|  ----  | ----  |
| selected_nodes  | 选中节点的元组，如果没有选中则返回空元组 |
| hit_item  | 在鼠标下方的节点，如果没用则返回None |
| screen_pos | 鼠标在屏幕上的绝对位置，元组类型 |
| editor_pos | 鼠标在Network Editor上的绝对位置，采用Network Editor的坐标系和单位，元组类型 |
| editor | 当前Network Editor对象 |
| qt_keys | 按下了哪些键，其值为PySide2中的key值 |
| network_node | 当前Network Editor面板所显示的Network节点 |
快速命令工具会在合适的时候执行用户传入的callback回调函数。

在完成了ConsoleItem的实例化后，将其插入到DolagPlugin/scripts/console/ConsoleItemCustom.py中的CUSTOM_ITEMS变量中——快速命令工具会读取此变量将其中的每一项都加入到快速命令列表中。
例如，在DolagPlugin/scripts/console/ConsoleItemCustom.py尾部插入如下语句，即可添加自定义命令。
```
...

def copy_node_path_cb(context):  
    nodes = context["selected_nodes"]  
    if nodes:  
        du.writeClipboard(nodes[0].path())  
  
create_python_shell = ConsoleItem(item_name="Copy Node Path", alias="np", callback=copy_node_path_cb)  
CUSTOM_ITEMS.append(create_python_shell)
```