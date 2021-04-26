---
title: Döküm ve geri yükleme ile geçiş-MariaDB için Azure veritabanı
description: Bu makalede, mysqldump, MySQL II ve phpMyAdmin gibi araçları kullanarak MariaDB için Azure veritabanınızdaki veritabanlarını yedeklemenin ve geri yüklemenin iki yaygın yolu açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628229"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>Döküm ve geri yükleme kullanarak MariaDB veritabanınızı MariaDB için Azure veritabanı 'na geçirme

Bu makalede, MariaDB için Azure veritabanınızdaki veritabanlarını yedeklemenin ve geri yüklemenin iki yaygın yolu açıklanmaktadır:
- Komut satırı aracını kullanarak dökümünü alma ve geri yükleme (mysqldump kullanarak) 
- PhpMyAdmin kullanarak döküm ve geri yükleme

## <a name="prerequisites"></a>Önkoşullar
Veritabanınızı geçirmeye başlamadan önce aşağıdakileri yapın:
- [MariaDB Server Için Azure veritabanı-Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)oluşturun.
- [Mysqldump](https://mariadb.com/kb/en/library/mysqldump/) komut satırı yardımcı programını yükler.
- Döküm ve geri yükleme komutlarını çalıştırmak için [MySQL çalışma ekranı](https://dev.mysql.com/downloads/workbench/) veya başka bir üçüncü taraf MySQL aracı indirin ve yükleyin.

## <a name="use-common-tools"></a>Ortak araçları kullanma
MariaDB için Azure veritabanınıza verileri uzaktan bağlamak ve geri yüklemek için MySQL çalışma ekranı veya mysqldump gibi ortak yardımcı programları ve araçları kullanın. MariaDB için Azure veritabanı 'na bağlanmak üzere bir internet bağlantısı ile istemci makinenizde bu araçları kullanın. SSL şifreli bir bağlantıyı en iyi güvenlik uygulaması olarak kullanın. Daha fazla bilgi için bkz. [MariaDB Için Azure veritabanı 'NDA SSL bağlantısını yapılandırma](concepts-ssl-connection-security.md). MariaDB için Azure veritabanınıza veri geçirdiğinizde, döküm dosyalarını herhangi bir özel bulut konumuna taşımanız gerekmez. 

## <a name="common-uses-for-dump-and-restore"></a>Döküm ve geri yükleme için ortak kullanımlar
Veritabanlarının dökümünü yapmak ve veritabanlarını bir MariaDB sunucusu için Azure veritabanı 'na bir çok yaygın senaryoda yüklemek üzere mysqldump ve mysqlpump gibi MySQL yardımcı programlarını kullanabilirsiniz. 

- Veritabanının tamamını geçirirken veritabanı dökümlerini kullanın. Bu öneri, büyük miktarda veriyi taşırken veya canlı siteler veya uygulamalar için hizmet kesintisini en aza indirmek istediğinizde geçerlidir. 
-  MariaDB için Azure veritabanınıza veri yüklerken veritabanındaki tüm tabloların InnoDB Storage altyapısını kullanmasını sağlayın. MariaDB için Azure veritabanı yalnızca InnoDB depolama altyapısını ve diğer depolama altyapılarını destekler. Tablolarınız diğer depolama altyapılarıyla yapılandırıldıysa, bunları MariaDB için Azure veritabanınıza geçirmeden önce InnoDB Engine biçimine dönüştürün.
   
   Örneğin, MyISAM tablolarını kullanan bir WordPress uygulamanız veya bir Web uygulamanız varsa, bu tabloları MariaDB için Azure veritabanınıza geri yüklemeden önce InnoDB biçimine geçirerek dönüştürmeniz gerekir. Yan tümcesini kullanarak `ENGINE=InnoDB` Yeni bir tablo oluştururken kullanılacak altyapıyı ayarlayın ve ardından verileri geri yüklemeden önce uyumlu tabloya aktarın. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Veritabanları dökümünü yaparken herhangi bir uyumluluk sorununu önlemek için kaynak ve hedef sistemlerde aynı MariaDB sürümünü kullandığınızdan emin olun. Örneğin, var olan MariaDB sunucunuz sürüm 10,2 ise, sürüm 10,2 ' yi çalıştırmak üzere yapılandırılmış MariaDB için Azure veritabanınıza geçiş yapmanız gerekir. `mysql_upgrade`Komut, MariaDB sunucusu Için Azure veritabanı 'nda çalışmaz ve desteklenmez. MariaDB sürümleri arasında yükseltmeniz gerekiyorsa, ilk olarak önceki sürüm veritabanınızı, kendi ortamınızda bulunan MariaDB 'nin sonraki bir sürümüne aktarın. Daha sonra, `mysql_upgrade` MariaDB Için Azure veritabanınıza geçiş yapmayı denemeden önce komutunu çalıştırabilirsiniz.

## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar
Büyük veritabanları dökümünü yaparken performansı iyileştirmek için aşağıdaki noktalara dikkat edin:
-   `exclude-triggers`Mysqldump içindeki seçeneğini kullanın. Veri geri yükleme sırasında tetikleyici komutlarının tetiklenmesine engel olmak için Tetikleyicileri döküm dosyalarından hariç tutun. 
-   `single-transaction`Veri dökümünü yapmadan, işlem yalıtım modunu YINELENEBILIR okuma ve sunucuya başlangıç IŞLEMI SQL deyimlerinin ayarlandığı şekilde ayarlama seçeneğini kullanın. Tek bir işlem içindeki birçok tablo dökümünü almak, bazı ek depolamanın geri yükleme sırasında tüketilmesine neden olur. `single-transaction`Seçeneği ve `lock-tables` seçeneği birbirini dışlıyor. Bunun nedeni, kılıt TABLOLARıNıN bekleyen işlemlerin örtük olarak kaydedilmesine neden olmasına neden olur. Büyük tabloların dökümünü yapmak için `single-transaction` seçeneğini `quick` seçeneğiyle birleştirin. 
-   `extended-insert`ÇEŞITLI değer listeleri içeren birden çok satırlık sözdizimini kullanın. Bu yaklaşım, daha küçük bir döküm dosyası ile sonuçlanır ve dosya yeniden yüklendiğinde daha fazla hızlanır.
-  `order-by-primary`Veritabanları dökümünü yaparken mysqldump içindeki seçeneğini kullanın, böylece veriler birincil anahtar sırasıyla komut dosyası haline gelecek.
-   `disable-keys`Verileri dökümünde, yüklemeden önce yabancı anahtar kısıtlamalarını devre dışı bırakmak için mysqldump içindeki seçeneğini kullanın. Yabancı anahtar denetimlerini devre dışı bırakmak performansı artırmaya yardımcı olur. Kısıtlamaları etkinleştirin ve bilgi tutarlılığı sağlamak için yüklemeden sonra verileri doğrulayın.
-   Uygun olduğunda bölümlenmiş tabloları kullanın.
-   Verileri paralel olarak yükleyin. Çok fazla paralellik kullanmaktan kaçının, bu, bir kaynak sınırına ulaşmanıza ve Azure portal mevcut ölçümleri kullanarak kaynakları izlemenize neden olabilir. 
-   `defer-table-indexes`Veritabanları dökümünü yaparken mysqlpump içindeki seçeneğini kullanın, böylece tablo verileri yüklendikten sonra Dizin oluşturma olur.
-   Yedekleme dosyalarını bir Azure Blob deposuna kopyalayın ve geri yüklemeyi buradan gerçekleştirin. Bu yaklaşım, geri yüklemeyi Internet üzerinden gerçekleştirmekten daha hızlı bir şekilde olmalıdır.

## <a name="create-a-backup-file"></a>Yedekleme dosyası oluşturma

Yerel Şirket içi sunucuda veya bir sanal makinede var olan bir MariaDB veritabanını yedeklemek için, mysqldump kullanarak şu komutu çalıştırın: 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

Sağlanacak parametreler şunlardır:
- *\<uname>*: Veritabanınızın Kullanıcı adı 
- *\<pass>*: Veritabanınızın parolası (-p ile parola arasında boşluk olmadığını unutmayın) 
- *\<dbname>*: Veritabanınızın adı 
- *\<backupfile.sql>*: Veritabanı yedeklemenizin dosya adı 
- *\<--opt>*: Mysqldump seçeneği 

Örneğin, MariaDB sunucunuzdaki *TestDB* adlı bir veritabanını, Kullanıcı adı *testuser* ve testdb_backup. SQL dosyasına parola olmadan yedeklemek için aşağıdaki komutu kullanın. Komutu, veritabanını `testdb` `testdb_backup.sql` yeniden oluşturmak için gereken tüm SQL deyimlerini içeren adlı bir dosyaya veritabanını yedekler. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Veritabanınızda yedeklenecek belirli tabloları seçmek için tablo adlarını boşluklarla ayırarak listeleyin. Örneğin, *TestDB*'den yalnızca Table1 ve Table2 tablolarını yedeklemek için şu örneği izleyin: 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Aynı anda birden fazla veritabanını yedeklemek için--Database anahtarını kullanın ve boşluklarla ayırarak veritabanı adlarını listeleyin. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Hedef sunucuda bir veritabanı oluşturma
Verileri geçirmek istediğiniz MariaDB sunucusu için hedef Azure veritabanı 'nda boş bir veritabanı oluşturun. Veritabanını oluşturmak için MySQL çalışma ekranı gibi bir araç kullanın. Veritabanı, dökülebilir verileri içeren veritabanıyla aynı ada sahip olabilir veya farklı bir ada sahip bir veritabanı oluşturabilirsiniz.

Bağlanmak için, MariaDB için Azure veritabanınızın **genel bakış** bölmesindeki bağlantı bilgilerini bulun.

![Azure portal bir MariaDB sunucusu için Azure veritabanı 'Na ilişkin genel bakış bölmesinin ekran görüntüsü.](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

MySQL çalışma ekranı 'nda bağlantı bilgilerini ekleyin.

![MySQL çalışma ekranı içindeki MySQL bağlantıları bölmesinin ekran görüntüsü.](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>MariaDB veritabanınızı geri yükleme
Hedef veritabanını oluşturduktan sonra, verileri döküm dosyasından yeni oluşturulan veritabanına geri yüklemek için MySQL komutunu veya MySQL çalışma ekranı ' nı kullanabilirsiniz.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

Bu örnekte, verileri yeni oluşturulan veritabanına MariaDB sunucusu için hedef Azure veritabanında geri yükleme işlemini gerçekleştirebilirsiniz.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>MariaDB veritabanınızı phpMyAdmin kullanarak dışarı aktarma

Dışarı aktarmak için, ortamınızda zaten yerel olarak yüklenmiş olabilecek olan phpMyAdmin ortak aracını kullanabilirsiniz. MariaDB veritabanınızı dışarı aktarmak için aşağıdakileri yapın:
1. PhpMyAdmin öğesini açın.
1. Sol bölmede, veritabanınızı seçin ve ardından **dışarı aktarma** bağlantısını seçin. Veritabanının dökümünü görüntülemek için yeni bir sayfa görüntülenir.
1. **Dışarı aktarma** alanında, veritabanınızdaki tabloları seçmek Için **Tümünü Seç** bağlantısını seçin. 
1. **SQL seçenekleri** alanında uygun seçenekleri seçin. 
1. **Dosya olarak kaydet** seçeneğini ve ilgili sıkıştırma seçeneğini belirleyin ve ardından **Git**' i seçin. İstemde, dosyayı yerel olarak kaydedin.

## <a name="import-your-database-by-using-phpmyadmin"></a>PhpMyAdmin kullanarak veritabanınızı içeri aktarın

İçeri aktarma işlemi dışa aktarma işlemine benzerdir. Şunları yapın:
1. PhpMyAdmin öğesini açın. 
1. PhpMyAdmin Setup sayfasında, MariaDB sunucusu için Azure veritabanınızı eklemek üzere **Ekle** ' yi seçin. 
1. Bağlantı ayrıntılarını ve oturum açma bilgilerini girin.
1. Uygun şekilde adlandırılmış bir veritabanı oluşturun ve sol bölmede seçin. Var olan veritabanını yeniden yazmak için, veritabanı adını seçin, tablo adlarının yanındaki tüm onay kutularını seçin ve var olan tabloları silmek için **bırak** ' ı seçin. 
1. SQL komutunu girebileceğiniz sayfayı göstermek için **SQL** bağlantısını SEÇIN veya SQL dosyanızı karşıya yükleyin. 
1. Veritabanı dosyasını bulmak için, **tarayıcı** düğmesini seçin. 
1. Yedeklemeyi dışarı aktarmak, SQL komutlarını yürütmek ve veritabanınızı yeniden oluşturmak için **Git** düğmesini seçin.

## <a name="next-steps"></a>Sonraki adımlar
- [Uygulamaları MariaDB Için Azure veritabanınıza bağlayın](./howto-connection-string.md).
