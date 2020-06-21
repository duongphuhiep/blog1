---
title: "Unit Testing"
date: 2018-11-04T00:35:33+02:00
tags: ["c#", "java", "xunit", "test"]
language: "vietnamese"
categories: ["backend", "test"]
keywords: ["unittest"]
include_toc: true
---
Lúc mới vào nghề, có lần Sếp yêu cầu tôi viết Unit Test cho 1 ứng dụng cũ của công ty để tăng [điểm Sonar](https://docs.sonarqube.org/latest/user-guide/metric-definitions/). Sau nhiều cố gắng, các "Unit test" của tôi cũng chỉ phủ được khoảng 40% đám codes của ứng dụng cũ đó. Hơn nữa, muốn chạy được các "Unit test" đó, cần phải cấu hình CSDL, có dữ liệu mẫu và kết nối với các Ứng dụng ngoài (được phát triển bởi các nhóm khác)

Lúc đó tôi không biết rằng nhiệm vụ mà sếp giao cho tôi vốn không thỏa đáng, và những Unit tests tôi viết ra không thực sự là Unit tests. Cho tới hôm nay, tôi nhận thấy vẫn có rất nhiều sếp CTO hay lập trình viên dày dạn kinh nghiệm hiểu sai về Unit Test. Họ vẫn cho rằng viết Unit test hay viết test nói chung là 1 công việc phụ dễ dàng cho người mới vào nghề (junior), là việc kém quan trọng nhất, thường để "lót nồi" viết cuối cùng khi đã code xong xuôi và còn dư thời gian..

Bài viết này trình bày 1 vài kinh nghiệm của tôi về Unit test / hay test ứng dụng nói chung, lấy C# làm ví dụ điển hình, nhưng những thông tin tôi muốn truyền tải có thể ứng dụng cho bất kỳ ngôn ngữ lập trình nào, bạn có thể tùy ý thay thế chữ C# trong bài viết thành các ngôn ngữ tương tự như Java, Ruby, Python...

Hiểu một cách mộc mạc thì..

## Một Unit test = test cho 1 hàm (method) cụ thể của 1 class C# nào đó

**Mỗi khi viết 1 hàm (method) mới cho 1 class, ta cần phải nghĩ cách để viết test cho hàm đó**. Một số coder thậm chí còn tuân theo quy trình TDD, quy trình này quy định họ phải viết test cho hàm mới trước khi bắt tay vào viết code cho hàm đó..

1 Unit-test không được là 1 kịch bản test phức tạp. Chúng cần phải chạy mà không yêu cầu phải có CSDL hay có kết nối với các dịch vụ bên ngoài.

* Nếu Test của bạn muốn chạy được cần phải có kết nối với CSDL hay cần có kết nối mạng để gọi các dịch vụ bên ngoài thì đó không phải là **Unit test**, mà đó gọi là **integration test** (bạn đang test kết nối với CSDL và test cả kết nối với dịch vụ bên ngoài).
* Có rất nhiều framework hay thư viện giúp chạy Unit test như [xUnit](https://xunit.net/), [nUnit](https://nunit.org/).. nhưng chúng thường xuyên bị lạm dụng để chạy các integration test hay các kịch bản test phức tạp... trong khi người dùng vẫn hiểu nhầm là họ đang chạy Unit tests..

## Ai sẽ là người viết Unit test cho hàm này?

Tất nhiên tác giả viết ra hàm đó cũng phải chịu trách nhiệm viết Unit test cho nó. Khi Bạn thay đổi (refactor) một hàm, bạn cũng sẽ phải thay đổi (refactor) tất cả các Unit test liên quan...

## Không thể viết được Unit test cho hàm này!

"Tôi tạo ra 1 hàm mới và nghĩ cách viết Unit test cho nó.. nhưng chịu thua, hàm của tôi phụ thuộc vào CSDL, và gọi dịch vụ bên ngoài, vậy nên không Unit test nào có thể test nổi nó".

Ví dụ: Giả sử một hàm `Rabbit.Jump()` gọi dịch vụ ngoài để lấy dữ liệu về, thay đổi nó 1 chút với **1 tính toán nhỏ** (chỉ trong 1 dòng code thôi). Cuối dùng dữ liệu tính ra được sẽ được lưu vào CSDL. Phần lớn code trong hàm dùng cho việc xuất/nhập (gọi dịch vụ bên ngoài, đọc ghi CSDL..) làm thế nào viết được unit test cho nó đây?

```
class Rabbit {
  void Jump() {
    Gọi dịch vụ bên ngoài // code Xuất/Nhập - không-unit-test-được
    tính toán nhỏ // unit-test-được (1 dòng code)
    Lưu vào CSDL // code Xuất/Nhập - không-unit-test-được
  }
}
```

Trong trường hợp này đúng là Unit test không thể phủ được 100% hàm, nhưng khi viết Unit test bạn thực sự định test cái gì trong hàm này?

Mục đích Unit test không phải để test các dịch vụ bên ngoài hay test kết nối tới CSDL, mà để test tất cả những gì còn lại.. Trong trường hợp này, bạn sẽ phải viết Unit test để phủ test cho cái **tính toán nhỏ** (1 dòng code) kia.

Đó không phải là nhiệm vụ dễ dàng, dòng code cần test nằm giữa các đoạn codes Xuất/Nhập. Để test được nó, bạn cần phải cải tổ (refactor) lại codes, để tách các đoạn **codes unit-test-được** ra khỏi các đoạn codes Xuất/Nhập **không-unit-test-được**. Một kỹ thuật thông thường là phân tách các đoạn codes Xuất/Nhập ra các class riêng, rồi gọi chúng thông qua các interface hay các abstract class, để sau đó, ta có thể giả lập (mock) được các đoạn codes này khi chạy unit test. (Công việc này đòi hỏi lập trình viên cần phải có kinh nghiệm, có lẽ tôi sẽ trình bày trong một bài viết khác.)

Vậy nếu có anh CTO nào đó yêu cầu một junior mới vào nghề phải "viết thêm unit test" cho đám codes cũ mà lúc được viết tác giả hoàn toàn không nghĩ gì tới Unit test... thì đây là 1 yêu cầu không thỏa đáng...

## Có đáng phải bỏ công sức ra để viết Unit tests không?

Để có thể test được cái tính toán nhỏ (1 dòng code) kia, bạn phải hì hục ngồi cải tổ (refactor) lại cả đám codes.. Đó là chuyện bình thường, để có thể viết ra được unit test bạn sẽ phải cải tổ lại codes, làm giả lập (mock) khá nhiều. Mã Codes để test 1 hàm thường dài hơn (có khi rất nhiều) so với codes của chính cái hàm đó. Sau đó bạn sẽ phải bảo trì (maintain) cho cả codes chính lẫn đám codes test.. => Tôi tin rằng **viết codes kèm Unit-tests đòi hỏi nỗ lực gấp 3 (thậm chí 4 lần) nỗ lực viết code thông thường (viết code không nghĩ tới Unit test)**.

Vậy liệu có đáng để viết Unit test không? - tôi nghĩ là "CÓ" trong phần lớn các ca..

Khi bạn viết codes theo cách tự nhiên (tức là hoàn toàn không nghĩ tới Unit test): các lập trình viên non nớt thường thảy tất cả mọi thứ gộp chung vào 1 **hàm to** (thường dài và phức tạp) để hàm đó làm tất cả.. Họ ngại (sợ hoặc lười) phân tách codes ra thành các hàm nhỏ, ngại tạo ra các file mới / class mới / hay hàm mới.. bởi họ chưa có thói quen đó, không biết khi nào nên tách hay nên chập codes...

Trong cái **hàm to** kia, họ không ngại gì gọi các hàm static trong các module khác, điều đó thật tự nhiên và thuận tiện.. Vậy cứ [viết tất cả mọi thứ static hết là ngon](https://codeburst.io/static-classes-are-evil-or-make-your-dependencies-explicit-af3e73bd29dd)... :)) Cuối cùng cái **hàm to** đó cũng hoàn thành nhiệm vụ của mình và chạy được ngon lành trong ứng dụng...

Bây giờ, nếu bạn yêu cầu tác giả viết unit test cho cái **hàm to** kia... thì thôi xong! Đám codes trong đó không được viết để chạy trong **bối cảnh "thiếu thốn" của Unit test**. Đám codes đó chỉ chạy trong bối cảnh khi cả ứng dụng được chạy, có đầy đủ kết nối CSDL, và kết nối các dịch vụ bên ngoài, mọi thứ được cấu hình đầy đủ.. Vậy mỗi lần có thay đổi gì đó trong cái **hàm to** kia, bạn sẽ phải khởi động lại cả ứng dụng, đợi cho 1 sự kiện gì đó xảy ra trong ứng dụng để đoạn codes thay đổi được chạy, rồi bạn mới test được nó. Nhỡ nó chạy không chuẩn bạn sẽ phải làm đi làm lại như vậy...

Để có thể viết Unit test được cho **hàm to** kia, bạn phải cải tổ lại nó, thậm chí cải tổ lại toàn bộ codes cả ứng dụng để phân tách ra được những phần **codes unit-test-được** ra khỏi những phần codes **không-unit-test-được**. Nếu bạn đủ gan để làm vậy codes của ứng dụng sẽ trở nên quy củ và logic hơn nhiều.. Toàn bộ codes làm nhiệm vụ Xuất/Nhập sẽ có thể được giả lập và thay thế, ứng dụng sẽ trở nên linh hoạt hơn do ít bị phụ thuộc vào CSDL hay các ứng dụng bên ngoài.

**Vậy giá trị của Unit testing không hẳn chỉ là để đạt điểm Sonar cao hơn nhưng chủ yếu để tăng chất lượng kiến trúc và chất lượng codes của ứng dụng.**

Liếc sơ qua codes của một ứng dụng, bạn có thể nói ngay được tác giả có nghĩ tới Unit-test không khi viết ra đám codes đó. Nếu nghĩ tới Unit test, bạn sẽ thấy trong code có nhiều interface, abstract class giành cho các codes Xuất/Nhập. Nếu tác giả không nghĩ tới Unit test khi viết code, bạn sẽ thấy các đám code Xuất/Nhập trộn dính cứng với codes tính toán, và static được sử dụng ở khắp nơi..

## Chỉ với Unit tests không thể bao phủ được 100% codes của ứng dụng

Bạn tốn bao công sức cải tổ codes trong ứng dụng để có thể viết được thêm nhiều unit test hơn.. nhưng tùy thuộc vào thể loại ứng dụng, các Unit tests bạn viết ra có lẽ chỉ phủ được 30% codes của ứng dụng (con số ví dụ)

=> Đừng thất vọng! Unit test chỉ phủ những phần tính toán cốt lõi nhất của ứng dụng, 70% đám codes còn lại có lẽ là để thực thi Xuất/Nhập (đọc viết database, gọi dịch vụ bên ngoài...) bởi vì nhiệm vụ ứng dụng của bạn chủ yếu là để thực thi Xuất/Nhập, các tính toán cốt lõi chỉ chiếm phần nhỏ, nên Unit tests chỉ có thể phủ được ngần đó thôi.

Ví dụ nếu bạn làm 1 ứng dụng "mạng điều hướng", trong đó nhiệm vụ ứng dụng là để nghe nhận các request, điều hướng chúng cho 1 dịch vụ thích hợp bên ngoài, nhận và trả lại kết quả.. Unit test có khi chỉ phủ được 5, 10% codes cho thể loại ứng dụng này: nó chỉ phủ được phần tính toán điều hướng là cùng...

Ngược lại, nếu Xuất/Nhập chỉ là 1 phần nhỏ của ứng dụng, phần lớn codes ứng dụng dùng để tính toán, nghiệp vụ ( if/ for/ while..) bạn sẽ phải viết được nhiều Unit tests để phủ được phần lớn codes trong ứng dụng. Nếu bạn không viết được => đó là dấu hiệu cho thấy kiến trúc ứng dụng và tổ chức codes của bạn đang yếu kém, các đoạn codes tính toán nghiệp vụ trộn lẫn với codes Xuất/Nhập ngăn cản bạn viết Unit test cho chúng.

## Làm thế nào để tests bao phủ được 100% codes

Tiếp tục ví dụ trên, các Unit test của bạn đã phủ được 30% những phần cốt lõi nhất trong ứng dụng, 70% còn lại là codes Xuất/Nhập. Để phủ được đám codes này, cách duy nhất là chạy **Integration tests**: Lúc này, bạn cần có CSDL, kết nối được với các dịch vụ bên ngoài hoặc kết nối được với môi trường test hay môi trường giả lập của các dịch vụ đó. Bạn và các nhóm liên quan phải bảo trì toàn bộ hệ sinh thái này, và đó thông thường không phải là điều dễ dàng (khó khăn hơn nhiều so với việc duy trì Unit tests).

Ngày nay các nhà phát triển thường sử dụng [Docker](https://www.docker.com/), nó giúp ta tự động tạo ra môi trường thuận lợi cho các Integration Tests mỗi khi chúng được chạy.

Các **integration tests** thường phủ được phần lớn codes ứng dụng bao gồm tất cả các phần codes mà Unit tests đã phủ được. Vì vậy nhiều nhà phát triển thường bỏ qua việc viết **Unit tests** mà nhảy vào viết trực tiếp **Integration tests** luôn.. Nực cười ở chỗ: tuy họ đang viết Integration tests để chạy trong Docker nhưng họ vẫn lầm tưởng rằng mình đang viết Unit tests...

Nếu Integration tests có thể phủ được 100% ứng dụng rồi, vậy liệu có còn đáng đầu tư công sức vào Unit tests nữa không? Câu trả lời của tôi vẫn là "Có", theo tôi Unit test vẫn sẽ giúp cải thiện đáng kể chất lượng codes và kiến trúc ứng dụng.. Hơn nữa đừng coi thường con số 30% codes mà Unit tests đang bao phủ.. đó là đám codes cốt lõi nhất trong ứng dụng, và có nghĩa là bạn có thể test được chúng, test được 1/3 ứng dụng nhanh gọn bất kỳ lúc nào mà không cần kết nối với CSDL hay bất kỳ dịch vụ nào bên ngoài hệ thống... và đó là giá trị tuyệt vời của Unit test...

## Kết luận

Viết test là công việc khó khăn, có lẽ còn khó hơn cả việc viết codes chính. Bảo trì cho tests cũng không dễ dàng, bảo trì cho môi trường chạy Tests integration lại càng không dễ dàng và thường đòi hỏi nhiều can thiệp thủ công cầu kỳ... Đừng coi nhẹ hay xem thường những nhiệm vụ này và đẩy chúng cho các lập trình viên mới vào nghề..

Người viết codes cũng phải là người viết test và vạch ra chiến thuật, phương thức dễ làm và thuận tiện nhất có thể để test được đám codes do mình viết ra. Đừng viết codes 1 cách cẩu thả cho xong việc, rồi thảy mớ codes của mình cho người khác tests... trong khi ngay chính bản thân mình cũng gặp đầy khó khăn nếu muốn test chúng.

Thực ra , khi bạn viết codes, chỉ cần bạn nghĩ tới vấn đề làm thể nào để viết được Unit tests cho đám codes đang viết, làm thế nào để chạy được đám codes đó trong hoàn cảnh "nghèo nàn" của Unit test (không có CSDL, không có kết nối với bên ngoài), tự nhiên bạn sẽ phải tìm cách viết hay cách tổ chức khác hay tìm cách cải tổ lại codes.. và một cách tự nhiên chất lượng codes sẽ được đẩy lên cao.

