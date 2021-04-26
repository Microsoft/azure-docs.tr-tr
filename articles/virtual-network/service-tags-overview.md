---
title: Azure hizmet etiketlerine genel bakış
titlesuffix: Azure Virtual Network
description: Hizmet etiketleri hakkında bilgi edinin. Hizmet etiketleri, güvenlik kuralı oluşturma karmaşıklığını en aza indirmenize yardımcı olur.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/14/2021
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 4a18e0f257ac8d7b7d981d53249cf29f2f8f38bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790158"
---
# <a name="virtual-network-service-tags"></a>Sanal ağ hizmeti etiketleri
<a name="network-service-tags"></a>

Hizmet etiketi, belirli bir Azure hizmetinden bir IP adresi önekleri grubunu temsil eder. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir ve ağ güvenlik kuralları için sık sık güncelleştirmelerin karmaşıklığını en aza indirir.

[Ağ güvenlik gruplarında](./network-security-groups-overview.md#security-rules) veya [Azure Güvenlik duvarında](../firewall/service-tags.md)ağ erişim denetimleri tanımlamak için hizmet etiketlerini kullanabilirsiniz. Güvenlik kuralları oluştururken belirli IP adreslerinin yerine hizmet etiketleri kullanın. Bir kuralın uygun *kaynak* veya *hedef* alanında **apimanayönetimi** gibi hizmet etiketi adını belirterek, ilgili hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. 

> [!NOTE] 
> 2021 Mart itibariyle, [Kullanıcı tanımlı rotalarda](./virtual-networks-udr-overview.md)açık IP aralıkları yerine hizmet etiketleri de kullanabilirsiniz. Bu özellik şu anda Genel Önizlemededir. 

Hizmet etiketlerini kullanarak, genel uç noktalarından oluşan Azure hizmetlerine erişirken ağ yalıtımı elde edebilir ve Azure kaynaklarınızı genel Internet üzerinden koruyabilirsiniz. **Internet** **'ten gelen** /giden trafiği reddetmek ve belirli Azure hizmetlerinin diğer [kullanılabilir hizmet etiketlerine](#available-service-tags) /sayısına giden trafiğe izin vermek için gelen/giden ağ güvenlik grubu kuralları oluşturun.

![Hizmet etiketleri kullanarak Azure hizmetlerinin ağ yalıtımı](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Kullanılabilir hizmet etiketleri
Aşağıdaki tablo, [ağ güvenlik grubu](./network-security-groups-overview.md#security-rules) kurallarında kullanılabilecek tüm hizmet etiketlerini içerir.

Sütunlar, etiketin şu şekilde olduğunu belirtir:

- Gelen veya giden trafiği kapsayan kurallar için uygundur.
- [Bölgesel](https://azure.microsoft.com/regions) kapsamı destekler.
- [Azure Güvenlik Duvarı](../firewall/service-tags.md) kurallarında kullanılabilir.

Varsayılan olarak, hizmet etiketleri tüm bulutun aralıklarını yansıtır. Bazı hizmet etiketleri, ilgili IP aralıklarını belirtilen bir bölgeye kısıtlayarak daha ayrıntılı denetime de olanak tanır. Örneğin, hizmet etiketi **depolaması** tüm bulut Için Azure Storage 'ı temsil eder, ancak **Storage. WestUS** aralığı yalnızca WestUS bölgesindeki depolama IP adresi aralıklarına göre daraltır. Aşağıdaki tablo, her bir hizmet etiketinin bu tür bölgesel kapsamları destekleyip desteklemediğini gösterir.  

| Etiket | Amaç | Gelen veya giden trafiği kullanabilir miyim? | Bölgesel olabilir mi? | Azure Güvenlik Duvarı ile kullanılabilir mi? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Eylem grubu. | Gelen | Hayır | Hayır |
| **ApiManagement** | Azure API Management adanmış dağıtımlar için yönetim trafiği. <br/><br/>*Note:* Bu etiket, bölge başına denetim düzlemi için Azure API Management hizmet uç noktasını temsil eder. Bu, müşterilerin API Management hizmetinde yapılandırılmış API 'Ler, Işlemler, Ilkeler, NamedValues üzerinde yönetim işlemleri gerçekleştirmesini sağlar.  | Gelen | Yes | Yes |
| **Applicationınsi, Savailability** | Kullanılabilirliği Application Insights. | Gelen | Hayır | Hayır |
| **AppConfiguration** | Uygulama yapılandırması. | Giden | Hayır | Hayır |
| **AppService**    | Azure App Service. Bu etiket, Web uygulamaları ve Işlev uygulamalarına giden güvenlik kuralları için önerilir.  | Giden | Yes | Yes |
| **AppServiceManagement** | App Service Ortamı adanmış dağıtımlar için yönetim trafiği. | Her ikisi | Hayır | Yes |
| **AzureActiveDirectory** | Azure Active Directory. | Giden | Hayır | Yes |
| **AzureActiveDirectoryDomainServices** | Azure Active Directory Domain Services adanmış dağıtımlar için yönetim trafiği. | Her ikisi | Hayır | Yes |
| **AzureAdvancedThreatProtection** | Azure Gelişmiş tehdit koruması. | Giden | Hayır | Hayır |
| **AzureAPIForFHIR** | FHıR için Azure API (hızlı sağlık ile birlikte çalışabilirlik kaynakları).<br/><br/> *Not: Bu etiket Şu anda Azure Portal aracılığıyla yapılandırılamaz.*| Giden | Hayır | Hayır |
| **AzureArcInfrastructure** | Azure Arc etkin sunucuları, Azure Arc etkin Kubernetes ve konuk yapılandırma trafiği.<br/><br/>*Note:* Bu etiketin **AzureActiveDirectory**,**AzureTrafficManager** ve **AzureResourceManager** etiketlerine bir bağımlılığı vardır. *Bu etiket Şu anda Azure Portal aracılığıyla yapılandırılamaz*.| Giden | Hayır | Yes |
| **AzureBackup** |Azure Backup.<br/><br/>*Note:* Bu etiketin **depolama** ve **AzureActiveDirectory** etiketlerine bir bağımlılığı vardır. | Giden | Hayır | Yes |
| **AzureBotService** | Azure bot hizmeti. | Giden | Hayır | Hayır |
| **AzureCloud** | Tüm [veri merkezi genel IP adresleri](https://www.microsoft.com/download/details.aspx?id=56519). | Giden | Yes | Yes |
| **Azurecognivesearch** | Azure Bilişsel Arama. <br/><br/>Bu etiket veya bu etiketin kapsadığı IP adresleri, dizin oluşturucularının veri kaynaklarına güvenli erişimini sağlamak için kullanılabilir. Daha fazla ayrıntı için [Dizin Oluşturucu bağlantı belgelerine](../search/search-indexer-troubleshooting.md#connection-errors) bakın. <br/><br/> *Not*: Arama hizmetinin IP 'si, bu hizmet etıketı için IP aralıkları listesine dahil değildir ve ayrıca VERI kaynaklarının IP güvenlik duvarına **eklenmelidir** . | Gelen | Hayır | Hayır |
| **AzureConnectors** | Bu etiket, Azure Logic Apps hizmetine gelen Web kancası geri aramalarını ve ilgili hizmetlerine giden çağrıları (örneğin, Azure depolama veya Azure Event Hubs) oluşturan yönetilen bağlayıcılar için kullanılan IP adreslerini temsil eder. | Gelen/giden | Yes | Yes |
| **AzureContainerRegistry** | Azure Container Registry. | Giden | Yes | Yes |
| **Azu, Smosdb** | Azure Cosmos DB. | Giden | Yes | Yes |
| **AzureDatabricks** | Azure Databricks. | Her ikisi | Hayır | Hayır |
| **AzureDataExplorerManagement** | Azure Veri Gezgini yönetimi. | Gelen | Hayır | Hayır |
| **AzureDataLake** | Azure Data Lake Storage 1.. | Giden | Hayır | Yes |
| **AzureDevSpaces** | Azure Dev Spaces. | Giden | Hayır | Hayır |
| **AzureDevOps** | Azure dev Ops.<br/><br/>*Not: Bu etiket Şu anda Azure Portal aracılığıyla yapılandırılamaz*| Gelen | Hayır | Yes |
| **AzureDigitalTwins** | Azure dijital TWINS.<br/><br/>*Note:* Bu etiket veya bu etiketin kapsadığı IP adresleri, olay yolları için yapılandırılmış uç noktalara erişimi kısıtlamak için kullanılabilir. *Bu etiket Şu anda Azure portalı aracılığıyla yapılandırılamaz* | Gelen | Hayır | Yes |
| **AzureEventGrid** | Azure Event Grid. | Her ikisi | Hayır | Hayır |
| **Azurefrontkapısı. ön uç** <br/> **Azurefrontkapısı. arka uç** <br/> **Azurefrontkapısı. Firstpartisi**  | Azure ön kapısı. | Her ikisi | Hayır | Hayır |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Note:* Bu etiketin **AzureActiveDirectory**, **Azurefrontkapı. ön uç** ve **Azurefrontkapısı. firstpartisi** etiketlerine bağımlılığı vardır. | Giden | Hayır | Hayır |
| **AzureIoTHub** | Azure IoT Hub. | Giden | Hayır | Hayır |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Note:* Bu etiketin **AzureActiveDirectory** etiketine bağımlılığı vardır. | Giden | Yes | Yes |
| **AzureLoadBalancer** | Azure altyapı yük dengeleyici. Etiketi, Azure sistem durumu araştırmalarının gerçekleştiği [konağın sanal IP adresine](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16) çevirir. Bu, arka uç kaynağınızın gerçek trafiğini değil yalnızca araştırma trafiği içerir. Azure Load Balancer kullanmıyorsanız, bu kuralı geçersiz kılabilirsiniz. | Her ikisi | Hayır | Hayır |
| **AzureMachineLearning** | Azure Machine Learning. | Her ikisi | Hayır | Yes |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon ve özel ölçümler (GB uç noktaları).<br/><br/>*Note:* Log Analytics için, **depolama** etiketi de gereklidir. Linux aracıları kullanılıyorsa **Guestandhybridmanagement** Tag de gereklidir. | Giden | Hayır | Yes |
| **AzureOpenDatasets** | Azure veri kümelerini açın.<br/><br/>*Note:* Bu etiketin **Azurefrontkapısı. ön uç** ve **depolama** etiketine bağımlılığı vardır. | Giden | Hayır | Hayır |
| **AzurePlatformDNS** | Temel altyapı (varsayılan) DNS hizmeti.<br/><br>Varsayılan DNS 'yi devre dışı bırakmak için bu etiketi kullanabilirsiniz. Bu etiketi kullanırken dikkatli olun. [Azure platformu konularını](./network-security-groups-overview.md#azure-platform-considerations)okumanızı öneririz. Ayrıca, bu etiketi kullanmadan önce test gerçekleştirmenizi öneririz. | Giden | Hayır | Hayır |
| **AzurePlatformIMDS** | Temel bir altyapı hizmeti olan Azure Instance Metadata Service (ıMDS).<br/><br/>Bu etiketi, varsayılan ıDS 'yi devre dışı bırakmak için kullanabilirsiniz. Bu etiketi kullanırken dikkatli olun. [Azure platformu konularını](./network-security-groups-overview.md#azure-platform-considerations)okumanızı öneririz. Ayrıca, bu etiketi kullanmadan önce test gerçekleştirmenizi öneririz. | Giden | Hayır | Hayır |
| **AzurePlatformLKM** | Windows lisanslama veya anahtar yönetimi hizmeti.<br/><br/>Lisanslama için Varsayılanları devre dışı bırakmak üzere bu etiketi kullanabilirsiniz. Bu etiketi kullanırken dikkatli olun. [Azure platformu konularını](./network-security-groups-overview.md#azure-platform-considerations)okumanızı öneririz.  Ayrıca, bu etiketi kullanmadan önce test gerçekleştirmenizi öneririz. | Giden | Hayır | Hayır |
| **AzureResourceManager** | Azure Resource Manager. | Giden | Hayır | Hayır |
| **AzureSignalR** | Azure SignalR. | Giden | Hayır | Hayır |
| **Azuresterecovery** | Azure Site Recovery.<br/><br/>*Note:* Bu etiketin **AzureActiveDirectory**, **AzureKeyVault**, **EventHub**,**Guestandhybridmanagement** ve **Storage** etiketlerine bir bağımlılığı vardır. | Giden | Hayır | Hayır |
| **AzureTrafficManager** | Azure Traffic Manager araştırması IP adresleri.<br/><br/>Traffic Manager araştırma IP adresleri hakkında daha fazla bilgi için bkz. [Azure TRAFFIC Manager SSS](../traffic-manager/traffic-manager-faqs.md). | Gelen | Hayır | Yes |  
| **BatchNodeManagement** | Azure Batch adanmış dağıtımlar için yönetim trafiği. | Her ikisi | Hayır | Yes |
| **Biliveservicesmanagement** | Azure bilişsel hizmetler için trafik için adres aralıkları. | Her ikisi | Hayır | Hayır |
| **DataFactory**  | Azure Data Factory | Her ikisi | Hayır | Hayır |
| **DataFactoryManagement** | Azure Data Factory için yönetim trafiği. | Giden | Hayır | Hayır |
| **Dynamics365ForMarketingEmail** | Dynamics 365 pazarlama e-posta hizmeti için adres aralıkları. | Giden | Yes | Hayır |
| **EventHub** | Azure Event Hubs. | Giden | Yes | Yes |
| **GatewayManager** | Azure VPN Gateway ve Application Gateway adanmış dağıtımlar için yönetim trafiği. | Gelen | Hayır | Hayır |
| **GuestAndHybridManagement** | Azure Otomasyonu ve konuk yapılandırması. | Giden | Hayır | Yes |
| **HDInsight** | Azure HDInsight. | Gelen | Yes | Hayır |
| **İnternet** | Sanal ağın dışında olan ve genel İnternet tarafından erişilebilen IP adresi alanı.<br/><br/>Adres aralığı, [Azure 'a ait genel IP adresi alanını](https://www.microsoft.com/download/details.aspx?id=41653)içerir. | Her ikisi | Hayır | Hayır |
| **LogicApps** | Logic Apps. | Her ikisi | Hayır | Hayır |
| **LogicAppsManagement** | Logic Apps için yönetim trafiği. | Gelen | Hayır | Hayır |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Giden | Hayır | Hayır |
| **MicrosoftContainerRegistry** | Microsoft Container görüntüleri için kapsayıcı kayıt defteri. <br/><br/>*Note:* Bu etiket, **Azurefrontkapısı. Firstpartisi** etiketine bir bağımlılığı vardır. | Giden | Yes | Yes |
| **PowerBI** | PowerBI. *Not: Bu etiket Şu anda Azure Portal aracılığıyla yapılandırılamaz.* | Her ikisi | Hayır | Hayır|
| **PowerQueryOnline** | Çevrimiçi Power Query. | Her ikisi | Hayır | Hayır |
| **ServiceBus** | Premium hizmet katmanını kullanan trafiği Azure Service Bus. | Giden | Yes | Yes |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Note:* Bu etiket, bölge başına denetim düzlemi için Service Fabric hizmet uç noktasını temsil eder. Bu, müşterilerin VNET 'lerden Service Fabric kümeleri için yönetim işlemleri gerçekleştirmesini sağlar (uç nokta örn. https://westus.servicefabric.azure.com) | Her ikisi | Hayır | Hayır |
| **SQL** | Azure SQL veritabanı, MySQL için Azure veritabanı, PostgreSQL için Azure veritabanı ve Azure SYNAPSE Analytics.<br/><br/>*Note:* Bu etiket hizmetin belirli örneklerini değil hizmeti temsil eder. Örneğin etiket belirli bir SQL veritabanını veya sunucusunu değil Azure SQL Veritabanı hizmetini temsil eder. Bu etiket SQL yönetilen örneği için uygulanmıyor. | Giden | Yes | Yes |
| **SqlManagement** | SQL adanmış dağıtımlar için yönetim trafiği. | Her ikisi | Hayır | Yes |
| **Depolama** | Azure Depolama. <br/><br/>*Note:* Bu etiket hizmetin belirli örneklerini değil hizmeti temsil eder. Örneğin etiket belirli bir Azure Depolama hesabını değil Azure Depolama hizmetini temsil eder. | Giden | Yes | Yes |
| **StorageSyncService** | Depolama eşitleme hizmeti. | Her ikisi | Hayır | Hayır |
| **WindowsVirtualDesktop** | Windows sanal masaüstü. | Her ikisi | Hayır | Yes |
| **VirtualNetwork** | Sanal ağ adres alanı (sanal ağ için tanımlanan tüm IP adresi aralıkları), bağlı olan tüm şirket içi adres [alanları, eşlenen sanal ağlar](virtual-network-peering-overview.md) , [sanal ağ geçidine](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)bağlı sanal ağlar, [ana bilgisayarın sanal IP adresi](./network-security-groups-overview.md#azure-platform-considerations)ve [Kullanıcı tanımlı yollar](virtual-networks-udr-overview.md)üzerinde kullanılan adres önekleri. Bu etiket varsayılan yollar da içerebilir. | Her ikisi | Hayır | Hayır |

>[!NOTE]
>Klasik dağıtım modelinde (Azure Resource Manager önce), önceki tabloda listelenen etiketlerin bir alt kümesi desteklenir. Bu Etiketler farklı şekilde yazılmış:
>
>| Klasik yazım | Eşdeğer Kaynak Yöneticisi etiketi |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | İnternet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure hizmetlerinin hizmet etiketleri, kullanılan belirli buluttan gelen adres öneklerini gösterir. Örneğin, Azure genel bulutundaki **SQL** etiketi değerine karşılık gelen temel IP aralıkları, Azure Çin bulutundaki temel aralıklardan farklı olacaktır.

> [!NOTE]
> Azure Depolama veya Azure SQL Veritabanı gibi bir hizmet için bir [sanal ağ hizmet uç noktası](virtual-network-service-endpoints-overview.md) uygularsanız Azure, sanal ağ alt ağına hizmet için bir [rota](virtual-networks-udr-overview.md#optional-default-routes) ekler. Rotadaki adres önekleri, karşılık gelen hizmet etiketiyle aynı adres ön ekleri veya CıDR aralıklarıdır.

## <a name="service-tags-on-premises"></a>Şirket içi hizmet etiketleri  
Şirket içi güvenlik duvarı yapılandırmalarının bir parçası olarak dahil edilecek geçerli hizmet etiketi ve Aralık bilgilerini elde edebilirsiniz. Bu bilgiler, her bir hizmet etiketine karşılık gelen IP aralıklarının geçerli zaman noktası listesidir. Aşağıdaki bölümlerde açıklandığı gibi, bilgileri programlı bir şekilde veya bir JSON dosya indirme aracılığıyla elde edebilirsiniz.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Hizmet etiketi bulma API 'sini (Genel Önizleme) kullanma
Hizmet etiketlerinin geçerli listesini, IP adres aralığı ayrıntıları ile birlikte program aracılığıyla alabilirsiniz:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Azure CLI](/cli/azure/network#az_network_list_service_tags)

> [!NOTE]
> Yeni hizmet etiketi verilerinin API sonuçlarına yayılması 4 hafta kadar sürer. Bu gerçekleştiğinde, yanıt meta verilerindeki değişiklik numarası artacaktır. Farklı konum değerleri belirtildiğinde sonuçlarda geçici farklılıklar olabilir. NSG kuralları oluşturmak için sonuçları kullanırken, konum parater ' ı NSG 'nin bölgesiyle eşleşecek şekilde ayarlamanız gerekir. 

> [!NOTE]
> API verileri, şu anda indirilebilir JSON dosyasındaki etiketlerin bir alt kümesi olan NSG kuralları ile kullanılabilen etiketleri temsil eder. Genel önizleme aşamasında, verilerin bir güncelleştirmeden sonraki bir güncelleştirmeden aynı kalacağından emin vermeyiz. 

### <a name="discover-service-tags-by-using-downloadable-json-files"></a>İndirilebilir JSON dosyalarını kullanarak hizmet etiketlerini bulma 
Hizmet etiketlerinin geçerli listesini içeren JSON dosyalarını, IP adresi aralığı ayrıntıları ile birlikte indirebilirsiniz. Bu listeler haftalık olarak güncelleştirilir ve yayımlanır. Her bulutun konumları şunlardır:

- [Azure genel](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure ABD Kamu](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure Çin](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Almanya](https://www.microsoft.com/download/details.aspx?id=57064)   

Bu dosyalardaki IP adresi aralıkları CıDR gösteriminde. 

> [!NOTE]
>Bu bilgilerin bir alt kümesi, [Azure genel](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Çin](https://www.microsoft.com/download/details.aspx?id=42064)ve [Azure Almanya](https://www.microsoft.com/download/details.aspx?id=54770)için XML dosyalarında yayımlanmıştır. Bu XML İndirmeleri 30 Haziran 2020 ' den kullanım dışı kalacak ve bu tarihten sonra kullanılamayacak. Önceki bölümlerde açıklandığı şekilde bulma API 'sini veya JSON dosya indirmelerini kullanarak geçiş yapmalısınız.

### <a name="tips"></a>İpuçları 
- JSON dosyasındaki daha fazla *ChangeNumber* değerini belirterek bir yayından sonraki güncelleştirmeleri tespit edebilirsiniz. Her alt bölüm (örneğin, **Storage. WestUS**), değişiklik gerçekleştiğinde arttırılan kendi *ChangeNumber* değerini içerir. Alt bölümleri değiştirildiğinde dosyanın *ChangeNumber* en üst düzeyi artırılır.
- Hizmet etiketi bilgilerini ayrıştırmayla ilgili örnekler için (örneğin, WestUS ' de depolama için tüm adres aralıklarını alma), [hizmet etiketi bulma API PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag) belgelerine bakın.
- Hizmet etiketlerine yeni IP adresleri eklendiğinde, bu adresler Azure 'da en az bir hafta boyunca kullanılmaz. Bu, hizmet etiketleriyle ilişkili IP adreslerini izlemek için gerekebilecek tüm sistemleri güncelleştirmeniz için size zaman kazandırır.

## <a name="next-steps"></a>Sonraki adımlar
- [Ağ güvenlik grubu oluşturmayı](tutorial-filter-network-traffic.md)öğrenin.
