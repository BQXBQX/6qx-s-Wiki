---
title: Untitled Page
description: 
published: true
date: 2024-02-24T08:32:42.359Z
tags: css, frontend, position
editor: markdown
dateCreated: 2024-02-24T08:31:23.976Z
---

# 粘性定位元素

## 介绍
sticky：对象在常态时遵循常规流。它就像是relative和fixed的合体，当在屏幕中时按常规流排版，当卷动到屏幕外时则表现如fixed。该属性的表现是现实中你见到的吸附效果。

## 使用场景
常用场景：当元素距离页面视口（Viewport，也就是fixed定位的参照）顶部距离大于 0px 时，元素以 relative 定位表现，而当元素距离页面视口小于 0px 时，元素表现为 fixed 定位，也就会固定在顶部。

## 表现形式
距离页面顶部大于20px，表现为 position:relative;
![bv2xut.webp](/public/bv2xut.webp)

距离页面顶部小于20px，表现为 position:fixed;
![bv2xu9.webp](/public/bv2xu9.webp)

## 生效规则
1. 须指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。

2. 并且 top 和 bottom 同时设置时，top 生效的优先级高，left 和 right 同时设置时，left 的优先级高。
3. 设定为 position:sticky 元素的任意父节点的 overflow 属性必须是 visible，否则 position:sticky 不会生效。这里需要解释一下：

4. 如果 position:sticky 元素的任意父节点定位设置为 overflow:hidden，则父容器无法进行滚动，所以 position:sticky 元素也不会有滚动然后固定的情况。
5. 如果 position:sticky 元素的任意父节点定位设置为 position:relative | absolute | fixed，则元素相对父元素进行定位，而不会相对 viewprot 定位。
5. 达到设定的阀值。这个还算好理解，也就是设定了 position:sticky 的元素表现为 relative 还是 fixed 是根据元素是否达到设定了的阈值决定的。