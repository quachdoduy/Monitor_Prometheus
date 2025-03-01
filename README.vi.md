<h1 align="center" style="border-bottom: none">
    <a href="https://prometheus.io" target="_blank"><img alt="Prometheus" src="/images/prometheus-logo.svg"></a><br>Prometheus
</h1>

<p align="center">Truy cập <a href="https://prometheus.io" target="_blank">prometheus.io</a> để biết đầy đủ tài liệu, ví dụ và hướng dẫn.</p>

[![Lang VI](https://img.shields.io/badge/lang-vi-green)](https://github.com/quachdoduy/Monitor_Prometheus/blob/main/README.vi.md)
[![Lang EN](https://img.shields.io/badge/lang-en-red)](https://github.com/quachdoduy/Monitor_Prometheus/blob/main/README.md)<br/>
[![GitHub stars](https://img.shields.io/github/stars/quachdoduy/Monitor_Prometheus?logo=GitHub&style=flat&color=red)](https://github.com/quachdoduy/Monitor_Prometheus/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/quachdoduy/Monitor_Prometheus?logo=GitHub&style=flat&color=green)](https://github.com/quachdoduy/Monitor_Prometheus/network)
[![GitHub watchers](https://img.shields.io/github/watchers/quachdoduy/Monitor_Prometheus?logo=GitHub&style=flat&color=blue)](https://github.com/quachdoduy/Monitor_Prometheus/watchers)<br/>
[![required RouterOS version](https://img.shields.io/badge/Prometheus-2.53.3(LTS)-yellow?style=flat)](https://github.com/prometheus/prometheus/blob/main/CHANGELOG.md)
[![donate with paypal](https://img.shields.io/badge/Like_it%3F-Donate!-green?logo=githubsponsors&logoColor=orange&style=flat)](https://paypal.me/quachdoduy)
[![donate with buymeacoffe](https://img.shields.io/badge/Like_it%3F-Donate!-blue?logo=githubsponsors&logoColor=orange&style=flat)](https://buymeacoffee.com/quachdoduy)

<h1 align="center" style="border-bottom: none">
    <a href="https://github.com/quachdoduy/Monitor_Prometheus" target="_blank"><img alt="Topo" src="/images/Prometheus.png"></a><br>Prometheus + Grafana
</h1>

Nguồn tài liệu tham khảo tại đây:
- [Prometheus.](https://prometheus.io/docs/introduction/overview/)
- [Grafana.](https://grafana.com/docs/)

Dự án được xây dựng trên mô hình ảo hóa (VMWare) với các máy chủ cài đặt hệ điều hành Ubuntu Server 24.02 LTS. Danh sách các máy chủ như dưới:
- Server Prometheus:
    Thực hiện cài đặt các thành phần:
    - Prometheus.
    - Alert Manager. *(Tùy điều kiện triển khai, chúng ta có thể tách rời Alert Manager độc lập)*.
- Server Grafana:
    Thực hiện cài đặt Grafana để kết nối đến Prometheus.
- Server Linux và Server Windows:
    Thực hiện cài đặt Node Exporter để kết Prometheus và cung cấp các Metrics.

## CÁC BƯỚC CHÍNH
1. Cài đặt và hiệu chỉnh các máy chủ như yêu cầu trên nền tảng VMWare.
2. Tiến hành cài đặt Prometheus trên Server Prometheus.
3. Tiến hành cài đặt Node Exporter trên Server Linux và Server Windows
4. Tiến hành cài đặt Grafana trên Server Grafana.
5. Tiến hành cài đặt Alert Manager trên Server Prometheus.

## CÁC BƯỚC CHI TIẾT
### Setup Server
1. Chuẩn bị các bản cài đặt hệ điều hành máy chủ.
    - Chuẩn bị bản cài đặt Ubuntu Server 24.02 LTS. [Nguồn tải ở đây](https://ubuntu.com/download/server)
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
3. Các chú ý tinh chỉnh sau khi cài đặt.
    Windows Server 2019: đăng nhập bằng tài khoản quản trị **.\Administrator**. *(Chú ý: môi trường Local không Domain.)*
    Ubutu 24.02 LTS: đăng nhập bằng tài khoản quản trị **root**. *(Chú ý: mặc định tài khoản root không được kích hoạt, bạn cần đặt mặt khẩu sau đó mới đăng nhập được.)*

Hướng dẫn thiết lập mật khẩu cho tài khoản root.

    ```bash
    sudo passwd root
    ```
    *Sau đó thực hiện thiết lập mật khẩu cho tài khoản root.*


### Setup Prometheus
