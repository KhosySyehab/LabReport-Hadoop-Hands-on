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
