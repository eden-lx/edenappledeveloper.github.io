---
title: 用masonry自适应label高度和宽度
date: 2019-03-29 10:14:35
tags:
---


1.自适应宽度

//宽度够时

    [yourLabel setContentHuggingPriority:UILayoutPriorityRequired forAxis:UILayoutConstraintAxisHorizontal];
//宽度不够时

    [yourLabel setContentHuggingPriority:UILayoutPriorityRequired forAxis:UILayoutConstraintAxisHorizontal];


2.label多行自适应高度

    label.preferredMaxLayoutWidth = width;//给一个maxWidth[label

    setContentHuggingPriority:UILayoutPriorityRequired forAxis:UILayoutConstraintAxisVertical];//设置huggingPrioritylabel.numberOfLines = 0;//设置换行
