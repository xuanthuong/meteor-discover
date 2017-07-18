#Những lưu ý trong quá trình học
- Chương 7: Người dùng không đăng nhập thì không được submit bài, trước đó submit được là nhờ gói package `insecure`. Sau khi remove thì người dùng chưa đăng nhập sẽ không submit được bài (`meteor remove insecure`). Chú ý là lúc này người dùng cũng không submit bài post từ client console được.
Sau đó ta dùng phương thức `allow` (`Posts.allow`) để thông báo cho phép user đã log in được phép post bài.

Nhưng vẫn có 1 số vấn đề là:
  + Người dùng chưa log in vẫn thấy form nhập bài post
  + Bài viết không được gắn với user nào
  + Vấn đề bài viết cùng url

Dùng kỹ thuật `route hook` để kiểm tra user đã log in hay chưa, nếu chưa thì `render` `accessDenied` template.
Để tránh kiểm tra log in hay không thì giấu luôn đường dẫn (giấu button `new post`) sử dụng `Spacebars` `currentUser` trong package `Meteor.user()`

Sử dụng `Method` để đóng gói, thay vì gọi các lệnh `insert` `update` `remove` thì đóng gói chúng vào các phương thức `postInsert` `postUpdate` `postRemove`, sau đó call những phương thức đó thông qua `Meteor.call()`. Khi call thì có 1 tham số là hàm `callback` lúc nào cũng nhận 2 tham số đầu vào là `(error, result)` cho 2 trường hợp `post` thành công hay thất bại.

Cuối cùng là kiểm tra `tính bảo mật` của `object sẽ insert` thông qua 2 packages `audit-argument-checks and check`. Lúc này chúng ta sẽ **Không cần phương thức allow()** mà sử dụng `_.extend` (thuộc thư viện `Underscore`), `postAttributes`. Ví dụ kiểm tra kiểu dữ liệu của userId, url, datetime. **Nếu bạn muốn chạy một đoạn code trước mỗi lệnh insert, update hoặc remove ngay cả trên server, chúng tôi đề nghị kiểm tra gói `collection-hooks`.**

Bonus: Kiểm tra url đã tồn tại hay chưa? sort bài viết

- ```Mục 7.5: Trong mô hình web hiện tại thì thời gian trễ cần phải nhỏ nhất để người dùng có cảm nhận đáp ứng tức thời. Kỹ thuật được sử dụng là khi người dùng nhấn nút submit thì trình duyệt gọi method insert hay gì đó, đồng thời client phải mô phỏng lại dữ liệu đó (giả sử đã insert thành công), sau đó mất thời gian 200 - 500ms thì server trả kết quả thật sự về (thường thì giống nhau) để client cập nhật lại```