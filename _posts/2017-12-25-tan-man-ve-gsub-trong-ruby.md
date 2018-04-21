---
layout: post
title: Tản mạn về gsub trong Ruby
tags: ruby gsub
summary: Tìm hiểu thêm về gsub trong Ruby
---

Chúng ta đã khá quen thuộc với method `gsub`, tuy nhiên sức mạnh của nó có thể bạn chưa biết.

Đây là ví dụ một cách sử dụng thông thường
```
"Green Mist".gsub('Green', 'Red')
=> "Red Mist"
```

Tuy nhiên ngoài sử dụng String thuần túy, ta có thể ốp cả regex vào
```
"2 year olds".gsub(/\d/, "3")
=> "3 year olds"
```

Không những dùng được regex để xác định vùng dữ liệu, ta còn có thể dùng cả `matched groups` của regex với `\1 \2 ...`. Ta có bài toán thực tế :

> Move the first letter of each word to the end of it, then add "ay" to the end of the word. Leave punctuation marks untouched.

Ví dụ :
```
pig_it('Pig latin is cool') # igPay atinlay siay oolcay
pig_it('Hello world !')     # elloHay orldWay !
```
Với `gsub`, ta có thể giải rất đơn giản như sau :
```
def pig_it text
  text.gsub(/(\w)(\w+)*/, '\2\1ay')
end
```

Ngoài ra ta còn có thể ốp cả block vào dùng với `gsub`
```
"Are your 10 or 11 year olds ?".gsub(/\d+/) {|i| i.to_i.next}
=> "Are your 11 or 12 year olds ?"
```

Hoặc bạn cũng chẳng cần tới `block param` :
```
'I am 12'.gsub(/(\d+)/) { Regexp.last_match[1].to_i.next }
=> "I am 13"
```
Nói chung `gsub` khá là mạnh, hay đọc thêm tài liệu và sử dụng nó một cách khôn khéo nhé.
https://ruby-doc.org/core-2.1.4/String.html#method-i-gsub
