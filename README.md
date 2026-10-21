<div align="center">

# Website_MayHocCoBan

### Hệ thống web trực quan hóa quá trình học và dự đoán của các thuật toán máy học cơ bản

**Huỳnh Thanh Phong**  -  [@huynhthanhphong231004IT](https://github.com/huynhthanhphong231004IT)

**Link website** - [huynhthanhphong231004it.github.io/Website_MayHocCoBan](https://huynhthanhphong231004it.github.io/Website_MayHocCoBan/)

</div>

---

> **Tóm tắt.** Chúng tôi xây dựng một ứng dụng web chạy hoàn toàn trên trình duyệt để người học thực hành các thuật toán máy học cơ bản trên dữ liệu của chính mình. Điểm khác với các công cụ sẵn có là hệ thống không chỉ trả về nhãn dự đoán mà còn trình bày các bước tính toán, công thức và biểu đồ đi kèm. Hệ thống gồm hai nhóm bài toán: học có giám sát (KNN, Naive Bayes, cây quyết định, hồi quy tuyến tính, hồi quy logistic) và học không giám sát (K-means, phân cụm phân cấp).
>
> **Từ khóa:** máy học cơ bản, phân loại, hồi quy, phân cụm, trực quan hóa, ứng dụng web giáo dục.

---

## 1. Tổng quan

Khi học máy học, chúng tôi nhận thấy giáo trình trình bày công thức rất chặt chẽ nhưng khó hình dung, còn các thư viện như scikit-learn [10] hay bộ công cụ như WEKA [12] cho kết quả chỉ sau vài dòng lệnh, và chính những bước trung gian mà người học cần nhìn thấy lại bị ẩn đi. Thêm vào đó, muốn chạy thử một thuật toán trên dữ liệu riêng thì phải cài môi trường và viết mã đọc dữ liệu, một rào cản không nhỏ với người mới.

Nghiên cứu này đặt ra câu hỏi: có thể xây dựng một công cụ chạy ngay trên trình duyệt, nhận dữ liệu Excel quen thuộc, và đồng thời cho người học thấy được quá trình tính toán phía sau mỗi kết quả hay không?

```mermaid
flowchart LR
    %% Cấu hình khoảng cách giữa các node và hàng
    linkStyle default stroke-width:2px;
    
    %% Định nghĩa kiểu dáng với padding rộng hơn
    classDef data fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#0D47A1,font-weight:bold,padding:12px;
    classDef lib fill:#FFFDE7,stroke:#FBC02D,stroke-width:2px,color:#F57F17,font-weight:bold,padding:12px;
    classDef web fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20,font-weight:bold,padding:12px;
    classDef output1 fill:#FFEBEE,stroke:#C62828,stroke-width:2px,color:#B71C1C,padding:10px;
    classDef output2 fill:#F3E5F5,stroke:#7B1FA2,stroke-width:2px,color:#4A148C,padding:10px;

    %% Khai báo các nút (xuống dòng chủ động cho ô dài)
    D["Dữ liệu .xlsx"]:::data
    
    A["Thư viện Ultralytics"]:::lib
    B["Nhãn dự đoán<br/>(Quá trình bị ẩn)"]:::output1
    
    C["Website_MayHocCoBan"]:::web
    E["Nhãn dự đoán"]:::output2
    F["Công thức và<br/>các bước tính"]:::output2
    G["Bảng số liệu<br/>và biểu đồ"]:::output2

    %% Luồng dữ liệu
    D --> A
    A --> B
    
    D --> C
    C --> E
    C --> F
    C --> G
```

<div align="center">
<p align="center">
  <i>Hình I. Khoảng trống mà hệ thống hướng tới: giữ lại quá trình tính toán bên cạnh kết quả.</i>
</p>
</div>

---

## 2. Phương pháp

### 2.1. Hướng tiếp cận

Chúng tôi chọn xây dựng ứng dụng web tĩnh, xử lý hoàn toàn phía máy khách. Lựa chọn này cho phép triển khai bằng GitHub Pages, không cần máy chủ, và dữ liệu của người dùng không bao giờ rời khỏi trình duyệt. Mọi thuật toán được đặt vào cùng một khuôn thao tác (chọn dữ liệu, chọn thuật toán, đặt tham số, bấm chạy, đọc kết quả) để người học có thể so sánh các phương pháp mà không phải làm quen lại giao diện.

### 2.2. Kiến trúc

Hệ thống chia làm bốn lớp, mỗi lớp phụ trách một việc, thể hiện ở Hình 2.

```mermaid
flowchart TB
    subgraph UI["Lớp giao diện: index.html, css.css"]
        F1[Biểu mẫu chọn dữ liệu, thuật toán, tham số]
        F2[Cửa sổ hướng dẫn]
        F3[Vùng kết quả chi tiết]
    end
    subgraph DATA["Lớp dữ liệu"]
        S[SheetJS: đọc .xlsx]
        M[math.js: tính toán số học]
    end
    subgraph ALGO["Lớp thuật toán"]
        T1[ThuatToan.js: có giám sát]
        T2[ThuatToan2.js: không giám sát]
    end
    subgraph VIS["Lớp trực quan hóa"]
        V1[Chart.js, D3.js: biểu đồ]
        V2[MathJax: công thức]
    end
    F1 --> S
    S --> T1
    S --> T2
    M -.-> T1
    M -.-> T2
    T1 --> F3
    T2 --> F3
    F3 --> V1
    F3 --> V2
```

<div align="center">
<p align="center">
  <i>Hình II. Kiến trúc bốn lớp của hệ thống</i>
</p>
</div>

### 2.3. Luồng xử lý

Một lượt chạy đi qua các bước ở Hình 3. Người dùng chọn cấu hình và nạp hai tệp, hệ thống đọc tệp thành bảng số liệu rồi gọi hàm giải tương ứng với nhóm bài toán đã chọn (`runAlgorithm()` cho học có giám sát, `runAlgorithmunsuperviseddata()` cho học không giám sát). Kết quả và các bước trung gian được đẩy về vùng kết quả, nơi công thức và biểu đồ được dựng lên.

```mermaid
sequenceDiagram
    actor U as Người dùng
    participant UI as index.html
    participant X as SheetJS
    participant A as Thuật toán
    participant R as Vùng kết quả
    U->>UI: Chọn loại dữ liệu, thuật toán, tham số
    U->>UI: Tải tệp train và test (.xlsx)
    U->>UI: Nhấn "Giải"
    UI->>X: Đọc tệp thành bảng số liệu
    X-->>UI: Thuộc tính và nhãn
    UI->>A: Gọi hàm giải tương ứng
    A-->>R: Kết quả và các bước trung gian
    R->>R: MathJax dựng công thức, Chart.js vẽ biểu đồ
    U->>R: Xem chi tiết từng phép tính
```

<div align="center">
<p align="center">
  <i>Hình III. Luồng xử lý của một lượt chạy</i>
</p>
</div>


### 2.4. Quy ước dữ liệu

Để mọi thuật toán nhận cùng một dạng đầu vào, chúng tôi thống nhất cấu trúc tệp Excel như Bảng 1. Dòng đầu luôn là tên thuộc tính; riêng tập huấn luyện có cột cuối là nhãn.
<div align="center">
    
| | Tập huấn luyện | Tập dự đoán |
|---|---|---|
| Vai trò | Xây dựng và tối ưu mô hình | Dự đoán, kiểm tra sau huấn luyện |
| Dòng đầu | Tên thuộc tính và nhãn | Tên thuộc tính |
| Cột nhãn | Có (cột cuối) | Không |
| Tỷ lệ gợi ý | 70–80% | 20–30% |
<p align="center">
  <i>Bảng 1. Quy ước định dạng dữ liệu có giám sát. Dữ liệu không giám sát không có cột nhãn.</i>
</p>
</div>

### 2.5. Ràng buộc cấu hình

Để tránh người dùng cấu hình sai, giao diện tự điều chỉnh theo lựa chọn (Hình 4). Hồi quy tuyến tính chỉ phù hợp nhãn liên tục và hồi quy logistic chỉ phù hợp nhãn rời rạc nên hệ thống khóa loại nhãn tương ứng; các ô tham số chỉ hiện ra khi thuật toán thực sự cần đến.

```mermaid
flowchart TD
    S{Loại tập dữ liệu}
    S -->|Có giám sát| A{Thuật toán}
    S -->|Không giám sát| U{Thuật toán}
    A -->|KNN| A1[Hiện ô K và loại khoảng cách]
    A -->|Hồi quy tuyến tính| A2[Khóa nhãn: liên tục]
    A -->|Hồi quy logistic| A3[Khóa nhãn: rời rạc]
    A -->|Naive Bayes, cây quyết định| A4[Cho chọn nhãn rời rạc hoặc liên tục]
    U -->|K-means| U1[Hiện ô số cụm K]
    U -->|Phân cụm phân cấp| U2[Hiện chiến lược: bottom-up hoặc top-down]
```
<div align="center">
<p align="center">
  <i>Hình IV. Cơ chế tự điều chỉnh giao diện theo lựa chọn của người dùng.</i>
</p>
</div>

### 2.6. Các thuật toán và cách chúng được đưa vào hệ thống

Chúng tôi chọn những thuật toán kinh điển, có công thức rõ ràng và cài đặt được trực tiếp bằng JavaScript: KNN [1], Naive Bayes [2], cây quyết định [3, 4], hồi quy tuyến tính [8, 9], hồi quy logistic [5], K-means [6] và phân cụm phân cấp [7]. Bảng 2 tóm tắt bài toán, tham số và nội dung được hiển thị cho từng thuật toán.

<div align="center">
    
| Thuật toán | Bài toán | Tham số người dùng đặt |
|---|---|---|
| KNN | Phân loại, hồi quy | Số K, khoảng cách Euclid hoặc Manhattan |
| Naive Bayes | Phân loại | Không |
| Cây quyết định | Phân loại, hồi quy | Không |
| Hồi quy tuyến tính | Hồi quy | Không |
| Hồi quy logistic | Phân loại | Không |
| K-means | Phân cụm | Số cụm K |
| Phân cụm phân cấp | Phân cụm | Chiến lược bottom-up hoặc top-down |
<p align="center">
  <i>Bảng 2. Các thuật toán trong hệ thống.</i>
</p>
</div>


### 2.7. Cách giải thích kết quả

Thay vì chỉ in ra nhãn, hệ thống cho phép người dùng truy ngược từng con số. Với KNN, nhấn vào một dòng dữ liệu sẽ mở cửa sổ trình bày phép tính khoảng cách giữa điểm kiểm tra $x'$ và điểm huấn luyện $x$, theo công thức Euclid hoặc Manhattan:

$$d_E = \sqrt{\sum_{i=1}^{n}(x_i - x'_i)^2}, \qquad d_M = \sum_{i=1}^{n}\lvert x_i - x'_i\rvert$$

Từng số hạng được thay giá trị cụ thể rồi cộng dồn thành kết quả (Hình 5). Cách làm này cho phép người học tự tính lại bằng tay để đối chiếu.

```mermaid
flowchart LR
    C[Nhấn vào một dòng dữ liệu] --> T{Loại khoảng cách}
    T -->|Euclid| E[Bình phương hiệu từng thuộc tính]
    T -->|Manhattan| M[Giá trị tuyệt đối hiệu từng thuộc tính]
    E --> S[Cộng dồn các số hạng]
    M --> S
    S --> Q[Lấy căn bậc hai nếu là Euclid]
    Q --> R[MathJax dựng công thức trong cửa sổ chi tiết]
    S --> R
```
<div align="center">
<p align="center">
  <i>Hình V. Quy trình diễn giải phép tính khoảng cách.</i>
</p>
</div>

---

## 3. Đóng góp

Đóng góp chính của nghiên cứu là một công cụ thực hành đặt bảy thuật toán của hai nhóm bài toán vào cùng một giao diện. Nhờ đó, người học đổi thuật toán hay tham số mà không phải đổi công cụ, và có thể quan sát cách các phương pháp xử lý cùng một tập dữ liệu.

Đóng góp thứ hai là cách trình bày kết quả có thể truy ngược. Mỗi kết quả đi kèm công thức và các bước thay số, để người học đối chiếu với lý thuyết trong giáo trình thay vì tin vào một con số không rõ nguồn gốc.

Đóng góp thứ ba là khả năng tiếp cận. Ứng dụng đọc trực tiếp tệp Excel, không cần cài đặt hay viết mã, và dữ liệu được xử lý ngay tại máy người dùng. Cùng với quy ước dữ liệu thống nhất và cơ chế tự khóa cấu hình sai, điều này giúp rút ngắn thời gian làm quen cho người mới.

---

## 4. Kết luận

Chúng tôi đã xây dựng Website_MayHocCoBan, một ứng dụng web giúp người học thực hành các thuật toán máy học cơ bản và nhìn thấy quá trình tính toán đằng sau kết quả. Hạn chế hiện tại là toàn bộ tính toán chạy trên trình duyệt nên tốc độ giảm khi dữ liệu lớn, hệ thống chưa có các thước đo đánh giá mô hình như ma trận nhầm lẫn hay kiểm định chéo, và cần kết nối Internet để tải thư viện.

Hướng phát triển tiếp theo gồm bổ sung các thuật toán như máy vector hỗ trợ và rừng ngẫu nhiên, thêm các thước đo đánh giá, và chuyển phần tính toán nặng sang Web Worker để giao diện vẫn mượt với dữ liệu lớn hơn.

---

## 5. Tài liệu tham khảo

[1] T. Cover and P. Hart, "Nearest neighbor pattern classification," *IEEE Trans. Inf. Theory*, vol. 13, no. 1, pp. 21–27, 1967.

[2] D. J. Hand and K. Yu, "Idiot's Bayes—not so stupid after all?," *Int. Stat. Rev.*, vol. 69, no. 3, pp. 385–398, 2001.

[3] J. R. Quinlan, "Induction of decision trees," *Mach. Learn.*, vol. 1, no. 1, pp. 81–106, 1986.

[4] L. Breiman, J. H. Friedman, R. A. Olshen, and C. J. Stone, *Classification and Regression Trees*. Belmont, CA: Wadsworth, 1984.

[5] D. W. Hosmer, S. Lemeshow, and R. X. Sturdivant, *Applied Logistic Regression*, 3rd ed. Hoboken, NJ: Wiley, 2013.

[6] J. MacQueen, "Some methods for classification and analysis of multivariate observations," in *Proc. 5th Berkeley Symp. Math. Stat. Probab.*, vol. 1, 1967, pp. 281–297.

[7] S. C. Johnson, "Hierarchical clustering schemes," *Psychometrika*, vol. 32, no. 3, pp. 241–254, 1967.

[8] T. Hastie, R. Tibshirani, and J. Friedman, *The Elements of Statistical Learning*, 2nd ed. New York: Springer, 2009.

[9] C. M. Bishop, *Pattern Recognition and Machine Learning*. New York: Springer, 2006.

[10] F. Pedregosa et al., "Scikit-learn: Machine learning in Python," *J. Mach. Learn. Res.*, vol. 12, pp. 2825–2830, 2011.

[11] M. Bostock, V. Ogievetsky, and J. Heer, "D³: Data-driven documents," *IEEE Trans. Vis. Comput. Graph.*, vol. 17, no. 12, pp. 2301–2309, 2011.

[12] M. Hall et al., "The WEKA data mining software: An update," *ACM SIGKDD Explor. Newsl.*, vol. 11, no. 1, pp. 10–18, 2009.
