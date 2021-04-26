---
title: Using Azure İzleyici Log Analytics'te müşteri tarafından yönetilen depolama hesaplarını kullanma
description: Log Analytics senaryolar için kendi depolama hesabınızı kullanın
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: a6d4c5811c08aa8c4de2eeea5f5f53967c3006b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025365"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Using Azure İzleyici Log Analytics'te müşteri tarafından yönetilen depolama hesaplarını kullanma

Log Analytics çeşitli senaryolarda Azure Storage 'ı kullanır. Bu kullanım genellikle otomatik olarak yönetilir. Ancak, bazı durumlar, müşteri tarafından yönetilen depolama hesabı olarak da adlandırılan kendi depolama hesabınızı sağlamanızı ve yönetmenizi gerektirir. Bu belgede WAD/LAD günlükleri, özel bağlantı ve müşteri tarafından yönetilen anahtar (CMK) şifrelemesi için müşteri tarafından yönetilen depolamanın kullanımı ele alınmaktadır. 

> [!NOTE]
> Bu biçimlendirmeye ve içeriğe göre değişiklik olabileceğinden, müşteri tarafından yönetilen depolama 'ya karşıya yükleme Log Analytics içerikler üzerinde bir bağımlılık yapmanızı öneririz.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Azure Tanılama uzantısı günlüklerini geri yükleme (WAD/LAD)
Azure Tanılama uzantısı aracıları (sırasıyla, Windows ve Linux aracıları için WAD ve LAD olarak da bilinir), çeşitli işletim sistemi günlükleri toplar ve bunları müşteri tarafından yönetilen bir depolama hesabında depolar. Daha sonra bu günlükleri gözden geçirmek ve analiz etmek için Log Analytics kullanabilirsiniz.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>Depolama hesabınızdan Azure Tanılama uzantısı günlüklerini toplama
Depolama hesabını, [Azure Portal](../agents/diagnostics-extension-logs.md#collect-logs-from-azure-storage) kullanarak veya [Storage Insights apı](/rest/api/loganalytics/storage%20insights/createorupdate)'sini çağırarak Log Analytics çalışma alanınıza depolama veri kaynağı olarak bağlayın.

Desteklenen veri türleri:
* Syslog
* Windows olayları
* Service Fabric
* ETW olayları
* IIS Günlükleri

## <a name="using-private-links"></a>Özel bağlantıları kullanma
Müşteri tarafından yönetilen depolama hesapları, Azure Izleyici kaynaklarına bağlanmak için özel bağlantılar kullanıldığında özel günlükleri veya IIS günlüklerini almak için kullanılır. Bu veri türlerinin alımı, önce günlükleri bir ara Azure depolama hesabına yükler ve ardından bunları bir çalışma alanına alır. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>Özel bir bağlantı üzerinden müşteri tarafından yönetilen bir depolama hesabı kullanma
#### <a name="workspace-requirements"></a>Çalışma alanı gereksinimleri
Azure Izleyici 'ye özel bir bağlantı üzerinden bağlanılırken, Log Analytics aracılar yalnızca bir özel bağlantı üzerinden erişilebilen çalışma alanlarına Günlükler gönderebilir. Bu gereksinim şunları yapmanız gerektiği anlamına gelir:
* Bir Azure Izleyici özel bağlantı kapsamı (AMPLS) nesnesi yapılandırma
* Çalışma Alanlarınızdaki bağlantısını bağlayın
* AMPLS 'LERI ağınıza özel bir bağlantı üzerinden bağlayın. 

AMPLS yapılandırma yordamı hakkında daha fazla bilgi için bkz. [Azure özel bağlantı kullanarak ağları güvenli bir şekilde Azure izleyici 'ye bağlama](./private-link-security.md). 

#### <a name="storage-account-requirements"></a>Depolama hesabı gereksinimleri
Depolama hesabının özel bağlantısına başarıyla bağlanması için şunları yapmanız gerekir:
* VNet 'iniz veya eşlenmiş bir ağ üzerinde yer alır ve özel bir bağlantı üzerinden sanal ağınıza bağlı olun.
* Bağlandığı çalışma alanıyla aynı bölgede yer alır.
* Azure Izleyici 'nin depolama hesabına erişmesine izin verin. Depolama hesabınıza yalnızca ağ seçme erişimine izin vermeyi seçtiyseniz, "Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver" özel durumunu seçmeniz gerekir.
![Depolama hesabı güveni MS Services resmi](./media/private-storage/storage-trust.png)
* Çalışma alanınız diğer ağlardan gelen trafiği de işlediğinde, depolama hesabını ilgili ağlardan/Internet 'ten gelen trafiğe izin verecek şekilde yapılandırmanız gerekir.
* Aracılar ve depolama hesabı arasında TLS sürümü koordine etmek, Log Analytics TLS 1,2 veya üstünü kullanarak veri göndermeniz önerilir. [Platforma özgü Kılavuzu](./data-security.md#sending-data-securely-using-tls-12)gözden geçirin ve gerekirse, [aracılarınızı TLS 1,2 kullanacak şekilde yapılandırın](../agents/agent-windows.md#configure-agent-to-use-tls-12). Mümkün olmayan bir nedenden dolayı depolama hesabını TLS 1,0 kabul edecek şekilde yapılandırın.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>CMK veri şifrelemesi için müşteri tarafından yönetilen bir depolama hesabı kullanma
Azure depolama, bir depolama hesabındaki bekleyen tüm verileri şifreler. Varsayılan olarak, verileri şifrelemek için Microsoft tarafından yönetilen anahtarları (MMK) kullanır; Ancak Azure depolama, depolama verilerinizi şifrelemek için Azure Anahtar Kasası 'ndan CMK 'yı kullanmanıza da olanak tanır. Kendi anahtarlarınızı Azure Key Vault içeri aktarabilir ya da anahtar oluşturmak için Azure Key Vault API 'Lerini kullanabilirsiniz.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Müşteri tarafından yönetilen bir depolama hesabı gerektiren CMK senaryoları
* CMK ile günlük uyarı sorgularını şifreleme
* Kaydedilen sorguları CMK ile şifreleme

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>CMK 'yi müşteri tarafından yönetilen depolama hesaplarına uygulama
##### <a name="storage-account-requirements"></a>Depolama hesabı gereksinimleri
Depolama hesabı ve Anahtar Kasası aynı bölgede olmalıdır, ancak farklı aboneliklerde olabilir. Azure depolama şifreleme ve anahtar yönetimi hakkında daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama şifrelemesi](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Depolama hesaplarınıza CMK uygulama
Azure depolama hesabınızı Azure Key Vault ile CMK kullanacak şekilde yapılandırmak için [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)veya [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)kullanın. 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Depolama hesaplarını Log Analytics çalışma alanınıza bağlama
### <a name="using-the-azure-portal"></a>Azure portalını kullanma
Azure portal, çalışma alanınızın menüsünü açın ve *bağlı depolama hesapları*' nı seçin. Bağlı depolama hesaplarını yukarıda bahsedilen kullanım örneklerine göre gösteren bir dikey pencere açılır. (özel bağlantı üzerinden alma, kaydedilen sorgulara veya uyarılara CMK uygulama).
![Bağlı depolama hesapları dikey penceresi ](./media/private-storage/all-linked-storage-accounts.png) tablo üzerinde bir öğe seçilirse, bu türün bağlantılı depolama hesabını ayarlayabileceğiniz veya güncelleştirebileceğiniz depolama hesabı ayrıntıları açılır. 
![Depolama hesabı dikey penceresini bağlama isterseniz ](./media/private-storage/link-a-storage-account-blade.png) , farklı kullanım örnekleri için aynı hesabı kullanabilirsiniz.

### <a name="using-the-azure-cli-or-rest-api"></a>Azure CLı veya REST API kullanma
Ayrıca, [Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) veya [REST API](/rest/api/loganalytics/linkedstorageaccounts)aracılığıyla çalışma alanınıza bir depolama hesabı da bağlayabilirsiniz.

Uygulanabilir dataSourceType değerleri şunlardır:
* CustomLogs: özel Günlükler ve IIS günlüklerinin alımı için depolama hesabını kullanmak üzere
* Sorgu-kayıtlı sorguları depolamak için depolama hesabını kullanmak için (CMK şifrelemesi için gereklidir)
* Uyarılar-günlük tabanlı uyarıları depolamak üzere depolama hesabını kullanmak için (CMK şifrelemesi için gereklidir)


## <a name="managing-linked-storage-accounts"></a>Bağlı depolama hesaplarını yönetme

### <a name="create-or-modify-a-link"></a>Bağlantı oluşturma veya değiştirme
Bir depolama hesabını bir çalışma alanına bağladığınızda, Log Analytics hizmetin sahip olduğu depolama hesabı yerine onu kullanmaya başlayacaktır. Şunları yapabilirsiniz 
* Günlüklerin yükünü aralarında yaymak için birden fazla depolama hesabı kaydetme
* Birden çok çalışma alanı için aynı depolama hesabını yeniden kullanma

### <a name="unlink-a-storage-account"></a>Depolama hesabının bağlantısını kaldırma
Bir depolama hesabı kullanmayı durdurmak için, depolamanın çalışma alanıyla bağlantısını kaldırın. Tüm depolama hesaplarının bir çalışma alanından bağlantısı kesmek, Log Analytics hizmet tarafından yönetilen depolama hesaplarına güvenmeye çalışacağı anlamına gelir. Ağınızın internet erişimi sınırlı ise, bu depolar kullanılamayabilir ve depolamaya bağlı olan herhangi bir senaryo başarısız olur.

### <a name="replace-a-storage-account"></a>Bir depolama hesabını değiştirme
Alma işlemi için kullanılan bir depolama hesabını değiştirmek için
1.  **Yeni bir depolama hesabının bağlantısını oluşturun.** Günlüğe kaydetme aracıları güncelleştirilmiş yapılandırmayı alacak ve yeni depolama alanına veri göndermeye başlayacak. İşlem birkaç dakika sürebilir.
2.  **Ardından, aracıların kaldırılan hesaba yazmayı durdurması için eski depolama hesabının bağlantısını kaldırın.** Alma işlemi, tüm içeri alınana kadar bu hesaptan veri okumayı sürdürür. Tüm Günlükler alınana kadar depolama hesabını silmeyin.

### <a name="maintaining-storage-accounts"></a>Depolama hesaplarını sürdürme
#### <a name="manage-log-retention"></a>Günlük bekletmesini yönetme
Kendi depolama hesabınızı kullanırken, bekletme size aittir. Log Analytics, özel depolama ortamınızda depolanan günlükleri silmez. Bunun yerine, tercihlerinizi tercihlerinize göre işlemek için bir ilke ayarlamanız gerekir.

#### <a name="consider-load"></a>Yüklemeyi düşünün
Depolama hesapları, istek azaltma isteklerini başlatmadan önce belirli bir okuma ve yazma isteği yükünü işleyebilir (daha fazla bilgi Için bkz. [BLOB depolama Için ölçeklenebilirlik ve performans hedefleri](../../storage/common/scalability-targets-standard-account.md)). Azaltma, günlüklerin alınması için gereken süreyi etkiler. Depolama Hesabınız aşırı yüklenmişse, yükü aralarında dağıtmak için ek bir depolama hesabı kaydedin. Depolama hesabınızın kapasitesini izlemek için [Azure Portal öngörülerini]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview)gözden geçirin.

### <a name="related-charges"></a>İlgili ücretler
Depolama hesapları, depolanan verilerin hacmi, depolama türü ve artıklık türü tarafından ücretlendirilir. Ayrıntılar için bkz. [Blok Blobu fiyatlandırma](https://azure.microsoft.com/pricing/details/storage/blobs) ve [Tablo Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Sonraki adımlar

- [Ağları Azure izleyici 'ye güvenli bir şekilde bağlamak Için Azure özel bağlantısını kullanma](private-link-security.md) hakkında bilgi edinin
- [Azure izleyici müşterinin yönettiği anahtarlar](../logs/customer-managed-keys.md) hakkında bilgi edinin