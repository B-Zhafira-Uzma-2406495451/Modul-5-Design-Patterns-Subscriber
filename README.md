# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [✅] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [✅] Commit: `Create Notification model struct.`
    -   [✅] Commit: `Create SubscriberRequest model struct.`
    -   [✅] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [✅] Commit: `Implement add function in Notification repository.`
    -   [✅] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [✅] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [✅] Commit: `Create Notification service struct skeleton.`
    -   [✅] Commit: `Implement subscribe function in Notification service.`
    -   [✅] Commit: `Implement subscribe function in Notification controller.`
    -   [✅] Commit: `Implement unsubscribe function in Notification service.`
    -   [✅] Commit: `Implement unsubscribe function in Notification controller.`
    -   [✅] Commit: `Implement receive_notification function in Notification service.`
    -   [✅] Commit: `Implement receive function in Notification controller.`
    -   [✅] Commit: `Implement list_messages function in Notification service.`
    -   [✅] Commit: `Implement list function in Notification controller.`
    -   [✅] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
1. Dalam tutorial ini, variabel NOTIFICATIONS yang menyimpan Vec<Notification> bersifat global dan diakses oleh
banyak thread secara bersamaan, sehingga kita wajib menggunakan mekanisme sinkronisasi untuk mencegah data race
atau tabrakan memori yang bisa membuat program crash. Kita memilih menggunakan RwLock<>, bukan Mutex<> karena
RwLock menawarkan fleksibilitas yang jauh lebih tinggi dan efisien untuk skenario kita. Berbeda dengan Mutex yang
sangat kaku dan hanya mengizinkan satu thread untuk mengakses data pada satu waktu, baik untuk membaca maupun
menulis, RwLock mengizinkan banyak thread untuk membaca daftar notifikasi secara bersamaan asalkan tidak ada thread
yang sedang menulis. Karena dalam sebuah sistem notifikasi operasi untuk melihat atau membaca pesan biasanya jauh
lebih sering dilakukan dibandingkan operasi menambahkan pesan baru, penggunaan RwLock memastikan aplikasi
BambangShop memiliki performa yang optimal dan terhindar dari antrean proses yang tidak perlu saat banyak pengguna
hanya ingin membaca data.

2. Berbeda dengan Java yang membebaskan mutasi variabel static global dan mengandalkan developer untuk mengatur
keamanan thread secara manual, Rust mendesain bahasanya dengan prinsip memory safety dan pencegahan data race yang
sangat ketat sejak proses kompilasi. Rust menganggap shared mutable state sebagai tindakan yang sangat rentan
terhadap tabrakan data dalam lingkungan multi-threading, sehingga compiler secara bawaan melarang kita
memodifikasinya secara langsung. Selain itu, Rust mewajibkan variabel static biasa untuk diinisialisasi pada saat
compile-time dengan nilai yang sudah pasti, padahal struktur data dinamis seperti Vec atau DashMap memerlukan
alokasi memori dinamis yang hanya bisa dilakukan saat program sedang berjalan. Untuk mengatasi kedua batasan
inilah kita menggunakan library lazy_static yang menunda proses inisialisasi struktur data tersebut hingga pertama
kali dipanggil, dan membungkusnya dengan perlindungan konkurensi seperti RwLock pada Vec agar isinya tetap bisa
dimodifikasi dengan aman tanpa melanggar aturan ketat ownership Rust.

#### Reflection Subscriber-2
1. Saya sudah mempelajari beberapa bagian kode di luar tutorial utama, salah satunya adalah file src/lib.rs. Dari file tersebut, saya
mempelajari bagaimana infrastruktur dasar aplikasi dibangun, seperti penggunaan REQWEST_CLIENT yang berfungsi sebagai klien HTTP
global untuk mengirimkan permintaan asinkron antar aplikasi. Selain itu, saya juga memahami peran APP_CONFIG dalam mengelola
konfigurasi variabel lingkungan secara terpusat, termasuk URL root dan nama instansi, yang sangat membantu dalam memastikan
konsistensi saat menjalankan beberapa instansi Receiver secara bersamaan. Terakhir, saya menemukan bahwa src/lib.rs menyediakan
abstraksi penting untuk penanganan error melalui tipe Result kustom dan fungsi compose_error_response, yang memastikan semua respon
kesalahan dalam sistem memiliki format yang seragam dan profesional.

2. Penerapan Observer Pattern memudahkan saya untuk menambahan subscriber karena main app hanya perlu menyimpan daftar subscriber
dalam sebuah koleksi dan melakukan iterasi melalui fungsi notify() untuk mengirimkan permintaan HTTP ke setiap URL subscriber yang
relevan secara otomatis. Hal ini memungkinkan kita untuk menambah atau menghapus subscriber secara dinamis melalui endpoint yang
tersedia tanpa harus mengubah logika inti pada aplikasi Publisher. Namun, menjalankan lebih dari satu instansi main app tidak akan
semudah itu karena saat ini daftar subscriber disimpan dalam variabel statis SUBSCRIBERS menggunakan DashMap yang bersifat in-memory.
Akibatnya, setiap instansi Main app akan memiliki daftar subscriber yang berbeda dan terisolasi di memorinya masing-masing, sehingga
seorang pengguna hanya akan menerima notifikasi dari instansi tempat ia mendaftar kecuali jika sistem dikembangkan lebih lanjut
menggunakan basis data atau message broker terpusat untuk sinkronisasi data antar instansi Publisher.

3. Saya telah mencoba mengimplementasikan fitur Tests pada koleksi Postman untuk mengotomatisasi validasi setiap endpoint yang
dikembangkan dalam tutorial ini. Contohnya, ketika saya membuat test untuk memastikan bahwa endpoint /notification/subscribe selalu
memberikan respon status 201 Created dan mengembalikan data JSON yang berisi URL serta nama subscriber yang tepat. Selain itu, saya
juga menambahkan test pada endpoint pembuatan produk untuk memverifikasi kesesuaian atribut produk seperti judul, harga, dan tipe
produk dalam respon yang diterima. Fitur ini sangat berguna bagi program saya karena sistem dapat secara otomatis mendeteksi jika
terjadi kesalahan pada alur kerja pola Observer atau pengelolaan data produk tanpa perlu melakukan pengecekan manual secara
repetitif.