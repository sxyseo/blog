---
title: OpenLayers 3 View
date: 2018-01-18 20:29:59
tags: openLayers
categories: openLayers
---
# View

所见即所得是大家喜闻乐见的，`View`就是这样的，作为地图的窗口，做一些改变，就能看到变化，便于初学者学习。同时，`View`是OpenLayers 3地图组成部分中非常重要的一个概念，涉及到地图移动，放大，缩小，旋转等，这些功能是任何一个GIS引擎必不可少的。为此，我们把它作为最先介绍和学习的组成部分。

<!-- more -->

# 地图导航

在深入学习之前，还是先来个开胃菜，见识一下`View`所承载的功能。下面将演示地图导航功能，关于导航相关功能，在前面操作地图的时候，已经有了充分的理解和认识。为什么我们还要再讨论它呢？因为这次的导航功能，需要自己来实现，而且只使用`ol.View`提供的方法就可以做到。

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%, height: 400px"></div>
<div id="navigate-container">
	<input type="button" onClick="moveToLeft();" value="左移" />
	<input type="button" onClick="moveToRight();" value="右移" />
	<input type="button" onClick="moveToUp();" value="上移" />
	<input type="button" onClick="moveToDown();" value="下移" />
	<input type="button" onClick="moveToChengDu();" value="移到成都" />
	<input type="button" onClick="zoomIn();" value="放大" />
	<input type="button" onClick="zoomOut();" value="缩小" />
</div>
<script>
  var map = new ol.Map({
		layers: [
			new ol.layer.Tile({source: new ol.source.OSM()})
		],
		view: new ol.View({
			// 设置成都为地图中心，此处进行坐标转换， 把EPSG:4326的坐标，转换为EPSG:3857坐标，因为ol默认使用的是EPSG:3857坐标
			// 请阅读“地图坐标系”了解更多坐标系的信息。
			center: ol.proj.transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857'),
			zoom: 10
		}),
		target: 'map'
  });
	
	// 向左移动地图
	function moveToLeft() {
		var view = map.getView();
		var mapCenter = view.getCenter();
		// 让地图中心的x值增加，即可使得地图向左移动，增加的值根据效果可自由设定
		mapCenter[0] += 50000;
		view.setCenter(mapCenter);
		map.render();
	}
	
	// 向右移动地图
	function moveToRight() {
		var view = map.getView();
		var mapCenter = view.getCenter();
		// 让地图中心的x值减少，即可使得地图向右移动，减少的值根据效果可自由设定
		mapCenter[0] -= 50000;
		view.setCenter(mapCenter);
		map.render();
	}
	
	// 向上移动地图
	function moveToUp() {
		var view = map.getView();
		var mapCenter = view.getCenter();
		// 让地图中心的y值减少，即可使得地图向上移动，减少的值根据效果可自由设定
		mapCenter[1] -= 50000;
		view.setCenter(mapCenter);
		map.render();
	}
	
	// 向下移动地图
	function moveToDown() {
		var view = map.getView();
		var mapCenter = view.getCenter();
		// 让地图中心的y值增加，即可使得地图向下移动，增加的值根据效果可自由设定
		mapCenter[1] += 50000;
		view.setCenter(mapCenter);
		map.render();
	}
	
	// 移动到成都
	function moveToChengDu() {
		var view = map.getView();
		// 设置地图中心为成都的坐标，即可让地图移动到成都
		view.setCenter(ol.proj.transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857'));
		map.render();
	}
	
	// 放大地图
	function zoomIn() {
		var view = map.getView();
		// 让地图的zoom增加1，从而实现地图放大
		view.setZoom(view.getZoom() + 1);
	}
	
	// 缩小地图
	function zoomOut() {
		var view = map.getView();
		// 让地图的zoom减小1，从而实现地图缩小
		view.setZoom(view.getZoom() - 1);
	}
	
</script>

点击地图下方的几个按钮试试响应的功能，用它们就可以完整的实现地图导航了。按照惯例，下面我们会给出对应的源码，但我希望你能先学习一下`ol.View`的API文档，从中找出我们可能使用了的方法。


好了，下面就来揭晓答案：

```html
<body>
	<div id="map" style="width: 100%, height: 400px"></div>
	<div id="navigate-container">
		<input type="button" onClick="moveToLeft();" value="左移" />
		<input type="button" onClick="moveToRight();" value="右移" />
		<input type="button" onClick="moveToUp();" value="上移" />
		<input type="button" onClick="moveToDown();" value="下移" />
		<input type="button" onClick="moveToChengDu();" value="移到成都" />
		<input type="button" onClick="zoomIn();" value="放大" />
		<input type="button" onClick="zoomOut();" value="缩小" />
	</div>
	<script>
	  var map = new ol.Map({
			layers: [
				new ol.layer.Tile({source: new ol.source.OSM()})
			],
			view: new ol.View({
				// 设置成都为地图中心，此处进行坐标转换， 把EPSG:4326的坐标，转换为EPSG:3857坐标，因为ol默认使用的是EPSG:3857坐标
				center: ol.proj.transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857'),
				zoom: 10
			}),
			target: 'map'
	  });
		
		// 向左移动地图
		function moveToLeft() {
			var view = map.getView();
			var mapCenter = view.getCenter();
			// 让地图中心的x值增加，即可使得地图向左移动，增加的值根据效果可自由设定
			mapCenter[0] += 50000;
			view.setCenter(mapCenter);
			map.render();
		}
		
		// 向右移动地图
		function moveToRight() {
			var view = map.getView();
			var mapCenter = view.getCenter();
			// 让地图中心的x值减少，即可使得地图向右移动，减少的值根据效果可自由设定
			mapCenter[0] -= 50000;
			view.setCenter(mapCenter);
			map.render();
		}
		
		// 向上移动地图
		function moveToUp() {
			var view = map.getView();
			var mapCenter = view.getCenter();
			// 让地图中心的y值减少，即可使得地图向上移动，减少的值根据效果可自由设定
			mapCenter[1] -= 50000;
			view.setCenter(mapCenter);
			map.render();
		}
		
		// 向下移动地图
		function moveToDown() {
			var view = map.getView();
			var mapCenter = view.getCenter();
			// 让地图中心的y值增加，即可使得地图向下移动，增加的值根据效果可自由设定
			mapCenter[1] += 50000;
			view.setCenter(mapCenter);
			map.render();
		}
		
		// 移动到成都
		function moveToChengDu() {
			var view = map.getView();
			// 设置地图中心为成都的坐标，即可让地图移动到成都
			view.setCenter(ol.proj.transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857'));
			map.render();
		}
		
		// 放大地图
		function zoomIn() {
			var view = map.getView();
			// 让地图的zoom增加1，从而实现地图放大
			view.setZoom(view.getZoom() + 1);
		}
		
		// 缩小地图
		function zoomOut() {
			var view = map.getView();
			// 让地图的zoom减小1，从而实现地图缩小
			view.setZoom(view.getZoom() - 1);
		}
		
	</script>
</body>
```
使用`input`标签增加了几个按钮，每个按钮都绑定了对应的点击事件监听，对应的监听函数实现了某一种导航的功能，都很简单，并且代码都有注释，容易理解。创建地图的代码几乎没有任何改变，也不过多说明，请结合代码和上面的地图进行理解。

在功能上，目前尚有两个方面的欠缺，一是按钮没有集成在地图上；二是移动并不平滑。这两部分功能缺失将在我们学习了`Control`和动画之后，一步一步再补充完善，此处暂时不深入下去。现在需要考虑的是我们在代码中使用了`ol.proj.transform`这个方法，它有什么作用？

# 坐标

是否已经知道`ol.proj.transform`的功能了？你应该知道的，因为有API文档。它主要用于坐标转换，它的第一个参数是`ol.Coordinate`类型的坐标，后面两个参数依次是当前坐标所用的坐标系，及转换后的坐标所用的坐标系，`ol.proj.transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857')`就能把`EPSG:4326`的坐标`[104.06, 30.67]`转换为`EPSG:3857`的坐标。

在了解这个函数的功能后，可能不禁会问：为什么要弄的如此复杂，是不是还有很多其他的坐标系？是的，确实存在很多其他的坐标系，如果可以选择的话，应该没人愿意这样。道理如同这个世界上存在各种各样的货币一样。在下一个小节，就会详细介绍多个坐标系及存在的原因。但在此之前，我们得先了解一个有意义的坐标应该如何定义。 

如果单独地看`[45.06, 30.67]`，这是没有任何意义的两个数字，在不同的场景，不同的人眼里，它们可以是某个仪器的两个测量值，也可以是学生的考试成绩，还可以是某个坐标。大家或许已经意识到，场景是非常重要的，假如我们定义这两个数字表示的是一个地理位置的坐标，那请问你知道这个坐标所指的具体位置吗？肯定是不能的。因为我们并不知道这个坐标里面的数字使用什么单位，是度？是米？还是其他的单位？如果添加上单位，也还不够，假设单位是度，我们还是不知道坐标中的前一个数字是经度，还是纬度。即使定义经纬度，也还不够，因为还有很多不确定的东西，比如这个坐标相对的原点在哪里？二维还是三维，哪个方向为正向？

一个有意义的坐标并不是我们看上去那样简单，特别是地理坐标，但初学者往往会忽略这点，从而造成在应用中经常出现定位错误。

# 坐标系及投影

关于原点，方向，单位等等的相关定义和描述，组成了我们常说的坐标系。谈到坐标系，就会想起初中数学中经常接触到的二维笛卡尔坐标系，在图形学中也会遇到三维坐标系，在GIS中我们需要地理坐标系。但它并不像笛卡尔坐标系那样简单，学过地理知识就知道，地球并不是一个完全规则的球体。在不同的地区，为了在数学上表示它，就出现了多种不同的参考椭球体，比如克拉索夫斯基(Krasovsky)椭球体，WGS1984椭球体，更多的椭球体参见[参考椭球体](https://zh.wikipedia.org/wiki/%E5%8F%82%E8%80%83%E6%A4%AD%E7%90%83%E4%BD%93)。在参考椭球体的基础上，就发展出了不同的地理坐标系，比如我国常用的WGS84，北京54，西安80坐标系，欧洲，北美也有不同的坐标系。北京54使用的是克拉索夫斯基(Krasovsky)椭球体，WGS84使用的是WGS1984椭球体。由此可见，多个坐标系是源于地理的复杂性。

由于存在着多种坐标系，即使同样的坐标，在不同的坐标系中，也表示的是不同的位置，这就是大家经常遇到的偏移问题的根源，要解决这类问题，就需要纠偏，把一个坐标系的坐标转换成另一个坐标系的坐标。由于WGS84是全球通用的坐标系，涉及到多个坐标系与它之间的转换，所以在此做个简单的介绍。

WGS84，全称World Geodetic System 1984，是为GPS全球定位系统使用而建立的坐标系统。通过遍布世界的卫星观测站观测到的坐标建立，其初次WGS84的精度为1-2m，在1994年1月2日，通过10个观测站在GPS测量方法上改正，得到了WGS84（G730），G表示由GPS测量得到，730表示为GPS时间第730个周。

1996年，National Imagery and Mapping Agency (NIMA) 为美国国防部 (U.S.Department of Defense, DoD)做了一个新的坐标系统。这样实现了新的WGS版本WGS（G873）。其因为加入了美国海军天文台和北京站的改正，其东部方向加入了31-39cm 的改正。所有的其他坐标都有在1分米之内的修正。

关于北京54和西安80坐标系，请自行通过网络查找相关资料进行了解。

有了坐标系后，我们就能精确的表示地球上的每一个位置，但为什么还需要投影呢？投影是为了把不可展的椭球面描绘到平面上，它使用几何透视方法或数学分析的方法，将地球上的点和线投影到可展的曲面(平面、园柱面或圆锥面)上，再将此可展曲面展成平面，建立该平面上的点、线和地球椭球面上的点、线的对应关系。正是因为有投影，大家才能在网页上看到二维平面的地球地图。

投影方式也多种多样，其中有一种投影叫墨卡托投影(Mercator Projection)，广泛使用于网页地图，对于OpenLayers 3的开发者而言，尤其重要，详情参见[墨卡托投影](http://baike.baidu.com/view/301981.htm)。

如果不了解上面这些基本知识，在使用OpenLayers 3的过程中，会感觉寸步难行，相反，则得心应手。


# OpenLayers 3使用的坐标系

目前OpenLayers 3支持两种投影，一个是`EPSG:4326`，等同于WGS84坐标系，参见[详情](http://spatialreference.org/ref/epsg/wgs-84/)。另一个是`EPSG:3857`，等同于900913，由Mercator投影而来，经常用于web地图，参见[详情](http://spatialreference.org/ref/sr-org/7483/)。一个是全球通用的，一个是web地图专用的，自然OpenLayers 3支持它们。在使用过程中，需要注意OpenLayers 3默认使用的是`EPSG:3857`。这也是为什么前面的代码里需要进行坐标转换的原因。 既然支持`EPSG:4326`，为什么还要转换？当然是可以不用转换的，但前提是你得指定使用具体那种投影，就像下面这样。

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<script>
  new ol.Map({
		layers: [
			new ol.layer.Tile({source: new ol.source.OSM()})
		],
		view: new ol.View({
			// 设置成都为地图中心
			center: [104.06, 30.67],
			projection: 'EPSG:4326',
			zoom: 10
		}),
		target: 'map'
  });
</script>

代码很简单：

```html
<body>
	<div id="map" style="width: 100%"></div>
	<script>
	  new ol.Map({
			layers: [
				new ol.layer.Tile({source: new ol.source.OSM()})
			],
			view: new ol.View({
				// 设置成都为地图中心
				center: [104.06, 30.67],
				// 指定投影使用EPSG:4326
				projection: 'EPSG:4326',
				zoom: 10
			}),
			target: 'map'
	  });
	</script>
</body>
```

所以开发时需要记住的是当前OpenLayers 3使用的坐标系及投影，以及我们自己的数据所使用的坐标系及投影。在没有弄清楚之前，就不要继续后面的内容学习，直到弄清楚为止。


# ol.View的应用

[地图导航](04-01.md)是`View`应用的一个例子，但仅限于导航。其实`View`还有一些其他的功能，在开发中经常遇到。比如控制地图显示范围，自动适配某个范围等等。若有所了解，可跳过本章节，直接进入下一个章节。

# 限制地图范围

在实际使用中，往往只关心某一个区域的地图，而无需显示整个地球的地图，这样可以聚焦于业务，同时可以减少前端和后台的地图数据。无疑，这样的功能是非常有用的。下面我们就将看到，地图只能在经度29度到31度，纬度在102到104度之间显示。

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<script>
  new ol.Map({
		layers: [
			new ol.layer.Tile({source: new ol.source.OSM()})
		],
		view: new ol.View({
			extent: [102, 29, 104, 31],
			// 设置成都为地图中心
			center: [104.06, 30.67],
			projection: 'EPSG:4326',
			zoom: 10
		}),
		target: 'map'
  });
</script>

不妨上下左右拖拽地图试试，是否发现上下左右都存在着边界？其实代码很简单：
```html
<body>
	<div id="map" style="width: 100%"></div>
	<script>
	  new ol.Map({
			layers: [
				new ol.layer.Tile({source: new ol.source.OSM()})
			],
			view: new ol.View({
				// 设置地图中心范围
				extent: [102, 29, 104, 31],
				// 设置成都为地图中心
				center: [104.06, 30.67],
				projection: 'EPSG:4326',
				zoom: 10
			}),
			target: 'map'
	  });
	</script>
</body>
```
就只是添加了	`extent: [102, 29, 104, 31],`这行代码就实现了功能。`extent`参数类型为`[minX, minY, maxX, maxY]`的`ol.Extent`，很容易记住。

如果对上面的地图进行缩小，然后再看地图，是否发现范围`[102, 29, 104, 31]`外的区域也显示出来了，而这并不是我们期望看到的。这时请注意仔细看`extent`参数的说明，这个范围指的是地图中心的限制范围，而不是整个地图显示的范围。那遇到这个问题该怎么办？我们发现，当我们地图放大后，这个问题并不那么明显，地图放大的越大，固定窗口显示的实际地理范围越小。一个简单的办法就是限制地图不能无限缩小，具体允许缩小到哪一级，可通过实际缩小地图到刚好填满整个窗口(`id`为`map`的`div`)来确定。限制地图缩放级别可参见[下一节](04-07.md)。

这是一种简单的做法，虽然有效，但并不精确，如果要做到非常精确，还需要学习后面更多的知识(分辨率等)，在后续章节会有更深入的说明和示例。

# 限制地图缩放级别

有时无限制地允许用户缩小或者放大地图，并不是一种明智的做法，在地图上的`feature`、标注、图形等都会变的不便于查看。为此，适中的地图缩放级别是被大多数场景所需要的。下面演示地图你只能在10到14级进行缩放。

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<script>
  new ol.Map({
		layers: [
			new ol.layer.Tile({source: new ol.source.OSM()})
		],
		view: new ol.View({
			// 设置成都为地图中心
			center: [104.06, 30.67],
			projection: 'EPSG:4326',
			zoom: 10,
			minZoom: 10,
			maxZoom: 14
		}),
		target: 'map'
  });
</script>

缩放试试便知，地图现在不能无限缩小放大了，代码也是非常的简单：

```html
<body>
	<div id="map" style="width: 100%"></div>
	<script>
	  new ol.Map({
			layers: [
				new ol.layer.Tile({source: new ol.source.OSM()})
			],
			view: new ol.View({
				// 设置成都为地图中心
				center: [104.06, 30.67],
				projection: 'EPSG:4326',
				zoom: 10,
				// 限制地图缩放最大级别为14，最小级别为10
				minZoom: 10,
				maxZoom: 14
			}),
			target: 'map'
	  });
	</script>
</body>
```
很多初学者问这个问题，其实仔细阅读一下API文档就会知道如何编码，以至于很多已经知道这个功能的其他开发者不愿意回答这么基础的功能。如果只是显示最小级别则只用设置`minZoom`的值即可，反之只设置`maxZoom`的值。

除了使用`minZoom`和`maxZoom`之外，还可以使用`minResolution`和`maxResolution`，其具体原理和使用，在分辨率小节会有介绍。对于开发者而言，建议使用`minZoom`和`maxZoom`，简单直接。

# 自适配区域

在实际应用中，会有一些地图查找和标注的业务，比如查看成都市锦江区。而这时，地图所在区域可能是北京，我们需要能够让地图直接定位到锦江区，并且最大化完全地显示这块区域。下面就将演示这个功能。

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<input type="button" value="显示成都" onclick="fitToChengdu();" />
<script>
  var map = new ol.Map({
		layers: [
			new ol.layer.Tile({source: new ol.source.OSM()})
		],
		view: new ol.View({
			// 设置成都为地图中心
			center: [104.06, 30.67],
			projection: 'EPSG:4326',
			zoom: 10
		}),
		target: 'map'
  });

  function fitToChengdu() {
  	// 让地图最大化完全地显示区域[104, 30.6, 104.12, 30.74]
  	map.getView().fit([104, 30.6, 104.12, 30.74], map.getSize());
  }
</script>

点击地图下方的按钮**显示成都**，看看有什么效果。代码如下：

```html
<body>
	<div id="map" style="width: 100%"></div>
	<input type="button" value="显示成都" onclick="fitToChengdu();" />
	<script>
	  var map = new ol.Map({
			layers: [
				new ol.layer.Tile({source: new ol.source.OSM()})
			],
			view: new ol.View({
				// 设置成都为地图中心
				center: [104.06, 30.67],
				projection: 'EPSG:4326',
				zoom: 10
			}),
			target: 'map'
	  });

	  function fitToChengdu() {
	  	// 让地图最大化完全地显示区域[104, 30.6, 104.12, 30.74]
	  	map.getView().fit([104, 30.6, 104.12, 30.74], map.getSize());
	  }
	</script>
</body>
```

只用了一行代码，`ol.View`的`fit`函数很强大，希望初学者能认真仔细的查看API文档，它的第三个参数还可以设置更多的选项，以适应更多的需要。关于更多的使用，参见官网例子[Advanced View Positioning](http://openlayers.org/en/v3.13.1/examples/center.html)。

