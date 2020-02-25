SQL Performance Explained
NOTES 2020-01-05   NOTES
The notes of mine after read SQL Performance Explained Ebook (boring, but helpful).

Anatomy of an Index
Cơ sở dữ liệu sử dụng 2 loại cấu trúc để lưu trữ index. B-Tree và Doubly Linked List. Vì vậy cách sử dụng 2 loại cấu trúc này sẽ giải thích cho phần lớn các đặc tính của SQL.

Doubly Linked List
Mỗi một node (node chứa index) sẽ là một node trong 1 Doubly Linked List, vì vậy nó sẽ có liên kết tới đến 2 node lân cận (một trước và một sau). Khi ta muốn chèn thêm 1 node mới vào dslk đôi này. Ta sẽ tìm vị trí 2 node cần chèn node mới vào giữa chúng, đơn giản chỉ thay đổi con trỏ của 2 node đó. Vì thế vị trí vật lý của node mới không quan trọng. Lợi ích của việc này là giúp việc chèn thêm dữ liệu không tốn quá nhiều công sức, chỉ thay đổi vài con trỏ.

Mỗi leaf node được lưu chữ trong 1 block/page. Mỗi block sẽ thườn là 1 vài KB, csdl sẽ cố gắng lưu nhiều node (node chưa index) nhất có thể trong mỗi block/page. Vậy csdl sẽ dùng dsdl đôi để quản lý trong 2 cấp (Dùng trong các node thuộc 1 block/page và dùng giữa các blog với nhau). Hiểu đơn giản như ta chia Việt Nam thành 63 block ứng với 63 tỉnh thành. Mỗi khi ta muốn tìm 1 tỉnh nào đó thì tìm trong sdl đôi của 63 tỉnh. Khi đã tìm thấy tỉnh cần tìm ta muốn tìm đến huyện (các huyện trong tỉnh đó cũng dùng dslk đôi để kết nối). Nhìn hình dưới đây.

2 level doubly linked list

The Search Tree (B-Tree)
Dslk đôi giúp thực hiện nhanh các thao tác chèn, xoá node, tuy nhiên vậy còn tìm kiếm thì sao. Nhớ lại cấu trúc dslk thì việc tìm kiếm là tuyến tính, vì vậy chúng ta cần một cấu trúc dữ liệu bổ sung để giúp csdl biết được đối tượng cần tìm đang nằm trong block/page nào 1 cách nhanh chóng. Và cấu trúc dữ liệu được chọn là B-Tree. Nhìn hình dưới đây:
b-tree

Như hình trên. Dslk đôi giúp sắp xếp các node và các block/page. Root và branch node giúp đẩy nhanh quá trình tìm kiếm 1 block/page cần thiết.

Lý do B-Tree được lựa chọn và sử dụng rộng rãi là vì đặc tính cân bằng của cây. Độ cao của cây là không đổi từ root đến bất kể node lá nào. Tính tăng trưởng chiều cao của cây theo Logarit khi tăng node lá dẫn đến 1 cây bậc 4, 5 có thể chứa hàng triệu node và trong thực tế gần như không có trường hợp nào có độ sâu lên đến 6.

The Where Clause
Concatenated Indexes
Các hệ quản trị cơ sở dữ liệu hiện nay sẽ đánh index cho khoá chính một cách tự động.

Concatenated Indexes: Index được tạo bằng nhiều cột.

Một index được tạo từ 2 cột ví dụ col-1 và col-2 thì trong 1 câu truy vấn where dùng với 2 cột trên, index sẽ hoạt động không khác gì so với index một cột cả. Tuy nhiên thứ tự 2 cột trong câu lệnh tạo index sẽ rất quan trọng.

e.g:

1
2
CREATE UNIQUE INDEX employee_pk
  ON employees (employee_id, subsidiary_id);
Khi này nếu chúng ta dùng truy vấn sau:

1
2
3
SELECT first_name, last_name
  FROM employees
  WHERE subsidiary_id = 20;
Index sẽ tạo bên trên vô dụng và csdl sẽ full scan toàn bộ bảng employees.

Cột đầu tiên sẽ là tiêu chí chính để xác định record cần tìm, chỉ sử dụng cột thứ 2 khi mà có nhiều hơn 1 record đáp ứng được tiêu chí về cột đầu tiên. Vì vậy 1 truy vấn với cột thứ 2 sẽ là vô nghĩa.

2-col

Trong hình trên ta có thể thấy, B-Tree được sắp xếp theo cột đầu tiên, và ta chỉ sử dụng cột thứ 2 khi có nhiều hơn 1 record đáp ứng được điều kiện với cột đầu tiên. Ví dụ như record có id = 123, B-Tree sẽ dẫn chúng ta tới block chứa id này, ở đây vì có nhiều hơn 1 record có id = 123. Nó mới dựa vào cột thứ 2 để lấy record cần tìm. Vậy nếu ta query với điều kiện where chỉ có cột thứ 2 thì rõ ràng là hoàn toàn vô nghĩa.

Bản thân B-Tree trong trường hớp này cũng chỉ lưu cột thứ 2 ở node lá mà thôi, những node trung gian không có thông tin của cột 2.
Điều quan trọng nhất khi tạo một index gồm nhiều cột là xác định xem nên chọn cột nào làm cột đầu tiên trong index.

Function-base index
Trong trường hợp đã đánh index cho cột last_name, vì không kiểm soát được dữ liệu của người dùng nhập và dữ liệu trong csdl, chữ hoa và chữ thường trong trường hợp này không phân biệt. Vậy ta nên làm gì?

1
2
3
SELECT first_name, last_name, phone_number
  FROM employees
  WHERE UPPER(last_name) = UPPER('winand');
Tìm các record có last_name = ‘winand’, trước khi tìm kiếm thì sẽ dùng hàm UPPER để covert dữ liệu sang chữ hoa khi so khớp. Nhớ điều kiện bên trên, chúng ta đã đánh index cho field last_name tuy nhiên trong trường hợp này index sẽ không còn hoạt động, thay vào đó là 1 full scan toàn bộ bảng.

Mô tuýt:

1
2
3
SELECT first_name, last_name, phone_number
  FROM employees
  WHERE BLACKBOX(...) = 'WINAND';
Trong đó thay BLACKBOX bằng một function bất kỳ, ứng với việc index không còn hoạt động.

Giải pháp: Đánh index luôn giá trị thực sự sẽ dùng khi tìm kiếm, ở đây là last_name dưới dạng viết hoa.

1
2
CREATE INDEX emp_up_name
  ON employees (UPPER(last_name));
Ta gọi chỉ mục được tạo trên là function-based index (FBI), chỉ mục dựa trên function.

Thay vì copy luôn dữ liệu cột được đánh vào cây index. Một function-based index sẽ apply function vào dữ liệu trước khi ghi vào cây index. Vậy trong ví dụ trên, cây index sẽ chứa toàn bộ last_name dưới dạng chữ hoa.

Một số ORM dùng upper/lower 1 cách ngầm và mặc định, chớ không cần dev phải trực tiếp thực hiện, ví dụ như Hibernate sẽ tự động thực hiện function-based index với lower của cột cần đánh.

Trên là kiến thức SQL, với các hệ quản trị cơ sở dữ liệu trong thực tế, cách thực hiện không phải lúc nào cũng như vậy.

User-Defined Functions
Không phải function nào cũng có thể dùng cho function-based index.

Chỉ những function là deterministic mới có thể dùng trong function-based index.

deterministic: những function trả về cùng một kết quả khi nhận tham số giống nhau.

1
2
3
4
5
6
7
CREATE FUNCTION get_age(date_of_birth DATE)
RETURN NUMBER
AS
BEGIN
 RETURN
 TRUNC(MONTHS_BETWEEN(SYSDATE, date_of_birth)/12);
END;
Không thể dùng get_age cho function-based index. Vì ví dụ tôi sinh ngày 30/08/1995. Ứng với mỗi thời điểm khác nhau output của hàm này sẽ khác nhau với cùng một đối số truyền vào là ngày sinh của tôi. Vậy nên nó không dùng để function-based index được.

Các function khác không thể dụng cho index là function random hoặc function dựa trên biến môi trường.

Notes: Giả sử chúng ta đánh index cho cột last_name rồi đánh tiếp cho UPPER(last_name), với mỗi lần update đối tượng có trường last_name, đồng nghĩa chúng ta phải update cho 2 index. Ứng với 2 index đã đánh, vì vậy không nên đánh index quá nhiều, nhất là với function-based index.

Parameterized Queries
Bind parameters/dynamic parameters/ bind variables là một cách truyền tham số vào câu query gián tiếp bằng các ký hiệu như ?, :name, @name.

Bind variables are the best way to prevent SQL injection.

Các hệ quản trị csdl sẽ cache lại các dữ liệu đường truy cập y hệt nhau. Khi ta thay đổi tham số truyền vào hệ cơ sở dữ liệu sẽ query mới và không còn dùng cache.

Not using bind parameters is like recompiling a program every time.

Searching for Ranges
Rủi do hiệu suất lớn nhất với INDEX RANGE SCAN là quá trình duyệt qua nút lá.

1
2
3
4
5
SELECT first_name, last_name, date_of_birth
  FROM employees
  WHERE date_of_birth >= TO_DATE(?, 'YYYY-MM-DD')
  AND date_of_birth <= TO_DATE(?, 'YYYY-MM-DD')
  AND subsidiary_id = ?
Ok, giờ chúng ta có một câu truy vấn như trên, tìm tất cả nhân viên có subsidiary_id = 27 và ngày sinh nằm trong khoảng 1/1/1971 và 9/1/1971.

Chúng ta sẽ đánh index cho 2 cột trên, đánh như bên trên đã ghi. Tuy nhiên thứ tự đánh index như nào?

Trường hợp xếp date_or_birth trước:

date- first

Trường hợp xếp subsidiary_id trước:

sub- first

Tại sao trường hợp cho subsidiary_id đầu tiên trong index lại đem lại performance tốt hơn?

Lý giải: khi dùng range first, kiểu gì cũng phải duyệt hết range đó thôi, record nào trong range đó là sẽ được duyệt qua rồi so khớp với điều kiện so bằng thứ hai.

Khi dùng trường so bằng đầu tiên, ta làm giảm phạm vì số record cần quét qua (theo quy tắc của B-Tree).

Rule of thumb: index for equality first—then for ranges.

Indexing LIKE Filters
Với các query với LIKE, chúng ta vẫn có thể đánh index bình thường lên trường được truy vấn. Tuy nhiên trong một số trường hợp chúng ta sẽ gặp vấn đề ví dụ như:

Dùng ký tự % trong từ cần tìm.

1
2
3
SELECT first_name, last_name, date_of_birth
  FROM employees
  WHERE UPPER(last_name) LIKE 'WINND'
Đánh index trên last_name và ngon.

1
2
3
SELECT first_name, last_name, date_of_birth
  FROM employees
  WHERE UPPER(last_name) LIKE 'WIN%D'
Ký tự % xuất hiện và không ngon nữa rồi. Vì lúc này, index chỉ sử dụng phần trước % để tính khoảng range cần quét, còn phần đằng sau % chỉ dùng nhiệm vụ so khớp.

Tức là phải quét qua bao nhiêu phần tử sẽ hoàn toàn do phần đầu tiên quyết định. Từ có ta có 2 định nghĩa tác giả nêu ra:

access predicate: phần đầu
filter predicate: phần còn lại
Ví dụ hình dưới đây:

like-query

Khi thay cụm từ đứng trước % từ WI -> WIN -> WINA thì scan range đã thay đổi từ:

18 -> 2 -> 1 (trong cơ sở dữ liệu mẫu).

Index Merge
One index scan is faster than two.

Liệu nên tạo mỗi index cho một column, hay tạo một index cho tất cả column, trường hợp nào hiệu quả hơn? Câu trả lời của tác giả là nhiều cột cho một index sẽ hiệu quả hơn trong truy vấn where.

Tại sao lại như vậy?

Quay trở lại định nghĩa, chúng ta đã được đọc rằng index dùng B-Tree kết hợp với dslk đôi để cover các trường hợp.

Vậy giả sử, ta đánh index lên 2 column (last_name và date_of_bird) theo đúng thứ tự sắp xếp. Khi này hệ quản trị csdl sẽ đánh sắp khoảng theo last_name, tức là node lá sẽ bắt đầu tới last_name có ký tự nhỏ nhất (ví dụ là A) và node lá cuối cùng sẽ là ký tự lớn nhất (ví dụ là Z). Khi này, trường thứ 2 trong index chỉ dùng để so khớp khi ta có 2 last_name bằng nhau mà thôi.

Vậy nếu ta đánh 2 index riêng biệt cho last_name và date_of_bird thì sao? Lúc này với một câu truy vấn dưới đây:

1
2
3
4
SELECT first_name, last_name, date_of_birth
  FROM employees
  WHERE UPPER(last_name) < ?
  AND date_of_birth < ?
hệ quản trị csdl sẽ phải duyệt qua 2 cây index (1 cây ứng với index cho last_name và cây còn lại ứng với index cho date_of_birth). Sau đó combine kết quả của 2 lần duyệt ở trên lại.

Sẽ tốn resource hơn rất nhiều so với đánh 1 index cho last_name + date_of_birth.

Có một loại index giúp giải quyết bài toán trên gọi là bitmap index, tuy nhiên bitmap index gặp vấn đề lớn trong việc ghi đồng thời, vì thế nó không thể sử dụng trong các csdl thanh toán online.

Partial Indexes
Đánh chỉ mục cho 1 nhóm phần tử thuộc 1 bảng.

1
2
3
CREATE INDEX messages_todo
  ON messages (receiver)
  WHERE processed = 'N'
Đánh index cho các row thuộc bảng messages mà có column processed có giá trị bằng N.

Vậy ta dùng được index này khi nào. Câu trả lời là chúng ta có thể dùng mọi lúc khi query với mệnh đề where.

Performance and Scalability
The Join Operation
Clustering Data
