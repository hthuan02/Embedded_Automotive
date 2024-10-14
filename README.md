# Bài 4: Communication Protocols

## 1.Truyền nhận dữ liệu là gì?**
>
> Là quá trình trao đổi tín hiệu điện áp của MCU A đến MCU B. Gồm các tín hiệu 0 và 1(0v và 3.3V hoặc 5V).

_VD: Đối với 1 chuỗi như `hello`_

- MCU A sẽ dịch tuần tự từ kí tự `h` sang mã nhị phân 1 0(theo tiêu chuẩn ASCII) đến kí tự `o` qua MCU B.

- Việc truyền tín hiệu, sẽ truyền từng bit, bằng cách chênh lệch tăng hoặc giảm điện áp.

- **Nhưng:** Đối với tín hiệu có 3 bit 0 (hoặc 3 bit 1) liên tiếp giống nhau như (h: 0b 0110 1000). Thì việc truyền tín hiệu sẽ không có sự chênh lệch tín hiệu điện áp nào. Sẽ xảy ra sai xót trong truyền nhận dữ liệu.

===>>> Vì vậy, các chuẩn giao tiếp ra đời như: SPI, I2C, UART,...

## 2. Ba chế độ của truyền nhận dữ liệu:

- **Đơn công**: Truyền dữ liệu 1 chiều, một thiết bị chỉ có 1 nhiệm vụ truyền hoặc nhận(thường truyền từ 1 máy phát đến 1 máy thu mà không thể phản hồi ngược lại).
_VD_: Kết nối micro đến loa. Chỉ có truyền âm thanh một chiều từ micro đến loa

- **Bán song công:** Tại 1 thời điểm chỉ thực hiện được 1 nhiệm vụ truyền hoặc nhận.

- Song công: Truyền nhận dữ iệu tại 1 thời điểm. ->> SPI

## I. SPI

> 
>

## II. I2C

## III. UART
