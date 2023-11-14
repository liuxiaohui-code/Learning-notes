# tls

```sh
mkdir -p /usr/local/ca
cd /usr/local/ca

openssl genrsa -aes256 -out ca-key.pem 4096
#执行完如上指令后，会要求我们输入密码才能进行下一步，在这我将密码设置为：1234
#补全CA证书信息
openssl req -new -x509 [-days 365] -key ca-key.pem -sha256 -out ca.pem
#然后依次输入：访问密码、国家、省、市、组织名称、单位名称、随便一个名字、邮箱等。为了省事，组织、单位之类的我都用 niceyoo 代替了。
#niceyoo cn beijing  beijing niceyoo niceyoo niceyoo apkdream@163.com
#生成server-key.pem
openssl genrsa -out server-key.pem 4096
#用CA签署公钥 由于可以通过IP地址和DNS名称建立TLS连接，因此在创建证书时需要指定IP地址。例如，允许使用10.211.55.4进行连接;如果你是用的网址(比如:www.sscai.club)则替换一下即可
openssl req -subj "/CN=10.211.55.4" -sha256 -new -key server-key.pem -out server.csr

#匹配白名单
echo subjectAltName = DNS:$HOST,IP:XX.XX.XX.XX,IP:XX.XX.XX.XX >> extfile.cnf
#使用时将$HOST替换为自己的ip地址或者网址，这取决于你对外暴漏的docker链接是ip还是网址。$HOST上的docker只允许此IP访问，可以为0.0.0.0，允许所有的ip可以链接（但只允许永久证书的才可以连接成功）

#将Docker守护程序密钥的扩展使用属性设置为仅用于服务器身份验证
echo extendedKeyUsage = serverAuth >> extfile.cnf
#生成签名整数
openssl x509 -req -days 365 -sha256 -in server.csr -CA ca.pem -CAkey ca-key.pem \
  -CAcreateserial -out server-cert.pem -extfile extfile.cnf
	#执行后需要输入上方设置的密码
#生成客户端的key.pem
openssl genrsa -out key.pem 4096
openssl req -subj '/CN=client' -new -key key.pem -out client.csr
#要使秘钥适合客户端身份验证，创建扩展配置文件
echo extendedKeyUsage = clientAuth >> extfile.cnf
echo extendedKeyUsage = clientAuth > extfile-client.cnf
#生成签名整数
openssl x509 -req -days 365 -sha256 -in client.csr -CA ca.pem -CAkey ca-key.pem \
  -CAcreateserial -out cert.pem -extfile extfile-client.cnf
  	#生成cert.pem，需要再次输入之前设置的密码：1234
#删除不需要的文件，两个整数签名请求
	#生成后cert.pem，server-cert.pem您可以安全地删除两个证书签名请求和扩展配置文件：
rm -v client.csr server.csr extfile.cnf extfile-client.cnf
#可修改权限
	#为了保护您的密钥免于意外损坏，请删除其写入权限。要使它们仅供您阅读，请按以下方式更改文件模式：
chmod -v 0400 ca-key.pem key.pem server-key.pem
	#证书可以使对外可读的，删除写入权限以防止意外损坏：
chmod -v 0444 ca.pem server-cert.pem cert.pem

#归集服务器证书
cp server-*.pem /etc/docker/
cp ca.pem /etc/docker/
#修改Docker配置使Docker守护程序仅接收来自提供CA信任的证书的客户端的链接
vi /lib/systemd/system/docker.service
#将 ExecStart 属性值进行替换：
ExecStart=/usr/bin/dockerd --tlsverify --tlscacert=/usr/local/ca/ca.pem --tlscert=/usr/local/ca/server-cert.pem --tlskey=/usr/local/ca/server-key.pem -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
#重新加载daemon并重启docker
systemctl daemon-reload && systemctl restart docker



ca.pem
cert.pem
key.pem
```

```
 No route to host
 iptables -F && systemctl restart docker
```

