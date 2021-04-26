---
title: Azure Cloud Services 'de dağıtımları değiştirme veya geçiş (genişletilmiş destek)
description: Azure Cloud Services (genişletilmiş destek) ile dağıtım arasında geçiş yapmayı veya geçişi yapmayı öğrenin.
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f5e01075ffb460c7ddd70b40a6b19f7ea70dd776
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748847"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Azure Cloud Services 'de dağıtımları değiştirme veya geçiş (genişletilmiş destek)

Azure Cloud Services (genişletilmiş destek) ile iki bağımsız bulut hizmeti dağıtımı arasında takas yapabilirsiniz. Azure Cloud Services 'den (klasik) farklı olarak, Azure Cloud Services 'daki Azure Resource Manager modeli (genişletilmiş destek) dağıtım yuvalarını kullanmaz. Azure Cloud Services 'de (genişletilmiş destek), bir bulut hizmetinin yeni bir sürümünü dağıtırken, bulut hizmetini Azure Cloud Services (genişletilmiş destek) ' de var olan bir bulut hizmetiyle "takas edilebilir" yapabilirsiniz.

Dağıtımları değiştirdikten sonra, yeni bulut hizmeti dağıtımını kullanarak yeni sürümü kullanıma alabilir ve test edebilirsiniz. Aslında, takas, üretim sürümü için hazırlanan yeni bir bulut hizmeti yükseltir.

> [!NOTE]
> Azure Cloud Services (klasik) dağıtımı ve Azure Cloud Services (genişletilmiş destek) dağıtımı arasında geçiş yapamazsınız.

Bir bulut hizmeti çiftinin ikinci kısmını dağıtırken, bulut hizmetini başka bir bulut hizmeti ile değiştirilebilir hale getirebilirsiniz.

Azure Resource Manager şablonu (ARM şablonu), Azure portal veya REST API kullanarak dağıtımları takas edebilirsiniz.

## <a name="arm-template"></a>ARM şablonu

Bir ARM şablonu dağıtım yöntemi kullanıyorsanız, bulut hizmetlerini değiştirilebilir hale getirmek için `SwappableCloudService` `networkProfile` `cloudServices` nesnesindeki özelliğini EŞLEŞTIRILMIŞ bulut hizmetinin kimliğine ayarlayın:

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Azure portalı

Azure portal bir dağıtımı değiştirmek için:

1. Portal menüsünde **Cloud Services (genişletilmiş destek)** veya **Pano**' yı seçin.
1. Güncelleştirmek istediğiniz bulut hizmetini seçin.
1. Bulut hizmeti **'Ne genel bakış** bölümünde **takas**' ı seçin:

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="Bulut hizmeti için takas sekmesini gösteren ekran görüntüsü.":::

1. Değiştirme onayı bölmesinde, dağıtım bilgilerini doğrulayın ve ardından dağıtımları değiştirmek için **Tamam** ' ı seçin:

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="Dağıtım takas bilgisinin onaylandığını gösteren ekran görüntüsü.":::

Yapılan tek şey, dağıtılan bulut hizmeti için sanal IP adresi olduğundan, dağıtımlar hızla takas edilir.

İşlem maliyetlerini kaydetmek için, bulunan bulut hizmetinizin beklendiği gibi çalıştığını doğruladıktan sonra bulut hizmetlerinden birini (uygulamanızın dağıtımı için hazırlama ortamı olarak belirlenmiş) silebilirsiniz.

## <a name="rest-api"></a>REST API

REST API kullanarak Azure Cloud Services (genişletilmiş destek) yeni bir bulut hizmetleri dağıtımına geçiş yapmak için aşağıdaki komutu ve JSON yapılandırmasını kullanın:

```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```

```json
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
}
```

## <a name="common-questions-about-swapping-deployments"></a>Dağıtımları değiştirme hakkında sık sorulan sorular

Azure Cloud Services 'de dağıtım takas (genişletilmiş destek) hakkında sık sorulan sorulara yönelik bu yanıtları gözden geçirin.

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>Yeni bir bulut hizmetleri dağıtımına değiştirme önkoşulları nelerdir?

Azure Cloud Services başarılı bir dağıtım takas işlemi için iki temel önkoşulları karşılamanız gerekir (genişletilmiş destek):

* Değiştirilebilir bulut hizmetlerinden biri için statik veya ayrılmış bir IP adresi kullanmak istiyorsanız, diğer bulut hizmeti de ayrılmış bir IP adresi kullanmalıdır. Aksi takdirde, değiştirme başarısız olur.
* Değiştirme işleminin başarılı olması için rollerinizin tüm örneklerinin çalışıyor olması gerekir. Örneklerinizin durumunu denetlemek için, Azure portal, yeni dağıtılan bulut hizmeti için **Genel Bakış ' a** gidin veya `Get-AzRole` Windows PowerShell 'de komutunu kullanın.

Konuk işletim sistemi güncelleştirmeleri ve hizmet düzeltme işlemleri bir dağıtım değiştirmenin başarısız olmasına neden olabilir. Daha fazla bilgi için bkz. [bulut hizmeti dağıtımlarında sorun giderme](../cloud-services/cloud-services-troubleshoot-deployment-problems.md).

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Bir VIP takasını, başka bir değiştirici işlemle paralel yapabilir miyim?

Hayır. Bir VIP takas, bir bulut hizmetinde başka bir işlem işlemi başlatılmadan önce yalnızca ağ salt ağ değişikdir. Bir bulut hizmeti için bir güncelleştirme, silme veya otomatik ölçeklendirme işlemi başlatmak veya başka bir işlem işlemi devam ederken bir VIP takas tetiklenmesi, bulut hizmetini kurtarılamaz bir hata durumuna yerleştirebilir.

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>Bir değiştirme uygulamam için kapalı kalma süresi doğurur ve bunu nasıl işleymem gerekir?

Yalnızca Azure Yük dengeleyicide bir yapılandırma değişikliği olduğundan, bulut hizmeti takası genellikle hızlıdır. Bazı durumlarda, takas 10 veya daha fazla saniye sürebilir ve geçici bağlantı hatalarıyla sonuçlanır. Kullanıcıların değiştirme efektini sınırlamak için, istemci yeniden deneme mantığını uygulamayı düşünün.

## <a name="next-steps"></a>Sonraki adımlar 

* Azure Cloud Services [dağıtım önkoşullarını](deploy-prerequisite.md) gözden geçirin (genişletilmiş destek).
* Azure Cloud Services (genişletilmiş destek) [hakkında sık sorulan soruları](faq.md) gözden geçirin.
* Aşağıdaki seçeneklerden birini kullanarak bir Azure Cloud Services (genişletilmiş destek) bulut hizmeti dağıtın:
  * [Azure portalı](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [ARM şablonu](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)
