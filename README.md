<a name="br1"></a>Project 3 ( Cloud Computing)

• **KASUS**

\- Anda mendapatkan project untuk membuat 2 web (statis dan dinamis)

• **GOAL**

1\. User dapat membuat web dinamis di instance Ubuntu

2\. User dapat membuat web statis di instance Debian

3\. User dapat menghubungkan web dinamis dengan database

4\. User dapat membuat direktori dan file di masing-masing EBS

5\. Kedua EC2 (Debian dan Ubuntu) dapat terhubung ke EFS

• **KETENTUAN**

1\. Website diupload di Github

2\. Membuat dua instance dengan OS yang berbeda

3\. Instance 1 menggunakan OS Ubuntu

4\. Instance 2 menggunakan OS Debian




<a name="br2"></a>5. Name tag EC2 : WebServer\_Ubuntu dan WebServer\_Debian
 IP VPC : 192.168.1.0/25

Subnet Public 1 : 192.168.1.0/27 Subnet Private 1 : 192.168.1.32/27 Subnet Public 2 : 192.168.1.64/27 Subnet Private 2 : 192.168.1.96/27

5\. Membuat 2 EBS Volume untuk di mount di masing-masing instance

6\. Name tag EBS : Volume\_Ubuntu dan Volume\_Debian

7\. User dapat membuat direktori dan file txt di masing-masing EBS

8\. Nama direktori : ebs-ubuntu dengan file ubuntu.txt dan ebs-debian dengan

file ubuntu.txt

9\. Kedua EC2 (Ubuntu dan Debian) dapat terhubung ke EFS (ditest dengan cara
 membuat direktori dari kedua instance)

10\. rsync isi dir web dinamis /var/www/html ke ebs

11\. rsync isi /var/log ke efs





<a name="br3"></a>

