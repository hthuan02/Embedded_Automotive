# Bài 4: Communication Protocols

**Truyền nhận dữ liệu là gì?**
>
> Là quá trình trao đổi tín hiệu điện áp của MCU A đến MCU B. Gồm các tín hiệu 0 và 1(0v và 3.3V hoặc 5V).

_VD: Đối với 1 chuỗi như `hello`_

- MCU A sẽ truyền tuần tự từ kí tự `h` đến `o` qua MCu B(theo tiêu chuẩn ASCII).

- Việc truyền tín hiệu, sẽ truyền từng bit, bằng cách chênh lệch tăng hoặc giảm điện áp

- **Nhưng:** Đối với tín hiệu có 3 bit 0 (hoặc 3 bit 1) liên tiếp giống nhau như (h: 0b 0110 1000). Thì việc truyền tín hiệu sẽ không có sự chênh lệch tín hiệu điện áp nào. Sẽ xảy ra sai xót trong truyền nhận dữ liệu.

===>>> Vì vậy, các chuẩn giao tiếp ra đời như: SPI, I2C, UART,...

## I. SPI

## II. I2C

## III. UART
