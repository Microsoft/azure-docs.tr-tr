---
title: Azure özel bağlantı kullanılabilirliği
description: Bu makalede, hangi Azure hizmetlerinin özel bağlantıyı desteklediğini öğrenin.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 866eb9feb152c0094cd5281fe4820ccc4589386f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778328"
---
# <a name="azure-private-link-availability"></a>Azure özel bağlantı kullanılabilirliği

Azure özel bağlantısı, Azure PaaS hizmetlerine (örneğin, Azure depolama ve SQL veritabanı) ve Azure 'da barındırılan, müşteriye ait/iş ortağı hizmetlerine sanal ağınızdaki özel bir [uç nokta](private-endpoint-overview.md) üzerinden erişmenizi sağlar. 

> [!IMPORTANT]
> Azure özel bağlantısı artık genel kullanıma sunulmuştur. Hem özel uç nokta hem de özel bağlantı hizmeti (Standart yük dengeleyicinin arkasındaki hizmet) genel kullanıma sunulmuştur. Farklı zamanlamalarda Azure özel bağlantısına farklı Azure PaaS da eklenecektir. Bilinen sınırlamalar için bkz. [Özel uç nokta](private-endpoint-overview.md#limitations) ve [özel bağlantı hizmeti](private-link-service-overview.md#limitations). 

## <a name="service-availability"></a>Hizmet kullanılabilirliği

Aşağıdaki tablolarda özel bağlantı Hizmetleri ve kullanılabilir oldukları bölgeler listelenmektedir. 

### <a name="ai--machine-learning"></a>Yapay Zeka + Makine Öğrenmesi

|Desteklenen hizmetler  |Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Tüm ortak bölgeler    |  | GA   <br/> [Azure Machine Learning için özel uç nokta oluşturmayı öğrenin.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Analiz

|Desteklenen hizmetler  |Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Tüm ortak bölgeler <br/> Tüm kamu bölgeleri |  Ara sunucu [bağlantı ilkesi](../azure-sql/database/connectivity-architecture.md#connection-policy) için desteklenir |GA <br/> [Azure SYNAPSE Analytics için özel uç nokta oluşturmayı öğrenin.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Event Hub | Tüm ortak bölgeler<br/>Tüm kamu bölgeleri      |   | GA   <br/> [Azure Olay Hub 'ı için özel bir uç nokta oluşturmayı öğrenin.](../event-hubs/private-link-service.md)  |
| Azure İzleyici <br/>(Log Analytics & Application Insights) | Tüm ortak bölgeler      |  | GA   <br/> [Azure Izleyici için özel bir uç nokta oluşturmayı öğrenin.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Tüm ortak bölgeler<br/> Tüm kamu bölgeleri<br/>Tüm Çin bölgeleri    | Kimlik bilgilerinin bir Azure Anahtar Kasası 'nda depolanması gerekir| GA   <br/> [Azure Data Factory için özel uç nokta oluşturmayı öğrenin.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>İşlem

|Desteklenen hizmetler  |Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Uygulama Yapılandırması | Tüm ortak bölgeler      |  | Önizleme  </br> [Azure Uygulama yapılandırması için özel bir uç nokta oluşturmayı öğrenin](../azure-app-configuration/concept-private-endpoint.md) |
|Azure tarafından yönetilen diskler | Tüm ortak bölgeler<br/> Tüm kamu bölgeleri<br/>Tüm Çin bölgeleri    | [Bilinen sınırlamalar için seçin](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [Azure yönetilen diskler için özel bir uç nokta oluşturmayı öğrenin.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Kapsayıcılar

|Desteklenen hizmetler  |Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | Tüm ortak bölgeler<br/> Tüm kamu bölgeleri    | Kapsayıcı kayıt defterinin Premium katmanıyla desteklenir. [Katmanlar için seçin](../container-registry/container-registry-skus.md)| GA   <br/> [Azure Container Registry için özel uç nokta oluşturmayı öğrenin.](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes hizmeti-Kubernetes API 'SI | Tüm ortak bölgeler      |  | GA   <br/> [Azure Kubernetes hizmeti için özel bir uç nokta oluşturmayı öğrenin.](../aks/private-clusters.md)   |

### <a name="databases"></a>Veritabanları

|Desteklenen hizmetler  |Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Veritabanı         | Tüm ortak bölgeler <br/> Tüm kamu bölgeleri<br/>Tüm Çin bölgeleri      |  Ara sunucu [bağlantı ilkesi](../azure-sql/database/connectivity-architecture.md#connection-policy) için desteklenir | GA <br/> [Azure SQL için özel uç nokta oluşturmayı öğrenin](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Tüm ortak bölgeler<br/> Tüm kamu bölgeleri</br> Tüm Çin bölgeleri | |GA <br/> [Cosmos DB için özel uç nokta oluşturmayı öğrenin.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  PostgreSQL için Azure veritabanı-tek sunucu         | Tüm ortak bölgeler <br/> Tüm kamu bölgeleri<br/>Tüm Çin bölgeleri     | Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarında desteklenir | GA <br/> [PostgreSQL için Azure veritabanı için özel uç nokta oluşturmayı öğrenin.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  MySQL için Azure Veritabanı         | Tüm ortak bölgeler<br/> Tüm kamu bölgeleri<br/>Tüm Çin bölgeleri      |  | GA <br/> [MySQL için Azure veritabanı için özel bir uç nokta oluşturmayı öğrenin.](../mysql/concepts-data-access-security-private-link.md)     |
|  MariaDB için Azure Veritabanı         | Tüm ortak bölgeler<br/> Tüm kamu bölgeleri<br/>Tüm Çin bölgeleri     |  | GA <br/> [MariaDB için Azure veritabanı için özel bir uç nokta oluşturmayı öğrenin.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Tümleştirme

|Desteklenen hizmetler  |Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Tüm ortak bölgeler<br/> Tüm kamu bölgeleri       |  | GA   <br/> [Azure Event Grid için özel uç nokta oluşturmayı öğrenin.](../event-grid/network-security.md) |
|Azure Service Bus | Tüm ortak bölgeler<br/>Tüm kamu bölgeleri  | Azure Service Bus Premium katmanıyla desteklenir. [Katmanlar için seçin](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Azure Service Bus için özel uç nokta oluşturmayı öğrenin.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Nesnelerin İnterneti (IoT)

|Desteklenen hizmetler  |Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Tüm ortak bölgeler    |  | GA   <br/> [Azure IoT Hub için özel bir uç nokta oluşturmayı öğrenin.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Azure dijital TWINS tarafından desteklenen tüm ortak bölgeler     |  | Önizleme <br/> [Azure dijital TWINS için özel bir uç nokta oluşturmayı öğrenin.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Yönetim ve İdare

| Desteklenen hizmetler | Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
| ------------ | ----------------| ------------| ----------------|
| Azure Otomasyonu  | Tüm ortak bölgeler<br/> Tüm kamu bölgeleri |  | Önizleme </br> [Azure Otomasyonu için özel bir uç nokta oluşturmayı öğrenin.](../automation/how-to/private-link-security.md)|
|Azure Backup | Tüm ortak bölgeler<br/> Tüm kamu bölgeleri   |  | GA <br/> [Azure Backup için özel uç nokta oluşturmayı öğrenin.](../backup/private-endpoints.md)   |

### <a name="security"></a>Güvenlik

|Desteklenen hizmetler  |Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | Tüm ortak bölgeler<br/> Tüm kamu bölgeleri      |  | GA   <br/> [Azure Key Vault için özel uç nokta oluşturmayı öğrenin.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Depolama
|Desteklenen hizmetler  |Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure Blob depolama (Data Lake Storage 2. dahil)       |  Tüm ortak bölgeler<br/> Tüm kamu bölgeleri       |  Hesap türü Genel Amaçlı v2 'de desteklenir | GA <br/> [BLOB depolama için özel bir uç nokta oluşturmayı öğrenin.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Dosyaları | Tüm ortak bölgeler<br/> Tüm kamu bölgeleri      | |   GA <br/> [Azure dosyaları ağ uç noktaları oluşturmayı öğrenin.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure Dosya Eşitleme | Tüm ortak bölgeler      | |   GA <br/> [Azure dosyaları ağ uç noktaları oluşturmayı öğrenin.](../storage/file-sync/file-sync-networking-endpoints.md)   |
| Azure Kuyruk Depolama       |  Tüm ortak bölgeler<br/> Tüm kamu bölgeleri       |  Hesap türü Genel Amaçlı v2 'de desteklenir | GA <br/> [Kuyruk depolaması için özel bir uç nokta oluşturmayı öğrenin.](tutorial-private-endpoint-storage-portal.md) |
| Azure Tablo depolama       |  Tüm ortak bölgeler<br/> Tüm kamu bölgeleri       |  Hesap türü Genel Amaçlı v2 'de desteklenir | GA <br/> [Tablo depolaması için özel bir uç nokta oluşturmayı öğrenin.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | Dışındaki tüm ortak bölgeler: Almanya Orta, Almanya kuzeydoğu <br/> Tüm kamu bölgeleri  | | GA <br/> [Azure Batch için özel uç nokta oluşturmayı öğrenin.](../batch/private-connectivity.md) |

### <a name="web"></a>Web
|Desteklenen hizmetler  |Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | DOĞU ABD, GÜNEY ORTA ABD,<br/>Batı ABD 2, tüm Çin bölgeleri      |  | Önizleme   <br/> [Azure SignalR için özel uç nokta oluşturmayı öğrenin.](../azure-signalr/howto-private-endpoints.md)   |
|Azure Web Apps | Tüm ortak bölgeler<br/> Çin Kuzey 2 & Doğu 2    | PremiumV2, PremiumV3 veya Function Premium planıyla desteklenir  | GA   <br/> [Azure Web Apps için özel bir uç nokta oluşturmayı öğrenin.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Tüm ortak bölgeler <br/> Tüm kamu bölgeleri | Özel modda hizmet ile desteklenir | GA   <br/> [Azure Search için özel uç nokta oluşturmayı öğrenin.](../search/service-create-private-endpoint.md)    |
|Azure Geçişi | Tüm ortak bölgeler      |  | Önizleme <br/> [Azure Relay için özel uç nokta oluşturmayı öğrenin.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Standart yük dengeleyiciye sahip özel bağlantı hizmeti

|Desteklenen hizmetler  |Kullanılabilir bölgeler | Diğer önemli noktalar | Durum  |
|:-------------------|:-----------------|:----------------|:--------|
|Standart Azure Load Balancer arkasındaki özel bağlantı Hizmetleri | Tüm ortak bölgeler<br/> Tüm kamu bölgeleri<br/>Tüm Çin bölgeleri  | Standart Load Balancer destekleniyor | GA <br/> [Özel bağlantı hizmeti oluşturmayı öğrenin.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Sonraki adımlar

Azure özel bağlantı hizmeti hakkında daha fazla bilgi edinin:
- [Azure Özel Bağlantı nedir?](private-link-overview.md)
- [Azure portalını kullanarak Özel Uç Nokta oluşturma](create-private-endpoint-portal.md)
