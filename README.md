一、常用軟件包

apt-get -y update && apt-get -y install unzip zip wget curl  nano sudo ufw socat ntp ntpdate gcc git vim

二、安裝Caddy

安裝主文件

curl https://getcaddy.com | bash -s personal

配置 Caddy
文件目錄

mkdir /etc/caddy
chown -R root:root /etc/caddy
mkdir /etc/ssl/caddy
chown -R root:www-data /etc/ssl/caddy
chmod 770 /etc/ssl/caddy 
                                 
網站目錄

mkdir -p /var/www/html
chown -R www-data:www-data /var/www

配置文件填充内容

touch /etc/caddy/Caddyfile
nano /etc/caddy/Caddyfile

chown root:root /etc/caddy/Caddyfile
chmod 644 /etc/caddy/Caddyfile

偽站安裝

wget https://github.com/jpalidev/jpalidev.github.io/archive/master.zip
unzip master.zip
cp -rf jpalidev.github.io-master/* /var/www/html

系統服務

wget https://raw.githubusercontent.com/caddyserver/caddy/master/dist/init/linux-systemd/caddy.service
cp caddy.service /etc/systemd/system/
chown root:root /etc/systemd/system/caddy.service
chmod 644 /etc/systemd/system/caddy.service
systemctl daemon-reload

啓動 ,偽站查看

systemctl enable caddy
systemctl start caddy
systemctl status caddy

三、安裝V2RAY

bash <(curl -Ls https://install.direct/go.sh)

配置文件

nano /etc/v2ray/config.json

启动 V2Ray

systemctl start v2ray
systemctl stop v2ray
systemctl restart v2ray

四、BBR加速優化

修改系统变量

echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf

保存生效

sysctl -p

查看内核是否已开启BBR

sysctl net.ipv4.tcp_available_congestion_control
sysctl net.ipv4.tcp_congestion_control

lsmod | grep bbr
返回值有 tcp_bbr 模块即说明 bbr 已启动

TLS开启OSCP

openssl s_client -connect www.cityjw.gq:443 -status -tlsextdebug < /dev/null 2>&1 | grep -i "OCSP response"

开启fastop

TCP fastopen
echo 3 > /proc/sys/net/ipv4/tcp_fastopen
