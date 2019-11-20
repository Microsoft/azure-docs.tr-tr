---
title: Klasik Azure dağıtım kaynaklarını taşıma
description: Klasik dağıtım kaynaklarını yeni bir kaynak grubuna veya aboneliğe taşımak için Azure Resource Manager kullanın.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: b97496e4abfdf248b9f5010417e9284c643a74ad
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150844"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Klasik dağıtım modeli kaynakları için kılavuzu taşıma

Klasik model aracılığıyla dağıtılan kaynakları taşıma adımları, kaynakları bir abonelik içinde veya yeni bir aboneliğe taşısanız göre farklılık gösterir.

## <a name="move-in-the-same-subscription"></a>Aynı abonelikte taşı

Aynı abonelik içindeki başka bir kaynak grubu için bir kaynak grubundan kaynakları taşırken aşağıdaki kısıtlamalar uygulanır:

* Sanal ağlar (Klasik) taşınamaz.
* Sanal makineler (Klasik) bulut hizmeti ile taşınmalıdır.
* Bulut hizmeti, tüm sanal makineleri taşıma içerdiğinde yalnızca taşınabilir.
* Bir kerede yalnızca bir bulut hizmeti taşınabilir.
* Bir kerede yalnızca bir depolama hesabı (Klasik) taşınabilir.
* Depolama hesabı (Klasik), bir sanal makine veya Bulut hizmeti ile aynı işlemde taşınamaz.

Klasik kaynakları aynı abonelik içindeki yeni bir kaynak grubuna taşımak için Portal, Azure PowerShell, Azure CLı veya REST API aracılığıyla [Standart taşıma işlemlerini](../resource-group-move-resources.md) kullanın. Resource Manager kaynaklarını taşımak için kullandığınız gibi işlemlerin aynısını kullanın.

## <a name="move-across-subscriptions"></a>Abonelikler arasında geçiş

Kaynakları yeni bir aboneliğe taşınmasını, aşağıdaki kısıtlamalar uygulanır:

* Abonelikteki tüm Klasik kaynaklar aynı işlem içinde taşınmalıdır.
* Hedef aboneliğin klasik başka bir kaynağı olmamalıdır.
* Taşıma, yalnızca klasik taşıma için ayrı bir REST API aracılığıyla istenebilir. Klasik kaynakları için yeni bir abonelik taşırken standart Resource Manager'a taşıma komutlar çalışmaz.

Klasik kaynakları için yeni bir aboneliği taşımak, Klasik kaynakları için özel REST işlemlerini kullanın. REST 'i kullanmak için aşağıdaki adımları uygulayın:

1. Kaynak abonelik bir çapraz abonelik taşıma katılabilir, kontrol edin. Aşağıdaki işlemi kullanın:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     İstek gövdesinde şunları içerir:

   ```json
   {
    "role": "source"
   }
   ```

     Doğrulama işleminde yanıta aşağıdaki biçimdedir:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Hedef abonelik bir çapraz abonelik taşıma katılabilir, kontrol edin. Aşağıdaki işlemi kullanın:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     İstek gövdesinde şunları içerir:

   ```json
   {
    "role": "target"
   }
   ```

     Kaynak abonelik doğrulama ile aynı biçimde yanıttır.
1. Her iki abonelik doğrulama testlerini geçerse, tüm Klasik kaynaklar bir abonelikten şu işlemi başka bir aboneliğe Taşı:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    İstek gövdesinde şunları içerir:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

İşlemi birkaç dakika çalışabilir.

## <a name="next-steps"></a>Sonraki adımlar

Klasik kaynakları taşırken sorun yaşarsanız [desteğe](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)başvurun.

Kaynakları taşıma komutları için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../resource-group-move-resources.md).
