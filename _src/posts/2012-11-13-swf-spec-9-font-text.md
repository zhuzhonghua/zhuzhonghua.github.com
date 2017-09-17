    Title: SWF9文件格式-字体和文本(SWF File Format Version9 - Fonts and Text)
    Date: 2012-11-13T00:00:00
    Tags: SWF


SWF8支持很多渲染文本的方法。在SWF6之后，所有的文本都使用Unicode编码，避免了文本和字符串在不同地区之间的区别。提醒一下，尽管FP是完全支持Unicode的，但是它仅仅支持从左到右的简单文本，还不支持Hebrew，Arabic，Thai等其它复杂的文本。  


#字型文本和设备文本  

SWF支持两种文本类型：字型文本和设备文本。字型文本通过在SWF中嵌入字符形状实现，设备文本使用的是各自平台的文本渲染功能。  

字型文本在所有平台上看起来都一样。并且可以使用标准反锯齿功能。在SWF8之后，也可以使用高级文本渲染引擎实现。相对设备文本，使用字型文本会让生成的SWF文件更大，尤其是在一个很大的字符集里使用了很多字符。  

设备字体由操作系统本身实现反锯齿效果，不同平台表现不一样。有两种方式指定设备文本：通过字体名称在系统中查找，或使用一个特殊的字体名称，映射到系统的高可用字体，尽量选择相近的字体。  

字型文本字符通过DefineFont，DefineFont2，DefineFont3定义。设备文本字体通过DefineFont和DefineFontInfo一起定义，或者直接通过DefineFont2定义。定义设备文本字体的DefineFont2，如果用于动态文本的话，不需要包含任何字符图像。DefineFont和DefineFont2标签可以同时为某些文本提供字型文本支持，并且为其它文本提供设备文本支持，只要提供了图像和字符编码。  

#静态文本和动态文本  

文本可以被定义为静态的，或者在SWF4之后，动态的。动态文本可以在运行时改变，并且还可以编辑。动态文本可以模拟几乎静态文本的所有特性。除了静态文本精确的单个字符的位置，如果不考虑实现难度和版本兼容性的话。动态文本还有很多静态文本没有的格式化功能。丰富的格式化功能由HTML的一个子集支持。  

静态文本通过DefineText定义。动态文本通过DefineEditText定义。这两个标签都可以引用DefineFont或者DefineFont2来获取字符来源。DefineEditText使用DefineFont2，而不是DefineFont。DefineText既可以使用DefineFont也可以使用DefineFont2。  

DefineEditText提供了一个标记，来指明使用字型文本还是设备文本(译者注：如果有谁知道这个标记请告诉我，我没找到)，而DefineText并没有。这意味着在静态文本中，SWF没办法指明使用字型文本还是设备文本。这个问题由运行时标记解决。一般情况下，所有静态文本都使用字型文本。当FP插件被嵌入到HTML页面中，一个叫devicefont的HTML标签选项会让FP把所有静态文本都渲染成设备文本。字型文本作为后备。DefineEditText能够指明使用静态文本还是字型文本的能力是被认为好于DefineText的另一个原因。  

#字型文本  

##字型定义  

字型是在一个称为EMSquare的标准坐标空间里定义的。相同的字型集合被用于每一个给定的字体大小。为了以不同的大小渲染字型，FP通过把EM坐标缩放到点阵坐标。
  

![glyphdef](/assets/glyphdefinitions.png)  


字型字体－不使用高级文本渲染引擎－没有包含任何提高小字体可读性的提示信息。然而反锯齿会很好的提高缩小字体的可读性。即使在12点的大小时(看作100％)，字型文本还是易读的。12点或更低，在渲染可读文本时，建议采用高级反锯齿。这样小字体也会有很好的可读性，并且还会包含额外字体元信息来提高渲染效果。  

TTF可以很好的转为SWF字型。一个简单的算法是使用Quadratic Bezier 曲线替换Quadratic B－splines。  

例子：  

左边是TTF中Arial的字符b的字型。它是由弯曲的和直线的边组成。方块指示的是on-curve点，x指示的是off-curve点。黑圈是字型的参考点。蓝色边框线指示的是字型的轮廓。  

##EM Square  

EMSquare 是想象的空间，用于字型大小和对齐。EMSquare足够大，包含了所有字型，包括重音字型。包括字体的上部，下部，还有一些额外空间来避免文本线的冲突。  

![emsquare](/assets/emsquare.png)  

SWF字型是在1024＊1024的空间中定义的。其它来源的字型(如TTF)会有不同的EMSquare。为了使用这些字型，应该缩放到1024大小。  

##把TTF转换为SWF字型  

TTF字型是使用Quadratic B－Splines定义的，这可以很容易的转换为SWF字型的Quadratic Bezier曲线。  

一个TTF B－splines是由一个on-curve点和很多off-curve点，再跟着一个on-curve点组成的。两个off-curve点的中点保证在曲线上。SWF的Bezier曲线是由一个on-curve点和一个off-curve点，再跟一个on-curve点组成。  

从TTF到SWF曲线的转换就是在两个off-curve点的中点插入一个on-curve点。  

例子：  

下面是4个点的B－splines，P0和P3是on-curve点，P1和P2是连续的off-curve点。  

![bsplines](/assets/bsplines.png)  

这个曲线可以由两个Bezier曲线组成，在P1和P2的中点插入M，结果就是两个Bezier曲线，P0P1M和MP2P3。  

完整的转换过程描述如下：  

1，Y坐标反向（TTF中Y向上，SWF中Y向下）  
2，把TTF中EMSquare坐标缩放到SWFEMSquare坐标中。  
3，在每对off-curve的中点插入on-curve点。  

##字距和差值信息  

字距指的是两个字型之间的水平距离。有些字体系统在每个字体定义时都存储了字距信息。SWF文件存储的是每个字型实例(字型文本块的每个字符)的字距信息。这样称为差值。  

![kerning](/assets/kerning.png)  

在前面的例子里，A字型和V字型重叠了。这种情况下，差值要比A的宽度窄。  

##高级文本渲染引擎  

字型可以使用普通的FP渲染器渲染，或者在SWF8之后，使用高级文本渲染引擎渲染。  

高级文本渲染引擎是一种在FP渲染器里的高质量文本渲染器。对于文本来说，这个高级系统相对一般的渲染器有如下优势：  

+ 即使小字体也可读  
+ 即使小字体看起来也美观  
+ 对于非常清晰的文本(左对齐的动态文本)支持像素对齐  
+ 提高了字型字体的性能  
+ 支持液晶屏幕的子像素渲染  

高级文本渲染引擎的一个局限是，它没有字型文本那样的动画效果。  

高级文本渲染引擎使用Continuous Stroke Modulation(CSM)参数来调整表现。CSM是一种笔画粗细和边缘锐度的连续调整。CSM使用两种渲染参数：内部和外部切断。这些参数的最佳值是有很大的主观性，并且依赖于用户的喜好，照明条件，显示属性，字样，前景和背景颜色，还有大小。然而大部分情况下，可以通过一小部分内插值的方法来实现高质量效果。  

创建了高级反锯齿边缘的功能，含有外切断(在这之下，边缘不画)和内切断(在这之上，边缘不透明)。在两个切断值之间是一个线性函数，从外切断的0到内切断的最大值。  

调整内外切断的值会影响笔画粗细和边缘锐度。两个参数之间的空间是经典的反锯齿方法中的过滤半径的两倍。窄一些的空间提供了更锐利的边缘，而宽一些的空间提供了更柔和的边缘。空间是0时，生成的图像是双层图像；当空间很大时，会生成像水一样的边缘。一般的，在小字体情况下，用户喜欢锐利的，高对比度的边缘；而对于动画效果的文本和大字体，喜欢更柔和的边缘。  

外切断是负值，内切断是正值，中点一般在0左右。把中点调向负的方向，会增加笔画粗细；调向正的方向，会减少笔画粗细。外切断应该永远小于等于内切断。  

FP创建了一个CSM的表格，为每个高级反锯齿字体提供文本大小和文本颜色。这个表格提供了一个CSM设置的集合，包含了很大范围的字体大小。你还可以定义一个用户自定义的表格，来代替默认值，使用AS的函数，setAdvancedAntiAliasingTable()  

##DefineFont 和 DefineText  

在SWF文件格式支持的四种文本类型中，最复杂的是静态字型文本。其它类型使用的是定义静态字型文本的简单变形。  

静态字型文本使用两个TAG定义：  

+ DefineFont定义字型集合  
+ DefineText定义使用那个字体显示的文本字符串  

DefineFont标签定义了所有用户DefineText的字型。DefineFont包含了一组SHAPERECORD，描述了字型的边缘。这些SHAPERECORD同样用于DefineShape，定义的是非文本内容。为了把文件大小降到最低，只有使用的字型才会包含进来。DefineText存储的是实际要显示的文本字符串，由字型下标表示。还有文本的包围盒，转换矩阵，属性如颜色，大小。  

DefineText包含了一组TEXTRECORD。TEXTRECORD记录了字体，颜色，大小，还有下一个字符的XY坐标。这个类型会应用到接下来的所有字符，直到另一个TEXTRECORD出现。TEXTRECORD还包含一组下标，引用了当前字体的字型表。字符不是由字符编码引用，而是由字型表的下标引用。字型数据还包含文本中每个字符的差值。  

##静态字型文本举例  

考虑一下使用24大小，以Arial字体显示bob。  

首先使用DefineFont定义字型。字型表有两个SHAPERECORD。0位置是小写B的形状，1位置是小写o的形状。(第二个b重复了，不需要再定义)DefineFont还包括一个唯一ID，接下来的DefineText可以引用。  

第二，使用DefineText定义文本。TEXTRECORD设置第一个字符的位置，选择Arial字体，设置大小为24，这样字体被缩放到正确大小。(字型是在EMSquare坐标系中定义的，大小是DefineText的一部分)还包含一组GLYPHENTRY。每一个GLYPHENTRY都引用字体形状数组的一个下标。在这个例子里，第一个GLYPHENTRY引用下标0(对应字符b)，第二个引用下标1(o)，第三个引用下标0(b)。每个GLYPHENTRY还包含差值来精确定位字型的位置。  

下面的图描述了DefineText如何与DefineFont交互。  

![definetextfont](/assets/definetextfont.png)  

#字体标签  

##DefineFont  

DefineFont定义了特定字体的每个字型的边框形状。只有在DefineText中使用的字型才会被定义。DefineFont不能用于动态文本。动态文本需要DefineFont2。  

最低文件版本是SWF1。  

![definefont](/assets/definefont.png)  

字体ID唯一标识了这个字体。可以用于DefineText引用。在SWF文件中字体ID也必须唯一。  

如果DefineFontInfo和DefineFont一起的话，要注意DefineFont中的字型顺序要和DefineFontInfo中的字符顺序一致，以字符码排序。OffsetTable和GlyphShapeTable一起使用。这两个表有相同数量的条目，offsets中的顺序和shape中的顺序，一一对应。OffsetTable指定了在GlyphShapeTable中的位置。每一个Offset条目存储的是offset table开始到相应shape的差值(BYTES)。因为GlyphShapeTable与OffsetTable相邻，那么每个表格中的条目数量(字体定义的字型数目)可以通过offsettable中的第一个条目除以2得到（译者注：读者可以自己计算一下，提示：offsetTable中条目的类型。如果不懂，可以联系译者）。  

在GlyphShapeTable中的每一个SHAPE的第一个STYLECHANGERECORD不使用LineStyle和LineStyles字段。这种情况下，这两个字段都被设为1。  

##DefineFontInfo  

DefineFontInfo定义的是字型字体到设备字体的一个映射。提供了一个字体名和样式，传到平台的文本的渲染引擎，还有一个字符编码表格指定了相应字体的字型，这样可以把DefineText的字型下标转换为字符串。  

DefineFontInfo并不会强制把字型文本转换为设备文本。它仅仅是可以这样做。实际是根据devicefont的值或者DefineText中的UseOutlines的值来决定。如果设备字体不可用，FP会继续用字型字体。  

最低版本要求是SWF1。  

![definefontinfo](/assets/definefontinfo1.png)
![definefontinfo](/assets/definefontinfo2.png)  

CodeTable中的条目必须是字码的升序排序。同时CodeTable中条目的顺序必须和引用的DefineFont中的字型顺序一致。这也要求DefineFont中的字型顺序要保持一致。  

SWF6和之后的文件中的文本都要求Unicode文本编码。这样所有的字符编码表都用的是UCS－2(UCS－2是第一个UTF－16的64K字符码)。这种编码为每个字符使用固定2字节大小。这就是说，如果DefineFontInfo出现在SWF6或之后的文件中，那么FontFlagsWideCodes一定被设为1，FontFlagsShiftJIS和FontFlagsANSI一定设为0，CodeTable一定使用的是UI16的UCS－2的编码组成。  

另外一点是在SWF6和之后的文件中，字体名一定是用UTF8编码的。在SWF5或之前，文件名是以平台特定的方式编码的，使用系统的码页。平台会使用当前的码页来显示，这样导致不一样的平台之间的显示不一样。如果平台是ANSI系统，字体名称就是ANSI字符串。如果平台是日本的Shift－JIS系统，字体名就会被翻译为Shift－JIS字符串。还有很多其他平台的码页。这种本地依赖非常不好，这也是为什么SWF6转向了统一编码。需要注意的是DefineFontInfo中的字体名不是空字符串结尾的。而是通过FontNameLen来定义长度的。FontNameLen指定了FontName需要的字节数，这不一定要和字符数量一致，因为有些编码，每个字符会使用多于1个字节。  

字体名是逐字使用的，直接传入平台的字体系统，定位字体。然而有很多特殊的间接字体名是,根据不同平台映射到不同的字体的。这些间接映射是硬编码到平台特定的FP中的，这些字体是在平台默认字体和最可用字体之间选择的。或者这样考虑，间接映射是为了让所有平台看起来尽可能一致。  

下面的表就是支持的间接映射字体名。  

![western](/assets/westernfonts.png)  
![japanese](/assets/japanesefonts.png)  

##DefineFontInfo2  

当生成SWF6或之后的文件时，建议使用DefineFontInfo2，而不是DefineFontInfo。DefineFontInfo2和DefineFontInfo是一样的，除了它增加了一个language code字段。如果使用DefineFontInfo，这个字段被认为是0，这将会导致，平台不一样，表现不一样。  

最小的版本要求是SWF6。  

![definefontinf2](/assets/definefontinfo22.png)  

##DefineFont2  

DefineFont2扩展了DefineFont功能。包括：  

+ OffsetTable中32位的条目，可以有多于64K的字型字体。  
+ 合并DefineFontInfo的功能，映射到设备字体。  
+ 提高了动态文本的字体规格。  

DefineFont2是唯一能用于动态文本的标签。  

最小的版本要求是SWF3。  

![definefont2](/assets/definefont21.png)  
![definefont2](/assets/definefont22.png)  
![definefont2](/assets/definefont23.png)  
![definefont2](/assets/definefont24.png)  

在SWF6和之后的文件中，DefineFont2和DefineFontInfo一样，要求Unicode。CodeTable(还有OffsetTable，GlyphTable， FontAdvanceTable)必须以字码顺序排序。如果DefineFont2仅仅用于动态文本，并且也不需要字型渲染，那么NumGlyphs被设置为0，并且忽略所有相关条目。这样会减少DefineFont2的大小。没有字型的DefineFont2不能够显示静态文本(需要字型索引)和字型文本(需要字型形状定义)。  

布局信息(ascent, descent, leading, advance table, bounds table, kerning table)仅仅对动态文本有用。此信息代替了用于静态字型文本的每个字符的布局信息。DefineFont2中的布局信息是相当标准的字体规格信息，可以从标准的字体定义中抽取，如TTF。  

就像在DefineFont里一样，DefineFont2里每个SHAPE的第一个STYLECHANGERECORD也不使用LineStyle和LineStyles字段，并且也被设置为1。  

DefeinFont2还保留了字体边框表和字距表。这些信息在FP7之后就不用了，但是必须被保留下来，为了DefineFont2的一致性。为FontBoundTable提供一个最小的RECT，把KerningCount永远设置为0，这样可以忽略掉FontKerningTable。

##DefineFont3  

DefineFont3是和DefineFontAlignZones一起在SWF8中引入的。当使用高级反锯齿功能时，DefineFontAlignZones是可选的，但是推荐使用；它会修改DefineFont3的行为。  

DefineFont3扩展了DefineFont2的功能，通过把GlyphShapeTable中的SHAPE坐标提高20倍。所有的EMSquare坐标在导出时，都乘以20，这样就可以精确到1/20个单位。这可以定义更精确的字型，有更好的可视效果。  

最低的版本要求是SWF8。  

![definefont3](/assets/definefont31.png)   
![definefont3](/assets/definefont32.png) 
![definefont3](/assets/definefont33.png) 
![definefont3](/assets/definefont34.png)   

##DefineFontAlignZones  

DefineFongAlignZones可以修改DefineFont3。高级文本渲染引擎使用alignment zones来建立字型边界，实现像素对齐。alignment zones对于高质量的字体很重要。  

Alignment Zones为每个有很强横竖感的字型都定义了一个包围盒。盒子是通过x坐标，粗细，y坐标，高度描述的。高度或粗细很小时，被设置为0。  

例如考虑一下字母I。  

![I](/assets/definefontalignzonesi.png)  

I在底部和顶部有很强烈的水平感。在主干的边缘，而不是在条形顶部或者衬线上，有很强烈的垂直感。在字母中心块上的强烈横竖感，决定了alignment zones。  

最低的版本要求是SWF8。  

![definefontalignmentzones](/assets/definefontalignmentzones.png)  
![zonerecord](/assets/zonerecord.png)  
![zonedata](/assets/zonedata.png)  

###Kerning Record  

Kerning Record定义的是在EMSquare坐标系中，两个字型的距离。某对字型更近或更远的话，看起来会更美观。FontKerningCode1和FontKerningCode2是两边的字符编码。FontKerningAdjustment字段有符号的整数，会加在左边字符的差值上。  

![kerningrecord](/assets/kerningrecord.png)  

##DefineFontName  

DefineFontName包含了嵌入在SWF文件里的字体名和版权信息。  

最低的版本要求是SWF9。  

![definefontname](/assets/definefontname1.png)  
![definefontname](/assets/definefontname2.png)  

#静态文本标签  

##DefineText  

DefineText定义了一块静态文本。描述了字体，大小，颜色和每个字符的精确位置。  

最低的版本要求是SWF1。  

![definetext](/assets/definetext.png)  

TextBounds字段是一个矩形区域，包含了这个文本块的所有字符。  

GlyphBits和AdvanceBits定义了用于GlyphIndex和GlyhAdvance字段(GLYPHRECORD中的条目里的)的位数。  

###TextRecord  

一个TEXTRECORD指定了接下来的文本样式。可以被用于选择字体，改变字体颜色，改变大小，插入换行，或者设置下一个字符的XY坐标。直到下一个TEXTRECORD出现。  

TEXTRECORD还定义了文本的所有字符。字符是通过索引到当前字体的字型表，而不是字符编码。每一个TEXTRECORD都包含一组字符，共享相同的样式，并且在同一行上。  

![textrecord](/assets/textrecord.png)  

FondID字段用于选择前面定义的字体。唯一标识DefineFont或者DefineFont2的ID。  

TextHeight字段定义了字体的高度，用TWIPS单位。如50像素就是TextHeight＝1000。  

XOffset字段定义了文本框矩形左边到字型引用点(EMSquare中第一个曲线段偏离的点)的偏移。一般引用点都在底部，接近字型的左边(参看GlyphText的例子)。XOffset一般用来创建缩进文本或者非左对齐文本。如果没有指定XOffset，那就默认0。  

YOffset字段定义了文本矩形上边到字型引用点的偏移。这个字段一般用于插入断行，把文本移动到新行的开始。  

GlyphCount字段定义了字符个数，还有GLYPHENTRY表格的大小。  

###GlyphEntry  

GlyphEntry描述的是单个字符，由一个引用到当前字体字型表的索引和差值组成。差值就是当前字符引用点到下一个字符引用点的水平距离。  

![glyphentry](/assets/glyphentry.png)  

##DefineText2  

DefineText2几乎和DefineText是一样的。唯一的区别是在DefineText2中的TEXTRECORD使用RGBA来定义字体颜色。这可以产生半透明甚至全透明的字符。  

由DefineText2定义的文本永远使用字型渲染。设备文本永远不包含透明性。  

最小的版本要求是SWF3。  

![definetext2](/assets/definetext21.png)  
![definetext2](/assets/definetext22.png)  

#动态文本标签  

##DefineEditText  

DefineEditText定义的是动态文本对象，或者文本域。  

文本域和AS里的变量名关联在一起。可以读写变量内容，并且和显示保持一致。如果ReadOnly没有设置，那么用户就可以交互的修改内容。  

这里使用的字体一定是DefineFont2定义的，而不能是DefineFont。  

最低的版本要求是SWF4。  

![defineedittext](/assets/defineedittext1.png)  
![defineedittext](/assets/defineedittext2.png)  
![defineedittext](/assets/defineedittext3.png)  
![defineedittext](/assets/defineedittext4.png)  
![defineedittext](/assets/defineedittext5.png)  

如果HTML字段设置了，InitialText的内容就会被解释为HTML的一个子集。下面是所有支持的标签。  

![html](/assets/html1.png)  
![html](/assets/html2.png)  

##CSMTextSettings  

除了高级文本渲染，渲染引擎还支持修改文本域的标签。CSMTextSetting会修改前面的DefineText，DefineText2，DefineEditText。CSMTextSettings可以打开或者关闭文本域的反锯齿，还可以用于定义quality和option。  

最低的版本要求是SWF8。  

![csmtextsetting](/assets/csmtextsettings1.png)  
![csmtextsetting](/assets/csmtextsettings2.png)  
![csmtextsetting](/assets/csmtextsettings3.png)  

粗细和锐度字段会应用到特定文本域，这会覆盖那个文本域的默认值。  

在渲染时，通过如下方式计算：  

![csmtextsettingcalc](/assets/csmtextsettingcalc.png)  

计算时使用字体大小会生成更加线性的缩放效果，并且进行很明显的文本缩放时，线性缩放会生成不好的近似效果。进行文本缩放时最好使用默认表格作为反锯齿表格。
