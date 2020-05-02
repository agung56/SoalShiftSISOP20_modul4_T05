# SoalShiftSISOP20_modul4_T05
#### Anggota Kelompok:
1. I Gede Pradhana Indra W. (05311840000031)
2. Agung Mulyono            (05311840000035)

### Soal Nomor 1
Source Code : [ssfs.c](https://github.com/agung56/SoalShiftSISOP20_modul4_T05/blob/master/ssfs.c)

**Pembahasan**  
**Fungsi changePath**
```c
void changePath(char *fpath, const char *path, int isWriteOper, int isFileAsked) {
  char *ptr = strstr(path, "/encv1_");
  int state = 0;
  if (ptr != NULL) {
    if (strstr(ptr+1, "/") != NULL) state = 1;
  }
  char fixPath[1000];
  memset(fixPath, 0, sizeof(fixPath));
  if (ptr != NULL && state) {
    ptr = strstr(ptr+1, "/");
    char pathEncvDirBuff[1000];
    char pathEncryptedBuff[1000];
    strcpy(pathEncryptedBuff, ptr);
    strncpy(pathEncvDirBuff, path, ptr-path);
    if (isWriteOper) {
      char pathFileBuff[1000];
      char pathDirBuff[1000];
      getDirAndFile(pathDirBuff, pathFileBuff, pathEncryptedBuff);
      decrypt(pathDirBuff, 0);
      sprintf(fixPath, "%s%s/%s", pathEncvDirBuff, pathDirBuff, pathFileBuff);
    } else if (isFileAsked) {
      char pathFileBuff[1000];
      char pathDirBuff[1000];
      char pathExtBuff[1000];
      getDirAndFile(pathDirBuff, pathFileBuff, pathEncryptedBuff);
      char *whereIsExtension = strrchr(pathFileBuff, '.');
      if (whereIsExtension-pathFileBuff <= 1) {
        decrypt(pathDirBuff, 0);
        decrypt(pathFileBuff, 0);
        sprintf(fixPath, "%s%s/%s", pathEncvDirBuff, pathDirBuff, pathFileBuff);
      } else {
        char pathJustFileBuff[1000];
        memset(pathJustFileBuff, 0, sizeof(pathJustFileBuff));
        strcpy(pathExtBuff, whereIsExtension);
        snprintf(pathJustFileBuff, whereIsExtension-pathFileBuff+1, "%s", pathFileBuff);
        decrypt(pathDirBuff, 0);
        decrypt(pathJustFileBuff, 0);
        sprintf(fixPath, "%s%s/%s%s", pathEncvDirBuff, pathDirBuff, pathJustFileBuff, pathExtBuff);
      }
    } else {
      decrypt(pathEncryptedBuff, 0);
      sprintf(fixPath, "%s%s", pathEncvDirBuff, pathEncryptedBuff);
    }
  } else {
    strcpy(fixPath, path);
  }
  if (strcmp(path, "/") == 0) {
    sprintf(fpath, "%s", dirpath);
  } else {
    sprintf(fpath, "%s%s", dirpath, fixPath);
  }
}
```
```c
void changePath(char *fpath, const char *path, int isWriteOper, int isFileAsked) {
  char *ptr = strstr(path, "/encv1_");
  int state = 0;
  if (ptr != NULL) {
    if (strstr(ptr+1, "/") != NULL) state = 1;
```
Terdapat 4 argumen input pada fugsi diatas, yaitu yang pertama adalah fpath yang nanti akan didefinisikan berbeda pada beberapa system call, yang kedua merupakan path temoat dibuatnya file atau direktori, dan argumen 
ketiga dan keempat adalah untuk menentukan kondisi system call yang dijalanakan

Terdapat dua kondisi `state`, dengan default *0*, state ini berfungsi untuk path yang hanya memiliki satu directory setelah `encv_1` 

```c
}
  char fixPath[1000];
  memset(fixPath, 0, sizeof(fixPath));
  if (ptr != NULL && state) {
    ptr = strstr(ptr+1, "/");
    char pathEncvDirBuff[1000];
    char pathEncryptedBuff[1000];
    strcpy(pathEncryptedBuff, ptr);
    strncpy(pathEncvDirBuff, path, ptr-path);
```
`fixpath[]` digunakan untuk menyimpan proses path dari tiap kondisi yang berbeda

```c
if (isWriteOper) {
      char pathFileBuff[1000];
      char pathDirBuff[1000];
      getDirAndFile(pathDirBuff, pathFileBuff, pathEncryptedBuff);
      decrypt(pathDirBuff, 0);
      sprintf(fixPath, "%s%s/%s", pathEncvDirBuff, pathDirBuff, pathFileBuff);
```
Kemudian fungsi diatas dilakukan untuk menjalankan fungsi `getDirAndFile()` yang akan mengembalikan nilai (me-return) direktori dan file berdasarkan dari path yang ada di 
`pathEncryptedBuff` dan akan men `decrypt()`nya yang selanjutnya akan di store di buffer `fixpath[]` dengan format 
`(encv1_,pathdir,pathfile)`

```c
} else if (isFileAsked) {
      char pathFileBuff[1000];
      char pathDirBuff[1000];
      char pathExtBuff[1000];
      getDirAndFile(pathDirBuff, pathFileBuff, pathEncryptedBuff);
      char *whereIsExtension = strrchr(pathFileBuff, '.');
      if (whereIsExtension-pathFileBuff <= 1) {
        decrypt(pathDirBuff, 0);
        decrypt(pathFileBuff, 0);
        sprintf(fixPath, "%s%s/%s", pathEncvDirBuff, pathDirBuff, pathFileBuff);
      } else {
        char pathJustFileBuff[1000];
        memset(pathJustFileBuff, 0, sizeof(pathJustFileBuff));
        strcpy(pathExtBuff, whereIsExtension);
        snprintf(pathJustFileBuff, whereIsExtension-pathFileBuff+1, "%s", pathFileBuff);
        decrypt(pathDirBuff, 0);
        decrypt(pathJustFileBuff, 0);
        sprintf(fixPath, "%s%s/%s%s", pathEncvDirBuff, pathDirBuff, pathJustFileBuff, pathExtBuff);
      }
```
`getDirAndFile(pathDirBuff, pathFileBuff, pathEncryptedBuff)`. Untuk mencari *extention* dari path yang ditentukan, akan di lakukan 
pengecekan karakter terlebih dulu menggunakan `if (whereIsExtension-pathFileBuff <= 1)`

sebelumnya fungsi `strrchr()` sudah digunakan untuk me-return pointer yang menunjuk ke semua karakter setelah ditemukannya karakter "."
sehingga selanjutnya dapat langsung dilakukan dekripsi untuk string yang hanya memiliki satu atau nol karakter "." dan di store ke buffer 
`fixpath[]` 
untuk string yang memiliki lebih dari satu karakter "." akan dilakukan `snprintf()` sebanyak ` whereIsExtension-pathFileBuff+1` dan 
akan di store ke dalam buffer `pathJustFileBuff[]` yang akan berisi extenxion saja dan seperti sebelumnya, akan dimasukan ke fix path
dengan format `(encv1_, dir, file & extension)`

```c
    else {
      decrypt(pathEncryptedBuff, 0);
      sprintf(fixPath, "%s%s", pathEncvDirBuff, pathEncryptedBuff);
    }
  } else {
    strcpy(fixPath, path);
  }
  if (strcmp(path, "/") == 0) {
    sprintf(fpath, "%s", dirpath);
  } else {
    sprintf(fpath, "%s%s", dirpath, fixPath);
  }
}
```
`else` pertama disini adalah untuk kondisi **isWriteOper** dan **isFileAsked** == 0, sehingga isi dalam buffer 
`pathEncryptedBuff` akan langsung di dekrip dan di store kedalam `fixpath` dengan format `(encv1_, decrypted path)`

`else` kedua adalah untuk kondisi `state` == 0 atau hanya ada **satu** directory ataupun file pada path setelah `encv1_`, sehingga akan 
langsung di store kedalam `fixpath`

selanjutnya adalah pengisian `fpath` dengan parameter "/" yang ada di path. `fixpath` yang sudah didapatkan berdasarkan kondisi yang 
diberikan oleh system call akan dimasukan kedalam `fpath` **jika "/" pada path > 1** disini mengindikasikan ada nya file/directory yang 
dituju setelah `encv1_`. Sebaliknya makan `fpath` hanya akan di isi dengan `dirpath` yang sudah di set di awal 
`(*dirpath = "/home/lab/Documents")`

**Fungsi getDirAndfile**
``` c 
void getDirAndFile(char *dir, char *file, char *path) {   
  char buff[1000];
  memset(dir, 0, 1000);
  memset(file, 0, 1000);
  strcpy(buff, path);
  char *token = strtok(buff, "/");
  while(token != NULL) {
    sprintf(file, "%s", token);
    token = strtok(NULL, "/");
    if (token != NULL) {
      sprintf(dir, "%s/%s", dir, file);
    }
  }
}
```
Fungsi ini akan membagi path menjadi beberapa token berdasarkan "/" menggunakan fungsi `strtok()`, `while()` pertama disini, berfungsi 
untuk mengambil memasukan setiap token sampai terakhir ke buffer **file** yang sudah di definisikan terlebih dahulu dengan memory 
tertentu menggunakan `memset(file, 0, 1000)` 

`if()` akan berfungsi untuk memasukkan `dir` yang diberikan oleh system call kedalam kedalam buffer **dir** yang sudah di definisikan 
terlebih dahulu dengan memory tertentu menggunakan `memset(dir, 0, 1000)` yang akan di tambahkan buffer **file** dibelakangnya yang
menjadikannya sebagai absolut path

**Fungsi Decrypt**
```c
void decrypt(char *path, int isEncrypt) {  
  char *cursor = path;
  while (cursor-path < strlen(path)) {
    char *ptr = strchr(key, *cursor);
    if (ptr != NULL) {
      int index = (ptr-key+strlen(key)-10)%strlen(key);
      if (isEncrypt) index = (ptr-key+strlen(key)+10)%strlen(key);
      *cursor = *(key+index);
    }
    cursor++;
  }
}
```
Pada fungsi diatas, terdapat `isEncrypt` pada argumen kedua yang akan merubah fungsi ini menjadi enkripsi dari path yang dituju jika 
`isEncyrpt` memiliki nilai atau **(path, 1)** 

`while()` disini akan berjalan untuk setiap karakter yang ada di `path` selama panjang dair kursor < panjang path dan melakukan enkripsi/
dekripsi sesuai kondisi yang berjalan, fungsi ini akan men dekrip dengan `int index = (ptr-key+strlen(key)-10)%strlen(key)` dan 
**cursor** akan di increment

Jika kondisi yang berjalan **(path, 1)** maka `if()`kedua akan berjalan dimana akan melakukan enkripsi dengan
`index = (ptr-key+strlen(key)+10)%strlen(key)` dan cursor akan diganti dengan hasil penjumlahan `key` dan `index`
  

**_mkdir** 
```c
static int _mkdir(const char *path, mode_t mode)
{
	char fpath[1000];
	changePath(fpath, path, 1, 0);

  char *ptr = strrchr(path, '/'); // strrchr intinya ngambil / terakhir sampe paling belakang
  char *filePtr = strstr(ptr, "/encv1_");  //nah baru dah, dari "encv1_" kebelakang di tunjuk *fileptr
  if (filePtr != NULL) {    //kalo ada encv1_ if ini jalan artinya user make encv1 masuk ke log
    if (filePtr - ptr == 0) {
      const char *desc[] = {path};
      logFile("SPECIAL", "ENCV1", 0, 1, desc);
    }
  }
```
System call ini berfungsi untuk menghandle pembuatan direktori, pertama tama system call akan melakukan pengecekan jika ada pembuatan direktori dengan nama `encv1_` dengan menggunakan fungsi `strstr(ptr, "/encv1_")` yang menyimpan path setelah karakter `encv1_`

selanjutnya pada fungsi `if()` akan dilakukan pengecekan pembuatan direktori yang diawali `encv1_`, jika tervalidasi adanya pembuatan dengan nama direktori tersebut maka fungsi `logfile()` akan dijalankan dengan ke 5 argumennya dan path akan di tunjuk oleh pointer `*desc[]` yang akan digunakan untuk mengisi kolom ke 5 dari output `logFile`

```c
 const char *desc[] = {path};
  logFile("INFO", "MKDIR", res, 1, desc);

	if (res == -1) return -errno;

	return 0;
```

### Script Soal Nomor 4
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
#### Penjelasan Script Soal Nomor 4
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

#### Kendala:
* Waktu yang diberikan kurang
* Belum terlalu paham dengan FUSE
* Keterbatasan referensi di internet
