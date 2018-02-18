---
title: OpenLayers 3 图标及提示信息
date: 2018-01-18 20:29:59
tags: openLayers
categories: openLayers
---

# 图标及提示信息

图标是GIS应用中必不可少的要素，比如在地图上标注饭店，学校，加油站等，就需要添加图标，点击图标，可能需要提示更为详细的信息，比如地址，评价，或者更为复杂的业务信息。本节将从基本的应用入手讲解，直到比较高级一些的自定义特色图标和信息展示。

<!-- more -->

# 应用overlay

在OpenLayer3中添加图标有两种方式，一种是我们这一小节马上就要介绍的，比较传统的`overlay`，另一种是下一小节马上就要介绍的`Feature` + `Style`的方式。 `overlay`之所以传统，是因为它就是传统的`html`方式显示图片。 下面就是用这种方式加载一个锚点的示例：

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map" style="width: 100%"></div>
<div id="anchor"><img src="../img/anchor.png" alt="示例锚点"/></div>
<script type="text/javascript">
  var map = new ol.Map({
    layers: [
      new ol.layer.Tile({
        source: new ol.source.OSM()
      })
    ],
    target: 'map',
    view: new ol.View({
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor = new ol.Overlay({
    element: document.getElementById('anchor')
  });
  anchor.setPosition([104, 30]);
  map.addOverlay(anchor);
</script>

代码如下：
```html
<div id="map" style="width: 100%"></div>
<!--下面就是传统的显示一个图片图标的方式，用img-->
<div id="anchor"><img src="../img/anchor.png" alt="示例锚点"/></div>
<script type="text/javascript">
  var map = new ol.Map({
    layers: [
      new ol.layer.Tile({
        source: new ol.source.OSM()
      })
    ],
    target: 'map',
    view: new ol.View({
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  // 下面把上面的图标附加到地图上，需要一个ol.Overlay
  var anchor = new ol.Overlay({
    element: document.getElementById('anchor')
  });
  // 关键的一点，需要设置附加到地图上的位置
  anchor.setPosition([104, 30]);
  // 然后添加到map上
  map.addOverlay(anchor);
</script>
```
代码的说明参加注释，最终效果是在地图中间有一个锚点图标。 

## 缺点
当图标比较多的情况下，如果采用这种方式，那么我们会加入非常多的HTML元素，从而造成效率降低。 关于效率的测试，大家可以自行测试。 为什么会这样呢？ 因为界面上元素的遍历在数量比较多的情况下，会变慢，基于此基础上的渲染，鼠标事件都会变慢。

## 优点
这种使用传统的方式显示图标可以应用传统的HTML技术，比如鼠标移动到图标上，鼠标图标变成手势。 我们可以用css来处理就可以了，比如在`head`里面添加下面的代码：

<style type="text/css">
	#anchor {
		cursor:pointer;
	}
</style>

```
<style type="text/css">
	#anchor {
		cursor:pointer;
	}
</style>
```
就可以看到鼠标放到锚点上去的时候，鼠标图标从箭头，变成手了。 类似的其他技术都可以应用上去，比如css动画。 鉴于动画在前端的重要性，下面单独分出一个小节用实例来讲解。

# 动画图标

动起来的图标会更有吸引力，下面用`overlay`+`css`的方式来实现：

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
	<style type="text/css">
	  @keyframes zoom
	  {
	    from {top: 0; left: 0; width: 32px; height: 32px;}
	    50% {top: -16px; left: -16px; width: 64px; height: 64px;}
	    to {top: 0; left: 0; width: 32px; height: 32px;}
	  }

	  @-moz-keyframes zoom /* Firefox */
	  {
	    from {top: 0; left: 0; width: 32px; height: 32px;}
	    50% {top: -16px; left: -16px; width: 64px; height: 64px;}
	    to {top: 0; left: 0; width: 32px; height: 32px;}
	  }

	  @-webkit-keyframes zoom /* Safari 和 Chrome */
	  {
	    from {top: 0; left: 0; width: 32px; height: 32px;}
	    50% {top: -16px; left: -16px; width: 64px; height: 64px;}
	    to {top: 0; left: 0; width: 32px; height: 32px;}
	  }

	  @-o-keyframes zoom /* Opera */
	  {
	    from {top: 0; left: 0; width: 32px; height: 32px;}
	    50% {top: -16px; left: -16px; width: 64px; height: 64px;}
	    to {top: 0; left: 0; width: 32px; height: 32px;}
	  }

	  #anchorImg
	  {
	    display: block;
	    position: absolute;
	    animation: zoom 5s;
	    animation-iteration-count: infinite;
	    -moz-animation: zoom 5s; /* Firefox */
	    -moz-animation-iteration-count: infinite;
	    -webkit-animation: zoom 5s;  /* Safari 和 Chrome */
	    -webkit-animation-iteration-count: infinite;
	    -o-animation: zoom 5s; /* Opera */
	    -o-animation-iteration-count: infinite;
	  }

	</style>
</head>

<div id="map" style="width: 100%"></div>
<div id="anchor" style="width: 64px;height: 64px;" ><img id='anchorImg' src="../img/anchor.png" alt="示例锚点"/></div>
<script type="text/javascript">
  var map = new ol.Map({
    layers: [
      new ol.layer.Tile({
        source: new ol.source.OSM()
      })
    ],
    target: 'map',
    view: new ol.View({
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor = new ol.Overlay({
    element: document.getElementById('anchor')
  });
  anchor.setPosition([104, 30]);
  map.addOverlay(anchor);
</script>

代码和之前的例子差不多，只是多了css动画的设置：
```html
<head>
	<!--定义动画，图标先放大，再缩小-->
	<style type="text/css">
	  @keyframes zoom
	  {
	    from {top: 0; left: 0; width: 32px; height: 32px;}
	    50% {top: -16px; left: -16px; width: 64px; height: 64px;}
	    to {top: 0; left: 0; width: 32px; height: 32px;}
	  }

	  @-moz-keyframes zoom /* Firefox */
	  {
	    from {top: 0; left: 0; width: 32px; height: 32px;}
	    50% {top: -16px; left: -16px; width: 64px; height: 64px;}
	    to {top: 0; left: 0; width: 32px; height: 32px;}
	  }

	  @-webkit-keyframes zoom /* Safari 和 Chrome */
	  {
	    from {top: 0; left: 0; width: 32px; height: 32px;}
	    50% {top: -16px; left: -16px; width: 64px; height: 64px;}
	    to {top: 0; left: 0; width: 32px; height: 32px;}
	  }

	  @-o-keyframes zoom /* Opera */
	  {
	    from {top: 0; left: 0; width: 32px; height: 32px;}
	    50% {top: -16px; left: -16px; width: 64px; height: 64px;}
	    to {top: 0; left: 0; width: 32px; height: 32px;}
	  }

	  /* 应用css动画到图标元素上 */
	  #anchorImg
	  {
	    display: block;
	    position: absolute;
	    animation: zoom 5s;
	    animation-iteration-count: infinite; /* 一直重复动画 */
	    -moz-animation: zoom 5s; /* Firefox */
	    -moz-animation-iteration-count: infinite; /* 一直重复动画 */
	    -webkit-animation: zoom 5s;  /* Safari 和 Chrome */
	    -webkit-animation-iteration-count: infinite; /* 一直重复动画 */
	    -o-animation: zoom 5s; /* Opera */
	    -o-animation-iteration-count: infinite; /* 一直重复动画 */
	  }
	</style>
</head>

<div id="map" style="width: 100%"></div>
<div id="anchor" style="width: 64px;height: 64px;" ><img id='anchorImg' src="../img/anchor.png" alt="示例锚点"/></div>
<script type="text/javascript">
  var map = new ol.Map({
    layers: [
      new ol.layer.Tile({
        source: new ol.source.OSM()
      })
    ],
    target: 'map',
    view: new ol.View({
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor = new ol.Overlay({
    element: document.getElementById('anchor')
  });
  anchor.setPosition([104, 30]);
  map.addOverlay(anchor);
</script>

```
除了这种css实现动画之外，你还可以直接加载gif动画，这是非常简单的，再此不表。


# 设置图标位置

如果像之前那样设置图标，不做任何位置设置，那么默认情况下，图标的中心点对应于地图位置。下面这个地图显示了这个位置，中心那个红点所处位置就是[104, 30]，可以这个点对应于图标的中心位置： 

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol.js" charset="utf-8"></script>
</head>
<div id="map2" style="width: 100%"></div>
<script type="text/javascript">
  var layer2 = new ol.layer.Vector({
    source: new ol.source.Vector()
  })
  var map2 = new ol.Map({
    layers: [
      new ol.layer.Tile({
        source: new ol.source.OSM()
      }), 
      layer2
    ],
    target: 'map2',
    view: new ol.View({
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor2 = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  anchor2.setStyle(new ol.style.Style({
    image: new ol.style.Icon({
      src: '../img/anchor.png'
    })
  }));
  layer2.getSource().addFeature(anchor2);

  var refFeature = new ol.Feature({
  	geometry: new ol.geom.Point([104, 30])
  })
  refFeature.setStyle(new ol.style.Style({
  	image: new ol.style.Circle({
  		radius: 2,
  		fill: new ol.style.Fill({
  			color: 'red'
  		})
  	})
  }));

  layer2.getSource().addFeature(refFeature);
</script>

有时候我们可能并不想这样，比如我们希望锚点图标下方的箭头指向地图位置。 比如这样：
<div id="map3" style="width: 100%"></div>
<script type="text/javascript">
  var layer3 = new ol.layer.Vector({
    source: new ol.source.Vector()
  })
  var map3 = new ol.Map({
    layers: [
      new ol.layer.Tile({
        source: new ol.source.OSM()
      }), 
      layer3
    ],
    target: 'map3',
    view: new ol.View({
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor3 = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  anchor3.setStyle(new ol.style.Style({
    image: new ol.style.Icon({
      src: '../img/anchor.png',
      anchor: [0.5, 1]
    })
  }));
  layer3.getSource().addFeature(anchor3);

  var refFeature3 = new ol.Feature({
  	geometry: new ol.geom.Point([104, 30])
  })
  refFeature3.setStyle(new ol.style.Style({
  	image: new ol.style.Circle({
  		radius: 2,
  		fill: new ol.style.Fill({
  			color: 'red'
  		})
  	})
  }));

  layer3.getSource().addFeature(refFeature3);
</script>

要做到这个效果，我们只需要把设置样式的代码加上`anchor`的设置：

```javascript
image: new ol.style.Icon({
  src: '../img/anchor.png',
  anchor: [0.5, 1]	// 设置图标位置
})
```
为什么是[0.5, 1]这种值，表示什么？ 默认情况下，位置坐标是按照比例的方式来设置的，范围从0到1，x轴上0表示最左边，1表示最右边，y轴上0表示最上边，1表示最下边。 如代码所示，x设置为0.5可以让图片在x方向上居中，y设置为1可以让图片在y方向上移动到最底端。 大家可以给予上面这个代码修改一下，试试[0, 0]会让图标处于什么位置？

除了按照比例进行移动之外，还可以按照像素来计算位置，但必须显示设置`anchorXUnits`或	`anchorYUnits`为`pixels`。 根据不同的需要，可以采用不同的单位来设置。

# 根据层级放大缩小图标

由于图标不会跟随图层的放大缩小而放大缩小，所以在某些业务应用中，可能并不合适，需要也跟随变化。 之前就有同学提到这个问题，在`ol.style.Icon`中是可以设置`scale`的，这样就为
我们提供了方便。 通过设置它，就可以做到。 下面这个地图中的锚点图标，就会随着地图放大缩小而变化大小：

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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  anchor.setStyle(new ol.style.Style({
    image: new ol.style.Icon({
      src: '../img/anchor.png'
    })
  }));
  layer.getSource().addFeature(anchor);

  map.getView().on('change:resolution', function(){
  	var style = anchor.getStyle();
  	style.getImage().setScale(this.getZoom() / 10);
  	anchor.setStyle(style);
  })
</script>

和之前的代码绝大部分都是相同的：
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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  anchor.setStyle(new ol.style.Style({
    image: new ol.style.Icon({
      src: '../img/anchor.png'
    })
  }));
  layer.getSource().addFeature(anchor);

  // 监听地图层级变化
  map.getView().on('change:resolution', function(){
  	var style = anchor.getStyle();
  	// 重新设置图标的缩放率，基于层级10来做缩放
  	style.getImage().setScale(this.getZoom() / 10);
  	anchor.setStyle(style);
  })
</script>
```
利用一个监听和`scale`改变，就实现了这个同比缩放。 具体缩放多少，请根据业务来设置，可以设置的更加精细，此处只是功能示例。 其实还有另外一种方式，可以实现动态缩放大小，参见[styleFunction应用](07-04.md)。

# 另类设置svg图标

图标除了可以直接设置png的文件url之外，也可以设置svg的文件url，但这并不是唯一的加载svg图标的方式。 OpenLayers 3提供了直接使用图像对象来设置的方式，对应于`ol.style.Icon`构造函数中的`img`参数。 如下：

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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });

  // 构建svg的Image对象
  var svg = '<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" width="30px" height="30px" viewBox="0 0 30 30" enable-background="new 0 0 30 30" xml:space="preserve">'+    
'<path fill="#156BB1" d="M22.906,10.438c0,4.367-6.281,14.312-7.906,17.031c-1.719-2.75-7.906-12.665-7.906-17.031S10.634,2.531,15,2.531S22.906,6.071,22.906,10.438z"/>'+
'<circle fill="#FFFFFF" cx="15" cy="10.677" r="3.291"/></svg>';

	var mysvg = new Image();
	mysvg.src = 'data:image/svg+xml,' + escape(svg);

  anchor.setStyle(new ol.style.Style({
    image: new ol.style.Icon({
      img: mysvg,	// 设置Image对象
      imgSize: [30, 30]	// 及图标大小
//          src: 'http://www.williambuck.com/portals/0/Skins/WilliamBuck2014/images/location-icon.svg',
//          size: [30, 30]
    })
  }));
  layer.getSource().addFeature(anchor);
</script>

对应的代码：
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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });

  // 构建svg的Image对象
  var svg = '<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" width="30px" height="30px" viewBox="0 0 30 30" enable-background="new 0 0 30 30" xml:space="preserve">'+    
'<path fill="#156BB1" d="M22.906,10.438c0,4.367-6.281,14.312-7.906,17.031c-1.719-2.75-7.906-12.665-7.906-17.031S10.634,2.531,15,2.531S22.906,6.071,22.906,10.438z"/>'+
'<circle fill="#FFFFFF" cx="15" cy="10.677" r="3.291"/></svg>';

	var mysvg = new Image();
	mysvg.src = 'data:image/svg+xml,' + escape(svg);

  anchor.setStyle(new ol.style.Style({
    image: new ol.style.Icon({
      img: mysvg,	// 设置Image对象
      imgSize: [30, 30]	// 及图标大小
//          src: 'http://www.williambuck.com/portals/0/Skins/WilliamBuck2014/images/location-icon.svg',
//          size: [30, 30]
    })
  }));
  layer.getSource().addFeature(anchor);
</script>
```
前半部分代码都一样，最后设置样式的时候，可以根据注释来理解不一样的代码，构建`Image`对象，设置`src`是关键。



# 规则几何体图标

相对于png而言，svg这样的矢量图在放大缩小方面更清晰，但对于规则几何体而言，如果也使用svg，未免复杂了一点，OpenLayers 3为了简化这样的操作，提供了一个规则几何体的样式类`ol.style.RegularShape`，使用它可以轻松绘制正方形，三角形等,也支持星形规则几何图形，比如下面这样：

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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var shape = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  shape.setStyle(new ol.style.Style({
    image: new ol.style.RegularShape({
      points: 3,
      radius: 10,
      stroke: new ol.style.Stroke({
      	color: 'red',
      	size: 2
      })
    })
  }));

  layer.getSource().addFeature(shape);

	var star = new ol.Feature({
    geometry: new ol.geom.Point([104.1, 30.1])
  });
  star.setStyle(new ol.style.Style({
    image: new ol.style.RegularShape({
      points: 5,
      radius1: 20,
      radius2: 10,
      stroke: new ol.style.Stroke({
      	color: 'red',
      	size: 2
      }),
      fill: new ol.style.Fill({ // 设置五星填充样式
      	color: 'blue'
      })
    })
  }));

  layer.getSource().addFeature(star);

</script>

图上有一个三角形，一个5星，代码如下：
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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  // 添加一个三角形
  var shape = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  shape.setStyle(new ol.style.Style({
    image: new ol.style.RegularShape({
      points: 3,	// 顶点数
      radius: 10,	// 图形大小，单位为像素
      stroke: new ol.style.Stroke({ // 设置边的样式
      	color: 'red',
      	size: 2
      })
    })
  }));

  layer.getSource().addFeature(shape);

  // 添加一个五星
	var star = new ol.Feature({
    geometry: new ol.geom.Point([104.1, 30.1])
  });
  star.setStyle(new ol.style.Style({
    image: new ol.style.RegularShape({
      points: 5,	// 顶点个数
      radius1: 20, // 外圈大小
      radius2: 10, // 内圈大小
      stroke: new ol.style.Stroke({ // 设置边的样式
      	color: 'red',
      	size: 2
      }),
      fill: new ol.style.Fill({ // 设置五星填充样式
      	color: 'blue'
      })
    })
  }));

  layer.getSource().addFeature(star);

</script>
```
除了基本的设置之外，还支持图形旋转，以及跟随地图旋转而旋转，这些设置在其他的应用中也多有涉及，此处不再用实例来介绍，可自行验证。

# 用Canvas自绘图标

除了规则的几何体之外，往往需要定义一些不规则的几何体，可以使用svg来实现，但也可以用`canvas`自己来绘制，比如像下面这个图标：

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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  // 使用canvas绘制一个不规则几何图形
	var canvas =document.createElement('canvas');
	canvas.width = 20;
  canvas.height = 20;
	var context = canvas.getContext("2d");
	context.strokeStyle = "red";  
  context.lineWidth = 1;  
  context.beginPath();   
  context.moveTo(0, 0);
  context.lineTo(20, 10);
  context.lineTo(0, 20);
  context.lineTo(10, 10);
  context.lineTo(0, 0);  
  context.stroke();

  // 把绘制了的canvas设置到style里面
	var style = new ol.style.Style({
		image: new ol.style.Icon({
		  img: canvas,
		  imgSize: [canvas.width, canvas.height],
		  rotation: 90 * Math.PI / 180
		})
	});

	// 创建一个Feature
  var shape = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });

  // 应用具有不规则几何图形的样式到Feature
  shape.setStyle(style);
  layer.getSource().addFeature(shape);
</script>

代码如下：
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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  // 使用canvas绘制一个不规则几何图形
	var canvas =document.createElement('canvas');
	canvas.width = 20;
  canvas.height = 20;
	var context = canvas.getContext("2d");
	context.strokeStyle = "red";  
  context.lineWidth = 1;  
  context.beginPath();   
  context.moveTo(0, 0);
  context.lineTo(20, 10);
  context.lineTo(0, 20);
  context.lineTo(10, 10);
  context.lineTo(0, 0);  
  context.stroke();

  // 把绘制了的canvas设置到style里面
	var style = new ol.style.Style({
		image: new ol.style.Icon({
		  img: canvas,
		  imgSize: [canvas.width, canvas.height],
		  rotation: 90 * Math.PI / 180
		})
	});

	// 创建一个Feature
  var shape = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });

  // 应用具有不规则几何图形的样式到Feature
  shape.setStyle(style);
  layer.getSource().addFeature(shape);
</script>
```
和svg的那个例子一样，使用了`ol.style.Icon`样式的`img`属性来设置，需要注意的是，必须设置`imgSize`属性，因为仅仅通过`img`设置的图像对象，没有办法自动获取宽高。 同时，官网也提供了一个类似的例子[earthquake-custom-symbol](http://openlayers.org/en/v3.13.1/examples/earthquake-custom-symbol.html)，只是使用OpenLayers3 内部提供的封装库来绘制图像到`canvas`上，原理一样。

有了这种方式之后，相信做任何图标都不会遇到难题了。

# 动态改变图标

在实际业务应用中，需要根据环境条件，动态的修改图标样式，以反馈数据变化。 比如像下面这样，点击五星，五星图标会用红色填充：


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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

	var star = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  star.setStyle(new ol.style.Style({
    image: new ol.style.RegularShape({
      points: 5,
      radius1: 20,
      radius2: 10,
      stroke: new ol.style.Stroke({
      	color: 'red',
      	size: 2
      })
    })
  }));

  layer.getSource().addFeature(star);

  map.on('click', function(event){
  	var feature = map.forEachFeatureAtPixel(event.pixel, function(feature){
  		return feature;
  	});
  	if (feature) {
  		var style = feature.getStyle().getImage();
  		feature.setStyle(new ol.style.Style({
		    image: new ol.style.RegularShape({
		      points: 5,
	  			radius1: 20,
	  			radius2: 10,
	  			stroke: style.getStroke(),
	  			fill: new ol.style.Fill({
		  			color: 'red'
		  		})
		    })
		  }));
  	}
  });

</script>

对应的代码如下：
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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  // 添加一个空心的五星
	var star = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  star.setStyle(new ol.style.Style({
    image: new ol.style.RegularShape({
      points: 5,
      radius1: 20,
      radius2: 10,
      stroke: new ol.style.Stroke({
      	color: 'red',
      	size: 2
      })
    })
  }));

  layer.getSource().addFeature(star);

  // 监听地图点击事件
  map.on('click', function(event){
  	var feature = map.forEachFeatureAtPixel(event.pixel, function(feature){
  		return feature;
  	});
  	if (feature) {
  		// 将空心五星为红色实心五星
  		var style = feature.getStyle().getImage();
  		feature.setStyle(new ol.style.Style({
		    image: new ol.style.RegularShape({
		      points: 5,
	  			radius1: 20,
	  			radius2: 10,
	  			stroke: style.getStroke(),
	  			fill: new ol.style.Fill({
		  			color: 'red'
		  		})
		    })
		  }));
  	}
  });

</script>
```
# 文字标注
前面基本都在围绕着图标进行说明，其实用`Feature` + `Style`的方式，也是可以单独添加文字的，虽然简单，但可能有些同学会忽略这样的做法，所以在此用一个简单的示例来说明：

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
      projection: 'EPSG:4326',
      center: [104.06, 30.67],
      zoom: 10
    })
  });

  var anchor = new ol.Feature({
    geometry: new ol.geom.Point([104.06, 30.67])
  });
  anchor.setStyle(new ol.style.Style({
    text: new ol.style.Text({
      text: '淡叔所在地成都',
      fill: new ol.style.Fill({
      	color: 'red'
      })
    })
  }));
  layer.getSource().addFeature(anchor);
</script>

代码如下：

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
      projection: 'EPSG:4326',
      center: [104.06, 30.67],
      zoom: 10
    })
  });

  var anchor = new ol.Feature({
    geometry: new ol.geom.Point([104.06, 30.67])
  });
  // 设置文字style
  anchor.setStyle(new ol.style.Style({
    text: new ol.style.Text({
      // font: '10px sans-serif' 默认这个字体，可以修改成其他的，格式和css的字体设置一样
      text: '淡叔所在地成都',
      fill: new ol.style.Fill({
      	color: 'red'
      })
    })
  }));
  layer.getSource().addFeature(anchor);
</script>
```
还有很多属性可以设置，比如缩放，旋转，以及位移等等，非常简单，可自行设置调试。

# style及应用

前面已经介绍了第一种加载图标的方式，现在介绍第二种方式，使用`Feature` + `Style`来实现，用这种方式实现之前的效果如下：

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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  anchor.setStyle(new ol.style.Style({
    image: new ol.style.Icon({
      src: '../img/anchor.png'
    })
  }));
  layer.getSource().addFeature(anchor);
</script>

代码如下：
```html
<div id="map" style="width: 100%"></div>
<script type="text/javascript">
  // 我们需要一个vector的layer来放置图标
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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  // 创建一个Feature，并设置好在地图上的位置
  var anchor = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  // 设置样式，在样式中就可以设置图标
  anchor.setStyle(new ol.style.Style({
    image: new ol.style.Icon({
      src: '../img/anchor.png'
    })
  }));
  // 添加到之前的创建的layer中去
  layer.getSource().addFeature(anchor);
</script>
```
效果是一样的，但从代码上来看，是不一样的:

* 首先`overlay`需要HTML元素`img`，但这种方式不需要
* `overlay`是添加在`map`上的，但是这种方式需要一个`Vector`的layer，并添加在其上
* 我们没有办法像`overlay`那样使用一些HTML技术

## 应用

虽然不能用css直接修改图标显示，但并不是说使用这种方式没有自定义的余地，大家可以先在官网API上看一下`ol.style.Icon`的构造参数，会看到可以设置位置，透明度，放大缩小，旋转等，基本能满足大多数的应用，由于和`CSS`不同，很多同学在应用时遇到一些问题，所以下面给出了一些具体的使用示例。

# styleFunction应用
很多时候，我们会忽略`styleFunction`的存在，但很明显的，它可以让我们的图标或者标签应用更加灵活，比如[根据层级放大缩小图标](07-03-02.md)也可以用`styleFunction`来实现：

<head>                  
	<link href="../src/ol3.13.1/ol.css" rel="stylesheet" type="text/css" />
	<script type="text/javascript" src="../src/ol3.13.1/ol-debug.js" charset="utf-8"></script>
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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  anchor.setStyle(function(resolution){
  	return [new ol.style.Style({
		  image: new ol.style.Icon({
		    src: '../img/anchor.png',
		    scale: map.getView().getZoom() / 10
		  })
		})];
  });

  layer.getSource().addFeature(anchor);
</script>

代码如下：
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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var anchor = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  // 应用style function，动态的获取样式
  anchor.setStyle(function(resolution){
  	return [new ol.style.Style({
		  image: new ol.style.Icon({
		    src: '../img/anchor.png',
		    scale: map.getView().getZoom() / 10
		  })
		})];
  });

  layer.getSource().addFeature(anchor);
</script>
```
对比一下前面同样功能的代码，你会发现这样更加的简单， 同时在此基础上扩展开来的应用也会更加的多。 比如动态替换图标，或者让图标不显示等等，可自行来实现这两个需求，以掌握此方式的使用。 

在上面这个例子中，我们是在`feature`上应用了`styleFunction`，通过官网API文档可以看到，其类型为`ol.FeatureStyleFunction`，函数仅带有一个参数`resolution`，在上面的代码中看到了，在函数体内`this`指的是当前的`feature`，根据文档说明，这个函数要范围一个`style`数组。 这一点需要注意，虽然实际使用中，即使没有返回数组也不会出错，但还是希望大家能遵守官网API的说明来使用该接口。

我们知道，除了`feature`可以设置样式之外，`layer`也是可以设置样式的，同样地也支持`styleFunction`，但是需要注意的是，其定义和`feature`的不一样，类型为`ol.style.StyleFunction`，该函数具有两个参数，第一个参数为`feature`，第二个参数为`resolution`，同样地，该函数需要返回`style`数组。 

`styleFunction`在`feature`上具有很好的灵活性，那么应用在`layer`上，同样威力无穷，比如像下面这个：

<div id="map2" style="width: 100%"></div>
<script type="text/javascript">
  var layerStyleFunction = function(feature, resolution) {
    var type = feature.get('type');
    var style = null;
    if (type === 'point') {
      style = new ol.style.Style({
        image: new ol.style.Circle({
          radius: 1,
          fill: new ol.style.Fill({
            color: 'red'
          })
        })
      });
    } else if ( type === 'circle') {
      style = new ol.style.Style({
        image: new ol.style.Circle({
          radius: 10,
          stroke: new ol.style.Stroke({
            color: 'red',
            size: 1
          })
        })
      });
    } else {
      style = new ol.style.Style({
        image: new ol.style.RegularShape({
          points: 5,
          radius: 10,
          fill: new ol.style.Fill({
            color: 'blue'
          })
        })
      });
    }

    return [style];
  };

  var layer2 = new ol.layer.Vector({
    source: new ol.source.Vector(),
    style: layerStyleFunction
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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  var rect = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  layer2.getSource().addFeature(rect);

  var circle = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  circle.set('type', 'circle');
  layer2.getSource().addFeature(circle);

  var point = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  point.set('type', 'point');
  layer2.getSource().addFeature(point);

</script>

在地图上可以看到中心位置有一个圆，一个点，一个五边形，但这次都没有直接在这些`feature`上设置样式，具体的代码如下：

```html
<div id="map2" style="width: 100%"></div>
<script type="text/javascript">

  // 创建layer使用的style function，根据feature的自定义type，返回不同的样式
  var layerStyleFunction = function(feature, resolution) {
    var type = feature.get('type');
    var style = null;
    // 点
    if (type === 'point') {
      style = new ol.style.Style({
        image: new ol.style.Circle({
          radius: 1,
          fill: new ol.style.Fill({
            color: 'red'
          })
        })
      });
    } else if ( type === 'circle') { // 圆形
      style = new ol.style.Style({
        image: new ol.style.Circle({
          radius: 10,
          stroke: new ol.style.Stroke({
            color: 'red',
            size: 1
          })
        })
      });
    } else { // 其他形状
      style = new ol.style.Style({
        image: new ol.style.RegularShape({
          points: 5,
          radius: 10,
          fill: new ol.style.Fill({
            color: 'blue'
          })
        })
      });
    }

    // 返回 style 数组
    return [style];
  };

  var layer2 = new ol.layer.Vector({
    source: new ol.source.Vector(),
    style: layerStyleFunction // 应用上面创建的 style function
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
      projection: 'EPSG:4326',
      center: [104, 30],
      zoom: 10
    })
  });

  // 添加三个feature，并设置自定义属性 type
  var rect = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  layer2.getSource().addFeature(rect);

  var circle = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  circle.set('type', 'circle');
  layer2.getSource().addFeature(circle);

  var point = new ol.Feature({
    geometry: new ol.geom.Point([104, 30])
  });
  point.set('type', 'point');
  layer2.getSource().addFeature(point);

</script>
```
这就是一个典型的根据`feature`的属性进行不同渲染的例子，可以在业务上无限扩展，比如`feature`的属性可以是速度，可以是大小，可以是时间，可以是权重等等。 由此可见，只要掌握了这个方法，前端按照条件渲染就不再困难。


# 大量图标方案
此处的大量图标方案，不涉及服务器端，如果图标不进行交互，可以把图标渲染到底图上。 此处只介绍说明在前端可交互的大量图标方案，在图标数量不大的情况，无论使用什么方式加载，都不会有性能问题，当图标多了之后，就会出现卡顿，内存占用增大等问题。 在OpenLayers 3开发中，可以考虑下面两个方案来解决这个问题。

## 复用样式减少内存占用
在应用大量图标的时候，其实图标样式差异化并不大，比如快餐店，公共厕所，公交站点等等有很多，但都是用同样的图标在地图上标准，在不注意的时候，我们是采用下面的方式来添加图标的：

```javascript
for (var index = 0; index < 10000; index++) {
	var feature = new ol.Feature({
		geometry: new ol.geom.Point([latlon[index].lon, latlon[index].lat])
	});
	feature.setStyle(new ol.style.Style({
		image: new ol.style.Icon({
			src: '../img/marker.png'
		})
	}));
}
```
注意上面代码，对每个`feature`设置`style`的时候，都是直接`new`的，这样势必会创建很多对象，占用很多内存。 那么复用必然减少很多内存，重构上面的代码为：

```javascript
var style = new ol.style.Style({
	image: new ol.style.Icon({
		src: '../img/marker.png'
	})
});
for (var index = 0; index < 10000; index++) {
	var feature = new ol.Feature({
		geometry: new ol.geom.Point([latlon[index].lon, latlon[index].lat])
	});
	feature.setStyle(style);
}
```

这样，我们就只创建了一个`style`对象，那么势必减少内存占用。 如果有多类图标，可以用数组缓存下来：

```javascript
var styles = [
	new ol.style.Style({
		image: new ol.style.Icon({
			src: '../img/marker1.png'
		})
	}),
	new ol.style.Style({
		image: new ol.style.Icon({
			src: '../img/marker2.png'
		})
	}),
	new ol.style.Style({
		image: new ol.style.Icon({
			src: '../img/marker3.png'
		})
	})
];


for (var index = 0; index < 10000; index++) {
	var feature = new ol.Feature({
		geometry: new ol.geom.Point([latlon[index].lon, latlon[index].lat])
	});
	feature.setStyle(styles[index % styles.length]);
}

```
由于官网有实际的例子，大家请移步到[icon-sprite-webgl](http://openlayers.org/en/v3.13.1/examples/icon-sprite-webgl.html)。 下面是其中的一些代码片段，在里面加入了一些注释，便于大家理解：

```javascript
// 预先设置好要使用的style，并缓存在icons数组中
for (i = 0; i < iconCount; ++i) {
  var info = iconInfo[i];
  icons[i] = new ol.style.Icon({
    offset: info.offset,
    opacity: info.opacity,
    rotateWithView: info.rotateWithView,
    rotation: info.rotation,
    scale: info.scale,
    size: info.size,
    src: 'data/Butterfly.png'
  });
}

......

for (i = 0; i < featureCount; ++i) {
  geometry = new ol.geom.Point(
      [2 * e * Math.random() - e, 2 * e * Math.random() - e]);
  feature = new ol.Feature(geometry);
  feature.setStyle(
      new ol.style.Style({
      	// 直接使用上面缓存的icons里面的样式
        image: icons[i % (iconCount - 1)]
      })
  );
  features[i] = feature;
}

```

大家可在官网例子的基础上修改一下代码，验证一下复用和不复用的情况下，内存占用相差多少。

## 复用Canvas提高效率
采用上一种方式基本能解决掉绝大部分的问题，但是OpenLayers 3还提供了一种复用图标渲染使用的`Canvas`的方式，对应的类是`ol.style.AtlasManager`。 在了解其作用之前，需要先了解一点图标的渲染机制，比如`ol.style.Circle`和`ol.style.RegularShape`这样的图标，在内部渲染时，都会创建一个HTML的`canvas`，然后在这个画布上绘制图像，然后再把图像复制到地图上。 这样创建一个图标，就会在内部创建一个`canvas`。  `ol.style.AtlasManager`解决的问题就是，用一个大的`canvas`来绘制多个图标，这样就能减少`canvas`的数量，从而提高效率。 

官网有一个具体的例子来说明这种方法的使用，参见[Symbols with WebGL](http://openlayers.org/en/v3.13.1/examples/symbol-atlas-webgl.html)。 其中，关键的代码在：

```javascript
var atlasManager = new ol.style.AtlasManager({
  // we increase the initial size so that all symbols fit into
  // a single atlas image
  initialSize: 512
});

......

					// circle symbol
          symbols.push(new ol.style.Circle({
            opacity: info.opacity,
            scale: info.scale,
            radius: radiuses[j],
            fill: new ol.style.Fill({
              color: info.fillColor
            }),
            stroke: new ol.style.Stroke({
              color: info.strokeColor,
              width: 1
            }),
            // by passing the atlas manager to the symbol,
            // the symbol will be added to an atlas
            atlasManager: atlasManager  // 注意：在创建style的这个地方设置了 atlasManager
          }));
```

需要注意的是，在API官方文档上，并没有这个属性的设置，但内部实现是有这个优化的。 同时需要注意的是经常使用的`ol.style.Icon`目前是没有实现这个优化的。

# 提示信息

提示信息在很多业务场景中都需要，比如显示当前位置周边的饭店列表，或者点击饭店，显示饭店详细信息，交通路线，电话号码等等。 鉴于显示的业务信息比较多，所以通常的做法都是采用`overlay`的方式来做。 用传统的HTML来布局和排版信息，然后附加到地图上的指定位置就可以了。 官网中提供了一个具体的例子： [popup](http://openlayers.org/en/v3.13.1/examples/popup.html)。 下面就解读一下这个例子的代码：

```html

<!--此处用html布局，各种样式均在css中定义好了-->
<div id="popup" class="ol-popup">
  <a href="#" id="popup-closer" class="ol-popup-closer"></a>
  <div id="popup-content"></div>
</div>

......

<script>

  // 获取到popup的节点
  var container = document.getElementById('popup');
  var content = document.getElementById('popup-content');
  var closer = document.getElementById('popup-closer');

  // 创建一个overlay, 绑定html元素container
  var overlay = new ol.Overlay(/** @type {olx.OverlayOptions} */ ({
    element: container,
    autoPan: true,
    autoPanAnimation: {
      duration: 250
    }
  }));

  ......

  // 监听地图点击事件
  map.on('singleclick', function(evt) {
  	// 获取当前点击坐标，并设置到HTML元素上去
    var coordinate = evt.coordinate;
    var hdms = ol.coordinate.toStringHDMS(ol.proj.transform(
        coordinate, 'EPSG:3857', 'EPSG:4326'));

    content.innerHTML = '<p>You clicked here:</p><code>' + hdms +
        '</code>';
    // 设置overlay的位置，从而显示在鼠标点击处
    overlay.setPosition(coordinate);
  });

</script>

```