# shader学习

## 概述

shader是给GPU执行的程序，中文名叫着色器。

着色器是运行在图形处理单元上的。

渲染流水线，模型投影，定点着色

一般主要有固定管线着色器，顶点片着色器，表面着色器

顶点shader：干预模型形状的

像素点shader：干预像素上色的

模型定点运算时，可以加入顶点shader进行干预顶点位置，定点着色时，加入像素shader来干预像素的上色。

## 编程语言

Untiy使用shaderLab来进行着色程序的编写，对不同平台进行编译，重点支持Cg语言（与C相似）

## 语法基础

### 1.定义一个Shader每一个着色程序都要有一个shader

```
Shader "name" { //name shader名字
	//定义的一些属性，定义在这里的徽在属性查看器中显示
	[Propeties]
	//子着色器列表，一个shader必须至少有一个子着色器
	Subshaders:{...}
	//如果子着色器显卡不支持，就会降级即Fallback操作
	[Fallback]
  
}
```

### 2.Properties的定义

```
1.name("display name", type) = 值;
name指的是属性的名字，Unity中用下划线开始_Name;
display name 是在属性检查器的名字;
type:这个属性的类型
值:这个属性的默认值
2.类型:
Float，Int，Color(num, num, num, num)(0~1)Vector(4维向量), Range(start, end);
2D:2D纹理
Rect：矩阵纹理属性
Cube：立方体纹理属性
3D：3D纹理属性
name("displayname", 2D) = "name"{options}
3.Options:纹理属性选项
TexGen：纹理生成模式，纹理自动生成纹理坐标的模式，顶点shader将会忽略这个选项
ObjectLinear, EyeLinear, SphereMap,CubeReflect CubeNormal
LightmapMod:光照贴图模式如果设置这个选项，纹理会被渲染器的光线贴图所影响。
```

#### 例：

``` 
_Range("range value", Range(0, 1)) = 0.3; //定义一个范围
_Color("color", Color) = (1, 1, 1, 1); //定义一个颜色
_FloatValue("float value", Float) = 1; //定义一个浮点
_MainTex("Albedo", Cube) = "skybox"{TexGen CunbeReflect} //定义一个立方贴图纹理属性
```

### 3.SubShader

```
1.SubShader{[Tags], [CommonState], Pass{}}
子着色器有标签（Tags），通用状态，通道列表组成，它定义了一个渲染通道列表，并可选为所有通道初始化需要的通用状态;
2.SubShader渲染的时候，将优先渲染一个被每个通道所定义的对象
3.通道的类型：RegularPass，UsePass， GrabPass
4.在通道中定义状态同时对整个子着色器可见，那么所有通道可以共享状态
```

#### 例：

```
SubShader {
  Tags{"Queue", "Transparent"}
  Pass {
    Lighting Off //关闭光照
    ....
  }
}
```

### 4.Tags

```
Tags{"标签1" = "value1" "key2" = "value2"}
标签的类型：
Queue tag 队列标签
RenderType tag渲染类型标签
DisableBatching tag 禁用批处理标签
ForceNoShadowCasting Tag强制不投阴影标签
IgnoreProjecttor忽略投影标签
CanUseSpriteAtlas Tag使用精灵图集标签
PreviewType Tag浏览类型标签
```

### 5.Pass

```
subshader包装了一个渲染方案，这些方案由一个个通道（Pass）来执行的，SubShader可以包括很多通道快，每个Pass都能使几何体渲染一次
Pass基础语法
Pass{[Name and Tags][RenderSetup][Texture Setup]}
Pass块的Name引用此Pass，可以在其他着色器的Pass块中引用它，减少重复操作，Name命令必须大写
```

### 6.RegularPass渲染设置

```
1.Lighting光照：开启关闭定点光照 On/Off
2.Maaterial{材质块}：材质，定义一个使用定点光照管线材质
3.ColorMaterial：颜色集 计算定点光照时使用顶点颜色
4.SeparateSpecular：开光状态 开启或关闭顶点光照相关的镜面高光颜色 On/Off
5.Color设置定点光照关闭时所使用的颜色
6.Fog{雾块}：设置雾参数
7.AlphaTest：Alpha测试
8.ZTest：深度测试模式
9.ZWrite：深度写模式
10.Blend：混合模式SourceBlendMode，DestBlendMode，AlphaSourcesBlendMode， AlphaDstBlendMode
11.ColorMask颜色遮罩：设置颜色遮罩，颜色值可以由RGB或A或0或R,G,B,A的组合，设置为0关闭所有颜色通道渲染
12.Offset偏移因子：设置深度偏移
```

### 7.特殊通道Pass

```
1.UsePass：插入所有来自其他着色器的给定名字的通道
UsePass"Shader/Name", Name为着色器通道
UsePass"Specular/BASE" //插入Specular中为Bass的通道
2.GrabPass{}：一种特殊通道类型，他会捕获物体所在的位置的屏幕内容，并写入一个纹理中，这个纹理能被用于后续通道中完成一些高级图像特效，后续通道可以使用
_GrabTexture进行访问
3.GrabPass{"纹理名称"}捕获屏幕内容到指定纹理中，后续通道可以通过纹理名称访问
```

### 8.Fallback

```
降级：定义在所有子着色器之后，如果没有任何子着色器能运行，则尝试降级
Fallback"着色器名称"
Fallback Off没有降级并且不会打印任何警告
```

### 9.Gategory分类

```
1.分类是渲染命令的逻辑组。例如着色器可以有多个子着色器，他们都需要关闭雾的效果和混合
Shader "xxxx" {
  Categroy {
    Fog{Mode Off}
    SubShader{...}
    SubShader{...}
  }
}
```























