# Veri Yedekleme ve AçkıKaynaklı Yöntemler
Bu çalışma, veri yedeklemenin temel prensiplerini, açık kaynaklı araçlarla uygulanışını ve backend sistemlerde yedekleme örneklerini anlatmaktadır.

1. Yedekleme Türleri

Yedekleme üç ana başlıkta incelenir:

Tam yedek (Full Backup): Sistemdeki tüm verilerin bir kopyası alınır. En güvenli ama en fazla yer kaplayan yöntemdir.

Artımlı yedek (Incremental Backup): Sadece son yedekten sonra değişen dosyalar kopyalanır. Daha hızlı ve verimlidir.

Fark yedeği (Differential Backup): Son tam yedekten sonra değişen tüm veriler alınır. Orta seviye bir yöntemdir.

Kurumsal veya sürekli veri üreten sistemlerde genellikle artımlı yedekleme tercih edilir.

2. Açık Kaynaklı Yedekleme Araçları
Restic

Restic, Go dilinde yazılmış, açık kaynaklı ve güvenli bir yedekleme aracıdır.
Verileri şifreler, sıkıştırır ve artımlı olarak depolar.
Yerel disklere, bulut depolara (S3, Backblaze, Google Cloud) veya uzak sunuculara (SFTP) yedek alabilir.

Örnek kullanım:
