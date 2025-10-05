# IPC-System
Building a Digital Instrument Cluster System and a Central Control Unit (VCU) for Electric Vehicles.

## 1. Mục Tiêu và Phạm Vi Dự Án

### Mục tiêu Kỹ thuật
- **Phát triển Cụm Đồng hồ Kỹ thuật số (IPC):** Xây dựng hệ thống IPC hoàn chỉnh trên Raspberry Pi 4, có khả năng hiển thị thông tin xe linh động, theo thời gian thực. Giao diện đồ họa sẽ được thiết kế bằng framework Qt/QML.
- **Mô phỏng Bộ Điều khiển Xe (VCU):** VCU sẽ đóng vai trò là bộ não trung tâm, chịu trách nhiệm quản lý các trạng thái hoạt động của xe (Tắt, Sạc, Lái), xử lý dữ liệu từ các ECU mô phỏng, và ra quyết định điều khiển giao diện HMI.
- **Thiết lập Giao thức Truyền thông CAN:** Xây dựng một mạng giao tiếp hoàn chỉnh sử dụng giao thức CAN (Controller Area Network) để đảm bảo việc truyền dữ liệu có cấu trúc và đáng tin cậy giữa VCU (Raspberry Pi 4) và các nút ECU mô phỏng (ESP32).

### Mục tiêu Chất lượng
- **Hiệu năng:** Đạt được thời gian khởi động hệ thống nhanh (dưới 2 giây) và giao diện đồ họa mượt mà (tối thiểu 30 FPS).
- **Độ tin cậy:** Đảm bảo tính chính xác của các dữ liệu quan trọng hiển thị cho người lái như tốc độ, mức năng lượng và cảnh báo an toàn.
- **Tiêu chuẩn:** Thiết kế HMI tuân thủ các nguyên tắc về an toàn chức năng theo ISO 26262 và sử dụng các biểu tượng (tell-tales) theo tiêu chuẩn quốc tế ISO 2575.
- **Trải nghiệm Người dùng:** Tạo ra một sản phẩm có giao diện trực quan, thẩm mỹ và có khả năng tùy biến linh hoạt theo các chế độ lái (Eco, Normal, Sport).

### Trong Phạm Vi (In-Scope)
- **Phần cứng:** Một mô hình phần cứng hoàn chỉnh bao gồm VCU (Raspberry Pi 4), ECU (ESP32), các cảm biến mô phỏng (encoder, chiết áp, nút bấm), và màn hình HMI.
- **Phần mềm:**
    - Phát triển phần mềm VCU/HMI trên Raspberry Pi sử dụng C++ và Qt/QML.
    - Phát triển firmware cho ECU trên ESP32.
- **Chức năng HMI:** Phát triển giao diện người dùng đầy đủ chức năng cho các chế độ Lái xe (Eco/Normal/Sport) và sạc pin.
- **Chức năng VCU:** Mô phỏng các chức năng VCU cốt lõi bao gồm quản lý máy trạng thái xe, xử lý mã lỗi chẩn đoán (DTC) cơ bản, và quản lý các chế độ lái.

### Ngoài Phạm Vi (Out-of-Scope)
- Không thực hiện quy trình chứng nhận chính thức theo tiêu chuẩn ISO 26262.
- Không tích hợp các chức năng Hỗ trợ Lái xe Nâng cao (ADAS) phức tạp.
- Không triển khai kết nối không dây (Wi-Fi, Bluetooth) hay các dịch vụ dựa trên đám mây.
- Không phát triển các hệ thống vật lý như động cơ, pin thật hay hệ thống phanh.

## 2. Thiết Kế Cấp Cao (High-Level Design - HLD)

Kiến trúc hệ thống E/E được mô phỏng theo tiêu chuẩn ô tô hiện đại, tập trung vào vai trò chức năng của từng thành phần.

### Các Thành Phần Chính

* **VCU/IPC Domain Controller (Raspberry Pi):**
    * Là bộ não trung tâm, đóng vai trò của một **Bộ điều khiển miền (Domain Controller)**, tích hợp cả chức năng điều khiển xe (VCU) và cụm đồng hồ (IPC).
    * Chạy hệ điều hành Linux cấp cao để thực thi các logic phức tạp và quản lý hệ thống.
    * Chịu trách nhiệm:
        * Xử lý logic VCU chính: quản lý trạng thái xe, chẩn đoán lỗi (DTC).
        * Vẽ và quản lý toàn bộ giao diện HMI hiệu năng cao bằng Qt/QML.

* **Nút ECU Mô phỏng (ESP32):**
    * Đóng vai trò là một ECU cấp thấp hơn (ví dụ: Body Control Module - BCM), chuyên thu thập dữ liệu từ môi trường vật lý.
    * Chịu trách nhiệm:
        * Đọc dữ liệu thô từ các cảm biến vật lý (encoder, chiết áp, nút bấm).
        * Thực hiện xử lý sơ bộ (ví dụ: tính tốc độ từ xung encoder) và đóng gói dữ liệu vào các khung tin CAN.

* **Bus Giao tiếp (CAN Bus):**
    * Dữ liệu được truyền đi trong các **khung tin (frames)** có cấu trúc chặt chẽ (ID, Payload,...), cho phép các bộ điều khiển giao tiếp một cách có tổ chức và đáng tin cậy.

### Luồng Dữ Liệu Chính

* **Thu thập Dữ liệu (ECU Node):** ESP32 liên tục giám sát các cảm biến vật lý (encoder, chiết áp, nút bấm) để phát hiện sự thay đổi.

* **Xử lý Sơ bộ (ECU Node):** Dữ liệu thô từ cảm biến được xử lý để chuyển thành các giá trị có ý nghĩa (ví dụ: tính toán tốc độ từ xung encoder).

* **Đóng gói & Gửi (ECU Node):** Dữ liệu đã xử lý được đưa vào một cấu trúc khung tin CAN với ID và Payload cụ thể. **CAN controller** sẽ tự động xử lý việc tạo khung tin hoàn chỉnh (bao gồm CRC, bit stuffing) và gửi nó qua CAN transceiver lên bus.

* **Nhận & Giải mã (VCU):** Trên Raspberry Pi 4, lớp quản lý CAN (`CanManager`) sử dụng giao diện **`SocketCAN` của Linux kernel** để đọc các khung tin từ bus. **CAN controller (MCP2515)** sẽ tự động xác thực tính toàn vẹn của khung tin, tiếp đó đọc ID và trích xuất dữ liệu từ payload.

* **Cập nhật Mô hình Dữ liệu (VCU):** Dữ liệu đã được giải mã được sử dụng để cập nhật trạng thái trong lớp dữ liệu trung tâm (`VehicleModel`). Lớp này phát ra các tín hiệu (Qt signals) để thông báo rằng dữ liệu đã thay đổi.

* **Cập nhật Giao diện (HMI):** Các thành phần giao diện QML, được liên kết với các thuộc tính trong `VehicleModel`, sẽ tự động nhận được tín hiệu thay đổi, update giao diện với dữ liệu mới, đảm bảo hiệu suất tối ưu.

## 3. Danh Sách Linh Kiện (Bill of Materials - BOM)

Dưới đây là danh sách các linh kiện phần cứng cần thiết để xây dựng hệ thống.

| Mục | Vai trò | Linh kiện | Số lượng | Ghi chú |
| :-- | :--- | :--- | :--- | :--- |
| 1 | VCU/IPC Controller | Raspberry Pi 4 Model B | 1 | Bộ não trung tâm của hệ thống. |
| 2 | ECU Node | ESP32 DevKit | 1 | Đọc cảm biến và giao tiếp CAN. |
| 3 | Màn hình HMI | Màn hình cảm ứng 7 inch, HDMI | 1 | Hiển thị giao diện người-máy (HMI). |
| 4 | Module CAN (cho Pi) | Module MCP2515 | 1 | Bổ sung khả năng giao tiếp CAN cho Raspberry Pi 4. |
| 5 | Module CAN (cho ESP32) | Module SN65HVD230 | 1 | Chuyển đổi tín hiệu logic 3.3V từ ESP32 sang tín hiệu vi sai của CAN bus. |
| 6 | Cảm biến Tốc độ | Encoder quay (Rotary Encoder) | 1 | Mô phỏng tín hiệu tốc độ bánh xe. |
| 7 | Cảm biến Mức Pin | Chiết áp (Potentiometer) 10K Ohm | 1 | Mô phỏng tín hiệu analog của mức pin. |
| 8 | Nút bấm | Nút nhấn nhả (Push Buttons) | ~10-20 | Mô phỏng các thao tác của người lái. |
| 9 | Breadboard & Dây cắm | Breadboard và bộ dây cắm | 1 bộ | Dùng để kết nối các linh kiện với nhau. |
| 10 | Nguồn & Cáp | Nguồn USB-C, Micro USB, cáp HDMI | 1 bộ | Cung cấp năng lượng và tín hiệu. |
| 11 | Báo hiệu Trạng thái | Đèn LED (LEDs) | ~10-20 | Dùng để báo hiệu nguồn, trạng thái hoạt động, truyền/nhận CAN, lỗi, xi nhan,..... |
| 12 | Bảo vệ LED/GPIO | Điện trở (Resistors) 220/330 Ohm | ~5-10 | Giới hạn dòng cho LED, bảo vệ GPIO. |



