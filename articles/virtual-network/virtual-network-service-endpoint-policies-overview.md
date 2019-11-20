---
title: Azure sanal ağ hizmet uç noktası ilkeleri | Microsoft Docs
description: Hizmet Uç Noktası İlkelerini kullanarak Azure hizmet kaynaklarına yönelik Sanal Ağ trafiğini filtrelemeyi öğrenin
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumi
ms.openlocfilehash: 86726eefb53638036a4e9207c648bf5ffe6c866e
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595376"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Sanal ağ hizmet uç noktası ilkeleri (Önizleme)

Sanal Ağ (VNet) hizmet uç noktası ilkeleri Azure hizmetlerine yönelik sanal ağ trafiğini filtrelemenizi sağlar. Böylelikle hizmet uç noktaları üzerinden yalnızca belirli Azure hizmet kaynaklarına izin verilir. Uç nokta ilkeleri Azure hizmetlerine yönelik sanal ağ trafiği için ayrıntılı erişim denetimi sağlar.

Bu özellik aşağıdaki Azure hizmetleri ve bölgeleri için __önizleme__ sürümündedir:

__Azure depolama__: WestCentralUS, WestUS2, NorthCentralUS, SouthCentralUS, CentralUS, EastUS2.

En güncel önizleme bildirimleri için [Azure Sanal Ağ güncelleştirmeleri](https://azure.microsoft.com/updates/?product=virtual-network) sayfasını gözden geçirin.

> [!NOTE]  
> Önizleme sırasında sanal ağ hizmet uç noktası ilkeleri genel kullanılabilirlik sürümündeki özelliklerle aynı düzeyde kullanılabilirliğe ve güvenilirliğe sahip olmayabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Microsoft Azure Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Önemli avantajlar

Sanal ağ hizmet uç noktası ilkeleri aşağıdaki avantajları sağlar:

- __Azure Hizmetlerine yönelik Sana Ağ trafiğiniz geçin geliştirilmiş güvenlik__

  [Ağ güvenlik grupları için Azure hizmet etiketleri](https://aka.ms/servicetags), sanal ağ giden trafiğini belirli Azure hizmetleriyle kısıtlamanıza olanak tanır. Öte yandan, söz konusu Azure hizmetinin tüm kaynaklarına yönelik trafiğe izin verir. 
  
  Uç nokta ilkeleriyle, artık sanal ağ diğer erişimini yalnızca belirli Azure kaynaklarıyla kısıtlayabilirsiniz. Bu sayede, sanal ağınızda erişilen verileri korumak için daha ayrıntılı bir güvenlik denetimine sahip olursunuz. 

- __Azure hizmet trafiğini filtrelemek için ölçeklenebilir, yüksek kullanılabilirliğe sahip ilkeler__

   Uç nokta ilkeleri, hizmet uç noktaları üzerinden sanal ağlardan gelen Azure hizmeti trafiğini filtrelemek için yatay olarak ölçeklenebilir, yüksek kullanılabilirliğe sahip bir çözüm sağlar. Sanal ağlarınızda bu trafiğe yönelik merkezi ağ aletleri bulundurmak için ek yük gerekmez.

## <a name="configuration"></a>Yapılandırma

- Sanal ağ trafiğini belirli Azure hizmet kaynaklarıyla kısıtlamak için uç nokta ilkelerini yapılandırabilirsiniz. Önizleme aşamasında, Azure Depolama için uç nokta ilkelerini destekliyoruz. 
- Uç nokta ilkesi bir sanal ağ içindeki alt ağ üzerinde yapılandırılır. İlkede listelenen tüm Azure hizmetlerinde, ilkeyi uygulamak için alt ağda hizmet uç noktaları etkinleştirilmelidir.
- Uç nokta ilkesi, resourceID biçimini kullanarak belirli Azure hizmet kaynaklarını beyaz listeye almanızı sağlar. Abonelikteki veya kaynak grubundaki tüm kaynaklara erişimi kısıtlayabilirsiniz. Ayrıca, belirli kaynaklara erişimi de kısıtlayabilirsiniz. 
- Varsayılan olarak, uç noktalarla bir alt ağa hiçbir ilke bağlanmazsa, hizmetteki tüm kaynaklara erişebilirsiniz. Alt ağda bir ilke yapılandırıldıktan sonra, o alt ağdaki işlem örneklerinden yalnızca ilkede belirtilen kaynaklara erişilebilir. Belirli bir hizmet için diğer tüm kaynaklara yönelik erişim reddedilir. 
- Hizmet uç noktasının yapılandırıldığı bölgelerde Azure hizmet kaynaklarına yönelik trafiği filtreleyebilirsiniz. Diğer bölgelerdeki hizmet kaynaklarına yönelik erişime varsayılan olarak izin verilir. 

  > [!NOTE]  
  > Hizmet uç noktası bölgelerindeki Azure kaynaklarına yönelik sanal ağ giden erişimini tümüyle kilitlemek için, [hizmet etiketlerini](security-overview.md#service-tags) kullanarak yalnızca hizmet uç noktası bölgelerine yönelik trafiğe izin verecek şekilde yapılandırılmış ağ güvenlik grubu ilkelerine de ihtiyacınız vardır.

- Bir alt ağa birden çok ilke uygulayabilirsiniz. Aynı hizmet için alt ağ ile birden çok ilke ilişkilendirildiğinde, bu ilkelerden herhangi birinde belirtilen kaynaklara yönelik sanal ağ trafiğine izin verilir. İlkelerden hiçbirinde belirtilmeyen diğer tüm hizmet kaynaklarına yönelik erişim reddedilir. 

  > [!NOTE]  
  > İlke yalnızca sanal ağdan listelenen hizmet kaynaklarına erişime izin verir. İlkeye belirli kaynaklar eklediğinizde, hizmete yönelik diğer tüm trafik otomatik olarak reddedilir. Uygulamalarınız için tüm hizmet kaynağı bağımlılıklarının tanımlanabildiğinden ve ilkede listelendiğinden emin olun.

  > [!WARNING]  
  > Sanal ağınıza dağıtılan Azure HDInsight gibi Azure hizmetleri, altyapı gereksinimlerinden dolayı Azure Depolama gibi diğer Azure hizmetlerine erişir. Uç nokta ilkesini belirli kaynaklarla kısıtlamak, sanal ağınızda dağıtılan hizmetler için bu altyapı kaynaklarına erişimi kesebilir. Belirli hizmetler için [Sınırlamalar](#limitations)'a bakın Önizleme sırasında, sanal ağınıza dağıtılan hiçbir yönetilen Azure hizmeti için hizmet uç noktası ilkeleri desteklenmez.

- Azure Depolama için: 
  -  Depolama hesabının Azure Resource Manager *resourceId* değerini listeleyerek erişimi kısıtlayabilirsiniz. Bu bloblara, tablolara, kuyruklara, dosyalara ve Azure Data Lake Storage 2. Nesil'e yönelik trafiği kapsar.

     Örneğin, Azure depolama hesapları uç nokta ilkesi tanımında aşağıdaki gibi listelenebilir:
    
     Belirli bir depolama hesabına izin vermek için:         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     Abonelikteki ve kaynak grubundaki tüm hesaplara izin vermek için: `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     Abonelikteki tüm hesaplara izin vermek için: `/subscriptions/subscriptionId`
    
- Uç nokta ilkesinde yalnızca Azure Dağıtım Modeli'ni kullanan depolama hesapları belirtilebilir.  

  > [!NOTE]  
  > Klasik depolama hesaplarına erişim uç nokta ilkeleriyle engellenir.

- Listelenen hesabın birincil konumu, alt ağ için hizmet uç noktasının geo-pair bölgeleri içinde yer almalıdır. 

  > [!NOTE]  
  > İlkeler, diğer bölgelerden hizmet kaynaklarının belirtilmesine izin verir. Azure hizmetlerine sanal ağ erişimi yalnızca geo-pair bölgeleri için filtrelenir. Azure Depolama için ağ güvenlik grupları geo-pair bölgeleriyle kısıtlanmamışsa, sanal ağ geo-pair bölgeleri dışındaki tüm depolama hesaplarına erişebilir.

- Birincil hesap listelenirse RA-GRS ikincil erişimine otomatik olarak izin verilir. 
- Depolama hesapları sanal ağ ile aynı veya farklı abonelikte ya da Azure Active Directory kiracısında yer alabilir. 

## <a name="limitations"></a>Sınırlamalar

- Hizmet uç noktası ilkelerini yalnızca Azure Resource Manager dağıtım modeli üzerinden dağıtılmış olan sanal ağlarda dağıtabilirsiniz.
- Sanal ağların hizmet uç noktası ilkesiyle aynı bölgede bulunması gerekir.
- Hizmet uç noktası ilkesini bir alt ağa uygulayabilmeniz için, ilkede listelenen Azure hizmetleri için hizmet uç noktalarının yapılandırılmış olması gerekir.
- Şirket içi ağınızdan Azure hizmetlerine yönelik trafik için hizmet uç noktası ilkelerini kullanamazsınız.
- Altyapı gereksinimleri için Azure hizmetlerine bağımlılığı olan yönetilen Azure hizmetlerinin bulunduğu alt ağlara uç nokta ilkeleri uygulanmamalıdır. 

  > [!WARNING]  
  > Sanal ağınıza dağıtılan Azure HDInsight gibi Azure hizmetleri, altyapı gereksinimlerinden dolayı Azure Depolama gibi diğer Azure hizmetlerine erişir. Uç nokta ilkesini belirli kaynaklarla kısıtlamak, sanal ağınızda dağıtılan Azure hizmetleri için bu altyapı kaynaklarına erişimi kesebilir.
  
  - Bazı Azure hizmetleri başka işlem örnekeri olan alt ağlara dağıtılabilir. Aşağıda listelenen yönetilen hizmetlerin dağıtıldığı alt ağa uç nokta ilkelerinin uygulanmadığından emin olun.
   
    - Azure HDInsight
    - Azure Batch (Azure Resource Manager)
    - Azure Active Directory Domain Services (Azure Resource Manager)
    - Azure Application Gateway (Azure Resource Manager)
    - Azure VPN Gateway (Azure Resource Manager)
    - Azure Güvenlik Duvarı

  - Bazı Azure hizmetleri ayrılmış alt ağlara dağıtılır. Önizleme boyunca, uç nokta ilkeleri aşağıda listelenen bu tür tüm hizmetlerde engellenir. 

     - Azure App Service Ortamı
     - Azure Rediscache
     - Azure API Management
     - Azure SQL Yönetilen Örnek
     - Azure Active Directory Domain Services
     - Azure Application Gateway (Klasik)
     - Azure VPN Gateway (Klasik)

- Azure Depolama: Uç nokta ilkelerinde Klasik depolama hesapları desteklenmez. İlkeler varsayılan olarak tüm klasik depolama hesaplarına erişimi reddedecektir. Uygulamanızın Azure Resource Manager'a ve klasik depolama hesaplarına erişmesi gerekiyorsa, bu trafik için uç nokta ilkeleri kullanılmamalıdır. 

## <a name="nsgs-with-service-endpoint-policies"></a>Hizmet Uç Nokta İlkeleri ile NSG'ler
- Varsayılan olarak NSG'ler, Azure hizmetlerine sanal ağ trafiği de dahil olmak üzere giden İnternet trafiğine izin verir.
- Giden İnternet trafiğinin tümünü reddetmek ve yalnızca belirli Azure hizmeti kaynaklarına giden trafiğe izin vermek istiyorsanız: 

  1\. adım: Nsg'ler yalnızca kullanan Azure hizmetlerine uç nokta bölgelerde, giden trafiğe izin verecek şekilde yapılandırma *Azure hizmet etiketlerini*. Daha fazla bilgi için bkz. [NSG'ler için hizmet etiketleri](https://aka.ms/servicetags)
      
  Örneğin, erişimi yalnızca uç nokta bölgeleriyle kısıtlayan ağ güvenlik grubu kuralları aşağıdaki örneğe benzer:

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  2\. adım: Hizmet uç noktası İlkesi erişim yalnızca belirli bir Azure hizmet kaynakları için geçerlidir.

  > [!WARNING]  
  > Ağ güvenlik grubu sanal ağın Azure hizmeti erişimini uç nokta bölgeleriyle sınırlayacak şekilde yapılandırılmadıysa, hizmet uç nokta ilkesi uygulanmış olsa bile diğer bölgelerdeki hizmet kaynaklarına erişebilirsiniz.

## <a name="scenarios"></a>Senaryolar

- **Eşleştirilmiş, bağlanmış veya birden çok sanal ağ**: Eşlenen sanal ağlardaki trafiği filtrelemek için uç noktası ilkeleri bu sanal ağları için ayrı ayrı uygulanması gerekir.
- **Ağ Gereçleri veya Azure güvenlik duvarı ile Internet trafiğini filtreleme**: Uç noktalar ilkeleri, Azure hizmet trafiğini filtrelemek ve cihazları veya Azure güvenlik duvarı üzerinden İnternet'te veya Azure trafiğinin geri kalanı filtreleyin. 
- **Azure hizmetlerinde dağıtılan sanal ağlara trafiği filtreleme**: Önizleme sırasında sanal ağınıza dağıtılmış yönetilen Azure Hizmetleri için hizmet uç noktası İlkesi desteklenmez. 
 Belirli hizmetler için [kısıtlamalara](#limitations) bakın.
- **Şirket içi ortamdan Azure hizmetlerine trafiği filtreleme**: Hizmet uç noktası İlkesi, ilkeleri için ilişkili alt ağlar'dan yalnızca trafiğe uygulanır. Şirket içinden belirli Azure hizmet kaynaklarına yönelik trafiğe izin vermek için, trafiğin ağ sanal cihazları veya güvenlik duvarları kullanılarak filtrelenmesi gerekir.

## <a name="logging-and-troubleshooting"></a>Günlüğe kaydetme ve sorun giderme
Hizmet uç noktası ilkelerinde hiçbir merkezi günlük sağlanmaz. Hizmet tanılama günlükleri için bkz. [Hizmet uç noktaları günlüğü](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Sorun giderme senaryoları
- Uç nokta ilkelerinde listelenmeyen depolama hesaplarına erişim izni verildi
  - Ağ güvenlik grupları diğer bölgelerde İnternet veya Azure Depolama hesaplarına erişim izni verebilir.
  - Ağ güvenlik grupları tüm giden İnternet trafiğini reddedecek ve yalnızca belirli Azure Depolama bölgelerine yönelik trafiğe izin verecek şekilde yapılandırılmalıdır. Ağ güvenlik grupları Ayrıntılar için bkz.
- Uç nokta ilkelerinde listelenen hesaplar için erişim reddedildi
  - Ağ güvenlik grupları veya güvenlik duvarı filtrelemesi erişimi engelliyor olabilir
  - İlkenin kaldırılması/yeniden uygulanması bağlantı kaybına yol açıyorsa:
    - Azure hizmetinin uç noktalar üzerinden sanal ağdan erişime izin verecek şekilde yapılandırıldığını veya kaynağın varsayılan ilkesinin *Tümüne İzin Ver* olarak ayarlandığını doğrulayın.
      > [!NOTE]      
      > Uç nokta ilkeleri üzerinde erişim için hizmet kaynakları sanal ağlara karşı güvenlik altına alınmamış olmalıdır. Bununla birlikte, en iyi güvenlik yöntemlerinden biri olarak hizmet kaynaklarının Azure sanal ağlarınız gibi güvenilen ağlarınızla hizmet uç noktaları üzerinden ve şirket içinde IP güvenlik duvarı üzerinden güvenlik altına alınmasını öneririz.
  
    - Hizmet tanılamalarında uç noktalar üzerinden trafiğin gösterildiğini doğrulayın.
    - Ağ güvenlik grubu akış günlüklerinde erişimin gösterilip gösterilmediğini ve depolama günlüklerinde beklendiği gibi hizmet uç noktaları üzerinden erişimin gösterilip gösterilmediğini denetleyin.
    - Azure desteğine başvurun.
- Hizmet uç noktası ilkelerinde listelenmeyen hesaplar için erişim reddedildi
  - Ağ güvenlik grupları veya güvenlik duvarı filtrelemesi erişimi engelliyor olabilir. Uç nokta bölgeleri için *Azure Depolama* hizmet etiketine izin verildiğinden emin olun. İlke kısıtlamaları için bkz: [Sınırlamalar](#limitations).
  Örneğin, ilke uygulanırsa klasik depolama hesaplarının erişimi reddedilir.
  - Azure hizmetinin uç noktalar üzerinden sanal ağdan erişime izin verecek şekilde yapılandırılıp yapılandırılmadığını veya kaynağın varsayılan ilkesinin *Tümüne İzin Ver* olarak ayarlanıp ayarlanmadığını denetleyin.

## <a name="provisioning"></a>Sağlama

Hizmet uç noktası ilkeleri, sanal ağda yazma erişimine sahip bir kullanıcı tarafından alt ağlarda yapılandırılabilir. Azure [yerleşik rolleri](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [özel rollere](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) belirli izinlerin atanması hakkında daha fazla bilgi edinin.

Sanal ağlar ve Azure hizmet kaynakları aynı ağda veya farklı aboneliklerde ya da Azure Active Directory kiracılarında olabilir. 

## <a name="pricing-and-limits"></a>Fiyatlandırma ve limitler

Hizmet uç noktası ilkelerinin kullanımından ek ücret alınmaz. Hizmet uç noktaları üzerinden Azure hizmetleri (Azure Depolama gibi) için güncel fiyatlandırma modeli uygulanır.

Hizmet uç noktası ilkelerinde aşağıdaki limitler zorunlu tutulur: 

 |Resource | Varsayılan limit |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Sonraki Adımlar

- [Sanal ağ hizmet uç noktası ilkelerini nasıl yapılandıracağınızı](virtual-network-service-endpoint-policies-portal.md) öğrenin
- [Sanal Ağ Hizmet Uç Noktaları](virtual-network-service-endpoints-overview.md) hakkında daha fazla bilgi edinin

