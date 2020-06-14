---
title: "Choose for the Future"
date: 2003-06-16T21:23:27+07:00
categories: ["memory"]
tags: ["java",".net"]
language: "vietnamese"
---

The me in 19 years old wrote this post! The boy was in the verge of choosing between .NET or Java.

----

Những khó khăn lớn trong lập trình thường là:

- Thứ nhất là do ranh giới giữa lập trình ứng dụng và lập trình hệ thống rất mờ nhạt, người lập trình ứng dụng cũng có đầy đủ trong tay các công cụ để thọc sâu và có thể làm tổn thương hệ thống (như thao tác các vùng nhớ trên đĩa, chặn các ngắt hệ thống, thay đổi cấu trúc đĩa cứng ...). Vì vậy, làm xong một chương trình, không biết có ổn không, chẳng biết sau khi chạy nó có gây ra tác dụng phụ nào không.
- Thứ hai là khi làm xong một chương trình, có khi nó chạy tốt ở máy này, lại chết khi sang máy khác.
- Thứ ba là sau lập chương trình trên hệ điều hành này, khi chuyển sang hệ điều hành khác lại phải chỉnh sửa lại mã và biên dịch lại trên hệ điều hành đó.
- Thứ tư là khi ta tách công việc của mình ra rồi làm chúng trên các công cụ lập trình khác nhau, thường gặp khó khăn khi liên kết các thành phần chương trình, vì vậy phần lớn các trình ứng dụng chỉ làm trên nền một công cụ lập trình duy nhất.

## Chặng đường JAVA

Đã có một giải pháp hiệu quả cho ba khó khăn đầu là: tạo một hệ thống ảo ngăn cách người lập trình ứng dụng với hệ thống thật. Những manh mối từ hệ thống thực đến hệ thống ảo được kiểm soát rất chặt chẽ bới nhà sản xuất khiến cho không ai có thể bày trò tai quái được. Hệ thống ảo đó gọi là Máy ảo Java (JVM - Java virtual machine).

Được Sun Microsystems công bố lần đầu tiên năm 1995, Java không chỉ là một ngôn ngữ lập trình mà là cả một công nghệ. Ở đâu có máy ảo Java, ở đó có thể chạy chương trình Java, bất kể hệ điều hành đang dùng, vì vậy Java có thể chạy trên Window, MacOS, Unix, Linux... thậm chí cả ở điện thoại di động và các thiết bị cầm tay khác. Sun đã tiến hành chiến dịch tiếp thị ầm ĩ với khẩu hiệu “Write once, run anywhere” và quảng cáo Java như một ngôn ngữ lập trình an toàn trên mạng.

Thực ra máy ảo Java được James Gosling (công trình sư của Sun Microsystems) tạo ra với mục đích ban đầu chẳng liên quan gì đến mạng, mà chỉ nhằm tạo điều kiện thuận lợi cho việc lập trình các thiết bị gia dụng. Còn về tên gọi “Java”, người ta đồn rằng các nhà phát minh của Sun nghĩ ra khi đang khề khà bên tách cafe của Java.

Java đã tạo ra một sàn diễn cho người lập trình ứng dụng tha hồ múa may. Trong máy ảo Java có sẵn những linh kiện được viết bằng Java (kiểu như là những Unit) đáp ứng mọi nhu cầu của việc lập trình ứng dụng. Máy ảo Java cùng những linh kiện ấy tạo thành Java runtime environment (môi trường chạy Java). Những vấn đề gai góc với người lập trình C++ như việc liên lạc qua mạng giữa các đối tượng nằm trên các máy khác nhau, việc tính toán song song... được thực hiện dễ dàng trên Java chỉ bằng vài câu lệnh. Tuy nhiên Java không có con trỏ khiến cho người lập trình một mặt khó mà bày trò được, mặt khác cũng khiến họ cảm thấy trống trải và rất khó chịu.

Chương trình Java được biên dịch thành Byte code (“mã máy” của máy của máy ảo Java). Khi chạy, bộ biên dịch tức thời (JIT - Just in time compiler) biên dịch một lượt Byte code thành mã máy thực rồi chạy. Vì vậy Java thường tỏ ra chậm chạp lúc khởi động nhưng khi chạy thì đạt tốc độ gần bằng chương trình viết bằng C++. Bằng chứng là việc Java ngày càng phổ biến trong những lĩnh vực tính toán bằng phương pháp số (vốn đòi hỏi tốc độ) trước đây là lãnh địa của Fortran.

Có 3 loại Java runtime environment:

- Java 2 Micro Edition (J2ME): Dùng cho các thiết bị cầm tay.
- Java 2 Standard Edition (J2SE): Dùng để xây dựng các ứng dụng trên máy tính để bàn.
- Java 2 Enterprise Edition (J2EE): Phong phú nhẩt, dùng để lập trình trên máy chủ.

Sun ra sức phát triển Java runtime environment trên nhiều hệ điều hành khác nhau, cung cấp miễn phí các công cụ lập trình Java và coi Java là chuẩn mở (open standard). Nhờ vậy, Java trưởng thành nhanh chóng, không phải long đong trong quá trình chuẩn hoá cực kỳ chậm chạp của C++ (vốn được thực hiện một cách “quan liêu” bởi ANSI - American national standard institute).

## Microsoft nhảy vào, .NET ra đời

Chỉ ít lâu sau khi Java ra đời, Microsoft đã nhanh nhạy ký ngay với Sun hợp đồng phát triển máy ảo Java trên Windows (Microsoft JVM). Tuy nhiên, máy ảo Java của Microsoft khác thường ở chỗ là cho phép người lập trình Java thọc sâu xuống hệ điều hành để sử dụng những chức năng đặc thù của Windows. Điều này quyến rũ người lập trình viết các chương trình tối ưu hoá cho Windows và dĩ nhiên không thể chạy được trên hệ điều hành khác. Chiêu này gọi là “cắt cỏ dưới chân đối thủ”, kéo theo những vụ kiện cáo triền miên giữa Sun Microsystems với Microsoft...

Bị làn sóng Java tấn công, nhưng Microsoft đánh giá rất cao ý tưởng công nghệ Java và phản công lại bằng một công nghệ tương tự gọi là “dot Net” (.Net), tên gọi này khẳng định đây là công nghệ trên mạng. Ba năm sau ngày Java chào đời, Microsoft bắt tay vào nghiên cứu .Net và công bố chính thức vào năm 2001. Công nghệ này vừa tạo ra nền tảng mới an ninh hơn cho việc lập trình ứng dụng trên Windows, vừa thống nhất mọi kĩ thuật của Microsoft (vốn được cơi nới khá lung tung). Với những người từng đau đầu với WIN32 API của Windows (công cụ cơ bản nhất tạo giao diện Windows, rất mạnh, nhưng rất linh tinh) sẽ rất sướng với sự chỉn chu của .NET.

Máy ảo .NET trên môi trường Windows được kêu là CLR - Common language runtime (ngôn ngữ chung lúc chạy). Tên như vậy thể hiện mưu đồ của .NET là mọi ngôn ngữ lập trình đều được biên dịch ra ngôn ngữ chung (kiểu như Byte code của Java), vào lúc chạy, mã chung được biên dịch một lượt thành mã máy thực rồi mới chạy. Điều này cho phép các đề án được thực hiện thoải mái bằng nhiều ngôn ngữ lập trình khác nhau ở các công đoạn khác nhau tận dụng sức mạnh của các công cụ lập trình. Hiện có 4 ngôn ngữ chính thức cho việc lập trình .NET là: C#, VB.NET, Managed C++, Jscript.NET. Việc xây dựng .NET cho các ngôn ngữ lập trình khác (Eiffel, COBOL, PERL ...) vẫn đang được tiến hành. Microsoft muốn giương cao chiêu bài đa ngôn ngữ để đối phó với chiêu bài đa hệ thống của Java.

Ngôn ngữ lập trình mới C# (ngụ ý là C++++) được thiết kế bởi một nhóm do Anders Hejlsberg (cha đẻ của Turbo Pascal và Delphi, từng phụ trách nhóm Microsoft J++) và Scott Wiltamuth lãnh đạo. Do đó không có gì lạ khi C# rất giống Java.

Điều đáng chú ý là, nếu viết 2 lớp có nhiệm vụ giống nhau trên hai ngôn ngữ khác nhau thì trình biên dịch .NET sẽ cho ra kết quả giống y hệt ở dạng ngôn ngữ chung. Cũng như máy ảo Java, CLR - Common Language Runtime cũng có những linh kiện được viết bằng C# thực hiện mọi nhiệm vụ của trình ứng dụng trên Windows và trên mạng, toàn bộ cơ sở hạ tầng ấy được gọi là .NET Framework (tương tự Java runtime environment). Để chạy chương trình .NET cần phải cài .NET Framework (với Windows Server 2003 thì có sẵn). Với sự ra đời của .NET, Java bị thất sủng hoàn toàn trên Windows. Cũng như công nghệ Java, về nguyên tắc, ở đâu có .NET Framework thì ở đó chạy được ứng dụng .NET. Tương tự như J2ME, Microsoft đang xây dựng một nền tảng .NET nhỏ gọn gọi là .NET Compact Framework (.NETcf) để dùng cho các thiết bị cầm tay, hứa hẹn sự cạnh tranh khốc liệt giữa .NETcf và J2ME trong tương lai trên hệ điều hành Palm OS.

Tính đa hệ thống của .NET cũng bắt đầu ló rạng khi người ta tiến hành xây dựng nền tảng của .NET trên Unix. Nhưng kì thực, sự phát triển mạnh mẽ của .NET thực chất là vì Windows, vì vậy nền tảng .NET trên các hệ điều hành khác nếu có thì cũng sẽ èo uột, còn lâu mới bằng .NET trên Windows.

## Hãy lựa chọn tương lai

Tình hình chiến sự là như vậy đấy, cuộc cạnh tranh giữa Windows với Linux, giữa .NET với Java. Bạn phải tự xác định mục tiêu ngay từ bây giờ để chau chuốt tụng kinh một thứ thôi. Vì không thể thông thạo tất cả được. Nếu bạn khoái Windows, hãy chú trọng lập trình .NET ngay. Nếu bạn cần chương trình chạy trên nhiều hệ điều hành thì hãy học Java. Tuy nhiên, dù chỉ chú trọng một thứ nhưng vẫn phải biết thứ kia, đủ để trở mặt khi cần, cũng như Microsoft bắt các chuyên gia tư vấn giải pháp .NET của mình theo học một khoá học về Java. Nếu quyết tâm sống chết với Linux, Unix thì rất nên học Java, vì bạn có thể dùng Windows để phát triển các ứng dụng trên Linux, Unix. Bản thân tôi nhận thấy Linux không hề thua Windows, thậm chí còn hơn, nhưng có nhược điểm là khó sử dụng với người không chuyên, chưa có nhiều ứng dụng đỉnh cao cho nó, vì mới ra đời, vì vậy Linux chưa phổ dụng trong, thường chỉ dùng trong những hệ thống cần bảo mật cao, với các chuyên gia vận hành. Nhưng với trình độ tin học ngày càng cao của con người thì trong tương lai, rất có thể nó sẽ qua mặt Windows. Có thể đến thời điểm của mình, cuộc cạnh tranh giữa các hệ điều hành rất khốc liệt, tạo ra các ứng dụng Java chạy trên nhiều hệ điều hành là một lợi thế to lớn dù rằng Java không tiện nghi như .NET, chưa được phát triển đa ngôn ngữ như .NET. Đi theo hệ điều hành nào, chọn thể loại lập trình “ruột” là gì, bạn nên quyết định sớm đi. Học máy tính “ăn” nhau ở kinh nghiệm, thời gian ngồi bên máy, vì vậy quyết định càng sớm càng có lợi cho bạn.
