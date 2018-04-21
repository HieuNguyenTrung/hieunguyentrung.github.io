---
layout: post
title: Hiểu thêm về proc và lambda trong Ruby
tags: ruby block proc lambda
summary: Hiểu thêm về proc và lambda trong Ruby
---

Khái niệm `proc` và `lambda` rất phổ biến khi lập trình Ruby, vậy chúng là gì, và chúng khác gì nhau ?

## Proc và Lambda là gì ?
Thử khởi tạo 2 object, và xem xét chúng :
```ruby
new_proc = proc {}
> #<Proc:0x007f993f99ac98@(pry):4>
new_lambda = lambda {}
> #<Proc:0x007f993f93bf40@(pry):5 (lambda)>
```
Trước mắc ta có thể thấy cả hai điều thuộc class `Proc`, và điểm khác biệt nhận thấy, cũng là thứ giúp ta phân biệt chúng đó là `lambda` có thêm đoạn `{lambda}`. Đơn giản hơn, ta có thể check bằng method `lambda?`

```ruby
new_proc.lambda?
> false
new_lambda.lambda?
> true
```
Vậy ta có thể coi `lambda` là một dạng đặc biệt của `Proc`.

Vậy chúng ta dùng chúng làm gì ? Trước hết, block trong Ruby không phải là object ( chi tiết về cái này sẽ nói trong bài viết khác ), vậy nên để sử dụng block trong một method nào đó, chúng ta cần gói gọn nó lại trong instance của class `Proc`.
## Khởi tạo và dùng Proc object thế nào ?
Sinh ra Proc object có khá là nhiều cách :
  + Proc.new

  ```ruby
  proc_object = Proc.new {puts "Hello"}
  proc_object.call
  > Hello
  ```

  + proc

  ```ruby
  proc_object = proc {puts "Hello"}
  proc_object.call
  > Hello
  ```

 + lambda

  ```ruby
  proc_object = lambda {puts "Hello"}
  proc_object.call
  > Hello
  ```

  + Đây không hẳn là cách, nhưng khi sử dụng chung ta đã ngấm ngầm tạo ra chúng.
Như chúng ta đã nói trong bài viết trước, các method đều ngấm ngầm nhận block vào như một tham số ẩn, khi đó sinh ra Proc object để wrap lại block của ta lại.

  ```ruby
  def method1
    yield
  end
  method1 {puts "Hello"}
  > Hello

  def method2 &block
    puts block.class
    block.call
  end
  method2 {puts "Hello"}
  > Proc
  > Hello
  ```

Nói thêm chút về Proc object, chúng sẽ có method `call` dùng để thực thi code trong block, theo sau sẽ là danh sách tham số ta muốn truyền vào block. Ví dụ :

```ruby
proc = proc {|name| puts "Hello #{name}"}
proc.call "Hieu"
> Hello Hieu
```

Tuy nhiên `call` không phải là cách duy nhất là thực thi code, ta có thể sử dụng các cách dị dị như sau :

```ruby
proc {|name| puts "Hello #{name}"}.("Hieu")
> Hello Hieu

proc = proc {|name| puts "Hello #{name}"}
proc["Hieu"]
> Hello Hieu
```

Mấy kiểu gọi là khá là dị, nên tốt nhất bạn nên dùng `call` tránh trường hợp người khác đọc code lại không hiểu bạn đang viết cái gì.
## lambda có gì khác với proc ?
Để rõ hơn, hãy xem 2 ví dụ sau :

```ruby
def method1
  puts "Before proc"
  new_proc = proc do
    puts "Inside proc"
    return
  end
  new_proc.call
  puts "After proc"
end
method1
```
```
Before proc
Inside proc
```
```ruby
def method2
  puts "Before lambda"
  new_lambda = lambda do
    puts "Inside lambda"
    return
  end
  new_lambda.call
  puts "After lambda"
end
method2
```
```
Before lambda
Inside lambda
After lambda
```

Dễ dàng nhận thấy `return` trong `proc` có tác dụng vượt ra khỏi phạm vi của block code, và các tác dụng ra bên ngoài, dẫn tới `method1` bị `return` mà chưa chạy tới đoạn `puts "After proc"`, trong khi đó bên `method2` chứa `lambda` thì ngược lại, `return` không hề có tác động gì ra bên ngoài.

Tóm lại sự khác nhau giữa `proc` và `lambda` là cách đối xử của chúng với control flow keywords, ví dụ như `return`, `break` ...

## Kết luận
Tổng kết lại, ta có thể nhận ra rằng `proc` và `lambda` đều thuộc class `Proc`, trong đó `lambda` là một dạng đặc biệt, sự khác nhau giữa chúng trong quá trình sử dụng là cách chúng điều phối các control flow keywords, có thể nói một cách nôm na là `proc` sẽ thoải mái còn `lambda` sẽ nghiêm túc với control flow keywords.
