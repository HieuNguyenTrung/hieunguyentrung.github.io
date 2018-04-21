---
layout: post
title: Tản mạn về thread-based và event-based
tags: thread-based event-based
summary: Tìm hiểu và so sánh thread-based và event-based
---
Tại sao khi nói tới việc xử lý lượng request lớn, người ta thường nhắc tới Node.js, hay cụ thể hơn là đang băn khoăn về khái niệm `event-driven programming`, `non-blocking`...

### Tình huống thực tế
Bạn đi làm thủ tục làm lại sim thẻ điện thoại, theo lý thuyết có những bước sau :
+ Bạn nêu nhu cầu làm lại sim và nhận phiếu điền thông tin từ nhân viên.
+ Bạn điền họ tên, các thông tin cơ bản, mở điển thoại kiểm tra 5 số liên lạc gần nhất để điền vào phiếu.
+ Bạn nộp lại phiếu thông tin cho nhân viên.
+ Nhân viên kiểm tra nếu bị sai sót sẽ yêu cầu bạn làm lại, nếu không có vấn đề gì, họ sẽ nhập thông tin của bạn lên hệ thống.
+ Nhân viên kích hoạt sim mới cho bạn.
+ Bạn nộp tiền phí và nhận sim mới.

### Thread-based
Nếu theo phương thức `thread-based`, với một yêu cầu làm lại sim của bạn, sẽ có một nhân viên phục vụ bạn, làm tất tần tật các bước ở trên. Hãy hình dung một điểm giao dịch của nhà mạng có khoảng 5 nhân viên, nghĩa là tại một thời điểm, sẽ chỉ có 5 khách hàng được phục vụ làm lại sim, những người còn lại sẽ phải ngồi chơi xơi nước đợi cho tới khi nhân viên nào đó hoàn tất thủ tục cho người khách trước đó. Nếu có tay khách hàng nào đang bận mà phải ngồi chờ, họ sẽ dễ dàng nhận ra được có quá nhiều khoảng thời gian lãng phí với mô hình trên.
Thằng KH1 đang được NV1 phục vụ, tới đoạn điền thông tin cá nhân vào phiếu, NV1 sẽ ngồi chơi đợi thằng KH1 lọ mọ nắn nón từng nét chữ điền vào phiếu, điền xong, anh ấy tiếp túc hì hụi xem lịch sử cuộc gọi tra cứu 5 số liên lạc gần nhất, NV1 bắt đầu chán nản chuyển qua dantri.com.vn đọc tạm vài tin tức chờ thằng KH1. May quá, sau khoảng 10 phút, khi đã lướt hết dantri.com.vn thì thằng KH1 cũng đã điền xong, NV1 tiếp nhận và bắt  đầu xác thực thông tin, chuyện tới đây chưa hết, NV1 phát hiện ra thằng KH1 điền sai số CMND, thế là lại phải bắt KH1 điền lại ...
Thôi dài quá, khỏi ví dụ nữa, tới đây ai cũng thấy có quá nhiều thời gian bị lãng phí.

### Event-based
Với mô hình này thì thú vị hơn nhiều, hãy hình dung từ bước thằng KH1 nhận phiếu điền thông tin mới từ NV1, NV1 lúc này sẽ không ngồi đợi thằng KH1 điền thông tin nữa, mà yêu cầu thằng KH1 ra bàn trống ngồi điền, và bản thân NV1 sẽ tiếp nhận yêu cầu và phát phiếu cho KH2 ...
Sau khi khi KH1 điền xong, họ sẽ xếp lại vào hàng đợi và chờ tới lượt mình nộp lại phiếu. Tới lúc KH1 tới lượt tiếp theo, NV1 sẽ nhận đơn của KH1 để xác nhận thông tin, nếu sai sót sẽ bắt thằng KH1 ra bàn trống điền lại, rồi lại xếp lại vào hàng và đợi tới lượt mình, nếu may mắn thông tin chuẩn, KH1 sẽ chuyển sang bàn tiếp theo gặp NV2, ở đây NV2 sẽ lấy phôi sim mới và kích hoạt sim cho KH1 và chuyển sang cho NV3, NV3 ở đây sẽ làm nhiệm vụ thanh toán, nhận và hối lại tiền thừa.
Dễ dàng nhận thấy các nhân viên phải làm việc liên tục và mô hình này tối giản được thời gian lãng phí cũng như tăng số lượng khách hàng được phục vụ lên đáng kể.

Nghe thì có thể thấy đang bóc lột nhân viên, nhưng điều đó là cần thiết, điều đó càng đúng hơn khi áp dụng vào lập trình.
Ví dụ đơn giản là một hệ thống nhận request của người dùng, sau đó thực hiện đọc dữ liệu để trả về cho người dùng, với event-based, sau khi nhận request của người dùng và bắt đầu xử lý dữ liệu, ta sẽ không để thread đó ngồi đợi việc xử lý dữ liệu một cách lãng phí ( cái này có thể gọi là blocking I/O ), thay vì đó nó sẽ được dùng để nhận request của người dùng khác, nói cách khác các request của người dùng không hề block lẫn nhau. Sau khi việc xử lý dữ liệu hoàn tất, nó sẽ quay trở lại hàng đợi, chờ tới lượt mình để trả lại cho người dùng. Với cách thức hoạt động này, các request sẽ được xử lý một cách tối đa mà không mất thời gian đợi các tác vụ đọc ghi của request xử lý trước. ( non-blocking I/O ).

### Kết luận
Lý thuyết là vậy, việc Node.js nổi tiếng trong việc xử lý lượng request một phần lớn là vì nó khá dễ trong việc sử dụng event-based. Không phải các ngôn ngữ khác không hỗ trợ event-based, nhưng ta có thể thấy các function trong JavaScript liên qua tới đọc ghi hầu hết là bất đồng bộ, callback, rất thích hợp để sử dụng event-based. Tất nhiên đó không phải điều duy nhất làm lên tên tuổi của Node.js, ta còn phải kể tới nhiều cái như JavaScript là ngôn ngữ rất phổ biến, hay việc nó được hậu thuẫn trên Google Chrome's JavaScript V8 Engine.
