# 简介
vex和Python的代码库。
# Python
Python是以模块形式导入和调用，被放置在DolagPlugin/python/python2.7libs文件夹下。
所有能作为代码库导入的模块都在Dolag文件夹下，因此要使用这些功能就需要先导入Dolag模块。
```python
import Dolag
```
当然也可以从Dolag导入模块
```python
from Dolag import path
```
## Dolag
Dolag为所有模块的主模块，后文每一个标题代表Dolag的子模块。以path为例，可以通过如下代码导入。
```python
from Dolag import path
```
### path
和路径相关的模块。
#### getRelativePath(src, dest)->str
计算目标路径相对于原始路径的相对路径。
+ src：原始路径，应当形如“/obj/geo/scatter”
+ dest：目标路径。
如果原始路径为/obj/geo/button1，而目标路径为/obj/geo/file/reload，则计算结果为../geo/file/reload。
如果输入路径名不合规则返回None。
#### increaseFileName(file_name, has_suffix=True)->str
计算增量后的文件名。
+ file_name：文件名
+ has_suffix：文件名是否带后缀例如dolag.jpg的jpg就是文件后缀。
通过在文件名后添加序号或者增加文件序号来获得增量后的文件名。若文件名为“/obj/geo/dolag”则计算后文件名为“/obj/geo/dolag_0”，若文件名为“/obj/geo/dolag_999”，则计算后的文件名为若文件名为“/obj/geo/dolag_1000”。
#### getHdaLibraryPath(hda_name)->str
输入hda名，返回hda的Library路径。
+ hda_name：hda的名称，也就是hda保存的文件名。例如dolag.hda，dolag.otl。
返回hda的Library路径，如果找不到则返回None。
### utils
一些有用的功能。
#### class Singleton
单例模式的元类。使用时将元类设为此类即可。
```python
''' python2 '''
class foo():
	__meta_class__ = Singleton
	...

''' python3 '''
class foo(__meta_class__ = Singleton)
	...
```
#### class Subject
实现观察者模式的被观察类。使用时直接继承。
#### lcString(str1, str2)->str, int
计算字符串str1和str2的最长公共子串。返回值第一项为最长公共子串， 第二项为第一个匹配相对于str1的位置。
#### lcSequence(str1, str2)->str
计算字符串str1和str2的最长公共子序列。返回值为最长公共子串。
#### writeClipBoard(text)
将text写入系统剪切板。
#### readClipBoard()->str
返回系统剪切板内容。
### parm
变量相关函数。
#### getParm(parm_desc)->hou.Parm
根据输入获取变量对象。
+ parm_desc：变量的描述。可以是变量的路径、变量对象本身。
返回变量描述所指的变量对象，如果不能根据变量描述找到变量，则返回None。
### node
#### getNode(node_desc, node_type=hou.Node)->hou.Node
根据输入获取节点对象。
+ node_desc：节点的描述。可以是节点的路径、节点对象本身。
+ node_type：节点的类型，只有在节点为此类型的节点时才会返回节点。在houdini中节点的类型是指hou.rampParmType、hou.stringParmType等。此变量还可以是元组等可迭代对象，表示节点可以为其中一种类型。
返回符合要求的节点，否则返回None。