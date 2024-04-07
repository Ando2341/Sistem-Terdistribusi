# Mencoba LXC
Membuat rangkaian Sistem Terdistribusi seperti gambar dibawah ini menggunakan:
![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/skema.jpg?raw=true)
- LXC
- Ubuntu WSL
- Microservice 1 Ubuntu Focal
- Microservice 2 Ubuntu Bionic

# Skema 1 Web-Server sister.local

- Install Ubuntu 22.04  
- Buka Ubuntu 22.04
- Mengganti Source list Ubuntu
    ```sh
    sudo nano /etc/apt/sources.list
    ```
    menjadi 
    ``````sh
    deb http://archive.ubuntu.com/ubuntu/ jammy main restricted universe multiverse
    deb-src http://archive.ubuntu.com/ubuntu/ jammy main restricted universe multiverse
    
    deb http://archive.ubuntu.com/ubuntu/ jammy-updates main restricted universe multiverse
    deb-src http://archive.ubuntu.com/ubuntu/ jammy-updates main restricted universe multiverse
    
    deb http://archive.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse
    deb-src http://archive.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse

    deb http://archive.ubuntu.com/ubuntu/ jammy-backports main restricted universe multiverse
    deb-src http://archive.ubuntu.com/ubuntu/ jammy-backports main restricted universe multiverse

    deb http://archive.canonical.com/ubuntu/ jammy partner
    deb-src http://archive.canonical.com/ubuntu/ jammy partner
    ``````
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/source-list.png?raw=true)
    
    ```sh
    sudo apt update; sudo apt upgrade -y
    ```
- Install lxc
    ```sh
    sudo apt-get install lxc lxctl lxc-templates net-tools  
    ```
- Check konfigurasi 
    ```sh
    sudo lxc-checkconfig
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/lxc-checkkonfigurasi.png?raw=true)
    
- Menampilkan template container yang tersedia
    ```sh
    sudo ls /usr/share/lxc/templates/
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/lxc-template.png?raw=true)
    
- Install nginx
    ```sh
    sudo apt install nginx nginx-extras
    ```
- Masuk ke direktori nginx 
    ```sh
    cd /etc/nginx/sites-enabled
    ```
    Copy file default menjadi sister.local
    ```sh
    ls
    sudo cp default sister.local
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/cp-sister.local.png?raw=true)

- Edit sister.local
    ```sh
    sudo nano sister.local
    ```
    menjadi
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/edit-sister.local.png?raw=true)
    
    cek konfigurasi jika syntax is ok lalu restart
    ```sh
    sudo nginx -t
    sudo nginx -s reload
    ```
- Masuk ke direktori /var/www/html 
    ```sh
    cd /var/www/html
    ```
    lihat isi direktorinya lalu copy index.nginx-debian.html menjadi index.html
    ```sh
    ls
    sudo cp index.nginx-debian.html index.html
    ```
- Edit file index.html
    ```sh
    sudo nano index.html
    ```
    menjadi 
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/edit-index.html.png?raw=true)
    
- Buka notepad rund as Admin lalu buka file yang ada di partisi C:\Windows\System32\drivers\etc\hosts
Tambahkan 127.0.0.1 sister.local 
![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/etc-hosts.png?raw=true)

- Selanjutnya buka browser ketikkan sister.local 
    hasilnya 
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/browser-sister.local.png?raw=true)

# Skema 2 Membuat sister.local/blog dan /aboutus menggunakan microservices
Jadi kita akan membuat microservices menggunakan ubuntu 20 dan ubuntu 18 ubuntu local di dalam ubuntu 22
- Buka 2 ubuntu  terminal untuk mengisntall ubuntu 20 & ubuntu 18
- install Ubuntu 20 & 18
    Ubuntu 20 untuk microservice1
    ```sh
    sudo lxc-create -n microservice1 -t download -- --dist ubuntu --release focal --arch amd64 --force-cache --server images.linuxcontainers.org
    ```
    Ubuntu 18 untuk microservice2
    ```sh
    sudo lxc-create -n microservice2 -t download -- --dist ubuntu --release bionic --arch amd64 --force-cache --server images.linuxcontainers.org
    ```
    
- Jalankan ke 2 microservicenya 
    ```sh
    sudo lxc-start -n microservice1
    sudo lxc-start -n microservice2
    ```
    Untuk mengeceknya menggunakan
    ```sh
    sudo lxc-ls -f
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/lxc-ls.png?raw=true)

- Attach ke microservice 1 & Microservice 2
    ```sh
    sudo lxc-attach -n microservice1
    sudo lxc-attach -n microservice2
    ```
- Set password tiap  microservice
    ```sh
    passwd
    ```
- Install nano di tiap microservice 
    ```sh
    apt install nano 
    ```
- Cek Ip di tiap microservice 
    ```sh
    ip a
    ```
- Edit netplan setiap microservice
    ```sh
    sudo nano /etc/netplan/10-lxc.yaml
    ```
    Edit sesuai ip yang tadi sudah di cek
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/netplan-1.png?raw=true)
     ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/netplan-2.png?raw=true)
    
- Selanjutnya ketikkan di setiap microservice
    ```sh
    sudo netplan apply
    ```
    update & upgrade
    ```sh
    apt updates; apt upgrade -y
    ```

Jadi microservice nya sudah kebuat 
Sekarang membuat webserver untuk /blog dan /aboutus
- Install nginx di tiap microservice
    ```sh 
    sudo apt install nginx nginx-extras
    ```
- Pindah direktori dan edit file default index.nginx
    ```sh
    cd var/www/html/
    ```
    Edit file defaultnya yaitu index.nginx-debian.html
    ```sh
    nano index.nginx-debian.html
    ```
    menjadi 
    microservice1
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/index-nginx-1.png?raw=true)
    microservice2
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/index-nginx-2.png?raw=true)
    
    Jalankan curl localhost untuk melihat hasilnya 
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/curl-localhost1.png?raw=true)
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/curl-localhost2.png?raw=true)

- Selanjutnya pindah ke direktori etc/nginx setiap microservice
    ```sh
    cd /etc/nginx/
    ```
    Pada microservice1 copy default menjadi mscv1.local
    ```sh
    cp default mcsv1.local
    ```
    Pada microservice2 copy default menjadi mscv2.local
    ```sh
    cp default mcsv2.local
    ```
- Edit file mscv.local tiap microservice
    ```sh
    nano mcsv1.local
    nano mcsv2.local
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/mcsv1.local.png?raw=true)
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/mcsv2.local.png?raw=true)

    cek konfigurasi tiap microservice 
    ```sh
    sudo nginx -t
    sudo nginx -s reload
    ```
- Selanjutnya pindah direktori var/www/html
    ```sh
    cd /var/www/html/
    ```
    copy file index.nginx-debian.html setiap microservie menjadi index.html
    ```sh
    cp index.ngin-debian.html index.html
    ```
- Selanjutnya edit file hosts setiap microservice
    ```sh
    sudo nano /etc/hosts
    ```
    tambahkan 127.0.0.1 microservice1 pada microservice 1
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/etc-host-1.png?raw=true)
    
    tambahkan 127.0.0.1 microservice2
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/etc-hosts-2.png?raw=true)
    
- Cek kembali apakah sudah sesuai
    ```sh
    curl mcsv1.local
    curl mcsv2.local
    ```
Selanjutnya kita lanjut ke parent. Kita akan mengarahkan jika ada orang yang ingin mengakses /blog maka dia akan ke microservice1 dan jika ada orang yang ingin mengakses /aboutus maka dia akan ke microservice2 untuk membuat itu kita menggunakan nginx reverse proxy

- Kita harus mendaftarkan domain baru microservice1 dan microservice2 
    ```sh
    Sudo nano /etc/hosts
    ```
    Tambahkan 10.0.3.204 mcsv1.local & 10.0.3.113 mcsv2.local
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/etc-host-parent.png?raw=true)

- Selanjutnya ke file nginx 
    ```sh
    cd /etc/nginx/sites-enabled
    ```
    Edit file sister.local
    ```sh
    sudo nano sister.local
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/edit-sister.local(2).png?raw=true)
    
    cek konfigurasi 
    ```sh
    sudo nginx -t 
    sudo nginx -s reload
    ```
    
- Selanjutnya coba curl sister.local/blog
![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/curl-sister.local-blog.png?raw=true)

- Jika bisa langsung kita coba di browser dengan mengetikkan sister.local/blog & sister.local/aboutus
Hasil /blog
![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/blog.png?raw=true)
Hasil /aboutus
![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%201/assets/about.png?raw=true)

    


    
