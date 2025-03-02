<h1 align="center" style="border-bottom: none">
    <a href="https://prometheus.io" target="_blank"><img alt="Prometheus" src="/images/prometheus-logo.svg"></a><br>Prometheus
</h1>

<p align="center">Truy cập <a href="https://prometheus.io" target="_blank">prometheus.io</a> để biết đầy đủ tài liệu, ví dụ và hướng dẫn.</p>

[![Lang VI](https://img.shields.io/badge/lang-vi-green)](https://github.com/quachdoduy/Monitor_Prometheus/blob/main/README.vi.md)
[![Lang EN](https://img.shields.io/badge/lang-en-red)](https://github.com/quachdoduy/Monitor_Prometheus/blob/main/README.md)<br/>
[![GitHub stars](https://img.shields.io/github/stars/quachdoduy/Monitor_Prometheus?logo=GitHub&style=flat&color=red)](https://github.com/quachdoduy/Monitor_Prometheus/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/quachdoduy/Monitor_Prometheus?logo=GitHub&style=flat&color=green)](https://github.com/quachdoduy/Monitor_Prometheus/network)
[![GitHub watchers](https://img.shields.io/github/watchers/quachdoduy/Monitor_Prometheus?logo=GitHub&style=flat&color=blue)](https://github.com/quachdoduy/Monitor_Prometheus/watchers)<br/>
[![Prometheus version](https://img.shields.io/badge/Prometheus-2.53.3(LTS)-yellow?style=flat)](https://github.com/prometheus/prometheus/blob/main/CHANGELOG.md)
[![donate with paypal](https://img.shields.io/badge/Like_it%3F-Donate!-green?logo=githubsponsors&logoColor=orange&style=flat)](https://paypal.me/quachdoduy)
[![donate with buymeacoffe](https://img.shields.io/badge/Like_it%3F-Donate!-blue?logo=githubsponsors&logoColor=orange&style=flat)](https://buymeacoffee.com/quachdoduy)

<h1 align="center" style="border-bottom: none">
    <a href="https://github.com/quachdoduy/Monitor_Prometheus" target="_blank"><img alt="Topo" src="/images/Prometheus.png"></a><br>Prometheus + Grafana
</h1>

Nguồn tài liệu tham khảo tại đây:
- [Prometheus.](https://prometheus.io/docs/introduction/overview/)
- [Grafana.](https://grafana.com/docs/)

Dự án được xây dựng trên mô hình ảo hóa (VMWare) với các máy chủ cài đặt hệ điều hành Ubuntu Server 24.04 LTS.<br>
Danh sách các máy chủ như dưới:
- Server Prometheus:
    Thực hiện cài đặt các thành phần:
    - Prometheus.
    - Alert Manager. *(Tùy điều kiện triển khai, chúng ta có thể tách rời Alert Manager độc lập)*.
- Server Grafana:
    Thực hiện cài đặt Grafana để kết nối đến Prometheus.
- Server Linux và Server Windows:
    Thực hiện cài đặt Node Exporter để kết Prometheus và cung cấp các Metrics.

# CÁC BƯỚC CHÍNH
1. Cài đặt và hiệu chỉnh các máy chủ như yêu cầu trên nền tảng VMWare.
2. Tiến hành cài đặt Prometheus trên Server Prometheus.
3. Tiến hành cài đặt Node Exporter trên Server Linux và Server Windows
4. Tiến hành cài đặt Grafana trên Server Grafana.
5. Tiến hành cài đặt Alert Manager trên Server Prometheus.

# CÁC BƯỚC CHI TIẾT
# 1.Setup Server
1. Chuẩn bị các bản cài đặt hệ điều hành máy chủ.
    - Chuẩn bị bản cài đặt Ubuntu Server 24.04 LTS. [Nguồn tải ở đây](https://ubuntu.com/download/server)
    - Chuẩn bị bản cài đặt Windows Server 2019. [Nguồn tải ở đây](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019)
2. Khởi tạo các máy chủ trên VMWare và cài đặt.
    - Prometheus Server:
        - HostName: ProSVR-VT
        - CPU: 4 | RAM: 8GB | HDD: 40GB
        - NIC: 1 (NAT) | IPv4: 192.168.203.150/24
    - Grafana Server:
        - HostName: GraSVR-VT
        - CPU: 2 | RAM: 4GB | HDD: 40GB
        - NIC: 1 (NAT) | IPv4: 192.168.203.151/24
    - Linux Server và Windows Server:
        - HostName: NuxSVR-VT và MicSVR-VT
        - CPU: 2 | RAM: 4GB | HDD: 40GB
        - NIC: 1 (NAT) | IPv4: 192.168.203.161/24 và 192.168.203.162/24
3. Các chú ý tinh chỉnh sau khi cài đặt.<br>
    Windows Server 2019: đăng nhập bằng tài khoản quản trị **.\Administrator**. *(Chú ý: môi trường Local không Domain.)*<br>
    Ubutu 24.02 LTS: đăng nhập bằng tài khoản quản trị **root**. *(Chú ý: mặc định tài khoản root không được kích hoạt, bạn cần đặt mặt khẩu sau đó mới đăng nhập được.)*

**Kích hoạt tài khoản root**
- Thiết lập mật khẩu cho tài khoản root.
```bash
sudo passwd root
```
*Sau đó thực hiện thiết lập mật khẩu cho tài khoản root.*

- Chuyển qua tài khoản root.
```bash
sudo -
```

**Cho phép root được quyền SSH.**
- Mở và sửa file cấu hình SSH bằng lệnh.
```bash
sudo nano /etc/ssh/sshd_config
```

- Tìm đến dòng lệnh `#PermitRootLogin prohibit-password` và bỏ dấu comment `#` và sửa thành `PermitRootLogin yes`
- Tìm đến dòng lệnh `#PasswordAuthentication yes` và bỏ dấu comment `#` thành `PasswordAuthentication yes`
- Lưu lại file `CTRL + O` và đóng lại `CTRL + x`.
- Khởi động lại dịch vụ SSH bằng 1 trong 2 lệnh dưới.
```bash
sudo systemctl restart ssh
```
```bash
sudo service ssh restart
```
*Kiểm tra lại kết nối SSH với tài khoản root*

**Xóa tài khoản quản trị được khởi tạo lúc cài đặt**
- Kiểm tra lại quyền `sudo` của tài khoản quản trị lúc cài đặt `nuxadmin`.
```bash
sudo cat /etc/sudoers.d/nuxadmin
```

- Nếu có bạn nên xóa file này trước.
```bash
sudo rm /etc/sudoers.d/nuxadmin
```

- Kiểm tra danh sách người dùng trước khi xóa.
```bash
cat /etc/passwd | grep nuxadmin
```

- Thực hiện xóa quản trị.
```bash
userdel -r nuxadmin
```
*Bạn nhớ kiểm tra lại danh sách người dùng sau khi xóa*

**Cập nhật hệ thống với các bản cập nhật mới nhất.**
```bash
sudo apt update && sudo apt upgrade -y
```

**Các câu lệnh kiểm tra khác**
- Hiển thị địa chỉ IPv4 các NIC của máy chủ.
```bash
ip address show
```

- Hiển thị địa chỉ IPv4 với NIC của máy chủ có tên `ens33`.
```bash
ip address show dev ens33
```

- Hiển thị bảng routing của máy chủ.
```bash
ip route list
```

# 2.Setup Prometheus
*Thực hiện trên máy chủ **ProSVR-VT**.*

## Creating a Prometheus User and Directory
- Vì một vài lý do bảo mật, Prometheus không chạy như là root user. Do đó cần khởi tạo user cho Prometheus.<br>*Chú ý: ta đặt tên cho user này là:* `prometheus`.
```bash
sudo useradd --no-create-home --shell /bin/false prometheus
```

- Tạo các thư mục mà chúng ta sẽ lưu trữ các tập tin cấu hình và thư viện.
```bash
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```

- Thiết lập quyền sở hữu thư mục `/var/lib/prometheus` bằng lệnh bên dưới.
```bash
sudo chown prometheus:prometheus /var/lib/prometheus
```

## Downloading Prometheus Binary File
Đến trang [tải xuống Prometheus chính thức](https://prometheus.io/download/) để tìm bản phát hành mới nhất. Tải xuống và giải nén Prometheus:<br>
Sử dụng lệnh bên dưới, chúng ta có thể tải xuống Prometheus, ở đây chúng ta đang tải xuống phiên bản **Prometheus 2.53.3 LTS** *(Filename: prometheus-2.53.3.linux-amd64.tar.gz)*, bạn sử dụng liên kết trên để tải xuống phiên bản cụ thể.

- Bạn cần di chuyển đến thư mục `/tmp`.
```bash
cd /tmp/
```

- Sử dụng `wget` để tải Prometheus.
```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.53.3/prometheus-2.53.3.linux-amd64.tar.gz
```

- Sử dụng `tar` để giải nén.
```bash
tar -xvf prometheus-2.53.3.linux-amd64.tar.gz
```

- Di chuyển về thư mục gốc `/`.
```bash
cd /
```

## Configuring Prometheus
- Di chuyển các tập tin cấu hình vào các thư mục thích hợp.
```bash
sudo mv /tmp/prometheus-2.53.3.linux-amd64/console*/ /etc/prometheus/
sudo mv /tmp/prometheus-2.53.3.linux-amd64/prometheus.yml /etc/prometheus/
```

- Di chuyển các tệp nhị phân vào các thư mục thích hợp.
```bash
sudo mv /tmp/prometheus-2.53.3.linux-amd64/prometheus /usr/local/bin/
sudo mv /tmp/prometheus-2.53.3.linux-amd64/promtool /usr/local/bin/
```

- Đặt chủ sở hữu của các tập tin cấu hình chocho người dùng `prometheus`.
```bash
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
```

- Đặt chủ sở hữu của các tập tin nhị phân cho người dùng `prometheus`.
```bash
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
```

## Prometheus configuration file
Chúng tôi đã sao chép tệp `prometheus.yml` từ `/tmp/prometheus-2.53.3.linux-amd64/` vào thư mục `/etc/prometheus` ở bước trên.<br>
Bạn hãy kiểm tra xem nó có tồn tại không.<br>
File này sẽ cần chỉnh sửa và sẽ được hướng dẫn trong phần sau.
```bash
sudo nano /etc/prometheus/prometheus.yml
```

<img alt="Prometheus Config File" src="/images/Prometheus_Config_File.png">

## Creating a Prometheus Service
- Để quản lý Prometheus bằng systemd, hãy tạo một tệp dịch vụ.
```bash
sudo nano /etc/systemd/system/prometheus.service
```

- Sau đó điền thông tin như nội dung dưới.
```bash
[Unit]
Description=Prometheus Monitoring
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file=/etc/prometheus/prometheus.yml \
    --storage.tsdb.path=/var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

- Tiếp theo thực hiện nạp lại systemd.
```bash
sudo systemctl daemon-reload
```

- Thực hiện Chạy, Kích hoạt, và xem Trạng thái dịch vụ Prometheus.
```bash
sudo systemctl start prometheus
sudo systemctl enable prometheus
sudo systemctl status prometheus
```

## Accessing Prometheus in Browser
Bây giờ Prometheus đã được cài đặt, thiết lập thành công và sẵn sàng để sử dụng.<br>
Chúng ta có thể truy cập các dịch vụ của nó thông qua giao diện web.<br>
Ngoài ra, hãy kiểm tra xem cổng 9090 có được bật trong tường lửa không.

- Sử dụng lệnh bên dưới để kích hoạt tường lửa cho dịch vụ Prometheus.
```bash
sudo ufw allow 9090/tcp
```

Bây giờ dịch vụ Prometheus đã sẵn sàng chạy và chúng ta có thể truy cập từ bất kỳ trình duyệt web nào `http://ProSVR-VT:9090.`
<img alt="Prometheus Finish" src="/images/Prometheus_Finish.png">
Như chúng ta có thể thấy bảng điều khiển Prometheus, chúng ta cũng có thể kiểm tra mục **Targets** có thể thấy trạng thái hiện tại là **UP** và chúng ta cũng có thể thấy lần cào cuối cùng.
<img alt="Prometheus Finish" src="/images/Prometheus_Finish_1.png">

# 3.Setup Node Exporter
## 3.1 Setup Node Exporter on Linux
*Thực hiện trên máy chủ **NuxSVR-VT**.*

## Creating a Node Exporter User
- Vì một vài lý do bảo mật, Node Exporter không chạy như là root user. Do đó cần khởi tạo user cho Node Exporter.<br>*Chú ý: ta đặt tên cho user này là:* `node_exporter`.
```bash
sudo useradd --no-create-home --shell /bin/false node_exporter
```
hoặc
```bash
sudo useradd -rs /bin/false node_exporter
```

## Download Node Exporter
Truy cập trang phát hành chính thức của [Prometheus Node Exporter](https://github.com/prometheus/node_exporter/releases/) và sao chép liên kết phiên bản mới nhất của gói Node Exporter theo loại hệ điều hành của bạn.<br>
Tại dự án này chúng ta sử dụng Prometheus Node Exporter version 1.9.0 (Filename: node_exporter-1.9.0.linux-amd64.tar.gz)

Chú ý: *Bạn hãy chọn đúng phiên bản phù hợp với máy chủ cần giám sát*

- Bạn cần di chuyển đến thư mục `/tmp`.
```bash
cd /tmp/
```

- Sử dụng `wget` để tải Node Exporter.
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.9.0/node_exporter-1.9.0.linux-amd64.tar.gz
```

- Sử dụng `tar` để giải nén.
```bash
tar -xvf node_exporter-1.9.0.linux-amd64.tar.gz
```

- Di chuyển về thư mục gốc `/`.
```bash
cd /
```

- Di chuyển tệp nhị phân của **Node Exporter** đến vị trí `/usr/local/bin`.
```bash
sudo mv /tmp/node_exporter-1.9.0.linux-amd64/node_exporter /usr/local/bin/
```

## Creating Node Exporter Systemd service
- Tạo tệp dịch vụ `node_exporter` trong thư mục `/etc/systemd/system`.
```bash
sudo nano /etc/systemd/system/node_exporter.service
```

- Sau đó điền thông tin như nội dung dưới.
```bash
[Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

- Tiếp theo thực hiện nạp lại systemd.
```bash
sudo systemctl daemon-reload
```

- Thực hiện Chạy, Kích hoạt, và xem Trạng thái dịch vụ Node Exporter.
```bash
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
sudo systemctl status node_exporter
```

## Accessing Node Exporter in Browser
Bây giờ  Node Exporter đã được cài đặt, thiết lập thành công và sẵn sàng để sử dụng.<br>
Chúng ta có thể truy cập các dịch vụ của nó thông qua giao diện web.<br>
Ngoài ra, hãy kiểm tra xem cổng 9100 có được bật trong tường lửa không.

- Sử dụng lệnh bên dưới để kích hoạt tường lửa cho dịch vụ Prometheus.
```bash
sudo ufw allow 9100/tcp
```

Bây giờ dịch vụ Node Exporter đã sẵn sàng chạy và chúng ta có thể truy cập từ bất kỳ trình duyệt web nào `http://NuxSVR-VT:9100`.
<img alt="Node Exporter" src="/images/Node_Exporter_Finish.png">

## 3.2 Setup Node Exporter on Windows
*Thực hiện trên máy chủ **MicSVR-VT**.*
- Trước đây các Node Exporter cho Windows được gọi là **WMI Exporter** nhưng nay được gọi là **Windows Exporter**.

## Download Windows_Exporter và Cài đặt.
Truy cập trang phát hành chính thức của [Prometheus Windows Exporter](https://github.com/prometheus-community/windows_exporter/releases) và tải bản cài đặt mới nhất.<br>
*Tại dự án này chúng ta sử dụng Prometheus Windows Exporter version 0.30.4 (Filename: windows_exporter-0.30.4-amd64.exe)*

- Thực hiện cài đặt như bình thường.
<img alt="Windows Exporter" src="/images/Windows_Exporter_Setup.png">

- Cấu hình.
  - Cấu hình Metrics mặc định như hình là: **[default]**.
  - Cấu hình Port mặc định là **9182** ta thay đổi để đồng nhất với dự án là: **9100**.
<img alt="Windows Exporter" src="/images/Windows_Exporter_Setup_1.png">

- Kiểm tra cấu hình Service.
  - Cấu hình `Startup Type` thành **Automatic**.
<img alt="Windows Exporter" src="/images/Windows_Exporter_Setup_2.png">

- Bây giờ dịch vụ Windows Exporter đã sẵn sàng chạy và chúng ta có thể truy cập từ bất kỳ trình duyệt web nào `http://MicSVR-VT:9100`.
<img alt="Node Exporter" src="/images/Node_Exporter_Finish.png">

## Configure the Node Exporter as a Prometheus target
Bây giờ để trích xuất `node_exporter`, hãy hướng dẫn **Prometheus** kết nối bằng cách:
- Thực hiện một thay đổi nhỏ trong tệp `prometheus.yml` **trên máy chủ ProSVR-VT**.

- Mở và sửa file cấu hình `prometheus.yml` bằng lệnh.
```bash
sudo nano /etc/prometheus/prometheus.yml
```
*Chú ý: Nội dung nguyên bản như hình dưới*
<img alt="Connect Prometheus to Node Exporter" src="/images/Prometheus_yml_Original.png">

- Nội dung thay đổi đề xuất như dưới.<br>
*Chú ý: cần đọc kỹ tài liệu của Prometheus để hiểu rõ và cấu hình file này theo đúng yêu cầu*
```bash
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]
  
  - job_name: "node_exporter_linux"
    static_configs:
      - targets:
          - "nuxsvr-vt:9100"
          - "192.168.203.167:9100"

  - job_name: "node_exporter_windows"
    static_configs:
      - targets:
          - "micsvr-vt:9100"
          - "192.168.203.168:9100"
```

- Bây giờ hãy khởi động lại dịch vụ Prometheus.
```bash
sudo systemctl restart prometheus
```

- Hoặc kiểm tra LOG nếu có lỗi.
```bash
sudo journalctl -u prometheus --no-pager --lines=50
```

- Bây giờ truy cập vào dịch vụ Prometheus từ bất kỳ trình duyệt web nào `http://ProSVR-Vt:9090`.
<img alt="Prometheus connected to Node Exporter" src="/images/prometheus_NodeExporter_Finish.png">

# 4.Setup Grafana
*Thực hiện trên máy chủ **GraSVR-VT**.*
