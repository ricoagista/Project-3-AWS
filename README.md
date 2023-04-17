<!-- Project Logo -->
<br/>
<div align="center">
    <img src="https://logos-world.net/wp-content/uploads/2021/08/Amazon-Web-Services-AWS-Logo.png" alt="Logo" width="100">
    <h3 align="center">School Project - AWS Project 4</h3>
     <p align="center">
        Rangkuman dan catatan ini dikhususkan untuk tugas AWS Project 4
    </p>
    <br />
</div>

# Kasus
<p align="center">
    
    Anda mendapatkan project untuk membuat 2 web (statis dan dinamis)

</p>

# Goal
<p align="center">
    
    1. User dapat membuat web dinamis di instance Ubuntu
    
    2. User dapat membuat web statis di instance Debian
    
    3. User dapat menghubungkan web dinamis dengan database
    
    4. User dapat membuat direktori dan file di masing-masing EBS
    
    5. Kedua EC2 (Debian dan Ubuntu) dapat terhubung ke EFS
</p>

# Ketentuan
<p align="center">
    
    1. Website diupload di Github

    2. Membuat dua instance dengan OS yang berbeda

    3. Instance 1 menggunakan OS Ubuntu

    4. Instance 2 menggunakan OS Debian

</p>

# Topologi Project
<div align="center">
<img src="images/topologi.png" alt="Logo" width="500">
</div>

Ini merupakan contoh topologi yang akan kita gunakan untuk praktek di dalam AWS Management Console.
Berikut beberapa service AWS yang kita butuhkan untuk praktek :

- **VPC**
- **AWS EC2**
- **EFS**
- **RDS**
- **AWS Auto Scaling**
- **Auto Balancing**

# Membuat VPC

Silahkan buka service VPC lalu pilih `Create VPC`. Setelah itu silahkan buat VPC dengan contoh konfigurasi seperti dibawah ini :

- **Name**          : Project4
- **IP CIDR**       : 192.168.1.0/24
- **AZs**           : 2 (US-1a and US-1b)
- **Subnets**       : 2 (2 public and 2 private) 
- **NAT Gateways**  : 1 per AZ
- **VPC endpoints** : None
<div align="center">
<img src="images/vpc.png" alt="Logo">
</div>

# Membuat Database

Silahkan buka service RDS lalu pilih `Create database`. Setelah itu silahkan buat database dengan contoh konfigurasi seperti dibawah ini
- **Engine**  : MySQL, Memilih MySQL karena lebih mudah digunakan dan umum
- **Templates**  : Free Tier,
- **DB Instance Name** : db-project4, Nama database servernya bebas
- **Master Username**  : admin, Nama username bebas
- **Master Password**  : 12345678, Password username bebas
- **DB Instance Class**  : db.t3.micro, Menggunakan type instance t3.micro
- **Storage Type**  : General Purpose SSD (gp2), Berikut perbedaan jenis storage gp2 dan gp3
<img src="images/jenis-storage.png" alt="Logo">

- **Allocate Storage** : 20GiB, Menggunakan storage SSD dengan kapasitas sekitar 20GB
- **Storage Autoscaling** : Enable, Otomatis menambah kapasitas storage ketika penuh hingga mencapai batas autoscaling yaitu sekitar 1000GB / 1TB
- **Compute Resource** : Don't connect to an EC2 compute storage, Memilih untuk tidak mengsetup database ke EC2 secara langsung
- **Network Type** : IPv4, Hanya menggunakan IP dengan versi 4
- **VPC** : project4-vpc, Pilih VPC yang berusan kita buat tadi
- **DB Subnet Group** : Create New, Membuat subnet group baru
- **Public Access** : No, Karena RDS bertepatan pada subnet private AZ US-1a sesuai dengan gambar topologi diatas
- **VPC Security Group** : Create New, Membuat security group baru untuk konektivitas
- **Security Group Name** : RDS-sg, Untuk nama terserah kalian
- **Availibity Zone** : us-east-1a, Memilih us-1a karena subnet tersebut merupakan subnet private dari AZ us-east-1a sesuai dengan gambar topologi 

<h3>Additional Configuration RDS</h3>

Karena pembuatan database ini hanya digunakan untuk praktek atau eksperimen, kita perlu mematikan beberapa fitur dari _additional configuration rds_ yaitu
- **auto backup** : disable, mematikan sistem auto backup
- **encryption** : disable, mematikan sistem enkripsi pada database
- **auto minor** : disable, mematikan sistem maintenance yaitu minor version upgrade

# Membuat Instance EC2 dan autoscaling

<h3>Membuat Template EC2</h3>
Launch Template pada AWS EC2 adalah sebuah template yang digunakan membuat instance secara konsisten. Dengan menggunakan launch template, pengguna dapat membuat instance dengan konfigurasi yang sama secara konsisten, sehingga tidak perlu lagi melakukan konfigurasi manual setiap kali membuat instance.
</br>

<img src="images/templet.png" align="right" 
alt="">

Silahkan buka service EC2, jika halaman dashboard sudah terbuka dibagian sidebar atau panel sebelah kiri terdapat menu `Launch Templates`, klik menu tersebut dan akan diarahkan untuk membuat EC2 launch template dan klik `Create launch template`.

Silahkan ikuti contoh konfigurasi untuk membuat template EC2 seperti di bawah ini :
- **Template Name** : template-project4, Nama dari template yang di buat
- **Template Ver Desc** : praktek project4, Deskripsi untuk template
- **OS Images** : Debian, Kita akan mencoba membuat instance dengan OS Linux Debian
- **Instance Type** : t3.micro, Karena dengan type instance t3.micro memiliki spesifikasi yg agak besar yaitu 2vCPU dan 1GB RAM
- **Key Pair** : Vockey, Key pair lebih mudah di akses menggunakan vockey
- **Security Group Name** : template-webserver, Membuat security group baru dengan nama terserah
- **Desc SG** : template, Deskripsi security terserah
- **Inbound Rules** : SSH, HTTP, HTTPS, source type anyware semua.

<h3>Membuat Autoscaling Group</h3>
Autoscaling group memungkinkan pengguna untuk mengatur skala otomatis pada instance EC2. Pengguna dapat mengatur jumlah instance EC2 yang dibuat secara otomatis sesuai dengan permintaan, sehingga dapat meminimalkan biaya dan memaksimalkan kinerja instance.

<img src="images/auto.png" align="right" 
alt="" width=280>

Silahkan buka service EC2, jika halaman dashboard sudah terbuka dibagian sidebar atau panel sebelah kiri terdapat menu `Auto Scaling Groups`, klik menu tersebut dan kita akan diarahkan untuk membuat EC2 launch template dan klik `Create Auto Scaling Group`.

Silahkan ikuti contoh konfigurasi untuk membuat auto scaling group seperti di bawah ini :
- **Auto Scaling Group Name** : ASG-project4, Nama terserah
- **Launch Template** : template-project4, Pilih template yang barusan di buat
- _**Next**_
- **VPC** : project4-vpc, Pilih VPC yang barusan dibuat tadi
- **Az Dan Subnet** : private 1a dan private 1b, Pilih subnet private 1a dan 1b karena instance akan di taruh kedalam subnet tersebut sesuai dengan topologi diatas
- _**Next**_
- **Load Balacing** : Attach to a new load balancer, Dengan memilih pilihan tersebut akan secara otomatis instance yang di konfigurasi dengan auto scaling akan terpasang auto balancer
- **Load Balancer Type** : Application Load Balancer
- **Load balancer scheme** : internet-facing, jenis load balancer yang ditempatkan di jaringan publik atau internet-facing subnet.
- **Network Mapping Az dan Subnets** : Pilih public subnet semua dari US-1a dan US-1b
- **Listeners and routing** : Create a target group
- **Health Checks** : ELB Checklist, Fungsi utama dari ELB adalah untuk meningkatkan ketersediaan dan skalabilitas aplikasi, memastikan bahwa permintaan pengguna diproses oleh instance EC2 yang tersedia, dan mengelola kesehatan instance EC2 untuk mencegah lalu lintas yang dibagikan ke instance yang tidak sehat.
- _**Next**_
- **Group Size** : Desire Capacity=2, Mini Capacity=2, Max Capacity 5. Yang berfungsi autoscaling akan secara otomatis membuat 2 instance sekaligus dengan maksimal kapasitas 5
- _**Next**_
- _**Next**_
- _**Next**_
- _**Create Auto Scaling group**_

Silahkan untuk check kembali instance, target group, dan load balancer apakah sudah sukses berjalan normal. Pastikan untuk instance sudah terdapat 2 instance dengan OS Debian dan health check pada target group berstatus health warna hijau.

<h3>Mengatur Security Group RDS</h3>

- Buka security group RDS-sg
- Pilih edit inbound rules
- remove rule MySQL
- lalu add lagi rule MySQL
- Sourcenya arahin ke template-webserver (security group yang barusan dibuat lewat auto scaling group)
- Save

<h3>Membuat Security Group EFS</h3>

Membuat security group baru untuk kebutuhan EFS yang berfungsi untuk memberi akses EFS ke instance.
- Create Security Group 
- Security Group Namenya EFS-sg
- Deskripsi bebas
- Pilih VPC project4-vpc
- Add rule NFS
- Sourcenya arahin ke template-webserver (security group yang barusan kita buat di auto scaling group)

<h3>Membuat instance bastion host</h3>

Sesuai dengan topologi diatas fungsi bastion bertujuan untuk mengakses instance yang berada pada subnet private dan silahkan untuk membuat bastion host dengan contoh konfigurasi seperti di bawah ini

- **Name** : Bastion Host
- **Image** : Debian
- **Key Pair** : Vockey
- **VPC** : project4-vpc
- **Subnet** : public us-ease-1b, Sesuai dengan topologi bastion host berada pada public subnet AZ 2 yaitu us-east-1b
- **Public IP** : enable
- **Security Group** : Create New dengan nama bastion-sg, description bebas
- **Rule** : SSH, HTTP, HTTPS, source anyware semua
- **Launch Instance**  

# Membuat EFS

Pilih ``Customize`` dan buatlah EFS dengan contoh konfigurasi seperti dibawah ini :
- **Name** : efs-project4, Nama terserah
- **Storage Class** : standart, Dengan memilih opsi standart memiliki jangkauan multiple AZs
- **Automatic Backup** : disable, Karena EFS hanya digunakan praktek jadi untuk auto backup tidak terlalu penting
- **Encryption** : disable, Karena EFS hanya digunakan praktek jadi untuk sistem encryption data tidak terlalu penting
- **Performance Setting** : Bursting, Fitur bursting memungkinkan file system EFS untuk menangani beban kerja yang lebih tinggi secara dinamis
- _**Next**_
- **VPC** : project4-vpc
- **Subnet** : Semua subnet di arahkan ke private
- **Security Group** : Semua security group arahkan ke EFS-sg
- _**Next**_
- _**Next**_
- _**Create**_

# Konfigurasi Linux
Disini saya menggunakan learner lab AWS Academy

<img src="images/ppk.png" 
alt="" width="280" align="center">

Sebelum memulai konfigurasi harap download terlebih dahulu file ppk untuk SSH Key
Silahkan buka SSH Client kalian seperti Putty, Bitvise SSH, Termius, dll dan konekkan bastion host ke SSH Client

Setelah berhasil terkoneksi ke Bastion Host, kita akan melakukan konfigurasi web server yang berada pada AZ 1

- Connect ke webserver AZ 1
```sh
ssh admin@(IP)
```

<h3>Command Line Web Server AZ 1</h3>

1. Masuk root user
```sh
sudo su
```

2. Update dan upgrade sistem
```sh
apt update -y && apt upgrade -y
```

3. Download dan mengeksekusi NodeSource instalasi
```sh
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```

4. Install nodejs
```sh
apt install nodejs -y
```

5. Menginstall beberapa package untuk kebutuhan server
```sh
apt install rsync build-essential git default-mysql-client -y
```

6. Install package binutils
```sh
apt install binutils -y
```

7. Download efs-utils tool
```sh
git clone https://github.com/aws/efs-utils.git
```

8. Masuk ke direktori efs-utils
```sh
cd efs-utils
```

9. Menjalankan script bash efs-utils
```sh
./build-deb.sh
```

10. Instaling amazon efs utils
```sh
apt -y install ./build/amazon-efs-utils*deb
```

11. Back to home
```sh
cd /home/admin
```

12. Download app deploymentnya
```sh
git clone https://github.com/adinur21/ukk.git
```

13. Masuk ke dalam direktori ukk dan install npm package
```sh
cd ukk/ && npm install
```

14. Install node modul ke dalam project
```sh
npm install --save express
npm install -g nodemon
npm install -g cors
npm install -g body-parser
```

15. Buka direktori ``ukk/src/model`` dan edit file dbConnection.js untuk mengsetup database connection
```js
const mySql = require("mysql")

const db = mySql.createPool({
  host: "db-project4.cwtinss4gobn.us-east-1.rds.amazonaws.com",
  user: "admin",
  password: "12345678",
  database: "cloud_api"
})

exports.db = db;
```

16. MYSQL RDS Rebuild
```sh
mysql -h database-1.cefenxcilrp4.us-east-1.rds.amazonaws.com -u admin -p <<EOF

# Show existing databases
show databases;

# Create the datasiswa database
create database cloud_api;

# Use the datasiswa database
use cloud_api;

# create table
  CREATE TABLE guru (
  id_guru int(11) AUTO_INCREMENT PRIMARY KEY,
  nama_guru varchar(255),
  mapel_guru varchar(255),
  sekolah_guru varchar(255)
  );

# Import the SQL script to create tables and populate data
INSERT INTO guru (nama_guru, mapel_guru, sekolah_guru) VALUES ('Adi','cloud','SMK Telkom Malang');
INSERT INTO guru (nama_guru, mapel_guru, sekolah_guru) VALUES ('OmTegar','Kuli Jawa','STM Kuli Telkom');
INSERT INTO guru (nama_guru, mapel_guru, sekolah_guru) VALUES ('Nopal','Kuli Jawa','STM Kuli Telkom');
INSERT INTO guru (nama_guru, mapel_guru, sekolah_guru) VALUES ('Julpan','Kuli Jawa','STM Kuli Telkom');

# Show tables in the datasiswa database
show tables;

# Select data from the users table
SELECT * FROM guru;

# Exit the MySQL prompt
EOF
```

17. Testing your project
```sh
<dns endpoint ELB>/api/guru/v1
```

18. Back to home
```sh
cd /home/admin
```

19. make direktori EFS
```sh
mkdir efs
```

20. Mount EFS ke direktori EFS server debian
```sh
mount -t efs -o tls fs-09944e3f023b56381:/ /home/admin/efs
```
Untuk command line mounting bisa lihat melalui EFS console dengan cara buka service EFS > pilih efs-project4 > pilih attach.

21. Silahkan cek apakah efs sudah berhasil terpasang
```sh
df -h
```

22. Melakukan rsync data log server kedalam EFS
```sh
rsync -avz /var/log /home/admin/efs
```

23. Auto script mytemplate userdata
Optional (Ga wajib)
```sh
#!/bin/bash

RDSmountpoint="database-1.c2tochjn7qjp.us-east-1.rds.amazonaws.com"
UsernameRDS="admin"
passwordRDS="admin123"
DNSEFS="fs-0ef13ba7dec46de8b.efs.us-east-1.amazonaws.com"
EFSid="fs-0ef13ba7dec46de8b"

sudo apt update
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install nodejs -y

sudo apt-get install rsync build-essential git mysql-client -y

sudo apt install binutils -y
git clone https://github.com/aws/efs-utils
cd efs-utils
./build-deb.sh
sudo apt-get -y install ./build/amazon-efs-utils*deb

cd /home/ubuntu
sudo mkdir efs
sudo mount -t efs -o tls $EFSid:/ efs
df -h

cd /home/ubuntu/ && git clone https://github.com/adinur21/ukk.git
cd ukk/ && npm install

npm install --save express
npm install -g nodemon
npm install -g cors
npm install -g body-parser

cd /home/ubuntu/ukk/src/model/
sudo cat <<EOF >dbConnection.js
const mySql = require("mysql")

const db = mySql.createPool({
  host: "$RDSmountpoint",
  user: "$UsernameRDS",
  password: "$passwordRDS",
  database: "cloud_api"
})

exports.db = db;
EOF

cd /home/ubuntu/efs/
sudo rsync -azP /var/log/ $DNSEFS
```


# Contributor

- [BangAthar](https://github.com/BangAthar)
- [OmTegar](https://github.com/OmTegar)
