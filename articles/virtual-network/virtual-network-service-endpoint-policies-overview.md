---
title: Azure sanal ağ hizmet uç noktası ilkeleri | Microsoft Docs
description: Hizmet Uç Noktası İlkelerini kullanarak Azure hizmet kaynaklarına yönelik Sanal Ağ trafiğini filtrelemeyi öğrenin
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 633d0b9e454fe2906d98624ac2cdbcc9a8b6c7fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022271"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Azure Depolama için sanal ağ hizmet uç noktası ilkeleri

Sanal ağ (VNet) hizmet uç noktası ilkeleri, çıkış sanal ağ trafiğini hizmet uç noktası üzerinden Azure depolama hesaplarına filtrelemenize ve verilerin yalnızca belirli Azure depolama hesaplarına sızmasına izin verebilir. Uç nokta ilkeleri, hizmet uç noktası üzerinden bağlanılırken Azure Storage 'a sanal ağ trafiği için ayrıntılı erişim denetimi sağlar.

![Azure depolama hesaplarına yönelik sanal ağ giden trafiği güvenli hale getirme](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Bu özellik __Genel Azure bölgelerinde__ __Azure depolama__ için genel kullanıma sunulmuştur.

## <a name="key-benefits"></a>Önemli avantajlar

Sanal ağ hizmet uç noktası ilkeleri aşağıdaki avantajları sağlar:

- __Azure depolama 'ya yönelik sanal ağ trafiğiniz için geliştirilmiş güvenlik__

  [Ağ güvenlik grupları Için Azure hizmet etiketleri](./network-security-groups-overview.md) , sanal ağ giden trafiği belirli Azure depolama bölgeleriyle sınırlamanıza izin verir. Ancak, bu, seçili Azure depolama bölgesi içindeki herhangi bir hesaba giden trafiğe izin verir.
  
  Uç nokta ilkeleri, sanal ağ giden erişimine izin verilen Azure depolama hesaplarını belirtmenizi sağlar ve diğer tüm depolama hesaplarına erişimi kısıtlar. Bu, sanal ağınızdan veri alımını korumak için çok daha ayrıntılı güvenlik denetimi sağlar.

- __Azure hizmet trafiğini filtrelemek için ölçeklenebilir, yüksek kullanılabilirliğe sahip ilkeler__

   Uç nokta ilkeleri, hizmet uç noktaları üzerinden sanal ağlardan gelen Azure hizmeti trafiğini filtrelemek için yatay olarak ölçeklenebilir, yüksek kullanılabilirliğe sahip bir çözüm sağlar. Sanal ağlarınızda bu trafiğe yönelik merkezi ağ aletleri bulundurmak için ek yük gerekmez.

## <a name="json-object-for-service-endpoint-policies"></a>Hizmet uç noktası ilkeleri için JSON nesnesi
Hizmet uç noktası Ilke nesnesine hızlıca göz atalım.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Yapılandırma

-   Uç nokta ilkelerini, sanal ağ trafiğini belirli Azure depolama hesaplarına kısıtlamak üzere yapılandırabilirsiniz.
-   Uç nokta ilkesi bir sanal ağ içindeki alt ağ üzerinde yapılandırılır. İlkeyi uygulamak için alt ağda Azure Storage hizmet uç noktaları etkinleştirilmelidir.
-   Uç nokta ilkesi, RESOURCEID biçimini kullanarak, izin verilenler listesine belirli Azure depolama hesapları eklemenize olanak tanır. Erişimini kısıtlayabilirsiniz
    - bir abonelikteki tüm depolama hesapları<br>
      `E.g. /subscriptions/subscriptionId`

    - bir kaynak grubundaki tüm depolama hesapları<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - karşılık gelen Azure Resource Manager RESOURCEID dökümünü yaparak bireysel bir depolama hesabı. Bu bloblara, tablolara, kuyruklara, dosyalara ve Azure Data Lake Storage 2. Nesil'e yönelik trafiği kapsar. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Varsayılan olarak, uç noktalar içeren bir alt ağa hiçbir ilke iliştirilmişse, hizmette tüm depolama hesaplarına erişebilirsiniz. Alt ağda bir ilke yapılandırıldıktan sonra, o alt ağdaki işlem örneklerinden yalnızca ilkede belirtilen kaynaklara erişilebilir. Diğer tüm depolama hesaplarına erişim reddedilecek.
-   Hizmet uç noktası ilkeleri bir alt ağa uygulanırken, Azure depolama *hizmeti uç noktası kapsamı* bölgesel sunucudan **genel**'e yükseltilir. Bu, Azure Storage 'a giden tüm trafiğin bundan sonra hizmet uç noktası üzerinden güvenli hale getirildiği anlamına gelir. Hizmet uç noktası ilkeleri Ayrıca genel olarak da geçerlidir. bu nedenle, açıkça izin verilmeyen depolama hesaplarının erişimi reddedilir. 
-   Bir alt ağa birden çok ilke uygulayabilirsiniz. Alt ağla birden çok ilke ilişkilendirildiğinde, bu ilkelerin hiçbirinde belirtilen kaynaklara olan sanal ağ trafiğine izin verilir. İlkelerden hiçbirinde belirtilmeyen diğer tüm hizmet kaynaklarına yönelik erişim reddedilir.

    > [!NOTE]  
    > Hizmet uç noktası ilkeleri **ilkelere izin veriyor**, bu nedenle belirtilen kaynaklardan ayrı olarak tüm diğer kaynaklar kısıtlıdır. Lütfen uygulamalarınıza yönelik tüm hizmet kaynak bağımlılıklarının ilkede tanımlanmış ve listede listelendiğinden emin olun.

- Uç nokta ilkesinde yalnızca Azure Dağıtım Modeli'ni kullanan depolama hesapları belirtilebilir. Klasik Azure depolama hesaplarınız, Azure hizmet uç noktası Ilkelerini desteklemez.
- Birincil hesap listelenirse RA-GRS ikincil erişimine otomatik olarak izin verilir.
- Depolama hesapları sanal ağ ile aynı veya farklı abonelikte ya da Azure Active Directory kiracısında yer alabilir.

## <a name="scenarios"></a>Senaryolar

- **Eşleştirilmiş, bağlı veya birden çok sanal ağ**: Eşleştirilmiş sanal ağlarda trafiği filtrelemek için, uç nokta ilkelerinin bu sanal ağlara tek tek uygulanması gerekir.
- **Internet trafiğini ağ gereçlerine veya Azure Güvenlik Duvarı Ile filtreleme**: Azure hizmet trafiğini ilkelerle, hizmet uç noktaları üzerinden filtreleyin ve cihazlar ya da Azure Güvenlik Duvarı aracılığıyla Internet veya Azure trafiğinin kalanını filtreleyin.
- **Sanal ağlara dağıtılan Azure hizmetlerindeki trafiği Filtreleme**: Şu anda, sanal ağınıza dağıtılan herhangi bir yönetilen Azure hizmeti Için Azure hizmet uç noktası ilkeleri desteklenmez. 
- **Şirket içinden Azure hizmetlerine yönelik trafiği filtreleme**: Hizmet uç noktası ilkeleri yalnızca ilkelerle ilişkilendirilmiş uç noktalardan gelen trafiğe uygulanır. Şirket içinden belirli Azure hizmet kaynaklarına yönelik trafiğe izin vermek için, trafiğin ağ sanal cihazları veya güvenlik duvarları kullanılarak filtrelenmesi gerekir.

## <a name="logging-and-troubleshooting"></a>Günlüğe kaydetme ve sorun giderme
Hizmet uç noktası ilkelerinde hiçbir merkezi günlük sağlanmaz. Hizmet kaynak günlükleri için bkz. [hizmet uç noktaları günlüğe kaydetme](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Sorun giderme senaryoları
- Önizleme aşamasında çalışan depolama hesaplarına erişim reddedildi (coğrafi eşlenmiş bölgede değil)
  - Azure Storage 'ın küresel hizmet etiketlerini kullanmak için yükseltilmesi sayesinde, hizmet uç noktası kapsamı ve bu nedenle hizmet uç noktası ilkeleri artık geneldir. Bu nedenle, Azure depolama 'ya giden tüm trafik hizmet uç noktaları üzerinden şifrelenir ve yalnızca ilkede açık olarak listelenen depolama hesaplarına erişim izni verilir.
  - Erişimi geri yüklemek için gerekli tüm depolama hesaplarının listesiyle açıkça izin ver.  
  - Azure desteğine başvurun.
- Uç nokta ilkelerinde listelenen hesaplar için erişim reddedildi
  - Ağ güvenlik grupları veya güvenlik duvarı filtrelemesi erişimi engelliyor olabilir
  - İlkenin kaldırılması/yeniden uygulanması bağlantı kaybına yol açıyorsa:
    - Azure hizmetinin uç noktalar üzerinden sanal ağdan erişime izin verecek şekilde yapılandırılıp yapılandırılmadığını veya kaynak için varsayılan ilkenin *tümüne Izin ver* olarak ayarlanmış olup olmadığını doğrulayın.
    - Hizmet tanılamalarında uç noktalar üzerinden trafiğin gösterildiğini doğrulayın.
    - Ağ güvenlik grubu akış günlüklerinde erişimin gösterilip gösterilmediğini ve depolama günlüklerinde beklendiği gibi hizmet uç noktaları üzerinden erişimin gösterilip gösterilmediğini denetleyin.
    - Azure desteğine başvurun.
- Hizmet uç noktası ilkelerinde listelenmeyen hesaplar için erişim reddedildi
  - Azure Storage 'ın uç noktalar üzerinden sanal ağdan erişime izin verecek şekilde yapılandırılıp yapılandırılmadığını veya kaynak için varsayılan ilkenin *tümüne Izin ver* olarak ayarlanmış olup olmadığını doğrulayın.
  - Hesapların alt ağdaki hizmet uç noktası ilkelerine sahip **Klasik depolama hesapları** olmadığından emin olun.
- Yönetilen bir Azure hizmeti, alt ağ üzerinden bir hizmet uç noktası Ilkesi uygulandıktan sonra çalışmayı durdurdu
  - Yönetilen hizmetler şu anda hizmet uç noktası ilkeleriyle desteklenmez. *Güncelleştirmeler için bu alanı izleyin*.
- Yönetilen depolama hesaplarına erişim, alt ağ üzerinden bir hizmet uç noktası Ilkesi uygulandıktan sonra çalışmayı durdurdu
  - Yönetilen depolama hesapları, hizmet uç noktası ilkeleriyle desteklenmez. Yapılandırıldıysa, ilkeler varsayılan olarak tüm yönetilen depolama hesaplarına erişimi reddeder. Uygulamanızın yönetilen depolama hesaplarına erişmesi gerekiyorsa, bu trafik için uç nokta ilkeleri kullanılmamalıdır.

## <a name="provisioning"></a>Sağlama

Hizmet uç noktası ilkeleri, sanal ağda yazma erişimine sahip bir kullanıcı tarafından alt ağlarda yapılandırılabilir. Azure [yerleşik rolleri](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [özel rollere](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) belirli izinlerin atanması hakkında daha fazla bilgi edinin.

Sanal ağlar ve Azure depolama hesapları aynı veya farklı aboneliklerde veya Azure Active Directory kiracılarda olabilir.

## <a name="limitations"></a>Sınırlamalar

- Hizmet uç noktası ilkelerini yalnızca Azure Resource Manager dağıtım modeli üzerinden dağıtılmış olan sanal ağlarda dağıtabilirsiniz.
- Sanal ağların hizmet uç noktası ilkesiyle aynı bölgede bulunması gerekir.
- Hizmet uç noktası ilkesini bir alt ağa uygulayabilmeniz için, ilkede listelenen Azure hizmetleri için hizmet uç noktalarının yapılandırılmış olması gerekir.
- Şirket içi ağınızdan Azure hizmetlerine yönelik trafik için hizmet uç noktası ilkelerini kullanamazsınız.
- Azure yönetilen hizmetler şu anda uç nokta ilkelerini desteklemez. Bu, paylaşılan alt ağlara dağıtılan yönetilen Hizmetleri (örn. *Azure Batch, Azure ekleme, azure Application Gateway, azure VPN Gateway, Azure Güvenlik Duvarı*) veya ayrılmış alt ağlara (örn. *Azure App Service ortamı, Azure Redis Cache, Azure API Management, Azure SQL mı, klasik yönetilen hizmetler*) dahil olmak üzere.

 > [!WARNING]
 > Sanal ağınıza dağıtılan Azure HDInsight gibi Azure hizmetleri, altyapı gereksinimlerinden dolayı Azure Depolama gibi diğer Azure hizmetlerine erişir. Uç nokta ilkesini belirli kaynaklarla kısıtlamak, sanal ağınızda dağıtılan Azure hizmetleri için bu altyapı kaynaklarına erişimi kesebilir.

- Klasik depolama hesapları, uç nokta ilkelerinde desteklenmez. İlkeler varsayılan olarak tüm klasik depolama hesaplarına erişimi reddedecektir. Uygulamanızın Azure Resource Manager'a ve klasik depolama hesaplarına erişmesi gerekiyorsa, bu trafik için uç nokta ilkeleri kullanılmamalıdır.

## <a name="pricing-and-limits"></a>Fiyatlandırma ve limitler

Hizmet uç noktası ilkelerinin kullanımından ek ücret alınmaz. Hizmet uç noktaları üzerinden Azure hizmetleri (Azure Depolama gibi) için güncel fiyatlandırma modeli uygulanır.

Hizmet uç noktası ilkelerinde aşağıdaki limitler zorunlu tutulur: 

 |Kaynak | Varsayılan limit |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpointPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Sonraki Adımlar

- [Sanal ağ hizmet uç noktası ilkelerini nasıl yapılandıracağınızı](virtual-network-service-endpoint-policies-portal.md) öğrenin
- [Sanal Ağ Hizmet Uç Noktaları](virtual-network-service-endpoints-overview.md) hakkında daha fazla bilgi edinin
