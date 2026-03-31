# BambangShop Publisher App
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
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. Subscriber memang didefinisikan sebagai interface secara umum. Hal ini dikarenakan biasanya Subscriber dapat berupa berbagai objek yang berbeda jenisnya sehingga dengan penerapan interface, Publisher dapat memberi notifikasi ke berbagai Subscribernya tanpa harus tahu detail implementasinya. Namun menurut saya dalam kasus BambangShop ini, semua Subscriber memiliki struktur data yang sama yaitu url dan name, sehingga penggunaan satu model struct saja sudah cukup. Tapi jika nanti ada berbagai jenis Subscriber dengan struktur data berbeda, maka lebih baik gunakan interface karena secara best practice juga menyarankan kita untuk penggunaan interface.

2. Karena data id dan url harus unik dalam penggunaannya, maka memakai DashMap jauh lebih tepat daripada Vec. Walaupun keduanya sama-sama dapat digunakan untuk menyimpan banyak data, tetapi secara struktur DashMap mencegah adanya duplikasi key sehingga integritas data dapat terjaga. Jadi jika kita insert key yang sama, maka data lama akan dioverwrite dengan data baru sehingga tidak menimbulkan duplikasi. Sedangkan Vec, kita bisa menambahkan data yang sama ke dalamnya sehingga rawan terjadi kesalahan jika kita tidak sengaja atau tidak sadar menambahkan data yang sama. Selain itu, DashMap juga lebih efisien dalam operasi pencariannya dengan waktu konstan (O(1)). Jika kita menggunakan Vec, setiap kali kita ingin melakukan operasi, kita harus iterasi dari awal sampai akhir dimana menjadi tidak efisien.

3. Singleton dan DashMap masing-masing memiliki tujuan yang berbeda. Singleton Pattern memastikan hanya ada satu instance objek yang dibuat di seluruh aplikasi. Penerapan Singleton Patten ini tidak menjamin kita aman untuk membuat thread safe program. Hal ini dikarenakan satu instance tunggal yang diakses oleh banyak thread dalam waktu bersamaan tetap dapat menimbulkan risiko seperti race condition jika mekanisme aksesnya tidak disertai dengan sinkronisasi yang tepat. Dengan alasan inilah, kita tetap mmebutuhkan DashMap karena ia menyediakan interior mutability yang aman dalam concurrency.

#### Reflection Publisher-2
1. Kita tetap harus memisahkan sisi logika bisnis (service) dan data storage (repository) dari model untuk penerapan Single Responsibility Principle (SRP). Model seharusnya hanya berisi representasi data dalam bentuk struct. Repository bertugas untuk urusan data storage dan akses data. Sedangkan service menangani business logic. Dengan adanya pemisahan ini, maka kita membuat kode lebih modular dan lebih mudah untuk ditest.

2. Jika kita tetap hanya menggunakan model untuk semua hal, maka akan menimbulkan masalah 'Fat Model'. Masalah ini terjadi saat model dibebani dengan banyak tanggung jawab sekaligus. Kompleksitas interaksinya akan jauh meningkat dan rumit karena misal jika kita ingin menambah Subscriber, maka kita harus memanggil method dalam model Subscriber dimana rawan juga memicu pemanggilan method lain di model Notification. Selain itu, kode juga menjadi sulit dibaca karena berisi banyak baris kode dan meningkatkan risiko bug saat kita melakukan sedikit modifikasi. Masalah 'tightly coupled' juga timbul karena semuanya saling bergantungan.

3. Menurut saya, Postman sangat membantu kita dalam melakukan pengujian dan validasi berbagai endpoint yang ada. Hal ini untuk memastikan endpoint kita bekerja sesuai dengan fungsinya. Sebenarnya tanpa Postman, kita juga bisa melakukan pengujian manual terhadap endpoint kita tetapi dengan bantuan Postman maka pengujian kita akan lebih cepat. Kita dapat memeriksa dan melihat langsung status pengembaliannya seperti apakah berhasil atau tidak. Salah satu fitur di Postman yang menurut saya menarik dan dapat membantu dalam group project adalah Collections dimana saya bisa mengelompokkan semua endpoint aplikasi dan dapat membagikan ke teman sekelompok juga.

#### Reflection Publisher-3
1. Dalam tutorial ini, sistem ini menggunakan variasi Push Model karena dalam NotificationService, khususnya method notify, kita bisa lihat bahwa Publisher secara aktif mengirimkan (push) data melalui Notification payload ke setiap subscriber melalui method update. Jadi, Subscriber tidak perlu meminta data ke Publisher karena mereka secara otomatis menerima notifikasi yang berisi detail produk baru.

2. Jika kita menerapkan variasi Pull Model dalam kasus ini, maka ada beberapa keuntungan dan kerugian dibandingkan Push Model. Berikut penjelasan detailnya : 
- Keuntungan : Dengan konsep Pull Model, maka kontrol sepenuhnya ada di Subsciber. Mereka bisa memilih untuk mengambil data atau tidak saat itu. Selain itu, mereka juga dapat hanya mengambil data yang dibutuhkan saja. Oleh karena itu, pendekatan ini memberikan fleksibilitas tinggi dari sisi Subscribernya. 
- Kekurangan : Karena dalam konsep Pull Model, terdapat dua kali komunikasi yaitu Publisher kasih tahu ada update dan Subscriber meminta data, maka traffic akan lebih padat. Jika jumlah subscribernya sangat banyak, maka akan sangat membebani server. Selain itu, Subscriber juga harus tahu endpoint untuk mengambil datanya sehingga kompleksitas dalam sisi Subscriber lebih meningkat. Hal ini juga menimbulkan coupling antar sistem.

3. Jika kita tidak menggunakan multi threading dalam proses notifikasi, maka ada beberapa dampak yang cukup penting karena sistem memproses notifikasi secara satu per satu (sekuensial). Beberapa dampaknya : 
- Butuh waktu lama: Tanpa adanya multi threading, program akan menunggu satu tugas selesai baru lanjut ke subscriber berikutnya. Proses sekuensial ini akan memakan waktu lama jika jumlah subscriber banyak.
- User Experience buruk : Setiap user/subscriber harus menunggu lama sampai semua proses notifikasi selesai. Hal ini membuat pengalaman user yang buruk.
- Risiko kegagalan : Jika salah satu proses gagal, maka dapat menghambat pengiriman notifikasi ke subscriber lain yang ada di urutan berikutnya.

Dengan adanya multithread ini, memungkinkan program berjalan secara paralel sehingga tidak ada sekuensial di dalamnya.