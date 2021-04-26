---
title: Azure abonelik limitleri ve kotaları
description: Ortak Azure aboneliği ve hizmet sınırları, Kotalar ve kısıtlamaların bir listesini sağlar. Bu makale, limitlerin en yüksek değerleriyle birlikte nasıl artabileceğine ilişkin bilgiler içerir.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 3ef3e6f3b67e8c92e0137f36e19c62cba79e1c14
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030836"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar

Bu belgede, bazen kotalar olarak da adlandırılan en yaygın Microsoft Azure limitlerin bazıları listelenir.

Azure fiyatlandırması hakkında daha fazla bilgi edinmek için bkz. [Azure fiyatlandırması genel bakış](https://azure.microsoft.com/pricing/). Burada, [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/)' nı kullanarak maliyetlerinizi tahmin edebilirsiniz. Ayrıca, belirli bir hizmet için (örneğin, [Windows VM 'ler](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)) Fiyatlandırma ayrıntıları sayfasına gidebilirsiniz. Maliyetlerinizi yönetmeye yardımcı olacak ipuçları için bkz. [Azure Faturalandırma ve maliyet yönetimiyle ilgili beklenmedik maliyetleri engelleme](../../cost-management-billing/cost-management-billing-overview.md).

## <a name="managing-limits"></a>Sınırları yönetme

> [!NOTE]
> Bazı hizmetler, ayarlanabilir sınırlara sahiptir.
>
> Bir hizmet, ayarlanabilir sınırlara sahip olmadığında aşağıdaki tablolarda üst bilgi **sınırı** kullanılır. Bu durumlarda, varsayılan ve en üst sınır aynıdır.
>
> Sınır ayarlanıyorsa, tablolar **varsayılan sınırı** ve **en fazla limit** üst bilgilerini içerir. Sınır, en yüksek sınırın üzerinde değil, varsayılan sınırın üzerinde oluşturulabilir.
>
> Sınırı veya kotayı varsayılan sınırın üzerinde yükseltmek istiyorsanız, [ücretsiz bir çevrimiçi müşteri destek isteği açın](../templates/error-resource-quota.md).
>
> *Geçici sınır* ve *sabit sınır* terimleri genellikle geçerli, ayarlanabilir sınırı (geçici sınır) ve en yüksek sınırı (sabit sınır) tanımlamakta kullanılır. Sınır ayarlanamaz ise, yalnızca bir sabit sınır değildir.
>

[Ücretsiz deneme abonelikleri](https://azure.microsoft.com/offers/ms-azr-0044p) sınır veya kota artışına uygun değildir. [Ücretsiz bir deneme aboneliğiniz](https://azure.microsoft.com/offers/ms-azr-0044p)varsa, bir [Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0003p/) aboneliğine yükseltebilirsiniz. Daha fazla bilgi için bkz. [Azure Ücretsiz deneme aboneliğinizi bir Kullandıkça Öde aboneliğine yükseltme](../../cost-management-billing/manage/upgrade-azure-subscription.md) ve [ücretsiz deneme aboneliği hakkında SSS](https://azure.microsoft.com/free/free-account-faq).

Bazı sınırlar, bölgesel düzeyde yönetilir.

Örnek olarak vCPU kotaları kullanalım. VCPU desteğiyle bir kota artışı istemek için, hangi bölgelerde kaç vCPU kullanmak istediğinize karar vermelisiniz. Daha sonra, istediğiniz miktarlar ve bölgeler için vCPU kotalarında artış isteğinde bulabilirsiniz. Uygulamanızı orada çalıştırmak için Batı Avrupa 'de 30 vCPU kullanmanız gerekiyorsa, Batı Avrupa özellikle 30 vCPU isteğinde bulunur. VCPU kotanızı başka hiçbir bölgede artmıyor; yalnızca Batı Avrupa 30-vCPU kotasına sahiptir.

Sonuç olarak, Kotalarınızın herhangi bir bölgede iş yükünüz için ne olması gerektiğine karar verin. Ardından, dağıtmak istediğiniz her bölgede bu miktarı isteyin. Belirli bölgeler için geçerli kotalarınızı belirleme konusunda yardım için bkz. [kaynak kotaları hatalarını giderme](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Genel sınırlar

Kaynak adları sınırları için bkz. [Azure kaynakları Için adlandırma kuralları ve kısıtlamalar](resource-name-rules.md).

Kaynak Yöneticisi API okuma ve yazma sınırları hakkında bilgi için bkz. [azaltma Kaynak Yöneticisi istekleri](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Yönetim grubu sınırları

Aşağıdaki sınırlar [Yönetim grupları](../../governance/management-groups/overview.md)için geçerlidir.

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Abonelik sınırları

Azure Resource Manager ve Azure kaynak gruplarını kullandığınızda aşağıdaki sınırlar geçerlidir.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Kaynak grubu sınırları

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Active Directory sınırları

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>API Management sınırları

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>App Service sınırları

Aşağıdaki App Service limitleri Web Apps, Mobile Apps ve API Apps sınırlarını içerir.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Otomasyon sınırları

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-app-configuration"></a>Azure Uygulama Yapılandırması

[!INCLUDE [app-configuration-limits](../../../includes/app-configuration-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Redsıs limitleri için Azure önbelleği

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Azure Cloud Services limitleri

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Azure Bilişsel Arama limitleri

Fiyatlandırma katmanları, arama hizmetinizin kapasitesini ve sınırlarını tespit edin. Katmanlar şunları içerir:

* Diğer Azure aboneleri ile paylaşılan **ücretsiz** çok kiracılı hizmet, değerlendirme ve küçük geliştirme projelerine yöneliktir.
* **Temel** , yüksek düzeyde kullanılabilir sorgu iş yükleri için en fazla üç çoğaltmaya sahip üretim iş yükleri için özel bilgi işlem kaynakları sağlar.
* S1, S2, S3 ve S3 yüksek yoğunluklu gibi **Standart**, daha büyük üretim iş yükleri için de kullanılır. İş yükü profilinizle en iyi şekilde eşleşen bir kaynak yapılandırması seçebilmeniz için Standart katmanda birden çok düzey bulunur.

**Abonelik başına sınırlar**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Arama hizmeti başına sınırlar**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Belge boyutu, saniye başına sorgu, anahtar, istek ve yanıt gibi daha ayrıntılı bir düzeyde sınırlamalar hakkında daha fazla bilgi edinmek için bkz. [Azure bilişsel arama hizmet limitleri](../../search/search-limits-quotas-capacity.md).

## <a name="azure-cognitive-services-limits"></a>Azure bilişsel hizmetler sınırları

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB sınırları

Azure Cosmos DB sınırları için bkz. [Azure Cosmos DB sınırları](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Azure Veri Gezgini limitleri

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>MySQL için Azure Veritabanı

MySQL için Azure veritabanı limitleri için bkz. [MySQL Için Azure veritabanı 'Nda sınırlamalar](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>PostgreSQL için Azure Veritabanı

PostgreSQL için Azure veritabanı limitleri için bkz. [PostgreSQL Için Azure veritabanı sınırlamaları](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Azure Işlevleri sınırları

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

Daha fazla bilgi için bkz. [Işlev barındırma planları karşılaştırması](../../azure-functions/functions-scale.md).

## <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes hizmet limitleri

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Azure Machine Learning sınırları

Azure Machine Learning Işlem kotaları için en son değerler [Azure Machine Learning kota sayfasında](../../machine-learning/how-to-manage-quotas.md) bulunabilir

## <a name="azure-maps-limits"></a>Azure haritalar limitleri

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Azure Izleyici sınırları

### <a name="alerts"></a>Uyarılar

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Eylem grupları

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="autoscale"></a>Otomatik Ölçeklendirme

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-autoscale.md)]

### <a name="log-queries-and-language"></a>Günlük sorguları ve dili

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics çalışma alanları

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/application-insights-limits.md)]

## <a name="azure-policy-limits"></a>Azure Ilke sınırları

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-quantum-limits"></a>Azure hisse limitleri

[!INCLUDE [quantum-limits](../../../includes/azure-quantum-limits.md)]

## <a name="azure-role-based-access-control-limits"></a>Azure rol tabanlı erişim denetimi limitleri

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control/limits.md)]

## <a name="azure-signalr-service-limits"></a>Azure SignalR hizmeti sınırları

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="azure-vmware-solution-limits"></a>Azure VMware Çözüm sınırları

[!INCLUDE [azure-vmware-solutions-limits](../../azure-vmware/includes/azure-vmware-solutions-limits.md)]

## <a name="backup-limits"></a>Yedekleme limitleri

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Toplu iş sınırları

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Klasik dağıtım modeli limitleri

Azure Resource Manager dağıtım modeli yerine klasik dağıtım modelini kullanıyorsanız, aşağıdaki sınırlar geçerlidir.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Container Instances sınırları

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Container Registry sınırları

Aşağıdaki tabloda temel, standart ve Premium [hizmet katmanlarının](../../container-registry/container-registry-skus.md)özellikleri ve limitleri ayrıntılı olarak verilmiştir.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Content Delivery Network sınırları

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Data Factory sınırları

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Data Lake Analytics sınırları

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-storage-limits"></a>Data Lake Storage sınırları

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Veri paylaşma sınırları

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Veritabanı geçiş hizmeti sınırları

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="digital-twins-limits"></a>Dijital TWINS limitleri

> [!NOTE]
> Bu hizmetin bazı alanlarında ayarlanabilir sınırlar ve diğerleri değildir. Bu, aşağıdaki tabloda, *ayarlanabilir?* sütunuyla birlikte gösterilir. Sınır ayarlanabilirken, *ayarlanabilir?* değeri *Evet*' tir.

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>Event Grid sınırları

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Event Hubs sınırları

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="iot-central-limits"></a>IoT Central sınırları
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>IoT Hub sınırları

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Cihaz sağlama hizmeti sınırlarını IoT Hub

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Key Vault sınırları

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="managed-identity-limits"></a>Yönetilen kimlik limitleri

[!INCLUDE [Managed-Identity-Limits](../../../includes/managed-identity-limits.md)]


## <a name="media-services-limits"></a>Media Services sınırları

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services v2 (eski)

Media Services V2 'ye (eski) özgü sınırlar için bkz. [Media Services V2 (eski)](../../media-services/previous/media-services-quotas-and-limitations.md)

## <a name="mobile-services-limits"></a>Mobile Services sınırları

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication sınırları

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Ağ limitleri

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>ExpressRoute limitleri

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-network-gateway-limits"></a>Sanal ağ geçidi sınırları

[!INCLUDE [virtual-network-gateway-limits](../../../includes/azure-virtual-network-gateway-limits.md)]

### <a name="nat-gateway-limits"></a>NAT ağ geçidi sınırları

[!INCLUDE [nat-gateway-limits](../../../includes/azure-nat-gateway-limits.md)]

### <a name="virtual-wan-limits"></a>Sanal WAN sınırları

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Application Gateway sınırları

Aşağıdaki tablo, aksi belirtilmediği takdirde v1, v2, standart ve WAF SKU 'Ları için geçerlidir.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Ağ Izleyicisi sınırları

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Özel Bağlantı sınırları

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

## <a name="purview-limits"></a>Purview limitleri

Azure purview kotaları için en son değerler [Azure purview kota sayfasında](../../purview/how-to-manage-quotas.md) bulunabilir.

### <a name="traffic-manager-limits"></a>Traffic Manager sınırları

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Azure savunma limitleri

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Azure DNS sınırları

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Azure Güvenlik Duvarı sınırları

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Azure ön kapı hizmeti sınırları

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Notification Hubs sınırları

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="service-bus-limits"></a>Service Bus sınırları

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Site Recovery limitleri

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>SQL veritabanı limitleri

SQL veritabanı limitleri için bkz. [SQL veritabanı kaynak limitleri tek veritabanları için](../../azure-sql/database/resource-limits-vcore-single-databases.md), [elastik havuzlar ve havuza alınmış veritabanları için SQL veritabanı kaynak limitleri](../../azure-sql/database/resource-limits-vcore-elastic-pools.md)ve [SQL yönetilen örneği için SQL veritabanı kaynak sınırları](../../azure-sql/managed-instance/resource-limits.md).

## <a name="azure-synapse-analytics-limits"></a>Azure SYNAPSE Analytics limitleri

Azure SYNAPSE Analytics limitleri için bkz. [Azure SYNAPSE kaynak sınırları](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="azure-files-and-azure-file-sync"></a>Azure dosyaları ve Azure Dosya Eşitleme
Azure dosya ve Dosya Eşitleme sınırları hakkında daha fazla bilgi edinmek için bkz. [Azure dosyaları ölçeklenebilirlik ve performans hedefleri](../../storage/files/storage-files-scale-targets.md).

## <a name="storage-limits"></a>Depolama sınırları

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Standart depolama hesapları sınırları hakkında daha fazla bilgi için bkz. [Standart depolama hesapları Için ölçeklenebilirlik hedefleri](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Depolama kaynak sağlayıcısı sınırları

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Azure Blob depolama sınırları

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Azure kuyruk depolama sınırları

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Azure Tablo depolama sınırları

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Sanal makine diski sınırları

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Daha fazla bilgi için bkz. [sanal makine boyutları](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="disk-encryption-sets"></a>Disk şifreleme kümeleri

Her abonelik için bölge başına 1000 disk şifreleme kümesi sınırlaması vardır. Daha fazla bilgi için bkz. [Linux](../../virtual-machines/disk-encryption.md#restrictions) veya [Windows](../../virtual-machines/disk-encryption.md#restrictions) sanal makineleri için şifreleme belgeleri. Kotayı artırmanız gerekiyorsa Azure desteği 'ne başvurun.

### <a name="managed-virtual-machine-disks"></a>Yönetilen sanal makine diskleri

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Yönetilmeyen sanal makine diskleri

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>StorSimple sistem sınırları

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Stream Analytics sınırları

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Sanal makine sınırları

### <a name="virtual-machines-limits"></a>Sanal makine sınırları

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Sanal makine limitleri-Azure Resource Manager

Azure Resource Manager ve Azure kaynak gruplarını kullandığınızda aşağıdaki sınırlar geçerlidir.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Paylaşılan görüntü Galerisi sınırları

Paylaşılan görüntü galerileri kullanarak kaynak dağıtmak için abonelik başına sınırlar vardır:

- 100 paylaşılan görüntü galerileri, her bölge için abonelik başına
- 1.000 görüntü tanımları, her bölge için abonelik başına
- 10.000 görüntü sürümü, her bölge için abonelik başına

## <a name="virtual-machine-scale-sets-limits"></a>Sanal Makine Ölçek Kümeleri sınırları

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Ayrıca bkz.

* [Azure sınırlarını ve artışları anlayın](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Azure için sanal makine ve bulut hizmeti boyutları](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure Cloud Services için Boyutlar](../../cloud-services/cloud-services-sizes-specs.md)
* [Azure kaynaklarına yönelik adlandırma kuralları ve kısıtlamaları](resource-name-rules.md)
