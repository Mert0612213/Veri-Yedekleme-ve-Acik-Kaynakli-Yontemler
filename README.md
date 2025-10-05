# Veri Yedekleme ve AçkıKaynaklı Yöntemler
Bu çalışma, veri yedeklemenin temel prensiplerini, açık kaynaklı araçlarla uygulanışını ve backend sistemlerde yedekleme örneklerini anlatmaktadır.

---

### 1. Yedekleme Türleri

Yedekleme üç ana başlıkta incelenir:

* **Tam yedek (Full Backup):** Sistemdeki tüm verilerin bir kopyası alınır. En güvenli ama en fazla yer kaplayan yöntemdir.
* **Artımlı yedek (Incremental Backup):** Sadece son yedekten sonra değişen dosyalar kopyalanır. Daha hızlı ve verimlidir.
* **Fark yedeği (Differential Backup):** Son tam yedekten sonra değişen tüm veriler alınır. Orta seviye bir yöntemdir.

![Backup](https://example.com/kedi.png](https://rkicdn.rkimball.com/how_database_backup_works_in_sql_server.png)


Kurumsal veya sürekli veri üreten sistemlerde genellikle artımlı yedekleme tercih edilir.

---

### 2. Açık Kaynaklı Yedekleme Araçları

#### Restic

Restic, Go dilinde yazılmış, açık kaynaklı ve güvenli bir yedekleme aracıdır.
Verileri şifreler, sıkıştırır ve artımlı olarak depolar.
Yerel disklere, bulut depolara (S3, Backblaze, Google Cloud) veya uzak sunuculara (SFTP) yedek alabilir.

Örnek kullanım:

```
restic init --repo /mnt/backup
restic -r /mnt/backup backup /srv/data/
restic forget --keep-daily 7 --keep-weekly 4 --prune
```

Bu örnekte `/srv/data/` dizini yedeklenir, son 7 günlük ve 4 haftalık yedekler tutulur.
Bu şekilde sistem, birkaç haftalık geçmişe kadar geri dönebilir.

---

#### Rsync

Rsync, dosya senkronizasyonu ve yedekleme için kullanılan klasik bir araçtır.
Yalnızca değişen dosyaları aktarır.
Uzak sistemler arasında da çalışabilir.

Örnek:

```
rsync -avh --delete /var/www/ /mnt/backup/www/
```

Bu komut, web sunucusundaki dosyaları yedek dizinle eşitler.
Silinen dosyaları da hedefte silerek tam bir senkron sağlar.

---

#### BorgBackup

BorgBackup, restic gibi artımlı yedekleme yapar ama veri sıkıştırma ve deduplikasyon (aynı veriyi tek kopya halinde saklama) konusunda daha etkilidir.
Büyük boyutlu sistemlerde disk kullanımını ciddi şekilde azaltır.

---

### 3. Yedekleme Sıklığı

Yedekleme aralıkları sistemin önemine göre belirlenir.
Genel olarak:

* **Kritik veriler (ör. veritabanı):** 3-6 saatte bir
* **Uygulama dosyaları:** Günlük
* **Konfigürasyon ve loglar:** Haftalık

Yedekleme otomasyonu genellikle `cron` görevleriyle yapılır.
Örneğin her 6 saatte bir yedek almak için:

```
0 */6 * * * /usr/local/bin/backup.sh
```

---

### 4. Veritabanı Yedekleme (MySQL Örneği)

Veritabanı, uygulamalardaki en hassas bölümdür.
Tüm kullanıcı bilgileri, içerikler, ayarlar burada saklanır.
Yedek almak için genelde `mysqldump` komutu kullanılır.

```
mysqldump -u root -p my_database > /mnt/backup/my_database_$(date +%F_%H-%M).sql
```

Bu komut, veritabanını `.sql` dosyası olarak kaydeder.
Dosya ismine tarih eklendiği için her yedek ayrı tutulur.

---

### 5. Uygulama Örneği: Online Oyun Sunucusu

Örnek olarak bir online oyun düşünelim.
Oyunun backend tarafında oyuncuların kayıtları, seviyeleri, skorları ve envanteri MySQL veritabanında tutuluyor.
Ayrıca oyunun dosya yapısında harita verileri, log dosyaları ve sunucu ayarları var.

Bu durumda sistem yöneticisi şu şekilde bir yedekleme planı kurabilir:

* **Veritabanı:** Her 3 saatte bir `mysqldump` ile otomatik yedek alınır.
* **Oyun dosyaları (ör. /srv/game_server/):** Restic ile günde bir kez yedeklenir.
* **Log dosyaları:** Haftalık olarak arşivlenir ve silinir.

Bu plan sayesinde hem oyuncu kayıtları hem de oyun dosyaları güvence altına alınmış olur.
Bir çökme veya hata durumunda en fazla birkaç saatlik veri kaybı yaşanır, sistem hızlıca geri yüklenebilir.
