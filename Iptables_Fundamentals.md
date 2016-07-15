#1. Giới Thiệu về IPTABLES
Iptables là firewall được cấu hình và hoạt động trên nền Conlose. 
Trên CentOS nó hoạt động như một service nhưng trên ubuntu Desktop thì như một app thông thường. :D
Git này Tocxu viết khi tìm hiểu và thực hành với ubuntu desktop 15.10 
#2. Sử dụng
##2.1 Cài đặt IPTABLES
	*sudo apt-get install iptables*
	*sudo apt-get install ufw*
**trên CentOS thì:** 
	*yum install iptables*
##2.2 Structure
the Iptable of iptables is: Tables -> Chains -> Rules
Iptables có 4 bảng dựng sẵn (bult-in tables)
###2.2.1. FILTER Table
Chịu trách nhiệm lọc gói dữ liệu. Nó gồm có 3 quy tắc nhỏ (chain):
<li>INPUT</li>: lọc gói khi vào trong server
<li>OUTPUT</li> lọc gói khi ra khỏi server
<li>FORWARD</li> lọc gói khi đi đến đến các server khác
###2.2.2 NAT Table
NAT - Network Address translation
<li>PREROUTING</li>: thay đổi địa chỉ đến của gói dữ liệu khi cần thiết
<li>POSTROUTING</li>: thay đổi địa chỉ nguồn của gói dữ liệu khi cần thiết
<li>OUPUT</li>: Nat sử dụng cho các gói dữ liệu xuất phát từ firewall
###2.2.3 MANGLE Table: 
Chịu trách nhiệm thay đổi các bits chất lượng dịch vụ trong TCP header như TOS (type of service), TTL (time to live), và MARK
<li>PREROUTING</li>
<li>POSTROUTING</li>
<li>OUTPUT</li>
<li>INPUT</li>
<li>FORWARD</li>
###2.2.4 RAW Table
Các trường hợp  ngoại lệ cấu hình (congiguration excemptions)
<li>PREROUTING</li>
<li>OUTPUT</li>
####2.2.5 Target Values
Target là một hành động sẽ diễn ra khi một gói dữ liệu được kiểm tra và phù hợp với một yêu cầu nào đó. Khi một yêu cầu nào đó đã được nhận dạng, gói dữ liệu cần nhảy (-j ~jump) để thực hiện các xử lý tiếp theo.
<li>ACCEPT</li>: chấp nhận gói tin
<li>DROP</li>:loại bỏ gói tin
<li>QUEUE</li>: firewall đẩy (pass) các gói tin đến không gian người dùng
<li>RETURN</li>: firewall sẽ dừng thực hiện những tập luật tiếp theo trong chuỗi quy tắc hiện hành đối với gói tin này. việc kiểm tra sẽ được trả lại cho chuỗi gọi (the calling chain)
<li>LOG</li>: thông tin gói sẽ được đưa vào syslog để kiểm tra. Iptables tiếp tục xử lý gói tin với luật tiếp theo
<li>REJECT</li>: tương tự DROP nhưng nó sẽ gởi trả lại cho phía người gởi một thông báo lỗi rằng gói tin đã  bị chặn và loại bỏ
<li>MASQUERADE</li>: dùng để thực hiện Source Network address Translation. Mặc định thì địa chỉ IP nguồn sẽ giống như IP nguồn của firewall
###2.3 Các tham số chuyển mạch (switching)
<ul> 
**-t <table>** 
: Nếu không có tables nào, thì bảng FILTER sẽ được áp dụng
</ul>
<ul> 
**-A chain** --append:  nối thêm một luật vào cuối chuỗi (chain) </ul>
<ul> **-C chain** --check: kiểm tra luật đã có chưa </ul>
<ul> **-D chain rulenum** --delete: xóa luật nào đó trong chain</ul>
<ul> **-F [chain]** --flush : xóa hết các luật có trong bảng hoặc trong chain nào đó</ul>
<ul> **-E old-chain new-chain** --rename-chain: thay tên chain</ul>
<ul> **-I chain [rulenum]** --insert: thêm vài chain một luật mới ở vị trí chỉ đinh (mặc định 1 là luật đầu tiên)</ul>
<ul> **-L [chain [rulenum]]** --list-rule: liệt kê các luật có trong một chain hoặc tất cả các chain</ul>
<ul> **-N chain** --new: tạo một chain mới do người dùng định nghĩa </ul>
<ul> **-P chain target** --policy: thay đổi luật của chain sang một target,(khi muốn đổi tất cả các luật trong chain nào đó có cùng một target)</ul>
<ul> **-R chain rulenum** --replace : thay đổi thứ tự luật trong chain</ul>
<ul> **-S [chain [rulenum]]** --list-rules: in (print) các quy tắc vào trong một chain hoặc tất cả các chain</ul>
<ul> **-Z [chain [rulenum]]** --zero </ul>
<ul> **-X [chain]** --delete-chain: xóa chain do người dùng định nghĩa</ul>
###Các tham số OPtion
<ul> **-j target** --jump: hành động cho luật</ul>
<ul> **-v** --verbose: hiển thị thông tin chi tiết cho luật</ul>
<ul> **-n** --numeric: hiển thị địa chỉ và cổng dưới dạng số</ul>
<ul> **-s address[/mask][...]** --source: địa chỉ nguồn</ul>
<ul> **-d address[/mask][...]** --destiantion: địa chỉ đích</ul>
<ul> **-p proto** --protocol: giao thức áp dụng</ul>
#3. 20 ví dụ về IPTABLES cho New SysAdmins
##3.1 Hiển thị trạng thái của Firewall
**iptables -L -n -v**









