# note-caidat-OS-bang-LOCAL-REPO
#I. Đặt vấn đề.
Mục đích của bài viết này phục vụ cho việc:
- Cài đặt Openstack mà không phụ thuộc vào đường truyền internet
- Backup, lưu trữ một phiên bản Openstack đã cài đặt hoàn chỉnh để phục vụ các lần cài đặt sau

---

# Thực hiện

Thực hiện theo ý tưởng của bài viết [sau](https://github.com/trananhkma/Local-Repository). 

- Tức là sẽ có một máy ở **local** làm **repository-server**, máy này sẽ chứa tất cả các gói đã được cài đặt thành công. 

- Các máy **client** (máy cài openstack) sẽ download các gói cài đặt này từ **repository-server** thay vì download từ internet.


---

#II. Các bước thực hiện

- 1. Trên **repository-server** tạo 2 folder (sẽ là 2 URI) cho mỗi phiên bản Openstack **IceHouse** và **Juno**
```
mkdir /var/www/html/icehouse
mkdir /var/www/html/juno
```

- 2. Tại các máy ***ĐÃ CÀI ĐẶT THÀNH CÔNG OPENSTACK*** sử dụng lệnh `scp`để copy toàn bộ những gói cài đặt trong folder `/var/cache/apt/archives/` vào ***folder tương ứng*** của **repository-server**
```
cd /var/cache/apt/archives/
scp -r * root@ip_repository_server:/var/www/html/icehouse     (Nếu là Icehouse) 
scp -r * root@ip_repository_server:/var/www/html/juno         (Nếu là Juno)
```

- 3. Trở lại **repository-server** `cd` đến mỗi folder và thực hiện lệnh sau
```
cd /var/www/html/icehouse 
dpkg-scanpackages . /dev/null | gzip -9c > Packages.gz  (Nếu là Icehouse)
cd /var/www/html/juno
dpkg-scanpackages . /dev/null | gzip -9c > Packages.g   (Nếu là Juno)
```

- 4. Tại máy ***CLIENT ( TỨC LÀ MÁY CÀI ĐẶT MỚI  OPENSTACK )*** thực hiện lệnh như sau
```
mv /etc/apt/sources.list /etc/apt/sources.list.bak
vi /etc/apt/sources.list
```

*Thêm dòng sau:*
```
deb http://172.16.69.181/icehouse ./    (Nếu cài Icehouse)
deb http://172.16.69.181/juno ./        (Nếu cài Juno)
```

- 5. thực hiện lệnh

```
apt-get update
```

- 6. Cài đặt Openstack như bình thường bằng [shell-script](https://github.com/vietstacker/openstack-juno-multinode-U14.04-v1/blob/master/hd-caidat-openstack-multi-node-ubuntu14.04.md) `hoặc` [doc](https://github.com/hocchudong/Install_Openstack_Juno)

---
#END

