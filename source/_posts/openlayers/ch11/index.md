---
title: OpenLayers 3 动画
date: 2018-01-18 20:29:59
tags: openLayers
categories: openLayers
---

# 动画
在OpenLayers 3中，动画是随处可见的，比如平移地图时，地图移动会有惯性，停止移动后，还会继续沿着之前的方向移动一会。 比如下面这个地图具有回到原始点的功能，一个是有动画效果的，一个是没有动画效果的。

<!-- more -->

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<input type="button" value="回到原点-不带动画" onclick="backNoAnim()"></input>
<input type="button" value="回到原点-带动画" onclick="backWithAnim()"></input>
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

	function backNoAnim() {
    map.getView().setCenter(ol.proj.transform([104, 30], 'EPSG:4326', 'EPSG:3857'));
	}

	function backWithAnim() {
		var pan = ol.animation.pan({
      duration: 2000,
      source: map.getView().getCenter()
    });
    map.beforeRender(pan);
    map.getView().setCenter(ol.proj.transform([104, 30], 'EPSG:4326', 'EPSG:3857'));
	}
	
</script>

先把地图移动到北京，再点击下方的两个按钮，感受一下带动画，和不带动画的差别，绝大多数人还是会喜欢带动画的。 无疑，动画具有很大的吸引力。 本章节将把你带入OpenLayers 3的动画世界，让你也能应用动画效果，做出漂亮的效果。


# 动画简单应用
前面那个回到原点的动画，使用了OpenLayers 3内置的动画效果，代码如下：

```html
<div id="map" style="width: 100%"></div>
<!--增加两个按钮，一个演示不带动画，一个演示带动画-->
<input type="button" value="回到原点-不带动画" onclick="backNoAnim()"></input>
<input type="button" value="回到原点-带动画" onclick="backWithAnim()"></input>
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

	// 不带动画的实现
	function backNoAnim() {
    map.getView().setCenter(ol.proj.transform([104, 30], 'EPSG:4326', 'EPSG:3857'));
	}

	// 带动画的实现
	function backWithAnim() {
		// 注意： 应用内置的动画，实现平移动画
		var pan = ol.animation.pan({
      duration: 2000,
      source: map.getView().getCenter()
    });
    // 注意： 使用beforeRender来添加
    map.beforeRender(pan);
    // 然后才是重新设置地图中心点到原来的位置
    map.getView().setCenter(ol.proj.transform([104, 30], 'EPSG:4326', 'EPSG:3857'));
	}
	
</script>
```
关键的代码始终在最后，结合注释，应该可以理解。 我们使用的内置动画是`ol.animation.pan`，用于平移动画，更详细的信息请参考[官网API](http://openlayers.org/en/v3.13.1/apidoc/ol.animation.html#.pan)。 可以看到它是一个函数，返回一个`ol.PreRenderFunction`，添加到`ol.Map`，从而在渲染的时候使用，实现动画。 

除了上面应用的动画之外，还有一些内置的动画，都在`ol.animation`里面，从官网API可以看到，包括：

* `ol.animation.bounce`: 来回弹。
* `ol.animation.pan`: 平移。
* `ol.animation.rotate`: 旋转。
* `ol.animation.zoom`: 缩放。

这些动画都可以尝试，或许就有你需要的。 


# 动画高阶应用
虽然内置动画并不多，但是细心的同学已经发现了内置动画有个参数`easing`，这是一个强大的参数，因为可以让开发者自定义动画实现，从而实现动画效果的扩展。 其实已经内置了一些相关的实现，在`ol.easing`里面，涉及到一些常用的效果，包括：

* `ol.easing.easeIn`: 加速
* `ol.easing.easeOut`: 减速
* `ol.easing.inAndOut`: 先加速再减速
* `ol.easing.linear`: 匀速
* `ol.easing.upAndDown`: 和inAndOut类似

在前面的地图的基础上修改一下，让大家体验一下不同的动画的效果：

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<select id = "easing-type">
	<option>easeIn</option>
	<option>easeOut</option>
	<option>inAndOut</option>
	<option>linear</option>
	<option>upAndDown</option>
</select>
<input type="button" value="回到原点" onclick="backWithAnim();"></input>
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

	function backNoAnim() {
    map.getView().setCenter(ol.proj.transform([104, 30], 'EPSG:4326', 'EPSG:3857'));
	}

	function getEasing() {
		var typeSelect = document.getElementById('easing-type');
		var easing = typeSelect.options[typeSelect.selectedIndex].text;
		if (easing === 'easeIn') {
			return ol.easing.easeIn;
		} else if (easing === 'easeOut') {
			return ol.easing.easeOut;
		} else if (easing === 'inAndOut') {
			return ol.easing.inAndOut;
		} else if (easing === 'linear') {
			return ol.easing.linear;
		} else if (easing === 'upAndDown') {
			return ol.easing.upAndDown;
		} 
	}

	function backWithAnim() {
		var pan = ol.animation.pan({
      duration: 2000,
      source: map.getView().getCenter(),
      easing: getEasing()
    });
    map.beforeRender(pan);
    map.getView().setCenter(ol.proj.transform([104, 30], 'EPSG:4326', 'EPSG:3857'));
	}
	
</script>

尝试完所有的动画方式后，可以看一下其实现代码：

```html
<div id="map" style="width: 100%"></div>
<!--添加动画效果选择项-->
<select id = "easing-type">
	<option>easeIn</option>
	<option>easeOut</option>
	<option>inAndOut</option>
	<option>linear</option>
	<option>upAndDown</option>
</select>
<input type="button" value="回到原点" onclick="backWithAnim()"></input>
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

	// 根据选择项，返回对应的动画，供下面的backWithAnim函数使用
	function getEasing() {
		var typeSelect = document.getElementById('easing-type');
		var easing = typeSelect.options[typeSelect.selectedIndex].text;
		if (easing === 'easeIn') {
			return ol.easing.easeIn;
		} else if (easing === 'easeOut') {
			return ol.easing.easeOut;
		} else if (easing === 'inAndOut') {
			return ol.easing.inAndOut;
		} else if (easing === 'linear') {
			return ol.easing.linear;
		} else if (easing === 'upAndDown') {
			return ol.easing.upAndDown;
		} 
	}

	function backWithAnim() {
		var pan = ol.animation.pan({
      duration: 2000,
      source: map.getView().getCenter(),
      easing: getEasing()	// 设置对应选择的动画
    });
    map.beforeRender(pan);
    map.getView().setCenter(ol.proj.transform([104, 30], 'EPSG:4326', 'EPSG:3857'));
	}
	
</script>
```
通过上面这种方法，我们完全可以自定义动画函数，通过官网API文档可知，这些函数有一个参数`t`，范围在0-1之间，然后函数返回一个0-1之间的数。 结合动画业务来看，虽然官网没有说明参数表示什么意思，但是我们可以猜测它就是时间，返回的值应该目标达成比。 下面我们自己来实现一个sin曲线式的动画：

<div id="map2" style="width: 100%"></div>
<input type="button" value="回到原点" onclick="backWithAnim2();"></input>
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

	// sin曲线动画
	function sin(t) {
		// 使用sin曲线公式
		return Math.sin(t * Math.PI / 2);
	}

	function backWithAnim2() {
		var pan = ol.animation.pan({
      duration: 2000,
      source: map2.getView().getCenter(),
      easing: sin
    });
    map2.beforeRender(pan);
    map2.getView().setCenter(ol.proj.transform([104, 30], 'EPSG:4326', 'EPSG:3857'));
	}
	
</script>

速度一开始会快一点，然后慢下来， 对应的代码如下：
```html
<div id="map2" style="width: 100%"></div>
<input type="button" value="回到原点" onclick="backWithAnim2();"></input>
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

	// sin曲线动画
	function sin(t) {
		// 使用sin曲线公式
		return Math.sin(t * Math.PI / 2);
	}

	function backWithAnim2() {
		var pan = ol.animation.pan({
      duration: 2000,
      source: map2.getView().getCenter(),
      easing: sin // 应用sin曲线动画
    });
    map2.beforeRender(pan);
    map2.getView().setCenter(ol.proj.transform([104, 30], 'EPSG:4326', 'EPSG:3857'));
	}
	
</script>
```


# 利用postcompose事件做动画

前面的内置动画，几乎都是和地图浏览相关的，很多时候我们有更多的动画需求，在此向大家介绍另一种可以做出动画的方式。 那就是利用`ol.Map`的`postcompose`事件，这个事件在地图渲染时都会触发，而我们只要改变地图上的某个`feature`或者`layer`或者其他任何东西，就会触发重新渲染。 如果利用这个原理，我们不断的改变`feature`的样式，就会触发`postcompose`，在这个事件的监听器里再改变`feature`的样式，就又会触发`postcompose`，从而一只循环下去，出现动画效果。  比如像下面这样：

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var layer = new ol.layer.Vector({
		source: new ol.source.Vector()
	})
	var map = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  }),
		  layer
		],
		target: 'map',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	var circle = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	});
	circle.setStyle(new ol.style.Style({
		image: new ol.style.Circle({
			radius: 0,
			stroke: new ol.style.Stroke({
				color: 'red',
				size: 1
			})
		})
	}));
	layer.getSource().addFeature(circle);

	var radius = 0;
	map.on('postcompose', function(){
		radius++;
		radius = radius % 20;
		circle.setStyle(new ol.style.Style({
			image: new ol.style.Circle({
				radius: radius,
				stroke: new ol.style.Stroke({
					color: 'red',
					size: 1
				})
			})
		}));
	})
</script>

地图中间的小圆圈，不断的重复变大动画，代码如下：

```html
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var layer = new ol.layer.Vector({
		source: new ol.source.Vector()
	})
	var map = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  }),
		  layer
		],
		target: 'map',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	var circle = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	});
	circle.setStyle(new ol.style.Style({
		image: new ol.style.Circle({
			radius: 0,
			stroke: new ol.style.Stroke({
				color: 'red',
				size: 1
			})
		})
	}));
	layer.getSource().addFeature(circle);

	// 关键的地方在此：监听postcompose事件，在里面重新设置circle的样式
	var radius = 0;
	map.on('postcompose', function(){
		// 增大半径，最大20
		radius++;
		radius = radius % 20;
		// 设置样式
		circle.setStyle(new ol.style.Style({
			image: new ol.style.Circle({
				radius: radius,
				stroke: new ol.style.Stroke({
					color: 'red',
					size: 1
				})
			})
		}));
	})
</script>
```

使用过`Javascript`做动画的同学可能会想，为什么弄的这么麻烦，直接用`requestAnimationFrame`不就行了？ 是的，我也这么认为，这个会比较简单，但如果你非要用OpenLayers 3的事件，也就可以照着上面的方式来实现动画了。