---
title: 用OpenCV实现“扫描全能王”APP
author: Daisy Gao
date: '2014-02-17 23:31:02'
slug: 'opencv-scanner'
tags: [computer vision]
categories: [Projects]
thumbnailImagePosition: right
thumbnailImage: http://ww4.sinaimg.cn/large/6cea169fjw1edp0oj3fkgj206g0bg74n.jpg
---
本人是[扫描全能王](http://weibo.com/camscanner) app的超级粉丝，自从手机里有了它，给律师传各种材料复印件啥的，再也没去过打印店。因为14号晚上比较闲...就稍微琢磨了下这个应用的原理，用[OpenCV](http://opencv.org/)实现了个精华版，发出来抛砖引玉一下~
<!--more-->

把要扫描的文件放在干净的背景上，用扫描全能王咔咔拍照，然后app会自动识别文件的边缘，把文件从照片上抠下来，并且拉伸为标准的纸张大小，如下图所示。拉伸其实是透视变换(perspective transform)，OpenCV可以直接实现，所以重点在于如何识别文件的边缘。

{{< image classes="fancybox fig-33" src="http://ww2.sinaimg.cn/large/6cea169fjw1edopgxi1vvj21kw2t4e81.jpg" title="原图" class="col-md-4 fancybox" >}}
{{< image classes="fancybox fig-33" src="http://ww1.sinaimg.cn/large/6cea169fjw1edopio31ewj20u01hcdkn.jpg" title="app自动识别文件边缘" >}}
{{< image classes="fancybox fig-33" src="http://ww3.sinaimg.cn/large/6cea169fjw1edopizee6jj20u01hc7ad.jpg" title="裁剪拉伸为A4大小" >}}




按照惯例，首先通过```canny```算子提取图像的边缘。发现书的四边形轮廓很明显嘛！于是从局部特征开始，试着分别提取文件的四个边缘吧！用```HoughsLineP```检测所有的直线，用红色标出来。

{{< image classes="fancybox fig-33" src="http://ww4.sinaimg.cn/large/6cea169fjw1edorhti317j206g0bgdg1.jpg" title="边缘图" >}}
{{< image classes="fancybox fig-33" src="http://ww2.sinaimg.cn/large/6cea169fjw1edorj6u529j206g0bgq39.jpg" title="检测出的直线" >}}
{{< image classes="fancybox fig-33 clear" src="http://img.qzone.la/uploads/allimg/121007/co12100G30619-86.png" title="效果不错嘛" >}}
<p></p>

换一张杂志封面试试~（暴露了博主年轻时也曾是一位骚年...）

{{< image classes="fancybox fig-25" src="http://ww4.sinaimg.cn/large/6cea169fjw1edorp77tezj20u01hch0q.jpg" title="原图" >}}
{{< image classes="fancybox fig-25" src="http://ww2.sinaimg.cn/large/6cea169fjw1edorppz76ij205k09vwev.jpg" title="边缘图" >}}
{{< image classes="fancybox fig-25" src="http://ww3.sinaimg.cn/large/6cea169fjw1edorqgib7mj205k09vgm1.jpg" title="检测出的直线" >}}
{{< image classes="fancybox fig-25 clear" src="http://img.qzone.la/uploads/allimg/121007/co12100G30619-95.png" title="有点杂但好像可以忍..." >}}


来加入一张终极挑战，从微博抓下来的最近很火的南锣鼓巷拉面店菜单~
{{< image classes="fancybox fig-25" src="http://ww3.sinaimg.cn/large/6cea169fjw1edorxi2nqqj20dc0hs414.jpg" title="原图" >}}
{{< image classes="fancybox fig-25" src="http://ww4.sinaimg.cn/large/6cea169fjw1edorxrxncwj205k07ewf3.jpg" title="边缘图" >}}
{{< image classes="fancybox fig-25" src="http://ww2.sinaimg.cn/large/6cea169fjw1edory24zmjj205k07emxo.jpg" title="检测出的直线" >}}
{{< image classes="fancybox fig-25 clear" src="http://img.qzone.la/uploads/allimg/121007/co12100G30619-85.png" title="这...是在逗我？！" >}}

不过虽然线段凌乱，但三幅图中文件的上下左右四条边都被检测了出来。所以只需要在所有横向线段中找出最上和最下，纵向线段中找出最左和最后的即可。每条线段取中点，横向线段按中点```y```坐标排序，第一条线段即为文件的上边界，最后一条线段即为下边界。同理，纵向线段按中点```x```坐标排序，即可得到文件的左右两边。检测结果如下。绿色代表横向的线段，蓝色代表纵向线段，加粗的为边框。

{{< image classes="fancybox fig-25 nocaption" src="http://ww4.sinaimg.cn/large/6cea169fjw1edp0oj3fkgj206g0bg74n.jpg"  >}}
{{< image classes="fancybox fig-25 nocaption" src="http://ww4.sinaimg.cn/large/6cea169fjw1edp0phm31cj205k09vjru.jpg"  >}}
{{< image classes="fancybox fig-25 nocaption" src="http://ww4.sinaimg.cn/large/6cea169fjw1edp0p51cs6j205k07eq3o.jpg"  >}}
{{< image classes="fancybox fig-25 clear" src="http://img.qzone.la/uploads/allimg/121007/co12100G30619-71.png" title="四条边都找到啦" >}}

现在对四条直线两两求交点，得到文件的四个角，用来进行下一步的透视变换。边界情况是，如果有某条边没有被检测出来，我们用相应方位的图片边缘代替，这样就确保了始终可以得到四个角点。结果如下图所示，角点和边框都用浅蓝色表示。可以看到文件或多或少出现了变形，为了把它拉伸到比如A4这种标准的长方形，我们把刚刚找到的四个角对应到A4纸大小矩形的四个角上，用```getPerspectiveTransform```计算转化矩阵，再用```warpPerspective```调用转化矩阵进行拉伸。

{{< image classes="fancybox fig-25 nocaption" src="http://ww4.sinaimg.cn/large/6cea169fjw1edp18013x3j206g0bg3yy.jpg"  >}}
{{< image classes="fancybox fig-25 nocaption" src="http://ww4.sinaimg.cn/large/6cea169fjw1edp18h25glj205k09v74s.jpg"  >}}
{{< image classes="fancybox fig-25 nocaption" src="http://ww4.sinaimg.cn/large/6cea169fjw1edp19pl0prj205k07emxo.jpg"  >}}
{{< image classes="fancybox fig-25 clear" src="http://img.qzone.la/uploads/allimg/121007/co12100G30619-49.png"  title="文件的四个角都找到啦" >}}


{{< image classes="fancybox fig-25" src="http://ww4.sinaimg.cn/large/6cea169fjw1edp1nxxprpj219y1sz7pl.jpg"  title="断舍离图" >}}
{{< image classes="fancybox fig-25" src="http://ww2.sinaimg.cn/large/6cea169fjw1edp1ola1ctj219y1sz1en.jpg"  title="萌芽图" >}}
{{< image classes="fancybox fig-25" src="http://ww1.sinaimg.cn/large/6cea169fjw1edp1ovkpiyj219y1szkbp.jpg"  title="菜单图" >}}
{{< image classes="fancybox fig-25 clear" src="http://img.qzone.la/uploads/allimg/121007/co12100G30619-86.png"  title="透视变换结果" >}}

下图是扫描全能王的识别效果。整体很不错，不过可以发现菜单图的上边框检测有些偏颇呢，我猜是因为用```canny```算边缘的时候参数没有调好，把对比度没有那么强的边也检测了出来。我这里用了Otsu的算法计算了相关的阈值，可以智能的过滤掉背景的干扰。所以同样的函数，不同的参数，对效果也是有直接的影响。希望app的程序员gg不要被老板打pp噢~


{{< image classes="fancybox fig-33" src="http://ww4.sinaimg.cn/large/6cea169fjw1edopio31ewj20u01hcdkn.jpg"  title="断舍离图" >}}
{{< image classes="fancybox fig-33" src="http://ww2.sinaimg.cn/large/6cea169fjw1edp29wuvw3j20u01hcth2.jpg"  title="萌芽图" >}}
{{< image classes="fancybox fig-33 clear" src="http://ww1.sinaimg.cn/large/6cea169fjw1edp2aqt9ixj20u01hcth3.jpg"  title="菜单图" >}}


通过以上的操作就可以把照片里的文件转化成像扫描出来一样的标准样子了。除此之外，全能扫描王还加入了用户交互来手动调节识别出来的边框，以使结果更加准确。还可以对扫描结果进行亮度色彩的处理，最后还能把图片转成pdf等等。也许这款app的技术原理没有搜索引擎那么多模型，没有深度学习那样的计算量，没有推荐系统的大数据，但是为现有方法创造性的找到了应用场景，并且做的非常实用。有人认为黑客就是要用技术改变世界；然而，像这样创造东西让人们的生活变得更容易，则是我理解的黑客之道。


[1]: https://opencv-code.com/tutorials/detecting-simple-shapes-in-an-image/

## 源代码
- [ScannerLite (C++)](https://github.com/daisygao/ScannerLite)
- [ScannerLite (Python)](https://gist.github.com/scturtle/9052852)

[scturtle](https://github.com/scturtle)同学在我上传代码到Github后第二天就搞出来了Python的版本！喜欢Python的同学可以去这里围观。


## 参考文献
1. [Detecting simple shapes in an image][1]
2. [Automatic perspective correction for quadrilateral objects](https://opencv-code.com/tutorials/automatic-perspective-correction-for-quadrilateral-objects/)