# gRPC Polyglot Lab

Praktikum komunikasi inter-service menggunakan gRPC dengan satu server Go
dan dua client berbeda bahasa (Python dan Node.js), berbagi satu kontrak
`user.proto`.

## Struktur Proyek

- `user.proto` : kontrak API (single source of truth)
- `go-server/` : gRPC server (Go)
- `python-client/` : gRPC client (Python)
- `node-client/` : gRPC client (Node.js)

## Analisis Pendekatan Kompilasi Protobuf

Pada praktikum ini, berkas `user.proto` diproses dengan dua pendekatan yang
berbeda. Pada pendekatan **static code generation** (Go dan Python), compiler
`protoc` dijalankan lebih dahulu pada saat build untuk menghasilkan berkas
kode sumber nyata (`user.pb.go`, `user_grpc.pb.go`, `user_pb2.py`, dan
`user_pb2_grpc.py`) yang menjadi bagian dari proyek. Kode hasil generate ini
mendefinisikan struct atau kelas pesan, stub client, dan interface server
secara eksplisit, sehingga kesalahan tipe data dapat terdeteksi lebih awal
(terutama pada Go yang dikompilasi), didukung autocomplete IDE, dan memiliki
kinerja serialisasi yang baik. Kekurangannya, setiap perubahan pada `.proto`
mengharuskan kita menjalankan ulang `protoc` agar kode hasil generate tetap
sinkron, seperti yang dilakukan saat menambahkan field `phone`. Sebaliknya,
pada pendekatan **dynamic runtime generation** (Node.js), pustaka
`@grpc/proto-loader` membaca dan mem-parsing berkas `.proto` langsung ketika
program berjalan (runtime) dan membentuk definisi service secara dinamis di
memori, tanpa langkah kompilasi dan tanpa berkas kode hasil generate.
Pendekatan ini lebih praktis dan fleksibel karena perubahan skema langsung
terbaca saat client dijalankan ulang, namun tidak ada pemeriksaan tipe saat
kompilasi, terdapat sedikit overhead parsing saat startup, dan berkas `.proto`
harus tersedia di lokasi runtime.