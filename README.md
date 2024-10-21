
# Bài 4: Communication Protocols

### 1.Truyền nhận dữ liệu là gì?

> Là quá trình trao đổi tín hiệu điện áp của MCU A đến MCU B. Gồm các tín hiệu 0 và 1(0v và 3.3V hoặc 5V).

_VD: Đối với 1 chuỗi như `hello`_

- MCU A sẽ dịch tuần tự từ kí tự `h` sang mã nhị phân 1 0(theo tiêu chuẩn ASCII) đến kí tự `o` qua MCU B.

- Việc truyền tín hiệu, sẽ truyền từng bit, bằng cách chênh lệch tăng hoặc giảm điện áp.

- **Nhưng:** Đối với tín hiệu có 3 bit 0 (hoặc 3 bit 1) liên tiếp giống nhau như (h: 0b 0110 1000). Thì việc truyền tín hiệu sẽ không có sự chênh lệch tín hiệu điện áp nào. Sẽ xảy ra sai xót trong truyền nhận dữ liệu.

Vì vậy, để phân biệt các bit liền kề giống thì các chuẩn giao tiếp ra đời như: SPI, I2C, UART,...

### 2. Ba chế độ của truyền nhận dữ liệu:

- **Đơn công**: Dữ liệu truyền theo 1 chiều, từ thiết bị gửi đến thiết bị nhận mà không có phản hồi ngược lại (Một thiết bị chỉ có thể truyền hoặc nhận).

_VD:_ Máy phát và máy thu (kết nối micro đến loa. Chỉ có truyền âm thanh một chiều từ micro đến loa).

- **Bán song công**: Dữ liệu truyền theo 2 chiều, nhưng không đồng thời. Tại 1 thời điểm chỉ một bên có thể truyền.

_VD:_ Bộ đàm.

- **Song công**: Dữ liệu truyền theo 2 chiều đồng thời; cả 2 bên có thể truyền hoặc nhận cùng một thời điểm. ->> SPI

_VD:_ Cuộc gọi điện thoại.

### 3. SPI
(SPI - Serial Peripheral Interface)

> Là chuẩn gao tiếp nối tiếp, đồng bộ
>
> Hoạt động ở chế độ song công (Có thể truyền hoặc nhận cùng 1 thời điểm).
>
> Một Master có thể giao tiếp với nhiều Slave (Master: Điều khiển quá trình giao tiếp SPI; Slave: là thiết bị thực hiện theo các yêu cầu của Master )
> 
> Sử dụng 4 giây giao tiếp: SCK, MISO, MOSI, SS.

->> Đồng bộ: Là sự thống nhất của 2 MCU thời điểm nào là đọc, khi nào là ghi tín hiệu.
![](https://github.com/hthuan02/Embedded_Automotive/blob/main/Bai4_Comunication%20Protocols/SPI.jpg)

- **SCK**(Serial Clock): Là dây tạo ra tín hiệu đồng bộ. Master tạo ra xung tín hiệu truyền đến Slave nhận.

-  **MISO**(Master Input Slave Ouput): Master nhận dữ liệu - Slave đẩy dữ liệu ra. Tín hiệu tạo bởi Slave và truyền đến Master.

-  **MOSI**(Master Output Slave Input): Master truyền đẩy dữ liệu - Slave nhận. Tín hiệu tạo bởi Master và truyền đến Slave nhận.

-  **SS**(Slave Select/CS-Chip Select): Là chân giúp Master chọn được những Slave để giao tiếp, truyền dữ liệu. Mỗi Slave ứng với 1 chân SS, nên Master muốn giao tiếp với Slave nào thì sẽ kéo dây SS về mức 0.

<img src="https://github.com/hthuan02/Embedded_Automotive/blob/main/Bai4_Comunication%20Protocols/CPOL_CPHA.png" alt="CPOL và CPHA" width="550"/>










### 4. I2C

### 5. UART
