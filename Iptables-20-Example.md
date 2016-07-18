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

<img src="http://imgur.com/BnRXBSU">

>iptables -A OUTPUT -d 75.126.153.206 -J DROP

Bạn cũng có thể sử dụng subnet:
>iptables -A OUTPUT -d 192.168.1.0/24 -j DROP
>iptables -A OUTPUT -o eth1 -d 192.168.1.0/24 -j 

###1.11.1 Example - Block Facebook.com Domain
Đầu tiên, tìm tất cả địa chỉ của facebook.com
>host -t a www.facebook.com

Some output:
<img src="ttp://imgur.com/6vyAMGU">

Find CIDR for 31.13.95.36
>whois 31.13.95.36 | grep CIDR

sample outputs:
<img src="http://imgur.com/6vyAMGU">

Để ngăn chặn truy cập từ www.facebook.com
>iptables -A OUTPUT -p tcp -d 169.171.224.0/19 -j 

Bạn có thể sử dụng tên miền:
>iptables -A OUTPUT -p tcp -d www.facebook.com -j DROP
>iptables -A OUTPUT -p tcp -d facebook.com -j DROP

##1.12 Log and Drop packets
>iptables -S INPUT -i eth1 -s 10.0.0.0/8 -j LOG --log-prefix "IP_SPOOF A:"
>iptables -A INPUT -i eth1 -s 10.0.0.0/8 -j DROP

Mặc định mọi thứ logged tại : /var/log/message
>tail -f /var/log/messages
>grep --color 'IP SOOF' /var/log/messages

##1.13 Log and Drop Packets with giới hạn cố lượng log
>iptables -S INPUT -i eth1 -s 10.0.0.0/8 -m limit --limit 5/m --lomit-bustt 7 -j LOG --log-prefix "IP_SPOOFA: "
>iptables -A INPUT -i eth1 -s 10.0.0.0/8 -j DROP

##1.14 Drop or Accept traffic form MAC address
>iptables -S INPUT -m mac --mac-source 00:0F:EA:91:04:08 -j DROP 
>iptables -A INPUT -p tcp --destination-port 22 -m mac --mac-source 00:0F:EA:91:04:07 -j ACCEPT

##1.14 Block or Allow ICMP ping request
>iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
>iptables -A INPUT -i eth1 -p icmp --icmp-type echo-request -j DROP

Ping request can also be limited to certain networks or hosts:
>iptables -A INPUT -s 192.168.1.0/24 -p icmp --icmpt-type echo-request -j ACCEPT

Giới hạn một số kiểu của ICMP request
>iptables -A INPUT -p icmp --icmp-type echo-relay -j ACCEPT
>iptables -A INPUT -p icmp --icmp-type destination-unreachable -j ACCEPT
>iptables -A INPUT -p icmp --icmp-type time-exceeded -j ACCEPT
>iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT

##1.16 Open Range of Ports
>iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 7000:7010 -j ACCEPT

##1.17 Open Ragne of IP Address
>iptables -A INPUT -p tcp --destiantion-port 80 -m iprange --src-range 192.168.1.100-192.168.1.200 -j ACCEPT
 
VS NAT:
>iptables -t nat -A POSTROUTING -j SNAT --to-source 192.168.1.20-192.168.1.25

##1.18 Thiết lập kết nối và Restart Firewall
Khi bạn restart dịch vụ firewall nó sẽ drop các kết nối đã thiết lập 
Edit: /etc/sysconfig/iptables-config and set IPTABLES_MODULES_UNLOAD as follow
>IPTaBLES_MODULES_UNLOAD = no

##1.19 Block or Open Common Ports
*Replace ACCEPT with DROP to block port*
* open port ssh tcp port 22
>iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
>iptables -A INPUT -s 192.168.1.0/24 -m state --state NEW -p tcp --dport 22 -j ACCEPT

* open cups (printing service) udp/tcp port 631 for LAN user
>ipbtales -A INPUT -s 192.168.1.0/24 -p udp -m udp --dport 631 -j ACCEPT
>iptables -A INPUT -s 192.168.1.0/24 -p tcp -m tcp --dport 631 -j ACCEPT

* allow time sync via NTP for LAN user (open udp port 123)
>iptables -A INPUT -s 192.168.1.0/24 -m state --state  NEW -p udp --dport 123 -j ACCEPT

* open tcp port 25 (smtp) for all
>iptables -A INPUT -m state --state NEW -p tcp -dport 25 -j ACCEPT

* open dns server ports for all
>iptables -A INPUT -m state --state NEW -p udp --dport 53 -j ACCEPT
>iptables -A INPUT -m state --state NEW -p tcp --dport 53 -j 

* open http/https (Apache) server port to all
>iptables -A INPUT -m state --state NEW -p tcp --dport 80 -j ACCEPT
>iptables -A INPUT -m state --state NEW -p tcp --dport 443 -j ACCEPT

* open tcp port 110 (pop3) for all
>iptables -A INPUT -m state --state NEW -p tcp --dport 143 -j ACCEPT

* open access to Samba file server for lan users only
>iptables -A IPNUT -s 192.168.1.0/24 -m state --state NEW -p tcp --dport 137 -j ACCEPT
>iptables -a INPUT -s 192.168.1.0/24 -m state --state NEW -p tcp --dport 138 -j ACCEPT
>iptables -A INPUT -s 192.168.1.0/24 -m state --state NEW -p tcp --dport 139 -j ACCEPT
>iptables -A INPUT -s 192.168.1.0/24 -m state --state NEW -p tcp --pdort 445 -j ACCEPT

* open access to proxy server for lan users only
>iptables -A INPUT -s 192.168.1.0/24 -m state --state NEW -p tcp --dport 3128 -j ACCEPT

* open access to mysql server for lan user only
>iptables -I INPUT -p tcp --dport 3306 -j ACCEPT


##1.21 Restrict the number of parallel connections to a server per  client IP
>iptables -A INPUT -p tcp --syn --dport 22 -m connlimit --connlimit-above 3 -j REJECT

Set HTTP request to 20
>iptables -p tcp --syn --dport 80 -m connlimit --connlimit-above 20 --connlimit-mask 24 -j DROP

##1.22 HOw to : use iptables like a pro

>man iptables
>iptables -h
>iptables -j DROP -h

##1.22.1 testing your firewall
>netstat -tulpn

Find out if tcp port 80 open or not
>netstat -tulpn | grep :80

if port 80 is not open, start the apache 
>service httpd start

Để chắc chắn iptables cho phép cổng 80
>ipables -L INPUT -v -n | grep 80

Otherwise open port 80 using the iptables for all users:
>iptables -A INPUT -m state --state NEW -p tcp --dport 80 -j ACCEPT
>service iptables save

using the telnet command to see if firewall to connect to port 80
>telnet www.cyberciti.biz 80

you can also using nmap to probe your own server using the following syntax:
>nmap -sS -p 80 www.cyberciti.biz 




