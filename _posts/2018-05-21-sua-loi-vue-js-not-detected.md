---
layout: post
title: Sửa lỗi Vue.js not detected
tags: vue vue.js
summary: Sửa lỗi extenstion Vue Devtools báo "Vue.js not detected"
---
### 1. Vấn đề
Test Vue.js ngay trên local sử dụng `vue.js` qua CDN, và extenstion Vue Devtools lại báo `Vue.js not detected`
![vue.js not detected](/public/images/posts/20180521-1/vue.js_not_dectected.png)

### 2. Xử lý
Lý do là chưa cấp quyền `Allow access to file URLs` cho extention Vue Devtools.

![allow access to file urls](/public/images/posts/20180521-1/allow_access_to_file_urls.png)
