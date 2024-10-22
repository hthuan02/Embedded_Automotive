
# Bài 4: Communication Protocols

## 1.Truyền nhận dữ liệu là gì?

> Là quá trình trao đổi tín hiệu điện áp của MCU A đến MCU B. Gồm các tín hiệu 0 và 1(0v và 3.3V hoặc 5V).

_VD: Đối với 1 chuỗi như `hello`_

- MCU A sẽ dịch tuần tự từ kí tự `h` sang mã nhị phân 1 0(theo tiêu chuẩn ASCII) đến kí tự `o` qua MCU B.

- Việc truyền tín hiệu, sẽ truyền từng bit, bằng cách chênh lệch tăng hoặc giảm điện áp.

- **Nhưng:** Đối với tín hiệu có 3 bit 0 (hoặc 3 bit 1) liên tiếp giống nhau như (h: 0b 0110 1000). Thì việc truyền tín hiệu sẽ không có sự chênh lệch tín hiệu điện áp nào. Sẽ xảy ra sai xót trong truyền nhận dữ liệu.

Vì vậy, để phân biệt các bit liền kề giống thì các chuẩn giao tiếp ra đời như: SPI, I2C, UART,...

## 2. Ba chế độ của truyền nhận dữ liệu:

- **Đơn công**: Dữ liệu truyền theo 1 chiều, từ thiết bị gửi đến thiết bị nhận mà không có phản hồi ngược lại (Một thiết bị chỉ có thể truyền hoặc nhận).

_VD:_ Máy phát và máy thu (kết nối micro đến loa. Chỉ có truyền âm thanh một chiều từ micro đến loa).

- **Bán song công**: Dữ liệu truyền theo 2 chiều, nhưng không đồng thời. Tại 1 thời điểm chỉ một bên có thể truyền.

_VD:_ Bộ đàm.

- **Song công**: Dữ liệu truyền theo 2 chiều đồng thời; cả 2 bên có thể truyền hoặc nhận cùng một thời điểm. ->> SPI

_VD:_ Cuộc gọi điện thoại.

## 3. SPI
(SPI - Serial Peripheral Interface)

> Là chuẩn gao tiếp nối tiếp, đồng bộ
>
> Hoạt động ở chế độ song công (Có thể truyền hoặc nhận cùng 1 thời điểm).
>
> Một Master có thể giao tiếp với nhiều Slave (Master: Điều khiển quá trình giao tiếp SPI; Slave: là thiết bị thực hiện theo các yêu cầu của Master )
> 
> Sử dụng 4 giây giao tiếp: SCK, MISO, MOSI, SS.

->> Đồng bộ: Là sự thống nhất của 2 MCU thời điểm nào là đọc, khi nào là ghi tín hiệu.
![](https://github.com/hthuan02/Embedded_Automotive/blob/main/Bai4_Comunication%20Protocols/img/SPI.jpg)

- **SCK**(Serial Clock): Là dây tạo ra tín hiệu đồng bộ. Master tạo ra xung tín hiệu truyền đến Slave nhận.

-  **MISO**(Master Input Slave Ouput): Master nhận dữ liệu - Slave đẩy dữ liệu ra. Tín hiệu tạo bởi Slave và truyền đến Master.

-  **MOSI**(Master Output Slave Input): Master truyền đẩy dữ liệu - Slave nhận. Tín hiệu tạo bởi Master và truyền đến Slave nhận.

-  **SS**(Slave Select/CS-Chip Select): Là chân giúp Master chọn được những Slave để giao tiếp, truyền dữ liệu. Mỗi Slave ứng với 1 chân SS, nên Master muốn giao tiếp với Slave nào thì sẽ kéo dây SS về mức 0.

<img src="https://github.com/hthuan02/Embedded_Automotive/blob/main/Bai4_Comunication%20Protocols/img/Sodo_SPI.png" alt=" " width="440"/>


## Quá trình truyền - nhận dữ liệu của SPI?

- Quá trình truyền - nhận Bit dữ liệu trong SPI: Chân SCK là chân tín hiệu đồng bộ ( 1 xung Clock). Mỗi lần gửi dữ liệu là kèm 1 xung Clock.

 _VD:_ MCU A(Master) gửi dữ liệu đến MCU B(Slave), thì mỗi lần dây MOSI gửi 1 bit dữ liệu đi thì sẽ kèm 1 xung Clock (Khi nào xung Clock lên 1 thì MCU B đọc được).

▶️▶️▶️ **Đây là quá trình đầy đủ**

- Bắt đầu quá trình, thì 1 Master chọn 1 Slave để giao tiếp, mà để chọn đc Slave giao tiếp thì Master kéo chân SS/CS của Slave tương ứng xuống mức 0, báo hiệu muốn truyền - nhận.

- Sau khi chọn được Slave, Master sẽ cấp cho Slave 1 xung Clock, tùy vào chế độ Master cài đặt. Cứ 1 xung clock truyền đi thì đồng thời gửi tín hiệu đi qua chân (MOSI) và nhận tín hiệu này vào bằng chân (MISO).
->> Tại 1 thời điểm, vừa nhận vừa truyên nên nó là giao tiếp song công.

- Với mỗi Clock truyền đi, thì sẽ có 1 bit được truyền từ Master -> Slave, hoặc 1 bit Master nhận từ Slave.

- Sau khi truyền 1 bit kèm 1 clock, các bên nhận sẽ cập nhật lại thanh ghi và dịch 1 bit, và lặp lại quá trình đến khi đủ 8 bit trong thanh ghi. Cuối cùng, khi thanh ghi đủ 8 bit rồi thì đưa chân SS/CS lên lại 1, để dừng quá trình giao tiếp.

## Bốn chế độ hoạt động của SPI?

**CPOL**: Xác định mức điện áp của tín hiệu SCK.

- CPOL = 0: Ở trạng thái rãnh SCK = 0, khi truyền dữ liệu SCK lên 1.

- CPOL = 1: Ở trạng thái rãnh SCK = 1, khi truyền dữ liệu SCK xuống 0.

**CPHA**: Giúp chúng ta biết được thời điểm nào đọc dữ liệu, thời điểm nào chuyển dữ liệu.

- CPHA = 0: Dữ liệu sẽ đến trước khi tín hiệu xung Clock đọc được.

- CPHA = 1: Clock được hình thành trước khi dữ liệu đến.

▶️▶️▶️Dựa vào CPOL và CPHA, chúng ta có 4 chế độ SPI

<img src="https://github.com/hthuan02/Embedded_Automotive/blob/main/Bai4_Comunication%20Protocols/img/CPOL_CPHA.png" alt=" " width="660"/>

## 4. I2C
(Inter-Integrated Circuit)

> Là chuẩn giao tiếp nối tiếp, đồng bộ.
>
> Hoạt động ở chế độ bán song công.
>
> Một Master có thể giao tiếp với nhiều Slave.
> 
> Có 2 dây kết nối: SCL, SDA.

<img src="https://github.com/hthuan02/Embedded_Automotive/blob/main/Bai4_Comunication%20Protocols/img/unnamed.png" alt=" " width="600"/>

- **SCL**(Serial Clock): Là dây tạo tín hiệu đồng bộ, truyền-nhận dữ liệu với các Slave.

- **SDA**(Serial): Chân chứa dữ liệu được truyền đi.

## Chế độ hoạt động I2C?

> Khác với SPI chế độ hoạt động là (0) (1), còn ở I2C chế độ hoạt động là Open Drain. 

**Open Drain**: Khi I2C muốn điều khiển đường bus, sẽ hạ xuống là 0. Khi không điều khiển, thì sẽ thả trôi đường ở mức điện áp floating(sẽ không hiểu là mức 0 hay 1). Nên cần điện trở kéo lên nguồn thì bus mới lên 1.

## Quá trình truyền - nhận dữ liệu của I2C?

Giống với SPI, 1 bit truyền đi thì kèm theo 1 Clock. Còn bên nhận thấy 1 Clock thì nhận được 1 Bit để lưu vào bộ nhớ.(phút 60)

 

## 5. UART
