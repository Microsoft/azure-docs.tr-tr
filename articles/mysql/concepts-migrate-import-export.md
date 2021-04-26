---
title: İçeri ve dışarı aktarma-MySQL için Azure veritabanı
description: Bu makalede, MySQL için Azure veritabanı 'nda MySQL çalışma ekranı gibi araçları kullanarak veritabanlarını içeri ve dışarı aktarmanın yaygın yolları açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 641dfa2439513138b5dd8f56843e81c31eb38609
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389783"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>İçeri ve dışarı aktarmayı kullanarak MySQL veritabanınızı geçirme

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Bu makalede MySQL çalışma ekranı kullanarak bir MySQL için Azure veritabanı sunucusuna verileri içeri ve dışarı aktarmaya yönelik iki yaygın yaklaşım açıklanmaktadır.

Ayrıntılı ve kapsamlı geçiş kılavuzu için, [Geçiş Kılavuzu kaynaklarına](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)bakın. 

Diğer geçiş senaryoları için [veritabanı geçiş kılavuzuna](https://datamigration.microsoft.com/)bakın. 

## <a name="prerequisites"></a>Önkoşullar

MySQL veritabanınızı geçirmeye başlamadan önce şunları yapmanız gerekir:
- [Azure Portal kullanarak MySQL Için Azure veritabanı sunucusu](quickstart-create-mysql-server-database-using-azure-portal.md)oluşturun.
- İçeri ve dışarı aktarma için [MySQL çalışma ekranı](https://dev.mysql.com/downloads/workbench/) veya başka bir üçüncü taraf MySQL aracı indirin ve yükleyin.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>MySQL için Azure veritabanı sunucusunda bir veritabanı oluşturma
MySQL çalışma ekranı, Toad veya Navıat kullanarak MySQL için Azure veritabanı sunucusunda boş bir veritabanı oluşturun. Veritabanı, dökülebilir verileri içeren veritabanıyla aynı ada sahip olabilir veya farklı bir ada sahip bir veritabanı oluşturabilirsiniz.

Bağlanmak için aşağıdakileri yapın:

1. Azure portal, MySQL için Azure veritabanı 'nın **genel bakış** bölmesinde bağlantı bilgilerini arayın.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Azure portal MySQL Server için Azure veritabanı bağlantı bilgilerinin ekran görüntüsü.":::

1. Bağlantı bilgilerini MySQL çalışma ekranına ekleyin.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="MySQL çalışma ekranı bağlantı dizesinin ekran görüntüsü.":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>İçeri ve dışarı aktarma tekniklerini ne zaman kullanacağınızı belirleme

> [!TIP]
> Tüm veritabanını dökümünü almak ve geri yüklemek istediğiniz senaryolar için, bunun yerine [döküm ve geri yükleme](concepts-migrate-dump-restore.md) yaklaşımını kullanın.

Aşağıdaki senaryolarda, MySQL araçları kullanarak veritabanlarını içeri aktarıp MySQL veritabanınıza dışarı aktarabilirsiniz. Diğer araçlar için [MySQL to Azure veritabanı geçiş kılavuzu](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf)' na yönelik "geçiş yöntemleri" bölümüne (sayfa 22) gidin. 

- Mevcut bir MySQL veritabanından Azure MySQL veritabanınıza içeri aktarmak için seçmeli olarak birkaç tablo seçmeniz gerektiğinde, bu, içeri ve dışarı aktarma tekniğinin kullanılması en iyisidir. Bunu yaptığınızda, zaman ve kaynakları kazanmak için geçişten gereksiz tabloları atlayabilirsiniz. Örneğin, `--include-tables` `--exclude-tables` [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables)ile veya anahtarını ve `--tables` [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables)ile anahtarı kullanın.
- Tablo dışındaki veritabanı nesnelerini taşırken, bu nesneleri açıkça oluşturun. Kısıtlama (birincil anahtar, yabancı anahtar ve dizinler), görünümler, işlevler, yordamlar, Tetikleyiciler ve geçirmek istediğiniz diğer veritabanı nesneleri dahil edin.
- MySQL veritabanı dışındaki dış veri kaynaklarından veri geçirirken, düz dosyalar oluşturun ve [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html)kullanarak içeri aktarın.

> [!Important]
> Tek sunucu ve esnek sunucu yalnızca InnoDB depolama altyapısını destekler. MySQL için Azure veritabanınıza veri yüklerken veritabanındaki tüm tabloların InnoDB Storage altyapısını kullanmasını sağlayın.
>
> Kaynak veritabanınız başka bir depolama altyapısı kullanıyorsa veritabanını geçirmeden önce InnoDB altyapısına dönüştürün. Örneğin, MyISAM altyapısını kullanan bir WordPress veya Web uygulamanız varsa, önce verileri InnoDB tablolarına geçirerek tabloları dönüştürün. Yan tümcesini kullanarak `ENGINE=INNODB` tablo oluşturma altyapısını ayarlayın ve ardından geçişten önce verileri uyumlu tabloya aktarın.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>İçeri ve dışarı aktarma için performans önerileri

En iyi verileri içeri aktarma ve dışarı aktarma performansı için aşağıdakileri yapmanızı öneririz:
-   Verileri yüklemeden önce kümelenmiş dizinler ve birincil anahtarlar oluşturun. Verileri birincil anahtar sırasıyla yükleyin.
-   Veriler yüklenene kadar ikincil dizinlerin oluşturulmasını geciktirin.
-   Verileri yüklemeden önce yabancı anahtar kısıtlamalarını devre dışı bırakın. Yabancı anahtar denetimlerinin devre dışı bırakılması, önemli ölçüde performans artışı sağlar. Kısıtlamaları etkinleştirin ve bilgi tutarlılığı sağlamak için yüklemeden sonra verileri doğrulayın.
-   Verileri paralel olarak yükleyin. Kaynak sınırına ulaşmanıza ve Azure portal bulunan ölçümleri kullanarak kaynakları izlemenize neden olacak çok fazla paralellik yapmaktan kaçının.
-   Uygun olduğunda bölümlenmiş tabloları kullanın.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>MySQL çalışma ekranı kullanarak verileri içeri ve dışarı aktarma
MySQL çalışma ekranına verileri dışarı ve içeri aktarmanın iki yolu vardır: nesne tarayıcısı bağlam menüsü ya da gezgin bölmesi. Her yöntem farklı bir amaca hizmet eder.

> [!NOTE]
> MySQL çalışma ekranı 'nda MySQL tek sunucuya veya esnek sunucuya (Önizleme) bağlantı ekliyorsanız, aşağıdakileri yapın:
> - MySQL tek sunucusu için, Kullanıcı adının biçimde olduğundan emin olun *\<username@servername>* .
> - MySQL esnek sunucusu için yalnızca kullanın *\<username>* . *\<username@servername>* Bağlanmak için kullanırsanız bağlantı başarısız olur.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Nesne tarayıcısı bağlam menüsünden Tablo verilerini dışarı ve içeri aktarma sihirbazları 'nı çalıştırın

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Nesne tarayıcısının bağlam menüsündeki MySQL çalışma ve içeri aktarma Sihirbazı komutlarının ekran görüntüsü.":::

Tablo veri sihirbazları CSV ve JSON dosyalarını kullanarak içeri ve dışarı aktarma işlemlerini destekler. Sihirbazlar ayırıcı, sütun seçimi ve kodlama seçimi gibi çeşitli yapılandırma seçeneklerini içerir. Her Sihirbazı yerel veya uzaktan bağlanan MySQL sunucularında çalıştırabilirsiniz. İçeri aktarma eylemi tablo, sütun ve tür eşlemeyi içerir.

Nesne tarayıcısı bağlam menüsünde bu sihirbazlara erişmek için, bir tabloya sağ tıklayın ve ardından **Tablo verileri dışarı aktarma Sihirbazı** veya **Tablo verileri içeri aktarma Sihirbazı**' nı seçin.

#### <a name="the-table-data-export-wizard"></a>Tablo verilerini dışarı aktarma Sihirbazı

Bir tabloyu bir CSV dosyasına aktarmak için:

1. Aktarılacak veritabanının tablosuna sağ tıklayın.
1. **Tablo verilerini dışarı aktarma Sihirbazı**' nı seçin. Aktarılacak sütunları, satır sapmasını (varsa) ve sayıyı (varsa) seçin.
1. **Dışarı aktarma için veri Seç** bölmesinde **İleri**' yi seçin. Dosya yolu, CSV veya JSON dosya türünü seçin. Ayrıca satır ayırıcı, kapsayan dizeler yöntemi ve alan ayırıcısı ' nı da seçin.
1. **Çıkış dosyası konumunu seçin** bölmesinde **İleri**' yi seçin.
1. **Verileri dışarı aktar** bölmesinde **İleri**' yi seçin.

#### <a name="the-table-data-import-wizard"></a>Tablo verilerini içeri aktarma Sihirbazı

Bir CSV dosyasından bir tablo içeri aktarmak için:

1. İçeri aktarılacak veritabanının tablosuna sağ tıklayın.
1. İçeri aktarılacak CSV dosyasını bulup seçin ve ardından **İleri**' yi seçin.
1. Hedef tabloyu (yeni veya var olan) seçin, **içeri aktarmadan önce tabloyu kes** onay kutusunu seçin veya temizleyin ve ardından **İleri**' yi seçin.
1. Kodlamayı ve içeri aktarılacak sütunları seçin ve ardından **İleri**' yi seçin.
1. **Verileri Içeri aktar** bölmesinde **İleri**' yi seçin. Sihirbaz verileri içeri aktarır.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Gezgin bölmesinden SQL veri dışa aktarma ve içeri aktarma sihirbazları 'nı çalıştırın
MySQL çalışma ekranı veya mysqldump komutundan oluşturulan SQL verilerini içeri veya dışarı aktarmak için bir sihirbaz kullanın. Sihirbazlara **Gezgin** bölmesinden erişebilirsiniz veya ana menüden **sunucu** ' yı seçebilirsiniz.

#### <a name="export-data"></a>Verileri dışarı aktarma

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Veri dışa aktarma bölmesini MySQL çalışma ekranı 'nda göstermek için Gezgin bölmesini kullanma ekran görüntüsü.":::

MySQL verilerinizi dışarı aktarmak için **veri dışa aktarma** bölmesini kullanabilirsiniz.

1. MySQL çalışma ekranı 'nda **Gezgin** bölmesinde, **veri dışarı aktarma**' yı seçin.

1. **Veri dışa aktarma** bölmesinde, dışarı aktarmak istediğiniz her şemayı seçin.
 
   Her şema için, belirli şema nesnelerini veya dışarı aktarılacak tabloları seçebilirsiniz. Yapılandırma seçenekleri bir proje klasörüne veya kendi içindeki bir SQL dosyasına dışarı aktarma, saklı yordamlar ve olayların dökümünü alma veya tablo verilerini atlama içerir.

   Alternatif olarak, SQL düzenleyicisinde belirli bir sonuç kümesini CSV, JSON, HTML ve XML gibi başka bir biçime dışarı aktarmak için **bir sonuç kümesini dışarı aktar** ' ı kullanın.

1. Dışarı aktarılacak veritabanı nesnelerini seçin ve ilgili seçenekleri yapılandırın.
1. Geçerli nesneleri yüklemek için **Yenile** ' yi seçin.
1. İsteğe bağlı olarak, dışarı aktarma işlemini iyileştirmek için sağ üst köşedeki **Gelişmiş Seçenekler** ' i seçin. Örneğin, tablo kilitleri ekleyin, `replace` deyimler yerine kullanın `insert` ve tırnak tanımlayıcıları ve geri değer karakterleri kullanın.
1. Dışarı aktarma işlemini başlatmak için **dışarı aktarmayı Başlat** ' ı seçin.


#### <a name="import-data"></a>Veri içeri aktarma

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Veri Içeri aktarma bölmesini MySQL çalışma ekranı 'nda göstermek için Gezgin bölmesini kullanma ekran görüntüsü.":::

Dışarı aktarılan verileri veri dışa aktarma işleminden veya mysqldump komutundan içeri aktarmak veya geri yüklemek için **veri Içeri aktarma** bölmesini kullanabilirsiniz.

1. MySQL çalışma ekranı 'nda **Gezgin** bölmesinde, **veri dışarı aktarma/geri yükleme**' yi seçin.
1. Proje klasörü veya kendi içinde kapsanan SQL dosyasını seçin, içine aktarılacak şemayı seçin veya yeni bir şema tanımlamak için **Yeni** düğmesini seçin.
1. İçeri aktarma işlemine başlamak için **Içeri aktarmayı Başlat** ' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar
- Başka bir geçiş yaklaşımı için bkz. [döküm ve geri yükleme kullanarak MySQL veritabanınızı MySQL Için Azure veritabanı 'Na geçirme](concepts-migrate-dump-restore.md).
- MySQL için Azure veritabanı 'na veritabanları geçirme hakkında daha fazla bilgi için [veritabanı geçiş kılavuzu](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)'na bakın.
