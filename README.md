# SoalShiftSISOP20_modul4_T05
#### Anggota Kelompok:
1. I Gede Pradhana Indra W. (05311840000031)
2. Agung Mulyono            (05311840000035)


# Script Soal Nomor 4
```c
static const char *logpath = "/home/lab/Documents/LAB/fuse.log";

void logFile(char *level, char *cmd, int res, int lenDesc, const char *desc[]) {
  FILE *f = fopen(logpath, "a");
  time_t t;
  struct tm *tmp;
  char timeBuff[100];

  time(&t);
  tmp = localtime(&t);
  strftime(timeBuff, sizeof(timeBuff), "%y%m%d-%H:%M:%S", tmp);

  fprintf(f, "%s::%s::%s::%d", level, timeBuff, cmd, res);
  for (int i = 0; i < lenDesc; i++) {
    fprintf(f, "::%s", desc[i]);
  }
  fprintf(f, "\n");

  fclose(f);
}
FILE *f = fopen(logpath, "a");
time_t t;
  struct tm *tmp;
  char timeBuff[100];
     time(&t);
     tmp = localtime(&t);
     strftime(timeBuff, sizeof(timeBuff), "%y%m%d-%H:%M:%S", tmp);
     fprintf(f, "%s::%s::%s::%d", level, timeBuff, cmd, res);

  for (int i = 0; i < lenDesc; i++) {
     fprintf(f, "::%s", desc[i]);
  }
  fprintf(f, "\n");

  fclose(f);
```
### Penjelasan Script Soal Nomor 4
```c
static const char *logpath = "/home/lab/Documents/LAB/fuse.log";
```

Langkah awal yaitu melakukan pendefinisian path dari log file yang nanti akan dimasukan pada fungsi logFile

```c
void logFile(char *level, char *cmd, int res, int lenDesc, const char *desc[]) {
  FILE *f = fopen(logpath, "a");
  time_t t;
  struct tm *tmp;
  char timeBuff[100];

  time(&t);
  tmp = localtime(&t);
  strftime(timeBuff, sizeof(timeBuff), "%y%m%d-%H:%M:%S", tmp);

  fprintf(f, "%s::%s::%s::%d", level, timeBuff, cmd, res);
  for (int i = 0; i < lenDesc; i++) {
    fprintf(f, "::%s", desc[i]);
  }
  fprintf(f, "\n");

  fclose(f);
}
```
**Fungsi logFile**
Fungsi ini akan membuat log dengan menuliskan pada log file sesuai dengan format yang sudah di tentukan (didefinisikan diawal), Pertama fungsi ini akan mendefinisikan beberapa argumen yang akan menjadi inputnya, yang dimana argumen ini akan terdefinisi pada setiap pendefinisian atribut pada system call yaitu:

 1. **level**  berfungsi untuk mendefinisikan level dari atribut yang berjalan
 2. **cmd**  berfungsi untuk menunjukan nama dari atribut yang berjalan
 3. **res** adalah suatu variabel yang ada di setiap atribut yang berfungsi untuk menyimpan status dari file
 4. **lenDesc** untuk mendifiniskan panjang path dari file yang
 5. **desc[]** untuk menunjuk absolut path dari file yang dituju

```c
FILE *f = fopen(logpath, "a");
```
Pertama fungsi ini akan membuka file sebagai logifile yang didefinisikan pada logpath awal  
```c
time_t t;
  struct tm *tmp;
  char timeBuff[100];
```
Selanjutnya mendefinisikan variabel dan buffer untuk waktu dan struct untuk fungsi `strftime()` yang akan dibahas dibawah

```c
  time(&t);
  tmp = localtime(&t);
  strftime(timeBuff, sizeof(timeBuff), "%y%m%d-%H:%M:%S", tmp);
```
Selanjutnya dengan membuat fungsi untuk menyimpan waktu eksekusi pada variabel `timeBUFF[100]` dengan  menggunakan fungsi `strftime()`, `strftime()`
akan melakukan return sebuah string ke dalam variabel `timeBuff` dengan format yang ada pada argumen 3 sesuai dengan `tmp` pada argumen 4 sebagai  variabel yang menyimpan `localtime()` dari time stamp yang didapat dari fungsi `time()`
```c
 fprintf(f, "%s::%s::%s::%d", level, timeBuff, cmd, res);
  for (int i = 0; i < lenDesc; i++) {
    fprintf(f, "::%s", desc[i]);
  }
  fprintf(f, "\n");

  fclose(f);
```

langkah terakhir adalah dengan membuat perintah untuk melakukan write dengan menggunakan `fprintf()` kedalam *logFile* yang di representasikan dengan variabel `f` yang isinya sesuai dengan format yang sudah ditentukan soal dengan menggunakan `level` yang akan diberikan oleh tiap
atribut, `timeBuff` yang sudah didapat dari process sebelumnya, `cmd` sebagai nama system call yang akan didapat dari tiap atribut dan `res` sebagai file status yang akan di write pada logfile tersebut.

Kemudian penempatan absolut path pada file yang dituju dengan menggunakan loop **for()**  yang dimana akan berjalan sebanyak langkah untuk mencapai nama file tersebut dengan variabel `i` untuk iterasi  dan variable`lenDesc` akan didapat dari tiap atribut sebagai panjang pathnya, dan command dengan `fprintf(f, "\n")`  yang fungsinya agar isi fogfile tersusun dengan terstruktur karena log ini tidak hanya berjalan sekali.
