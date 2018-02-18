---
title: OpenLayers 3 事件
date: 2018-01-18 20:29:59
tags: openLayers
categories: openLayers
---

# 事件

事件让很多业务的东西串联在一起，在前端中起着非常重要的作用，比如鼠标点击，移动事件。但其应用远不仅如此，现实生活中就存在很多大大小小的事件。 在OpenLayers 3中，同样存在非常多的事件，比如鼠标左键单击，双击等等。 同时还有一些用于各个模块之间进行协作使用的事件，比如`ol.Map`的`postrender`和`propertychange`事件。 通过这些事件，OpenLayers 3的功能模块协作一致，同样地，也可以让我们自己二次开发的功能模块运作起来。 同时，根据需要，我们还可以在系统中新增自定义事件，使得我们的开发使用方式同OpenLayers 3更加的一致。在本章节将详细介绍OpenLayers 3中的各种事件，及相关应用。

<!-- more -->

# 一个简单的事件应用 
在深入分析和理解之前，我们还是以简单的一个例子入手，来学习一下如何使用OpenLayers 3中的事件来做一个简单的事，从而熟悉整个流程。 用鼠标点击下面这个地图，将弹出一个对话框，显示点击位置的经纬度：

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var map = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  })
		],
		target: 'map',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	map.on('singleclick', function(event){
		alert(ol.proj.transform(map.getEventCoordinate(event), 'EPSG:3857', 'EPSG:4326'));
	})
</script>

代码如下：
```html
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var map = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  })
		],
		target: 'map',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	// 监听singleclick事件
	map.on('singleclick', function(event){
		// 通过getEventCoordinate方法获取地理位置，再转换为wgs84坐标，并弹出对话框显示
		alert(ol.proj.transform(map.getEventCoordinate(event), 'EPSG:3857', 'EPSG:4326'));
	})
</script>
```
代码很简单，但需要注意的是，任意的事件应用，必然会有三个步骤：

* 找准事件发送者，比如上面这个例子，`map`就是事件发送者。 如何找到它呢？ 一般都是要交互的对象。
* 找准事件名称，比如上面例子中的`singleclick`，切忌不要随便想象，或者按照惯例来写名称，初次使用，请一定参照API文档，不然后果自负。 如何参看一个类有什么事件？ 请参见[看懂API](../ch03/03-03.md)。
* 编写事件响应函数，在OpenLayers中，事件发送者都会有一个名字为`on`的函数，调用这个函数，就能监听指定的事件，响应函数`listener`具有一个参数`event`，这个`event`类就对应于API文档中事件名称后边括号里的类。

这三个步骤缺一不可，如果使用过`jquery`监听事件，应该对此比较熟悉。 除此之外，关于OpenLayers 3中的事件应用，还有下面两点需要说明：

* 几乎OpenLayers 3中所有的类，都能监听事件和触发事件，因为它们都继承于类`ol.Observable`，这个类甚至是`ol.Object`的父类。 如此可见，OpenLayers在整个引擎中，是多么的依赖于事件通信。
* 而OpenLayers 3的整个事件机制，又是基于Google的`Closure Library`。所以如果你对这个库熟悉，那么对OpenLayers 3提供的事件机制会比较熟悉。

如果仅限于直接使用OpenLayers 3中的事件，就没有必要深入源码去探个究竟，只要遵照约定对事件进行应用就好。

# 注销事件响应
上一节的示例仅仅演示了如何监听一个事件，并响应处理。 但如果之后又不需要再响应该事件了，要怎么办。 比如有一个引导用户使用的需求，用户第一次点击地图的时候，需要弹出一个使用说明，之后点击地图就不用再弹出这个使用说明了。 那我们有哪些方式可以做到这一点呢？ 尝试点击一下下面这个地图，之后再点击一次试试：

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var map = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  })
		],
		target: 'map',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	var key = map.on('singleclick', function(event){
		alert('大家好，我是淡叔，这是一个演示如何取消事件监听的应用，之后再点击地图时，你将不会再看到这个说明。');
		map.unByKey(key);
	})

</script>

第二次点击地图后，是否就不会再弹出提示信息了？ 其实代码很简单：

```html
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var map = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  })
		],
		target: 'map',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	var key = map.on('singleclick', function(event){
		alert('大家好，我是淡叔，这是一个演示如何取消事件监听的应用，之后再点击地图时，你将不会再看到这个说明。');
		// 下面这行代码就是取消事件监听
		map.unByKey(key);
	})
</script>
```

除了上面这个方式可以注销事件监听之外，还可以用下面这样的方式：
```html
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var map = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  })
		],
		target: 'map',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	// 创建事件监听器
	var singleclickListener = function(event){
		alert('大家好，我是淡叔，这是一个演示如何取消事件监听的应用，之后再点击地图时，你将不会再看到这个说明。');
		// 在响应一次后，注销掉该监听器
		map.un('singleclick', singleclickListener);
	};
	map.on('singleclick', singleclickListener);
</script>
```

针对这个应用，还可以按照下面这样来实现：
```html
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var map = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  })
		],
		target: 'map',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	// 使用once函数，只会响应一次事件，之后自动注销事件监听
	map.once('singleclick', function(event){
		alert('大家好，我是淡叔，这是一个演示如何取消事件监听的应用，之后再点击地图时，你将不会再看到这个说明。');
	})
</script>
```
到此，OpenLayers 3的事件应用基本介绍完毕，后续将介绍OpenLayers 3常用事件。


# 常用事件

几乎OpenLayers主要的类都会派发相关的事件，虽然事件很多，但日常使用的事件大致分为下面几类。

## 常用鼠标事件

### 地图鼠标左键单击事件
对应的类为`ol.Map`，事件名为`singleclick`。  

### 地图鼠标左键双击事件
对应的类为`ol.Map`，事件名为`dblclick`。

### 地图鼠标点击事件
对应的类为`ol.Map`，事件名为`click`。

### 地图鼠标移动事件
对应的类为`ol.Map`，事件名为`pointermove `。

### 地图鼠标拖拽事件
对应的类为`ol.Map`，事件名为`pointerdrag `。

### 地图移动事件
对应的类为`ol.Map`，事件名为`moveend`。

可以通过下面这个地图来尝试一下鼠标事件：
<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="info" style="background-color: #999;">触发事件提示信息</div>
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var map = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  })
		],
		target: 'map',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	map.on('singleclick', function(event){
		document.getElementById('info').innerHTML = '触发了ol.Map的单击事件：singleclick';
	});

	map.on('dblclick', function(event){
		document.getElementById('info').innerHTML = '触发了ol.Map的双击事件：dblclick';
	});

	map.on('click', function(event){
		document.getElementById('info').innerHTML = '触发了ol.Map的点击事件：click';
	});

	// map.on('pointermove', function(event){
	// 	document.getElementById('info').innerHTML = '触发了ol.Map的鼠标移动事件：pointermove';
	// });

	map.on('pointerdrag', function(event){
		document.getElementById('info').innerHTML = '触发了ol.Map的拖拽事件：pointerdrag';
	});

	map.on('moveend', function(event){
		document.getElementById('info').innerHTML = '触发了ol.Map的地图移动事件：moveend';
	});

</script>

注意在`singleclick`和`dblclick`响应之前，都会触发`click`事件，在选择事件时，需要谨慎考虑。 同时发现`moveend`事件在地图缩放的时候，也会触发。 代码如下：

```html
<div id="info" style="background-color: #999;">触发事件提示信息</div>
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var map = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  })
		],
		target: 'map',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	// 响应单击事件
	map.on('singleclick', function(event){
		document.getElementById('info').innerHTML = '触发了ol.Map的单击事件：singleclick';
	});

	// 响应双击事件
	map.on('dblclick', function(event){
		document.getElementById('info').innerHTML = '触发了ol.Map的双击事件：dblclick';
	});

	// 响应点击事件
	map.on('click', function(event){
		document.getElementById('info').innerHTML = '触发了ol.Map的点击事件：click';
	});

	// 响应鼠标移动事件，事件太频繁，故注释掉了，可自行验证该事件
	// map.on('pointermove', function(event){
	// 	document.getElementById('info').innerHTML = '触发了ol.Map的鼠标移动事件：pointermove';
	// });

	// 响应拖拽事件
	map.on('pointerdrag', function(event){
		document.getElementById('info').innerHTML = '触发了ol.Map的拖拽事件：pointerdrag';
	});

	// 地图移动事件
	map.on('moveend', function(event){
		document.getElementById('info').innerHTML = '触发了ol.Map的地图移动事件：moveend';
	});

</script>
```

## 非直接交互事件

### 地图缩放事件
对应的类为`ol.View`，事件名为 `change:resolution`，为什么？ 因为分辨率改变了，自然层级就变化了。

### 地图中心改变事件
对应的类是`ol.View`，事件名为 `change:center`。

下面这个地图，可以让你感受一下这两个事件：

<div id="info2" style="background-color: #999;">事件提示信息</div>
<div id="map2" style="width: 100%"></div>
<script type="text/javascript">
	var map2 = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  })
		],
		target: 'map2',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	var view = map2.getView();
	view.on('change:resolution', function(event){
		document.getElementById('info2').innerHTML = '触发了ol.View的缩放事件：change:resolution，当前层级为： ' + this.getZoom();
	});

	view.on('change:center', function(event){
		document.getElementById('info2').innerHTML = '触发了ol.View的地图中心改变事件：change:center，当前中心点为： ' + ol.proj.transform(
		      this.getCenter(), 'EPSG:3857', 'EPSG:4326');
	});
</script>

### 改变事件
OpenLayers 3的大多数类都拥有一些改变事件，这些事件常用于模块之间联动使用，在二次开发的业务需求中，也会经常需要，所以希望能引起重视，具体的事件可以参照API官网文档的说明。 比如上面的例子其实就是应用的这类事件。

# 阻止事件传递


# 自定义事件及应用

通过API文档，我们可以看到OpenLayers 3的相关类，都有一些事件，但这些事件大多是和现有引擎相关的，并不能满足我们大多数的业务需求。如果能为OpenLayers 3的类增加自定义事件，那么必然能更好地实现业务需求。接下来就尝试为`ol.Feature`添加一个`mouseover`的事件，通过这个事件，就可以实现在鼠标移到`Feature`上时，改变它的样式。

要添加自定义事件，需要知道这样一个事实：`ol.Feature`继承于`ol.Object`，而`ol.Object`具有派发事件([dispatchEvent](http://openlayers.org/en/v3.13.1/apidoc/ol.Object.html#dispatchEvent))和监听事件([on](http://openlayers.org/en/v3.13.1/apidoc/ol.Object.html#on))的功能。 关于这两个功能的详细信息可以参见API文档。这样，我们要自定义事件就非常容易了，如果注意观察，会发现OpenLayers 3中的类都继承于`ol.Object`，也就是说，如果自定义事件方法在`ol.Feature`上有效，那么在其他的OpenLayers 3的类上也是同样有效的。

下面就先展示一下通过自定义的`mouseover`事件来改变`Feature`的样式：

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>

<div id="map" style="width: 100%, height: 400px"></div>
<script>

	// 在原点处创建一个feature
	var feature1 = new ol.Feature({
		geometry: new ol.geom.Point([0, 0])
	});

	// 并设置为半径为100像素的圆，用红色填充
	feature1.setStyle(new ol.style.Style({
		image: new ol.style.Circle({
			radius: 100,
			fill: new ol.style.Fill({
				color: 'red'
			})
		})
	}));

	// 在坐标[5000000, 5000000]处创建另一个feature
	var feature2 = new ol.Feature({
		geometry: new ol.geom.Point([5000000, 5000000])
	});

	// 并设置为半径为100像素的圆，用黄色填充
	feature2.setStyle(new ol.style.Style({
		image: new ol.style.Circle({
			radius: 100,
			fill: new ol.style.Fill({
				color: 'yellow'
			})
		})
	}));

  // 创建地图
  var map = new ol.Map({
		// 设置地图图层
		layers: [
		  // 创建一个使用Open Street Map地图源的瓦片图层
		  new ol.layer.Tile({source: new ol.source.OSM()}),
		  // 把之前创建的feature1和feature2放在另一个层里
		  new ol.layer.Vector({source: new ol.source.Vector({
		  	features: [feature1, feature2]
		  })})
		],
		// 设置显示地图的视图
		view: new ol.View({
		  center: [0, 0],	// 定义地图显示中心于经度0度，纬度0度处
		  zoom: 2			// 并且定义地图显示层级为2
		}),
		// 让id为map的div作为地图的容器
		target: 'map'	
	});

  // 为地图注册鼠标移动事件的监听
  map.on('pointermove', function(event){
  	map.forEachFeatureAtPixel(event.pixel, function(feature){
  		// 为移动到的feature发送自定义的mousemove消息
  		feature.dispatchEvent({type: 'mousemove', event: event});
  	});
  });

  // 为feature1注册自定义事件mousemove的监听
  feature1.on('mousemove', function(event){
  	// 修改feature的样式为半径100像素的园，用蓝色填充
  	this.setStyle(new ol.style.Style({
			image: new ol.style.Circle({
				radius: 100,
				fill: new ol.style.Fill({
					color: 'blue'
				})
			})
		}));
  });
</script>

为了对比，在地图上添加了两个圆，一个黄色的，一个红色的。鼠标移动到红色的圆上，它会变成蓝色的圆。但是鼠标移到黄色的圆上，不会有任何改变。这个功能在官网的例子中也有，参见[select-features](http://openlayers.org/en/v3.13.1/examples/select-features.html)。那么用自定义事件的方式来做，又该怎样来实现呢？对应的源码如下：

```html
<!Doctype html>
<html xmlns=http://www.w3.org/1999/xhtml>
<head>                  
	<meta http-equiv=Content-Type content="text/html;charset=utf-8">
	<meta http-equiv=X-UA-Compatible content="IE=edge,chrome=1">
	<meta content=always name=referrer>
	<title>OpenLayers 3地图示例</title>
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>

<body>
	<div id="map" style="width: 100%, height: 400px"></div>
	<script>

		// 在原点处创建一个feature
		var feature1 = new ol.Feature({
			geometry: new ol.geom.Point([0, 0])
		});

		// 并设置为半径为100像素的圆，用红色填充
		feature1.setStyle(new ol.style.Style({
			image: new ol.style.Circle({
				radius: 100,
				fill: new ol.style.Fill({
					color: 'red'
				})
			})
		}));

		// 在坐标[5000000, 5000000]处创建另一个feature
		var feature2 = new ol.Feature({
			geometry: new ol.geom.Point([5000000, 5000000])
		});

		// 并设置为半径为100像素的圆，用黄色填充
		feature2.setStyle(new ol.style.Style({
			image: new ol.style.Circle({
				radius: 100,
				fill: new ol.style.Fill({
					color: 'yellow'
				})
			})
		}));

	  // 创建地图
	  var map = new ol.Map({
			// 设置地图图层
			layers: [
			  // 创建一个使用Open Street Map地图源的瓦片图层
			  new ol.layer.Tile({source: new ol.source.OSM()}),
			  // 把之前创建的feature1和feature2放在另一个层里
			  new ol.layer.Vector({source: new ol.source.Vector({
			  	features: [feature1, feature2]
			  })})
			],
			// 设置显示地图的视图
			view: new ol.View({
			  center: [0, 0],	// 定义地图显示中心于经度0度，纬度0度处
			  zoom: 2			// 并且定义地图显示层级为2
			}),
			// 让id为map的div作为地图的容器
			target: 'map'	
		});

	  // 为地图注册鼠标移动事件的监听
	  map.on('pointermove', function(event){
	  	map.forEachFeatureAtPixel(event.pixel, function(feature){
	  		// 为移动到的feature发送自定义的mousemove消息
	  		feature.dispatchEvent({type: 'mousemove', event: event});
	  	});
	  });

	  // 为feature1注册自定义事件mousemove的监听
	  feature1.on('mousemove', function(event){
	  	// 修改feature的样式为半径100像素的园，用蓝色填充
	  	this.setStyle(new ol.style.Style({
				image: new ol.style.Circle({
					radius: 100,
					fill: new ol.style.Fill({
						color: 'blue'
					})
				})
			}));
	  });
	</script>
</body>
	
</html>
```
代码中有详细的注释，再辅以API文档，相信应该看懂绝大部分的代码，当然最关键的代码在于最后的两个事件监听，在这两段代码中，可能下面这句不是很明白： 
```javascript
feature.dispatchEvent({type: 'mousemove', event: event});
```
`dispatchEvent`的参数具有`type`和`event`属性，必须这样构造吗？在回答这个问题之前，需要先看一下API文档，发现参数类型为`goog.events.EventLike`，说明它其实用的是google的closure库来实现的，通过closure库的源码我们知道，派发的事件如果是一个对象，那么**必须**包含`type`属性，用于表示事件类型。其他的属性可以自由定义，比如此处定义了`event`属性，并设置对应的值，为的是让鼠标事件传递给`feature1`的监听函数。

`dispatchEvent`的参数会被原封不动的传递给事件响应函数，对应代码
```javascript
feature1.on('mousemove', function(event){
```
里`function`的参数`event`，可以通过调试窗口看到此处的`event`和`dispatchEvent`的参数是一样的。注意事件名称是可以自定义的，只要派发和监听使用的事件名称是一致的就可以。

除了可以通过`dispatchEvent({type: 'mousemove', event: event})`这种形式派发一个事件之外，还可以通过`dispatchEvent('mousemove')`这中形式直接发送`mousemove`事件。有兴趣的同学可以自行验证。

到此，我们就完成了自定义事件的三件事：定义事件类型，派发事件，监听事件。

使用自定义事件会带来下面几个好处：
* 灵活控制，对比一下官网例子和这个例子，就能看出一二。
* 能更好地满足业务需要，适当地进行扩展。
* 采用更统一的事件处理框架。

有很多同学为了处理多个`Feature`的鼠标事件，可能会为`Map`注册很多次`pointermove`事件监听，从而会导致性能降低，这种做法是不可取的。如果采用上面这种自定义事件的处理方式，就只用注册一次，让真正有需要的`Feature`通过注册事件响应来处理业务，从而还能简化处理逻辑。

上面这个例子鼠标移到红色圆后变蓝色，但是移出圆后，还是蓝色，能不能再移出圆的时候再变成红色呢？ 试试用自定义事件的方式再改进一下，实现`mousein`和`mouseout`两种事件。
