20 Example IPTABLES cho New SysAdmins
##1.1 Hiển thị trạng thái của Firewall
>iptables -L -n -v

Hiển thịtheo số thứ tự luật:
>iptables -L -n -v --line-numbers

Hiển thị rules trong chain nào đó:
>iptables -L INPUT -n -v

>iptables -L OUTPUT -n -v --line-numbers

##1.2 Xóa luật
Trước tiên hiện luật theo số thứ tự để biết luật ta muốn xóa ở vịtrí dòng (line) bao nhiêu
>iptables -L INPUT -n --line-numbers
>iptables -L OUTPUT -n --line-numbers |less
>iptables -L OUTPUT -n --line-numbers | grep 202.54.1.1

Ví dụ muốn xóa luật thứ 4 :
>iptables -D INPUT 4

Hoặc xóa theo địa chỉ IP:
>iptables -D INPUT -s 202.54.1.1 -j DROP

##1.3 Thêm Luật mới
Ta cũng hiển thị luật đã có theo dòng:
>iptables -L INPUT -n -line-numbers

Thêm luật vào giữa 1 và 2:
>iptables -I INPUT 2 -s 202.54.1.2 -j DROP

Xem lại cập nhật:
>iptables -L INPUT -n --line-numbers

##1.4 Lưu luật lại
Trên CentOS thì iptables là một service nên có thể dùng lệnh **iptables service save**

Còn Ubuntu ta sử dụng:
>iptables-save > /root/myccativefirewall.rules

##1.5 Khôi phục lại luật
>iptables-restore < /root/myactivefirewall.rules/

##1.6 Thiết đặt chính sách mặc định firewall 
Chặn và loại bỏ tất cả traffic
>iptables -P INPUT DROP
>iptables -P OUTPUT DROP
>iptables -P FORWARD DROP

ChỉBlock traffic đến thêm luật:
>iptables -A INPUT -m state --state NEW, ESTABLISHED -j ACCEPT

##1.7 Drop địa chỉ ip riêng trên giao diện public
>iptables -A INPUT -i eth1 -s 192.168.0.0/24 -j DROP
>iptables A INPUT -i eth1 -s 10.0.0.0/8 -j DROP

Giải địa chỉ IPv4 cho địa chỉ Private Network (hãy chắc chắn rằng bạn block chúng trên public interface)
* 10.0.0.0/8 -j(A)
* 172.16.0.0/12 (B)
* 192.168.0.0/16 (C)
* 224.0.0.0/4 (MULTICAST D)
* 224.0.0.0/5(E)
* 127.0.0.0/8 (LOOPBACK)

##1.8 Block một IP
>iptables -A INPUT -s 1.2.3.4 -j DROP
>iptables -A INPUT -s 192.168.0.0/24 -j DROP

##1.9 Block Incoming Port Requests (Block Port)
>iptables -A INPUT -p tcp --dport 80 -j DROP
>iptables -A INPUT -i eth1 -p tcp -s 192.168.1.0/24 --dport 80 -j DROP

##1.10 Block Outgoing IP Address
Block theo tên miền
>host -t a cyberciti.biz

Block với địa chỉ IP
>iptables -A OUTPUT -d 75.126.153.206 -j DROP

Bạn có thể sử dụng bằng một subnet:
>iptables -A OUTPUT -d 192.168.1.0/24 -j DROP
>iptables -A OUTPUT -o eth1 -d 192.168.1.0/24 -j DROP

Để tìm ip của một tên miền:
>host -t a wwư.facebook.com
>whois 69.171.228.40 | grep CIDR

>iptables -A OUTPUT -p tcp -d 69.171.224.0/19 -j DROP
>iptables -A OUTPUT -p tcp -d facebook.com -j DROP

##1.11 Block Outgoing IP Address
>host -t a cyberciti.biz

<img src="https://3.basecamp.com/3260930/projects/566891m">
