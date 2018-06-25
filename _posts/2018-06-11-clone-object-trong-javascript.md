---
layout: post
title: Mẹo nhỏ clone object phức tạp trong JavaScript
tags: javascript reference
summary: Việc gán giá trị là một object phức tạp trong JavaScript nếu không cẩn thận sẽ gây ra những bug tiềm tàng khó phát hiện
---

### 1. Đặt vấn đề
Việc gán dữ liệu dạng nguyên thuỷ thì không có gì cần bàn, nhưng khi gán dữ liệu là object thì sẽ xảy ra `pass by reference`, cái này nếu chưa rõ có thể Google tìm hiểu thêm. Hiện trạng sẽ như sau
```
let x1 = {'name': 'John'};
let x2 = x1;
x2['name'] = 'Peter';

console.log(x2);
=> {name: 'Peter'}
console.log(x1);
=> {name: 'Peter'}
```

Thường thì với những object đơn giản một cấp thế này, bạn có thể sử dụng `Object.assign`. Ví dụ như
```
let x1 = {'name': 'John'};
let x2 = Object.assign({}, x1);
x2['name'] = 'Peter';

console.log(x2);
=> {name: 'Peter'}
console.log(x1);
=> {name: 'John'}
```

Tuy nhiên, nếu gán object phức tạp thì `Object.assign` không đỡ được. Ví dụ như
```
let x1 = {
    'odd': [1, 3, 5],
    'even': [0, 2, 4]
}

let x2 = Object.assign({}, x1);
x2['odd'].push(7);

console.log(x1);
=>
{
    'odd': [1, 3, 5, 7],
    'even': [0, 2, 4]
}
```

### Giải pháp
Đơn giản nhất bạn có thể biến đổi object phức tạp đó sang JSON, sau đó parse lại để chuyển JSON đó thành một object hoàn toàn mới.
```
let x1 = {
    'odd': [1, 3, 5],
    'even': [0, 2, 4]
}

let x2 = JSON.parse(JSON.stringify(x1));
```
Lưu lý nhỏ là cách này chỉ sử dụng khi giá trị của các key có kiểu nguyên thuỷ hoặc những object đơn giản như mảng hay hash. Nếu giá trị key là object phức tạp, việc chuyển sang JSON và parse trở lại sẽ đôi phần bị biến dạng kiểu dữ liệu, gây lên lỗi không mong muốn.
