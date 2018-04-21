---
layout: post
title: Tản mạn về case...when trong Ruby
tags: ruby case
summary: Tìm hiểu thêm về case when trong Ruby, bản chất hoạt động và cách tận dụng nó theo cách riêng
---

Tản mạn về `case ... when`,  bản chất hoạt động ra sao và tận dụng nó theo cách riêng ra sao ?

### Thông thường
#### Loại đầy đủ
```
case grade
when "A"
	p "Good"
when "B"
	p "Good"
when "C"
	p "Ok"
else
	p "Try harder"
end
```
#### Khuyết giá trị `case`
```
case
when weather == "rain"
	p "I love that"
when bank_amount > 99999999999999999999999999999999999
	p "I'm fk** rich"
when number_of_wife > 10
	p "Enough, I don't want anymore"
else
	p "Hmmm
end
```

#### Dạng multiple-value `when`
```
case grade
when "A", "B"
	p "Good"
when "C"
	p "Ok"
else
	p "Try harder"
end
```
### Bắt đầu lạ mắt
#### Dạng dùng `regex`
```
case "hello"
when /ello/
	p "Oh hi"
when /\d*/
	p "I am sorry"
end
```

####  Loại này ít gặp hơn
```
object = Staff.new ....

case object
when Customer
	p "Hello customer"
when Staff
	p "Hello staff"
else
	p "Hello"
end
```

```
case 4
when 1..5
	p "Low"
when 6..10
	p "Good"
else
	p "Hmmm..."
end
```

### Đặt vấn đề
Nhìn nhận 2 ví dụ cuối, chắc hẳn `case...when` không dùng phép so sánh `==` bình thường được.
Bản chất các object trong ruby đều được kế thừa method `===` https://ruby-doc.org/core-2.4.2/Object.html#method-i-3D-3D-3D
`case...when` sẽ sử dụng `===` của bản thân object đó để thực hiện match kết quả. Đơn giản hơn có thể hình dung ví dụ đầu tiên sẽ được hiểu thành :
```
case grade
if "A" === grade
	p "Good"
elsif "B" === grade
	p "Good"
elsif "C" === grade
	p "Ok"
else
	p "Try harder"
end
```
### `===` mang ý nghĩa gì ?
`a === b` là sao ?. Hiểu nôm na là "Coi như a là một set, thì b liệu có phải là 1 member trong đó không". Ví dụ :
```
(1..5) === 2 #true
(1..5) === 3 #true
(1..5) === 6 #false
Integer === 100 #true
Integer === "hello" #false
/ello/ === "hello" #true
```

=> Vậy bạn đã biết `case...when` hoạt  theo bằng cách sử dụng `===` để kiểm tra các điều kiện, nên chắc hẳn đã giải đáp được thắc mắc sao những ví dụ trên hoạt động được.

### Tận dụng theo cách riêng
Với việc viết lại nội dung  method `===` cho object, ta hoàn toàn có thể sử dụng `case...when` để có thể kiểm tra object theo cách riêng.
```
class SuccessRequest
	def self.=== response
		200 <= response.status < 300
	end
end

case http_response
when SuccessRequest
	p "Nice"
...
```
