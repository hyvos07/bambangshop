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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
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

> Do we still need an interface (or `trait` in Rust) in this BambangShop case, or a single Model struct is enough?

Untuk sekarang, kita tidak memerlukan interface (atau `trait` di Rust) karena observer yang ada di aplikasi ini hanya berupa satu class, yaitu `Subscriber` dengan data tetap (`url` dan `name`). Penggunaan dari `trait` baru akan diperlukan jika ada banyak tipe observer dengan perilaku berbeda.

> Is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

Untuk menjaga keunikan `id` pada Product dan `url` pada Subscriber, menggunakan `DashMap` akan jauh lebih efisien daripada `Vec`. Seperti halnya `HashMap` di Java, `DashMap` yang menyimpan data dengan bentuk key-value pair menyediakan proses pencarian dengan waktu konstan, mempermudah query data, dan menjamin keunikan serta kinerja yang optimal. Jika kita hanya menggunakan vektor, maka kita akan memerlukan 2 vektor untuk menyimpan key serta related objectnya secara efisien. Walaupun begitu, proses query data akan tetap lebih sulit daripada menggunakan `DashMap`.

> Do we still need DashMap or we can implement Singleton pattern instead?

Pada aplikasi BambangShop ini, kita menggunakan multithreading yang dimana Map Subscriber akan diakses oleh banyak thread. Penggunaan `DashMap` akan sangat berguna pada kasus ini, karena `DashMap` dapat menyediakan sinkronisasi bawaan pada operasi read dan write pada kondisi yang memerlukan multithread seperti sekarang. Jadi, walaupun sudah ada singleton yang menjaga sifat single instance yang ada di aplikasi ini, penggunaan `DashMap` akan tetap diperlukan.

<br>

#### Reflection Publisher-2

> Why we need to separate “Service” and “Repository” from a Model?

Pemisahan Service dan Repository dari Model adalah salah satu bentuk dalam menerapkan prinsip **Single Responsibility Principle**. Pemisahan tanggung jawab (separation of concerns) dapat menghasilkan kode yang modular dan lebih mudah untuk dipelihara, dimana `Repository` akan hanya bertugas untuk akses dan penyimpanan data, sementara `Service` menangani logika bisnis di aplikasi. Pemisahan ini juga memungkinkan masing-masing komponen dapat diuji (lewat unit testing ataupun functional testing nantinya) dan dikembangkan secara terpisah tanpa mengganggu fungsionalitas dari Model utama.

> What happens if we only use the Model? How the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

Jika kita hanya menggunakan Model, Model tersebut akan menerima banyak beban dan tanggung jawab sekaligus, mulai dari penyimpanan data hingga pengolahan logika. Hal ini dapat menyebabkan kode menjadi lebih kompleks, rentan terhadap bug, dan sulit dipelihara ketika terjadi perubahan fungsionalitas di dalam aplikasi. Interaksi antara model (seperti Program, Subscriber, Notification) juga menjadi saling bergantung dan bercampur-campur logika, sehingga menyebabkan coupling yang tinggi dan mengurangi fleksibilitas. Setiap kali terjadi perubahan pada salah satu, nantinya akan berdampak pada bagian yang lain juga, yang akhirnya meningkatkan kompleksitas secara keseluruhan.

> Have you explored more about Postman? Tell us how this tool helps you to test your current work.

Saya sendiri sudah cukup familiar dengan penggunaan Postman sebagai tool yang bisa digunakan untuk mencoba endpoints dari aplikasi web yang saya buat. Dengan Postman, saya bisa mencoba untuk mengirim berbagai jenis request (GET, POST, PUT, DELETE, dll.) dan langsung melihat response dari server. Integrasinya dengan koneksi lokal yang ada di workspace kita juga membantu saya dalam mencoba endpoint-endpoint dari aplikasi web saya pada tahap pengembangan (development). Fitur seperti environment variable, scripting untuk automated testing, dan koleksi request yang bisa saling dibagikan dalam bentuk JSON juga membuat Postman sangat berguna untuk pengerjaan proyek kelompok maupun proyek pribadi milik saya sendiri.

<br>

#### Reflection Publisher-3

> Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

Pada aplikasi BambangShop ini, variasi dari Observer yang digunakan adalah **Push model**, yang mana saat terjadi operasi atau event `create`, `delete`, dan `update` program akan memanggil `NotificationService` yang akan mengiterasi semua subscriber yang ada dan memberikan update terbaru kepada subscriber-subscriber yang ditargetkan untuk mendapatkan notifikasi.


> What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

Jika variasi **Pull model** digunakan pada aplikasi BambangShop, maka data hanya akan dikirim pada saat subscriber membutuhkannya, bukan saat setiap kali ada produk yang ditambah. Tentu perubahan ini akan berdampak pada efisiensi aplikasi BambangShop, dimana kita tidak perlu menambah workload yang dilakukan oleh program pada setiap kali produk baru ditambahkan. Namun, sekarang pihak Subscriber harus mengambil dan menyeleksi data yang benar dan relevan untuknya karena publisher hanya mengirimkan notifikasi tanpa ada klasifikasi khusus untuk siapa dan apa. Hal ini tentu saja akan memindahkan sebagian kompleksitas program pada sisi Subscriber.


> Explain what will happen to the program if we decide to not use multi-threading in the notification process.

Jika aplikasi ini tidak mengimplementasikan multithreading untuk mengirimkan notifikasi ke subscriber, maka semua notifikasi yang dikirim akan dijalankan secara sekuensial pada satu thread. Hal ini akan mengakibatkan antrian yang panjang karena `NotificationService` perlu meng-notify tiap subscriber-nya satu per satu dan menambah beban server jika jumlah data dari subscriber sangat banyak.