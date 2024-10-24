# Bài 1: Setup KeilC - stm32f103c8

## VD: Blink Led PC13 bằng thanh ghi
(PC13: Chân 13 của GPIO C13)

> Cấp xung clock cho ngoại vi
>
> Cấu hình chế độ chân ngoại vi
>
> Sử dụng ngoại vi

### 1. Cấp xung clock cho ngoại vi

- Cấp xung clock cho GPIO từ APB2 bằng thanh ghi RCC_APB2ENR.
  
  (RCC: Module đang làm việc; APB: Tên đường bus; 2ENR: Chức năng ngoại vi)

```c
   RCC->APB2ENR |= RCC_APB2ENR_IOPCEN; // Cap clock cho ngoai vi
```

### 2. Cấu hình chế độ chân ngoại vi

- Sử dụng 2 thanh ghi GPIOx_CRL và GPIOx_CRH để cấu hình chân PC13 làm ngõ ra Push-Pull, tốc độ 50MHz.

  (Một GPIO có 16 chân, chia 2 thanh ghi CRL: 0-7; CRH: 8-15)

- Nháy LED PC13-> Dùng thanh ghi CRH (**GPIOx_CRH**), CRH có 2 loại là CRFx và MODEx.

*Push-pull: 1 0

*Open drain: Chỉ kéo xuống 0, không làm gì thì floating(lơ lửng).

![](https://github.com/hthuan02/Embedded_Automotive/blob/main/Bai1_Setup-KeilC/cau_hinh_che_do_chan.png)


### 3. Sử dụng ngoại vi
 
- Điều khiển LED: Sử dụng thanh ghi GPIOx_ODR để điều khiển LED nhấp nháy qua mã 





# Bài 2: GPIO



# Bài 3: Interrupt & Timer

## I. Interrupt (Ngắt)
### 1. Ngắt là gì?

> Ngắt là 1 sự kiện khẩn cấp xảy ra trong hoặc ngoài MCU. Nó yêu cầu MCU dừng chương trình chính `main()` và thực thi **chuơng trình ngắt.**

**Các loại ngắt thông dụng:**

- Mỗi ngắt có 1 trình phục vụ ngắt, mỗi  khi có ngắt thì trình phục vụ ngắt sẽ yêu cầu MCU thực thi hàm ngắt đó.

- Các hàm ngắt có địa chỉ cố định trong bộ nhớ -> Vector Ngắt.

- Các loại ngắt thông dụng: Reset, Ngắt ngoài, Timer, Ngắt truyền thông.

![](https://github.com/hthuan02/Embedded_Automotive/blob/main/Bai3_Interrupt_Timer/img/Ngat.png)

### 2. Ngắt ngoài

> Xảy ra khi có **thay đổi điện áp** trên các chân GPIO được **cấu hình làm ngõ vào ngắt.**

Có 4 loại ngắt ngoài:

- **LOW:** Kích hoạt ngắt khi chân ở mức thấp.
 
- **HIGH:** Kích hoạt ngắt khi chân ở mức cao.

- **Rising:** Ngắt khi trạng thái của chân chuyển từ thấp -> cao.

- **Falling:** Ngắt khi trạng thái của chân chuyển từ cao -> thấp.

### 3. Ngắt Timer

> Xảy ra khi giá trị trong thanh ghi đếm của timer tràn.
>
> Vì là ngắt nội trong MCU, phải reset giá trị thanh ghi timer để có thể tạo lần ngắt tiếp theo.

(Nếu không reset giá trị thanh ghi đếm thì ngắt timer sẽ đếm từ 0-255 rồi mới reset về 0, chứ không reset khi đến giá trị chỉ định)

###  4. Ngắt truyền thông

> Xảy ra khi có sự kiện **truyền/nhận** dữ liệu giữa MCU với các thiết bị bên ngoài hay MCU với nhau.
>
> Các phương thức giao tiếp như: SPI, I2C, UART,... được sử dụng để ngát truyền thông.

### 5. Độ ưu tiên ngắt

>  Độ ưu tiên ngắt sẽ khác nhau ở mỗi ngắt.
>
>  Khi có nhiều ngắt xảy ra đồng thời, ngắt nào có độ ưu tiên cao hơn thì sẽ thực thi trước.

Trong STM32 ngắt có số ưu tiên thấp thì độ ưu tiên càng cao.


**PC(Program Counter):** Là thanh ghi luôn trỏ đến ô nhớ chứa lệnh tiếp theo trong chương trình.

_VD: Quy trình chạy của Program Counter:_
![](https://github.com/hthuan02/Embedded_Automotive/blob/main/Bai3_Interrupt_Timer/img/DO_UU_TIEN.png)

```
   PC: 0x01 -> 0x02 -> 0x03 pc:0x04: Lưu vào Stack(Last In - First Out)
   // Ngắt truyền thông(UART)
   PC: 0xD4 -> 0xD5 -> 0xD6 pc:0xD7( Lưu vào Stack -> Đỉnh stack)

   // Nngắt timer, do mức độ ưu tiên cao hơn, nên sẽ thực hiện hết ngắt này.
   PC: 0xB2 -> 0xB3 ... 0xB9 

   /* Sau khi xong ngắt mức độ ưu tiên cao nhất,
   *  PC trỏ đến đỉnh của stack.
   *  PC trỏ đáy của stack.
   */
   PC: 0xD7 -> 0xD8 ... 0xE2
   PC: 0x04 -> 0x05 ... 0xA1
```

## II. Timer

### 1. Timer là gì?

**đếm lên:** Từ 0 đến giá trị mình mong muốn sẽ tràn -> reset lại.

**đếm xuống:** Từ giá trị mong muốn đếm về 0, vượt qua 0 tràn tới -1 -> reset.

> Timer là 1 mạch digital logic(đếm nhị phân), nó sẽ đếm sau mỗi chu kỳ clock nhất định (đếm lên hoặc xuống).
> 
> Timer có thể hoạt động ở chế độ nhận xung clock từ tín hiệu ngoài, bộ dao động hoặc CPU. Có thể cấp xung vào 1 chân cho MCU và dùng xung đó điều khiển timer (tạo 1 nút nhấn, bộ đếm sẽ tăng lên sau mỗi lần nhấn nút).
>
> Timer có chế độ khác như PWM, định thời,...

**STM32F103 có 7 timer**

### 2. Cấu hình Timer










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

**Open Drain**: Khi I2C muốn điều khiển đường bus, sẽ hạ xuống là 0. Khi không điều khiển, thì sẽ thả trôi đường dây ở mức điện áp floating(sẽ không hiểu là mức 0 hay 1). Nên cần điện trở kéo lên nguồn thì bus mới lên 1.

## Quá trình truyền - nhận dữ liệu của I2C?

Giống với SPI, 1 bit truyền đi thì kèm theo 1 Clock. Còn bên nhận thấy 1 Clock thì nhận được 1 Bit để lưu vào bộ nhớ.

- Đầu tiên, ở trạng thái rảnh SDA và SCL đều ở mức 1. Master bắt đầu quá trình truyền bằng cách kéo SDA xuống mức 0 trước SCL (điều kiện bắt đầu).

- Truyền địa chỉ: truyền theo 1 frame(khung) Master gửi 7 bit địa chỉ để chọn Slave mà nó muốn giao tiếp, kèm theo 1 bit R/W (để xác định việc ghi hoặc đọc dữ liệu).

- Kiểm tra địa chỉ và bit R/W:

  - Slave nhận địa chỉ từ Master và so sánh với địa chỉ của mình. Nếu trùng khớp,   - Slave sẽ kiểm tra bit R/W.
  - R/W = 0: Master muốn ghi dữ liệu vào Slave(Write).
  - R/W = 1: Master muốn đọc dữ liệu từ Slave(Read).

- Xác nhận (ACK): Sau khi nhận được 8 bit (7 bit địa chỉ và 1 bit R/W), Slave gửi lại một bit ACK (bit xác nhận) bằng cách kéo SDA xuống mức 0.

- Kiểm tra (ACK): Master chờ nhận bit ACK từ Slave. Nếu SDA = 0 (ACK thành công), Master tiếp tục truyền dữ liệu. Nếu SDA = 1 (ACK thất bại), Master sẽ sử dụng timer để kiểm tra, Nếu sau một khoảng thời gian mà SDA vẫn chưa xuống 0, Master sẽ gửi lại dữ liệu. Quá trình này tiếp tục cho đến khi đạt giới hạn số lần truyền, nếu không nhận được ACK, Master sẽ dừng quá trình I2C.
  
- Tiếp tục truyền: Sau khi nhận được ACK, Master tiếp tục gửi hoặc nhận 8 bit dữ liệu tiếp theo, với mỗi bit kèm theo 1 xung clock.

- Kết thúc: Khi dữ liệu đã truyền hết, Master gửi tín hiệu kết thúc bằng cách đưa SDA và SCL lên mức 1 để dừng quá trình giao tiếp I2C.
  
## 5. UART
(Universal Asynchronous Receiver-Transmitter)

> Chuẩn giao tiếp nối tiếp, không đồng bộ.
>
> Hoạt động ở chế độ song công.
>
> Chỉ 2 thiết bị giao tiếp với nhau.
>
> Sử dụng 2 dây giao tiêp: TX, RX.

<img src="https://github.com/hthuan02/Embedded_Automotive/blob/main/Bai4_Comunication%20Protocols/img/img_temp_6449845cb3ff48-87888352-85375715.png" alt=" " width="500"/>

- ****(Transmit): Chân truyền dữ liệu.

- **Rx**(Receive): Chân nhận dữ liệu.

UART không có sự đồng bộ về gửi và nhận dữ liệu giữa 2 thiết bị, giải pháp là tạo 1 timer. Vì mỗi MCU có tần số xung nhịp khác nhau, dẫn đến thời gian delay của timer 2 MCU sẽ khác. 

▶️▶️▶️ Baundrate là tham số để thống nhất lại, đồng bộ thời gian truyền-nhận dữ liệu và thời gian delay của 2 MCU (ố Bit truyền/s).

## Quá trình truyền - nhận dữ liệu của UART?

- Mỗi lần truyền 1 là frame(khung), 1 frame bao gồm:

<img src="https://github.com/hthuan02/Embedded_Automotive/blob/main/Bai4_Comunication%20Protocols/img/1%20frame_uart.png" alt=" " width="500"/>

- Start bit: Không truyền dữ liệu thì Tx, Rx = 1. Đầu tiên MCU A hạ chân Tx từ 1 xuống 0 để truyền dữ liệu, sau đó delay 1 khoảng thời gian trong 1 Bit.

- Khi MCU A hạ Tx = 0, thì MCU B sẽ Rx sẽ đọc dữ liệu từ Tx(Rx =1), sau khoảng thời gian delay. MCU B sẽ hạ RX = 0 để tiên hành ghi dữ liệu

- Data frame: Là dữ liệu muốn gửi VD:`hello`, tùy vào cài đặt ban đầu 5-9 Bit. Thông thường là 8 Bit.

- Để gửi data frame:
   - MCU A(bên gửi) sau khoảng thời gian delay sẽ gửi 1 Bit dữ liệu rồi dịch Bit, tuần tự đến khi nào hết 8 Bit dữ liệu.
   - MCU B(bên nhận), sau khoảng thời gian từ sẽ đọc dữ liệu rồi dịch, tuần tự hết 8. MCU B sẽ nhận dữ liệu cùng với thời gian bên MCU A gửi.

  ▶️ Gửi và nhận cùng 1 thời điểm

- Sau khi gửi dữ liệu xong thì có 1 Bit Optional(Bit tùy chọn): Có Parity Bit, không có Parity Bit -> Giúp kiểm tra lỗi trong data frame: Quy luật chẵn và lẻ

   - Quy luật chẵn: (Số lượng Bit 1 của Data frame) + (Parity Bit) là số chẵn.
 
   - Quy luật lẻ: (Số lượng Bit 1 của Data frame) + (Parity Bit) là số lẻ.
  
  ▶️Nhược điểm: Không phát hiện được số Bit chẵn bị sai.
  
- Cuối cùng, stop bit: Có thể chiếm trong 1 Bit hoặc 2 Bit
   
   - Nếu 1 Bit: Đưa TX lên 1, delay thời gian trong 1 Bit để kết thúc quá trình.
 
   - Nếu 2 Bit: Đưa TX lên 1, và thời gian gấp 2 lần thời gian braudrate ban đầu.


