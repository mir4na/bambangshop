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

1. Pada saat ini, menurut saya struct Subscriber sudah cukup karena fungsionalitasnya sederhana (hanya menyimpan data URL dan nama). Namun, jika nantinya ada kebutuhan untuk berbagai jenis subscriber dengan perilaku yang berbeda, maka menggunakan trait akan lebih sesuai dengan prinsip Observer pattern. Trait memungkinkan fleksibilitas dalam implementasi sehingga publisher (BambangShop) tidak perlu tahu detail cara subscriber menerima notifikasi (hanya perlu memanggil method standarnya saja).

2. Penggunaan DashMap lebih efisien daripada Vec karena DashMap menyimpan data dalam bentuk key-value (dalam hal ini, URL sebagai key). Operasi seperti menambah, menghapus, atau mencari subscriber berdasarkan URL bisa dilakukan dalam waktu konstan (O(1)), sedangkan Vec memerlukan iterasi linear (O(n)) untuk operasi yang sama. Lalu, karena URL bersifat unik, struktur map lebih cocok untuk menghindari duplikasi dan memastikan pencarian value dengan cepat. 

3. Menurut saya, DashMap sudah merupakan solusi yang tepat karena menggabungkan HashMap yang thread-safe dengan dukungan concurrency. Meskipun Singleton pattern bisa diimplementasikan secara manual (misalnya dengan Mutex), DashMap lebih efisien karena dirancang untuk multi-threading. Singleton manual mungkin memerlukan pengelolaan locking yang rumit dan berisiko deadlock, sedangkan DashMap menangani sinkronisasi secara internal. Jadi, DashMap lebih sesuai untuk kasus ini karena sekaligus memenuhi pola Singleton (melalui lazy_static).

#### Reflection Publisher-2

1. Pemisahan dilakukan untuk mematuhi prinsip Single Responsibility Principle (SRP). Model sebaiknya hanya mewakili struktur data dan validasi dasar, Repository fokus pada interaksi dengan penyimpanan (seperti database), sedangkan Service menangani logika programnya. Dengan pemisahan ini, kode menjadi lebih modular, mudah dilakukan testing, dan scalable. Jika semua logika dimasukkan ke dalam Model, kelas akan menjadi terlalu besar dan sulit dikelola saat sistem berkembang.

2. Jika hanya mengandalkan Model tanpa Service dan Repository, interaksi antar-objek (seperti Product, Subscriber, dan Notification) akan menjadi rumit karena logika program dan akses data tercampur. Misalnya, Product harus langsung mengelola daftar subscriber dan mengirim notifikasi sehingga kode menjadi tightly coupled dan sulit diubah. Kompleksitas akan meningkat saat menambahkan fitur baru seperti error handling, logging, atau optimasi query karena semua logika harus dimasukkan ke dalam Model.

3. Menurut saya, Postman sangat membantu dalam menguji API secara manual maupun otomatis. Fitur seperti Collections, Environments, dan Automated Testing memudahkan saya untuk memverifikasi fungsionalitas API. Untuk kasus ini, Postman bisa digunakan untuk memastikan endpoint /subscribe dan /unsubscribe bekerja sesuai ekspektasi sebelum diintegrasikan dengan frontend.

#### Reflection Publisher-3

1. Pada kasus ini, kita menggunakan model Push karena publisher (NotificationService) secara aktif mengirimkan data notifikasi ke subscriber melalui metode update() tanpa menunggu subscriber meminta data terlebih dahulu. Hal ini terlihat dari cara NotificationService.notify() membuat payload dan memanggil subscriber.update(payload) secara langsung. Model Push dipilih karena lebih efisien untuk kasus ketika publisher tahu persis data apa yang perlu dikirim dan subscriber tidak perlu mengakses publisher untuk menarik data.

2. Jika menggunakan model Pull, subscriber akan memanggil method (misalnya get_latest_update()) ke publisher untuk mengambil data notifikasi. Keuntungannya adalah subscriber bisa fleksibel memilih data yang dibutuhkan sehingga dapat mengurangi beban publisher jika tidak semua data diperlukan. Namun, kerugiannya adalah kompleksitas meningkat karena subscriber harus menyimpan referensi ke publisher dan latency bisa lebih tinggi karena ada tambahan langkah pull. Untuk kasus ini, model Pull kurang cocok karena notifikasi bersifat real-time dan data yang dikirim sudah spesifik ("CREATED" atau "DELETED").

3. Jika notifikasi tidak menggunakan multi-threading (contoh: menghapus thread::spawn di NotificationService.notify()), setiap pengiriman notifikasi akan berjalan secara blocking dan sequential. Artinya, publisher harus menunggu satu subscriber selesai menerima notifikasi sebelum mengirim ke subscriber berikutnya. Ini akan memperlambat respons API jika ada banyak subscriber atau jaringan lambat. Multi-threading memastikan proses notifikasi berjalan di background tanpa mengganggu alur utama program.