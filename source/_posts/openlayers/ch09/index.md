---
title: OpenLayers 3 内置交互方式介绍
date: 2018-01-18 20:29:59
tags: openLayers
categories: openLayers
---

# Interaction
交互是人机界面非常重要的一个部分，任何一个GIS引擎都会内置这一部分功能，而不是让开发者自己实现。 在交互方式上，几乎都是约定俗成的，比如用鼠标左键双击地图可以放大地图，按住鼠标左键拖动地图可以移动浏览地图，用滚动鼠标中间的滑轮可以放大缩小地图等等。 OpenLayers 3都内置支持这些交互方式，同时还具备更多的其他交互方式，这些都将一并在本章节介绍。 更为重要的是，了解其中的基本原理，并在此基础上，应用于自己的业务开发。

<!-- more -->

# 内置交互方式介绍

OpenLayers 3提供了最基本的地图放大，缩小，平移等功能，以满足用户浏览地图的需要。 这些功能都是内置的，实现类都放在包`ol.interaction`下面，可以通过官网API查询到。 在做二次开发的时候，我们无需做任何设置，地图就具有这些功能，比如下面这个最简单的地图，你可以用鼠标对它进行浏览，不管是放大，还是缩小，平移都可以。

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
</script>

这个地图的代码在本教程最开始的时候，就已经见过了：
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
</script>
```
虽然代码中没有做任何的设置，`ol.Map`的默认行为中，设置了和地图的交互方式，如果表示出来，代码是这样的：
```html
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var map = new ol.Map({
		interactions: ol.interaction.defaults(),  // 不设置的情况下，默认会设置为ol.interaction.defaults()
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
</script>
```
`ol.interaction.defaults()`这个函数用于返回默认的交互方式，通过API文档可知，还可以通过参数控制交互方式，非常的灵活。 在进一步深入之前，还是先了解一下默认都提供了那些交互方式：

* 按住alt+shift键，用鼠标左键拖动地图，就能让地图旋转，对应的交互类为`ol.interaction.DragRotate`。
* 用鼠标左键双击地图，就可以放大地图，对应的交互类为`ol.interaction.DoubleClickZoom`。
* 用鼠标左键，拖拽地图，就可以平移地图，对应的交互类为`ol.interaction.DragPan`。
* 在触摸屏上，用两个手指在触摸屏上旋转，就可以旋转地图，对应的交互类为`ol.interaction.PinchRotate`。
* 在触摸屏上，用两个手指在触摸屏上缩放，就可以缩放地图，对应的交互类为`ol.interaction.PinchZoom`。
* 用键盘上的上下左右键，就可以平移地图，对应的交互类为`ol.interaction.KeyboardPan`。
* 用键盘上的+/-键，就可以缩放地图，对应的交互类为`ol.interaction.KeyboardZoom`。
* 滚动鼠标中间的滑轮，就可以缩放地图，对应的交互类为`ol.interaction.MouseWheelZoom`。
* 按住shift键，同时用鼠标左键在地图上拖动，就可以放大地图，对应的交互类为`ol.interaction.DragZoom`。

从上面可以看到，支持的交互方式挺多的，归纳为缩放，平移，旋转三类。 同时支持键盘，鼠标，和触屏三种方式。 

虽然默认的交互方式很全，但如果我们的地图只是在PC端提供或者只是在触屏提供，那么有些交互方式就会显得多余，最好是去掉不需要的，或者我们的地图因为业务需要，不允许用户平移，或者缩放地图。 为了满足这样的需求，`ol.interaction.defaults()`提供了相应的参数来控制交互方式，详见[ol.interaction.defaults API文档](http://openlayers.org/en/v3.13.1/apidoc/ol.interaction.html#.defaults)。 下面简单演示一个不能缩放的地图：
<div id="map2" style="width: 100%"></div>
<script type="text/javascript">
	new ol.Map({
		interactions: ol.interaction.defaults({
			doubleClickZoom: false,
			mouseWheelZoom: false,
			shiftDragZoom: false,
			pinchZoom:false
		}),
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
</script>

代码如下：
```html
<div id="map2" style="width: 100%"></div>
<script type="text/javascript">
	new ol.Map({
		// 让所有的zoom开关都设置为false
		interactions: ol.interaction.defaults({
			doubleClickZoom: false,
			mouseWheelZoom: false,
			shiftDragZoom: false,
			pinchZoom:false
		}),
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
</script>
```
有时候也需要设置地图不能旋转，只要设置`altShiftDragRotate:false, pinchRotate:false`即可，可自行验证。

## 注意事项
细心的读者有可能已经发现了，前面的第一个地图，我们并没有做任何的限制，但是用键盘控制鼠标平移和缩放，是不行的。 原因在于地图使用的`target`获取不到键盘事件，我们需要做个简单的设置才行：

<div id="map3" tabindex="0" style="width: 100%"></div>
<script type="text/javascript">
	new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  })
		],
		target: 'map3',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});
</script>

代码如下：

```html
<!--注意：需要设置tabindex，才能使div获得键盘事件-->
<div id="map3" tabindex="0" style="width: 100%"></div>
<script type="text/javascript">
	new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  })
		],
		target: 'map3',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});
</script>
```

# 实现原理

不管是上一节中的哪一种交互方式，本质上都是通过监听事件，来处理相应的业务的，在`ol.Map`的实现代码中，有下面的代码：

```javascript
	/**
   * @private
   * @type {ol.MapBrowserEventHandler}
   */
  this.mapBrowserEventHandler_ = new ol.MapBrowserEventHandler(this);
  for (var key in ol.MapBrowserEvent.EventType) {	// 遍历所有的事件类型
    ol.events.listen(this.mapBrowserEventHandler_, ol.MapBrowserEvent.EventType[key],
        this.handleMapBrowserEvent, this);	// 监听事件，函数handleMapBrowserEvent为事件响应函数
  }

  ......

  /**
	 * @param {ol.MapBrowserEvent} mapBrowserEvent The event to handle.
	 */
	ol.Map.prototype.handleMapBrowserEvent = function(mapBrowserEvent) {
	  if (!this.frameState_) {
	    // With no view defined, we cannot translate pixels into geographical
	    // coordinates so interactions cannot be used.
	    return;
	  }
	  this.focus_ = mapBrowserEvent.coordinate;
	  mapBrowserEvent.frameState = this.frameState_;
	  var interactions = this.getInteractions();
	  goog.asserts.assert(interactions !== undefined,
	      'interactions should be defined');
	  var interactionsArray = interactions.getArray();
	  var i;
	  if (this.dispatchEvent(mapBrowserEvent) !== false) {	// 注意：如果事件处理返回false，交互类就不起作用了
	    for (i = interactionsArray.length - 1; i >= 0; i--) {	// 遍历所有的交互方式
	      var interaction = interactionsArray[i];
	      if (!interaction.getActive()) {
	        continue;
	      }
	      var cont = interaction.handleEvent(mapBrowserEvent);	// 让各个交互类处理响应的事件
	      if (!cont) {
	        break;
	      }
	    }
	  }
	};

```
至于每一个交互类如何处理，就和业务相关了，再此之前，先看一下具体有哪些事件：

```javascript
	/**
	 * Constants for event names.
	 * @enum {string}
	 */
	ol.MapBrowserEvent.EventType = {

	  /**
	   * A true single click with no dragging and no double click. Note that this
	   * event is delayed by 250 ms to ensure that it is not a double click.
	   * @event ol.MapBrowserEvent#singleclick
	   * @api stable
	   */
	  SINGLECLICK: 'singleclick',

	  /**
	   * A click with no dragging. A double click will fire two of this.
	   * @event ol.MapBrowserEvent#click
	   * @api stable
	   */
	  CLICK: ol.events.EventType.CLICK,

	  /**
	   * A true double click, with no dragging.
	   * @event ol.MapBrowserEvent#dblclick
	   * @api stable
	   */
	  DBLCLICK: ol.events.EventType.DBLCLICK,

	  /**
	   * Triggered when a pointer is dragged.
	   * @event ol.MapBrowserEvent#pointerdrag
	   * @api
	   */
	  POINTERDRAG: 'pointerdrag',

	  /**
	   * Triggered when a pointer is moved. Note that on touch devices this is
	   * triggered when the map is panned, so is not the same as mousemove.
	   * @event ol.MapBrowserEvent#pointermove
	   * @api stable
	   */
	  POINTERMOVE: 'pointermove',

	  POINTERDOWN: 'pointerdown',
	  POINTERUP: 'pointerup',
	  POINTEROVER: 'pointerover',
	  POINTEROUT: 'pointerout',
	  POINTERENTER: 'pointerenter',
	  POINTERLEAVE: 'pointerleave',
	  POINTERCANCEL: 'pointercancel'
	};
```
这些事件在`ol.Map`的API文档里面，有些是能看到的，就是注释里面有`@api`的部分，其他的是暂时内部使用的。 按照这个逻辑，我们也可以实现自己的交互方式。 但有一点需要注意，`ol.Map`处理事件是有先后顺序的，注意看最前面的那段代码的注释，`ol.Map`会先派发事件给自己的监听器，然后才会把事件给`interaction`类处理。 如果前面的事件监听器返回`false`，那么后面的交互类就不会起作用。 比如像下面这样，用鼠标左键双击地图，并不能放大地图：

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

	map.on('dblclick', function(event){
		return false;
	});
</script>

为什么？ 注意看代码：
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

	// 监听了dblclick事件，并返回了false
	map.on('dblclick', function(event){
		return false;
	});
</script>
```
这样做了后，后果非常的严重，出问题后，可能还不知道为什么？ 所以建议不要轻易在`MapBrowserEvent`事件的监听器里面返回`false`。

到此，我们可以进一步分析一下`ol.interaction`相关交互类的内部实现了，以`ol.interaction.DoubleClickZoom`为例，其核心必然是处理事件：

```javascript
	ol.interaction.DoubleClickZoom.handleEvent = function(mapBrowserEvent) {
	  var stopEvent = false;
	  var browserEvent = mapBrowserEvent.originalEvent;
	  if (mapBrowserEvent.type == ol.MapBrowserEvent.EventType.DBLCLICK) {	// 事件类型过滤
	    var map = mapBrowserEvent.map;
	    var anchor = mapBrowserEvent.coordinate;
	    var delta = browserEvent.shiftKey ? -this.delta_ : this.delta_;	// 按住shift键，就缩小，否则就放大
	    var view = map.getView();
	    goog.asserts.assert(view, 'map must have a view');
	    ol.interaction.Interaction.zoomByDelta(
	        map, view, delta, anchor, this.duration_);		// 调用 ol.interaction.Interaction.zoomByDelta函数实现放大缩小
	    mapBrowserEvent.preventDefault();
	    stopEvent = true;
	  }
	  return !stopEvent;
	};
```

代码其实很简单，如果要自己实现一种交互方式，对照`ol.interaction.DoubleClickZoom`这个学习，再加以应用就可以了。

# Feature选取之选中样式
OpenLayers 3除了在地图浏览方面提供内置的交互方式之外，还提供了地图上`Feature`选取的交互类： `ol.interaction.Select`。 这是一个经常会用到的类，应用范围非常的广。 我们可以先简单操作一下下面地图中的圆，点击一下，颜色就变了：

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var layer = new ol.layer.Vector({
		source: new ol.source.Vector()
	});
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

	// 在地图上添加一个圆
	var circle = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	})
	circle.setStyle(new ol.style.Style({
		image: new ol.style.Circle({
			radius: 10,
			fill: new ol.style.Fill({
				color: 'red'
			})
		})
	}));

	layer.getSource().addFeature(circle);

	// 添加一个用于选择Feature的交互方式
	var selectSingleClick = new ol.interaction.Select({
		// style: new ol.style.Style({
		// 	image: new ol.style.Circle({
		// 		radius: 10,
		// 		fill: new ol.style.Fill({
		// 			color: 'blue'
		// 		})
		// 	})
		// })
	});
	map.addInteraction(selectSingleClick);
	selectSingleClick.on('select', function(event){
		event.selected[0].setStyle(new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'blue'
				})
			})
		}));
	})

</script>

在[自定义事件及应用](../ch08/08-05.md)中，我们用了方法`map.forEachFeatureAtPixel`来获取当前选择的`Feature`，这个例子中，我们没有这样使用：

```html
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var layer = new ol.layer.Vector({
		source: new ol.source.Vector()
	});
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

	// 在地图上添加一个圆
	var circle = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	})
	circle.setStyle(new ol.style.Style({
		image: new ol.style.Circle({
			radius: 10,
			fill: new ol.style.Fill({
				color: 'red'
			})
		})
	}));

	layer.getSource().addFeature(circle);

	// 前面的代码我们已经看了很多遍了，关键是下面这段代码
	// 添加一个用于选择Feature的交互方式
	var selectSingleClick = new ol.interaction.Select({
		// API文档里面有说明，可以设置style参数，用来设置选中后的样式，但是这个地方我们注释掉不用，因为就算不注释，也没作用，为什么？
		// style: new ol.style.Style({
		// 	image: new ol.style.Circle({
		// 		radius: 10,
		// 		fill: new ol.style.Fill({
		// 			color: 'blue'
		// 		})
		// 	})
		// })
	});
	map.addInteraction(selectSingleClick);
	// 监听选中事件，然后在事件处理函数中改变被选中的`feature`的样式
	selectSingleClick.on('select', function(event){
		event.selected[0].setStyle(new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'blue'
				})
			})
		}));
	})

</script>
```
官网其实有例子[select-features](http://openlayers.org/en/v3.13.1/examples/select-features.html)，但是这里面还是有一些需要注意的地方，比如上面注释里面说到的，为什么`style`参数设置了没用？ 因为我们的`circle`本身就设置了样式，而`style`参数设置的样式，其实是设置在内部新建的一个`layer`上的，而OpenLayers 3中，`feature`的样式优先级是大于`layer`的样式的优先级的。所以没生效，如果换成下面这种方式，就可以了：

<div id="map2" style="width: 100%"></div>
<script type="text/javascript">
	var layer2 = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'red'
				})
			})
		})
	});
	var map2 = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  }), 
		  layer2
		],
		target: 'map2',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	// 在地图上添加一个圆
	var circle2 = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	})
	layer2.getSource().addFeature(circle2);

	// 添加一个用于选择Feature的交互方式
	map2.addInteraction(new ol.interaction.Select({
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'blue'
				})
			})
		})
	}));
</script>

代码变成这样了：

```html
<div id="map2" style="width: 100%"></div>
<script type="text/javascript">
	var layer2 = new ol.layer.Vector({
		source: new ol.source.Vector(),
		// 注意：把feature上的style，直接移到layer上，避免直接在feature上设置style
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'red'
				})
			})
		})
	});
	var map2 = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  }), 
		  layer2
		],
		target: 'map2',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	// 在地图上添加一个圆
	var circle2 = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	})
	// 此处不再为feature设置style
	layer2.getSource().addFeature(circle2);

	// 添加一个用于选择Feature的交互方式
	map2.addInteraction(new ol.interaction.Select({
		// 设置选中后的style
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'blue'
				})
			})
		})
	}));
</script>
```
代码改成上面这样就可以了。在使用过程中，请千万留意这个问题。 

# Feature选取之条件过滤
涉及到选取，自然会有各种条件用于过滤，比如是鼠标左键单击，还是双击，是可以选取地图上的任意`feature`，还是某一类的`feature`。 对于这些需求，`ol.interaction.Select`都能满足，在API的文档里面就有相关的参数设置，下面就是一些简单的使用示例。

## 改变默认的单击选取方式
默认情况下，是支持鼠标左键单击选取`feature`的，在地图上其他地方点击一下，就取消选取了，但这并不是定死的选取方式，你完全可以自定义。 比如鼠标移动到`feature`上，就选取了，试试：

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var layer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'red'
				})
			})
		})
	});
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

	// 在地图上添加一个圆
	var circle = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	})
	layer.getSource().addFeature(circle);

	// 添加一个用于选择Feature的交互方式
	map.addInteraction(new ol.interaction.Select({
		condition: ol.events.condition.pointerMove, 	// 唯一的不同之处，设置鼠标移到feature上就选取
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'blue'
				})
			})
		})
	}));
</script>

代码同之前的例子只有一个地方不同：

```html
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
	var layer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'red'
				})
			})
		})
	});
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

	// 在地图上添加一个圆
	var circle = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	})
	layer.getSource().addFeature(circle);

	// 添加一个用于选择Feature的交互方式
	map.addInteraction(new ol.interaction.Select({
		condition: ol.events.condition.pointerMove, 	// 唯一的不同之处，设置鼠标移到feature上就选取
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'blue'
				})
			})
		})
	}));
</script>
```

`ol.events.condition`里面有很多内置的条件过滤函数，可以在[官网API](http://openlayers.org/en/v3.13.1/apidoc/ol.events.condition.html)查询，如果内置方式没有，也可以自己写一个类似的函数，比如：

```javascript
ol.events.condition.singleClick = function(mapBrowserEvent) {
  return mapBrowserEvent.type == ol.MapBrowserEvent.EventType.SINGLECLICK;
};
```
就只是一个类型判断而已，是否非常的简单，任何人都可以自定义一个这种条件过滤函数。


## 直接对`feature`过滤
除了可以自定义选取的交互方式之外，我们还可以对`feature`进行过滤，如果当前选取的并不是我们需要的`feature`，我们可以决定不选取。 比如我们只希望用户能选取圆形，而不能选取五星，选取方式为默认的单击：

<div id="map2" style="width: 100%"></div>
<script type="text/javascript">
	var circleLayer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'red'
				})
			})
		})
	});

	var starLayer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			image: new ol.style.RegularShape({
				points: 5,
				radius1: 20,
				radius2: 10,
				fill: new ol.style.Fill({
					color: 'red'
				})
			})
		})
	});

	var map2 = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  }), 
		  circleLayer, starLayer
		],
		target: 'map2',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	// 在地图上添加一个圆
	var circle1 = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	})
	circleLayer.getSource().addFeature(circle1);

	// 在地图上添加一个五星
	var star = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104.06, 30.05], 'EPSG:4326', 'EPSG:3857'))
	})
	starLayer.getSource().addFeature(star);

	// 添加一个用于选择Feature的交互方式
	map2.addInteraction(new ol.interaction.Select({
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'blue'
				})
			})
		}),
		filter: function(feature, layer){
			return layer === circleLayer;
		}
	}));
</script>

代码其实也是非常简单，前面大部分代码都是一样的，关键在最后：

```html
<div id="map2" style="width: 100%"></div>
<script type="text/javascript">
	// 创建一个用于存放circle的layer
	var circleLayer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'red'
				})
			})
		})
	});

	// 创建一个用于存放star的layer
	var starLayer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			image: new ol.style.RegularShape({
				points: 5,
				radius1: 20,
				radius2: 10,
				fill: new ol.style.Fill({
					color: 'red'
				})
			})
		})
	});

	var map2 = new ol.Map({
		layers: [
		  new ol.layer.Tile({
		    source: new ol.source.OSM()
		  }), 
		  circleLayer, starLayer
		],
		target: 'map2',
		view: new ol.View({
		  center: ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'),
		  zoom: 10
		})
	});

	// 在地图上添加一个圆
	var circle1 = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	})
	circleLayer.getSource().addFeature(circle1);

	// 在地图上添加一个五星
	var star = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104.06, 30.05], 'EPSG:4326', 'EPSG:3857'))
	})
	starLayer.getSource().addFeature(star);

	// 添加一个用于选择Feature的交互方式
	map2.addInteraction(new ol.interaction.Select({
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'blue'
				})
			})
		}),
		// 关键： 设置过了条件，可以用feature来写过滤，也可以用layer来写过滤
		filter: function(feature, layer){
			return layer === circleLayer;
		}
	}));
</script>
```

## 选取多个feature
前面的例子都只是一个`feature`的情况，在实际应用中，肯定是不止一个的，如果要能同时选择多个，只需要设置一下构造函数的参数`multi: true`，默认情况下，只能选取一个`feature`。 仿照上面的代码，可自行尝试。

# Feature选取之取消选中
 取消选中有两种方式， 一种是在界面上以交互的方式取消选中，一种是用代码的方式取消选中，下面就分别介绍一下

## 交互方式取消选中
如果是采用的默认的方式选取`feature`，即使用鼠标左键单击`feature`就可选取。那么这种交互方式下，要取消选中，则只需要同样使用鼠标左键，在地图其他地方单击一下，就可取消选中。 可跳到[要素选取之选中样式](09-03.md)，在第一个地图上试试。 

如果采用的是自定义的方式选取`feature`，那么要取消选中，交互方式还是一样的，只要目标不再是已选中的`feature`就行，就能取消选中。 比如[要素选取之条件过滤](09-04.md)中，采用了自定义的鼠标移入`feature`就可选中，要取消选中，只需要把鼠标移出`feature`即可。

## 代码方式取消选中
点击地图下方的“取消选中”按钮，就可触发代码，取消`circle`的选中状态：

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<input type="button" value="取消选中" onclick="unselectFeature();"></input>
<script type="text/javascript">
	var layer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'red'
				})
			})
		})
	});
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

	// 在地图上添加一个圆
	var circle = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	})
	layer.getSource().addFeature(circle);

	// 添加一个用于选择Feature的交互方式
	var clickSelect = new ol.interaction.Select({
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'blue'
				})
			})
		})
	});
	map.addInteraction(clickSelect);

	// 取消选中
	function unselectFeature() {
		clickSelect.getFeatures().clear();
		// 下面这样也是可以取消选中的，根据情况选择
		// map.removeInteraction(clickSelect);
	}
</script>

前面的代码都一样，关键在最后：

```html
<div id="map" style="width: 100%"></div>
<input type="button" value="取消选中" onclick="unselectFeature();"></input>
<script type="text/javascript">
	var layer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'red'
				})
			})
		})
	});
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

	// 在地图上添加一个圆
	var circle = new ol.Feature({
		geometry: new ol.geom.Point(ol.proj.transform(
		      [104, 30], 'EPSG:4326', 'EPSG:3857'))
	})
	layer.getSource().addFeature(circle);

	// 添加一个用于选择Feature的交互方式
	var clickSelect = new ol.interaction.Select({
		style: new ol.style.Style({
			image: new ol.style.Circle({
				radius: 10,
				fill: new ol.style.Fill({
					color: 'blue'
				})
			})
		})
	});
	map.addInteraction(clickSelect);

	// 取消选中
	function unselectFeature() {
		clickSelect.getFeatures().clear();
		// 下面这样也是可以取消选中的，根据情况选择
		// map.removeInteraction(clickSelect);
	}
</script>
```

至此，地图上元素的选取相关的知识点都介绍完毕，希望能逐个掌握并融会贯通于实际应用中。

# 绘图
除了内置了用于选取`feature`的类之外，OpenLayers 3还提供了用户绘图的类`ol.interaction.Draw`，支持绘制点，线，多边形，圆等绘制。 我们可以先看一下下面这个可以绘制一条线的例子，用鼠标左键单击地图，地图上就会出现待绘制线的起点，随后移动鼠标，就看到了线，再单击地图，就在这个线上添加了点，依次可以添加多个点，如果要完成线的绘制，在最后一个点处，再点击一下鼠标左键即可。

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

	map.addInteraction(new ol.interaction.Draw({
		type: 'LineString'
	}));
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

	// 添加绘图的交互类
	map.addInteraction(new ol.interaction.Draw({
		type: 'LineString' // 设置绘制线
	}));
</script>
```
看起来非常的简单。 但貌似还不可用，因为不知道怎么保存起来，下面我们把绘制完的线保存起来：
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

	// 添加一个绘制的线使用的layer
	var lineLayer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			stroke: new ol.style.Stroke({
				color: 'red',
				size: 1
			})
		})
	})
	map2.addLayer(lineLayer);

	map2.addInteraction(new ol.interaction.Draw({
		type: 'LineString',
		source: lineLayer.getSource()	// 注意设置source，这样绘制好的线，就会添加到这个source里
	}));
</script>

比上一个地图写的代码稍微多一点，关键的代码还是在最后：

```html
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

	// 添加一个绘制的线使用的layer
	var lineLayer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			stroke: new ol.style.Stroke({
				color: 'red',
				size: 1
			})
		})
	})
	map2.addLayer(lineLayer);

	map2.addInteraction(new ol.interaction.Draw({
		type: 'LineString',
		source: lineLayer.getSource()	// 注意设置source，这样绘制好的线，就会添加到这个source里
	}));
</script>
```
这样，在地图上绘制一条线的工作基本上算是完成了。 按照这个流程，修改`ol.interaction.Draw`的构造参数`type`的值为'Point'，'Polygon',，'MultiPoint'，'MultiLineString'，'MultiPolygon' 或者 'Circle'就可以添加点，多边形，多个点，多条线，多个多边形，及圆。 可以自行修改验证。

# 绘图进阶
前面只是简单的绘制上了一条线，在实际业务中，可能还需要设置绘图时的样式，限制绘制的点的个数，获取绘制的图形的所有坐标等。 在上一节的基础上，以下面这个地图为例，再给大家介绍一下这些方面的更深入的应用。下面绘制这条线的点不能超过4个，在绘制时，样式会和前一节的样式不太一样，在地图上方，会显示当前绘制完的线的所有点的坐标。

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div style="background-color: #999;"><span>当前绘制线的坐标：</span><span id='points'></span></div>
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

	// 添加一个绘制的线使用的layer
	var lineLayer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			stroke: new ol.style.Stroke({
				color: 'red',
				size: 1
			})
		})
	})
	map.addLayer(lineLayer);
	var lineDraw = new ol.interaction.Draw({
		type: 'LineString',
		source: lineLayer.getSource(),	// 注意设置source，这样绘制好的线，就会添加到这个source里
		style: new ol.style.Style({
			stroke: new ol.style.Stroke({
				color: '#009933',
				size: 1
			})
		}),
		maxPoints: 4
	});

	// 监听线绘制结束事件，获取坐标
	lineDraw.on('drawend', function(event){
		document.getElementById('points').innerHTML = JSON.stringify(event.feature.getGeometry().getCoordinates());
	});

	map.addInteraction(lineDraw);
</script>

代码如下，关键代码还是放在最后：
```html
<div style="background-color: #999;"><span>当前绘制线的坐标：</span><span id='points'></span></div>
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

	// 添加一个绘制的线使用的layer
	var lineLayer = new ol.layer.Vector({
		source: new ol.source.Vector(),
		style: new ol.style.Style({
			stroke: new ol.style.Stroke({
				color: 'red',
				size: 1
			})
		})
	})
	map.addLayer(lineLayer);
	var lineDraw = new ol.interaction.Draw({
		type: 'LineString',
		source: lineLayer.getSource(),	// 注意设置source，这样绘制好的线，就会添加到这个source里
		style: new ol.style.Style({			// 设置绘制时的样式
			stroke: new ol.style.Stroke({
				color: '#009933',
				size: 1
			})
		}),
		maxPoints: 4	// 限制不超过4个点
	});

	// 监听线绘制结束事件，获取坐标
	lineDraw.on('drawend', function(event){
		// event.feature 就是当前绘制完成的线的Feature
		document.getElementById('points').innerHTML = JSON.stringify(event.feature.getGeometry().getCoordinates());
	});

	map.addInteraction(lineDraw);
</script>
```

如果需要取消当前绘制，直接用`map.removeInteraction(lineDraw)`就可以了。


# 总结

## 交互实现流程

从地图浏览的交互类，到`feature`选取的类，再到绘制图形的类，都是从事件入手，在引擎内部，提供了一个事件传递的通道，支持通过`ol.Map`添加`interaction`的方式，接收事件消息，然后再根据业务进行处理。 

## 封装的优点和缺点
毫无疑问，已经内置实现的功能，将极大地提高系统可用性，减少开发者的负担。 但内置的这些通用功能，如果是简单的封装，并不能满足大多数的需要，但是过多的封装，将增大复杂性，比如很多`interaction`类都有很多的参数设置，必然增加学习和使用成本，这就是缺点。

对于一般的开发者而言，没有能力自己重新定制开发交互方式，那么最好是学会内置类的应用，虽然有学习成本，但能保证功能没有问题。对于高阶开发者而言，可选择性就很多了，如果效率不行或者流程太复杂，就自己开发。

