
## 說明：這是在練習在AWS中設定Applicaiton load balancer跟auto scaling group ，會提及當中有些設定，並說明遇到的問題以及解決方式。
後續增加WAF設定以及S3連線並且由Athena查詢log，此部分會直接加在文末
### 更新日期2023/5/30

## Application Load Balancer設定

* VPC設:172.31.0.0/16 (要自己建)
* Mapping：選擇要部署的AZ
* Security group:開放80 port
* Listeners and routing :設定target group

### target group建立
* 選擇instance
* 輸入Target group name
* Register targets

## Auto Scaling group

### Create launch template
1.裝linux
2.加上key pair
3.subnet?
4.加上user data(附件)

* 選擇VPC AZ 與 Load Balancer相同
* Attach to an existing load balancer
加入上方製作的applicaiton load balancer
* Health check 開啟
* 設定group size

* 設定完後可以至edit更改Desired Minimum Maximum capcity
* 在Automatic scaling 可以設定增減條件
1. scheduled 根據時間啟動
2. Predictive scaling policies AWS預測
3. Dynamic scaling policies 設定條件(simple step target)


## status check
* 在Auto Scaling group 的Activity可以看Activity Hsitory 查看EC2是否有啟動
* 在Target groups 查看target可以看到開啟的instance

* 設定完後可在Load Balancer介面中的details DNS name 測試建立結果

## 壓力測試
* key word: install stress amazon linux 2
```
sudo amazon-linux-extras install epel -y
sudo yum install stress -y
```
```
stress -c 4
```

## WAF、S3、Athena

### WAF建立
* 進入WAF 點選Create web ACL
**注意Region**
* 選擇要使用CloudFront或者其他Region resource 
* 如果練習可以預設全部開
### S3串接
* 到WAF頁面選擇Logging and metrics 
* 選擇Enable
* 建立S3 bucket，名稱必須為"aws-waf-logs-"開頭
* 建立後可以至S3查看該資料夾

### Athena查詢





## 刪除資料
依序刪除ASG -> Target Group -> Load Balancer -> Launch Template

### 附件 網頁測試
```
#!/bin/bash
# USe this for our user data(script from top to bottom)
# install httpd(Linux 2 version)
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello World from $(hostname -f)</h1>">/var/www/html/index.html

```

### 有啟用的設定(如果要清理)
1. Applicaiton Load Balancer
2. Auto Scaling Gorup
3. Target Group
4. Launch template

