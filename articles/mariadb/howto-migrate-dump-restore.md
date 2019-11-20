---
title: MariaDB veritabanınızı, MariaDB için Azure veritabanı 'nda dökümünü ve geri yüklemeyi kullanarak geçirme
description: Bu makalede, mysqldump, MySQL II ve PHPMyAdmin gibi araçları kullanarak MariaDB için Azure veritabanınızdaki veritabanlarını yedeklemenin ve geri yüklemenin iki yaygın yolu açıklanmaktadır.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 05626535a2ab2d8da29b8c817ebfe84c257c76aa
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845055"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>MariaDB veritabanınızı, döküm ve geri yükleme kullanarak MariaDB için Azure veritabanı 'na geçirme
Bu makalede, MariaDB için Azure veritabanınızdaki veritabanlarını yedeklemenin ve geri yüklemenin iki yaygın yolu açıklanmaktadır
- Komut satırından dökümünü al ve geri yükle (mysqldump kullanarak) 
- PHPMyAdmin kullanarak döküm ve geri yükleme

## <a name="before-you-begin"></a>Başlamadan önce
Bu nasıl yapılır kılavuzunda ilerlemek için şunları yapmanız gerekir:
- [MariaDB sunucusu için Azure veritabanı oluşturma-Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- bir makineye yüklü [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) komut satırı yardımcı programı.
- Bilgi döküm ve geri yükleme komutlarını bağlamak için MySQL II [MySQL çalışma ekranı Download](https://dev.mysql.com/downloads/workbench/), Toad, Navicat veya diğer üçüncü taraf MySQL aracı.

## <a name="use-common-tools"></a>Ortak araçları kullanma
MariaDB için Azure veritabanı 'na uzaktan bağlanıp veri geri yüklemek için MySQL çalışma ekranı, mysqldump, Toad veya Navicat gibi yaygın yardımcı programları ve araçları kullanın. MariaDB için Azure veritabanı 'na bağlanmak üzere bir internet bağlantısı ile istemci makinenizde bu tür araçları kullanın. En iyi güvenlik uygulamaları için SSL şifreli bir bağlantı kullanın, Ayrıca bkz. [MariaDB Için Azure veritabanı 'NDA SSL bağlantısını yapılandırma](concepts-ssl-connection-security.md). MariaDB için Azure veritabanı 'na geçiş yaparken döküm dosyalarını herhangi bir özel bulut konumuna taşımanız gerekmez. 

## <a name="common-uses-for-dump-and-restore"></a>Döküm ve geri yükleme için ortak kullanımlar
Veritabanlarının dökümünü yapmak ve veritabanlarını bir MariaDB sunucusu için Azure veritabanı 'na bir çok yaygın senaryoda yüklemek üzere mysqldump ve mysqlpump gibi MySQL yardımcı programlarını kullanabilirsiniz. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Tüm veritabanını geçirirken veritabanı dökümlerini kullanın. Bu öneri, büyük miktarda veriyi taşırken veya canlı siteler veya uygulamalar için hizmet kesintisini en aza indirmek istediğinizde barındırır. 
-  MariaDB için Azure Veritabanı'na veri yüklerken veritabanındaki tüm tabloların InnoDB depolama altyapısını kullandığından emin olun. MariaDB için Azure veritabanı yalnızca InnoDB depolama altyapısını destekler ve bu nedenle alternatif depolama altyapılarını desteklemez. Tablolarınız diğer depolama altyapılarıyla yapılandırıldıysa, MariaDB için Azure veritabanı 'na geçiş yapmadan önce InnoDB Engine biçimine dönüştürün.
   Örneğin, MyISAM tablolarını kullanarak bir WordPress veya WebApp varsa, önce bu tabloları MariaDB için Azure veritabanı 'na geri yüklemeden önce InnoDB biçimine geçiş yaparak dönüştürmeniz gerekir. Yeni bir tablo `ENGINE=InnoDB` oluştururken kullanılan altyapıyı ayarlamak için yan tümcesini kullanın, ardından geri yüklemeden önce verileri uyumlu tabloya aktarın. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Uyumluluk sorunlarından kaçınmak için veritabanlarının dökümünü alırken kaynak ve hedef sistemlerde aynı MariaDB sürümünün bulunduğundan emin olun. Örneğin, var olan MariaDB sunucunuz sürüm 10,2 ise, sürüm 10,2 ' yi çalıştırmak üzere, MariaDB için Azure veritabanı 'na geçiş yapmanız gerekir. `mysql_upgrade` Komut, MariaDB sunucusu için Azure veritabanı 'nda çalışmaz ve desteklenmez. MariaDB sürümleri arasında yükseltmeniz gerekiyorsa, ilk olarak daha düşük sürüm veritabanınızı, kendi ortamınızda MariaDB 'nin daha yüksek bir sürümüne aktarın. Ardından, `mysql_upgrade`MariaDB için Azure veritabanı 'na geçişi denemeden önce komutunu çalıştırın.

## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar
Performansı iyileştirmek için, büyük veritabanlarının dökümünü yaparken aşağıdaki noktalara dikkat edin:
-   Veritabanları dökümünü alırken mysqldump içindeki seçeneğinikullanın.`exclude-triggers` Veri geri yükleme sırasında tetikleyici komutlarının tetiklemesini önlemek için Tetikleyicileri döküm dosyalarından hariç tutun. 
-   Veri dökümünü yapmadan, işlem yalıtım modunu yinelenebilir okuma olarak ayarlama ve sunucuya bir başlangıç işlemi SQL açıklaması gönderme seçeneğinikullanın.`single-transaction` Tek bir işlem içindeki birçok tablo dökümünü almak, bazı ek depolamanın geri yükleme sırasında tüketilmesine neden olur. Kilitleme `single-transaction` tabloları bekleyen işlemlerin `lock-tables` örtük olarak kaydedilmesine neden olduğundan, seçeneği ve seçeneği birbirini dışlıyor. Büyük tabloların dökümünü yapmak için `single-transaction` seçeneğini `quick` seçeneğiyle birleştirin. 
-   Çeşitli değer listeleri içeren birdençoksatırlıksözdiziminikullanın.`extended-insert` Bu, daha küçük bir döküm dosyası ile sonuçlanır ve dosya yeniden yüklendiğinde daha fazla hızlanır.
-  Verileri birincil anahtar sırasıyla betikleştirilmiş hale getirmek için veritabanları dökümünü yaparken mysqldump içindeki seçeneğinikullanın.`order-by-primary`
-   Verileri dökümünde, yüklemeden önce yabancı anahtar kısıtlamalarını devre dışı bırakmak için mysqldump içindeki seçeneğinikullanın.`disable-keys` Yabancı anahtar denetimlerini devre dışı bırakmak, performans artışı sağlar. Kısıtlamaları etkinleştirin ve bilgi tutarlılığı sağlamak için yüklemeden sonra verileri doğrulayın.
-   Uygun olduğunda bölümlenmiş tabloları kullanın.
-   Verileri paralel olarak yükleyin. Kaynak sınırına ulaşmanıza ve Azure portal bulunan ölçümleri kullanarak kaynakları izlemenize neden olacak çok fazla paralellik yapmaktan kaçının. 
-   Tablo verileri yüklendikten sonra Dizin oluşturma işleminin gerçekleşmemesi için, veritabanları dökümünü alırken mysqlpump içindeki seçeneğinikullanın.`defer-table-indexes`
-   Yedekleme dosyalarını bir Azure Blob 'una/deposuna kopyalayın ve geri yükleme işlemini Internet 'te gerçekleştirmekten daha hızlı bir şekilde gerçekleştirin.

## <a name="create-a-backup-file"></a>Yedekleme dosyası oluşturma
Yerel Şirket içi sunucuda veya bir sanal makinede var olan bir MariaDB veritabanını yedeklemek için, mysqldump kullanarak şu komutu çalıştırın: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Sağlanacak parametreler şunlardır:
- [uname] Veritabanınızın Kullanıcı adı 
- aktar Veritabanınızın parolası (-p ile parola arasında boşluk yok) 
- dbname Veritabanınızın adı 
- [BackupFile. SQL] veritabanı yedeklemenizin dosya adı 
- [--opt] Mysqldump seçeneği 

Örneğin, MariaDB sunucunuzdaki ' TestDB ' adlı bir veritabanını ' testuser ' Kullanıcı adı ile ve testdb_backup. SQL dosyasına parola olmadan yedeklemek için aşağıdaki komutu kullanın. Komutu, veritabanını yeniden oluşturmak `testdb` için gereken tüm SQL deyimlerini `testdb_backup.sql`içeren adlı bir dosyaya veritabanını yedekler. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Veritabanınızdaki belirli tabloları seçerek, yedeklenecek tablo adlarını boşluklarla ayırarak listeleyin. Örneğin, ' TestDB ' öğesinden yalnızca Table1 ve Table2 tablolarını yedeklemek için şu örneği izleyin: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Aynı anda birden fazla veritabanını yedeklemek için--Database anahtarını kullanın ve boşluklarla ayrılmış veritabanı adlarını listeleyin. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Hedef sunucuda bir veritabanı oluşturma
Verileri geçirmek istediğiniz MariaDB sunucusu için hedef Azure veritabanı 'nda boş bir veritabanı oluşturun. Veritabanını oluşturmak için MySQL çalışma ekranı, Toad veya gezinti \ gibi bir araç kullanın. Veritabanı, dökülebilir verileri içeren veritabanıyla aynı ada sahip olabilir veya farklı bir ada sahip bir veritabanı oluşturabilirsiniz.

Bağlanmak için, MariaDB için Azure veritabanı 'na **genel bakış** bölümündeki bağlantı bilgilerini bulun.

![Azure portal bağlantı bilgilerini bulun](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Bağlantı bilgilerini MySQL çalışma ekranına ekleyin.

![MySQL çalışma ekranı bağlantı dizesi](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>MariaDB veritabanınızı geri yükleme
Hedef veritabanını oluşturduktan sonra, verileri döküm dosyasından yeni oluşturulan belirli veritabanına geri yüklemek için MySQL komutunu veya MySQL çalışma ekranı ' nı kullanabilirsiniz.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Bu örnekte, verileri yeni oluşturulan veritabanına MariaDB sunucusu için hedef Azure veritabanında geri yükleyin.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>PHPMyAdmin kullanarak dışarı aktarma
Dışarı aktarmak için, ortamınızda yerel olarak zaten yüklü olabilecek olan phpMyAdmin adlı ortak aracı kullanabilirsiniz. MariaDB veritabanınızı PHPMyAdmin kullanarak dışarı aktarmak için:
1. PhpMyAdmin öğesini açın.
2. Veritabanınızı seçin. Sol taraftaki listede veritabanı adına tıklayın. 
3. **Dışarı aktarma** bağlantısına tıklayın. Veritabanının dökümünü görüntülemek için yeni bir sayfa görüntülenir.
4. Dışarı aktarma alanında, veritabanınızdaki tabloları seçmek için **Tümünü Seç** bağlantısına tıklayın. 
5. SQL seçenekleri alanında uygun seçeneklere tıklayın. 
6. **Dosya olarak kaydet** seçeneğine ve ilgili sıkıştırma seçeneğine tıklayın ve sonra **Git** düğmesine tıklayın. Dosyayı yerel olarak kaydetmenizi isteyen bir iletişim kutusu görüntülenmelidir.

## <a name="import-using-phpmyadmin"></a>PHPMyAdmin kullanarak içeri aktarma
Veritabanınızı içeri aktarma işlemi dışa aktarmaya benzer. Aşağıdaki eylemleri yapın:
1. PhpMyAdmin öğesini açın. 
2. PhpMyAdmin Setup sayfasında, MariaDB sunucusu için Azure veritabanınızı eklemek üzere **Ekle** ' ye tıklayın. Bağlantı ayrıntılarını ve oturum açma bilgilerini girin.
3. Uygun şekilde adlandırılmış bir veritabanı oluşturun ve ekranın sol tarafında seçin. Var olan veritabanını yeniden yazmak için veritabanı adına tıklayın, tablo adlarının yanındaki tüm onay kutularını seçin ve var olan tabloları silmek için **bırak** ' ı seçin. 
4. SQL komutlarına ekleyebileceğiniz sayfayı göstermek için **SQL** bağlantısına tıklayın veya SQL dosyanızı karşıya yükleyin. 
5. Veritabanı dosyasını bulmak için **Araştır** düğmesini kullanın. 
6. Yedeklemeyi dışarı aktarmak, SQL komutlarını yürütmek ve veritabanınızı yeniden oluşturmak için **Git** düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
- [Uygulamaları MariaDB Için Azure veritabanı 'Na bağlayın](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
