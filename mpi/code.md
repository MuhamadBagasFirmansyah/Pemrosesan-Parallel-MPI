# MPI-Numeric dan MPI-Bubble-Sort
MPI yang merupakan singkatan dari Message Passing Interface, adalah standar komunikasi yang digunakan dalam pemrograman paralel. Fungsinya adalah memfasilitasi komunikasi di antara proses-paralel yang beroperasi pada sistem komputasi terdistribusi. MPI merupakan salah satu alat yang sering digunakan dalam konteks komputasi paralel, khususnya dalam pengembangan aplikasi ilmiah dan rekayasa yang membutuhkan pemrosesan sejajar.
## Daftar Isi
- [Program yang dibutuhkan](#program-yang-dibutuhkan)
- [Package yang dibutuhkan](#package-yang-dibutuhkan)
- [Konfigurasi SSH](#konfigurasi-ssh)
- [Konfigurasi NFS](#konfigurasi-nfs)
- [Menjalankan MPI Numeric pada Python](#menjalankan-mpi-numeric-pada-python)
- [Menjalankan MPI Bubble Sort pada Python](#menjalankan-mpi-bubble-sort-pada-python)
## Program yang dibutuhkan
1. [Ubuntu 20.04.6 Desktop](https://releases.ubuntu.com/focal/)
   - Ubuntu Master
   - Ubuntu Slave 1
   - Ubuntu Slave 2
   - Ubuntu Slave 3
3. MPI (Master dan Slave)
4. SSH (Master dan Slave)
5. NFS (Master dan Slave)
6. Kode Python
## Package yang dibutuhkan
   - OpenSSH Server :
   ```bash
   sudo apt install openssh-server
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/1c500073-27fd-4006-9238-cb2b21f29c35)

   - NFS Kernel Server :
   ```bash
   sudo apt install nfs-kernel-server
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/5d4ec62f-d8b5-4255-94f5-dd3b50769604)

   - Open MPI :
   ```bash
   sudo apt install openmpi-bin libopenmpi-dev
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/1b22f947-d43d-47ff-8fb9-c6eb5cceb2ab)

   - MPI4PY :
   ```bash
   sudo apt install python3-pip
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/153e9dfc-4223-4334-83c9-433d390684d0)

   ```bash
   pip install mpi4py
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/889c2a03-fd85-4eb0-983a-3df4b5a32b25)

## Konfigurasi SSH
1. Sebelum melakukan konfigurasi SSH, hal yang harus pertama kali dilakukan adalah menentukan 1 buah master dan 3 buah slave yang akan digunakan. Caranya adalah dengan menggunakan perintah `sudo nano /etc/hosts` pada semua master dan slave.
![sudo nano etc hosts](https://github.com/SyahrulWijaya/MPI-Numeric-dan-MPI-Bubble-Sort/blob/2be5d00f7a520aebe0fc313915d2331d95f83f69/sudo%20nano%20etc%20hosts.png)
3. Pada device master lakukan generate ssh key dengan menggunakan perintah
   ```bash
   ssh-keygen -t rsa
   ```
   ![WhatsApp Image 2023-11-15 at 12 37 36_343c0dbc](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/284e33dd-b408-4da0-b2b2-e3b25e6af378)
   
3. Pada device master copy key ssh yang sudah di generate pada direktori `.ssh` sebelumnya kesemua slave yang ada. Jangan Lupa untuk menyesuaikan `<namauser>` dengan nama user yang anda miliki dan `<namaslave>` dengan nama slave yang anda miliki. Lakukan perintah tersebut untuk semua slave yang anda gunakan. 
   ```bash
   cd .ssh
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/e2cd5e83-8b93-4c44-acea-0b5a50bac1c7)

   ```bash
   cat id_rsa.pub | ssh <namauser>@<namaslave> "mkdir .ssh; cat >> .ssh/authorized_keys"
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/0be85b95-8954-40be-99b1-21e793565fe1)


## Konfigurasi NFS
1. Buat shared direktori pada master dan slave. Sesuaikan `<namauser>` dengan nama user anda dan `<namadirektori>` sesuai keinginan anda.
   ```bash
   mkdir /home/<namauser>/<namadirektori>
   ```
   ![WhatsApp Image 2023-11-15 at 12 37 37_8fd4a421](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/a52f779b-9e6e-4fc3-bba2-5c3307bf342c)

2. Pada master lakukanlah konfigurasi pada `sudo nano /etc/exports` dengan menambahkan baris perintah berikut.
   ```bash
   /home/<namauser>/<namadirektori> *(rw,sync,no_root_squash,no_subtree_check)
   ```
   ![WhatsApp Image 2023-11-15 at 12 37 37_2664bad8](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/aec33e0b-42c4-432c-8efa-b339d12c688b)

   ![WhatsApp Image 2023-11-15 at 12 38 43_aebe5bf5](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/71777283-6bdc-46a6-b146-7f87b1093e3b)


4. Restart NFS Server pada master.
   ```bash
   sudo exportfs -a
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/6b560063-30f9-4796-92cd-38e37be66909)

   ```bash
   sudo systemctl restart nfs-kernel-server
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/f91e990b-c2c4-49e2-86c5-38e11a6c30d3)

5. Lakukan mount pada direktori yang telah dibuat sebelumnya, lakukanlah pada semua slave.
   ```bash
   sudo mount master:/home/<namauser>/<namadirektori> /home/<namauser>/<namadirektori>
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/23f47ac3-7aea-403a-894f-ece24890ac38)

## Menjalankan MPI Numeric pada Python
1. Buatlah file python pada direktori yang sudah di mount sebelumnya
   ```bash
   touch /home/<namauser>/<namadirektori>/Mpi_Numeric.py
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/8dabd43d-307b-417b-b495-533a5b8d00da)

2. Isi file python tersebut dengan file py yang saya miliki [disini.](https://github.com/SyahrulWijaya/MPI-Numeric-dan-MPI-Bubble-Sort/blob/5daf3118b4ee42cebcdd8c3d19c11f9733513351/Mpi_Numeric.py)
   ```bash
   cd /home/<namauser>/<namadirektori>
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/c1489bdc-b6eb-4252-9766-e84d01291283)

   ```bash
   nano /Mpi_Numeric.py
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/55f9f6e9-2817-4ce2-937a-b1497d917177)

3. Jalankan file python tersebut pada master
   ```bash
   mpirun -np 4 -host master,slave1,slave2,slave3 python3 Mpi_Numeric.py
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/2906ebc6-dfbf-4def-b964-99411eaacb79)

![mpirun -np 4 -host master,slave1,slave2,slave3 python3.jpg](https://github.com/SyahrulWijaya/MPI-Numeric-dan-MPI-Bubble-Sort/blob/5030266e7d2562d04577676db6b7bd1935690642/mpirun%20-np%204%20-host%20master%2Cslave1%2Cslave2%2Cslave3%20python3.jpg)
## Menjalankan MPI Bubble Sort pada Python
1. Buatlah file python pada direktori yang sudah di mount sebelumnya
   ```bash
   touch /home/<namauser>/<namadirektori>/Mpi_Bubble_Sort.py
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/0349e817-77ca-4073-8d86-729000f865dc)

2. Isi file python tersebut dengan file py yang saya miliki [disini.](https://github.com/SyahrulWijaya/MPI-Numeric-dan-MPI-Bubble-Sort/blob/f2a964ac22721e1eda8a46160232c01e8fdf0585/Mpi_Bubble_Sort.py)
   ```bash
   cd /home/<namauser>/<namadirektori>
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/04ffbf29-a70c-4de3-8a13-536fae02c14e)

3. Jalankan file python tersebut pada master
   ```bash
   mpirun -np 4 -host master,slave1,slave2,slave3 python3 Mpi_Bubble_Sort.py
   ```
   ![image](https://github.com/MuhamadBagasFirmansyah/Pemrosesan-Parallel-MPI/assets/151048513/7a24cee2-153e-4598-b862-49d1a4f862ef)

![mpirun -np 4 -host master,slave1,slave2,slave3 python3 versi 2.jpg](https://github.com/SyahrulWijaya/MPI-Numeric-dan-MPI-Bubble-Sort/blob/caa37b9bb1fb679741f5385ceee03f14913c47fb/mpirun%20-np%204%20-host%20master%2Cslave1%2Cslave2%2Cslave3%20python3%20versi%202.jpg)
