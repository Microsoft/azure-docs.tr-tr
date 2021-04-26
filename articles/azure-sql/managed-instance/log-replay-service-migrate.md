---
title: Günlük yeniden yürütme hizmeti 'ni kullanarak veritabanlarını SQL yönetilen örneği 'ne geçirme
description: Günlük yeniden yürütme hizmeti 'ni kullanarak SQL Server veritabanlarını SQL yönetilen örneğine geçirmeyi öğrenin
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/31/2021
ms.openlocfilehash: 522f4ec2f28f9d8975a8a7a7b10e435f602af855
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484039"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Günlük yeniden yürütme hizmeti 'ni (Önizleme) kullanarak SQL Server veritabanlarını SQL yönetilen örneğine geçirme
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bu makalede, şu anda genel önizlemede olan günlük yeniden yürütme hizmeti 'ni (LRS) kullanarak SQL Server 2008-2019 ' dan Azure SQL yönetilen örneği 'ne veritabanı geçişini el ile yapılandırma açıklanmaktadır. LRS, SQL yönetilen örneği için etkinleştirilen ve SQL Server günlük gönderim teknolojisini temel alan bir bulut hizmetidir. 

[Azure veritabanı geçiş hizmeti](../../dms/tutorial-sql-server-to-managed-instance.md) ve LRS, aynı temel geçiş teknolojisini ve aynı API 'leri kullanır. LRS 'yi serbest bırakarak, şirket içi SQL Server ve SQL yönetilen örneği arasında karmaşık özel geçişleri ve karma mimariyi daha da etkinleştiriyoruz.

## <a name="when-to-use-log-replay-service"></a>Günlük yeniden yürütme hizmeti ne zaman kullanılır?

Geçiş için Azure veritabanı geçiş hizmeti 'ni kullandığınızda, SQL yönetilen örneği için veritabanı geçişlerini el ile oluşturmak ve düzenlemek üzere LRS 'yi doğrudan PowerShell, Azure CLı cmdlet 'leri veya API 'Ler ile kullanabilirsiniz. 

Aşağıdaki durumlarda LRS 'yi kullanmayı düşünebilirsiniz:
- Veritabanı geçiş projeniz için daha fazla denetime ihtiyacınız vardır.
- Cutover geçişi sırasında kapalı kalma süresi için çok az tolerans vardır.
- Veritabanı geçiş hizmeti yürütülebilir dosyası ortamınıza yüklenemez.
- Veritabanı geçiş hizmeti yürütülebilir dosyasının veritabanı yedeklerine dosya erişimi yok.
- Konak işletim sistemine erişim yok veya yönetici ayrıcalıkları yok.
- Ortamınızdaki ağ bağlantı noktalarını Azure 'a açamazsınız.
- Ağ daraltma veya ortamınızda ara sunucu engelleme sorunları.
- Yedeklemeler, seçeneği aracılığıyla doğrudan Azure Blob depolama alanına depolanır `TO URL` .
- Değişiklik yedeklemeleri kullanmanız gerekir.

> [!NOTE]
> Veritabanı geçiş hizmeti 'ni kullanarak SQL Server veritabanlarının SQL yönetilen örneğine geçirilmesini otomatikleştirmenizi öneririz. Bu hizmet, arka uçta aynı LRS bulut hizmetini, günlük gönderimi `NORECOVERY` modunda kullanır. Veritabanı geçiş hizmeti senaryolarınızı tam olarak desteklemediği zaman geçişleri düzenlemek için LRS 'yi el ile kullanmayı düşünün.

## <a name="how-it-works"></a>Nasıl çalışır?

Veritabanlarını buluta geçirmek için LRS kullanarak özel bir çözüm oluşturmak, bu bölümün ilerleyen kısımlarında bulunan diyagramda ve tabloda gösterildiği gibi birkaç düzenleme adımı gerektirir.

Geçiş, `CHECKSUM` etkin ve yedekleme dosyalarını Azure Blob depolamaya kopyalamak için SQL Server tam veritabanı yedeklemeleri yapmaktan oluşur. LRS, blob depolamadan SQL yönetilen örneğine yedekleme dosyalarını geri yüklemek için kullanılır. BLOB depolama, SQL Server ile SQL yönetilen örneği arasında ara depolama alanı.

LRS, tam yedekleme geri yüklendikten sonra eklenen tüm yeni farklar veya günlük yedeklemeleri için blob depolamayı izler. LRS daha sonra bu yeni dosyaları otomatik olarak geri yükler. SQL yönetilen örneği 'ne geri yüklenen yedekleme dosyalarının ilerlemesini izlemek için hizmetini kullanabilir ve gerekirse işlemi durdurabilirsiniz.

LRS, yedekleme dosyaları için belirli bir adlandırma kuralı gerektirmez. Blob depolamaya yerleştirilmiş tüm dosyaları tarar ve yedekleme zincirinin yalnızca dosya üstbilgilerini okumasını oluşturur. Veritabanları, geçiş işlemi sırasında "geri yükleme" durumundadır. Veritabanları, geçiş işlemi [](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) tamamlanana kadar okuma veya yazma için kullanılamaz. 

Çeşitli veritabanlarını geçiriyorsanız şunları yapmanız gerekir:
 
- Her bir veritabanı için yedeklemeleri blob depolamada ayrı bir klasöre yerleştirin.
- LRS 'yi her veritabanı için ayrı olarak başlatın.
- BLOB depolama klasörlerini ayırmak için farklı yollar belirtin. 

LRS 'yi *otomatik tamamlama* veya *sürekli* modda başlatabilirsiniz. Otomatik tamamlama modunda başlattığınızda, belirtilen yedekleme dosyalarının son geri yüklendiği zaman geçiş işlemi otomatik olarak tamamlanır. LRS 'yi sürekli modda başlattığınızda hizmet, eklenen yeni yedekleme dosyalarını sürekli olarak geri yükler ve geçiş işlemi yalnızca el ile tam geçişi üzerinde tamamlanır. 

Son günlük kuyruğu yedeklemesi alındıktan ve SQL yönetilen örneği 'nde geri yüklenmiş olarak gösterildikten sonra el ile kesmeniz önerilir. Son tam geçişi adımı, veritabanının çevrimiçi hale gelmesini ve SQL yönetilen örneği üzerinde okuma ve yazma kullanımı için kullanılabilir hale getirir.

LRS durdurulduktan sonra otomatik tamamlama aracılığıyla veya cutover aracılığıyla el ile, SQL yönetilen örneği üzerinde çevrimiçi hale getirilen bir veritabanı için geri yükleme işlemini sürdürülemez. Geçiş tamamlandıktan sonra otomatik tamamlama veya cutover aracılığıyla ek yedekleme dosyalarını geri yüklemek için veritabanını silmeniz gerekir. Ayrıca, LRS 'yi yeniden başlatarak yedekleme zincirinin tamamını sıfırdan geri yüklemeniz gerekir.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="SQL yönetilen örneği için günlük yeniden yürütme hizmeti düzenleme adımlarını açıklayan diyagram." border="false":::
    
| İşlem | Ayrıntılar |
| :----------------------------- | :------------------------- |
| **1. SQL Server veritabanı yedeklerini blob depolamaya kopyalayın**. | [AzCopy](../../storage/common/storage-use-azcopy-v10.md) veya [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)kullanarak SQL Server tam, değişiklik ve günlük yedeklemelerini bir BLOB depolama kapsayıcısına kopyalayın. <br /><br />Herhangi bir dosya adı kullanın. LRS 'ler belirli bir dosya adlandırma kuralı gerektirmez.<br /><br />Çeşitli veritabanlarını geçirirken her veritabanı için ayrı bir klasöre ihtiyacınız vardır. |
| **2. bulutta LRS 'Yi başlatın**. | Hizmeti bir cmdlet seçimi ile yeniden başlatabilirsiniz: PowerShell ([Start-azsqlınstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) veya Azure clı ([az_sql_midb_log_replay_start cmdlet 'leri](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Blob depolamada bir yedekleme klasörünü işaret eden her veritabanı için LRS 'yi ayrı olarak başlatın. <br /><br /> Hizmeti başlattıktan sonra, BLOB depolama kapsayıcısından yedeklemeler alınır ve bunları SQL yönetilen örneği 'ne geri yüklemeye başlar.<br /><br /> LRS 'yi sürekli modda başlattıysanız, başlangıçta karşıya yüklenen tüm yedeklemeler geri yüklendikten sonra hizmet, klasöre yüklenen tüm yeni dosyaları izleyebilir. Hizmet, günlük sıra numarası (LSN) zincirine göre günlükleri, durduruluncaya kadar sürekli olarak uygular. |
| **2,1. işlemin Ilerlemesini izleyin**. | Geri yükleme işleminin ilerlemesini bir cmdlet seçimi ile izleyebilirsiniz: PowerShell ([Get-azsqlınstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) veya Azure clı ([az_sql_midb_log_replay_show cmdlet 'leri](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2,2. gerekirse Işlemi durdurun**. | Geçiş işlemini durdurmanız gerekiyorsa, cmdlet seçiminiz vardır: PowerShell ([stop-azsqlınstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) veya Azure clı ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> İşlem durdurulduğunda SQL yönetilen örneği üzerinde geri yüklemekte olduğunuz veritabanı silinir. Bir işlemi durdurduktan sonra, bir veritabanı için LRS 'yi sürdürülemez. Geçiş işlemini sıfırdan yeniden başlatmanız gerekir. |
| **3. hazırsanız buluta kesin**. | Uygulamayı ve iş yükünü durdurun. Son günlük kuyruğu yedeklemesini gerçekleştirin ve Azure Blob depolama alanına yükleyin.<br /><br /> Bir LRS işlemini bir cmdlet seçimi ile başlatarak tam geçişi ' i doldurun `complete` : PowerShell ([tam-azsqlınstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) veya Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Bu işlem LRS 'yi durduracak ve SQL yönetilen örneği üzerinde okuma ve yazma kullanımı için veritabanının çevrimiçi duruma gelmesine neden olacak.<br /><br /> Uygulama bağlantı dizesinin SQL Server SQL yönetilen örneği 'ne yeniden işaret edin. Bu adımı uygulamanızda el ile bağlantı dizesi değişikliği aracılığıyla kendiniz veya otomatik olarak (örneğin, uygulamanızın bir özellikten veya bir veritabanından bağlantı dizesini okuyabilir) düzenleyebilmeniz gerekecektir. |

## <a name="requirements-for-getting-started"></a>Başlangıç gereksinimleri

### <a name="sql-server-side"></a>SQL Server tarafı
- SQL Server 2008-2019
- Veritabanlarının tam yedeklemesi (bir veya daha fazla dosya)
- Değişiklik yedekleme (bir veya birden çok dosya)
- Günlük yedeklemesi (bir işlem günlük dosyası için bölünmez)
- `CHECKSUM` yedeklemeler için etkinleştirildi (zorunlu)

### <a name="azure-side"></a>Azure tarafı
- PowerShell az. SQL Module Version 2.16.0 veya üzeri ( [Azure Cloud Shell](/azure/cloud-shell/)aracılığıyla[yüklenir](https://www.powershellgallery.com/packages/Az.Sql/) veya erişilir)
- Azure CLı sürüm 2.19.0 veya üzeri ([yüklü](/cli/azure/install-azure-cli))
- Sağlanan Azure Blob depolama kapsayıcısı
- BLOB depolama kapsayıcısı için oluşturulan okuma ve listeleme izinleriyle paylaşılan erişim imzası (SAS) güvenlik belirteci

### <a name="migration-of-multiple-databases"></a>Birden çok veritabanının geçirilmesi
Farklı veritabanları için yedekleme dosyalarını blob depolamada ayrı klasörlere yerleştirmeniz gerekir.

Blob depolamada uygun bir klasöre işaret ederek LRS 'yi her veritabanı için ayrı olarak başlatın. LRS, tek bir yönetilen örnek başına en fazla 100 eşzamanlı geri yükleme işlemini destekleyebilir.

### <a name="azure-rbac-permissions"></a>Azure RBAC izinleri
LRS 'yi belirtilen istemcilerle çalıştırmak için aşağıdaki Azure rollerinden biri gerekir:
- Abonelik Sahibi rolü
- [Yönetilen örnek katılımcısı](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) rolü
- Aşağıdaki izne sahip özel rol: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>En iyi uygulamalar

Aşağıdaki en iyi yöntemleri öneririz:
- Veritabanlarınızı SQL yönetilen örneği 'ne geçirilmeye hazırlandığını doğrulamak için [Data Migration Yardımcısı](/sql/dma/dma-overview) çalıştırın. 
- Tam ve değişiklik yedeklemelerini, tek bir dosya kullanmak yerine birden çok dosyaya ayırın.
- Yedekleme sıkıştırmasını etkinleştirin.
- Her zaman yayınlanan en son cmdlet 'lere güncelleştirildiği için betikleri çalıştırmak için Cloud Shell kullanın.
- LRS 'yi başlattıktan sonra geçişi 47 saat içinde tamamlamayı planlayın. Bu, sistem tarafından yönetilen yazılım düzeltme eklerinin yüklenmesini önleyen bir yetkisiz kullanım dönedir.

> [!IMPORTANT]
> - Geçiş işlemi bitene kadar LRS aracılığıyla geri yüklenen veritabanını kullanamazsınız. 
> - LRS, geçiş sırasında veritabanlarına salt okuma erişimini desteklemez.
> - Geçiş bittikten sonra, LRS geri yükleme işlemini sürdürmeyi desteklemediğinden geçiş işlemi sonlandırılır.

## <a name="steps-to-execute"></a>Yürütülecek adımlar

### <a name="make-backups-of-sql-server"></a>SQL Server yedeklemeleri yapın

SQL Server yedeklerini aşağıdaki seçeneklerden birini kullanarak yapabilirsiniz:

- Yerel disk depolama alanına yedekleme yapın ve ardından ortamınız BLOB depolama alanına doğrudan yedekleri kısıtlarsa Azure Blob depolama alanına dosya yükleyin.
- `TO URL`Ortamınız ve güvenlik yordamlarınız buna izin verirseniz T-SQL ' i seçeneğiyle doğrudan blob depolamaya yedekleme. 

Günlük yedeklemelerine izin vermek için tam kurtarma moduna geçirmek istediğiniz veritabanlarını ayarlayın.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Yerel depolamada veritabanınızın tam, değişiklik ve günlük yedeklemelerini el ile yapmak için aşağıdaki örnek T-SQL betiklerini kullanın. `CHECKSUM`LRS için zorunlu olduğundan, seçeneğinin etkinleştirildiğinden emin olun.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Azure Blob depolama, SQL Server ile SQL yönetilen örneği arasındaki yedekleme dosyaları için ara depolama alanı olarak kullanılır. Depolama hesabı içinde yeni bir depolama hesabı ve bir blob kapsayıcısı oluşturmak için aşağıdaki adımları izleyin:

1. [Depolama hesabı oluşturun](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. Depolama hesabı içinde [bir blob kapsayıcısı Crete](../../storage/blobs/storage-quickstart-blobs-portal.md) .

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>SQL Server yedeklemeleri blob depolamaya kopyalama

LRS kullanarak veritabanlarını yönetilen bir örneğe geçirirken, yedeklemeleri blob depolamaya yüklemek için aşağıdaki yaklaşımları kullanabilirsiniz:
- URL işlevselliğine SQL Server yerel [yedekleme](/sql/relational-databases/backup-restore/sql-server-backup-to-url) kullanma
- Yedeklemeleri bir blob kapsayıcısına yüklemek için [AzCopy](../../storage/common/storage-use-azcopy-v10.md) veya [Azure Depolama Gezgini](https://azure.microsoft.com/en-us/features/storage-explorer) kullanma
- Azure portal Depolama Gezgini kullanma

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>SQL Server yedeklemeleri doğrudan blob depolamaya yapın
Şirket ve ağ ilkeleriniz buna izin verirseniz, bir alternatif, SQL Server yerel [olarak yedekleme URL 'si](/sql/relational-databases/backup-restore/sql-server-backup-to-url) seçeneğini kullanarak SQL Server yedeklemeleri doğrudan blob depolamaya yapandır. Bu seçeneği bir daha belirtirseniz, yerel depolama üzerinde yedeklemeler yapmanız ve bunları blob depolamaya yüklemeniz gerekmez.

İlk adım olarak bu işlem, BLOB depolama için bir SAS kimlik doğrulama belirteci oluşturmanızı ve ardından belirteci SQL Server almanızı gerektirir. İkinci adım `TO URL` T-SQL ' i kullanarak yedeklemeler yapmak. Tüm yedeklemelerin etkin seçeneğiyle yapıldığından emin olun `CHEKSUM` .

Başvuru için aşağıdaki örnek kod, blob depolamaya yedeklemeler sağlar. Bu örnek SAS belirtecinin nasıl içeri aktarılacağı hakkında yönergeler içermez. SQL Server için SAS belirtecini oluşturma ve içeri aktarma da dahil olmak üzere ayrıntılı yönergeler bulabilirsiniz. öğreticide [Azure Blob depolamayı SQL Server kullanma](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)hakkında bilgi edinebilirsiniz. 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>LRS için bir blob Storage SAS kimlik doğrulama belirteci oluşturma

Azure Blob depolama, SQL Server ile SQL yönetilen örneği arasındaki yedekleme dosyaları için ara depolama alanı olarak kullanılır. LRS için yalnızca liste ve okuma izinlerine sahip bir SAS kimlik doğrulama belirteci oluşturmanız gerekir. Belirteç, LRS 'nin blob depolamaya erişmesini sağlar ve bunları SQL yönetilen örneği 'ne geri yüklemek için yedekleme dosyalarını kullanır. 

Belirteci oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal Depolama Gezgini açın.
2. **BLOB kapsayıcıları**' nı genişletin.
3. Blob kapsayıcısına sağ tıklayın ve **paylaşılan erişim Imzasını al**' ı seçin.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="S A s kimlik doğrulama belirteci oluşturma seçimlerini gösteren ekran görüntüsü.":::

4. Belirteç süre sonu için zaman çerçevesini seçin. Belirtecin geçişinizin süresi için geçerli olduğundan emin olun.
5. Belirtecin saat dilimini seçin: UTC veya yerel saat.
    
   > [!IMPORTANT]
   > Belirtecin saat dilimi ve yönetilen örneğiniz uyuşmayabilir. SAS belirtecinin uygun zaman geçerliliği olduğundan emin olun ve zaman dilimlerini göz önünde bulundurun. Mümkünse, saat dilimini planlı geçiş pencerenizin daha önceki ve sonraki bir zamanına ayarlayın.
6. Yalnızca **okuma** ve **Listeleme** izinlerini seçin.

   > [!IMPORTANT]
   > Başka herhangi bir izin seçmeyin. Bunu yaparsanız LRS başlatılmaz. Bu güvenlik gereksinimi tasarıma göre yapılır.
7. **Oluştur**’u seçin.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Oluştur düğmesiyle birlikte s belirteç süre sonu, saat dilimi ve izinleri seçimlerini gösteren ekran görüntüsü.":::

SAS kimlik doğrulaması, belirttiğiniz zaman geçerliliği ile oluşturulur. Aşağıdaki ekran görüntüsünde gösterildiği gibi belirtecin URI sürümü gerekir.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="S bir s belirtecinin U R ı sürümüne bir örnek gösteren ekran görüntüsü.":::

### <a name="copy-parameters-from-the-sas-token"></a>SAS belirtecinden parametreleri Kopyala

LRS 'yi başlatmak için SAS belirtecini kullanmadan önce, yapısını anlamanız gerekir. Oluşturulan SAS belirtecinin URI 'SI `?` , bu örnekte gösterildiği gibi bir soru işaretiyle () ayrılmış iki bölümden oluşur:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Günlük yeniden yürütme hizmeti için oluşturulan s için bir s belirteci için örnek U R ı." border="false":::

Soru işareti () ile başlayan ilk bölüm, `https://` `?` `StorageContainerURI` LRS 'ye girişte olduğu gibi beslendiği parametre için kullanılır. Veritabanı yedekleme dosyalarının depolandığı klasör hakkında LRS bilgileri verir.

İkinci bölüm, soru işaretinden () sonra başlayan `?` ve dizenin sonuna kadar tüm şekilde olacak şekilde `StorageContainerSasToken` parametre olur. Bu, belirtilen süre boyunca geçerli olan gerçek imzalı kimlik doğrulama belirtecidir. Bu bölümün `sp=` örnekte gösterildiği gibi ile başlaması gerekmez. Servis talebi farklılık gösterebilir.

Parametreleri aşağıdaki gibi kopyalayın:

1. `https://`Soru işaretine () kadar tüm yollardan başlayarak belirtecin ilk kısmını kopyalayın `?` . Bunu `StorageContainerUri` PowerShell 'de parametre olarak veya LRS 'yi başlatmak Için Azure CLI 'yi kullanarak kullanın.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Belirtecin ilk bölümünün kopyalanmasını gösteren ekran görüntüsü.":::

2. Simgenin sonuna kadar olan soru işaretinden () başlayarak belirtecin ikinci kısmını kopyalayın `?` . Bunu `StorageContainerSasToken` PowerShell 'de parametre olarak veya LRS 'yi başlatmak Için Azure CLI 'yi kullanarak kullanın.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Belirtecin ikinci kısmının kopyalanmasını gösteren ekran görüntüsü.":::
   
> [!NOTE]
> Belirtecin herhangi bir bölümünü kopyaladığınızda soru işaretini eklemeyin.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Azure 'da oturum açın ve bir abonelik seçin

Azure 'da oturum açmak için aşağıdaki PowerShell cmdlet 'ini kullanın:

```powershell
Login-AzAccount
```

Aşağıdaki PowerShell cmdlet 'ini kullanarak, yönetilen örneğinizin bulunduğu uygun aboneliği seçin:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Geçişi başlatma

LRS 'yi başlatarak geçişi başlatabilirsiniz. Hizmeti otomatik tamamlama ya da sürekli modda başlatabilirsiniz. 

Otomatik tamamlama modunu kullandığınızda, belirtilen yedekleme dosyalarının son geri yüklendiği zaman geçiş işlemi otomatik olarak tamamlanır. Bu seçenek, son yedekleme dosyasının dosya adını belirtmek için Start komutunu gerektirir. 

Sürekli modu kullandığınızda hizmet, eklenen tüm yeni yedekleme dosyalarını sürekli olarak geri yükler. Geçiş işlemi yalnızca el ile tam geçişi üzerinde tamamlanır. 

### <a name="start-lrs-in-autocomplete-mode"></a>LRS 'yi otomatik tamamlama modunda Başlat

LRS 'yi otomatik tamamlama modunda başlatmak için aşağıdaki PowerShell veya Azure CLı komutlarını kullanın. Parametresini kullanarak son yedekleme dosyası adını belirtin `-LastBackupName` . Belirtilen yedekleme dosyalarının son sürümlerini geri yükledikten sonra hizmet otomatik olarak bir cutover başlatılır.

Aşağıda, PowerShell kullanarak otomatik tamamlama modunda LRS başlatma örneği verilmiştir:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Azure CLı kullanarak LRS 'yi otomatik tamamlama modunda başlatma örneği aşağıda verilmiştir:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS 'yi sürekli modda Başlat

Aşağıda, PowerShell kullanarak, LRS 'yi sürekli modda başlatma örneği verilmiştir:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Azure CLı kullanarak, LRS 'yi sürekli modda başlatmaya bir örnek aşağıda verilmiştir:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

LRS 'yi sürekli modda başlatmak için PowerShell ve CLı istemcileri zaman uyumludur. Diğer bir deyişle, istemcilerin işi başlatmak için başarılı veya başarısız durumu raporlamak üzere API yanıtının bekleyeceği anlamına gelir. 

Bu bekleme sırasında komut komut istemine denetim döndürmez. Geçiş deneyimini betiğinden ve betiğin geri kalanı ile çalışmaya hemen devam etmek için LRS start komutuna ihtiyacınız varsa, PowerShell 'i anahtarla bir arka plan işi olarak çalıştırabilirsiniz `-AsJob` . Örnek:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Bir arka plan işi başlattığınızda, işin bitiş tarihi uzasa bile bir iş nesnesi hemen döndürülür. İş çalışırken kesintiye uğramadan oturumda çalışmaya devam edebilirsiniz. PowerShell 'i bir arka plan işi olarak çalıştırmaya ilişkin ayrıntılar için bkz. [PowerShell başlangıç-iş](/powershell/module/microsoft.powershell.core/start-job#description) belgeleri.

Benzer şekilde, bir arka plan işlemi olarak Linux üzerinde bir Azure CLı komutu başlatmak için, `&` LRS start komutunun sonundaki ampersan () işaretini kullanın:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> LRS 'yi başlattıktan sonra, sistem tarafından yönetilen tüm yazılım yamaları 47 saat boyunca durdurulur. Bu pencereden sonra, sonraki otomatik yazılım düzeltme eki LRS 'yi otomatik olarak durdurur. Bu durumda, geçişi sürdürülemez ve sıfırdan yeniden başlatmanız gerekir. 

## <a name="monitor-the-migration-progress"></a>Geçiş ilerlemesini izleme

PowerShell aracılığıyla geçişin ilerlemesini izlemek için aşağıdaki komutu kullanın:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Azure CLı aracılığıyla geçişin ilerlemesini izlemek için aşağıdaki komutu kullanın:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Geçişi durdur

Geçişi durdurmanız gerekiyorsa aşağıdaki cmdlet 'leri kullanın. Geçişin durdurulması SQL yönetilen örneğindeki geri yükleme veritabanını siler, bu nedenle geçişe devam etmek mümkün olmayacaktır.

PowerShell aracılığıyla geçiş işlemini durdurmak için aşağıdaki komutu kullanın:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Azure CLı aracılığıyla geçiş işlemini durdurmak için aşağıdaki komutu kullanın:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Geçişi (sürekli mod) doldurun

LRS 'yi sürekli modda başlattıysanız, tüm yedeklemelerin geri yüklendiğini doğruladıktan sonra tam geçişi ' i başlatmak geçişi tamamlar. Cutover sonrasında, veritabanı geçirilir ve okuma ve yazma erişimi için kullanılabilir hale gelir.

PowerShell aracılığıyla LRS sürekli modundaki geçiş işlemini gerçekleştirmek için aşağıdaki komutu kullanın:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Azure CLı aracılığıyla LRS sürekli modundaki geçiş işlemini gerçekleştirmek için aşağıdaki komutu kullanın:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>İşlevsel sınırlamalar

LRS 'nin işlevsel sınırlamaları şunlardır:
- Geri yüklemekte olduğunuz veritabanı, geçiş işlemi sırasında salt okuma erişimi için kullanılamaz.
- Sistem tarafından yönetilen yazılım düzeltme ekleri, LRS başlatıldıktan sonra 47 saat boyunca engellenmiştir. Bu zaman penceresinin süresi dolduktan sonra, bir sonraki yazılım güncelleştirmesi LRS 'yi durdurur. Daha sonra LRS 'yi sıfırdan yeniden başlatmanız gerekir.
- LRS, SQL Server etkinleştirilmiş bir seçenek ile yedeklenmek üzere veritabanlarının yedeklenmesini gerektirir `CHECKSUM` .
- LRS 'nin kullanacağı SAS belirtecinin tüm Azure Blob depolama kapsayıcısı için oluşturulması ve yalnızca okuma ve listeleme izinlerine sahip olması gerekir.
- Farklı veritabanları için yedekleme dosyalarının blob depolamada ayrı klasörlere yerleştirilmesi gerekir.
- LRS 'ler, blob depolamada yedekleme dosyaları olan ayrı klasörlere işaret eden her veritabanı için ayrı olarak başlatılmalıdır.
- LRS, tek bir yönetilen örnek başına en fazla 100 eşzamanlı geri yükleme işlemini destekleyebilir.

## <a name="troubleshooting"></a>Sorun giderme

LRS 'yi başlattıktan sonra, `get-azsqlinstancedatabaselogreplay` işlemin durumunu görmek için izleme cmdlet 'ini (veya `az_sql_midb_log_replay_show` ) kullanın. LRS bir süre sonra başlatılamazsa ve bir hata alırsanız, en sık karşılaşılan sorunları kontrol edin:

- SQL yönetilen örneğindeki mevcut bir veritabanı, SQL Server geçirmeye çalıştığınız adla aynı ada sahip mi? Veritabanlarından birini yeniden adlandırarak bu çakışmayı çözün.
- Veritabanı yedeklemesi SQL Server, bu `CHECKSUM` seçenek aracılığıyla mi yapıldı?
- SAS Simge² izinleri yalnızca LRS için okuma ve listeleme izinleridir?
- LRS için SAS belirtecini, soru işaretinden ( `?` ) sonra şu şekilde başlayan içerikle kopyaladınız: `sv=2020-02-10...` ? Lið 
- Geçiş işlemini başlatma ve tamamlama zaman penceresi için SAS belirteci geçerlilik süresi geçerli mi? SQL yönetilen örneği ve SAS belirteci için kullanılan farklı saat dilimlerine bağlı olarak uyuşmazlıklar olabilir. SAS belirtecini yeniden üretmeyi ve geçerli tarihten önce ve sonra zaman penceresinin belirteç geçerliliğini genişletmenize çalışın.
- Veritabanı adı, kaynak grubu adı ve yönetilen örnek adı doğru yazılmış mı?
- LRS 'yi otomatik tamamlama modunda başlattıysanız, belirtilen son yedekleme dosyası için geçerli bir dosya adı idi mı?

## <a name="next-steps"></a>Sonraki adımlar
- [SQL Server SQL yönetilen örneği 'ne geçirme](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)hakkında daha fazla bilgi edinin.
- [SQL Server Ile SQL yönetilen örneği arasındaki farklılıklar](transact-sql-tsql-differences-sql-server.md)hakkında daha fazla bilgi edinin.
- [Azure 'a geçirilen iş yükleri için en iyi uygulamalar](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)hakkında daha fazla bilgi edinin.
