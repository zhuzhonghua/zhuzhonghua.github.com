    Title: SWF9文件格式-形状(SWF File Format Version9 - Shape)
    Date: 2012-12-21T00:00:00
    Tags: SWF

Shape结构很紧凑，也很灵活。FP渲染起来效率很高。
和大多数的矢量格式一样，在Shape中定义的矢量也是由路径，也就是一组边，定义的。
路径可以是闭合的，也可以是开放的。路径中有直线，曲线，还有一些移动的指令。
指令可以使得，使用一个Shape结构定义好几个不相关的形状。  


填充样式会影响由路径包围的区域的外观。SWF文件中的填充样式里包含颜色，渐变，还有位图图像。  


线段样式会影响路径轮廓的外观。线段样式是关于笔画的粗细和颜色的。  


大多数的矢量格式只允许一个路径中只能包含一种填充和线段样式。而SWF扩展了这个限制，允许每条边都有自己的填充和线段样式。但是如果填充样式在路径中间变化，那么结果是不可知的。  


Adobe Flash创作工具还支持每条边有两个样式，内边和外边，FillStyle0和FillStyle1。FillStyle0永远都是先应用，然后根据需要，决定是否应用FillStyle1。  


#Shape 简介  


一个Shape由如下几种元素组成：  


* CharacterId－16位的数字，在字典里作为唯一标识。CharacterID可以在控制标签中使用，如在PlaceObject里。这样可以重用并且也可以和其他元素组合为更复杂的元素。  
* Bounding Box-包围Shape的矩形  
* Fill Style Array-在Shape中使用的填充样式的列表  
* Line Style Array-在Shape中使用的线段样式的列表  
* Shape Record Array-Shape Record的列表。Shape Record可以是直边，曲边，可以改变样式，移动当前点位置  


#Shape举例  


下面的例子是一组Shape，但是可以由仅仅一个DefineShape标签定义。  


![ShapeExample](/assets/shapeexample.png)   


红圈，红方块还有绿色的圆边矩形都是闭合路径。曲线是一个开放路径。
红方块是由直线组成，红圈是由曲线组成，圆边矩形由曲线交叉直线组成。  


这里有两种填充样式，solid 红和solid 绿，两种线段样式，1像素黑和2像素黑。
红圈和红方块的线段样式和填充样式都一样。
圆边矩形和曲线有相同的线段样式。  


下面就是如何在SWF里描述这个例子：  


###定义填充样式  


1. 首先填充样式由FILLSTYLEARRAY定义，solid 红和solid 绿。
2. 接下来是LINESTYLEARRAY，包含了1像素黑和2像素黑。
3. 接下来是一组Shape Record。  


所有的Shape Record结构相似，但是意义不同。Shape Record可以定义直边，曲边，样式改变和移动当前点。  


###定义曲线  


1. 第一个Shape Record使用2像素宽的线段样式，然后设置StateMoveTo标签，把当前点移动到曲线的顶部。  
2. 接下来的Shape Record是一个曲边，在线段底部结束。路径没有闭合。  


###定义红方块   


1. 接下来的Shape Record使用1像素的线段样式，红色填充样式。然后把当前点移动到红方块的左上角。  
2. 下面的四个Shape Record都是直边。最后一个边必须在左上角结束。FP要求闭合图形必须互相衔接。也就是说第一个和最后一个点必须重合。  


###定义红圈  


1. 下一个Shape Record并不改变任何样式设定，但是会把当前点移动到红圈顶部。  
2. 接下来的8个Shape Record都是曲边。并且最后要停在开始点。  


###定义绿色圆边矩形   


1. 接下来的ShapeRecord使用2像素宽的线段样式，绿色填充样式。然后把当前点移动到绿矩形的左上角。
2. 接下来的12个ShapeRecord是由StraightShapeRecord和CurvedShapeRecord交叉组成。路径在开始的地方结束。  


#Shape结构  


##填充样式  


SWF支持3种非常基础的填充样式。  


* 单色填充 简单的RGB或者RGBA颜色填充Shape的一部分，Alpha255意味着完全不透明填充。Alpha0意味着完全透明填充。在这之间的数值意味着部分透明。  
* 渐进填充 渐进填充可以是线性渐进填充或者放射渐进填充。详细内容参考，Gradients一章。  
* 位图填充 位图填充使用了一个位图ID。有两种类型：裁减的，平铺的。如果填充区域超过了位图边界，裁减位图填充就重复使用位图边缘的颜色。而平铺填充会重复使用该位图。  


##FILLSTYLEARRAY  


填充样式数组枚举了一组填充样式。填充样式数组的格式如下：  


![FILLSTYLEARRAY](/assets/fillstylearray.png)


##FILLSTYLE  


填充样式的格式如下：  


![Fillstyle](/assets/fillstyle1.png)
![Fillstyle](/assets/fillstyle2.png)


##线段样式  


线段样式数组枚举了一组线段样式。  


##LINESTYLEARRAY  


![LineStyleArray](/assets/linestylearray.png)  


##LINESTYLE  


线段样式表示的是线段的颜色和宽度。  


![LineStyle](/assets/linestyle.png)   


##LINESTYLE2  


LINESTYLE2建立在与LINESTYLE兼容的基础上，允许使用新类型的join和cap，还可以缩放，填充笔画。LINESTYLE2只能用于DefineShape4。   


LINESTYLE只允许圆连接和圆cap，LINESTYLE2允许miter join和bevel join，square cap和no cap。下面描述了完整的join和cap：   


![LineStyle2JoinCap](/assets/linestyle2joincap.png)   


在miter join中使用LINESTYLE2时，必须指定MiterLimitFactor，用来计算最大miter长度。  


最大miter长度 ＝ LINESTYLE2 MiterLimitFactor X LINESTYLE2 Width  


如果miter join超过了最大miter长度，FP会剪断。MiterLimitFactor的值是8.8格式。  


LINESTYLE2还包含一些选项来做像素微调，来校正垂直或者水平线段。  


![LineStyle2](/assets/linestyle21.png)   
![lineStyle2](/assets/linestyle22.png)   
![LineStyle2](/assets/linestyle23.png)   


##Shape结构  


SHAPE结构中没有填充样式和线段样式。  


##SHAPE  


SHAPE结构在DefineFont中使用，用来定义字型。  


![SHPAE](/assets/shape.png)  


##SHAPEWITHSTYLE  


SHAPEWITHSTYLE扩展了SHAPE结构，包含了填充样式和线段样式。在DefineShape中使用。  


![SHAPEWITHSTYLE](/assets/shapewithstyle.png)  


下面的图描述了SHAPEWITHSTYLE的结构：  


![SHAPEWITHSTYLEDIAGRAM](/assets/shapewithstylediagram.png)  


首先定义填充样式数组和线段样式数组，只定义一次，后面可以引用。   


蓝色区域代表SHAPERECORD数组。
第一个SHAPERECORD从填充样式数组中选择一个，然后把当前点移动到SHAPE的开始。
之后是一组边来定义SHAPE。下一个SHAPERECORD重新选择了一个填充样式，并且之后的边都采用新的样式。  


此标签完全是“自治”标签，所有引用的样式都是在本标签内定义的。  


##Shape Records 


有四种类型的SHAPERECORD：  


1. End Shape Record
2. Style Change Record  
3. Straight Edge Record  
4. Curved Edge Record  


在一组SHAPERECORD内，每一个独立的SHAPERECORD都是字节对齐的。
在下一个SHAPERECORD开始之前，前一个SHAPERECORD都是先对齐到字节。  


每一个SHAPERECORD都是由一个TYPEFLAG开始的。
如果TYPEFLAG的值是0，那么此SHAPERECORD就是“非edge”的，之后的5位提供具体信息。  


##End Shape Record  


End Shape Record简单的表明ShapeRecord数组结束，它的TypeFlag为0，并且之后的5位也是0。  


![EndShpeRecord](/assets/endshaperecord.png)  


##Style Change Record  


Style Change Record也是一个“非edge”Record，它可以做如下几件事情：  


1. 选择填充样式或线段样式。  
2. 移动当前点。  
2. 修改当前的填充样式和线段样式。  


因为改变样式都发生在一条路径的最开始部分，因此在单个record内进行多个操作是比较有用的。
例如，假想一下DefineShape标签定义了一个红圈和蓝方块。在红圈闭合之后，需要移动当前点，改变当前样式。
Style Change Record可以在一个Record里做到这些动作。  


![StyleChangeRecord](/assets/stylechangerecord1.png)  
![StyleChangeRecord](/assets/stylechangerecord2.png)  
![StyleChangeRecord](/assets/stylechangerecord3.png)  


在第一个shape record中，movedeltax 和movedeltay是和shape的起始处相关的。  
在之后的shape record中，movedeltax 和movedeltay是和当前点相关的。  


样式数组从下标1开始，而不是从0开始。FillStyle＝1引用的是填充样式数组的第一个样式，FillStyle＝2引用的是第二个。
等等。填充样式的下标0表示没有填充，线段样式的下标0表示没有笔画。
最开始，填充样式和线段样式的下标都是0。  


##FillStyle0和FillStyle1  


Flash创作工具支持每条边2个样式：FillStyle0和FillStyle1。
对于不重叠不交叉的Shape，应该使用FillStyle0。
对于重叠的Shape，情况比较复杂。  


例如，如果一个Shape由两个重叠方块组成，并且只定义了FillStyle0，FP会在重叠的地方渲染一个洞。
这个区域可以使用FillStyle1。在这种情况下，对于任何方向向量，Fillstyle0是左边的颜色，FillStyle1是右边的颜色。  


![FillStyle01](/assets/fillstyle01.png)  


##Edge Record


Edge Record的TypeFlag是1。内含两种类型：直的，弯的，由StraightFlag标识。


##StraightEdgeRecord


StraightEdgeRecord存储的是XY增量。
把增量添加到当前的位置，构成了新位置。
在原来的点和现在的点之间画一条线。  


此Record支持三种类型的线：


1. 任意线   
2. 水平线   
3. 垂直线   


任意线存储的是XY增量，其它两种线只存储一个轴的增量。


![StraightEdgeRecord](/assets/straightedgerecord.png)


##CurvedEdgeRecord


SWF文件和大多数其他的类似文件格式最大的不同在于使用的是平方贝塞尔曲线，而不是立方贝塞尔曲线。
PostScript跟大多数的画图软件一样，使用的是立方贝塞尔曲线。
SWF文件之所以使用平方贝塞尔是因为可以更紧凑，更高效。


下面的图展示了两种曲线：


![QuadraticCubicBezierCurves](/assets/quadraticcubicbeziercurves.png)


平方贝塞尔曲线有三个点：2个on-curve定位点，1个off-curve控制点。
立方贝塞尔曲线有四个点：2个on-curve定位点，2个off-curve控制点。


curved-edge record存储了两个XY增量。三个点的计算方式如下：


1 第一个定位点是当前的位置。  
2 控制点是当前位置 plus Control增量。   
3 另一个定位点是当前位置 plus Control增量 plus Anchor增量。  


最后一个定位点成为了新的当前点。


![CurveEdgeRecord](/assets/curveedgerecord.png)


##平方和立方贝塞尔曲线之间的转换  


把off-curve控制点用两个off-curve控制点替换。
两个off-curve控制点分别放在两个on-curve定位点和原来的off-curve控制点之间。
新的off-curve控制点在线段的2/3处，距离原来的off-curve控制点更近。可参见上图。  


平方贝塞尔曲线只能近似模拟立方贝塞尔曲线，因为一个是三阶曲线，一个是二阶曲线。
这个过程中会递归地分割曲线，直到两个类型的曲线误差可以接受。  


这里有一些讨论关于立方贝塞尔曲线由平方贝塞尔曲线模拟的：


* Converting Bezier Curves to Quadratic Splines at stevehollasch.com/cgindex/curves/cbez-quadspline.html
* TrueType Reference Manual, Converting Outlines to the TrueType Format at developer.apple.com/fonts/TTRefMan/RM08/appendixE.html


##Shape 标签  


##DefineShape  


由DefineShape定义的Shape可以由后面的控制标签使用，如PlaceObject。
ShapeID作为唯一标识索引。
ShapeBounds是包含了Shape的矩形区域。
SHAPEWITHSTYLE包含了所有的路径，填充和线段样式。


最低版本要求是SWF1。  


![DefineShape](/assets/defineshape.png)


##DefineShape2  


DefineShape2扩展了DefineShape，支持多余255种样式，一个Shape中有多个样式表。  


最低版本呢要求是SWF2。  


![DefineShape2](/assets/defineshape2.png)


##DefineShape3


DefineShape3扩展了DefineShape2，支持RGBA颜色。  


最低的版本要求是SWF3。  


![DefineShape3](/assets/defineshape3.png)


##DefineShape4


DefineShape4扩展了DefineShape3，使用了一个新的线段样式。
LINESTYLE2允许使用新类型的join和caps，还有缩放选项和填充一个笔画。  


DefineShape4除了指定Shape边界，还指定了边的边界。
就像Shape边界在笔画外围计算，边的边界是在边的外围计算，参看下图。
EdgeBounds字段辅助FP计算特定的布局。


![EdgeBounds](/assets/edgebounds.png)


另外DefineShape4还添加了两个标记，UseNonScalingStrokes，UsesScalingStrokes。
这些标记可以帮助FP创建更好的重绘区域。  


最低版本要求SWF8。


![DefineShape4](/assets/defineshape4.png)
