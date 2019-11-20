---
title: Azure abonelik limitleri ve kotaları
description: Ortak Azure aboneliği ve hizmet sınırları, Kotalar ve kısıtlamaların bir listesini sağlar. Bu makale, limitlerin en yüksek değerleriyle birlikte nasıl artabileceğine ilişkin bilgiler içerir.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: f9fe540611b1b773867e1f92439e13df91ad4bd3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818340"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure aboneliği ve hizmet sınırları, kotalar ve kısıtlamalar
Bu belgede, bazen kotalar olarak da adlandırılan en yaygın Microsoft Azure limitlerin bazıları listelenir. Bu belge şu anda tüm Azure hizmetlerini kapsamıyor. Zamanla, liste genişletilir ve daha fazla hizmeti kapsayacak şekilde güncelleştirilir.

Azure fiyatlandırması hakkında daha fazla bilgi edinmek için bkz. [Azure fiyatlandırması genel bakış](https://azure.microsoft.com/pricing/). Burada, [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/)' nı kullanarak maliyetlerinizi tahmin edebilirsiniz. Ayrıca, belirli bir hizmet için (örneğin, [Windows VM 'ler](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)) Fiyatlandırma ayrıntıları sayfasına gidebilirsiniz. Maliyetlerinizi yönetmeye yardımcı olacak ipuçları için bkz. [Azure Faturalandırma ve maliyet yönetimiyle ilgili beklenmedik maliyetleri engelleme](billing/billing-getting-started.md).

> [!NOTE]
> Sınırı veya kotayı varsayılan sınırın üzerinde yükseltmek istiyorsanız, [ücretsiz bir çevrimiçi müşteri destek isteği açın](azure-resource-manager/resource-manager-quota-errors.md). Sınırlar, aşağıdaki tablolarda gösterilen en büyük sınır değerinin üzerinde olamaz. En fazla limit sütunu yoksa, kaynak, ayarlanabilir sınırlara sahip değildir.
>
> [Ücretsiz deneme abonelikleri](https://azure.microsoft.com/offers/ms-azr-0044p) sınır veya kota artışına uygun değildir. [Ücretsiz bir deneme aboneliğiniz](https://azure.microsoft.com/offers/ms-azr-0044p)varsa, bir [Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0003p/) aboneliğine yükseltebilirsiniz. Daha fazla bilgi için bkz. [Azure Ücretsiz deneme aboneliğinizi bir Kullandıkça Öde aboneliğine yükseltme](billing/billing-upgrade-azure-subscription.md) ve [ücretsiz deneme aboneliği hakkında SSS](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Sınırlar ve Azure Resource Manager
Artık birden çok Azure kaynağını tek bir Azure Kaynak grubunda birleştirmek mümkündür. Kaynak gruplarını kullandığınızda, bir kez genel olan sınırlar, Azure Resource Manager ile bölgesel düzeyde yönetilmeye gelir. Azure Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](azure-resource-manager/resource-group-overview.md).

Aşağıdaki sınırlar listesinde, yeni bir tablo Azure Resource Manager kullandığınızda limitlerde yapılan farkları yansıtır. Örneğin, bir **abonelik limitleri** tablosu ve **abonelik limitleri Azure Resource Manager** tablo vardır. Her iki senaryo için de bir sınır geçerliyse, bu yalnızca ilk tabloda gösterilir. Aksi belirtilmedikçe, sınırlar tüm bölgelerde geneldir.

> [!NOTE]
> Azure kaynak gruplarındaki kaynaklara yönelik kotalar, hizmet yönetimi kotaları olduğu için abonelik başına değil, aboneliğiniz tarafından erişilebilir. Örnek olarak vCPU kotaları kullanalım. VCPU desteğiyle bir kota artışı istemek için, hangi bölgelerde kaç vCPU kullanmak istediğinize karar vermelisiniz. Daha sonra istediğiniz miktarlar ve bölgeler için Azure Kaynak grubu vCPU kotaları için özel bir istek yaparsınız. Uygulamanızı orada çalıştırmak için Batı Avrupa 'de 30 vCPU kullanmanız gerekiyorsa, Batı Avrupa özellikle 30 vCPU isteğinde bulunur. VCPU kotanızı başka hiçbir bölgede artmıyor; yalnızca Batı Avrupa 30-vCPU kotasına sahiptir.
> <!-- -->
> Sonuç olarak, Azure Kaynak grubu Kotalarınızın herhangi bir bölgede iş yükünüz için ne olması gerektiğine karar verin. Ardından, dağıtmak istediğiniz her bölgede bu miktarı isteyin. Belirli bölgeler için geçerli kotalarınızı belirleme konusunda yardım için bkz. [dağıtım sorunlarını giderme](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Hizmete özgü sınırlar
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Otomasyon](#automation-limits)
* [Redis için Azure Önbelleği](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Bilişsel Arama](#azure-cognitive-search-limits)
* [Azure Bilişsel Hizmetler](#azure-cognitive-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [MySQL için Azure Veritabanı](#azure-database-for-mysql)
* [PostgreSQL için Azure Veritabanı](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Azure Güvenlik Duvarı](#azure-firewall-limits)
* [Azure İşlevleri](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Azure Machine Learning](#azure-machine-learning-limits)
* [Azure Haritalar](#azure-maps-limits)
* [Azure İzleyici](#azure-monitor-limits)
* [Azure İlkesi](#azure-policy-limits)
* [Azure SignalR hizmeti](#azure-signalr-service-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Veritabanı Geçiş Hizmeti](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [Ön kapı hizmeti](#azure-front-door-service-limits)
* [Kimlik Yöneticisi](#identity-manager-limits)
* [IoT Hub’ı](#iot-hub-limits)
* [IoT Hub Cihazı Sağlama Hizmeti](#iot-hub-device-provisioning-service-limits)
* [Anahtar Kasası](#key-vault-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Ağ](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [Azure savunma](#azure-bastion-limits)
  * [Azure DNS](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Azure Güvenlik Duvarı](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Yük Dengeleyici](#load-balancer)
  * [Ağ Izleyicisi](#network-watcher-limits)
  * [Genel IP adresi](#publicip-address)
  * [Özel bağlantı](#private-link-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Sanal Ağ](#networking-limits)
  * [Sanal WAN](#virtual-wan-limits)
* [Notification Hubs](#notification-hubs-limits)
* [Kaynak grubu](#resource-group-limits)
* [Rol tabanlı erişim denetimi](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Veritabanı](#sql-database-limits)
* [SQL Veri Ambarı](#sql-data-warehouse-limits)
* [Depolama](#storage-limits)
* [StorSimple sistemi](#storsimple-system-limits)
* [Akış Analizi](#stream-analytics-limits)
* [Abonelik](#subscription-limits)
* [Sanal Makineler](#virtual-machines-limits)
* [Sanal makine ölçek kümeleri](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Abonelik sınırları
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Abonelik limitleri-Azure hizmet yönetimi (klasik dağıtım modeli)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Abonelik limitleri-Azure Resource Manager
Azure Resource Manager ve Azure kaynak gruplarını kullandığınızda aşağıdaki sınırlar geçerlidir. Azure Resource Manager ile değiştirilmemiş sınırlar listelenmez. Bu limitler için önceki tabloya bakın.

Kaynak Yöneticisi API okuma ve yazma sınırları hakkında bilgi için bkz. [azaltma Kaynak Yöneticisi istekleri](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Kaynak grubu sınırları
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Sanal makine sınırları
#### <a name="virtual-machines-limits"></a>Sanal makine sınırları
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Sanal makine limitleri-Azure Resource Manager
Azure Resource Manager ve Azure kaynak gruplarını kullandığınızda aşağıdaki sınırlar geçerlidir.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Paylaşılan görüntü Galerisi sınırları

Paylaşılan görüntü galerileri kullanarak kaynak dağıtmak için abonelik başına sınırlar vardır:
- 100 paylaşılan görüntü galerileri, her bölge için abonelik başına
- 1\.000 görüntü tanımları, her bölge için abonelik başına
- 10.000 görüntü sürümü, her bölge için abonelik başına

### <a name="virtual-machine-scale-sets-limits"></a>Sanal Makine Ölçek Kümeleri sınırları
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Container Instances sınırları
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Container Registry sınırları
Aşağıdaki tabloda temel, standart ve Premium [hizmet katmanlarının](./container-registry/container-registry-skus.md)özellikleri ve limitleri ayrıntılı olarak verilmiştir.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes hizmet limitleri
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-limits"></a>Azure Machine Learning sınırları
Azure Machine Learning Işlem kotaları için en son değerler [Azure Machine Learning kota sayfasında](../articles/machine-learning/service/how-to-manage-quotas.md) bulunabilir

### <a name="networking-limits"></a>Ağ limitleri
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>ExpressRoute limitleri
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="virtual-wan-limits"></a>Sanal WAN sınırları
[!INCLUDE [virtual-wan-limits](../includes/virtual-wan-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway sınırları

Aşağıdaki tablo, aksi belirtilmediği takdirde v1, v2, standart ve WAF SKU 'Ları için geçerlidir.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Ağ Izleyicisi sınırları
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="private-link-limits"></a>Özel bağlantı limitleri
[!INCLUDE [private-link-limits](../includes/private-link-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager sınırları
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-bastion-limits"></a>Azure savunma limitleri
[!INCLUDE [Azure Bastion limits](../includes/bastion-limits.md)]

#### <a name="azure-dns-limits"></a>Azure DNS sınırları
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Azure Güvenlik Duvarı sınırları
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Azure ön kapı hizmeti sınırları
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Depolama sınırları
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Depolama hesabı sınırları hakkında daha fazla bilgi için bkz. [Azure Storage ölçeklenebilirlik ve performans hedefleri](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Depolama kaynak sağlayıcısı sınırları

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure Blob depolama sınırları
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure dosya sınırları
Azure dosya limitleri hakkında daha fazla bilgi için bkz. [Azure dosyaları ölçeklenebilirlik ve performans hedefleri](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Azure Dosya Eşitleme sınırları
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Azure kuyruk depolama sınırları
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure Tablo depolama sınırları
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Sanal makine diski sınırları
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Daha fazla bilgi için bkz. [sanal makine boyutları](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Yönetilen sanal makine diskleri

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Yönetilmeyen sanal makine diskleri

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Azure Cloud Services limitleri
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Azure bilişsel hizmetler sınırları
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>App Service sınırları
Aşağıdaki App Service limitleri Web Apps, Mobile Apps ve API Apps sınırlarını içerir.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>İşlev limitleri
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Zamanlayıcı sınırları
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Toplu iş sınırları
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services sınırları
Aşağıdaki tabloda Azure BizTalk Services sınırları gösterilmektedir.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB sınırları
Azure Cosmos DB sınırları için bkz. [Azure Cosmos DB sınırları](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>MySQL için Azure Veritabanı
MySQL için Azure veritabanı limitleri için bkz. [MySQL Için Azure veritabanı 'Nda sınırlamalar](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>PostgreSQL için Azure Veritabanı
PostgreSQL için Azure veritabanı limitleri için bkz. [PostgreSQL Için Azure veritabanı sınırlamaları](postgresql/concepts-limits.md).

### <a name="azure-cognitive-search-limits"></a>Azure Bilişsel Arama limitleri
Fiyatlandırma katmanları, arama hizmetinizin kapasitesini ve sınırlarını tespit edin. Katmanlar şunları içerir:

* Diğer Azure aboneleri ile paylaşılan **ücretsiz** çok kiracılı hizmet, değerlendirme ve küçük geliştirme projelerine yöneliktir.
* **Temel** , yüksek düzeyde kullanılabilir sorgu iş yükleri için en fazla üç çoğaltmaya sahip üretim iş yükleri için özel bilgi işlem kaynakları sağlar.
* S1, S2, S3 ve S3 yüksek yoğunluklu gibi **Standart**, daha büyük üretim iş yükleri için de kullanılır. İş yükü profilinizle en iyi şekilde eşleşen bir kaynak yapılandırması seçebilmeniz için Standart katmanda birden çok düzey bulunur.

**Abonelik başına sınırlar**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Arama hizmeti başına sınırlar**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Belge boyutu, saniye başına sorgu, anahtar, istek ve yanıt gibi daha ayrıntılı bir düzeyde sınırlamalar hakkında daha fazla bilgi edinmek için bkz. [Azure bilişsel arama hizmet limitleri](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Media Services sınırları
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Content Delivery Network sınırları
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Mobile Services sınırları
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Azure Izleyici sınırları

#### <a name="alerts"></a>Uyarılar

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Eylem grupları

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Günlük sorguları ve dili

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Log Analytics çalışma alanları

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Notification Hubs sınırları
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs sınırları
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus sınırları
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT Hub sınırları
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Cihaz sağlama hizmeti sınırlarını IoT Hub
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Data Factory sınırları
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics sınırları
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store sınırları
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Veritabanı geçiş hizmeti sınırları
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics sınırları
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory sınırları
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Event Grid sınırları
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure haritalar limitleri
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Azure Ilke sınırları
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple sistem sınırları
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Yedekleme limitleri
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Azure SignalR hizmeti sınırları
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Site Recovery limitleri
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>API Management sınırları
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Redsıs limitleri için Azure önbelleği
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Key Vault sınırları
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication sınırları
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Otomasyon sınırları
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Identity Manager sınırları
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Rol tabanlı erişim denetimi limitleri
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>SQL veritabanı limitleri
SQL veritabanı limitleri için bkz. [SQL veritabanı kaynak limitleri tek veritabanları için](sql-database/sql-database-vcore-resource-limits-single-databases.md), [elastik havuzlar ve havuza alınmış veritabanları için SQL veritabanı kaynak limitleri](sql-database/sql-database-vcore-resource-limits-elastic-pools.md)ve [yönetilen örnekler için SQL veritabanı kaynak sınırları](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>SQL veri ambarı sınırları
SQL veri ambarı sınırları için bkz. [SQL veri ambarı kaynak sınırları](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Ayrıca bkz.
- [Azure sınırlarını ve artışları anlayın](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Azure için sanal makine ve bulut hizmeti boyutları](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure Cloud Services için Boyutlar](cloud-services/cloud-services-sizes-specs.md)
