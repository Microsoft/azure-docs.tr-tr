---
title: Çoğaltma gecikmesi sorunlarını giderme-MySQL için Azure veritabanı
description: MySQL için Azure veritabanı okuma çoğaltmaları ' nı kullanarak çoğaltma gecikmesini nasıl giderebileceğinizi öğrenin.
keywords: MySQL, sorun giderme, saniye cinsinden çoğaltma gecikme süresi
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 01/13/2021
ms.openlocfilehash: 92513a8c24b5106e3a59c8cfa4d743e900b957bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98249780"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>MySQL için Azure Veritabanı'nda çoğaltma gecikmesi sorununu giderme

[!INCLUDE[applies-to-single-flexible-server](./includes/applies-to-single-flexible-server.md)]

[Çoğaltma oku](concepts-read-replicas.md) özelliği, bir MySQL Için Azure veritabanı sunucusu sunucusundan salt bir çoğaltma sunucusuna veri çoğaltmanıza olanak sağlar. Okuma ve raporlama sorgularını uygulamadan çoğaltma sunucularına yönlendirerek iş yüklerini ölçekleyebilirsiniz. Bu kurulum, kaynak sunucu üzerindeki basıncı azaltır. Ayrıca, ölçeklendirilirken uygulamanın genel performansını ve gecikme süresini geliştirir.

Çoğaltmalar, MySQL altyapısının yerel ikili günlük (binlog) dosya konumu tabanlı çoğaltma teknolojisi kullanılarak zaman uyumsuz olarak güncelleştirilir. Daha fazla bilgi için bkz. [MySQL binlog dosyası konum tabanlı çoğaltma yapılandırmasına genel bakış](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

İkincil okuma çoğaltmalarının çoğaltma gecikmesi çeşitli etkenlere bağlıdır. Bu faktörler arasında şunlar yer alır ancak bunlarla sınırlı değildir:

- Ağ gecikmesi.
- Kaynak sunucudaki işlem hacmi.
- Kaynak sunucunun ve ikincil okuma çoğaltması sunucusunun işlem katmanı.
- Kaynak sunucuda ve ikincil sunucuda çalışan sorgular.

Bu makalede, MySQL için Azure veritabanı 'nda çoğaltma gecikmesini nasıl giderebileceğinizi öğreneceksiniz. Çoğaltma sunucularında daha fazla çoğaltma gecikme süresinin bazı yaygın nedenlerini de anlamış olursunuz.

> [!NOTE]
> Bu makale, Microsoft 'un artık kullandığı bir terim olan _bağımlı_ dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.
>

## <a name="replication-concepts"></a>Çoğaltma kavramları

İkili günlük etkinleştirildiğinde, kaynak sunucu kaydedilmiş işlemleri ikili günlüğe yazar. İkili günlük çoğaltma için kullanılır. 16 TB 'a kadar depolamayı destekleyen yeni sağlanan tüm sunucular için varsayılan olarak açıktır. Çoğaltma sunucularında, her çoğaltma sunucusunda iki iş parçacığı çalışır. Bir iş parçacığı *GÇ iş parçacığıdır* ve diğeri *SQL iş parçacığıdır*:

- GÇ iş parçacığı kaynak sunucuya bağlanır ve ikili günlükleri güncelleştirilmiş olarak ister. Bu iş parçacığı ikili günlük güncelleştirmelerini alır. Bu güncelleştirmeler bir çoğaltma sunucusuna, *geçiş günlüğü* adlı yerel bir günlüğe kaydedilir.
- SQL iş parçacığı geçiş günlüğünü okur ve sonra veri değişikliklerini çoğaltma sunucularında uygular.

## <a name="monitoring-replication-latency"></a>İzleme çoğaltma gecikmesi

MySQL için Azure veritabanı, [Azure izleyici](concepts-monitoring.md)'de saniye cinsinden çoğaltma gecikmesi için ölçüm sağlar. Bu ölçüm yalnızca okuma çoğaltması sunucularında kullanılabilir. MySQL 'de kullanılabilen seconds_behind_master ölçümü tarafından hesaplanır. 

Daha fazla çoğaltma gecikmesi Nedenini anlamak için, [MySQL](connect-workbench.md) çalışma sunucusunu veya [Azure Cloud Shell](https://shell.azure.com)kullanarak çoğaltma sunucusuna bağlanın. Ardından aşağıdaki komutu çalıştırın.

>[!NOTE]
> Kodunuzda, örnek değerleri, çoğaltma sunucunuzun adı ve yönetici kullanıcı adınızla değiştirin. Yönetici Kullanıcı adı, `@\<servername>` MySQL Için Azure veritabanı gerektirir.

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

Bu deneyim Cloud Shell terminalinde nasıl görünür:

```
Requesting a Cloud Shell.Succeeded.
Connecting terminal...

Welcome to Azure Cloud Shell

Type "az" to use Azure CLI
Type "help" to learn about Cloud Shell

user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 64796
Server version: 5.6.42.0 Source distribution

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

Aynı Cloud Shell terminalde, aşağıdaki komutu çalıştırın:

```
mysql> SHOW SLAVE STATUS;
```

Tipik bir çıktı aşağıda verilmiştir:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="İzleme çoğaltma gecikmesi":::

Çıktı çok fazla bilgi içerir. Normal olarak, yalnızca aşağıdaki tabloda açıklanan satırlara odaklanmanız gerekir.

|Metric|Açıklama|
|---|---|
|Slave_IO_State| GÇ iş parçacığının geçerli durumunu temsil eder. Normalde, kaynak (ana) sunucusu eşitleme ise durum "ana öğe gönderilmesi bekleniyor" olur. "Ana ağa bağlanma" gibi bir durum, çoğaltmanın kaynak sunucuyla olan bağlantıyı kaybettiğini belirtir. Kaynak sunucunun çalıştığından emin olun veya bir güvenlik duvarının bağlantıyı engelleyip engellemediğini denetleyin.|
|Master_Log_File| Kaynak sunucunun yazıldığı ikili günlük dosyasını temsil eder.|
|Read_Master_Log_Pos| Kaynak sunucunun ikili günlük dosyasında yazdığını gösterir.|
|Relay_Master_Log_File| Çoğaltma sunucusunun kaynak sunucudan okuduğu ikili günlük dosyasını temsil eder.|
|Slave_IO_Running| GÇ iş parçacığının çalışıp çalışmadığını belirtir. Değer olmalıdır `Yes` . Değer ise `NO` , çoğaltma muhtemelen bozulur.|
|Slave_SQL_Running| SQL iş parçacığının çalışıp çalışmadığını belirtir. Değer olmalıdır `Yes` . Değer ise `NO` , çoğaltma muhtemelen bozulur.|
|Exec_Master_Log_Pos| Çoğaltmanın uygulama Relay_Master_Log_File konumunu gösterir. Gecikme süresi varsa, bu konum sırası Read_Master_Log_Pos daha küçük olmalıdır.|
|Relay_Log_Space|Geçiş günlük boyutunun üst sınırını gösterir. Boyutunu girerek kontrol edebilirsiniz `SHOW GLOBAL VARIABLES` `relay_log_space_limit` .|
|Seconds_Behind_Master| Çoğaltma gecikmesini saniye cinsinden görüntüler.|
|Last_IO_Errno|Varsa, GÇ iş parçacığı hata kodunu görüntüler. Bu kodlar hakkında daha fazla bilgi için [MySQL sunucusu hata iletisi başvurusuna](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)bakın.|
|Last_IO_Error| Varsa, GÇ iş parçacığı hata iletisini görüntüler.|
|Last_SQL_Errno|Varsa, SQL iş parçacığı hata kodunu görüntüler. Bu kodlar hakkında daha fazla bilgi için [MySQL sunucusu hata iletisi başvurusuna](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)bakın.|
|Last_SQL_Error|Varsa, SQL iş parçacığı hata iletisini görüntüler.|
|Slave_SQL_Running_State| Geçerli SQL iş parçacığı durumunu gösterir. Bu durumda, `System lock` normaldir. Durumunu görmek de normaldir `Waiting for dependent transaction to commit` . Bu durum, çoğaltmanın kaynak sunucunun işlenen işlemleri güncelleştirmesini beklediğini belirtir.|

Slave_IO_Running `Yes` ve Slave_SQL_Running ise, `Yes` çoğaltma sorunsuz şekilde çalışır. 

Ardından Last_IO_Errno, Last_IO_Error, Last_SQL_Errno ve Last_SQL_Error denetleyin.  Bu alanlar, SQL iş parçacığının durdurulmasına neden olan en son hatanın hata numarasını ve hata iletisini görüntüler. Hata numarası `0` ve boş bir ileti bir hata olmadığı anlamına gelir. [MySQL sunucusu hata iletisi başvurusunda](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)hata kodunu denetleyerek sıfır olmayan hata değerlerini araştırın.

## <a name="common-scenarios-for-high-replication-latency"></a>Yüksek çoğaltma gecikmesi için yaygın senaryolar

Aşağıdaki bölümlerde, yüksek çoğaltma gecikmesi ortak olan senaryolar ele verilmektedir.

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>Kaynak sunucuda ağ gecikmesi veya yüksek CPU tüketimi

Aşağıdaki değerleri görürseniz, çoğaltma gecikme süresi büyük olasılıkla kaynak sunucuda yüksek ağ gecikmesi veya yüksek CPU tüketimine neden olur.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

Bu durumda, GÇ iş parçacığı çalışıyor ve kaynak sunucu üzerinde bekliyor. Kaynak sunucu zaten 20 günlük dosyası numarasına yazıldı. Çoğaltma yalnızca dosya numarası 10 ' a kadar aldı. Bu senaryoda yüksek çoğaltma gecikme süresinin birincil faktörleri, kaynak sunucuda ağ hızı veya yüksek CPU kullanımlardır.  

Azure 'da bir bölgedeki ağ gecikmesi genellikle ölçülen milisaniyelik olabilir. Bölgeler arasında, gecikme süresi milisaniye ile saniyeye saniye arasındadır.

Çoğu durumda, GÇ iş parçacıkları ve kaynak sunucu arasındaki bağlantı gecikmesi, kaynak sunucuda yüksek CPU kullanımı nedeniyle oluşur. GÇ iş parçacıkları yavaş işlenir. Bu sorunu, Azure Izleyici 'yi kullanarak CPU kullanımını ve kaynak sunucudaki eşzamanlı bağlantı sayısını denetlemek için algılayabilirsiniz.

Kaynak sunucuda yüksek CPU kullanımı görmüyorsanız, sorun ağ gecikmesi olabilir. Ağ gecikmesi aniden anormal bir şekilde yüksekse, bilinen sorunlar veya kesintiler için [Azure durum sayfasına](https://status.azure.com/status) bakın. 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>Kaynak sunucudaki işlemlerin büyük bir kopyası

Aşağıdaki değerleri görürseniz, kaynak sunucudaki yoğun işlem gecikmesi büyük olasılıkla çoğaltma gecikmesine neden olur. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Çıktı, çoğaltmanın kaynak sunucunun arkasındaki ikili günlüğü alacağının gösterir. Ancak çoğaltma GÇ iş parçacığı, geçiş günlük alanının zaten dolu olduğunu gösterir.

Ağ hızı gecikmeye neden olmaz. Çoğaltma yakalamaya çalışıyor. Ancak güncelleştirilmiş ikili günlük boyutu, geçiş günlük alanının üst sınırını aşıyor.

Bu sorunu gidermek için, kaynak sunucuda [yavaş sorgu günlüğünü](concepts-server-logs.md) etkinleştirin. Kaynak sunucuda uzun süre çalışan işlemleri belirlemek için yavaş sorgu günlüklerini kullanın. Ardından, sunucuda gecikme süresini azaltmak için tanımlanan sorguları ayarlayın. 

Bu sıralamanın çoğaltma gecikmesi genellikle kaynak sunucudaki veri yükünün oluşmasına neden olur. Kaynak sunucularda haftalık veya aylık veri yükleri olduğunda, çoğaltma gecikmesi ne yazık ki kaçınılmaz. Çoğaltma sunucuları, kaynak sunucudaki veri yükünün sonunda sona ermesinden sonra yakalar.

### <a name="slowness-on-the-replica-server"></a>Çoğaltma sunucusunda yavaşlık

Aşağıdaki değerleri gözlemlerseniz, sorun Çoğaltma sunucusunda olabilir.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

Bu senaryoda, çıkış hem GÇ iş parçacığı hem de SQL iş parçacığının iyi çalıştığını gösterir. Çoğaltma, kaynak sunucunun yazdığı ikili günlük dosyasını okur. Ancak, Çoğaltma sunucusunda bazı gecikme süreleri kaynak sunucudan aynı işlemi yansıtır.

Aşağıdaki bölümlerde bu tür bir gecikme süresinin yaygın nedenleri açıklanır.

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>Tabloda birincil anahtar veya benzersiz anahtar yok

MySQL için Azure veritabanı satır tabanlı çoğaltmayı kullanır. Kaynak sunucu, olayları ikili günlüğe yazar ve tek tablo satırlarındaki değişiklikleri kaydeder. SQL iş parçacığı daha sonra bu değişiklikleri çoğaltma sunucusundaki ilgili tablo satırlarına çoğaltır. Bir tabloda birincil anahtar veya benzersiz anahtar olmadığında SQL iş parçacığı değişiklikleri uygulamak için hedef tablodaki tüm satırları tarar. Bu tarama çoğaltma gecikmesine yol açabilir.

MySQL 'de birincil anahtar, NULL değerler içeremediği için hızlı sorgu performansını sağlayan ilişkili bir dizindir. InnoDB Storage altyapısını kullanıyorsanız, tablo verileri fiziksel olarak hızlı aramalar yapmak ve birincil anahtara göre sıralama yapmak için fiziksel olarak düzenlenir.

Çoğaltma sunucusunu oluşturmadan önce kaynak sunucudaki tablolara birincil anahtar eklemenizi öneririz. Kaynak sunucuya birincil anahtarlar ekleyin ve ardından çoğaltma gecikmesini artırmaya yardımcı olmak için okuma çoğaltmalarını yeniden oluşturun.

Kaynak sunucuda birincil anahtar eksik olan tabloları bulmak için aşağıdaki sorguyu kullanın:

```sql
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="long-running-queries-on-the-replica-server"></a>Çoğaltma sunucusunda uzun süre çalışan sorgular

Çoğaltma sunucusundaki iş yükü, SQL iş parçacığı gecikmesini GÇ iş parçacığı arkasında yapabilir. Çoğaltma sunucusunda uzun süre çalışan sorgular, yüksek çoğaltma gecikmesinin genel nedenleriyle biridir. Bu sorunu gidermek için, Çoğaltma sunucusunda [yavaş sorgu günlüğünü](concepts-server-logs.md) etkinleştirin.

Yavaş sorgular, çoğaltma kaynak sunucuyla yakalayamaması için kaynak tüketimini artırabilir veya sunucunun yavaşlamasına neden olabilir. Bu senaryoda, yavaş sorguları ayarlayın. Daha hızlı sorgular SQL iş parçacığının engellenmesini engeller ve çoğaltma gecikmesini önemli ölçüde geliştirir.

#### <a name="ddl-queries-on-the-source-server"></a>Kaynak sunucuda DDL sorguları

Kaynak sunucuda, gibi bir veri tanımlama dili (DDL) komutu [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) uzun zaman alabilir. DDL komutu çalışırken, diğer binlerce sorgu kaynak sunucuda paralel olarak çalışıyor olabilir. 

DDL 'nin çoğaltılırsa, veritabanı tutarlılığını sağlamak için MySQL altyapısı DDL 'yi tek bir çoğaltma iş parçacığında çalıştırır. Bu görev sırasında, diğer tüm çoğaltılan sorgular engellenir ve Çoğaltma sunucusunda DDL işlemi bitene kadar beklemeniz gerekir. Hatta çevrimiçi DDL işlemleri bu gecikmeye neden olur. DDL işlemleri çoğaltma gecikmesini artırır.

Kaynak sunucuda [yavaş sorgu günlüğünü](concepts-server-logs.md) etkinleştirdiyseniz, kaynak sunucuda çalışan bir DDL komutunu denetleyerek bu gecikme sorununu tespit edebilirsiniz. Dizin bırakma, yeniden adlandırma ve oluşturma aracılığıyla ALTER TABLE için INPLACE algoritmasını kullanabilirsiniz. Tablo verilerini kopyalamanız ve tabloyu yeniden oluşturmanız gerekebilir.

Genellikle, eş zamanlı DML, ıNPLACE algoritması için desteklenir. Ancak işlemi hazırlarken ve çalıştırdığınızda tablo üzerinde özel bir meta veri kilidi alabilirsiniz. Bu nedenle CREATE INDEX ifadesinde, tablo kopyalama yöntemini ve okuma ve yazma için eşzamanlılık düzeyini etkilemek için yan tümce ALGORITMASıNı ve KILIDI kullanabilirsiniz. Bir tam metın dizini veya uzamsal dizin ekleyerek DML işlemlerini yine de engelleyebilirsiniz.

Aşağıdaki örnek, ALGORITMA ve kılıt yan tümceleri kullanarak bir dizin oluşturur.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Ne yazık ki kilit gerektiren bir DDL bildiriminde çoğaltma gecikmesini önleyebilirsiniz. Olası etkileri azaltmak için, bu tür DDL işlemlerini yoğun olmayan saatlerde yapın, örneğin gece boyunca.

#### <a name="downgraded-replica-server"></a>Çoğaltma sunucusu düşürülemez

MySQL için Azure veritabanı 'nda, okuma çoğaltmaları, kaynak sunucuyla aynı sunucu yapılandırmasını kullanır. Oluşturulduktan sonra çoğaltma sunucusu yapılandırmasını değiştirebilirsiniz.

Çoğaltma sunucusu indirgenirse, iş yükü daha fazla kaynak tüketebilir ve bu da çoğaltma gecikmesine neden olabilir. Bu sorunu tespit etmek için Azure Izleyici 'yi kullanarak çoğaltma sunucusunun CPU ve bellek tüketimini denetleyin.

Bu senaryoda, çoğaltma sunucusunun yapılandırmasını kaynak sunucu değerlerine eşit veya ondan daha büyük değerlerle tutmanızı öneririz. Bu yapılandırma, çoğaltmanın kaynak sunucu ile kalmasına izin verir.

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>Kaynak sunucu parametrelerini ayarlayarak çoğaltma gecikmesini artırma

MySQL için Azure veritabanı 'nda, varsayılan olarak çoğaltma, çoğaltmalarda paralel iş parçacıklarıyla çalışacak şekilde en iyi duruma getirilir. Kaynak sunucudaki yüksek eşzamanlılık iş yükleri çoğaltma sunucusunun arkasına dönmesine neden oluyorsa, kaynak sunucuda binlog_group_commit_sync_delay parametresini yapılandırarak çoğaltma gecikmesini geliştirebilirsiniz.

Binlog_group_commit_sync_delay parametresi, ikili günlük dosyasının eşitlenmesi için kaç mikrosaniye ikili günlük işlemesini denetler. Bu parametrenin avantajı, her bir kaydedilmiş işlemi hemen uygulamak yerine, kaynak sunucunun ikili günlük güncelleştirmelerini toplu olarak göndereceğini unutmayın. Bu gecikme, çoğaltmada GÇ 'yi azaltır ve performansı artırmaya yardımcı olur.

Binlog_group_commit_sync_delay parametresini 1000 olarak ayarlamak yararlı olabilir. Sonra çoğaltma gecikmesini izleyin. Bu parametreyi dikkatle ayarlayın ve yalnızca yüksek eşzamanlılık iş yükleri için kullanın.

> [!IMPORTANT]
> Çoğaltma sunucusunda, binlog_group_commit_sync_delay parametresinin 0 olması önerilir. Bu, kaynak sunucunun aksine, çoğaltma sunucusunun yüksek eşzamanlılık sahibi olmadığından ve çoğaltma sunucusu üzerindeki binlog_group_commit_sync_delay değerinin artırılması yanlışlıkla çoğaltma gecikmesi artışına neden olabileceğinden, bu önerilir.

Birçok tek işlem içeren düşük eşzamanlılık iş yükleri için binlog_group_commit_sync_delay ayarı gecikmeyi artırabilir. GÇ iş parçacığı yalnızca birkaç işlem yapıldığından bile toplu ikili günlük güncelleştirmeleri beklediği için gecikme artabilir.

## <a name="next-steps"></a>Sonraki adımlar

[MySQL binlog çoğaltmasına genel bakış konusuna](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)göz atın.
