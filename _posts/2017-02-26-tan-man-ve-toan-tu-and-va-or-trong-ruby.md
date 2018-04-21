---
layout: post
title: Tản mạn về toán tử "and" và "or" trong Ruby
tags: ruby
summary: Tản mạn về toán tử "and" và "or" trong Ruby
---

Trong Ruby, ta đều biết tới các cặp toán tử `and` và `&&`, `||` và `or`.  Về ý nghĩa chúng không khác gì nhau, nhưng về cách sử dụng thì lại có sự khác biết khá rõ ràng.

Đầu tiên phải nói là `&&` và `||` được sử dụng khả phổ biến, lấn át hẳn so với `and` và `or`. Ngay trong Ruby style guide cũng có nhắc tới việc không sử dụng `and` và `or` https://github.com/bbatsov/ruby-style-guide#no-and-or-or

Cá nhân mình thì vẫn thấy dùng `and` và `or` giúp code dễ đọc hơn khá nhiều. Trước khi đưa ra ý kiến cá nhân, phải nói tới sự khác biệt giữa chúng.

### `and` và `&&`, `or` và `||` không sử dụng tương đương nhau, hay thay thế nhau trong mọi trường hợp.

Ví dụ đơn giản nhất để thấy sự khác biệt khi sử dụng chúng như sau :

```ruby
#1
a1 = true and false

#2
a2 = true && false
```
```
#1 a1 = true
#2 a2 = false
```

Thêm ví dụ nữa :

```ruby
a3 = 10 and a3 / 2
=> 5

a4 = 10 && a4 / 2
NoMethodError: undefined method `/' for nil:NilClass
```

Qua sự khác nhau trên, ta cũng biết được chúng khác nhau là do **thứ tự ưu tiên** của các toán tử. Bạn có thể tham khảo tại đây :
http://phrogz.net/ProgrammingRuby/language.html#table_18.4

### Có nên dùng `and` và `or` ?
Như trong repo về Ruby style trên có nhắc tới

> The `and` and `or` keywords are banned.

Tuy nhiên cá nhân mình thấy sử dụng chúng vẫn khá hay, nhất là đọc code dễ hiểu hơn. Họ lưu ý không dùng có lẽ là sợ anh em lẫn lộn thứ tự ưu tiên các toán tử, gây ra sai logic trong code. Dùng `and` và `or` hay ở chỗ nào ? Xem xét ví dụ sau :

```ruby
def do_something
	current_user = User.find_by ...
	...
end
```

Trong trường hợp `current_user == nil`, ta sẽ có những hướng sau :

```ruby
# Option 1
current_user = User.find_by ...
return unless current_user

# Option 2
current_user = User.find_by ...
if current_user
	...
end

# Option ...

# Option n
current_user = User.find_by or return
```

Bạn thấy sao ? Cá nhân mình thấy cách cuối cùng `Option n` là đẹp mắt và dễ đọc nhất. Hơn nữa ở cách này ta cũng không thể thay `or` bằng `||` do thứ tự ưu tiên toán tử của chúng khác nhau. (`||` > `=` > `or`).

Nếu bạn đồng ý là dùng `and` và `or` sẽ cho ra những dòng code dễ đọc hơn thì cũng cần hiểu nó yêu cần ta phải nắm rõ thứ tự ưu tiên các toán tử, tránh dùng lộn xộn lại gây ra lỗi.
