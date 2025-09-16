**1. Clone Repository** </br>
Pertama saya membuat folder kerja di dalam direktori Docker </br>
<i>/d/docker/data/part1</i> </br>
<img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo">
 
lalu clone repository React sederhana dari GitHub. </br>
<i>git clone https://github.com/aditya-sridhar/simple-reactjs-app.git react-app </br>
cd react-app</> </i></br>
<img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo">

**2. Install Dependencies** </br>
Setelah repo berhasil di-clone, saya masuk ke folder react-app lalu menginstall semua dependency yang dibutuhkan aplikasi dengan perintah:</br>
<i>npm install</i> </br>
<img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo">

**3. Jalankan Aplikasi Lokal**</br>
Untuk memastikan aplikasi React berjalan dengan baik sebelum di-build ke Docker, saya jalankan aplikasi secara lokal:</br>
<i>npm start</i> </br>
<img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo">

Jika berhasil, aplikasi bisa diakses melalui browser di alamat: 👉 http://localhost:3000</br>
<img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo">

**4. Build React App**</br>
Selanjutnya saya build aplikasi ke mode production agar siap dijalankan di dalam container.</br>
<i>npm run build</i></br>
<img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo">

**5. Dockerfile**</br>
Saya membuat file bernama Dockerfile dengan isi sebagai berikut:</br>

<i># Stage 1: Build </br>
FROM node:22-alpine AS builder </br>
WORKDIR /usr/src/app </br>
COPY package*.json ./ </br>
RUN npm install </br>
COPY . . </br>
RUN npm run build </br>

# Stage 2: Serve </br>
FROM node:22-alpine </br>
RUN npm install -g serve </br>
WORKDIR /usr/src/app </br>
COPY --from=builder /usr/src/app/build ./build </br>
EXPOSE 3000 </br>
CMD ["serve", "-s", "build", "-l", "3000"]</i> </br>

<img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo">

Penjelasan kode Stage 1: </br>
- FROM node:22-alpine AS builder : Pakai image Node.js versi 22 Alpine (ringan, cocok untuk build). </br>
- WORKDIR /usr/src/app : Set working directory di dalam container → semua perintah berikutnya akan dijalankan dari folder ini. </br>
- COPY package*.json ./: Copy file package.json dan package-lock.json dulu, agar npm install bisa jalan tanpa harus copy semua source code dulu → ini mempercepat build (Docker cache). </br>
- RUN npm install: Install semua dependencies aplikasi sesuai package.json. </br>
- COPY . . : Copy semua source code React app ke dalam container.</br>
- RUN npm run build : Jalankan perintah build React app → hasilnya ada di folder build/.</br>

Penjelasan kode Stage 2 : </br>
- FROM node:22-alpine : Buat stage baru, lebih bersih (nggak bawa semua node_modules dari stage 1). Pakai image Node.js ringan lagi.</br>
- RUN npm install -g serve : Install serve secara global → tool untuk serve file React build.</br>
- WORKDIR /usr/src/app : Set working directory lagi di stage 2.</br>
- COPY --from=builder /usr/src/app/build ./build : Ambil hanya folder build dari stage pertama (builder). Jadi image final lebih kecil, karena tidak ada source code & node_modules. </br>
- EXPOSE 3000 : Buka port 3000 agar bisa diakses dari host.</br>
- CMD ["serve", "-s", "build", "-l", "3000"]\ : Command default saat container jalan → jalankan serve, ambil file di build/, listen di port 3000. </br>

Dockerfile ini menggunakan Node.js versi alpine agar lebih ringan, kemudian build React app, dan menjalankannya menggunakan serve di port 3000.</br>

**6. Docker Ignore**
Untuk mengurangi Size Context Docker Build, saya membuat file .dockerignore: </br>
<i>node_modules </br>
build </br>
npm-debug.log</i> </br>
<img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo">

**7. Build Docker Image** </br>
Setelah Dockerfile siap, saya build image dengan nama simple-app versi 1.0: </br>
<i>build -t simple-app:1.0 .</i></br>
<img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo">

**8. Jalankan Docker Container** </br>
Terakhir, saya jalankan container dari image yang sudah dibuat dengan mapping port 3000 ke host: </br>
<i> run -d --name simple-app -p 3000:3000 simple-app:1.0</i></br>
<img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo">

Aplikasi React sekarang sudah berjalan di dalam container Docker dan bisa diakses melalui browser di http://localhost:3000. </br>
<img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo">


**Kesimpulan**
Dengan langkah-langkah di atas, saya berhasil:
- Menjalankan aplikasi React secara lokal
- Membuat Dockerfile untuk build React app
- Build image dengan nama simple-app:1.0
- Menjalankan container sehingga aplikasi React berjalan di Docker


