# 简介

本工具集提供了分门别类的Houdini资产，默认全局安装，可以以节点的形式直接调用。
+ 这些资产都放置在DolagPlugin/otls内
+ 资产名称以Dolag开头。
+ 这些资产在Tab菜单中的Dolag分类内，并分为了如下四类：
	1. Attribute：和属性相关的节点
	2. Heightfield：和地形相关节点
	3. Polygon：对Polygon进行处理的节点
	4. Primitive：生成模型的节点
	5. Utility：其他功能性节点
由于节点数目颇多，仅挑选必要节点进行说明。

# 节点说明

## Dolag Parse Json Data To Gemotry
<a id = "parse"></a>

用来解析简单结构的json数据。
由于几何体承载数据类型有限，以及json数据嵌套的复杂性，本节点只支持由字典组成的列表形式的json解析。如下：
```json
[
	{
		"unreal_instance":"StaticMesh'/Game/STF/Pack03-LandscapePro/Environment/Foliage/Grass/SM_GrassGroup01.SM_GrassGroup01'",
		"propagate_dist":420.0,
		"heliophile":0.12,
		"species":"VegA",
		"viability":0.9,
		"viability_radius":0.035,
		"animal_spread_factor":0.9,
		"pioneer_order":0,
		"init_seed_weight":0.6,
		"reproductivity":8,
		"up_align":0.6,
		"plant_into_soil":0.02,
		"hygrophile":-0.15
	},
	{
		"unreal_instance":"StaticMesh'/Game/STF/Pack03-LandscapePro/Environment/Foliage/Grass/SM_FlowerGroup01.SM_FlowerGroup01'",
		"species":"VegB",
		"propagate_dist":350.0,
		"heliophile":0.1,
		"viability":0.85,
		"viability_radius":0.15,
		"animal_spread_factor":0.6,
		"pioneer_order":1,
		"init_seed_weight":0.45,
		"reproductivity":9,
		"up_align":0.6,
		"plant_into_soil":0.015,
		"hygrophile":0.5
	},
	{
		"unreal_instance":"StaticMesh'/Game/STF/Pack03-LandscapePro/Environment/Foliage/Grass/SM_WeathGroup02.SM_WeathGroup02'",
		"species":"VegC",
		"propagate_dist":300.0,
		"heliophile":0.1,
		"viability":0.8,
		"viability_radius":0.3,
		"animal_spread_factor":0.5,
		"pioneer_order":2,
		"init_seed_weight":0.5,
		"reproductivity":6,
		"up_align":0.3,
		"plant_into_soil":0.025,
		"hygrophile":-0.2
	}
]
```
用户可选择Point和Primitive两种几何元素类型，此节点会将解析属性输出到用户选择的几何元素类型中。

## Dolag Divide Points By Weight

此节点有两个输入，第一个输入是待划分的点，第二个输入是用来划分的数据，与[[Dolag Parse Json Data To Gemotry]]搭配有奇效。
节点要求第二个输入包含用以划分的元信息：
1. 节点可以选择要读取的几何元素类型(Point和Primitive)，第二个输入的每一个对应几何元素都代表了一个划分
2. 每个划分都需要有一个属性来表示该划分的权重，节点的Weight Attribute参数控制这一属性
3. 对第一个节点的划分结果需要用类名来标识。如果第二个输入的每一个划分都有可以用于标识的类名，则可以将此类名作为划分结果的类名。
   例如第二个输入有类名分别为Tree、Flower，权重分别为0.1、0.9的划分，其中类名的属性名为foliage_name，则划分的结果也会以foliage_name为类属性名进行标识，包含Tree和Flower两种划分。
   当然也可以不使用第二个输入的类名作为划分的类名，可以使用节点的New Class Name和Class Prefix来分别设置划分类别的属性名和类名的前缀。
   例如当New Class Name为class，Class Prefix为pile_时，划分后点属性可能为：
   + 点1：class：pile_0
   + 点2：class：pile_3
   + 点3：class：pile_2
   + 点4：class：pile_0
显而易见的，这非常时候将[Dolag Parse Json Data To Geometry](#parse)的结果作为此节点的第二个输入。

## Dolag Unreal Mesh Importer

将静态网格的uasset转换为FBX并导入houdini。
下为节点参数:
+ Unreal Path: 资产的ue工程所对应版本的unreal根目录。 例如 F:\Unreal Engine\UE_4.26

+ Unreal Project Path: 资产所在的unreal项目的项目文件。例如 F:\unreal_project\ProcedrualWorld.uproject

+ Asset Path: 静态网格资产在unreal项目中的路径，可以通过右键资产，选择“复制引用”来获取引用，获取引用后还需要删除两个单引号以及资产的类型。例如/Game/Quixel/Mesh/Rock_01.Rock_01;/Game/Quixel/Mesh/Rock_02.Rock_02

+ Output Path: FBX的导出路径。FBX文件会维持其在Unreal项目中的路径。例如，如果Output Path是 C:/tmp而Asset Path为/Game/Mesh/obj1.obj1，那么模型会导出为C:/tmp/Game/Mesh/obj1.FBX
**注意，Unreal Project Path和Asset Path都不能包含空格和汉字**