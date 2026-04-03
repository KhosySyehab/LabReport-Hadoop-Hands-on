# LabReport-Hadoop-Hands-on

## Laporan Praktikum - Lab 1: HDFS CLI Operations
### Setup Awal & Menjalankan Cluster
Command & Output:
```
khosy@DESKTOP-EOGBIUA:~/playground/bigdata$ docker compose up -d
[+] up 5/5
 ✔ Network bigdata_default          Created                                                                                                                                                                0.1s
 ✔ Container hadoop-namenode        Created                                                                                                                                                                0.2s
 ✔ Container hadoop-datanode        Created                                                                                                                                                                0.2s
 ✔ Container hadoop-resourcemanager Created                                                                                                                                                                0.1s
 ✔ Container hadoop-nodemanager     Created                                                                                                                                                                0.1s

khosy@DESKTOP-EOGBIUA:~/playground/bigdata$ docker exec -it hadoop-namenode bash
bash-4.2$ hdfs dfs -ls /
```
<img width="786" height="198" alt="image" src="https://github.com/user-attachments/assets/82614435-773a-492d-8493-de2498b61e80" />                      

### Task 1.1: Membuat Direktori di HDFS                                                        
Command & Output:        
```
bash-4.2$ hdfs dfs -mkdir -p /user/mahasiswa/kelompok1
bash-4.2$ hdfs dfs -mkdir -p /user/mahasiswa/kelompok1/input
bash-4.2$ hdfs dfs -mkdir -p /user/mahasiswa/kelompok1/output
bash-4.2$ hdfs dfs -ls /user/mahasiswa/kelompok1
Found 2 items
drwxr-xr-x   - hadoop supergroup          0 2026-04-03 07:01 /user/mahasiswa/kelompok1/input
drwxr-xr-x   - hadoop supergroup          0 2026-04-03 07:02 /user/mahasiswa/kelompok1/output
```
<img width="912" height="157" alt="image" src="https://github.com/user-attachments/assets/2b94c217-c7d8-4401-86c5-077a1f023bf7" />

📝 Jawaban Pertanyaan Task 1.1:                                                                        
- Apa output dari perintah -ls?
Output dari perintah -ls menampilkan daftar isi dari direktori /user/mahasiswa/kelompok1, yang ternyata berisi dua item berupa directory (d pada drwxr-xr-x), yaitu folder input dan output. Output tersebut juga menampilkan detail meta-data seperti hak akses (permission), pemilik (user hadoop), grup (supergroup), ukuran file (0), tanggal dan waktu pembuatan, serta path lengkap dari masing-masing direktori.

### Task 1.2 & 1.3: Upload dan Membaca File dari HDFS
Command & Output:
```
bash-4.2$ echo "big data is big data processing big data analytics" > sample.txt
bash-4.2$ echo "hadoop hdfs yarn mapreduce spark" >> sample.txt
bash-4.2$ echo "distributed computing cluster node" >> sample.txt
bash-4.2$ hdfs dfs -put sample.txt /user/mahasiswa/kelompok1/input/
bash-4.2$ hdfs dfs -ls /user/mahasiswa/kelompok1/input/
Found 1 items
-rw-r--r--   1 hadoop supergroup        119 2026-04-03 07:02 /user/mahasiswa/kelompok1/input/sample.txt

bash-4.2$ hdfs dfs -cat /user/mahasiswa/kelompok1/input/sample.txt
big data is big data processing big data analytics
hadoop hdfs yarn mapreduce spark
distributed computing cluster node

bash-4.2$ hdfs dfs -head /user/mahasiswa/kelompok1/input/sample.txt
big data is big data processing big data analytics
hadoop hdfs yarn mapreduce spark
distributed computing cluster node

bash-4.2$ hdfs dfs -get /user/mahasiswa/kelompok1/input/sample.txt downloaded.txt
bash-4.2$ cat downloaded.txt
big data is big data processing big data analytics
hadoop hdfs yarn mapreduce spark
distributed computing cluster node
```
<img width="1041" height="442" alt="image" src="https://github.com/user-attachments/assets/d9ab6d30-197e-4d57-8aba-a643952ba157" />

### Task 1.4: Cek Info File & Storage
Command & Output:
```
bash-4.2$ hdfs dfs -du -h /user/mahasiswa/kelompok1/input/
119  119  /user/mahasiswa/kelompok1/input/sample.txt

bash-4.2$ hdfs dfsadmin -report
Configured Capacity: 1081101176832 (1006.85 GB)
Present Capacity: 1020113498242 (950.05 GB)
DFS Remaining: 1020113473536 (950.05 GB)
DFS Used: 24706 (24.13 KB)
...
Live datanodes (1):
Name: 172.18.0.3:9866 (hadoop-datanode.bigdata_default)
...

bash-4.2$ hdfs dfs -stat "%r" /user/mahasiswa/kelompok1/input/sample.txt
1
```
``(Catatan: Output dfsadmin -report dipotong sebagian agar lebih rapi, detail lengkap ada pada log)``
<img width="752" height="783" alt="image" src="https://github.com/user-attachments/assets/991fa6f8-7580-412e-b295-9cd1e9df8936" />
<img width="608" height="41" alt="image" src="https://github.com/user-attachments/assets/e5ff24c4-2fa1-45e2-8ce6-18b238d65a04" />

📝 Jawaban Pertanyaan Task 1.4:
- Berapa replication factor file sample.txt?
Jawab: 1 (Dilihat dari output hdfs dfs -stat "%r").
- Berapa total ruang disk yang digunakan HDFS?
Jawab: 24.13 KB atau 24706 bytes (Dilihat dari atribut DFS Used pada report).
- Berapa jumlah DataNode yang aktif?
Jawab: 1 node (Dilihat dari atribut Live datanodes (1) pada report).

### Task 1.4: Cek Info File & Storage
Command & Output:
```
bash-4.2$ hdfs dfs -cp /user/mahasiswa/kelompok1/input/sample.txt /user/mahasiswa/kelompok1/input/sample_copy.txt
bash-4.2$ hdfs dfs -mv /user/mahasiswa/kelompok1/input/sample_copy.txt /user/mahasiswa/kelompok1/input/backup.txt 
bash-4.2$ hdfs dfs -rm /user/mahasiswa/kelompok1/input/backup.txt
Deleted /user/mahasiswa/kelompok1/input/backup.txt
bash-4.2$ hdfs dfs -stat "%r" /user/mahasiswa/kelompok1/input/sample.txt
1
```


<img width="948" height="114" alt="image" src="https://github.com/user-attachments/assets/3941b613-36fc-4669-96ed-d1c3beb88b62" />

- 📝 Jawaban Pertanyaan Task 1.5:
Mengapa replication factor di cluster kita adalah 1, bukan 3 seperti default? Apa implikasinya terhadap fault tolerance?
Jawab: Replication factor adalah 1 karena pada saat inisialisasi cluster, kita telah mendefinisikan variabel HDFS-SITE.XML_dfs.replication=1 di dalam file hadoop.env. Selain itu, melalui arsitektur Docker Compose, kita memang hanya menjalankan 1 container DataNode.
Implikasinya terhadap fault tolerance (toleransi kesalahan) adalah sistem tidak memiliki perlindungan sama sekali (0 fault tolerance). Jika satu-satunya DataNode tersebut rusak, mati, atau datanya corrupt, maka data akan hilang permanen karena HDFS tidak memiliki salinan (replika) data di node lain untuk memulihkannya.
---

## Laporan Praktikum - Lab 2: Explore Hadoop Web UI
### Task 2.1: NameNode Web UI
Akses Web: ``http://localhost:9870``                         
Hasil Eksplorasi Overview & Datanodes:
1. Overview:
- Versi Hadoop: Hadoop 3.3.6
- Jumlah DataNode Aktif (Live Nodes): 1
- Total Kapasitas HDFS:	1006.85 GB
- Kapasitas Terpakai (DFS Used): 44 KB (0%)
<img width="1919" height="904" alt="image" src="https://github.com/user-attachments/assets/137e61f5-8f00-4007-9de2-1b49956ca86d" />

2. Datanodes:
- Status DataNode: In Service / Normal
- Kapasitas masing-masing DataNode: 1006.85 GB
<img width="1836" height="447" alt="image" src="https://github.com/user-attachments/assets/62274bd3-b9e0-4d0f-8e38-a7f9c957d9e6" />

3. Utilities → Browse the file system: (Navigasi ke /user/mahasiswa/kelompok1/input/sample.txt)
- Block ID: 1073741825
- Replication: 1
<img width="1919" height="917" alt="image" src="https://github.com/user-attachments/assets/47ea425a-0964-491f-b645-9ef82b3e09aa" />
<img width="956" height="792" alt="image" src="https://github.com/user-attachments/assets/e55207b5-c9c9-41f4-b220-b4d3813c0320" />

### Task 2.2: YARN ResourceManager Web UI
Akses Web: ``http://localhost:8088``                                                  
Hasil Eksplorasi ResourceManager:
1. Cluster Metrics:
- Jumlah NodeManager Aktif (Active Nodes): 1
- Total Memory: 2 GB atau 2048 MB
- Total vCores: 8
<img width="1542" height="119" alt="image" src="https://github.com/user-attachments/assets/9d6bab2a-aea1-467a-82c2-95854db21718" />
<img width="1919" height="924" alt="image" src="https://github.com/user-attachments/assets/b8d5e092-e5cd-4490-b355-660e77adff30" />

2. Scheduler (Tab Scheduler):
- Jenis Scheduler: Capacity Scheduler (Ini adalah tipe scheduler default pada Hadoop 3 YARN)
- Jumlah Queue: 1 Queue utama (yaitu queue default)
<img width="1919" height="925" alt="image" src="https://github.com/user-attachments/assets/0039f33a-8695-4954-a243-f665eb7222b8" />

---

## Laporan Praktikum - Lab 3: Jalankan MapReduce WordCount
### Task 3.1 & 3.2: Download Dataset & Upload ke HDFS
Command & Output:
```
bash-4.2$ curl -L -o shakespeare.txt https://www.gutenberg.org/cache/epub/100/pg100.txt
bash-4.2$ hdfs dfs -put shakespeare.txt /user/mahasiswa/kelompok1/input/
bash-4.2$ hdfs fsck /user/mahasiswa/kelompok1/input/shakespeare.txt -blocks
...
Total blocks (validated):  1 (avg. block size 5638483 B)
```
<img width="917" height="632" alt="image" src="https://github.com/user-attachments/assets/6e2b4adc-0c55-4b71-99a9-f3d83d09c400" />
<img width="1333" height="773" alt="image" src="https://github.com/user-attachments/assets/7b20eb1d-c9ab-4701-91e8-d6fcaf83c47e" />

- Jawaban Pertanyaan Task 3.2:
Berapa block yang terbentuk dari file shakespeare.txt? Mengapa hanya sejumlah itu?
Jawab: Hanya terbentuk 1 block. Hal ini terjadi karena ukuran file shakespeare.txt yang diunduh hanya sekitar ~5.5 MB. Secara default, ukuran satu block di HDFS adalah 128 MB. Karena 5.5 MB jauh lebih kecil dari batas maksimal 128 MB, maka seluruh isi file tersebut muat dan disimpan di dalam satu block tunggal.


## Task 3.3, 3.4, & 3.5: WordCount MapReduce Job & Monitor YARN
Command & Output:
```
bash-4.2$ hadoop jar /opt/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar wordcount \
   -D mapreduce.map.memory.mb=512 \
   -D mapreduce.reduce.memory.mb=512 \
   -D yarn.app.mapreduce.am.resource.mb=512 \
   /user/mahasiswa/kelompok1/input/shakespeare.txt \
   /user/mahasiswa/kelompok1/output/wordcount

...
2026-04-03 08:15:35 INFO  Job:1766 - Job job_1775199682471_0002 completed successfully
2026-04-03 08:15:35 INFO  Job:1773 - Counters: 54
        Job Counters 
                Launched map tasks=1
                Launched reduce tasks=1
...
bash-4.2$ hdfs dfs -ls /user/mahasiswa/kelompok1/output/wordcount/
Found 2 items
-rw-r--r--   1 hadoop supergroup          0 2026-04-03 08:15 /user/mahasiswa/kelompok1/output/wordcount/_SUCCESS
-rw-r--r--   1 hadoop supergroup     788789 2026-04-03 08:15 /user/mahasiswa/kelompok1/output/wordcount/part-r-00000

bash-4.2$ hdfs dfs -cat /user/mahasiswa/kelompok1/output/wordcount/part-r-00000 | sort -t$'\t' -k2 -nr | head -5
the     25689
I       20717
and     19850
of      17111
to      17075
```
<img width="1542" height="329" alt="image" src="https://github.com/user-attachments/assets/4ece544a-63bd-4cf2-a31f-d8a6076fee54" />
<img width="1919" height="924" alt="image" src="https://github.com/user-attachments/assets/c056fbf5-4088-418b-a8f0-b60beaffc497" />

<img width="1317" height="613" alt="image" src="https://github.com/user-attachments/assets/91de35f4-967e-4243-8592-711859ab10c2" />
<img width="1307" height="739" alt="image" src="https://github.com/user-attachments/assets/487fd9e1-3d36-48d4-b47a-a9a162972be6" />
<img width="1790" height="351" alt="image" src="https://github.com/user-attachments/assets/77cd60a3-f375-41a9-9807-9613d9990bd9" />

<img width="1059" height="649" alt="image" src="https://github.com/user-attachments/assets/f03097fd-3c43-4490-a0e0-401c91d25a7f" />
<img width="935" height="416" alt="image" src="https://github.com/user-attachments/assets/860b9616-0846-4e3a-9b57-0e37940d113f" />

- 📝 Jawaban Pertanyaan Task 3.5:
1. Sebutkan 5 kata yang paling sering muncul beserta frekuensinya!
- Jawab: the (25689), I (20717), and (19850), of (17111), to (17075).
2. Berapa jumlah Map tasks yang dijalankan? Mengapa sejumlah itu?
- Jawab: 1 Map task. Jumlah Map task ditentukan oleh jumlah Input Splits. Karena file input (shakespeare.txt) hanya memakan 1 block memori di HDFS, maka Hadoop hanya menugaskan 1 Map task untuk memproses 1 block tersebut.
3. Berapa jumlah Reduce tasks? Mengapa?
- Jawab: 1 Reduce task. Secara default, konfigurasi framework MapReduce di Hadoop memang menggunakan 1 reducer per eksekusi, kecuali jika kita mendefinisikan sebaliknya secara manual melalui parameter spesifik.
4. Sebutkan file output yang dihasilkan. Apa fungsi file _SUCCESS?
- Jawab: File yang dihasilkan adalah _SUCCESS dan part-r-00000. File _SUCCESS adalah penanda (flag) kosong berukuran 0 byte yang dibuat oleh sistem Hadoop untuk menandakan bahwa job MapReduce telah selesai dieksekusi dengan sukses tanpa masalah di direktori tujuan tersebut.
5. Berapa lama waktu yang dibutuhkan job untuk selesai (elapsed time)?
- Jawab: Berdasarkan log terminal, job dimulai pada 08:15:09 dan berhasil diselesaikan pada 08:15:35, sehingga memakan waktu kurang lebih 26 detik. (Atau sesuaikan dengan angka yang tertera di YARN Web UI-mu).

### Task 3.6: Tantangan Bonus — Bandingkan File Kecil vs. Besar
Command & Output:
```
bash-4.2$ hadoop jar /opt/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar wordcount \
   -D mapreduce.map.memory.mb=512 \
   -D mapreduce.reduce.memory.mb=512 \
   -D yarn.app.mapreduce.am.resource.mb=512 \
   /user/mahasiswa/kelompok1/input/ \
   /user/mahasiswa/kelompok1/output/wordcount_all

...
2026-04-03 08:19:32 INFO  Job:1766 - Job job_1775199682471_0003 completed successfully
        Job Counters 
                Launched map tasks=2
                Launched reduce tasks=1
...
```
<img width="1347" height="739" alt="image" src="https://github.com/user-attachments/assets/6002e3da-5933-41e2-a96b-f8e83a4fc180" />
<img width="969" height="383" alt="image" src="https://github.com/user-attachments/assets/90e511a1-cd21-4ea5-b0aa-f899a5e1efc8" />

📝 Jawaban Pertanyaan Bonus Task 3.6:

1. Apa perbedaan jumlah Map tasks jika input berupa satu file vs. satu direktori?
- Jawab: Jumlah Map tasks bertambah menjadi 2. Saat input diarahkan ke direktori penuh, Hadoop membaca semua file di dalamnya (shakespeare.txt dan sample.txt). Setiap file memicu minimal 1 input split yang mewakili block-nya masing-masing, sehingga Hadoop meluncurkan 2 Map tasks yang berjalan secara paralel untuk tiap file.
2. Apakah kata-kata yang paling sering muncul berubah? Mengapa?
- Jawab: Susunan urutan 20 kata paling teratas secara umum tidak banyak berubah karena file sample.txt hanya memiliki sedikit kata dibandingkan puluhan ribu kata di shakespeare.txt. Namun, frekuensi angkanya berubah. Sebagai contoh, kata "is" pada single file berjumlah 8361, sedangkan pada direktori menjadi 8362 (bertambah 1). Hal ini terjadi karena proses Reduce sekarang menggabungkan frekuensi kata dari semua file (kalimat "big data is big data processing..." dari file pertama ikut terhitung dan ditambahkan).
---

## Refleksi Praktikum
Melalui hands-on ini, saya paling memahami alur kerja perintah dasar HDFS CLI dan bagaimana framework MapReduce secara otomatis membagi pekerjaan (Map dan Reduce tasks) berdasarkan ukuran block serta jumlah file input. Namun, saya masih sedikit bingung mengenai mekanisme rinci bagaimana YARN mengatur alokasi memori secara dinamis jika parameternya tidak kita tentukan secara eksplisit. Selama proses lab, saya menemui beberapa kendala teknis, seperti eksekusi WordCount yang stuck di 5% karena keterbatasan alokasi memori RAM cluster, serta memunculkan error FileAlreadyExistsException saat dijalankan ulang. Kendala-kendala tersebut berhasil saya atasi dengan melakukan yarn application -kill, menambahkan parameter -D pada perintah untuk membatasi pemakaian RAM per task, dan memastikan untuk selalu menghapus direktori output menggunakan hdfs dfs -rm -r sebelum me-run sebuah job baru.
