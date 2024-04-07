## Tugas 2 Load Balancing dengan Round Robin
![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/skema.jpg?raw=true)
Untuk skema webserver sister.local bisa diakses ke [Tugas 1]([http://www.contohlink.com](https://github.com/Ando2341/Sistem-Terdistribusi/tree/8578f11da250dd32963dcd61a2f8cb7d8d45892a/Tugas%201))

# Membuat app.sister.local
- Masuk ke direktori 
    ```sh
    cd /etc/nginx/sites-enabled
    ```
- Copy file default menjadi app.sister.local
    ```sh
    cp default app.sister.local
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/cpy-dflt-app.sister.png?raw=true)
- Edit file app.sister.local
    ```sh
    sudo nano app.sister.local
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/edit-app.sister.png?raw=true)
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
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/AppSister-html.png?raw=true)
- Buka notepad run as administrator lalu buka file yang ada di partisi C:\Windows\System32\drivers\etc\hosts Tambahkan 127.0.0.1 sister.local
![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/notepad.png?raw=true)

- Buka browser ketikkan app.sister.local
![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/brows-app.sister.png?raw=true)

Web server app.sister.local sudah selesai selanjutnya install microservice3,4,5,
-  Install Debian 10 untuk microservice 3,4 dan 5
    ```sh
    sudo lxc-create -n microservice3 -t download -- --dist debian --release buster --arch amd64 --force-cache --server images.linuxcontainers.org
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/install-mcsv-3.png?raw=true)
    ```sh
    sudo lxc-create -n microservice4 -t download -- --dist debian --release buster --arch amd64 --force-cache --server images.linuxcontainers.org
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/install-mcsv-4.png?raw=true)
    ```sh
    sudo lxc-create -n microservice5 -t download -- --dist debian --release buster --arch amd64 --force-cache --server images.linuxcontainers.org
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/install-mcsv-5.png?raw=true)

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
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/lxc-ls.png?raw=true)
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
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/edit-hosts-mcsv3.png?raw=true)
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/edit-hosts-mcsv4.png?raw=true)
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/edit-hosts-mcsv5.png?raw=true)
    
## LOAD BALANCING
- Selanjutnya konfigurasi hosts di parent untuk menambahkan ke3 ip microservice tadi
    ```sh
        sudo nano /etc/hosts
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/etc-hosts.png?raw=true)
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
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/logging.png?raw=true)
- Selanjutnya edit File app.sister.local
    ```sh
    sudo nano app.sister.local
    ```
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/edit-app.sister.local.png?raw=true)
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
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/brows-app.sister.png?raw=true)
    Maka hasilnya begini 
    ![alt text](https://github.com/Ando2341/Sistem-Terdistribusi/blob/main/Tugas%202/assets/log.png?raw=true)
    Bisa kita lihat pada upstream_addr setiap log nya dia dimulai berurut dari microservice3, microservice4, microservice5. 
    
    
    
    
    
