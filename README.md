
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=2 orderedList=false} -->
<!-- code_chunk_output -->

- [拼图](#拼图)
  - [初始化(Initialization)](#初始化initialization)
  - [事件（Events）](#事件events)
  - [选择器（Selectors）](#选择器selectors)
  - [物体/对象（Objects）](#物体对象objects)
  - [材质（Materials）](#材质materials)
  - [动画（Animation）](#动画animation)
  - [相机（Camera）](#相机camera)
  - [场景（Scenes）](#场景scenes)
  - [杂项（Miscellaneous）](#杂项miscellaneous)
  - [时间（Time）](#时间time)
  - [HTML](#html)
  - [AR/VR拼图](#arvr拼图)
  - [声音（Sound）](#声音sound)
  - [物理和约束（Physics&Constraints）](#物理和约束physicsconstraints)
  - [后期效果（Post-processing）](#后期效果post-processing)
  - [字典（Dictionaries）](#字典dictionaries)
  - [变量（Variables）](#变量variables)
  - [函数（Procedures）](#函数procedures)
  - [拼图库（Puzzles Library）](#拼图库puzzles-library)
  - [拼图库（Puzzles Library）](#拼图库puzzles-library)
<!-- /code_chunk_output -->
# 拼图

## 初始化(Initialization)

以下拼图位于 init 标签，并且会在Verge3D应用程序初始化时被解析。

![puzzles-init](http://imgs.zjbcool.com/puzzles/puzzles-init.jpg)

### "configure application"

允许设置应用程序初始化参数，包括一些WebGL上下文创建参数。

- "compressed assets" - 使应用程序加载.xz格式的压缩场景，而不是常规.gltf文件（有关详细信息，请参阅资产压缩）;
- "default fullscreen button" - 启用位于右上角的默认全屏按钮;
- "transparent background" - 使背景透明，以便通过WebGL画布显示网页的底层部分;
- "enable screenshots"- 可以从WebGL画布中正确捕获屏幕截图（将WebGL上下文的preserveDrawingBuffer属性设置为true）;
- "fade annotations"- 当场景物体阻挡注释时，使注释淡出。
  
![puzzles-init-configure-app](http://imgs.zjbcool.com/puzzles/puzzles-init-configure-app.jpg)

### "setup preloader" + "percentage"

删除默认预加载器并公开事件回调以允许处理预加载器进度事件。

![puzzles-init-setup-preloader](http://imgs.zjbcool.com/puzzles/puzzles-init-setup-preloader.jpg)

在以下示例中，自定义预加载器由具有id为"preloader"的容器HTML元素（例如，`<div>`）表示。在它的内部存在id为"loading progress"的另一个元素（例如，`<span>`），其中使用**persentage**拼图打印％的加载。除此之外，还会动态地改变第三个元素的宽度（例如，`<div>`）以可视化地表示加载进度。

![puzzles-init-setup-preloader-example](http://imgs.zjbcool.com/puzzles/puzzles-init-setup-preloader-example.jpg)

> 译注：本例的HTML代码如下：

```html
<div id="preloader">
      <span id="loading_progress"></span>
      <div id="loading_bar"></div>
</div>
```

> 通过**setup preloader**拼图反映出一个预加载通常分为三个部分：
> 在**on start do**插槽中设置加载器开始状态，这里设置**preloader**为可见;
> 在**on progress do**插槽中通常设置加载的进度和进度条动画;
> 在**on finish do**插槽中设置加载完成后执行的动作，这里设置隐藏**preloader**。</br>
> 最后，一旦**setup preloader**拼图被拖进拼图编辑器，默认的加载器就会自动失效。

## 事件（Events）

此类别的拼图处理用户生成的事件：鼠标点击/触摸，悬停和拖动。

> 译注：Verge3D中事件有两类，一是这里介绍的三维场景下的交互事件，还有一类是HTML页面上的交互事件。

此类别的拼图处理用户生成的事件：鼠标点击/触摸，悬停和拖动。

![puzzles-events](http://imgs.zjbcool.com/puzzles/puzzles-events.jpg)

### “when clicked”

等待用户点击指定的3D对象 – 然后在“do”插槽中运行拼图，或者如果用户点击其他内容则在“miss：do”插槽中运行拼图。

![puzzles-events-when-clicked](http://imgs.zjbcool.com/puzzles/puzzles-events-when-clicked.jpg)

### "picked object"

返回用户单击的对象。用于“when clicked”拼图。

![puzzles-events-picked-object](http://imgs.zjbcool.com/puzzles/puzzles-events-picked-object.jpg)

### "when hovered"

等待用户将鼠标悬停在指定的3D对象上（或者，如果使用了**all objects**拼图，则指定列表或组中的任何对象，或场景中的任何对象）– 然后在“over / out：do”插槽中运行拼图。

![puzzles-events-when-hovered.jpg](http://imgs.zjbcool.com/puzzles/puzzles-events-when-hovered.jpg)

### "hovered object"

返回用户悬停的对象。常与"when hovered"拼图一起使用。

![puzzles-events-hovered-object.jpg](http://imgs.zjbcool.com/puzzles/puzzles-events-hovered-object.jpg)

### "when moved"

跟踪指定对象（或指定列表或组中的任何对象，或场景中的任何对象，如果使用**all objects**拼图）的任何移动（位置，旋转和缩放）。如果对象开始移动，则在“start：do”插槽中运行拼图，在"while moving: do"插槽中继续运行拼图，直到对象停止移动，然后在"stop: do"插槽中运行拼图。 “delta”参数表示触发此拼图所需的位置，旋转或比例的任何坐标（x，y或z）的绝对变化。 “period”参数表示在再次检查运动之前拼图等待的渲染帧数。

![puzzles-events-when-moved.jpg](http://imgs.zjbcool.com/puzzles/puzzles-events-when-moved.jpg)

### "when dragged"

等待用户使用鼠标或触摸手势拖动指定的3D对象（或指定列表或组中的任何对象，或者如果使用了**all objects**拼图，则为场景里的任何对象） - 然后生成移动数据用于"drag move", "drag rotate" 或 "drag rotate"拼图。还捕获已启动和已停止拖动的事件。

![puzzles-events-when-dragged-over.jpg](http://imgs.zjbcool.com/puzzles/puzzles-events-when-dragged-over.jpg)

### "drag move"

根据**when dragged**拼图生成的拖动移动数据，移动指定的3D对象（或指定列表或组中的所有对象，如果使用了**all objects**拼图，则移动场景中的所有对象）。使用下拉菜单限制移动到特定轴或平面，或完全不限制。

![puzzles-events-drag-move.jpg](http://imgs.zjbcool.com/puzzles/puzzles-events-drag-move.jpg)

###　"drag rotate"

根据**when dragged**拼图生成的拖动移动数据，旋转指定的3D对象（或指定列表或组中的所有对象，或者如果使用**all objects**拼图，则为场景中的所有对象）。使用下拉菜单将旋转限制为特定轴。 ＂space＂下拉列表允许在“本地”和“父”坐标空间之间切换。

![puzzles-events-drag-rotate.jpg](http://imgs.zjbcool.com/puzzles/puzzles-events-drag-rotate.jpg)

### "drag scale"

根据**when dragged**拼图生成的拖动移动数据，缩放指定的3D对象（或指定列表或组中的所有对象，或者如果使用**all objects**拼图，则为场景中的所有对象）。使用下拉菜单限制缩放到特定轴。

![puzzles-events-drag-scale.jpg](http://imgs.zjbcool.com/puzzles/puzzles-events-drag-scale.jpg)

## 选择器（Selectors）

此类别的拼图用于选择3D对象，组，动画片段和材质以适应其他拼图。

![puzzles-selectors.jpg](http://imgs.zjbcool.com/puzzles/puzzles-selectors.jpg)

### "select object"

下拉列表包含场景中所有对象的按字母顺序排列的列表。

![puzzles-selector-object.jpg](http://imgs.zjbcool.com/puzzles/puzzles-selector-object.jpg)

### "all objects"

表示场景中的所有对象，以便可以批量应用操作。不可迭代。

![puzzles-selector-all-objects.jpg](http://imgs.zjbcool.com/puzzles/puzzles-selector-all-objects.jpg)

### "select group"

下拉列表包含场景中显示的所有对象组的按字母顺序排列的列表。操作可以批量应用于一组对象。不可迭代。

![puzzles-selector-group.jpg](http://imgs.zjbcool.com/puzzles/puzzles-selector-group.jpg)

### "select animation"

下拉列表包含场景中显示的所有动画片段的按字母顺序排列的列表。根据在3ds Max或Blender中应用动画的对象命名动画剪辑。

![puzzles-selector-animation.jpg](http://imgs.zjbcool.com/puzzles/puzzles-selector-animation.jpg)

### "select material"

下拉列表包含场景中存在的所有材质的按字母顺序排列的列表。

![puzzles-selector-material.jpg](http://imgs.zjbcool.com/puzzles/puzzles-selector-material.jpg)

> 译注：选择器也可以通过**text**拼图输入对象名称来实现，使用输入名字的方式可以在对象暂时没有加载到场景之前选择对象，而选择器只能在场景中有了该对象时才能使用。
> 其中组选择器不能直接使用文本来选择，但是有一个隐藏技巧，就是在组名之前加上"GROUP"，引擎就会把组名识别为组选择器了。
> 另外，材质选择器暂时也不能用文本代替。

## 物体/对象（Objects）

此类别的拼图用于对象的各种操作。

![puzzles-objects.jpg](http://imgs.zjbcool.com/puzzles/puzzles-objects.jpg)

### “show”

使最初或以前隐藏的指定对象可见。也适用于对象列表，组（或组列表）或**all objects**拼图。

![puzzles-object-show.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-show.jpg)

### "hide"

使指定对象不可见。也适用于对象列表，组（或组列表）或**all objects**拼图。

![puzzles-object-show.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-show.jpg)

### "is visible"

检查对象（或列表中的任何对象）当前是否可见。如果是，则返回true，否则返回false。

![puzzles-object-is-visible.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-is-visible.jpg)

### "get objects from"

这个通用拼图允许您：

- 获取组中包含的对象列表，从而使其可迭代
- 将**all objects**拼图的输出转换为列表，从而使其可迭代
- 得到一些父对象的子对象

![puzzles-object-get-objects.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-get-objects.jpg)

通过使用下拉列表，您还可以过滤掉某种对象（例如相机，灯，注释等）。

> 译注：此处注释（annotation）指的是annotation拼图所产生的注释，并非指空物体或虚拟体。

### "clone"

制作对象副本，为新对象生成唯一名称并立即将其添加到场景中。输出新对象。不适用于列表，组或**all objects**拼图。

> 译注：克隆出的新对象名称是唯一且不可修改的。

![puzzles-object-clone.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-clone.jpg)

### "snap to object"

通过复制变换数据将对象移动到另一个对象的位置。还复制旋转和缩放。不适用于列表，组或所有对象拼图。

![puzzles-object-snap.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-snap.jpg)

### "set transform"

根据指定的变换数据移动，旋转或缩放对象。 "offset"复选框允许相对于对象原始的位置/旋转/缩放值，来移动/旋转/缩放对象。任何轴输入都可以留空。也适用于对象列表，组（或组列表）或所有对象拼图。

![puzzles-object-set-transform.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-set-transform.jpg)

### "get transform"

检索对象的位置，旋转或缩放数据。不适用于列表，组或所有对象拼图。

![puzzles-object-get-transform.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-get-transform.jpg)

### "change local transform"

根据对象本地空间中的变换数据移动，旋转或缩放。任何轴输入都可以留空。也适用于对象列表，组（或组列表）或**all objects**拼图。

![puzzles-object-change-local-transform.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-change-local-transform.jpg)

### "get object direction"

返回对象的方向向量或数字X，Y，Z向量组件。

> 译注：例如返回值为1，0，0，说明对象朝向x轴正方向。

![puzzles-object-get-object-direction.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-get-object-direction.jpg)

### "add annotation"

为一个对象添加一个2D标签，用户可点击展开查看对象描述。也适用于对象列表，组（或组列表）或**all objects**拼图。

![puzzles-object-add-annotation.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-add-annotation.jpg)

### "remove annotation"

移除之前在对象上添加的注释。也适用于对象列表，组（或组列表）或**all objects**拼图。

![puzzles-object-remove-annotation.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-remove-annotation.jpg)

### "open annotation"

通过指定的标签展开注释。

![puzzles-object-open-annotation.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-open-annotation.jpg)

### "close annotation"

通过指定的标签关闭注释。

![puzzles-object-close-annotation.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-close-annotation.jpg)

### "update text object"

根据指定的文本内容为文本对象生成新网格。
也适用于对象列表，组（或组列表）或**all objects**拼图。

![puzzles-object-update-text.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-update-text.jpg)

### "parent"

在对象之间创建父关系，以便第一个对象跟随第二个对象的位置/旋转/缩放。不适用于列表，组或**all objects**拼图。

![puzzles-object-parent.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-parent.jpg)

### "distance"

输出两个指定对象的距离。不适用于列表，组或**all objects**拼图。

![puzzles-object-distance.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-distance.jpg)

### "outline"

对指定对象应用或删除轮廓效果。要解锁此拼图，请在3ds Max或Blender中启用轮廓效果。也适用于对象列表，组（或组列表）或**all objects**拼图。

![puzzles-object-outline.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-outline.jpg)

### "get custom props"

返回指定到对象上的字典类型的自定义属性。

![puzzles-object-get-custom-props.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-get-custom-props.jpg)
![puzzles-object-get-custom-props-example2.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-get-custom-props-example2.jpg)

在3ds Max中可以通过“User Defined Properties”面板指定自定义属性，在Blender中可以使用“Custom Properties”面板指定。

![puzzles-object-get-custom-props-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-get-custom-props-example.jpg)

> 译注：自定义属性可以将数据绑定到物体上，以实现一些与数据有关的操作。
> 使用字典类拼图可以操作这些数据。

## 材质（Materials）

此类别的拼图使用材质和纹理进行各种操作。

![puzzles-materials.jpg](http://imgs.zjbcool.com/puzzles/puzzles-materials.jpg)

### "assign material"

将材质指定给对象，完全替换旧材质。也适用于对象列表，组（或组列表）或**all objectgs**拼图。

![puzzles-materials-assign-material.jpg](http://imgs.zjbcool.com/puzzles/puzzles-materials-assign-material.jpg)

### "replace texture"

使用从指定的URI加载的纹理替换为指定材质中找到的纹理。

> 译注：URI应为URL，即路径。
> 这里指定材质的纹理是自动查找的，不能使用文本、变量，但是有个技巧就是可以通过visual_logic.js生成的对应代码手动改为文本、变量。

![puzzles-materials-replace-texture.jpg](http://imgs.zjbcool.com/puzzles/puzzles-materials-replace-texture.jpg)

### "set color"

设置指定材质的颜色参数的R，G和B分量。

![puzzles-materials-set-color.jpg](http://imgs.zjbcool.com/puzzles/puzzles-materials-set-color.jpg)

**对于3ds Max用户**：您可以在材质（标准或物理）中添加**Controllers**，其名称将显示在下拉菜单中。此外，还可以访问标准或物理材质节点的漫反射（或“基础”）颜色输入。对于**glTF-compliant PBR**材质， **Base Color**和**Emission**输入只能通过此拼图访问。

**对于Blender用户**：您可以在基于节点的材质（GLSL Internal，Cycles，Eevee）中添加**RGB**节点，它们的名称将显示在下拉菜单中。此外，还可以访问材质，扩展材质，BSDF Principled，BSDF Diffuse和BSDF Glossy节点的漫反射（或“基础”）颜色输入。对于**glTF-compliant PBR materials**材质，**BaseColor**和**Emissive**输入只能通过此拼图访问。

此拼图还可用于修改环境材质。在下拉列表中，环境材质的名称以“Verge3D_Environment”开头。 有关Verge3D用户可用材质的更多信息，请参阅本手册的材质系统概述章节：3ds Max，Blender。

### "set value"

设置指定材质的值参数。

![puzzles-materials-set-value.jpg](http://imgs.zjbcool.com/puzzles/puzzles-materials-set-value.jpg)

**对于3ds Max用户**：您可以在材质（标准或物理）中添加**Controllers**，其名称将显示在下拉菜单中。此外，还可以访问标准或物理材质节点的漫反射（或“基础”）颜色输入。对于**glTF-compliant PBR**材质，此拼图可以访问以下输入：金属度，粗糙度，凹凸比例，发射强度和环境贴图强度。

**对于Blender用户**：您可以在基于节点的材质（GLSL Internal，Cycles，Eevee）中添加**RGB**节点，它们的名称将显示在下拉菜单中。此拼图可以访问以下输入：metalness（PBR节点中的MetallicFactor），粗糙度（PBR节点中的RoughnessFactor），bumpScale（PBR节点中的NormalScale），emissiveIntesity（PBR节点中的EmissiveFactor）和envMapIntensity（未在PBR节点中呈现）。

此拼图还可用于修改环境材质。在下拉列表中，环境材质的名称以“Verge3D_Environment”开头。

有关Verge3D用户可用材质的更多信息，请参阅本手册的材质系统概述章节：3ds Max，Blender。

### "get material"

检索分配给对象的材质的名称。如果为同一对象分配了多个材质，则返回第一个。

![puzzles-object-get-material.jpg](http://imgs.zjbcool.com/puzzles/puzzles-object-get-material.jpg)

## 动画（Animation）

此类别的拼图使用动画片段执行操作。

![puzzles-animation.jpg](http://imgs.zjbcool.com/puzzles/puzzles-animation.jpg)

### "play animation"

播放动画片段。动画剪辑名称对应于在3ds Max或Blender中为其指定动画的对象（每个对象只能分配一个动画片段）。使用**animation selector**拼图为此拼图提供动画剪辑。

使用“from”和“to”字段指定帧范围。使用"speed"字段指定播放速度。 "reversed"复选框启用反向播放。下拉菜单可用于更改动画模式 - “auto”允许使用3ds Max或Blender中指定的动画模式，而其他模式则覆盖3ds Max或Blender中指定的设置。

动画结束后处理"when finished: do" 插槽中的拼图（这仅对"once" 动画模式有效）。

![puzzles-animation-play.jpg](http://imgs.zjbcool.com/puzzles/puzzles-animation-play.jpg)

此拼图也适用于动画片段列表。

### "stop animation"

停止播放动画片段。也适用于动画片段列表。

![puzzles-animation-stop.jpg](http://imgs.zjbcool.com/puzzles/puzzles-animation-stop.jpg)

### "pause animation"

暂停动画片段播放，从而稍后可以从暂停的帧恢复。也适用于动画片段列表。

![puzzles-animation-pause.jpg](http://imgs.zjbcool.com/puzzles/puzzles-animation-pause.jpg)

### "resume animation"

恢复以前暂停的动画片段。也适用于动画片段列表。

![puzzles-animation-resume.jpg](http://imgs.zjbcool.com/puzzles/puzzles-animation-resume.jpg)

### "set animation frame"

将动画片段设置为指定的帧。也适用于动画片段列表。

![puzzles-animation-set-animation-frame.jpg](http://imgs.zjbcool.com/puzzles/puzzles-animation-set-animation-frame.jpg)

### "is animation playing"

检查当前是否正在播放动画片段（或列表中的任何动画）。

![puzzles-animation-is-playing.jpg](http://imgs.zjbcool.com/puzzles/puzzles-animation-is-playing.jpg)

### "get animation"

检索指定对象的动画片段。也适用于对象列表，组（或组列表）或**all objects**拼图。返回值始终是动画片段列表（即使只有一个动画片段）。

![puzzles-animation-get-animation.jpg](http://imgs.zjbcool.com/puzzles/puzzles-animation-get-animation.jpg)

### "animate param"

在以**duration**（以秒为单位）指定的时间段内，在**from**和**to**之间作为数字参数（或列表或字典中的所有参数）设置动画。**easing**下拉菜单允许您指定动画模式（见下文）。

**repeat**字段指定第一个动画完成后的重复次数。 **yoyo**复选框启用往复运动（适用于**repeat**> 1）。

在参数设置动画时，每个渲染帧都会触发**on update do**插槽中的拼图。动画完成后，触发**when finished do**插槽的拼图。

![puzzles-animation-animate-param.jpg](http://imgs.zjbcool.com/puzzles/puzzles-animation-animate-param.jpg)

这个拼图包装了[Tween.js](https://github.com/tweenjs/tween.js/blob/master/docs/user_guide.md)库，包括它的所有31种缓动模式（如图），持续时间，重复和yoyo设置，以及更新和完整的回调。

![puzzles-animation-animate-param-easing-modes.jpg](http://imgs.zjbcool.com/puzzles/puzzles-animation-animate-param-easing-modes.jpg)

可以使用通常放置在**on update do**插槽内的**updated value**拼图来检索中间值。

### "updated value"

返回**animate param**拼图产生的中间值。根据动画参数的类型，可以是数字，列表或字典。

![puzzles-animation-updated-value.jpg](http://imgs.zjbcool.com/puzzles/puzzles-animation-updated-value.jpg)

该拼图可以放在场景中的任何位置，但是它通常用于**animate param**拼图的**on update do**插槽，在这里它会逐帧更新。

## 相机（Camera）

该分类下的拼图使用相机来执行操作。

![puzzles-camera.jpg](http://imgs.zjbcool.com/puzzles/puzzles-camera.jpg)

### "look at"

以指定对象为目标点平滑地移动激活相机。数字参数指定动画执行（秒）的时间周期。

![puzzles-camera-lookat.jpg](http://imgs.zjbcool.com/puzzles/puzzles-camera-lookat.jpg)

### "tween camera"

平滑地为激活相机设置动画，使其位置最终与指定对象的位置一致，并且相机以另一个指定对象为目标。数字参数指定动画的时间段（以秒为单位）。

可选的**when finished do**的插槽可用于检测补间完成的时刻。

![puzzles-camera-tween-camera.jpg](http://imgs.zjbcool.com/puzzles/puzzles-camera-tween-camera.jpg)

### "set active camera"

使指定的相机处于活动状态。这可用于更改摄像机控制模式（“轨道”与“飞行”与“无控制”），视野和其他设置。

![puzzles-camera-set-active.jpg](http://imgs.zjbcool.com/puzzles/puzzles-camera-set-active.jpg)

### "get camera direction"

返回活动摄像机世界方向向量的X，Y和Z分量列表。如果选中**from mouse/touch**，则此拼图会从相机发射一条光线到光标的屏幕位置，并返回该光线的方向。如果选中了附加**inverted**，则取消光标的屏幕位置坐标。

![puzzles-camera-get-camera-direction.jpg](http://imgs.zjbcool.com/puzzles/puzzles-camera-get-camera-direction.jpg)

以下示例的是效果是使一个物体始终看向鼠标光标。这可以通过使用一些简单的数学运算，将空/虚拟对象的位置映射到鼠标光标来实现。

![puzzles-camera-get-camera-direction-example2.jpg](http://imgs.zjbcool.com/puzzles/puzzles-camera-get-camera-direction-example2.jpg)

要在3ds Max中设置此类行为，请使用**Rotation Controllers / LookAt Constraint**使对象跟随虚拟对象。在Blender中，这对应于**TrackTo**约束。

> 译注：**get camera direction**的返回值是一个列表，分别对应x,y,z的值;
> 我们可以通过该值设置相机注视方向上一个空物体的位置，从而实现让一个物体始终注视相机方向的效果。
> 通过该拼图可以实现第一人称相机、点击地面传送等相机控制方式。

### "autorotate camera"

通过围绕目标旋转激活的轨道摄像机，可以平滑地动画。

![puzzles-camera-autorotate-camera.jpg](http://imgs.zjbcool.com/puzzles/puzzles-camera-autorotate-camera.jpg)

在以下示例中，相机在用户不活动3秒后开始旋转。当用户单击鼠标按钮（或触摸屏幕）时，用户重新控制相机，直到又停止3秒钟。

![puzzles-camera-autorotate-camera-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-camera-autorotate-camera-example.jpg)

> 译注：即相机环绕动画。仅支持轨道相机。
> 上面的例子中使用了计时器拼图，这里也是计时器拼图的一个典型应用。可以在**Time**类别下找到该拼图。

## 场景（Scenes）

该分类下的拼图对场景执行加载/卸载操作。

![puzzles-scenes.jpg](http://imgs.zjbcool.com/puzzles/puzzles-scenes.jpg)

### "load scene" + "percentage"

触发此拼图时，将卸载当前场景并从指定的.gltf文件加载新场景。加载完成后，触发 **when loaded do**插槽中的拼图。还可以启用**on progress do**插槽。放置在此插槽中的拼图在加载期间不断触发，并可利用**percentage**拼图。

![puzzles-scenes-load-scene.jpg](http://imgs.zjbcool.com/puzzles/puzzles-scenes-load-scene.jpg)

![puzzles-scenes-load-scene-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-scenes-load-scene-example.jpg)

### "append scene" + "percentage"

触发此拼图时，将从指定的.gltf文件加载新场景并将其**附加**到当前场景。加载完成后，触发**when loaded do**插槽中的拼图。还可以启用**on progress do**插槽。放置在此插槽中的拼图在加载期间不断触发，并可利用**percentage**拼图。默认情况下，**append scene**拼图不会从新场景加载相机和灯光。可以在拼图选项中更改此行为。

![puzzles-scenes-append-scene.jpg](http://imgs.zjbcool.com/puzzles/puzzles-scenes-append-scene.jpg)

![puzzles-scenes-append-scene-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-scenes-append-scene-example.jpg)

> 译注：我们在拼图编辑器中使用**append scene**拼图时，如果场景还没有加载完成，在选择器中是无法找到该场景下的对象的。这时可以先点击拼图编辑器的运行按钮，再到选择器中查看，就会找到了。

### "unload scene"

从应用程序卸载指定的场景或其部分。使用空文本值以卸载所有场景。

![puzzles-scenes-unload-scene.jpg](http://imgs.zjbcool.com/puzzles/puzzles-scenes-unload-scene.jpg)

### "enable rendering"

恢复以前禁用的渲染。

![puzzles-scenes-enable-rendering.jpg](http://imgs.zjbcool.com/puzzles/puzzles-scenes-enable-rendering.jpg)

### "disable rendering"

禁用渲染。图形不会更新，但会捕获用户事件并且动画时间轴将会进行。

![puzzles-scenes-disable-rendering.jpg](http://imgs.zjbcool.com/puzzles/puzzles-scenes-disable-rendering.jpg)

您可以使用禁用渲染来节省移动设备或笔记本电脑上的电量，并消除桌面设备上较冷的噪点。您还可以勾选**anti-alias last frame**，以显着提高渲染质量并补偿性能损失。

![puzzles-scenes-enable-disable-rendering-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-scenes-enable-disable-rendering-example.jpg)

> 译注：当相机开始运动时启用渲染，当相机停止时禁用渲染。

## 杂项（Miscellaneous）

杂项拼图在高层次上执行各种操作。

> 译注：该类拼图需要Javascript中级以上水平。

![puzzles-misc.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc.jpg)

### "open web page"

触发此拼图时，将根据下拉选项在新的或相同的浏览器选项卡中打开指定的URL。当从拼图编辑器触发时，它会在离开选项卡之前要求用户确认。

![puzzles-misc-open-web-page.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-open-web-page.jpg)

### "social share link"

生成用于在流行社交媒体中共享应用程序的链接。

![puzzles-misc-social-share-link.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-social-share-link.jpg)

### "call JS function"

执行应用程序的JavaScript代码中定义的函数。可选择传递要用作函数参数的参数。

> 译注：对于拼图无法实现的功能，我们可以在app.js中定义一个函数，然后使用**call JS function**拼图调用该函数，并且该拼图还支持传参。

![puzzles-misc-call-js-function.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-call-js-function.jpg)

要向JavaScript代码添加函数，请使用任意文本编辑器打开app.js文件（例如，位于verge3d / applications / my_awesome_app中的my_awesome_app.js）。搜索“prepareExternalInterface”并在该声明中添加您的函数（在大括号之间），使它看起来像这样：

``` javascript
function prepareExternalInterface(app) {
    app.ExternalInterface.myJSFunction = function(numericArg, textArg) {
        alert('Got some params from Puzzles: ' + numericArg + ' and ' + textArg);
    }
}
```

### "when called from JS"

允许从应用程序的JavaScript代码触发拼图。可以选择是否检索从JavaScript代码传递的参数，并将它们保存为变量以供“do”插槽中的拼图使用。

![puzzles-misc-when-called-from-js.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-when-called-from-js.jpg)

要从JavaScript代码触发此拼图，请使用任意文本编辑器打开app.js文件（例如，位于verge3d / applications / my_awesome_app中的my_awesome_app.js）。搜索“runCode”并在该声明中添加一个函数调用（在大括号之间），使它看起来像这样：

``` javascript
function runCode(app) {
    app.ExternalInterface.myJSCallback('Hello, Puzzles!', 80);
}
```

> 译注：**call JS function** 和 **When called from JS** 是扩展拼图功能很重要的拼图。

### "load data"

尝试从指定位置加载数据。无论尝试是否成功，都会解析"once ready do"插槽中的拼图。可以通过**loaded data**拼图访问检索到的数据。

![puzzles-misc-load-data.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-load-data.jpg)

### "send data"

尝试使用异步POST HTTP请求，将指定数据发送到远程位置。无论尝试是否成功，都会解析"once ready do"插槽中的拼图。如果有任何响应数据，可以通过**loaded data**拼图访问它。

![puzzles-misc-send-data.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-send-data.jpg)

### "loaded data"

返回由**load data**或**send data**拼图获取的数据。

![puzzles-misc-loaded-data.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-loaded-data.jpg)

### "read JSON"

将文本解析为[JavaScript Object Notation](https://en.wikipedia.org/wiki/JSON#Example)（JSON）数据，该数据在一个字典中返回。

![puzzles-misc-read-JSON.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-read-JSON.jpg)

###　"read CSV"

将文本解释为[comma-separated values](https://en.wikipedia.org/wiki/Comma-separated_values#Example)（CSV）。返回表的行列表，每行表示为值列表。可以使用下拉列表选择分隔符以对应CSV文件的导出设置。 “From row”值表示从顶部开始将跳过多少行。

![puzzles-misc-read-CSV.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-read-CSV.jpg)

表的行和值由它们的数字索引从0开始访问。

![puzzles-misc-csv-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-csv-example.jpg)

### "save state"

保存指定对象的状态和/或由其名称指定的变量值。对象被克隆并存储在内存中。检索变量的值并将其存储在每个指定变量名的内存中。

![puzzles-misc-save-state.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-save-state.jpg)

如果多次调用此拼图，则按顺序保存状态，以便可以使用**undo state**拼图返回任何先前的状态。

### "undo state"

恢复使用**save state**拼图保存的对象和/或变量的状态。

![puzzles-misc-undo-state.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-undo-state.jpg)

如果多次调用此拼图，状态按顺序保存，以便可以使用**undo state**拼图返回任何先前的状态。

### "all variable names"

返回一个列表，其中包含拼图中使用的所有变量的名称。

![puzzles-misc-all-variable-names.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-all-variable-names.jpg)

### "variable value by name"

返回由其名称指定的变量的值。此拼图与标准变量值拼图的工作方式类似，但不需要从预定义的下拉菜单中选择变量。

![puzzles-misc-variable-value-by-name.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-variable-value-by-name.jpg)

### "place order"

使用“标题”，“内容”和“总价格”字段以及可选的屏幕截图组成隐藏的订单表单，并根据Wordpress插件部分中说明的规范将此表单提交到指定的URL。默认情况下，订单表单将提交给[演示订购页面](https://sandbox.soft8soft.com/order-form/)。

Verge3D附带一个免费的Wordpress插件，可以处理这个拼图提交的请求。收到此类请求后，此Wordpress插件会呈现一个页面，其中包含扩展表单，并附带联系和注释字段，嵌入式屏幕截图和验证码。最终填写的表单由客户提交，并在Wordpress管理界面中创建新订单。客户和销售经理都通过电子邮件通知订单。

有关设置信息，请参阅本手册的Wordpress插件部分。

请务必在[configure application](https://www.soft8soft.com/docs/manual/en/puzzles/Initialization.html#configure_application)拼图中启用屏幕截图，否则屏幕截图可能会以黑色呈现。

![puzzles-misc-place-order.jpg](http://imgs.zjbcool.com/puzzles/puzzles-misc-place-order.jpg)

## 时间（Time）

此类拼图用于计算时间和触发基于时间的事件。

![puzzles-time.jpg](http://imgs.zjbcool.com/puzzles/puzzles-time.jpg)

### "after"

等待指定的时间，然后触发放置在“do”插槽内的拼图。

![puzzles-time-after.jpg](http://imgs.zjbcool.com/puzzles/puzzles-time-after.jpg)

### "every"

等待指定的时间，然后触发放置在“do”插槽内的拼图。然后重复。

![puzzles-time-every.jpg](http://imgs.zjbcool.com/puzzles/puzzles-time-every.jpg)

### "every frame"

触发每个渲染帧放置在“do”插槽内的拼图（通常以每秒60帧的速率）。

![puzzles-time-every-frame.jpg](http://imgs.zjbcool.com/puzzles/puzzles-time-every-frame.jpg)

### "elapsed delta"

输出从前一个渲染帧传递的时间量（以秒为单位）。可以与"every frame"拼图一起使用来实现非关键帧动画。

![puzzles-time-elapsed-delta.jpg](http://imgs.zjbcool.com/puzzles/puzzles-time-elapsed-delta.jpg)

### "elapsed total"

输出从应用程序启动传递的时间量（以秒为单位）。可以与"every frame"拼图一起使用来实现程序动画和各种视觉效果。

![puzzles-time-elapsed-total.jpg](http://imgs.zjbcool.com/puzzles/puzzles-time-elapsed-total.jpg)

### "set timer"

等待指定的时间，然后触发放置在“do”插槽内的拼图。指定的ID可用于覆盖或删除计时器。

### "remove timer"

通过ID移除之前设置的计时器。

> 译注：**set timer**和**remove timer**通常一起使用，打个比方来说，它们可以实现设置定时炸弹和拆除定时炸弹的效果。

### "system date / time"

获取系统日期和时间。启用UTC复选框以获取UTC / GMT区域的时间而不是本地时区。

![puzzles-time-get-date-time.jpg](http://imgs.zjbcool.com/puzzles/puzzles-time-get-date-time.jpg)

## HTML

这类拼图用于操纵HTML DOM元素。

> HTML拼图可以用于动态创建UI，修改属性和样式，在网络上传输数据等。

![puzzles-html.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html.jpg)

### "add HTML element"

创建具有指定类型和标识符（对应于“id”属性）的HTML元素，并将其附加到文档正文。并将其样式属性“position”设置为“absolute”。

![puzzles-html-add-html-elem.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-add-html-elem.jpg)

### "get attribute"

从具有指定标识的HTML元素中获取属性。如果HTML元素位于外部HTML文档（使用iframe嵌入Verge3D应用程序的.html文件）中，则应启用"in parent doc"复选框。

![puzzles-html-get-attr.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-get-attr.jpg)

### "set attribute"

为具有指定标识的HTML元素设置[属性](https://www.w3schools.com/tags/ref_attributes.asp)。如果HTML元素位于外部HTML文档（使用iframe嵌入Verge3D应用程序的.html文件）中，则应启用"in parent doc"复选框。也适用于元素ID列表。

![puzzles-html-set-attr.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-set-attr.jpg)

### "set style"

为具有指定标识的HTML元素设置[CSS属性](https://www.w3schools.com/cssref/default.asp)。如果HTML元素位于外部HTML文档（使用iframe嵌入Verge3D应用程序的.html文件）中，则应启用"in parent doc"复选框。也适用于元素ID列表。

![puzzles-html-set-style.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-set-style.jpg)

### "set css rule"

为指定的CSS规则（在应用程序的.css文件中找到）设置[CSS属性](https://www.w3schools.com/cssref/default.asp)。如果样式表属于外部HTML文档（使用iframe嵌入Verge3D应用程序的.html文件），则应启用"in parent doc"复选框。

![puzzles-html-set-css-rule.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-set-css-rule.jpg)

### "event"

为具有指定标识的HTML元素注册事件侦听器。如果HTML元素位于外部HTML文档（使用iframe嵌入Verge3D应用程序的.html文件）中，则应启用"in parent doc"复选框。一旦发生事件，触发放置在“do”插槽中的拼图。也适用于元素ID列表。

![puzzles-html-on-event.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-on-event.jpg)

### "get event property"

输出"event"拼图生成的事件的[属性](https://www.w3schools.com/jsref/obj_events.asp)值。

![puzzles-html-get-event-property.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-get-event-property.jpg)

### "window"

表示[DOM窗口对象](https://www.w3schools.com/jsref/obj_window.asp) - 浏览器选项卡或加载HTML文档的iframe。

![puzzles-html-window.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-window.jpg)

### "document"

表示[DOM文档对象](https://www.w3schools.com/jsref/dom_obj_document.asp) - HTML文档的根节点。

![puzzles-html-document.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-document.jpg)

### "body"

表示[DOM正文对象](https://www.w3schools.com/jsref/dom_obj_body.asp) - HTML文档的`<body>`元素。

![puzzles-html-body.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-body.jpg)

### "draw line"

通过动态更新的线条将指定的3D对象与指定的HTML元素连接起来。如果HTML元素位于外部HTML文档（使用iframe嵌入Verge3D应用程序的.html文件）中，则应启用“in parent doc”复选框。您还可以设置线条的宽度，颜色和偏移量。

![puzzles-html-draw-line.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-draw-line.jpg)

### "remove line"

从指定对象中删除以前创建的线条。

![puzzles-html-remove-line.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-remove-line.jpg)

### "bind element"

使指定的HTML元素跟随屏幕空间中指定3D对象的中心。如果HTML元素位于外部HTML文档（使用iframe嵌入Verge3D应用程序的.html文件）中，则应启用“in parent doc”复选框。一个更加可自定义的“add annotation”拼图的变体。

![puzzles-html-bind-element.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-bind-element.jpg)

> 译注：**bind element**拼图与**add annotation**拼图一致，需要注意的是它的html元素的中心默认位于左上角。

### "init fullscreen"

使指定的HTML元素变为全屏模式按钮 - 首次单击它会触发进入全屏模式，第二次单击会退出全屏模式。进入或退出全屏模式时会触发放置在“on enter do”和“on do do”插槽中的拼图。如果浏览器不支持全屏（例如iOS Safari），则会触发放置在“if unavailable do”中的拼图。

> 译注：在IOS Safari下全屏按钮无法显示。

![puzzles-html-init-fullscreen.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-init-fullscreen.jpg)

### "get URL data"

检索加载应用程序的窗口的URL数据：

- Entire URL - 文本, 如 `"https://www.example.com/my_awesome_app.html?image_url=https://www.uploadserver.com/path/to/image.jpg"`
- Parameters - 字典, 如 `{"image_url": "https://www.uploadserver.com/path/to/image.jpg"}`
- Host name - 文本, 如 `"www.example.com"`

![puzzles-html-get-url-data.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-get-url-data.jpg)

在应用程序的配置数据存储在其URL中的情况下，此拼图可能很有用。例如，您可以通过以下方式指定要用作纹理的图像：

`https://www.example.com/my_awesome_app.html?image_url=https://www.uploadserver.com/path/to/image.jpg`

![puzzles-html-get-url-data-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-get-url-data-example.jpg)

有关操作演示，请参阅Verge3D发行版中的**Custom Image**演示。

### "set URL param"

如有必要，通过自动形成有效的查询字符串来指定或更新指定URL中的参数值，并返回更新的URL。

![puzzles-html-set-url-param.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-set-url-param.jpg)

在应用程序的配置数据存储在其URL中的情况下，此拼图可能很有用。例如，您可以通过以下方式指定要用作纹理的图像：

`https://www.example.com/my_awesome_app.html?image_url=https://www.uploadserver.com/path/to/image.jpg`

您可以上传纹理，检索其服务器端路径并将其保存到应用程序URL的image_url参数，如下所示：

![puzzles-html-set-url-param-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-set-url-param-example.jpg)

有关操作演示，请参阅Verge3D发行版中的**Custom Image**演示。

### "take screenshot"

在当前视图截图，并输出为[Data URI](https://en.wikipedia.org/wiki/Data_URI_scheme#Examples_of_use)格式的数据。

![puzzles-html-take-screenshot.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-take-screenshot.jpg)

### "open file" and "opened file"

调用浏览器的本地对话窗口，以从用户设备中选择文件。用户选择文件后，以Data URI格式返回文件内容。

![puzzles-html-open-file.jpg](http://imgs.zjbcool.com/puzzles/puzzles-html-open-file.jpg)

## AR/VR拼图

这些拼图用于实现基于网络的增强现实（AR）和虚拟现实（VR）体验，这些体验运行在开发中的浏览器技术之上，称为[WebXR](https://github.com/immersive-web/webxr/blob/master/explainer.md)（Web上的eXtended Reality）。

![puzzles-ar-vr.jpg](http://imgs.zjbcool.com/puzzles/puzzles-ar-vr.jpg)

这些拼图需要您的浏览器支持WebXR标准。为了在其他浏览器中使用VR拼图，我们建议在创建新项目时打开Legacy VR复选框。

### "check VR mode"

检查虚拟现实系统。如果成功，将触发**if available do**中的拼图。否则，如果浏览器不支持VR或VR硬件未找到，则触发**if unavailable do**插槽中的拼图。

![puzzles-ar-vr-check-vr-mode.jpg](http://imgs.zjbcool.com/puzzles/puzzles-ar-vr-check-vr-mode.jpg)

有关支持的VR设备和最佳实践的详细信息，请参阅本“用户手册”的相应主题。

### "enter VR mode"

进入虚拟现实模式。在进入或离开VR模式时触发放置在**on enter do** 和**on exit do**插槽中的拼图。如果无法建立VR会话，则触发放置在**if unavailable do**拼图中。

VR相机模式：

- **sitting or standing** - 固定相机位于地面以上的某个高度。

- **room** - 使相机位于特定的边界内，例如房间（例如HTC Vive）。

- **looking around** - 固定相机位于零坐标处。

- **walking** - 相机无边界移动。
- **viewer locked** - 固定相机位于零坐标处。所有内容取决于该视图。

数字参数**hover to click after**表示一个对象的时间段（以秒为单位），VR光标悬停在该对象上以接收鼠标单击事件。默认无限意味着没有悬停转换。

![puzzles-ar-vr-enter-vr-mode.jpg](http://imgs.zjbcool.com/puzzles/puzzles-ar-vr-enter-vr-mode.jpg)

为了在VR模式下正确控制摄像机，请务必在进入VR模式时将其捕捉并将其父级设置为位于所选3D编辑器中的空/虚拟或真实对象。这是必需的，因为VR会话可以完全控制您的摄像机，并且您只能移动摄像机所在的空/虚拟对象。

![puzzles-ar-vr-enter-vr-mode-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-ar-vr-enter-vr-mode-example.jpg)

### "end VR session"

结束虚拟现实会话。

![puzzles-ar-vr-end-vr-session.jpg](http://imgs.zjbcool.com/puzzles/puzzles-ar-vr-end-vr-session.jpg)

### "check AR mode"

检查增强现实系统。如果成功，将触发**if available do**中的拼图。否则，如果浏览器不支持未找到AR或AR硬件，则触发**if unavailable do**插槽中的拼图。

![puzzles-ar-vr-check-ar-mode.jpg](http://imgs.zjbcool.com/puzzles/puzzles-ar-vr-check-ar-mode.jpg)

### "enter AR mode"

进入增强现实模式。在进入或退出AR模式时触发放置在**on enter do**和**on exit do**插槽中的拼图。如果无法建立AR会话，则会触发**if unavailable do**中的拼图。

![puzzles-ar-vr-enter-ar-mode.jpg](http://imgs.zjbcool.com/puzzles/puzzles-ar-vr-enter-ar-mode.jpg)

### "detect horizontal surface AR"

通过向前投射光线来检测AR模式下的水平表面。交叉时，此光线将触发**on intersection do**的拼图。如果没有发生交叉或设备处于预热状态，则触发**if missed do**插槽的拼图。

![puzzles-ar-vr-ar-hit-test.jpg](http://imgs.zjbcool.com/puzzles/puzzles-ar-vr-ar-hit-test.jpg)

### "AR hit point"

表面命中点坐标。

![puzzles-ar-vr-ar-hit-point.jpg](http://imgs.zjbcool.com/puzzles/puzzles-ar-vr-ar-hit-point.jpg)

### "on session event"

捕获由VR设备（例如，头显或控制器按钮）生成的VR会话事件并触发在do插槽中指定的拼图。

![puzzles-ar-vr-on-session-event.jpg](http://imgs.zjbcool.com/puzzles/puzzles-ar-vr-on-session-event.jpg)

我们建议为不支持WebXR的设备提供备用。例如，以下代码段适用于插入了Android / iOS智能手机的Google Cardboard v2。

![puzzles-ar-vr-on-session-event-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-ar-vr-on-session-event-example.jpg)

## 声音（Sound）

这些拼图用于加载和播放HTML5的声音文件。

![puzzles-sound.jpg](http://imgs.zjbcool.com/puzzles/puzzles-sound.jpg)

### "load sound"

创建HTML5音频元素并使用指定的URL加载声音文件。此拼图还会将创建的音频元素添加到内存缓存中，以便具有相同URL的此拼图的任何后续使用不会再次加载相同的声音文件。建议使用.mp3格式，因为大多数Web浏览器都支持。

![puzzles-sound-load-sound.jpg](http://imgs.zjbcool.com/puzzles/puzzles-sound-load-sound.jpg)

### "play sound"

开始声音播放。loop复选框会重复声音播放。

![puzzles-sound-play-sound.jpg](http://imgs.zjbcool.com/puzzles/puzzles-sound-play-sound.jpg)

### "pause sound"

暂停声音播放。

![puzzles-sound-pause-sound.jpg](http://imgs.zjbcool.com/puzzles/puzzles-sound-pause-sound.jpg)

### "rewind sound"

设置音频元素从头开始播放声音。

![puzzles-sound-rewind-sound.jpg](http://imgs.zjbcool.com/puzzles/puzzles-sound-rewind-sound.jpg)

### "set volume"

设置声音音量。输入音量限制为0.0-1.0之间。

![puzzles-sound-set-volume.jpg](http://imgs.zjbcool.com/puzzles/puzzles-sound-set-volume.jpg)

### "is playing"

检测一个声音当前是否处于播放状态。

![puzzles-sound-is-playing.jpg](http://imgs.zjbcool.com/puzzles/puzzles-sound-is-playing.jpg)

## 物理和约束（Physics&Constraints）

这些拼图用于模拟物理行为和限制物体的运动。

![puzzles-physics.jpg](http://imgs.zjbcool.com/puzzles/puzzles-physics.jpg)

### physics模块入门

在使用这些拼图以前，必须在应用程序中添加物理模块。可以在程序管理器的程序创建面板使用对应的选项。

![puzzles-physics-option-app-manager.jpg](http://imgs.zjbcool.com/puzzles/puzzles-physics-option-app-manager.jpg)

或者，可以将模块ammo.js从verge3d / build文件夹复制到应用程序文件夹，然后再将应用程序更新为较新版本。然后，更新操作将自动将物理模块添加到应用程序。

![puzzles-physics-ammo-update.jpg](http://imgs.zjbcool.com/puzzles/puzzles-physics-ammo-update.jpg)

### Physics拼图

#### "create physics world"

使用指定的**gravity**和**frame-per-second**参数初始化物理引擎。默认**gravity**值9.8对应于地球表面条件。零值意味着在空间中没有引力。较高的**fps**值会以牺牲性能为代价来提高仿真质量。

![puzzles-physics-create-physics-world.jpg](http://imgs.zjbcool.com/puzzles/puzzles-physics-create-physics-world.jpg)

在内部，这个拼图允许碰撞检测和图形自动同步。

#### "create physics body"

从**Dynamic**，**Kinematic**或**Static**类型的指定对象创建物理体。指定碰撞形状：**Box**，**Sphere**或**Mesh**。设置主体的质量（仅对动态实体有效）。也适用于对象列表，组（或组列表）或**all objects**拼图。

![puzzles-physics-create-physics-body.jpg](http://imgs.zjbcool.com/puzzles/puzzles-physics-create-physics-body.jpg)

物理体的类型：

- **Dynamic** - 由物理引擎驱动，受碰撞影响并与其他物体碰撞。
- **Kinematic** - 通过动画或用户动作驱动。 Dynamic对象将被推开，但不会受Dynamic对象影响。
- **Static** - 不能移动但能与其他实体发生碰撞。

建议：

- 确保在3ds Max或Blender中应用缩放，因为物理引擎会忽略对象的比例。
- 为获得最佳效果，物体的原点应与质心重合。
- 使用**snap body**拼图移动**Dynamic**或**Static**物体，而不是尝试直接移动物体。

#### "remove physics body"

通过销毁与之关联的物理体来从指定对象中移除物理。也适用于对象列表，组（或组列表）或**all objects**拼图。

![puzzles-physics-remove-physics-body.jpg](http://imgs.zjbcool.com/puzzles/puzzles-physics-remove-physics-body.jpg)

#### "physics body params"

设置与指定对象关联的物理体的参数。也适用于对象列表，组（或组列表）或**all objects**拼图。

![puzzles-physics-body-params.jpg](http://imgs.zjbcool.com/puzzles/puzzles-physics-body-params.jpg)

参数：

- **friction** - 固体相对运动的阻力系数。
- **linear damping** - 阻碍移动物体的阻力系数。
- **angular damping** - 使旋转物体减速的阻力系数。
- **restitution** - 弹性系数（如果0-主体像粘土那样没有弹性，如果1-主体的行为就像是由橡胶制成的）。

#### "apply vector"

将力、重力、线速度、角速度、冲量、扭矩、扭矩冲量或位置以世界空间中的指定方向应用于与指定对象关联的物理体。也适用于对象列表、组（或组列表）或**all objects**的拼图。

![puzzles-physics-apply-vector.jpg](http://imgs.zjbcool.com/puzzles/puzzles-physics-apply-vector.jpg)

参数：

- **Force** - 按指定的方向推物体。
- **Gravity** - 为指定的实体单独指定重力。
- **Linear Velocity** - 设置移动速度。
- **Angular Velocity** - 设置旋转速度。
- **Impulse** - 类似于Linear Velocity，但也考虑到了物体质量（较重的物体获得的速度较低）。
- **Torque** - 沿指定方向旋转物体。
- **Torque Impulse** - 类似于角速度，但也考虑到了物体质量（较重的物体获得的旋转速度较低）。
- **Position** - 设置物理体位置（类似于**snap body**拼图）。

#### "snap body"

通过复制变换（transform）数据，将与指定对象关联的物理体以及对象本身移动到另一个对象的位置。同时复制旋转。不适用于列表、组或**all objects**拼图。

与**apply vector / Position**拼图类似。

![puzzles-physics-snap-body.jpg](http://imgs.zjbcool.com/puzzles/puzzles-physics-snap-body.jpg)

#### "detect collision" and "collision_info"

检测指定实体与另一个实体（或列表或组中的任何实体）之间的碰撞。如果发生碰撞，将触发"upon collide" 槽中的拼图，否则将触发"upon no collide"插槽中的拼图。

![puzzles-physics-detect-collision.jpg](http://imgs.zjbcool.com/puzzles/puzzles-physics-detect-collision.jpg)

**collision info**拼图输出带有如下字段的字典类型数据：

- **objectA** - 第一个碰撞体的名称。
- **objectB** - 第二个碰撞体的名称。
- **distance** - 两个碰撞点的距离。
- **positionOnA** - 第一个物体的碰撞点的XYZ坐标。
- **positionOnB** - 第二个物体的碰撞点的XYZ坐标。
- **normalOnB** - 第二个物体碰撞点处法线向量的XYZ分量。

### Constraints Puzzles

如果对象不是另一个对象的父级，则约束将在世界空间中起作用。否则它们将在父对象的空间中工作 - 您可以在3ds Max或Blender中选择父对象以显示坐标轴。

#### "limit transform"

设置约束以限制沿选定轴的对象移动，旋转或缩放。指定的id应该是唯一的，否则将替换具有相同名称的现有约束。 “min”和“max”插槽指定允许移动的范围。

![puzzles-constraints-limit-transform.jpg](http://imgs.zjbcool.com/puzzles/puzzles-constraints-limit-transform.jpg)

#### "copy transform"

设置约束以从另一个对象复制对象的位置，旋转或缩放。指定的id应该是唯一的，否则将替换具有相同名称的现有约束。

![puzzles-constraints-copy-transform.jpg](http://imgs.zjbcool.com/puzzles/puzzles-constraints-copy-transform.jpg)

#### "operations"

使用分配给指定对象的指定id删除，静音或取消静音约束。分配给此对象的其他约束（如果有）将保持不变。

![puzzles-constraints-operations.jpg](http://imgs.zjbcool.com/puzzles/puzzles-constraints-operations.jpg)

## 后期效果（Post-processing）

以下拼图用于设置各种后期效果。

![puzzles-postprocessing.jpg](http://imgs.zjbcool.com/puzzles/puzzles-postprocessing.jpg)

### "bloom"

开户bloom物效。

![puzzles-postprocessing-bloom.jpg](http://imgs.zjbcool.com/puzzles/puzzles-postprocessing-bloom.jpg)

### "brightness/contrast"

允许调整渲染的亮度和对比度。

![puzzles-postprocessing-brightness-contrast.jpg](http://imgs.zjbcool.com/puzzles/puzzles-postprocessing-brightness-contrast.jpg)

### "depth of field"

开启景深（DOF）特效。

![puzzles-postprocessing-dof.jpg](http://imgs.zjbcool.com/puzzles/puzzles-postprocessing-dof.jpg)

### "grayscale"

开启灰度特效。

![puzzles-postprocessing-grayscale.jpg](http://imgs.zjbcool.com/puzzles/puzzles-postprocessing-grayscale.jpg)

### "ambient occlusion"

开启屏幕空间AO(SSAO)特效。

![puzzles-postprocessing-ssao.jpg](http://imgs.zjbcool.com/puzzles/puzzles-postprocessing-ssao.jpg)

### "simple refraction"

为指定材质开启简单折射特效。

![puzzles-postprocessing-simple-refraction.jpg](http://imgs.zjbcool.com/puzzles/puzzles-postprocessing-simple-refraction.jpg)

参数：

- **material** - 要被指定特效的材质。
- **distance** - 折射光线的最大长度。
- **render after refraction** - 要从正常渲染队列中排除并在应用折射效果后渲染的对象。

### "screen space reflection/refraction"

启用屏幕空间反射/折射（SSR）效果。

![puzzles-postprocessing-ssr.jpg](http://imgs.zjbcool.com/puzzles/puzzles-postprocessing-ssr.jpg)

参数：

- for material - 要被指定特效的材质;
- steps - 沿反射/折射光线的深度搜索步骤数（值越高=质量越好，性能越差）；
- stride - 搜索步骤的步幅（以像素为单位）（值越高=质量越低，性能越好，1像素=最大质量）；
- binary search steps - 二进制搜索迭代次数（值越高=质量越好，性能越差）；
- thickness - 消除SSR伪影的系数（仅折射）；
- max distance - 反射/折射光线的最大长度;
- resolution - 深度缓冲区的分辨率乘数（从0到1；1=最佳质量）；
- jitter - 改善深度搜索的噪点系数（从0到1）；
- render after - 应用SSR后要呈现的对象。

![puzzles-postprocessing-ssr-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-postprocessing-ssr-example.jpg)

### "remove post-processing effects"

移除所有后期特效。

![puzzles-postprocessing-remove-postprocessing.jpg](http://imgs.zjbcool.com/puzzles/puzzles-postprocessing-remove-postprocessing.jpg)

## 字典（Dictionaries）

这个类别的拼图允许您有效地存储、传输和检索各种数据。特别是，字典对于存储多个参数以及在javascript和网络之间批量传递这些参数非常有用。

![puzzles-dictionaries.jpg](http://imgs.zjbcool.com/puzzles/puzzles-dictionaries.jpg)

### "create empty dict"

返回一个不包含任何数据记录的字典。

![puzzles-dictionaries-create-empty-dict.jpg](http://imgs.zjbcool.com/puzzles/puzzles-dictionaries-create-empty-dict.jpg)

在JavaScript中，字典表示为一个不包括任何属性的对象（Object）,即{}。

### "dict set key"

将项设置为与字典中的指定键关联。键必须是文本，而分配的值可以是任何类型（文本、数字、列表、其他字典等）。

![puzzles-dictionaries-dict-set-key.jpg](http://imgs.zjbcool.com/puzzles/puzzles-dictionaries-dict-set-key.jpg)

### "dict get key"

返回与字典中指定键关联的项。

![puzzles-dictionaries-dict-get-key.jpg](http://imgs.zjbcool.com/puzzles/puzzles-dictionaries-dict-get-key.jpg)

### "get keys"

返回字典中存在的所有键的列表。

![puzzles-dictionaries-get-keys.jpg](http://imgs.zjbcool.com/puzzles/puzzles-dictionaries-get-keys.jpg)

### "dict check key"

检查字典中是否存在指定的键，并返回布尔类型的结果true或false。

![puzzles-dictionaries-dict-check-key.jpg](http://imgs.zjbcool.com/puzzles/puzzles-dictionaries-dict-check-key.jpg)

### "dict remove key"

删除与字典中指定键关联的项目，并删除该键本身。

![puzzles-dictionaries-dict-remove-key.jpg](http://imgs.zjbcool.com/puzzles/puzzles-dictionaries-dict-remove-key.jpg)

### "is empty"

检查指定的字典是否不包含键，并返回布尔类型的结果true或false。

![puzzles-dictionaries-is-empty.jpg](http://imgs.zjbcool.com/puzzles/puzzles-dictionaries-is-empty.jpg)

## 变量（Variables）

变量是可以更改（变化）的命名值。变量可以通过几种不同的方式创建：

- 可以通过单击Create variable ...按钮创建变量，然后为其选择任何名称。这个名称，无论它出现在程序中的哪个位置，都可以随时通过变量的下拉菜单进行更改。
- 函数可以定义输入，这些输入会创建只能在函数中使用的变量。这些传统上称为参数（parameters或arguments）。
- 每个“count with”和“for each”拼图都使用变量并定义其值。这些值只能在拼图中使用。

> 译注：注意拼图中变量的作用域问题，
> 1.由Create variable创建的变量属于全局作用域（在main范围内）;
> 2.在函数内的和循环体内的变量为局部作用域。
> 也就是说，除了后两种情况下，其余都是全局作用域 ，这就需要注意了，当变量多的时候注意重复命名问题。

![puzzles-variables-create.jpg](http://imgs.zjbcool.com/puzzles/puzzles-variables-create.jpg)

变量的典型用例是当您有一个可以处于多个状态的对象时。例如，门可以打开或关闭，如果您想要正确地设置它，您需要知道它现在的状态。

![puzzles-variables-example.jpg](http://imgs.zjbcool.com/puzzles/puzzles-variables-example.jpg)

### "set"

指定一个变量的值。

![puzzles-variables-set.jpg](http://imgs.zjbcool.com/puzzles/puzzles-variables-set.jpg)

### "get"

在变量中保存一个值，不改变它。

![puzzles-variables-get.jpg](http://imgs.zjbcool.com/puzzles/puzzles-variables-get.jpg)

### "change"

将存储在变量中的值增加指定的数字。如果初始值不是数字，或者未设置，则认为它是零，并且递增。

![puzzles-variables-change.jpg](http://imgs.zjbcool.com/puzzles/puzzles-variables-change.jpg)

## 函数（Procedures）

函数是一组执行特定任务的命名拼图。通过在程序中组织拼图，可以使场景更加紧凑和可维护。

> 译注：将想要复用的拼图作为函数保存到拼图库，可以方便重复利用。

通过从工具箱中拖出函数定义拼图，可以创建一个新函数：

![puzzles-procedures-create.jpg](http://imgs.zjbcool.com/puzzles/puzzles-procedures-create.jpg)

新创建的函数拼图可以重命名并填充其他拼图来执行某些任务：

![puzzles-procedures-create2.jpg](http://imgs.zjbcool.com/puzzles/puzzles-procedures-create2.jpg)

要能够触发（调用）过程，请从工具箱中拖出相应的拼图：

![puzzles-procedures-trigger.jpg](http://imgs.zjbcool.com/puzzles/puzzles-procedures-trigger.jpg)

一个函数可以从一个拼图场景的几个地方触发多次。这允许重复使用拼图，而不是直接复制类似的一组拼图几次。例如，只要用户单击对象本身或HTML按钮，就可以启动动画（从而确保重复控制）。

![puzzles-procedures-trigger2.jpg](http://imgs.zjbcool.com/puzzles/puzzles-procedures-trigger2.jpg)

一个函数可以有额外的输入参数。要将输入添加到您的函数中，请单击齿轮图标并从左侧的工具箱中拖出一个参数拼图，将其粘贴到输入拼图中：

![puzzles-procedures-inputs.jpg](http://imgs.zjbcool.com/puzzles/puzzles-procedures-inputs.jpg)

为了能够在函数中使用输入参数，请拖出在"Variables"中自动创建的相应“get”变量拼图：

![puzzles-procedures-inputs2.jpg](http://imgs.zjbcool.com/puzzles/puzzles-procedures-inputs2.jpg)

此变量可用作实际数据（如对象名称）的替代，以执行某些任务。

![puzzles-procedures-inputs3.jpg](http://imgs.zjbcool.com/puzzles/puzzles-procedures-inputs3.jpg)

使用输入触发函数时，为每个触发器拼图提供数据。

![puzzles-procedures-trigger-with-inputs.jpg](http://imgs.zjbcool.com/puzzles/puzzles-procedures-trigger-with-inputs.jpg)

您可以使用右键单击菜单从触发器拼图跳转到相应的函数定义拼图：

![puzzles-procedures-jump.jpg](http://imgs.zjbcool.com/puzzles/puzzles-procedures-jump.jpg)

### "procedure with return"

函数可以将计算值输出到其调用者（也称为返回值）。要创建这样的函数，请从工具箱中拖出带有返回槽的函数拼图的实例。

![puzzles-procedures-return.jpg](http://imgs.zjbcool.com/puzzles/puzzles-procedures-return.jpg)

### "if return"

在解析所有内部拼图之前，函数可以在某些条件下返回值。在这种情况下，程序会提早停止运行。

![puzzles-procedures-if-return.jpg](http://imgs.zjbcool.com/puzzles/puzzles-procedures-if-return.jpg)

### 系统（System）

这些拼图可用于执行各种系统功能，例如打印到控制台，功能使用，获取/设置渲染像素比率以及检索GPU信息。

![puzzles-system.jpg](http://imgs.zjbcool.com/puzzles/puzzles-system.jpg)

### "print to console"

将任意类型的数据打印到浏览器控制台。后者通常可以使用F12键打开（Chrome，Firefox，Windows，Linux）。在Mac上，使用Chrome中的View> Developer> JavaScript Console菜单（Option-Cmd-J），或Safari中的Develop> Show Error Console菜单（Option-Cmd-C）。

![puzzles-system-print-console.jpg](http://imgs.zjbcool.com/puzzles/puzzles-system-print-console.jpg)

### "print performance info"

在1秒内记录性能配置文件并将其打印到浏览器控制台。后者通常可以使用F12键打开（Chrome，Firefox，Windows，Linux）。在Mac上，使用Chrome中的View> Developer> JavaScript Console菜单（Option-Cmd-J），或Safari中的Develop> Show Error Console菜单（Option-Cmd-C）。

![puzzles-system-print-performance-info.jpg](http://imgs.zjbcool.com/puzzles/puzzles-system-print-performance-info.jpg)

> 译注：通过该拼图可以查看程序中各种资源的加载速度，从而根据这些信息优化程序。

### "feature available"

检查用户浏览器中是否提供从下拉列表中选择的功能。

![puzzles-system-feature-available.jpg](http://imgs.zjbcool.com/puzzles/puzzles-system-feature-available.jpg)

### "get GPU"

输出用户的GPU信息 - 供应商，例如：NVIDIA Corporation，Apple Inc.，Qualcomm和GPU型号，例如：GeForce GTX 1060 3GB / PCIe / SSE2，Apple A9 GPU，Adreno（TM）330。

![puzzles-system-get-GPU.jpg](http://imgs.zjbcool.com/puzzles/puzzles-system-get-GPU.jpg)

### "set screen scale"

设置渲染画布的屏幕分辨率系数。将此值设置为高于1可提高质量，1 - 非视网膜显示屏（non-retina）上的默认屏幕比例。

![puzzles-system-set-screen-scale.jpg](http://imgs.zjbcool.com/puzzles/puzzles-system-set-screen-scale.jpg)

### "native screen scale"

原生画布分辨率系数。对于非视网膜显示器，等于1。

![puzzles-system-native-screen-scale.jpg](http://imgs.zjbcool.com/puzzles/puzzles-system-native-screen-scale.jpg)

## 拼图库（Puzzles Library）

库是一组持久存储，用于在多个项目中重复使用的拼图组。

![puzzles-library.jpg](http://imgs.zjbcool.com/puzzles/puzzles-library.jpg)

可以通过右键单击拼图组并选择**Save N Puzzles to Library**选项，将拼图组添加到库中。在模态窗口中键入组的名称，因为它将出现在库的条目列表中（通过重新加载Puzzle编辑器来刷新它）。

![puzzles-library-save.jpg](http://imgs.zjbcool.com/puzzles/puzzles-library-save.jpg)

通过将其拖出到工作区，可以从库中检索已保存的拼图组。

![puzzles-library-retrieve.jpg](http://imgs.zjbcool.com/puzzles/puzzles-library-retrieve.jpg)

可以通过单击**Delete this item**按钮从库中删除一个条目（通过重新加载拼图编辑器刷新条目列表）。
