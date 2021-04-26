---
title: Azure Özel Uç Nokta nedir?
description: Azure özel uç noktası hakkında bilgi edinin
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: f18d9cb2a1bf76986a5c77477085f2f12ab728ae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771812"
---
# <a name="what-is-azure-private-endpoint"></a>Azure Özel Uç Nokta nedir?

Azure Özel Uç Noktası sizi Azure Özel Bağlantı ile desteklenen bir hizmete özel olarak ve güvenle bağlayan bir ağ arabirimidir. Özel Uç Nokta, sanal ağınızdaki bir özel IP adresini kullanır ve bu sayede hizmeti sanal ağınıza getirir. Hizmet, Azure depolama, Azure Cosmos DB, SQL gibi bir Azure hizmeti ya da kendi [özel bağlantı hizmetiniz](private-link-service-overview.md)olabilir.
  
## <a name="private-endpoint-properties"></a>Özel uç nokta özellikleri 
 Özel bir uç nokta aşağıdaki özellikleri belirtir: 


|Özellik  |Açıklama |
|---------|---------|
|Ad    |    Kaynak grubu içinde benzersiz bir ad.      |
|Alt ağ    |  Sanal bir ağdan özel IP adresleri dağıtmak ve ayırmak için alt ağ. Alt ağ gereksinimleri için, bu makaledeki sınırlamalar bölümüne bakın.         |
|Özel bağlantı kaynağı    |   Kullanılabilir türler listesinden kaynak KIMLIĞI veya diğer ad kullanarak bağlanacak özel bağlantı kaynağı. Bu kaynağa gönderilen tüm trafik için benzersiz bir ağ tanımlayıcısı oluşturulacak.       |
|Hedef alt kaynak   |      Bağlanılacak alt kaynak. Her özel bağlantı kaynağı türü, tercihe göre seçim yapmak için farklı seçeneklere sahiptir.    |
|Bağlantı onay yöntemi    |  Otomatik veya el ile. Azure rol tabanlı erişim denetimi (Azure RBAC) izinlerine bağlı olarak, Özel uç noktanız otomatik olarak onaylanabilir. Azure RBAC olmadan bir özel bağlantı kaynağına bağlanmaya çalışırsanız, kaynağın sahibinin bağlantıyı onaylamasını sağlamak için el ile yöntemini kullanın.        |
|İstek Iletisi     |  İstenen bağlantıların el ile onaylanabilmesi için bir ileti belirtebilirsiniz. Bu ileti, belirli bir isteği tanımlamak için kullanılabilir.        |
|Bağlantı durumu   |   Özel uç noktanın etkin olup olmadığını belirten bir salt okunurdur özelliği. Trafiği göndermek için yalnızca onaylanan durumdaki özel uç noktalar kullanılabilir. Kullanılabilir ek durumlar: <br>-**Onaylandı**: bağlantı otomatik olarak veya el ile onaylandı ve kullanılabilir hale gelmiştir.</br><br>-**Bekliyor**: bağlantı el ile oluşturuldu ve özel bağlantı kaynağı sahibi tarafından onay bekliyor.</br><br>-**Reddedildi**: bağlantı, özel bağlantı kaynağı sahibi tarafından reddedildi.</br><br>-Bağlantı **kesildi**: bağlantı, özel bağlantı kaynağı sahibi tarafından kaldırıldı. Özel uç nokta bilgilendirici hale gelir ve temizlik için silinmelidir. </br>|

Özel uç noktalarla ilgili bazı önemli ayrıntılar aşağıda verilmiştir: 
- Özel uç nokta, özel bağlantı tarafından desteklenen [VPN](https://azure.microsoft.com/services/vpn-gateway/) veya [Express Route](https://azure.microsoft.com/services/expressroute/) ve Hizmetleri kullanarak, bölgesel olarak eşlenmiş sanal ağlar, genel olarak eşlenmiş sanal ağlar ve şirket içi olan sanal ağlar arasında bağlantı sağlar.
 
- Ağ bağlantıları yalnızca özel uç noktaya bağlanan istemciler tarafından başlatılabilir, hizmet sağlayıcılarının hizmet tüketicilerine bağlantı başlatmak için herhangi bir yönlendirme yapılandırması yoktur. Bağlantılar yalnızca tek bir yönde ayarlanabilir.

- Özel bir uç nokta oluştururken, kaynağın yaşam döngüsü için salt okunurdur bir ağ arabirimi de oluşturulur. Arabirimine, özel bağlantı kaynağıyla eşleşen alt ağdan dinamik olarak özel IP adresleri atanır. Özel IP adresinin değeri, Özel uç noktanın tüm yaşam döngüsünün değişmeden kalır.
 
- Özel uç noktanın, sanal ağ ile aynı bölgede ve abonelikte dağıtılması gerekir. 
 
- Özel bağlantı kaynağı, sanal ağ ve özel uç noktadan farklı bir bölgede dağıtılabilir.
 
- Aynı özel bağlantı kaynağı kullanılarak birden çok özel uç nokta oluşturulabilir. Ortak bir DNS sunucusu yapılandırması kullanan tek bir ağ için önerilen uygulama, DNS çözünürlüğünde yinelenen girişlerin veya çakışmaların oluşmaması için belirli bir özel bağlantı kaynağı için tek bir özel uç nokta kullanmaktır. 
 
- Aynı sanal ağ içindeki aynı veya farklı alt ağlarda birden çok özel uç nokta oluşturulabilir. Bir abonelikte oluşturabileceğiniz özel uç noktalar sayısı için sınırlar vardır. Ayrıntılar için bkz. [Azure Limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

- Özel bağlantı kaynağından gelen aboneliğin Ayrıca, Micosoft. Network kaynak sağlayıcısına kayıtlı olması gerekir. Ayrıntılar için bkz. [Azure kaynak sağlayıcıları](../azure-resource-manager/management/resource-providers-and-types.md).

 
## <a name="private-link-resource"></a>Özel bağlantı kaynağı 
Özel bir bağlantı kaynağı, belirli bir özel uç noktanın hedef hedefidir. Aşağıda, kullanılabilir özel bağlantı kaynağı türlerinin listesi verilmiştir: 
 
|Özel bağlantı kaynağı adı  |Kaynak türü   |Alt kaynaklar  |
|---------|---------|---------|
|**Özel bağlantı hizmeti** (kendi hizmetiniz)   |  Microsoft. Network/privateLinkServices       | empty |
|**Azure Otomasyonu** |  Microsoft. Automation/automationAccounts | Web kancası, Dscandhyıbridworker |
|**Azure SQL Veritabanı** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        | 
|**Azure Depolama**  | Microsoft. Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Tablo (tablo, table_secondary)<BR> Kuyruk (kuyruk, queue_secondary)<BR> Dosya (dosya, file_secondary)<BR> Web (Web, web_secondary)        |
|**Azure Data Lake Storage 2. Nesil**  | Microsoft. Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Data Lake dosya sistemi Gen2 (DFS, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft. Azu, Smosdb/databaseAccounts    | SQL, MongoDB, Cassandra, Gremlin, tablo|
|**PostgreSQL için Azure veritabanı-tek sunucu** | Microsoft. DBforPostgreSQL/sunucuları    | Postgressqlserver |
|**MySQL için Azure Veritabanı** | Microsoft. Dbformyısql/sunucuları    | Te yapılandırılmış MySQLServer |
|**MariaDB için Azure Veritabanı** | Microsoft. Dbformarıdb/sunucular    | mariadbServer |
|**Azure IoT Hub** | Microsoft. Devices/IotHubs    | ıothub |
|**Azure Key Vault** | Microsoft. Keykasası/kasa    | kasa |
|**Azure Kubernetes hizmeti-Kubernetes API 'SI** | Microsoft. ContainerService/Managedkümeler    | yönetim |
|**Azure Search** | Microsoft. Search/searchService| searchService|  
|**Azure Container Registry** | Microsoft. ContainerRegistry/kayıt defterleri    | registry |
|**Azure Uygulama Yapılandırması** | Microsoft. Appconfiguration/Configurationmağazaları    | Configurationmağazaların |
|**Azure Backup** | Microsoft. RecoveryServices/kasa    | kasa |
|**Azure Event Hub** | Microsoft. EventHub/ad alanları    | ad alanı |
|**Azure Service Bus** | Microsoft. ServiceBus/ad alanları | ad alanı |
|**Azure Geçişi** | Microsoft. Relay/Namespace | ad alanı |
|**Azure Event Grid** | Microsoft. EventGrid/konuları    | konu başlığı |
|**Azure Event Grid** | Microsoft. EventGrid/Domains    | etki alanı |
|**Azure App Service** | Microsoft. Web/siteler    | Siteler |
|**Azure App Service Yuvaları** | Microsoft. Web/siteler    | barındıra`<slot name>` |
|**Azure Machine Learning** | Microsoft. MachineLearningServices/çalışma alanları    | amlworkspace |
|**SignalR** | Microsoft. SignalRService/SignalR    | signalR |
|**Azure İzleyici** | Microsoft. Insights/privateLinkScopes    | azuremonitor |
|**Bilişsel Hizmetler** | (Microsoft. Biliveservices/hesapları    | account |
|**Azure Dosya Eşitleme** | Microsoft. Storagessync/storageSyncServices    | 'Nin |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Özel uç noktaların ağ güvenliği 
Azure hizmetleri için özel uç noktalar kullanılırken, trafik belirli bir özel bağlantı kaynağıyla güvenli hale getirilir. Platform yalnızca belirtilen özel bağlantı kaynağına ulaşan ağ bağlantılarını doğrulamak için bir erişim denetimi gerçekleştirir. Aynı Azure hizmeti içindeki ek kaynaklara erişmek için ek özel uç noktalar gereklidir. 
 
Desteklenen bir Azure hizmetine bağlanmak için iş yüklerinizin genel uç noktalara erişmesini tamamen kilitleyemezsiniz. Bu denetim, aynı Azure hizmetinde barındırılan diğer kaynaklara erişimi engelleyen yerleşik bir savunma koruması sağlayarak kaynaklarınıza ek bir ağ güvenlik katmanı sağlar. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Onay iş akışı kullanarak bir özel bağlantı kaynağına erişim 
Aşağıdaki bağlantı onay yöntemlerini kullanarak bir özel bağlantı kaynağına bağlanabilirsiniz:
- Belirli özel bağlantı kaynağına sahip olduğunuzda veya izniniz olduğunda **otomatik olarak** onaylandı. Gerekli olan izin, özel bağlantı kaynak türüne aşağıdaki biçimde dayalıdır: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- Gerekli izinlere sahip olmadığınız ve erişim istemek istediğiniz zaman **el ile** istek. Bir onay iş akışı başlatılacak. Özel uç nokta ve izleyen özel uç nokta bağlantısı "Beklemede" durumunda oluşturulur. Bağlantıyı onaylamak özel bağlantı kaynağı sahibinin sorumluluğundadır. Onaylandıktan sonra, aşağıdaki onay iş akışı diyagramında gösterildiği gibi özel uç nokta trafiği normal şekilde göndermek üzere etkinleştirilir.  

![iş akışı onayı](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Özel bağlantı kaynağı sahibi, özel bir uç nokta bağlantısı üzerinde aşağıdaki eylemleri gerçekleştirebilir: 
- Tüm özel uç nokta bağlantılarının ayrıntılarını gözden geçirin. 
- Özel bir uç nokta bağlantısını onaylayın. Karşılık gelen özel uç nokta, trafiği özel bağlantı kaynağına göndermek için etkinleştirilir. 
- Özel bir uç nokta bağlantısını reddedin. İlgili özel uç nokta, durumu yansıtacak şekilde güncelleştirilir.
- Özel bir uç nokta bağlantısını herhangi bir durumda silin. Karşılık gelen özel uç nokta, eylemi yansıtacak şekilde bağlantısı kesik bir durumla güncelleştirilir, Özel uç nokta sahibi kaynağı yalnızca bu noktada silebilir. 
 
> [!NOTE]
> Yalnızca onaylanan bir durumdaki özel uç nokta, belirli bir özel bağlantı kaynağına trafik gönderebilir. 

### <a name="connecting-using-alias"></a>Takma ad kullanarak bağlanma
Diğer ad, hizmet sahibi bir standart yük dengeleyicinin arkasında özel bağlantı hizmeti oluşturduğunda oluşturulan benzersiz bir addır. Hizmet sahibi, bu diğer adı tüketicileriyle çevrimdışı olarak paylaşabilir. Tüketiciler, kaynak URI 'sini veya diğer adı kullanarak özel bağlantı hizmetine bağlantı isteğinde bulunabilir. Diğer adı kullanarak bağlanmak istiyorsanız, el ile bağlantı onay yöntemi kullanarak özel uç nokta oluşturmanız gerekir. El ile bağlantı onay yöntemi kullanmak için, Özel uç nokta oluşturma akışı sırasında el ile istek parametresini true olarak ayarlayın. Ayrıntılar için [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) ve [az Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) bölümüne bakın. 

## <a name="dns-configuration"></a>DNS yapılandırması 
Bağlantı dizesinin bir parçası olarak tam etki alanı adı (FQDN) kullanarak bir özel bağlantı kaynağına bağlanırken, DNS ayarlarınızı ayrılmış özel IP adresine çözümlemek üzere doğru şekilde yapılandırmak önemlidir. Mevcut Azure hizmetlerinde ortak bir uç nokta üzerinden bağlanılırken kullanılacak bir DNS yapılandırması zaten olabilir. Özel uç noktanız kullanılarak bağlanmak için bunun geçersiz kılınması gerekir. 
 
Özel uç nokta ile ilişkili ağ arabirimi, DNS 'nizi yapılandırmak için gerekli olan ve belirli bir özel bağlantı kaynağı için ayrılan FQDN ve özel IP adresleri dahil olmak üzere tam bilgi kümesini içerir. 

Özel uç noktalar için DNS yapılandırma önerilerini ve en iyi yöntemler hakkında ayrıntılı bilgi edinmek için lütfen [Özel uç nokta DNS yapılandırma makalesini](private-endpoint-dns.md)gözden geçirin.



 
## <a name="limitations"></a>Sınırlamalar
 
Aşağıdaki tabloda özel uç noktalar kullanılırken bilinen kısıtlamaların bir listesi yer almaktadır: 


|Sınırlama |Açıklama |Risk azaltma  |
|---------|---------|---------|
|Ağ güvenlik grubu (NSG) kuralları ve Kullanıcı tanımlı yollar özel uç nokta için uygulanmıyor    |NSG özel uç noktalar üzerinde desteklenmez. Özel uç noktayı içeren alt ağlarda NSG ile ilişkili olabilir, kurallar özel uç nokta tarafından işlenen trafikte geçerli olmayacaktır. Bir alt ağda özel uç noktalar dağıtmak için [ağ ilkeleri zorlamasının devre dışı](disable-private-endpoint-network-policy.md) olması gerekir. NSG aynı alt ağda barındırılan diğer iş yükleri üzerinde de zorlanır. Herhangi bir istemci alt ağındaki rotalar bir/32 öneki kullanacaktır, varsayılan yönlendirme davranışının değiştirilmesi benzer bir UDR gerektirir  | Kaynak istemcilerde giden trafik için NSG kurallarını kullanarak trafiği denetleyin. Özel uç nokta yollarını geçersiz kılmak için/32 ön ekiyle tek tek yolları dağıtın. Giden bağlantılar için NSG akış günlükleri ve izleme bilgileri hala destekleniyor ve kullanılabilir        |


## <a name="next-steps"></a>Sonraki adımlar
- [Portalı kullanarak SQL veritabanı için özel bir uç nokta oluşturma](create-private-endpoint-portal.md)
- [PowerShell kullanarak SQL veritabanı için özel bir uç nokta oluşturma](create-private-endpoint-powershell.md)
- [CLı kullanarak SQL veritabanı için özel bir uç nokta oluşturma](create-private-endpoint-cli.md)
- [Portalı kullanarak depolama hesabı için özel bir uç nokta oluşturma](./tutorial-private-endpoint-storage-portal.md)
- [Portalı kullanarak Azure Cosmos hesabı için özel bir uç nokta oluşturma](../cosmos-db/how-to-configure-private-endpoints.md)
- [Azure PowerShell kullanarak kendi özel bağlantı hizmetinizi oluşturun](create-private-link-service-powershell.md)
- [Portalı kullanarak PostgreSQL için Azure veritabanı için kendi özel bağlantısını oluşturun-tek sunucu](../postgresql/howto-configure-privatelink-portal.md)
- [PostgreSQL için Azure veritabanı-CLı kullanarak tek bir sunucu için kendi özel bağlantısını oluşturun](../postgresql/howto-configure-privatelink-cli.md)
- [Portalı kullanarak MySQL için Azure veritabanı için kendi özel bağlantısını oluşturun](../mysql/howto-configure-privatelink-portal.md)
- [CLı kullanarak MySQL için Azure veritabanı için kendi özel bağlantısını oluşturun](../mysql/howto-configure-privatelink-cli.md)
- [Portalı kullanarak MariaDB için Azure veritabanı için kendi özel bağlantısını oluşturun](../mariadb/howto-configure-privatelink-portal.md)
- [CLı kullanarak MariaDB için Azure veritabanı için kendi özel bağlantısını oluşturun](../mariadb/howto-configure-privatelink-cli.md)
- [Portal ve CLı kullanarak Azure Key Vault için kendi özel bağlantısını oluşturun](../key-vault/general/private-link-service.md)
