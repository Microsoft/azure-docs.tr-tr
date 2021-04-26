---
title: Yönetilen sanal ağ & yönetilen özel uç noktaları
description: Azure Data Factory yönetilen sanal ağ ve yönetilen özel uç noktalar hakkında bilgi edinin.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: 1213d5f7421cc71255f29d013fa47878559110ee
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481591"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory yönetilen sanal ağ (Önizleme)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Azure Data Factory yönetilen sanal ağ ve yönetilen özel uç noktalar açıklanmaktadır.


## <a name="managed-virtual-network"></a>Yönetilen sanal ağ

Azure Data Factory yönetilen sanal ağ (VNET) içinde bir Azure Integration Runtime (IR) oluşturduğunuzda, tümleştirme çalışma zamanı yönetilen sanal ağla birlikte sağlanır ve desteklenen veri depolarına güvenli bir şekilde bağlanmak için özel uç noktalardan yararlanır. 

Yönetilen sanal ağ içinde Azure IR oluşturmak, veri tümleştirme işleminin yalıtılmış ve güvenli olmasını sağlar. 

Yönetilen sanal ağ kullanmanın avantajları:

- Yönetilen bir sanal ağla, sanal ağın Azure Data Factory yönetiminin yükünü devretmek için yük devretme gerçekleştirebilirsiniz. Azure Integration Runtime için bir alt ağ oluşturmanız gerekmez, bu, sonunda sanal ağınızdan çok sayıda özel IP kullanabilir ve önceki ağ altyapısı planlamasını gerektirebilir. 
- Veri tümleştirmelerini güvenli bir şekilde yapmak için derin Azure ağ bilgileri gerektirmez. Bunun yerine, güvenli ETL ile çalışmaya başlamak, veri mühendisleri için çok basitleştirilmiştir. 
- Yönetilen özel uç noktaları ile birlikte yönetilen sanal ağ, veri taşmakla karşı koruma sağlar. 

> [!IMPORTANT]
>Şu anda, yönetilen VNet yalnızca Azure Data Factory bölgesiyle aynı bölgede desteklenir.
 

![ADF yönetilen sanal ağ mimarisi](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Yönetilen özel uç noktalar

Yönetilen özel uç noktalar, Azure kaynaklarına özel bir bağlantı kurarak Azure Data Factory yönetilen sanal ağda oluşturulan özel uç noktalardır. Azure Data Factory bu özel uç noktaları sizin adınıza yönetir. 

![Yeni yönetilen özel uç nokta](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory özel bağlantıları destekler. Özel bağlantı, Azure (PaaS) hizmetlerine (Azure depolama, Azure Cosmos DB, Azure SYNAPSE Analytics gibi) erişmenizi sağlar.

Özel bir bağlantı kullandığınızda, veri depolarınız ve yönetilen sanal ağınız arasındaki trafik tamamen Microsoft omurga ağı üzerinden geçer. Özel bağlantı, veri savunma risklerine karşı koruma sağlar. Özel bir uç nokta oluşturarak kaynağa özel bir bağlantı kurarsınız.

Özel uç nokta, hizmeti etkin bir şekilde kendisine getirmek için yönetilen sanal ağda özel bir IP adresi kullanır. Özel uç noktalar Azure 'daki belirli bir kaynakla eşlenir ve hizmetin tamamı değildir. Müşteriler, şirket tarafından onaylanan belirli bir kaynakla bağlantıyı sınırlayabilir. [Özel bağlantılar ve özel uç noktalar](../private-link/index.yml)hakkında daha fazla bilgi edinin.

> [!NOTE]
> Tüm Azure veri kaynaklarınıza bağlanmak için yönetilen özel uç noktalar oluşturmanız önerilir. 
 
> [!WARNING]
> PaaS veri deposu (blob, ADLS 2., Azure SYNAPSE Analytics) üzerinde zaten oluşturulmuş bir özel uç nokta varsa ve tüm ağlardan erişime izin veriyorsa bile, ADF yalnızca yönetilen özel uç nokta kullanarak erişebilir. Bu senaryolarda özel bir uç nokta oluşturduğunuzdan emin olun. 

Azure Data Factory içinde yönetilen bir özel uç nokta oluşturduğunuzda bir "bekleyen" durumunda özel bir uç nokta bağlantısı oluşturulur. Bir onay iş akışı başlatılır. Özel bağlantı kaynağı sahibi bağlantıyı onaylaması veya reddetmekten sorumludur.

![Özel uç noktayı Yönet](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Sahip bağlantıyı onayladığında, özel bağlantı oluşturulur. Aksi takdirde, özel bağlantı kurulmaz. Her iki durumda da, yönetilen özel uç nokta bağlantının durumuyla güncelleştirilir.

![Yönetilen özel uç noktayı Onayla](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Yalnızca onaylanan bir durumdaki yönetilen özel uç nokta, belirli bir özel bağlantı kaynağına trafik gönderebilir.

## <a name="interactive-authoring"></a>Etkileşimli yazma
Etkileşimli yazma özellikleri, test bağlantısı, klasör listesi ve tablo listesi, şema al ve Önizleme verileri gibi işlevler için kullanılır. ADF tarafından yönetilen sanal ağdaki bir Azure Integration Runtime oluştururken veya düzenlerken etkileşimli yazma özelliğini etkinleştirebilirsiniz. Arka uç hizmeti, etkileşimli yazma işlevleri için işlem ön ayırmasını alacak. Aksi takdirde, işlem, daha fazla zaman alacak herhangi bir etkileşimli işlem gerçekleştirildiğinde tahsis edilir. Etkileşimli yazma için yaşam süresi (TTL) 60 dakikadır. Bu, son etkileşimli yazma işleminin 60 dakika sonra otomatik olarak devre dışı bırakılacağından oluşur.

![Etkileşimli yazma](./media/managed-vnet/interactive-authoring.png)

## <a name="create-managed-virtual-network-via-azure-powershell"></a>Azure PowerShell aracılığıyla yönetilen sanal ağ oluşturma
```powershell
$subscriptionId = ""
$resourceGroupName = ""
$factoryName = ""
$managedPrivateEndpointName = ""
$integrationRuntimeName = ""
$apiVersion = "2018-06-01"
$privateLinkResourceId = ""

$vnetResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default"
$privateEndpointResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default/managedprivateendpoints/${managedPrivateEndpointName}"
$integrationRuntimeResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/integrationRuntimes/${integrationRuntimeName}"

# Create managed Virtual Network resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${vnetResourceId}"

# Create managed private endpoint resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${privateEndpointResourceId}" -Properties @{
        privateLinkResourceId = "${privateLinkResourceId}"
        groupId = "blob"
    }

# Create integration runtime resource enabled with VNET
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${integrationRuntimeResourceId}" -Properties @{
        type = "Managed"
        typeProperties = @{
            computeProperties = @{
                location = "AutoResolve"
                dataFlowProperties = @{
                    computeType = "General"
                    coreCount = 8
                    timeToLive = 0
                }
            }
        }
        managedVirtualNetwork = @{
            type = "ManagedVirtualNetworkReference"
            referenceName = "default"
        }
    }

```

## <a name="limitations-and-known-issues"></a>Sınırlamalar ve bilinen sorunlar
### <a name="supported-data-sources"></a>Desteklenen Veri Kaynakları
ADF tarafından yönetilen sanal ağdan özel bağlantı üzerinden bağlanmak için aşağıdaki veri kaynaklarının altında desteklenir.
- Azure Blob depolama (depolama hesabı v1 dahil değil)
- Azure Tablo Depolama (depolama hesabı v1 dahil değil)
- Azure dosyaları (depolama hesabı v1 dahil değil)
- Azure Data Lake Gen2
- Azure SQL veritabanı (Azure SQL yönetilen örneği dahil değil)
- Azure Synapse Analytics
- Azure CosmosDB SQL
- Azure Key Vault
- Azure özel bağlantı hizmeti
- Azure Search
- MySQL için Azure Veritabanı
- PostgreSQL için Azure Veritabanı
- MariaDB için Azure Veritabanı

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory yönetilen sanal ağ aşağıdaki Azure bölgelerinde kullanılabilir:
- Doğu ABD
- Doğu ABD 2
- Orta Batı ABD
- Batı ABD
- Batı ABD 2
- Orta Güney ABD
- Central US
- Kuzey Avrupa
- West Europe
- Güney Birleşik Krallık
- Güneydoğu Asya
- Doğu Avustralya
- Güneydoğu Avustralya
- Norveç Doğu
- Doğu Japonya
- Batı Japonya
- Güney Kore - Orta
- Güney Brezilya
- Orta Fransa
- İsviçre Kuzey
- Batı Birleşik Krallık
- Doğu Kanada
- Orta Kanada

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>ADF tarafından yönetilen sanal ağdan gelen genel uç nokta aracılığıyla giden iletişimler
- Giden iletişimler için yalnızca bağlantı noktası 443 açılır.
- Azure Depolama ve Azure Data Lake 2. Nesil, ADF Yönetilen Sanal Ağı'ndan genel uç nokta aracılığıyla bağlantı için desteklenmez.

### <a name="linked-service-creation-of-azure-key-vault"></a>Bağlı hizmet oluşturma Azure Key Vault 
- Azure Key Vault için Bağlı Hizmet oluşturduğunuzda Azure Integration Runtime başvurusu yoktur. Bu nedenle Azure Key Vault bağlı hizmet oluşturma sırasında özel uç nokta oluşturamazsınız. Ancak, Azure Key Vault bağlı hizmete başvuran ve bu bağlı hizmet, yönetilen sanal ağ ile Azure Integration Runtime başvuran veri depoları için bağlı hizmet oluşturduğunuzda, oluşturma sırasında Azure Key Vault bağlı hizmeti için özel bir uç nokta oluşturabilirsiniz. 
- Azure Key Vault bağlı hizmeti için **test bağlantısı** IŞLEMI yalnızca URL biçimini doğrular, ancak herhangi bir ağ işlemi yapmaz.
- Özel uç nokta **kullanan** sütun, Azure Key Vault Için özel uç nokta oluştursanız bile her zaman boş olarak gösterilir.
![AKV için özel uç nokta](./media/managed-vnet/akv-pe.png)

## <a name="next-steps"></a>Sonraki adımlar

- Öğretici: [yönetilen sanal ağ ve özel uç noktaları kullanarak kopyalama işlem hattı oluşturma](tutorial-copy-data-portal-private.md) 
- Öğretici: [yönetilen sanal ağ ve özel uç noktaları kullanarak eşleme veri akışı işlem hattı oluşturma](tutorial-data-flow-private.md)