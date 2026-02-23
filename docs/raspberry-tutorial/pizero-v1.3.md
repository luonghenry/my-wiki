# 🛠️ Cẩm Nang: Build Pi Zero Portable (512MB RAM)
Tài liệu này hướng dẫn cách biến Raspberry Pi Zero v1.3 thành một thiết bị di động mạnh mẽ, tối ưu hóa RAM và tích hợp các công cụ bảo mật, tự động hóa.

## 1. Chuẩn bị & Tối ưu RAM
💿 Flash OS
OS: Raspberry Pi OS Lite (32-bit).
Thiết lập: Bật SSH và cấu hình User trong Pi Imager.

⚡ Tối ưu Zram (Quan trọng)Giúp 512MB RAM hoạt động như 800MB bằng cách nén dữ liệu.

```bash
sudo apt update && sudo apt install zram-tools -y
sudo nano /etc/default/zramswap
# Sửa: ALGO=zstd, PERCENT=60, PRIORITY=100
sudo systemctl restart zramswap
```

## 2. WiFi & Access Point
📶 Driver WN725NDành cho USB Wi-Fi TP-Link WN725N (Chipset RTL8188EUS).
```bash
sudo wget [http://www.fars-robotics.net/install-wifi](http://www.fars-robotics.net/install-wifi) -O /usr/bin/install-wifi
sudo chmod +x /usr/bin/install-wifi
sudo install-wifi
```

### 🛰️ Tạo trạm phát Wi-Fi (AP)Giúp điện thoại kết nối trực tiếp vào Pi khi ở ngoài trời.
Cài đặt hostapd dnsmasq.
Cấu hình IP tĩnh 192.168.4.1 trong /etc/dhcpcd.conf.
Thiết lập SSID và Password trong /etc/hostapd/hostapd.conf.3. 
WireGuard & Pi-holeWireGuard: Cài qua pivpn.io. 
Chọn WireGuard để có tốc độ cao nhất trên chip đơn nhân.

### Pi-hole: Chặn quảng cáo toàn hệ thống. 
Dùng lệnh:
curl -sSL https://install.pi-hole.net | bash4. 

### Dự án chi tiết (Workloads)
🤖 Pwnagotchi ("Thú ảo" cho Hacker)
Pwnagotchi là một AI sử dụng học máy để thu thập handshake Wi-Fi.
Cấu hình: Chỉnh sửa file config.toml để đặt tên và loại màn hình.

📩 Telegram Bot (Điều khiển & Cảnh báo)
Dùng Telegram để theo dõi Pi và nhận thông báo tự động khi máy quá nóng.
Script Python nâng cao (bot.py):
```python
import telebot
import os
import time
import threading

API_TOKEN = 'TOKEN_CỦA_BẠN'
CHAT_ID = 'ID_CHAT_CỦA_BẠN' 
TEMP_THRESHOLD = 65.0       

bot = telebot.TeleBot(API_TOKEN)

def get_cpu_temp():
    temp = os.popen("vcgencmd measure_temp").read()
    return float(temp.replace("temp=","").replace("'C\n",""))

def monitor_temp():
    while True:
        current_temp = get_cpu_temp()
        if current_temp > TEMP_THRESHOLD:
            bot.send_message(CHAT_ID, f"⚠️ CẢNH BÁO: Pi quá nóng! {current_temp}°C")
        time.sleep(300)

@bot.message_handler(commands=['status'])
def send_status(message):
    temp = get_cpu_temp()
    ram = os.popen("free -m | grep Mem").read()
    bot.reply_to(message, f"Nhiệt độ: {temp}°C\nRAM: {ram}")

threading.Thread(target=monitor_temp, daemon=True).start()
bot.polling()
```

🔋 Mẹo tiết kiệm Pin & Bảo trì
Tắt HDMI: tvservice -o (Tiết kiệm ~20-30mA).
Tắt LED: Thêm vào config.txt để ẩn mình ban đêm.
Chạy ngầm Bot:
```bash
sudo nano /etc/systemd/system/pibot.service
# Thêm cấu hình ExecStart=/usr/bin/python3 /home/pi/bot.py
sudo systemctl enable pibot.service
```

EOF