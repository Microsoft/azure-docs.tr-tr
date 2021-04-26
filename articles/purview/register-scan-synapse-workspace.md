---
title: Azure SYNAPSE çalışma alanlarını tarama
description: Azure purview veri kataloğunuzda bir Synapse çalışma alanını taramayı öğrenin.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/31/2021
ms.openlocfilehash: 230894b8e474c8d230322fb1e240f0317512d036
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031449"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>Azure SYNAPSE çalışma alanlarını kaydetme ve tarama

Bu makalede, Azure SYNAPSE çalışma alanını purview 'a kaydetme ve üzerinde tarama ayarlama özetlenmektedir.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Azure SYNAPSE çalışma alanı taramaları, kendilerine özel ve sunucusuz SQL veritabanları için meta veri ve şema yakalamayı destekler. Ayrıca, verileri sistem ve özel sınıflandırma kurallarına göre otomatik olarak sınıflandırır.

## <a name="prerequisites"></a>Önkoşullar

- Veri kaynaklarını kaydetmeden önce bir Azure purview hesabı oluşturun. Bir purview hesabı oluşturma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure purview hesabı oluşturma](create-catalog-portal.md).
- Azure purview veri kaynağı Yöneticisi olmanız gerekir
- Aşağıdaki bölümlerde açıklandığı şekilde kimlik doğrulamasını ayarlama

### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>Bir Synapse çalışma alanı altındaki adanmış SQL veritabanı kaynaklarını numaralandırmak için kimlik doğrulaması ayarlama

1. Azure portal, SYNAPSE çalışma alanının içinde bulunduğu **kaynak grubuna** veya **aboneliğe** gidin.  
1. Sol gezinti menüsünden **Access Control (IAM)** seçeneğini belirleyin   
1. Kaynak grubuna veya aboneliğe bir rol eklemek için sahip veya Kullanıcı erişimi yöneticisi olmanız gerekir. *+ Ekle* düğmesini seçin. 
1. **Okuyucu** rolünü ayarlayın ve giriş kutusunu seçin altında Azure purview hesabınızın adını (MSI 'sini temsil eder) girin. Rol atamasını son vermek için *Kaydet* ' e tıklayın.
1. Kaynak grubunda veya SYNAPSE çalışma alanının bulunduğu abonelikte Azure purview MSI için **Depolama Blobu veri okuyucu** rolü eklemek için yukarıdaki 2 ile 4 arasındaki adımları izleyin.

### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>SYNAPSE çalışma alanı altındaki sunucusuz SQL veritabanı kaynaklarını numaralandırmak için kimlik doğrulaması ayarlama

> [!NOTE]
> Bu komutları çalıştırmak için çalışma alanında bir **SYNAPSE Yöneticisi** olmanız gerekir. SYNAPSE [izinleri hakkında](../synapse-analytics/security/how-to-set-up-access-control.md)daha fazla bilgi edinin.

1. SYNAPSE çalışma alanınıza gidin
1. **Veri** bölümüne ve SUNUCUSUZ SQL veritabanlarınızdan birine gidin
1. Üç nokta simgesine tıklayın ve yeni bir SQL betiği başlatın
1. SQL betiğinizin aşağıdaki komutunu çalıştırarak sunucusuz SQL veritabanlarında Azure purview (hesap adı ile temsil edilen) hesap **MSI ' yı** ekleyin:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

### <a name="setting-up-authentication-to-scan-resources-under-a-synapse-workspace"></a>SYNAPSE çalışma alanı altındaki kaynakları taramak için kimlik doğrulaması ayarlama

Azure SYNAPSE kaynağı için kimlik doğrulaması kurmanın üç yolu vardır:

- Yönetilen Kimlik
- Hizmet Sorumlusu
 
#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>Adanmış SQL veritabanları için yönetilen kimliği kullanma

1. **SYNAPSE çalışma alanınıza** gidin
1. **Veri** bölümüne ve SUNUCUSUZ SQL veritabanlarınızdan birine gidin
1. Üç nokta simgesine tıklayın ve yeni bir SQL betiği başlatın
1. SQL betiğinizin aşağıdaki komutunu çalıştırarak adanmış SQL veritabanı 'nda Azure purview (hesap adı ile temsil edilen) hesap MSI 'sini **db_owner** olarak ekleyin:

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [PurviewAccountName]
    GO
    ```
#### <a name="using-managed-identity-for-serverless-sql-databases"></a>Sunucusuz SQL veritabanları için yönetilen kimlik kullanma

1. **SYNAPSE çalışma alanınıza** gidin
1. **Veri** bölümüne ve SUNUCUSUZ SQL veritabanlarınızdan birine gidin
1. Üç nokta simgesine tıklayın ve yeni bir SQL betiği başlatın
1. SQL betiğinizin aşağıdaki komutunu çalıştırarak sunucusuz SQL veritabanlarında Azure purview (hesap adı ile temsil edilen) hesap **MSI ' yı** ekleyin:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>Adanmış SQL veritabanları için hizmet sorumlusu kullanma

> [!NOTE]
> Aşağıdaki [yönergeleri izleyerek](manage-credentials.md)önce hizmet sorumlusu türünde yeni bir **kimlik bilgisi** ayarlamanız gerekir.

1. **SYNAPSE çalışma alanınıza** gidin
1. **Veri** bölümüne ve SUNUCUSUZ SQL veritabanlarınızdan birine gidin
1. Üç nokta simgesine tıklayın ve yeni bir SQL betiği başlatın
1. SQL betiğinizdeki aşağıdaki komutu çalıştırarak, **hizmet sorumlusu kimliğini** adanmış sql veritabanına **db_owner** olarak ekleyin:

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [ServicePrincipalID]
    GO
    ```

#### <a name="using-service-principal-for-serverless-sql-databases"></a>Sunucusuz SQL veritabanları için hizmet sorumlusu kullanma

1. **SYNAPSE çalışma alanınıza** gidin
1. **Veri** bölümüne ve SUNUCUSUZ SQL veritabanlarınızdan birine gidin
1. Üç nokta simgesine tıklayın ve yeni bir SQL betiği başlatın
1. SQL betiğinizin aşağıdaki komutunu çalıştırarak sunucusuz SQL veritabanlarında Azure purview (hesap adı ile temsil edilen) hesap **MSI ' yı** ekleyin:
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [ServicePrincipalID];
    ```

> [!NOTE]
> Kaydolmak ve taramak istediğiniz SYNAPSE çalışma alanınızdaki her adanmış SQL veritabanında kimlik doğrulamasını ayarlamanız gerekir. Sunucusuz SQL veritabanı için yukarıda bahsedilen izinler, çalışma alanınızdaki tüm bunlar için geçerlidir, yani yalnızca bir kez çalıştırmanız gerekir.
    
## <a name="register-an-azure-synapse-source"></a>Azure SYNAPSE kaynağını kaydetme

Yeni bir Azure SYNAPSE kaynağını veri kataloğunuza kaydetmek için aşağıdakileri yapın:

1. Purview hesabınıza gidin
1. Sol gezinti bölmesinde **kaynakları** seçin
1. **Kaydol** ' u seçin
1. **Kayıt kaynakları** üzerinde **Azure SYNAPSE Analytics (birden çok)** seçeneğini belirleyin.
1. **Devam**'ı seçin

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Azure SYNAPSE kaynağını ayarlama":::

**Kaynakları Kaydet (Azure SYNAPSE Analytics)** ekranında şunları yapın:

1. Veri kaynağının katalogda listelenecek bir **ad** girin.
1. İsteğe bağlı olarak filtrelemek için bir **abonelik** seçin.
1. Açılan listeden **bir Synapse çalışma alanı adı seçin** . SQL uç noktaları, çalışma alanı seçiminize göre otomatik olarak doldurulur. 
1. Bir **koleksiyon** seçin veya yeni bir tane oluşturun (isteğe bağlı)
1. Veri kaynağının kaydedileceği **son**

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Azure SYNAPSE Source için bilgi doldur":::

## <a name="creating-and-running-a-scan"></a>Tarama oluşturma ve çalıştırma

Yeni bir tarama oluşturup çalıştırmak için aşağıdakileri yapın:

1. **Kaynaklar** bölümüne gidin.

1. Kaydettiğiniz veri kaynağını seçin.

1. Ayrıntıları görüntüle ' ye tıklayın ve **+ Yeni Tara** ' yı seçin ya da kaynak kutucuğunda hızlı eylem Tara simgesini kullanın

1. *Adı* girin ve bu kaynak içinde taramak istediğiniz tüm kaynak türlerini seçin. **SQL veritabanı** , şu anda bir Synapse çalışma alanı içinde destekduğumuz tek türdür.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Azure SYNAPSE kaynak taraması":::

1. Veri kaynağınızdaki kaynaklara bağlanacak **kimlik bilgisini** seçin. 
  
1. Her bir tür içinde, tüm kaynakları veya bunların bir alt kümesini ada göre taramayı seçebilirsiniz.
1.  Devam etmek için **devam** ' a tıklayın. 

1.  Azure SYNAPSE SQL türünde bir **tarama kuralı kümesi** seçin. Ayrıca, tarama kuralı kümelerini satır içi olarak da oluşturabilirsiniz.

1. Tarama tetikleyiciyi seçin. Bunu **haftalık/aylık** veya **bir kez** çalışacak şekilde planaktarabilirsiniz

1. Taramayı gözden geçirin ve ayarlamayı tamamladıktan sonra Kaydet ' i seçin   

## <a name="viewing-your-scans-and-scan-runs"></a>Taramalarınızı ve tarama çalıştırmalarını görüntüleme

1. Kaynaklar bölümünün altındaki kutucukta **Ayrıntıları görüntüle** ' ye tıklayarak kaynak ayrıntılarını görüntüleyin. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Azure SYNAPSE kaynak ayrıntıları"::: 

1. **Tarama ayrıntıları** sayfasına giderek tarama çalıştırma ayrıntılarını görüntüleyin.
    1. *Durum çubuğu* , alt kaynakların çalışma durumu hakkında kısa bir özettir. Çalışma alanı düzeyi taramasında görüntülenecektir.
    1. Yeşil, kırmızı, başarısız anlamına gelir. Gri, taramanın hala devam ettiği anlamına gelir
    1. Daha ayrıntılı bilgi görüntülemek için her taramaya tıklayabilirsiniz

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Azure SYNAPSE tarama ayrıntıları" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. Kaynak Ayrıntıları sayfasının en altında başarısız olan en son tarama çalışmalarının özetini görüntüleyin. Ayrıca, Bu çalıştırmaların ilgili daha ayrıntılı ayrıntılarını görüntülemek için de tıklayabilirsiniz.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Taramalarınızı yönetme-düzenleme, silme veya iptal etme
Bir taramayı yönetmek veya silmek için aşağıdakileri yapın:

- Yönetim merkezine gidin. Kaynaklar ve Tarama bölümünde veri kaynakları ' nı seçin ve ardından istediğiniz veri kaynağını seçin.

- Yönetmek istediğiniz taramayı seçin. Taramayı Düzenle seçeneğini belirleyerek düzenleyebilirsiniz.

- , Sil ' i seçerek taramayı silebilirsiniz.
- Bir tarama çalışıyorsa, bu işlemi de iptal edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure purview veri kataloğuna gözatın](how-to-browse-catalog.md)
- [Azure purview Veri Kataloğu 'Nda arama](how-to-search-catalog.md)   