<div align="center">
    
## Hệ thống web trực quan hóa quá trình học và dự đoán của các thuật toán máy học cơ bản

**Link website** - [huynhthanhphong231004it.github.io/Website_MayHocCoBan](https://huynhthanhphong231004it.github.io/Website_MayHocCoBan/)

</div>

---

<p>
    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**Facbook**: https://www.facebook.com/huynh.thanh.phong.561667 <br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**Tiktok**: https://www.tiktok.com/@reorioll <br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**Youtobe**: https://www.youtube.com/@ReoRioll-2304CICTCTU <br>
</p>
<br>

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
    %% Khai báo các nút - dùng <br/> để chữ không bị cắt ngang
    D["Dữ liệu .xlsx"]:::data
    
    A["Thư viện<br/>Ultralytics"]:::lib
    B["Nhãn dự đoán<br/>(Khó hiểu quá trình)"]:::output1
    
    C["Website<br/>MayHocCoBan"]:::web
    E["Nhãn dự đoán"]:::output2
    F["Công thức và<br/>các bước tính"]:::output2
    G["Bảng số liệu<br/>và biểu đồ"]:::output2

    %% Mối liên kết
    D --> A
    A --> B
    
    D --> C
    C --> E
    C --> F
    C --> G

    %% Định dạng style và khoảng đệm (padding) để chữ luôn hiển thị trọn vẹn
    classDef data fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#0D47A1,font-weight:bold,rx:15px,ry:5px;
    classDef lib fill:#FFFDE7,stroke:#FBC02D,stroke-width:2px,color:#F57F17,font-weight:bold,rx:5px,ry:5px;
    classDef web fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20,font-weight:bold,rx:5px,ry:5px;
    classDef output1 fill:#FFEBEE,stroke:#C62828,stroke-width:2px,color:#B71C1C,rx:5px,ry:5px;
    classDef output2 fill:#F3E5F5,stroke:#7B1FA2,stroke-width:2px,color:#4A148C,rx:5px,ry:5px;
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
flowchart LR
    %% Style chuẩn bài báo khoa học (Paper style)
    classDef ui fill:#F0F4F8,stroke:#102A43,stroke-width:1.5px,color:#102A43;
    classDef data fill:#FFFbea,stroke:#B7791F,stroke-width:1.5px,color:#744210;
    classDef algo fill:#EBF8FF,stroke:#2B6CB0,stroke-width:1.5px,color:#1A365D;
    classDef vis fill:#F3E8FF,stroke:#6B46C1,stroke-width:1.5px,color:#322659;

    subgraph UI ["Lớp Giao Diện (Presentation Layer)"]
        direction TB
        F1["Biểu mẫu chọn dữ liệu,<br/>thuật toán & tham số"]
        F2["Cửa sổ hướng dẫn"]
        F3["Vùng hiển thị<br/>kết quả chi tiết"]
    end

    subgraph DATA ["Lớp Dữ Liệu (Data Layer)"]
        direction TB
        S["SheetJS<br/><i>(Đọc file .xlsx)</i>"]
        M["math.js<br/><i>(Tính toán số học)</i>"]
    end

    subgraph ALGO ["Lớp Thuật Toán (Business/Algo Layer)"]
        direction TB
        T1["ThuatToan.js<br/><b>(Học có giám sát)</b>"]
        T2["ThuatToan2.js<br/><b>(Học không giám sát)</b>"]
    end

    subgraph VIS ["Lớp Trực Quan Hóa (Visualization Layer)"]
        direction TB
        V1["Chart.js / D3.js<br/><i>(Vẽ biểu đồ)</i>"]
        V2["MathJax<br/><i>(Hiển thị công thức)</i>"]
    end

    %% Luồng liên kết dữ liệu
    F1 --> S
    S --> T1
    S --> T2
    M -.-> T1
    M -.-> T2
    T1 --> F3
    T2 --> F3
    F3 --> V1
    F3 --> V2

    %% Áp dụng kiểu dáng
    class F1,F2,F3 ui;
    class S,M data;
    class T1,T2 algo;
    class V1,V2 vis;
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
    %% Định nghĩa phong cách chuẩn Paper (Academic style)
    classDef decision fill:#F0F4F8,stroke:#102A43,stroke-width:1.5px,color:#102A43,font-weight:bold;
    classDef supervised fill:#EBF8FF,stroke:#2B6CB0,stroke-width:1.5px,color:#1A365D;
    classDef unsupervised fill:#F3E8FF,stroke:#6B46C1,stroke-width:1.5px,color:#322659;

    %% Các nút phân nhánh chính
    S{"Loại tập dữ liệu"}:::decision
    
    A{"Thuật toán<br/><b>(Có giám sát)</b>"}:::supervised
    U{"Thuật toán<br/><b>(Không giám sát)</b>"}:::unsupervised

    %% Các nhánh có giám sát (Chủ động ngắt dòng để không bị xén chữ)
    A -->|KNN| A1["Hiện ô K và<br/>loại khoảng cách"]
    A -->|Hồi quy tuyến tính| A2["Khóa nhãn:<br/>liên tục"]
    A -->|Hồi quy logistic| A3["Khóa nhãn:<br/>rời rạc"]
    A -->|Naive Bayes, cây quyết định| A4["Cho chọn nhãn<br/>rời rạc hoặc liên tục"]

    %% Các nhánh không giám sát
    U -->|K-means| U1["Hiện ô số cụm K"]
    U -->|Phân cụm phân cấp| U2["Hiện chiến lược:<br/>bottom-up hoặc top-down"]

    %% Kết nối từ gốc
    S -->|Có giám sát| A
    S -->|Không giám sát| U

    %% Gán class màu sắc chi tiết
    class A1,A2,A3,A4 supervised;
    class U1,U2 unsupervised;
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
    %% Định nghĩa phong cách chuẩn Paper (Academic style)
    classDef action fill:#F0F4F8,stroke:#102A43,stroke-width:1.5px,color:#102A43,font-weight:bold;
    classDef decision fill:#FFFBEA,stroke:#B7791F,stroke-width:1.5px,color:#744210,font-weight:bold;
    classDef process fill:#EBF8FF,stroke:#2B6CB0,stroke-width:1.5px,color:#1A365D;
    classDef output fill:#F3E8FF,stroke:#6B46C1,stroke-width:1.5px,color:#322659;

    %% Các nút trong sơ đồ (đã ngắt dòng chủ động tránh xén chữ)
    C["Nhấn vào một<br/>dòng dữ liệu"]:::action
    
    T{"Loại<br/>khoảng cách"}:::decision
    
    E["Bình phương hiệu<br/>từng thuộc tính"]:::process
    M["Giá trị tuyệt đối hiệu<br/>từng thuộc tính"]:::process
    
    S["Cộng dồn<br/>các số hạng"]:::process
    
    Q["Lấy căn bậc hai<br/>(Dành cho Euclid)"]:::process
    
    R["MathJax dựng công thức<br/>trong cửa sổ chi tiết"]:::output

    %% Luồng liên kết dữ liệu
    C --> T
    
    T -->|Euclid| E
    T -->|Manhattan| M
    
    E --> S
    M --> S
    
    S --> Q
    Q --> R
    S --> R
```
<div align="center">
<p align="center">
  <i>Hình V. Quy trình diễn giải phép tính khoảng cách mô phỏng cho giải thuật KNN.</i>
</p>
</div>

---

## 3. Kết luận

Đóng góp chính của nghiên cứu là một công cụ thực hành đặt bảy thuật toán của hai nhóm bài toán vào cùng một giao diện. Nhờ đó, người học đổi thuật toán hay tham số mà không phải đổi công cụ, và có thể quan sát cách các phương pháp xử lý cùng một tập dữ liệu. Đóng góp thứ hai là cách trình bày kết quả có thể truy ngược. Mỗi kết quả đi kèm công thức và các bước thay số, để người học đối chiếu với lý thuyết trong giáo trình thay vì tin vào một con số không rõ nguồn gốc. Đóng góp thứ ba là khả năng tiếp cận. Ứng dụng đọc trực tiếp tệp Excel, không cần cài đặt hay viết mã, và dữ liệu được xử lý ngay tại máy người dùng. Cùng với quy ước dữ liệu thống nhất và cơ chế tự khóa cấu hình sai, điều này giúp rút ngắn thời gian làm quen cho người mới.

Chúng tôi đã xây dựng Website_MayHocCoBan, một ứng dụng web giúp người học thực hành các thuật toán máy học cơ bản và nhìn thấy quá trình tính toán đằng sau kết quả. Hạn chế hiện tại là toàn bộ tính toán chạy trên trình duyệt nên tốc độ giảm khi dữ liệu lớn, hệ thống chưa có các thước đo đánh giá mô hình như ma trận nhầm lẫn hay kiểm định chéo, và cần kết nối Internet để tải thư viện. Hướng phát triển tiếp theo gồm bổ sung các thuật toán như máy vector hỗ trợ và rừng ngẫu nhiên, thêm các thước đo đánh giá, và chuyển phần tính toán nặng sang Web Worker để giao diện vẫn mượt với dữ liệu lớn hơn.
