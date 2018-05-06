---
layout: post
title: Sử dụng subdomain và test trên localhost với Rails
tags: rails subdomain
summary: Config subdomain trong Rails, test RSpec và test thử trên localhost
---

Mục đích chuyển từ `your_app.com/api` sang `api.your_app.com`.

### 1. Config
Sửa file `config/routes.rb` :
```
constraints subdomain: "api" do
  scope module: "api" do
	resources :posts
  end
end
```

### 2. RSpec
Để test được những controller sử dụng qua subdomain, bạn cần sửa lại host trước khi RSpec tạo request test.
Ví dụ :
```
...
host! "api.your_app.com"
get posts_path
...
```

### 3. Test trên localhost
Để test subdomain trên localhost có nhiều cách, nhưng cá nhân mình thích sửa lại file `/etc/hosts` trên máy.
Ví dụ :
```
127.0.0.1 your_app.test
127.0.0.1 api.your_app.test
```
Sau đó bạn có thể sử dụng `api.your_app.test:3000` để gọi api.

Lưu ý :
+ Đuôi domain bạn có thể đặt tuỳ ý, tuy nhiên tránh những đuôi như `.dev` ra, vì domain này đã được sử dụng thật bên ngoài, nên các trình duyệt sẽ tự chuyển sang giao thức `https` dẫn tới bạn không sử dụng được. Tốt nhất là cứ dùng `.test` hoặc `.localhost` cho an toàn.
+ Nếu vẫn không sử dụng được, bạn có thể thêm dòng sau vào file `config/environments/development.rb`  và restart lại server.
	```
	config.action_dispatch.tld_length
	```
