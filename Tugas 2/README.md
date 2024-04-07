## Tugas 2 Load Balancing dengan Round Robin
Gambar Rangkaian
Untuk skema webserver sister.local bisa diakses ke link ini 
# Membuat app.sister.local
- Masuk ke direktori 
    ```sh
    cd /etc/nginx/sites-enabled
    ```
- Copy file default menjadi app.sister.local
    ```sh
    cp default app.sister.local
    ```
- Edit file app.sister.local
    ```sh
    sudo nano app.sister.local
    ```
    gambar edit-app.sister
- Selanjutnya pindah direktori 
    ```sh
    dc /var/www/html
    ```
- Copy file index.nginx-debian.html menjadi AppSister.html
    ```sh
    cp index.nginx-debian.html AppSister.html
    ```
- Edit file AppSister.html
    ```sh
    sudo nano AppSister.html
    ```
    Gambar AppSister-html
- Buka notepad run as administrator lalu buka file yang ada di partisi C:\Windows\System32\drivers\etc\hosts Tambahkan 127.0.0.1 sister.local
gambar notepad

- Buka browser ketikkan app.sister.local
gambar browser

Web server app.sister.local sudah selesai selanjutnya install microservice3,4,5,
-  Install Debian 10 untuk microservice 3,4 dan 5
    ```sh
    sudo lxc-create -n microservice3 -t download -- --dist debian --release buster --arch amd64 --force-cache --server images.linuxcontainers.org
    ```
    gambar install mcsv3
    ```sh
    sudo lxc-create -n microservice4 -t download -- --dist debian --release buster --arch amd64 --force-cache --server images.linuxcontainers.org
    ```
    gambar install mcsv4
    ```sh
    sudo lxc-create -n microservice5 -t download -- --dist debian --release buster --arch amd64 --force-cache --server images.linuxcontainers.org
    ```
    gambar install mcsv5

- Jalankan ketiga microservice tersebut
    ```sh
    sudo lxc-start -n microservice3
    ```
    ```sh
    sudo lxc-start -n microservice4
    ```    
    ```sh
    sudo lxc-start -n microservice5
    ```    
-  Cek apakah sudah jalan apa engga mengggunakan perintah 
    ```sh
    lxc-ls -f
    ```
    gambar lxc-ls
- Attach ke 3 microservice tadi 
    ```sh
    sudo lxc-attach -n microservice3
    ```
    ```sh
    sudo lxc-attach -n microservice4
    ```
    ```sh
    sudo lxc-attach -n microservice5
    ```
- Install nano dan nginx di microservice 3,4,5
    ```sh
    apt install nano
    ```
    ```sh
    apt install nginx
    ```
- Selanjutnya tambahkan di /etc/hosts pada microservice 3,4,5
    ```sh
    sudo nano /etc/hosts
    ```
    gambar edit-host mscv3
    gambar edit-host mscv4
    gambar edit-host mscv5
    
## LOAD BALANCING
- Selanjutnya konfigurasi hosts di parent untuk menambahkan ke3 ip microservice tadi
    ```sh
        sudo nano /etc/hosts
    ```
    gambar etc-hosts
- Selanjutnya pindah ke direktori /etc/nginx/sites-enabled
    ```sh
    cd /etc/nginx/sites-enabled
    ```
- Edit file nginx.conf
    ```sh
    sudo nano ../nginx.conf
    ```
    Tambahkan pada bagian logging settings
    ```sh
    log_format logger-json escape-json '{"source": "nginx", "time": $msec, "resp_body_size": $body_bytes_sent, "host": "$http_host", "address": "$remote_addr", "request_length": $request_length, "method": "$request_method", "uri": "$request_uri", "status": $status, "user_agent": "$http_user_agent", "resp_time": $request_time, "upstream_addr": "$upstream_addr"}';
    ```
    gambar logging
- Selanjutnya edit File app.sister.local
    ```sh
    sudo nano app.sister.local
    ```
    gambar edit-app.sister.local
- Cek konfigurasi
    ```sh
    nginx -t 
    ```    
- Reload
    ```sh
    nginx -s reload
    ```
- Untuk melihat bagaimana load balancing nya 
    Ketikkan 
    ```sh
     sudo tail -f /var/log/nginx/app.sister.local-access.log
    ```
    Lalu Buka browser ketikkan app.sister.local dan lakukan refresh beberapa kali
    gambar browser
    Maka hasilnya begini 
    gambar log
    Bisa kita lihat pada upstream_addr setiap log nya dia dimulai berurut dari microservice3, microservice4, microservice5. 
    
    
    
    
    