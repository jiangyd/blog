---
title: Other element would receive the click
date: 2018-10-27 16:42:13
tags:
	- selenium
	- webdriver
---

在调试ui自动化脚本的时候，点击某元素报错 ，报错的意思是 某个a链接在坐标(701,728)是不能点击的，有一个其它的div元素会接收这个点击


```
unknown error: Element <a class="btn btn-primary ng-scope" ng-show="step < stepC
ount - 1 &amp;&amp; pkgInfo.prodPkgName !== 'market'" ng-click="toNextStep()" ng
-disabled="form.$invalid || submitting" ng-class="{'disabled': (form.$invalid ||
 submitting)}" translate="apply.NEXT_STEP" tabindex="0" aria-hidden="false" aria
-disabled="false" style="">...</a> is not clickable at point (701, 728). Other e
lement would receive the click: <div ng-show="app.isStruct &amp;&amp; app.copyri
ght" class="app-footer wrapper b-t bg-light ng-scope" aria-hidden="false">...</d
iv>
running chrome
Error: An unknown server-side error occurred while processing the command.
```

既然有其它元素会收到这个点击，那么就要找到是什么元素，报错信息给出了详细的html,我们通过这个简单的html去把它找出来

```
<div ng-show="app.isStruct &amp;&amp; app.copyri
ght" class="app-footer wrapper b-t bg-light ng-scope" aria-hidden="false">
```

{% asset_img other.jpg %}


看下图，我其实是想点"下一步",但是这个按钮被下面的footer挡住了，如果不把滚动条滚到最下面就点不了

{% asset_img gif.gif %}


想一想怎么解决,我目前有几个思路

1. 通过执行js脚本(webdriverio提供了该方法)给div增加隐藏样式,这样它就没发挡住我要点击的按钮了

```
browser.execute('var ele=document.getElementsByClassName("app-footer wrapper b-t bg-light ng-scope");ele[0].setAttribute("style","display:none");')
```

2. 操作滚动条到底部(当前页面无法操作滚动条，原因待确认，此办法用不了)

```
window.scrollTo(0,document.body.scrollHeight)
```

但是这个document.body.scrollTop，在当前页面可以用, 滚动到距离顶部100像素的位置(设置垂直滚动条位置)

```
document.body.scrollTop=100
```

3. 我们最终的目的是点击那个按钮，那么我们可以直接通过js直接点击a链接 (通过$定位元素，有ID直接传ID，没有ID传css selector也行)

```
browser.execute('$("#apply-content > div > div > div > div > div > step.ng-scope.ng-isolate-scope.ng-hide > div:nth-child(2) > step-content > div > form > div.step-content-footer > div > div > a:nth-child(2)")[0].click()')
```

4. 通过scollIntoView,滚动文档。使该元素出现在窗口的顶部或底部。

```


browser.execute('$("#apply-content > div > div > div > div > div > step:nth-child(4) > div:nth-child(2) > step-content.step-content-region.ng-scope.ng-isolate-scope > div > form > div.step-content-footer > div > div > a:nth-child(2)")[0].scrollIntoView()')
```