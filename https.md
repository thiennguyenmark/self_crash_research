`Tìm Hiểu Về Chữ S Trong Https`

Đặt vấn đề

Đi luôn vào chủ đề, khi mới bắt đầu với môn mạng máy tính ở trường đại học, sự mập mờ khi còn ngồi trên ghế nhà trường khiến chúng ta đôi khi đã nhầm lẫn về http và https, nhớ ngày đó tôi đã từng nghĩ chúng là 2 giao thức khác nhau, à mà ngày đó tôi có hiểu giao thức là gì không nhỉ :fearful: thôi dẹp qua một bên, bản chất của https cũng là giao thức http nhưng thêm được chữ s ứng với secure. Nghe thôi đã thấy bảo mật rồi, https ra đời nhằm mục đích làm cho giao thức http trở nên an toàn hơn, vậy nó làm cho http trở nên an toàn hơn như thế nào?

Luận bàn

`1. Http hiểu đơn giản thì hoạt động thế nào?`

Mục đích cuối cùng của mớ hỗn độn rối rắm này cũng là truyền nhận thông tin, tôi có một website tôi muốn gửi nó lên mạng internet để bạn, một ai đó mà tôi không quen có thể vô tình lướt qua đọc được.
Http là một giao thức, hiểu đơn giản là một bộ quy tắc để chung để người sử dụng dùng trình duyệt có thể truy cập tới 1 trang web, và trang web đó hiểu được yêu cầu đó rồi trả về nội dung mà trình duyệt có thể hiểu được, ví như có nhiều ngôn ngữ trên thế giới, vậy giữa 2 người có 2 tiếng nói khác nhau để giao tiếp được họ phải cùng nhau sử dụng một bộ ngôn ngữ chung mà cả hai cùng hiểu được.

Một đặc điểm cơ bản của http là nó truyền nhận thông tin dưới dạng text thông thường, không hề mã hoã, điều này dẫn tới vấn đề đó là thông tin được truyền nhận nhanh chóng, nhẹ nhàng, nội dung rất thân thiện với người dùng (vì nó là text mà).

Mọi thứ có vẻ good? không hẳn như vậy, việc dữ liệu truyền gửi được lưu dưới dạng text khiến nó quá thân thiện với người dùng, mà người dùng thì không phải ai cũng tốt :smirk: . Có những người dùng mà mục đích họ tới với website của bạn là để phá hoại, họ sẽ dễ dàng tấn công bằng cách nào đó lấy được request của bạn rồi đọc được nội dung trong request đó 1 cách dễ dàng.

Bài học: Không phải lúc nào cũng nên quá thân thiện với người dùng cuối :worried:

`2. Https khắc phục vấn đề của http như thế nào?`

Chúng ta đã nắm được khái quát vấn đề bảo mật gặp phải khi sử dụng http. Với những website giải trí thuần tuý với nội dung chủ yếu là hình ảnh, âm thanh và không có thông tin cần bảo mật thì sử dụng https hình như cũng chỉ để tốt cho SEO, ngoài ra cũng không còn giá trị gì. Nhưng nếu bạn sử dụng Internet Banking với http, rồi bạn đăng nhập với tài khoản ngân hàng của mình trên đó, mọi thông tin bạn gửi đi đều dưới dạng những gói tin có nội dung plain/text trần trụi, vậy điều gì bảo vệ được tài khoản của bạn nếu request kể trên bị những kẻ xấu dùng những thủ thuật hay phần mềm chuyên dụng để bắt được?

Https giải bài toán trên bằng cách rất đơn giản, nội dung dạng plain/text giúp kẻ gian có thể bắt và đọc dễ dàng, vậy chúng ta hãy mã hoá nội dung để kẻ gian đó không thể hiểu được, và có bắt được gói tin cũng vô dụng?.

`3. Https và vấn đề mã hoá dữ liệu`

Trước tiên phải làm rõ là:

https = http + ssl

Trong đó:

http là giao thức tryền tải.

ssl là cơ chế bảo mật thông tin.

tam the tu tin

Flow tối thiểu của https sẽ như sau:

Client A, người dùng sử dụng trình duyệt sẽ request tới google.com.

Server B, request ở bước một đã tới nơi, server sẽ gửi trả client 2 thông tin quan trọng đó là ssl certificate (Để chứng minh “Ê, tao là google.com thật nha, not fake”) và 1 Public key (Gọi là Public-key-1. Vậy ta có thể hiểu là ở Server google.com nó đang giữ Private key 1 ứng với Public key 1 kia).

Browser sẽ xác minh ssl certificate nhận được để xem nó có phải là hàng real hay không. Vậy còn làm sao mà browser có thể phân định thật giả? Chúng ta sẽ tìm hiểu trong 1 bài viết khác.

Sau khi browser xác minh và đã tin tưởng máy chủ google.com, browser sẽ tạo 1 cặp khoá bảo mật RSA thứ 2, ta gọi là Public key 2 và Private key 2.

Browser dùng Public key 1 đã nhận được từ bước 3, để mã hoá Public key 2 và Private key 2 sinh ra trong bước 4, rồi gửi cặp khoá này dưới dạng đã mã hoá tới máy chủ google.com.

Như bạn nhớ, thì máy chủ google.com đang giữ Private key 1 và thế là nó có thể giải mã và lấy được cặp key ở bước 5.

Vậy là cả máy chủ google.com và client đều có cặp khoá RSA thứ 2, mọi thông tin truyền gửi từ cả 2 phía bây giờ đều được mã hoá bằng Public key 2.

Note: Nhờ cơ chế mã hoá RSA (Chỉ có thể mã hoá bằng public key và giải mã nội dung bằng private key) kể trên. Server và client đã có thể liên lạc với nhau một cách an toàn. Trong quá trình trên, kể cả kẻ thủ ác có bắt được gói tin ở bước 2, bên trong có public key 1, thì cũng không phải vấn đề, bởi vì với public key đó thì hắn không thể đọc được thông tin mà client gửi ở bước 5.
