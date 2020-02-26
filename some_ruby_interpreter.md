`1. Ruby chạy code của bạn như nào?`
Trước khi nói về các trình thông dịch, tôi xin nêu khái quát về quá trình ruby thực thi mã code của bạn.
Với bất cứ đoạn mã ruby nào, mã nguồn bạn viết sẽ trải qua các bước sau để có thể tới được trình thông dịch (chủ đề mà ta đang tìm hiểu).

Token:
Chuyển đổi mã nguồn ruby thành một dạng keyword. Hiểu đơn giản, ruby sẽ quét qua mã nguồn bạn viết và so sánh với một symbol table nào đó (giả dụ như vậy), để xác định xem trong mã nguồn bạn viết gồm những thành phần nào (để xác định đâu là biến, đâu là keyword, đâu là từ khoá). Ví dụ việc từ do xuất hiện trong mã nguồn, có thể nó là một biến local, hoặc nó là một keyword trong một block, Token sẽ quét mã nguồn và đảm bảo xác định đúng type của từng thành phần trong code của bạn, chú ý là type nhé, ở bước này ruby chỉ quan tâm đến xác định type.

Parse: Qua bước đầu tiên, ta đã có một token, đến bước thứ hai nó sẽ chuyển đổi token đó thành một AST (Abstract Syntax Tree).

Compile: Đến bước này, ruby sẽ biên dịch kết quả ở bước 2 thành bytecode và truyền kết quả biên dịch được sang cho máy ảo ruby (là phần mà chúng ta đang tìm hiểu trong bài viết).

`2. CRuby, MRI, YARV, KRI`
Để kiểm tra xem ruby trên máy tính của bạn đang sử dụng trình thông dịch nào, hãy mở irb và sử dụng lệnh sau:

```
RbConfig::CONFIG["RUBY_INSTALL_NAME"]
#=> kết quả trả về trên máy của tôi là: ruby
```
Điều này có nghĩa tôi đang sử dụng trình thông dịch mặc định trong ruby. Được gọi là MRI/CRuby hay phiên bản nâng cấp sau này là YARV/KRI. Giải thích về việc có nhiều tên gọi như trên, tôi xin trích dẫn các thông tin trên wiki và trình thông dịch này:

Matz’s Ruby Interpreter or Ruby MRI (also called CRuby) was the reference implementation of the Ruby programming language named after Ruby creator Yukihiro Matsumoto (“Matz”)

YARV (Yet another Ruby VM) is a bytecode interpreter that was developed for the Ruby programming language by Koichi Sasada. The goal of the project was to greatly reduce the execution time of Ruby programs.

Since YARV has become the official Ruby interpreter for Ruby 1.9, it is also named KRI (Koichi’s Ruby Interpreter), in the same vein as the original Ruby MRI, named for Ruby’s creator Yukihiro Matsumoto.

YARV was merged into the Ruby Subversion repository on January 1, 2007. It was released as part of Ruby 1.9.0 on December 26, 2007, replacing Ruby MRI.

Qua vài trích dẫn kể trên, chúng ta có thể thấy đơn giản những khái niệm CRuby, YARV, MRI, KRI đều nhằm mô tả về một trình thông dịch được sử dụng mặc định cho ruby (tuỳ phiên bản sẽ có khác nhau, hiểu theo nghĩa là một sự update thay vì là thay thế).

`3. JRuby`
Còn một vài trình thông dịch khác mà tôi đã đọc được, nhưng JRuby là phần cuối mà chúng ta sẽ tìm hiểu, vì những những trình thông dịch còn lại không được phổ biến cho lắm.

JRuby = Java + Ruby

Tức là dùng phần java làm core để chạy máy ảo xử lý code ruby. Sự khác biệt với mục 2,chính là ở mục 2, dù dưới tên gọi hay phiên bản nào, thì những trình thông dịch đó đều sử dụng C làm ngôn ngữ xây dựng.

Một số ý kiến xoay quanh JRuby

JRuby nhanh hơn đáng kể với trình thông dịch mặc định trong ruby, có lợi thế mạnh mẽ khi xử lý đa luồng.

Tuy nhiên đồng nghĩa với điều đó là việc JRuby chiếm dụng bộ nhớ nhiều hơn và mất thời gian hơn để khởi động.

Vậy sẽ là một thảm hoạ nếu sử dụng JRuby để làm trình thông dịch cho ruby trong những ứng dụng nhỏ, hoạt động đơn giản và không cần tốc độ quá cao.
