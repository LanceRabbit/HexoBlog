---
layout: post
title: '[Android] EditText using single line'
date: 2017-01-17 15:47
comments: true
categories: Android
tag:
	- UI
	- Single line
	- EditText
---
幾百年沒寫的Blog.  Yoooooo

Note: 
When I implement my app, I want to EditBox only can input single line. 
And I using 
`android:maxLines="1"
`

Magic, it can type multi-line in editbox. 
Actually ~  This xml should add an attribute. 
`android:inputType="text"
`

This way can achieve single line in Editbox. 



BTW: 
**maxLines** is an attribute that user can see lines on your app. 
if maxLines = 5;
Users can input more than 5 lines but only see 5 lines on app. 

