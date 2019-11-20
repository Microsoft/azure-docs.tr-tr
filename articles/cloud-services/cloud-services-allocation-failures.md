---
title: Bulut hizmeti ayırma hatası giderme | Microsoft Docs
description: Azure’da Cloud Services dağıtımı sırasında oluşan ayırma hatalarını giderme
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 470778e5c441bb05ffc7c5e1c5ef97a6c30d3359
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155632"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Azure’da Cloud Services dağıtımı sırasında oluşan ayırma hatalarını giderme
## <a name="summary"></a>Özet
Bir bulut hizmetine örnekler dağıtırken veya yeni Web ya da çalışan rolü örnekleri eklediğinizde Microsoft Azure işlem kaynaklarını ayırır. Azure abonelik limitlerine ulaşmadan önce bu işlemleri gerçekleştirirken zaman zaman bir hata alabilirsiniz. Bu makalede, bazı yaygın ayırma hatalarının nedenleri açıklanmaktadır ve olası düzeltme önerilir. Ayrıca, hizmetlerinizin dağıtımını planlarken bilgiler de yararlı olabilir.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Arka plan – ayırmanın nasıl çalıştığı
Azure veri merkezlerindeki sunucular kümelere bölünmüştür. Birden çok kümede yeni bir bulut hizmeti ayırma isteği yapılmaya çalışıldı. İlk örnek bir bulut hizmetine dağıtıldığında (herhangi bir hazırlama veya üretimde), bu bulut hizmeti bir kümeye sabitlenir. Bulut hizmeti için başka dağıtımlar da aynı kümede olur. Bu makalede, "bir kümeye sabitlenmiş" olarak bu makaleye başvuracağız. Aşağıdaki şekil 1 ' de, birden çok kümede denendiği normal ayırmanın durumu gösterilmektedir; Diyagram 2 ' de, var olan bulut hizmeti CS_1 barındırıldığından, küme 2 ' ye sabitlenmiş bir ayırmanın durumu gösterilmektedir.

![Ayırma diyagramı](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Ayırma hatası neden meydana gelir
Bir ayırma isteği bir kümeye sabitlendiğinde, kullanılabilir kaynak havuzu bir kümeyle sınırlı olduğundan, ücretsiz kaynakları bulmasının daha yüksek bir olasılığı vardır. Ayrıca, ayırma isteğiniz bir kümeye sabitlenir ancak istediğiniz kaynak türü bu küme tarafından desteklenmiyorsa, kümede boş kaynak olsa bile isteğiniz başarısız olur. Aşağıdaki şekil 3 ' te, tek aday kümenin ücretsiz kaynakları olmadığından, sabitlenmiş bir ayırmanın başarısız olduğu durum gösterilmektedir. Diyagram 4 ' te, küme boş kaynaklara sahip olsa bile, tek aday kümesi istenen VM boyutunu desteklemediğinden, sabitlenmiş bir ayırmanın başarısız olduğu durum gösterilmektedir.

![Sabitlenmiş ayırma hatası](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Cloud Services için ayırma başarısızlığının sorunlarını giderme
### <a name="error-message"></a>Hata İletisi
Aşağıdaki hata iletisini görebilirsiniz:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Genel Sorunlar
Bir ayırma isteğinin tek bir kümeye sabitlenememesine neden olan ortak ayırma senaryoları aşağıda verilmiştir.

* Hazırlama yuvasına dağıtım-bir bulut hizmetinin her iki yuvada da bir dağıtımı varsa, tüm bulut hizmeti belirli bir kümeye sabitlenmiştir.  Diğer bir deyişle, üretim yuvasında bir dağıtım zaten varsa, yeni bir hazırlama dağıtımı yalnızca üretim yuvası ile aynı kümede ayrılabilir. Küme kapasiteye yaklaştığı takdirde istek başarısız olabilir.
* Ölçeklendirme-var olan bir bulut hizmetine yeni örnekler eklemek aynı kümede olmalıdır.  Küçük ölçeklendirme istekleri her zaman olmasa da genellikle ayrılabilir. Küme kapasiteye yaklaştığı takdirde istek başarısız olabilir.
* Benzeşim grubu-bulut hizmeti bir benzeşim grubuna sabitlenmemişse, boş bir bulut hizmetine yeni bir dağıtım, bu bölgedeki herhangi bir kümede yapı tarafından ayrılabilir. Aynı benzeşim grubuna yönelik dağıtımlar aynı kümede denenmeyecektir. Küme kapasiteye yaklaştığı takdirde istek başarısız olabilir.
* Benzeşim grubu vNet-daha eski sanal ağlar bölgeler yerine benzeşim gruplarına bağlandı ve bu sanal ağlardaki bulut hizmetleri benzeşim grubu kümesine sabitlenmiştir. Bu sanal ağ türüne yapılan dağıtımlar sabitlenmiş kümede denenmeyecektir. Küme kapasiteye yaklaştığı takdirde istek başarısız olabilir.

## <a name="solutions"></a>Çözümler
1. Yeni bir bulut hizmetine yeniden dağıtın-bu çözüm, platformun bu bölgedeki tüm kümelerden seçim yapmasına izin verdiğinden en başarılı olma olasılığı yüksektir.

   * İş yükünü yeni bir bulut hizmetine dağıtma  
   * CNAME veya bir kaydı, trafiği yeni bulut hizmetine işaret etmek üzere güncelleştirme
   * Sıfır trafik eski siteye gittikten sonra eski bulut hizmetini silebilirsiniz. Bu çözümün sıfır kapalı kalma süresi olması gerekir.
2. Hem üretim hem de hazırlama yuvalarını Sil-Bu çözüm, var olan DNS adınızı korur, ancak uygulamanızda kapalı kalma süresine yol açacaktır.

   * Bulut hizmetinin boş olması için mevcut bir bulut hizmetinin üretim ve hazırlama yuvalarını silin ve ardından
   * Mevcut bulut hizmetinde yeni bir dağıtım oluşturun. Bu, bölgedeki tüm kümelerdeki ayırmayı yeniden dener. Bulut hizmetinin bir benzeşim grubuna bağlı olmadığından emin olun.
3. Ayrılmış IP-bu çözüm, mevcut IP adresinizi koruyacaktır, ancak uygulamanızda kesinti yapılmasına neden olur.  

   * PowerShell kullanarak mevcut dağıtımınız için bir ReservedIP oluşturma

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Hizmetin CSCFG öğesinde yeni ReservedIP 'yi belirttiğinizden emin olmak için yukarıdaki #2 izleyin.
4. Yeni dağıtımlar için benzeşim grubunu Kaldır-benzeşim grupları artık önerilmez. Yeni bir bulut hizmeti dağıtmak için yukarıdaki 1 numaralı öğeye yönelik adımları izleyin. Bulut hizmetinin bir benzeşim grubunda olmadığından emin olun.
5. Bölgesel sanal ağa dönüştürme- [benzeşim gruplarından bir bölgesel sanal ağa (VNet) geçiş](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)yapma konusuna bakın.
