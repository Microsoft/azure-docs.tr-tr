---
title: SQL Data Sync’i ayarlama
description: Bu öğreticide Azure için SQL Data Sync ayarlama gösterilmektedir
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 77073d21f982e82e567e517b7d9eca061cb91859
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812962"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Öğretici: Azure SQL veritabanı ve SQL Server veritabanları arasında SQL Data Sync ayarlama
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu öğreticide, hem Azure SQL veritabanı hem de SQL Server örnekleri içeren bir eşitleme grubu oluşturarak SQL Data Sync ayarlamayı öğreneceksiniz. Eşitleme grubu özel olarak yapılandırılır ve ayarladığınız zamanlamaya göre eşitlenir.

Bu öğreticide, SQL veritabanı ve SQL Server ile ilgili en az bir önceki deneyim olduğunu varsaymaktadır.

SQL Data Sync genel bir bakış için bkz. [SQL Data Sync ile bulut ve şirket içi veritabanları arasında verileri eşitleme](sql-data-sync-data-sql-server-sql-database.md).

SQL Data Sync nasıl yapılandırılacağı hakkında PowerShell örnekleri için bkz. [SQL veritabanı 'nda veritabanları arasında](scripts/sql-data-sync-sync-data-between-sql-databases.md) veya [Azure SQL veritabanı 'nda](scripts/sql-data-sync-sync-data-between-azure-onprem.md) veritabanları arasında eşitleme ve SQL Server

> [!IMPORTANT]
> SQL Data Sync Şu anda Azure SQL yönetilen **örneğini desteklemez.**

## <a name="create-sync-group"></a>Eşitleme grubu oluştur

1. SQL veritabanı 'nda veritabanınızı bulmak için [Azure Portal](https://portal.azure.com) gidin. **SQL veritabanlarını** arayın ve seçin.

    ![Veritabanları ara, Microsoft Azure portal](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Veri eşitleme için merkez veritabanı olarak kullanmak istediğiniz veritabanını seçin.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/select-sql-database.png" alt-text = "Select from the database list, Microsoft Azure portal":::

    > [!NOTE]
    > Merkez veritabanı, bir eşitleme grubunun birden çok veritabanı uç noktasına sahip olduğu bir eşitleme topolojisinin merkezi uç noktasıdır. Eşitleme grubundaki uç noktalara sahip diğer tüm üye veritabanları, hub veritabanıyla eşitlenir.

1. Seçili veritabanı için **SQL veritabanı** menüsünde **diğer veritabanlarına Eşitle**' yi seçin.

    :::image type="content" source="./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png" alt-text = "Sync to other databases, Microsoft Azure portal":::

1. **Diğer veritabanlarına Eşitle** sayfasında, **Yeni eşitleme grubu**' nu seçin. **Yeni eşitleme grubu** sayfası, **eşitleme grubu oluştur** ile açılır.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/create-sync-group.png" alt-text = "Set up new sync group with private link":::

   **Veri eşitleme grubu oluştur** sayfasında, aşağıdaki ayarları değiştirin:

   | Ayar                        | Açıklama |
   | ------------------------------ | ------------------------------------------------- |
   | **Eşitleme Grubu Adı** | Yeni eşitleme grubu için bir ad girin. Bu ad, veritabanının kendisinin adından farklıdır. |
   | **Meta veri veritabanını Eşitle** | Veritabanı oluşturmayı seçin (önerilir) veya var olan bir veritabanını kullanın.<br/><br/>**Yeni veritabanı**' nı seçerseniz **Yeni veritabanı oluştur** ' u seçin. Ardından **SQL veritabanı** sayfasında, yeni veritabanını adlandırın ve yapılandırın ve **Tamam**' ı seçin.<br/><br/>**Varolan veritabanını kullan**' ı seçerseniz, listeden veritabanını seçin. |
   | **Otomatik eşitleme** | **Açık** veya **kapalı** seçeneğini belirleyin.<br/><br/>**Açık**' ı seçerseniz, bir sayı girin ve **eşitleme sıklığı** bölümünde **saniye**, **dakika**, **saat** veya **gün** ' yı seçin.<br/> İlk eşitleme, seçilen Aralık süresi yapılandırmanın kaydedildiği zamandan geçtiğinde başlar.|
   | **Çakışma çözümleme** | **Hub Win** veya **member WIN**' i seçin.<br/><br/>**Merkez kazanma** , çakışmaların ne zaman meydana geldiğini, Merkez veritabanındaki verileri üye veritabanındaki çakışan verilerin üzerine yazar.<br/><br/>**Üye kazanma** , çakışmaların ne zaman meydana geldiğini, üye veritabanındaki verileri Merkez veritabanındaki çakışan verilerin üzerine yazar. |
   | **Özel bağlantı kullan** | Eşitleme hizmeti ve hub veritabanı arasında güvenli bir bağlantı kurmak için hizmet tarafından yönetilen özel uç nokta seçin. |

   > [!NOTE]
   > Microsoft, **eşitleme meta verileri veritabanı** olarak kullanılmak üzere yeni, boş bir veritabanı oluşturmayı önerir. Veri eşitleme, bu veritabanında tabloları oluşturur ve sık sık iş yükü çalıştırır. Bu veritabanı, seçilen bir bölgedeki ve abonelikteki tüm eşitleme grupları için **eşitleme meta verileri veritabanı** olarak paylaşılır. Bölgedeki tüm eşitleme gruplarını ve eşitleme aracılarını kaldırmadan veritabanını veya adını değiştiremezsiniz. Ayrıca, esnek bir işler veritabanı SQL Data Sync meta veri veritabanı olarak kullanılamaz ve tam tersi de geçerlidir.  

   **Tamam** ' ı seçin ve eşitleme grubunun oluşturulmasını ve dağıtılmasını bekleyin.
   
1. **Yeni eşitleme grubu** sayfasında **özel bağlantı kullan**' ı seçtiyseniz, Özel uç nokta bağlantısını onaylamanız gerekir. Bilgi iletisindeki bağlantı sizi, bağlantıyı onaylayabileceğiniz özel uç nokta bağlantıları deneyimine götürür. 

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/approve-private-link-update.png" alt-text = "Approve private link":::
   
   > [!NOTE]
   > Eşitleniyor, onaylandı ve devre dışı bırakmak için Syng grubu ve eşitleme üyeleri için özel bağlantılar. 

## <a name="add-sync-members"></a>Eşitleme üyeleri Ekle

Yeni eşitleme grubu oluşturulup dağıtıldıktan sonra, eşitleme grubunu açın ve **veritabanlarını** Eşitle sayfasına erişin.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/add-sync-members.png" alt-text = "Select sync members":::
   
   > [!NOTE]
   > Hub veritabanınıza Kullanıcı adını ve parolayı güncelleştirmek veya eklemek için, **eşitleme üyelerini seçin** sayfasında **merkez veritabanı** bölümüne gidin. 

### <a name="to-add-a-database-in-azure-sql-database"></a>Azure SQL veritabanı 'na bir veritabanı eklemek için

**Eşitleme üyelerini Seç** bölümünde, **Azure veritabanı Ekle**' yi seçerek isteğe bağlı olarak, Azure SQL veritabanı 'nda bir veritabanını eşitleme grubuna ekleyin. **Azure veritabanını Yapılandır** sayfası açılır.
  
   :::image type="content" source="./media/sql-data-sync-sql-server-configure/step-two-configure.png" alt-text = "Add a database to the sync group":::
   
  **Azure SQL veritabanını Yapılandır** sayfasında, aşağıdaki ayarları değiştirin:

  | Ayar                       | Açıklama |
  | ----------------------------- | ------------------------------------------------- |
  | **Eşitleme üyesi adı** | Yeni eşitleme üyesi için bir ad sağlayın. Bu ad, veritabanı adından farklıdır. |
  | **Abonelik** | Faturalama amacıyla ilişkili Azure aboneliğini seçin. |
  | **Azure SQL Sunucusu** | Mevcut sunucuyu seçin. |
  | **Azure SQL Veritabanı** | SQL veritabanı 'nda var olan veritabanını seçin. |
  | **Eşitleme yönleri** | **Çift yönlü eşitlemeyi**, **hub 'A** veya **hub 'dan** seçin. |
  | **Kullanıcı adı** ve **parola** | Üye veritabanının bulunduğu sunucu için mevcut kimlik bilgilerini girin. Bu bölüme *Yeni* kimlik bilgileri girmeyin. |
  | **Özel bağlantı kullan** | Eşitleme hizmeti ile üye veritabanı arasında güvenli bir bağlantı kurmak için hizmet tarafından yönetilen özel uç nokta seçin. |

  **Tamam** ' ı seçin ve yeni eşitleme üyesinin oluşturulması ve dağıtılması için bekleyin.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>SQL Server veritabanı eklemek için

**Üye veritabanı** bölümünde, isteğe bağlı olarak, **bir şirket içi veritabanı Ekle**' yi seçerek eşitleme grubuna bir SQL Server veritabanı ekleyin. **Şirket Içi yapılandırma** sayfası açılır ve aşağıdaki işlemleri yapabilirsiniz:

1. **Eşitleme Aracısı ağ geçidini Seç**' i seçin. **Eşitleme aracısını seçin** sayfası açılır.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo-agent.png" alt-text = "Creating a sync agent":::

1. **Eşitleme aracısını seçin** sayfasında, var olan bir aracıyı mi kullanacağınızı yoksa bir aracı mı oluşturulacağını seçin.

   **Mevcut aracılar**' ı seçerseniz, listeden var olan aracıyı seçin.

   **Yeni bir aracı oluştur**' u seçerseniz, aşağıdaki işlemleri yapın:

   1. Veri eşitleme aracısını, belirtilen bağlantıdan indirin ve SQL Server bulunduğu bilgisayara yükleyin. Aracıyı doğrudan [Azure SQL Data Sync aracısından](https://www.microsoft.com/download/details.aspx?id=27693)de indirebilirsiniz.

      > [!IMPORTANT]
      > İstemci aracısının sunucusuyla iletişim kurmasına izin vermek için güvenlik duvarında giden TCP bağlantı noktası 1433 ' i açmanız gerekir.

   1. Aracı için bir ad girin.

   1. **Anahtar oluştur ve oluştur** ' u seçin ve aracı anahtarını panoya kopyalayın.

   1. **Eşitleme aracısını Seç** sayfasını kapatmak için **Tamam ' ı** seçin.

1. SQL Server bilgisayarda, İstemci Eşitleme Aracısı uygulamasını bulun ve çalıştırın.

   ![Veri eşitleme istemci Aracısı uygulaması](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. Eşitleme Aracısı uygulamasında, **Aracı anahtarını gönder**' i seçin. **Eşitleme meta verileri veritabanı yapılandırması** iletişim kutusu açılır.

    1. **Eşitleme meta verileri veritabanı yapılandırması** iletişim kutusunda, Azure Portal kopyaladığınız aracı anahtarını yapıştırın. Ayrıca, meta veri veritabanının bulunduğu sunucu için mevcut kimlik bilgilerini sağlayın. (Bir meta veri veritabanı oluşturduysanız, bu veritabanı hub veritabanıyla aynı sunucuda bulunur.) **Tamam** ' ı seçin ve yapılandırmanın bitmesini bekleyin.

        ![Aracı anahtarını ve sunucu kimlik bilgilerini girin](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Bir güvenlik duvarı hatası alırsanız, SQL Server bilgisayardan gelen trafiğe izin vermek için Azure 'da bir güvenlik duvarı kuralı oluşturun. Kuralı portalda veya SQL Server Management Studio (SSMS) içinde el ile oluşturabilirsiniz. SSMS 'de, adını <hub_database_name>. database.windows.net olarak girerek Azure 'daki hub veritabanına bağlanın.

    1. SQL Server bir veritabanını aracıya kaydetmek için **Kaydet** ' i seçin. **SQL Server yapılandırma** iletişim kutusu açılır.

        ![SQL Server veritabanı ekleme ve yapılandırma](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. **SQL Server yapılandırma** iletişim kutusunda, SQL Server kimlik doğrulaması veya Windows kimlik doğrulaması kullanarak bağlanmayı seçin. SQL Server kimlik doğrulaması ' nı seçerseniz, mevcut kimlik bilgilerini girin. SQL Server adı ve eşitlemek istediğiniz veritabanının adını girip ayarlarınızı test etmek için **Bağlantıyı Sına** ' yı seçin. Ardından **Kaydet** ' i seçin ve kayıtlı veritabanı listede görüntülenir.

        ![SQL Server veritabanı artık kayıtlı](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. İstemci Eşitleme Aracısı uygulamasını kapatın.

1. Portalda, **Şirket Içi yapılandırma** sayfasında **veritabanını Seç**' i seçin.

1. **Veritabanı Seç** sayfasında, **eşitleme üye adı** alanında, yeni eşitleme üyesi için bir ad girin. Bu ad, veritabanının kendisinin adından farklıdır. Listeden veritabanını seçin. **Eşitleme yönleri** alanında çift **yönlü eşitleme**' yi, **hub 'a** veya **hub 'dan** seçin.

    ![Şirket içi veritabanını seçin](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. **Veritabanı Seç** sayfasını kapatmak için **Tamam ' ı** seçin. Ardından, **Şirket Içi yapılandırma** sayfasını kapatmak için **Tamam** ' ı seçin ve yeni eşitleme üyesinin oluşturulması ve dağıtılması için bekleyin. Son olarak, **eşitleme üyelerini Seç** sayfasını kapatmak için **Tamam** ' ı seçin.

> [!NOTE]
> SQL Data Sync ve yerel aracısına bağlanmak için, Kullanıcı adınızı rol *DataSync_Executor* ekleyin. Veri eşitleme, bu rolü SQL Server örneğinde oluşturur.

## <a name="configure-sync-group"></a>Eşitleme grubunu yapılandır

Yeni eşitleme grubu üyeleri oluşturulup dağıtıldıktan sonra **veritabanı eşitleme grubu** sayfasındaki **Tablolar** bölümüne gidin.

![Adım 3 ayarları](./media/sql-data-sync-sql-server-configure/configure-sync-group.png)

1. **Tablolar** sayfasında, eşitleme grubu üyeleri listesinden bir veritabanı seçin ve **şemayı Yenile**' yi seçin. Lütfen yenileme şemasında birkaç dakika gecikme süresi beklediğinden, özel bağlantı kullanılıyorsa gecikme birkaç dakika daha uzun olabilir.

1. Listeden eşitlemek istediğiniz tabloları seçin. Varsayılan olarak, tüm sütunlar seçilidir, bu nedenle eşitlemek istemediğiniz sütunlar için onay kutusunu devre dışı bırakın. Birincil anahtar sütununu seçili bırakmayı unutmayın.

1. **Kaydet**’i seçin.

1. Varsayılan olarak, veritabanları zamanlanana veya el ile çalışmaya kadar eşitlenmez. El ile eşitleme çalıştırmak için Azure portal SQL veritabanı 'nda veritabanınıza gidin, **diğer veritabanlarına Eşitle**' yi seçin ve eşitleme grubunu seçin. **Veri eşitleme** sayfası açılır. **Eşitle**’yi seçin.

    ![El ile eşitleme](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>SSS

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

Gerekli değildir. Bir hub ve üç bağlı bileşen (A, B ve C) içeren bir eşitleme grubu alıp, eşitlemelerin hub olduğu, hub 'Dan B 'ye ve hub 'a Merkez 'e Veritabanında bir eşitleme *sonrasında* bir değişiklik yapılırsa, bu değişiklik sonraki eşitleme görevine kadar veritabanı B 'ye veya C veritabanına yazılmaz.

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

İstemci Aracısı hakkında sık sorulan sorular için bkz. [Aracı SSS](sql-data-sync-agent-overview.md#agent-faq).

**Bağlantıyı kullanmaya başlayabilmeniz için bağlantıyı el ile onaylamanız gerekiyor mu?**

Evet, eşitleme grubu dağıtımı sırasında veya PowerShell kullanarak, Azure portal özel uç nokta bağlantıları sayfasında, hizmet yönetilen özel uç noktasını el ile onaylamanız gerekir.

**Eşitleme işi Azure veritabanımı sağlanırken neden bir güvenlik duvarı hatası alıyorum?**

Bu durum, Azure kaynaklarının sunucunuza erişmesine izin verilmediği için meydana gelebilir. Azure veritabanında güvenlik duvarının "Azure hizmetleri ve kaynaklarına bu sunucuya erişmesine Izin ver" ayarı "Evet" olarak ayarlanmış olduğundan emin olun.


## <a name="next-steps"></a>Sonraki adımlar

Tebrikler. Hem SQL veritabanı örneği hem de bir SQL Server veritabanı içeren bir eşitleme grubu oluşturdunuz.

SQL Data Sync hakkında daha fazla bilgi için bkz.:

- [Azure SQL Data Sync için veri eşitleme Aracısı](sql-data-sync-agent-overview.md)
- [En iyi uygulamalar](sql-data-sync-best-practices.md) ve [Azure SQL Data Sync sorunları nasıl giderilir](sql-data-sync-troubleshoot.md)
- [Azure Izleyici günlükleriyle SQL Data Sync izleme](./monitor-tune-overview.md)
- [Transact-SQL veya PowerShell ile eşitleme şemasını güncelleştirme](sql-data-sync-update-sync-schema.md) [](scripts/update-sync-schema-in-sync-group.md)

SQL Veritabanı hakkında daha fazla bilgi için bkz.:

- [SQL Veritabanı'na Genel Bakış](sql-database-paas-overview.md)
- [Veritabanı Yaşam Döngüsü Yönetimi](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
