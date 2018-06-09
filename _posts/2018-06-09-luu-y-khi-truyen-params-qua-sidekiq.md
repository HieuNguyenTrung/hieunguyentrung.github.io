---
layout: post
title: Lưu ý khi truyền params khi sử dụng Sidekiq
tags: rails sidekiq
summary: Một vấn đề khá cơ bản khi truyền params khi sử dụng sidekiq nhưng dễ mắc phải
---

### 1. Đặt vấn đề
Giả sử bạn có một method như sau :
```
def test_method params
  puts params[:title]
end
```

```
test_method title: 'Hello'
=> Hello
```

Và đưa nó vào 1 worker
```
class TestWorker
  include Sidekiq::Worker

  def perform
    test_method params
  end
end
```

Chạy thử
```
# Chạy ngay lập tức
TestWorker.new.perform title: 'Hello'
=> Hello

# Chạy bất đồng bộ
TestWorker.perform_async title: 'Hello'
=> 
```
Như bạn có thể thấy, nếu chạy bất đồng bộ, output sẽ không ra được `Hello` mặc dù chạy ngay lập tức thì vẫn bình thường.

### 2. Nguyên nhân
Thêm một chút log vào, bạn sẽ dễ nhận ra vấn đề
```
class TestWorker
  include Sidekiq::Worker

  def perform
    logger.info params
    test_method params
  end
end
```

```
# Chạy ngay lập tức
TestWorker.new.perform title: 'Hello'
=> {title: 'Hello'}
=> Hello

# Chạy bất đồng bộ
TestWorker.perform_async title: 'Hello'
=> {"title"=> "Hello"}
=> 
```

Về phần ý do tại sao params lại biến đổi như vậy, theo cá nhân mình, do khi sử dụng bất đồng bộ, sidekiq đã chuyển hoá job trên thành string để lưu vào redis, đồng nghĩa với việc symbol key trong hash cũng bị chuyển sang string để lưu trữ, vậy nên khi parse trở lại để thực thi, params của chúng ta đã bị biến đổi hoàn toàn.

### 3. Khắc phục
Có nhiều cách khắc phục, tuy nhiên tiện nhất cho trường hợp này, ta có thể biến đổi params hash của chúng ta trở về với symbol key với method `.symbolize_keys`

```
hash = {"name" => "John"}
=> {"name"=>"John"}
hash.symbolize_keys
=> {:name=>"John"}
```
