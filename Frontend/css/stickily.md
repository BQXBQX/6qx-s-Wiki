---
title: Untitled Page
description: 
published: true
date: 2024-02-24T08:31:30.648Z
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