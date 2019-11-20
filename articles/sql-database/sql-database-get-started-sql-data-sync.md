---
title: Veri eşitlemesini ayarlama
description: Bu öğreticide, Azure SQL Data Sync ayarlama gösterilmektedir
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: 315d65b635f34847d0310e6b6adf03e20f2afd45
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73807519"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Öğretici: Azure SQL veritabanı ve şirket içi SQL Server arasında SQL Data Sync ayarlama

Bu öğreticide, Azure SQL veritabanı ve SQL Server örneklerini içeren bir eşitleme grubu oluşturarak Azure SQL Data Sync ayarlamayı öğreneceksiniz. Eşitleme grubu özel olarak yapılandırılır ve ayarladığınız zamanlamaya göre eşitlenir.

Bu öğreticide, SQL veritabanı ve SQL Server ile ilgili en az bir önceki deneyim olduğunu varsaymaktadır.

SQL Data Sync genel bir bakış için bkz. [Azure SQL Data Sync ile bulut ve şirket içi veritabanları arasında verileri eşitleme](sql-database-sync-data.md).

SQL Data Sync nasıl yapılandırılacağı hakkında PowerShell örnekleri için bkz. [Azure SQL veritabanları](scripts/sql-database-sync-data-between-sql-databases.md) veya [Azure sql veritabanı ile SQL Server şirket içi veritabanı](scripts/sql-database-sync-data-between-azure-onprem.md) arasında eşitleme

> [!IMPORTANT]
> Azure SQL Data Sync Şu anda Azure SQL veritabanı yönetilen **örneğini desteklemez.**

## <a name="create-sync-group"></a>Eşitleme grubu oluştur

1. Tarayıcıda Azure portal gidin. Panodan SQL veritabanınızı bulun veya araç çubuğunda **SQL veritabanları** simgesini seçin ve **SQL veritabanları** sayfasında veri eşitleme için merkez veritabanı olarak kullanmak istediğiniz veritabanını seçin.

    > [!NOTE]
    > Merkez veritabanı, bir eşitleme grubunun birden çok veritabanı uç noktasına sahip olduğu bir eşitleme topolojisinin merkezi uç noktasıdır. Eşitleme grubundaki uç noktalara sahip diğer tüm üye veritabanları, hub veritabanıyla eşitlenir.

1. Seçili veritabanı için **SQL veritabanı** sayfasında **diğer veritabanlarına Eşitle**' yi seçin.

    ![Diğer veritabanlarıyla Eşitle seçeneği](media/sql-database-get-started-sql-data-sync/datasync-overview.png)

1. **Diğer veritabanlarına Eşitle** sayfasında, **Yeni eşitleme grubu**' nu seçin. **Yeni eşitleme grubu** sayfası, **eşitleme grubu oluştur (1. adım)** vurgulanmış olarak açılır.

   ![Adım 1 ayarları](media/sql-database-get-started-sql-data-sync/stepone.png)

   **Veri eşitleme grubu oluştur** sayfasında, aşağıdaki ayarları değiştirin:

   | Ayar                        | Açıklama |
   | ------------------------------ | ------------------------------------------------- |
   | **Eşitleme grubu adı** | Yeni eşitleme grubu için bir ad girin. Bu ad, veritabanının kendisinin adından farklıdır. |
   | **Meta veri veritabanını Eşitle** | Veritabanı oluşturmayı seçin (önerilir) veya var olan bir veritabanını kullanın.<br/><br/>**Yeni veritabanı**' nı seçerseniz **Yeni veritabanı oluştur** ' u seçin. Ardından **SQL veritabanı** sayfasında, yeni veritabanını adlandırın ve yapılandırın ve **Tamam**' ı seçin.<br/><br/>**Varolan veritabanını kullan**' ı seçerseniz, listeden veritabanını seçin. |
   | **Otomatik eşitleme** | **Açık** veya **kapalı**seçeneğini belirleyin.<br/><br/>**Açık**' ı seçerseniz, bir sayı girin ve **eşitleme sıklığı** bölümünde **saniye**, **dakika**, **saat**veya **gün** ' yı seçin. |
   | **Çakışma çözümleme** | **Hub Win** veya **member WIN**' i seçin.<br/><br/>**Merkez kazanma** , çakışmaların ne zaman meydana geldiğini, Merkez veritabanındaki verileri üye veritabanındaki çakışan verilerin üzerine yazar.<br/><br/>**Üye kazanma** , çakışmaların ne zaman meydana geldiğini, üye veritabanındaki verileri Merkez veritabanındaki çakışan verilerin üzerine yazar. |

   > [!NOTE]
   > Microsoft, **eşitleme meta verileri veritabanı**olarak kullanılmak üzere yeni, boş bir veritabanı oluşturmayı önerir. Veri eşitleme, bu veritabanında tabloları oluşturur ve sık sık iş yükü çalıştırır. Bu veritabanı seçili bir bölgedeki tüm eşitleme grupları için **eşitleme meta verileri veritabanı** olarak paylaşılır ve bölgedeki tüm eşitleme gruplarını ve eşitleme aracılarını kaldırmadan veritabanını veya adını değiştiremezsiniz.

   **Tamam** ' ı seçin ve eşitleme grubunun oluşturulmasını ve dağıtılmasını bekleyin.

## <a name="add-sync-members"></a>Eşitleme üyeleri Ekle

Yeni eşitleme grubu oluşturulup dağıtıldıktan sonra, **eşitleme üyeleri Ekle (2. adım)** , **Yeni eşitleme grubu** sayfasında vurgulanır.

**Merkez veritabanı** bölümünde, hub VERITABANıNıN bulunduğu SQL veritabanı sunucusu için mevcut kimlik bilgilerini girin. Bu bölüme *Yeni* kimlik bilgileri girmeyin.

![2\. adım ayarları](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Azure SQL veritabanı eklemek için

**Üye veritabanı** bölümünde, isteğe bağlı olarak Azure SQL veritabanı **Ekle**' yi seçerek EŞITLEME grubuna bir Azure SQL veritabanı ekleyin. **Azure SQL veritabanını Yapılandır** sayfası açılır.

  ![2\. adım-veritabanını yapılandırma](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  **Azure SQL veritabanını Yapılandır** sayfasında, aşağıdaki ayarları değiştirin:

  | Ayar                       | Açıklama |
  | ----------------------------- | ------------------------------------------------- |
  | **Eşitleme üyesi adı** | Yeni eşitleme üyesi için bir ad sağlayın. Bu ad, veritabanı adından farklıdır. |
  | **Abonelik** | Faturalama amacıyla ilişkili Azure aboneliğini seçin. |
  | **Azure SQL Server** | Mevcut SQL veritabanı sunucusunu seçin. |
  | **Azure SQL Veritabanı** | Mevcut SQL veritabanını seçin. |
  | **Eşitleme yönleri** | **Çift yönlü eşitlemeyi**, **hub 'A**veya **hub 'dan**seçin. |
  | **Kullanıcı adı** ve **parola** | Üye veritabanının bulunduğu SQL veritabanı sunucusu için mevcut kimlik bilgilerini girin. Bu bölüme *Yeni* kimlik bilgileri girmeyin. |

  **Tamam** ' ı seçin ve yeni eşitleme üyesinin oluşturulması ve dağıtılması için bekleyin.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Şirket içi SQL Server veritabanı eklemek için

**Üye veritabanı** bölümünde, isteğe bağlı olarak, Şirket **içi veritabanı Ekle**' yi seçerek eşitleme grubuna şirket içi SQL Server ekleyin. **Şirket Içi yapılandırma** sayfası açılır ve aşağıdaki işlemleri yapabilirsiniz:

1. **Eşitleme Aracısı ağ geçidini Seç**' i seçin. **Eşitleme aracısını seçin** sayfası açılır.

   ![Eşitleme Aracısı oluşturma](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. **Eşitleme aracısını seçin** sayfasında, var olan bir aracıyı mi kullanacağınızı yoksa bir aracı mı oluşturulacağını seçin.

   **Mevcut aracılar**' ı seçerseniz, listeden var olan aracıyı seçin.

   **Yeni bir aracı oluştur**' u seçerseniz, aşağıdaki işlemleri yapın:

   1. Veri eşitleme aracısını, belirtilen bağlantıdan indirin ve SQL Server bulunduğu bilgisayara yükleyin. Aracıyı doğrudan [SQL Azure veri eşitleme aracısından](https://www.microsoft.com/download/details.aspx?id=27693)de indirebilirsiniz.

      > [!IMPORTANT]
      > İstemci aracısının sunucusuyla iletişim kurmasına izin vermek için güvenlik duvarında giden TCP bağlantı noktası 1433 ' i açmanız gerekir.

   1. Aracı için bir ad girin.

   1. **Anahtar oluştur ve oluştur** ' u seçin ve aracı anahtarını panoya kopyalayın.

   1. **Eşitleme aracısını Seç** sayfasını kapatmak için **Tamam ' ı** seçin.

1. SQL Server bilgisayarda, İstemci Eşitleme Aracısı uygulamasını bulun ve çalıştırın.

   ![Veri eşitleme istemci Aracısı uygulaması](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. Eşitleme Aracısı uygulamasında, **Aracı anahtarını gönder**' i seçin. **Eşitleme meta verileri veritabanı yapılandırması** iletişim kutusu açılır.

    1. **Eşitleme meta verileri veritabanı yapılandırması** iletişim kutusunda, Azure Portal kopyaladığınız aracı anahtarını yapıştırın. Ayrıca, meta veri veritabanının bulunduğu Azure SQL veritabanı sunucusu için mevcut kimlik bilgilerini sağlayın. (Bir meta veri veritabanı oluşturduysanız, bu veritabanı hub veritabanıyla aynı sunucuda bulunur.) **Tamam** ' ı seçin ve yapılandırmanın bitmesini bekleyin.

        ![Aracı anahtarını ve sunucu kimlik bilgilerini girin](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Bir güvenlik duvarı hatası alırsanız, SQL Server bilgisayardan gelen trafiğe izin vermek için Azure 'da bir güvenlik duvarı kuralı oluşturun. Kuralı portalda veya SQL Server Management Studio (SSMS) içinde el ile oluşturabilirsiniz. SSMS 'de, adını < hub_database_name >. Database. Windows. net olarak girerek Azure 'daki hub veritabanına bağlanın.

    1. SQL Server bir veritabanını aracıya kaydetmek için **Kaydet** ' i seçin. **SQL Server yapılandırma** iletişim kutusu açılır.

        ![SQL Server veritabanı ekleme ve yapılandırma](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. **SQL Server yapılandırma** iletişim kutusunda, SQL Server kimlik doğrulaması veya Windows kimlik doğrulaması kullanarak bağlanmayı seçin. SQL Server kimlik doğrulaması ' nı seçerseniz, mevcut kimlik bilgilerini girin. SQL Server adı ve eşitlemek istediğiniz veritabanının adını girip ayarlarınızı test etmek için **Bağlantıyı Sına** ' yı seçin. Ardından **Kaydet** ' i seçin ve kayıtlı veritabanı listede görüntülenir.

        ![SQL Server veritabanı artık kayıtlı](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. İstemci Eşitleme Aracısı uygulamasını kapatın.

1. Portalda, **Şirket Içi yapılandırma** sayfasında **veritabanını Seç**' i seçin.

1. **Veritabanı Seç** sayfasında, **eşitleme üye adı** alanında, yeni eşitleme üyesi için bir ad girin. Bu ad, veritabanının kendisinin adından farklıdır. Listeden veritabanını seçin. **Eşitleme yönleri** alanında çift **yönlü eşitleme**' yi, **hub 'a**veya **hub 'dan**seçin.

    ![Şirket içi veritabanını seçin](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. **Veritabanı Seç** sayfasını kapatmak için **Tamam ' ı** seçin. Ardından, **Şirket Içi yapılandırma** sayfasını kapatmak için **Tamam** ' ı seçin ve yeni eşitleme üyesinin oluşturulması ve dağıtılması için bekleyin. Son olarak, **eşitleme üyelerini Seç** sayfasını kapatmak için **Tamam** ' ı seçin.

> [!NOTE]
> SQL Data Sync ve yerel aracısına bağlanmak için, Kullanıcı adınızı rol *DataSync_Executor*ekleyin. Veri eşitleme, bu rolü SQL Server örneğinde oluşturur.

## <a name="configure-sync-group"></a>Eşitleme grubunu yapılandır

Yeni eşitleme grubu üyeleri oluşturulup dağıtıldıktan sonra, **eşitleme grubunu yapılandırın (3. adım)** , **Yeni eşitleme grubu** sayfasında vurgulanır.

![Adım 3 ayarları](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. **Tablolar** sayfasında, eşitleme grubu üyeleri listesinden bir veritabanı seçin ve **şemayı Yenile**' yi seçin.

1. Listeden eşitlemek istediğiniz tabloları seçin. Varsayılan olarak, tüm sütunlar seçilidir, bu nedenle eşitlemek istemediğiniz sütunlar için onay kutusunu devre dışı bırakın. Birincil anahtar sütununu seçili bırakmayı unutmayın.

1. **Kaydet**’i seçin.

1. Varsayılan olarak, veritabanları zamanlanana veya el ile çalışmaya kadar eşitlenmez. El ile eşitleme çalıştırmak için Azure portal SQL veritabanınıza gidin, **diğer veritabanlarına Eşitle**' yi seçin ve eşitleme grubunu seçin. **Veri eşitleme** sayfası açılır. **Eşitle**’yi seçin.

    ![El ile eşitleme](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>SSS

**Veri eşitleme, verilerimi ne sıklıkta eşitler?**

Eşitlemeler arasındaki en az süre beş dakikadır.

**SQL Data Sync tamamen tablo oluşturur mi?**

Hedef veritabanında eşitleme şeması tabloları eksikse SQL Data Sync seçtiğiniz sütunlarla o tabloları oluşturur. Bununla birlikte, bu, aşağıdaki nedenlerden dolayı tam aslına uygunluk şemasına neden olmaz:

- Hedef tabloda yalnızca seçtiğiniz sütunlar oluşturulmuştur. Seçilmeyen sütunlar yoksayılmıştır.
- Hedef tabloda yalnızca seçili sütun dizinleri oluşturulmuştur. Seçili olmayan sütunların dizinleri yoksayılmıştır.
- XML türü sütunlarında dizinler oluşturulmaz.
- DENETIM kısıtlamaları oluşturulmaz.
- Kaynak tablolardaki Tetikleyiciler oluşturulmaz.
- Görünümler ve saklı yordamlar oluşturulmaz.

Bu sınırlamalar nedeniyle aşağıdakileri öneririz:

- Üretim ortamları için tam doğruluk şemasını kendiniz oluşturun.
- Hizmetle denemeler yaparken otomatik sağlama özelliğini kullanın.

**Neden oluşturamadığım tabloları görüyorum?**

Veri eşitleme, değişiklik izleme için veritabanında ek tablolar oluşturur. Bu veya veri eşitleme, çalışmayı silme işlemini silmez.

**Bir eşitlemeden sonra veri dönüştürüme Gent mı?**

Olmayabilir. Bir hub ve üç bağlı bileşen (A, B ve C) içeren bir eşitleme grubu alıp, eşitlemelerin hub olduğu, hub 'Dan B 'ye ve hub 'a Merkez 'e Veritabanında bir eşitleme *sonrasında* bir değişiklik yapılırsa, bu değişiklik sonraki eşitleme görevine kadar veritabanı B 'ye veya C veritabanına yazılmaz.

**Nasıl yaparım?, şema değişiklikleri bir eşitleme grubuna mı alınır?**

Tüm şema değişikliklerini el ile yapın ve yayın.

1. Şema değişikliklerini hub 'a ve tüm eşitleme üyelerine çoğaltın.
1. Eşitleme şemasını güncelleştirin.

Yeni tablo ve sütun eklemek için:

Yeni tablolar ve sütunlar geçerli eşitlemeyi etkilemez ve veri eşitleme, eşitleme şemasına eklenene kadar onları yoksayar. Yeni veritabanı nesneleri eklerken sırayı izleyin:

1. Hub 'a ve tüm eşitleme üyelerine yeni tablo veya sütun ekleyin.
1. Eşitleme şemasına yeni tablolar veya sütunlar ekleyin.
1. Yeni tablolara ve sütunlara değer eklemeye başlayın.

Bir sütunun veri türünü değiştirmek için:

Varolan bir sütunun veri türünü değiştirdiğinizde, yeni değerler eşitleme şemasında tanımlanan özgün veri türüne uygun olduğu sürece veri eşitleme çalışmaya devam eder. Örneğin, kaynak veritabanındaki türü **int** 'ten **bigint**'e değiştirirseniz, veri eşitleme, **int** veri türü için çok büyük bir değer ekleyene kadar çalışmaya devam eder. Değişikliği tamamladıktan sonra, şema değişikliğini hub 'a ve tüm eşitleme üyelerine çoğaltın, ardından eşitleme şemasını güncelleştirin.

**Veri eşitleme ile bir veritabanını nasıl dışarı ve dışarı aktarabilirim?**

Bir veritabanını bir *. bacpac* dosyası olarak dışa aktarıp bir veritabanı oluşturmak için dosyayı içeri aktardıktan sonra, yeni veritabanında veri eşitlemesini kullanmak için aşağıdakileri yapın:

1. [Bu betiği](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql)kullanarak, veri eşitleme nesnelerini ve yeni veritabanında ek tabloları temizleyin. Betik tüm gerekli veri eşitleme nesnelerini veritabanından siler.
1. Yeni veritabanıyla birlikte eşitleme grubunu yeniden oluşturun. Artık eski eşitleme grubuna ihtiyacınız yoksa, silin.

**İstemci aracısında bilgileri nereden bulabilirim?**

İstemci Aracısı hakkında sık sorulan sorular için bkz. [Aracı SSS](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Sonraki adımlar

Tebrikler. Hem SQL veritabanı örneği hem de bir SQL Server veritabanı içeren bir eşitleme grubu oluşturdunuz.

SQL Data Sync hakkında daha fazla bilgi için bkz.:

- [Azure SQL Data Sync için veri eşitleme Aracısı](sql-database-data-sync-agent.md)
- [En iyi uygulamalar](sql-database-best-practices-data-sync.md) ve [Azure SQL Data Sync sorunları nasıl giderilir](sql-database-troubleshoot-data-sync.md)
- [Azure Izleyici günlükleriyle SQL Data Sync izleme](sql-database-sync-monitor-oms.md)
- [Transact-SQL veya PowerShell ile eşitleme şemasını güncelleştirme](sql-database-update-sync-schema.md) [](scripts/sql-database-sync-update-schema.md)

SQL Veritabanı hakkında daha fazla bilgi için bkz.:

- [SQL Veritabanı'na Genel Bakış](sql-database-technical-overview.md)
- [Veritabanı Yaşam Döngüsü Yönetimi](https://msdn.microsoft.com/library/jj907294.aspx)
