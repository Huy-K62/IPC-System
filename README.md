# IPC-System
Building a Digital Instrument Cluster System and a Central Control Unit (VCU) for Electric Vehicles
### Tuần 1: Setup môi trường, xây dựng FW cho ECU

#### 🎯 Mục tiêu:
Xây dựng sơ bộ firmware trên ESP32 và xây dựng một chương trình nhận dữ liệu ổn định trên Pi. Mục tiêu là chứng minh toàn bộ chuỗi giao tiếp vật lý và phần mềm cơ bản hoạt động.

#### 📝 Nhiệm vụ:
- [ ] **Môi trường:** Cài đặt ESP-IDF, QT, kiểm tra các linh kiện. 
- [ ] **ESP32:** Lập trình đọc tất cả cảm biến: encoder (tốc độ), chiết áp (mức pin), và các nút bấm (chuyển số, chế độ lái) - với các tính năng liên quan đến nút bấm, xây dựng ở mức cơ bản, hoàn thiện dần sau. 
- [ ] **ESP32:** Hoàn thiện logic tính toán tốc độ từ encoder theo công thức.
- [ ] **ESP32:** Định nghĩa và gửi đi tất cả các khung tin CAN cần thiết theo "Ma trận Thông điệp". 
- [ ] **Raspberry Pi:** Hoàn tất 100% môi trường cross-compile Qt/C++. 
- [ ] **Raspberry Pi:** Xây dựng lớp `CanManager` trong C++ để nhận và giải mã tất cả tin nhắn CAN từ ESP32.

#### ✅ Kết quả Yêu cầu/Đạt được:
Terminal trên Pi hiển thị chính xác và theo thời gian thực toàn bộ dữ liệu (tốc độ, mức pin, cấp số, chế độ lái...) khi tương tác với các cảm biến trên ESP32.

---

### Tuần 2: Lõi VCU và Model Dữ Liệu

#### 🎯 Mục tiêu:
Xây dựng bộ xử lý logic trung tâm và cấu trúc dữ liệu trên Pi, tách biệt hoàn toàn với lớp giao diện.

#### 📝 Nhiệm vụ:
- [ ] **Raspberry Pi (C++):** Xây dựng lớp `VehicleModel` theo mẫu Singleton cho mọi dữ liệu của xe. 
- [ ] **Raspberry Pi (C++):** Tích hợp `CanManager` để cập nhật dữ liệu trực tiếp vào `VehicleModel`.
- [ ] **Raspberry Pi (C++):** Xây dựng `VCUStateMachine` hoàn chỉnh để quản lý các trạng thái xe (OFF, ACC, READY_TO_DRIVE, CHARGING, FAULT) như trong bảng máy trạng thái.

#### ✅ Kết quả Yêu cầu/Đạt được:
Ứng dụng C++ trên Pi có thể tự động chuyển đổi trạng thái một cách logic. Log trên terminal phải thể hiện `VCUStateMachine` đã chuyển sang đúng trạng thái (ví dụ: `CHARGING`) khi có tin nhắn CAN tương ứng.

---

### Tuần 3: Giao Diện Người - Máy (HMI LLD)

#### 🎯 Mục tiêu:
Xây dựng giao diện, kết nối dữ liệu thật với giao diện đồ họa.

#### 📝 Nhiệm vụ:
- [ ] **Raspberry Pi (C++/QML):** Đăng ký đối tượng `VehicleModel` và `VCUStateMachine` để QML có thể truy cập. 
- [ ] **Raspberry Pi (QML):** Thiết kế màn hình lái xe chính (`DrivingScreen.qml`) với đầy đủ các thành phần: đồng hồ tốc độ, mức năng lượng, cấp số, Odometer, Trip Meter, và khu vực đèn báo. 
- [ ] **Raspberry Pi (QML):** Triển khai logic QML để thay đổi giao diện (màu sắc, bố cục) khi chế độ lái thay đổi (ECO, NORMAL, SPORT). 
- [ ] **Raspberry Pi (QML):** Thực hiện hoạt ảnh khởi động "quét kim đồng hồ" và "kiểm tra đèn báo".

#### ✅ Kết quả Yêu cầu/Đạt được:
Một giao diện lái xe trên màn hình Pi, phản hồi với dữ liệu thật từ ESP32. Các chế độ lái có thể được chuyển đổi và giao diện thay đổi tương ứng.

---

### Tuần 4: Hoàn Thiện HMI và Luồng Logic

#### 🎯 Mục tiêu:
Xây dựng các màn hình phụ và luồng điều khiển hoàn chỉnh cho HMI, đảm bảo trải nghiệm người dùng liền mạch.

#### 📝 Nhiệm vụ:
- [ ] **Raspberry Pi (QML):** Thiết kế màn hình sạc pin (`ChargingScreen.qml`) với đầy đủ thông tin yêu cầu. 
- [ ] **Raspberry Pi (QML):** Thiết kế lớp phủ cho các cảnh báo khẩn cấp (`WarningOverlay.qml`). 
- [ ] **Raspberry Pi (C++/QML):** Viết logic điều hướng chính trong `main.qml` để hiển thị đúng màn hình (Chào mừng, Lái xe, Sạc pin, Cảnh báo lỗi) dựa trên trạng thái hiện tại của `VCUStateMachine`.

#### ✅ Kết quả Yêu cầu/Đạt được:
HMI hoàn chỉnh về mặt luồng hoạt động. Hệ thống có thể tự động chuyển đổi giữa tất cả các màn hình chính một cách chính xác theo ngữ cảnh của xe.

---

### Tuần 5: Logic Chẩn Đoán Lỗi (DTC Management)

#### 🎯 Mục tiêu:
Mô phỏng quy trình xử lý lỗi chẩn đoán "two-trip" theo chuẩn ô tô, một tính năng phức tạp và thực tế. 

#### 📝 Nhiệm vụ:
- [ ] **ESP32:** Mô phỏng một lỗi cảm biến và gửi tin nhắn CAN chứa mã lỗi DTC.
- [ ] **ESP32:** Hoàn thiện toàn bộ logic còn lại cho firmware. 
- [ ] **Raspberry Pi (C++):** Xây dựng hoàn chỉnh lớp `DTCManager`.
- [ ] **Raspberry Pi (C++):** Triển khai logic "two-trip" để xác nhận lỗi: lưu trạng thái "Pending", và chuyển sang "Confirmed" ở chu trình lái thứ hai. 
- [ ] **Raspberry Pi (C++):** Lập trình logic lưu trữ DTC vào file. 
- [ ] **Raspberry Pi (C++/QML):** Tích hợp `DTCManager` với HMI để điều khiển bật/tắt đèn báo lỗi (MIL). 

#### ✅ Kết quả Yêu cầu/Đạt được:
Tính năng chẩn đoán hoạt động như tài liệu mô tả. Có thể mô phỏng lỗi trên ESP32, khởi động lại hệ thống, và thấy đèn MIL bật sáng chính xác ở chu trình lái thứ hai.

---

### Tuần 6: Các Tính Năng Tính Toán và Lưu Trữ

#### 🎯 Mục tiêu:
Hoàn thiện các thuật toán tính toán phức tạp (DTE) và logic lưu trữ dữ liệu bền bỉ (Odometer).

#### 📝 Nhiệm vụ:
- [ ] **Raspberry Pi (C++):** Lập trình chức năng lưu trữ giá trị Odometer vào bộ nhớ non-volatile và đọc lại khi khởi động. 
- [ ] **Raspberry Pi (C++):** Lập trình thuật toán "pha trộn" để tính toán DTE một cách ổn định. 
- [ ] **Raspberry Pi (QML):** Thêm các thành phần giao diện để hiển thị DTE, Odometer, Trip Meter và nút reset cho Trip Meter. 

#### ✅ Kết quả Yêu cầu/Đạt được:
Odometer không bị reset sau mỗi lần tắt máy. DTE hiển thị một giá trị hợp lý và ổn định, thay đổi linh hoạt theo điều kiện lái.

---

### Tuần 7: Tối Ưu Hóa Hiệu Năng và Ổn Định

#### 🎯 Mục tiêu:
Hoàn thiện nốt toàn bộ logic và giao diện, tập trung vào các yêu cầu phi chức năng (NFRs) để hệ thống chạy mượt và ổn định. 

#### 📝 Nhiệm vụ:
- [ ] **Tối ưu Thời gian Khởi động:** Phân tích log boot, loại bỏ các dịch vụ không cần thiết, và cấu hình ứng dụng Qt tự khởi động cùng hệ thống (`systemd`) để đạt mục tiêu dưới 2 giây (tùy chọn sẽ làm nếu có đủ thời gian). 
- [ ] **Tối ưu Hiệu năng HMI:** Sử dụng các công cụ profiling của Qt để tối ưu hóa QML, đảm bảo tốc độ làm tươi luôn trên 30 FPS.
- [ ] **Tăng độ tin cậy:** Triển khai cơ chế watchdog để tự khởi động lại ứng dụng nếu bị treo.
- [ ] **Unit Test:** Viết các unit test cơ bản cho các lớp logic quan trọng (`DTCManager`, các hàm tính toán).

#### ✅ Kết quả Yêu cầu/Đạt được:
Hệ thống khởi động nhanh, giao diện mượt mà trong mọi tình huống, và có khả năng tự phục hồi. Các yêu cầu NFR chính được đáp ứng.

---

### Tuần 8: Hoàn Thiện & Tính Năng Mở Rộng (AI - Optional)

#### 🎯 Mục tiêu:
Kiểm thử, tối ưu và thử nghiệm tính năng nâng cao (nếu còn thời gian).

#### 📝 Nhiệm vụ:
- [ ] **Kiểm thử hệ thống:** Thực hiện các kịch bản kiểm thử quan trọng nhất trong tài liệu.
- [ ] **Dọn dẹp và tối ưu code:** Rà soát, thêm bình luận, chuẩn hóa code cho cả hai nền tảng. 
- [ ] **(Tùy chọn) Tích hợp AI - Điều khiển bằng giọng nói:**
    - [ ] Nghiên cứu và tích hợp một thư viện nhận dạng giọng nói offline nhẹ cho Linux.
    - [ ] Xây dựng 1-2 câu lệnh đơn giản (ví dụ: "Hey IPC, switch to Sport mode", "Hey IPC, what's my range?").
    - [ ] Tích hợp lệnh thoại để thay đổi trạng thái trong `VehicleModel`.

#### ✅ Kết quả Yêu cầu/Đạt được:
Một sản phẩm hoàn chỉnh, ổn định, đáp ứng mọi yêu cầu cốt lõi và sẵn sàng để demo.
