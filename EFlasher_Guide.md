
# Hướng dẫn tạo EFlasher

## Bước 1: Tạo đĩa boot bằng phần mềm balenaEtcher
1. Sử dụng **balenaEtcher** để ghi tệp image vào thẻ nhớ (download link: http://repo.smatec.com.vn/EFlasher/):
   - File image: `h3-eflasher-friendlywrt-4.14-armhf-20210512.img.gz`
   
## Bước 2: Copy các file cần thiết vào thẻ nhớ
1. Sau khi cài đặt xong, copy các tệp sau vào ổ đĩa (FriendlyARM) vừa được tạo ở bước 1:
   - **File cấu hình EFlasher**: `eflasher.conf`
   - **Firmware thiết bị**: `openwrt-sunxi-cortexa7-xunlong_orangepi-pc-plus-squashfs-sdcard.img.gz` (lấy từ bản build mới nhất)

## Bước 3: Boot thiết bị và cấu hình LED báo hiệu
1. Cắm thẻ nhớ vào thiết bị **MIRA V4 SV3** để boot.
2. Sử dụng **USB-TTL RS232** để kết nối console vào thiết bị. Thông tin đăng nhập mặc định là:
   - **Username**: `pi`
   - **Password**: `pi`

3. Thêm đoạn mã sau vào file `/opt/run-eflasher.sh` để nháy LED báo hiệu:

   ```bash
   sudo vi /opt/run-eflasher.sh
   ```

   Thêm đoạn mã:

   ```bash
   echo 16 > /sys/class/gpio/export
   echo out > /sys/class/gpio/gpio16/direction
   while true
   do
     echo 1 > /sys/class/gpio/gpio16/value
     sleep 0.1
     echo 0 > /sys/class/gpio/gpio16/value
     sleep 0.1
   done
   ```

## Mẫu file `/opt/run-eflasher.sh` hoàn chỉnh

```bash
#!/bin/bash

# Thiết lập môi trường QT4
. /usr/bin/setqt4env

# Khởi động giao diện eFlasher
cd /opt/
./eflasher-gui -qws >/dev/null 2>&1

# Cấu hình nháy LED báo hiệu
echo 16 > /sys/class/gpio/export
echo out > /sys/class/gpio/gpio16/direction
while true
do
  echo 1 > /sys/class/gpio/gpio16/value
  sleep 0.1
  echo 0 > /sys/class/gpio/gpio16/value
  sleep 0.1
done
```

## Bước 4: Khởi động lại và kiểm tra
1. Khởi động lại thiết bị để kiểm tra việc boot và copy Firmware vào bộ nhớ eMMC.
2. Sau khi hoàn tất, đèn LED màu đỏ (LED3) sẽ nhấp nháy liên tục báo hiệu.

## Ghi chú
- Đảm bảo các tệp cần thiết được sao chép chính xác vào thẻ nhớ.
- Khi hoàn tất, thiết bị sẽ nháy LED báo hiệu sau khi chạy script `run-eflasher.sh`.
