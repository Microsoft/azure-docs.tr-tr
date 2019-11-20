---
title: Dayanıklı işlevler faturalandırma-Azure Işlevleri
description: Dayanıklı İşlevler iç davranışları ve bunların Azure Işlevleri için faturalandırmayı nasıl etkilediği hakkında bilgi edinin.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: f2de6bdf24aa1a0a11349c8f0ec9b3995b026a47
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694893"
---
# <a name="durable-functions-billing"></a>Dayanıklı İşlevler faturalandırma

[Dayanıklı işlevler](durable-functions-overview.md) Azure işlevleriyle aynı şekilde faturalandırılır. Daha fazla bilgi için bkz. [Azure işlevleri fiyatlandırması](https://azure.microsoft.com/pricing/details/functions/).

Azure Işlevleri [Tüketim planında](../functions-scale.md#consumption-plan)Orchestrator işlevlerini yürütürken bazı faturalandırma davranışlarından haberdar olmanız gerekir. Aşağıdaki bölümlerde bu davranışlar ve bunların etkileri daha ayrıntılı olarak açıklanır.

## <a name="orchestrator-function-replay-billing"></a>Orchestrator işlevi yeniden yürütme faturalandırma

[Orchestrator işlevleri](durable-functions-orchestrations.md) , bir Orchestration 'un ömrü boyunca birkaç kez tekrar çalışabilir. Her yeniden yürütme işlemi, Azure Işlevleri çalışma zamanı tarafından ayrı bir işlev çağrısı olarak görüntülenir. Bu nedenle, Azure Işlevleri tüketim planında bir Orchestrator işlevinin her yeniden oynaması için faturalandırılırsınız. Diğer plan türleri Orchestrator işlevi yeniden yürütmeyi ücretlendirilmez.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Orchestrator işlevleri bekleniyor ve sürüyor

Bir Orchestrator işlevi bir zaman uyumsuz eylemin, C# JavaScript 'te **await** veya **yield** kullanarak tamamlanmasını bekliyorsa, çalışma zamanı belirli yürütmenin bitmesini kabul eder. Orchestrator işlevinin faturalandırması bu noktada sona erer. Sonraki Orchestrator işlevi yeniden yürütmeden önce bu işlemi sürdürmez. Orchestrator işlevinde bekleyen veya olmayan herhangi bir zaman faturalandırılmaz.

> [!NOTE]
> Diğer işlevleri çağıran işlevler bazıları bir kötü model olarak değerlendirilir. Bunun nedeni, _Double faturalandırma_olarak bilinen bir sorundur. Bir işlev doğrudan başka bir işlevi çağırdığında, her ikisi de aynı anda çalışır. Çağrılan işlev, çağırma işlevi bir yanıt beklerken kodu etkin bir şekilde çalıştırır. Bu durumda, çağırma işlevinin çağrılan işlevin çalışmasını beklerken geçen süre için ödeme yapmanız gerekir.
>
> Orchestrator işlevlerinde bir Double faturalandırma yoktur. Bir Orchestrator işlevinin faturalandırması, bir etkinlik işlevi veya alt düzenleme sonucunu beklerken duraklar.

## <a name="durable-http-polling"></a>Kalıcı HTTP yoklaması

Orchestrator işlevleri, [http özellikleri makalesinde](durable-functions-http-features.md)açıklandığı gibi dış uç noktalara uzun süre çalışan http çağrıları yapabilir. C# İçindeki **CallHttpAsync** yöntemi ve JavaScript 'teki **callhttp** yöntemi, [zaman uyumsuz 202 modelini](durable-functions-http-features.md#http-202-handling)takip ederken bir HTTP uç noktasını dahili olarak yoklayabilirler.

Şu anda iç HTTP yoklama işlemleri için doğrudan faturalandırma yoktur. Ancak iç yoklama, Orchestrator işlevinin düzenli olarak yeniden oynamasına neden olabilir. Bu iç işlev yeniden yürütüldüğünde standart ücretler faturalandırılırsınız.

## <a name="azure-storage-transactions"></a>Azure depolama işlemleri

Dayanıklı İşlevler, durumu kalıcı tutmak, iletileri işlemek ve BLOB kiraları aracılığıyla bölümleri yönetmek için varsayılan olarak Azure Storage 'ı kullanır. Bu depolama hesabına sahip olduğunuzdan, tüm işlem maliyetleri Azure aboneliğinize faturalandırılır. Dayanıklı İşlevler tarafından kullanılan Azure depolama yapıtları hakkında daha fazla bilgi için bkz. [görev hub 'ları makalesi](durable-functions-task-hubs.md).

Dayanıklı İşlevler uygulamanız tarafından tahakkuk eden gerçek Azure depolama maliyetlerine birçok etken katkıda bulunur:

* Tek bir işlev uygulaması, bir dizi Azure depolama kaynağını paylaşan tek bir görev hub 'ı ile ilişkilendirilir. Bu kaynaklar, bir işlev uygulamasındaki tüm dayanıklı işlevler tarafından kullanılır. İşlev uygulamasındaki gerçek işlev sayısı, Azure depolama işlem maliyetlerine hiçbir etkiye sahip değildir.
* Her işlev uygulaması örneği, bir üstel geri alma yoklama algoritması kullanarak depolama hesabındaki birden çok kuyruğu dahili olarak yoklar. Boş bir uygulama örneği, kuyrukları etkin bir uygulama uygulamaktan daha az bir şekilde yoklar, bu da daha az işlem maliyeti elde etmez. Dayanıklı İşlevler kuyruğu-yoklama davranışı hakkında daha fazla bilgi için [performans ve ölçek makalesinin sıra yoklaması bölümüne](durable-functions-perf-and-scale.md#queue-polling)bakın.
* Azure işlevleri tüketimine veya Premium planlarında çalışırken, [Azure işlevleri ölçek denetleyicisi](../functions-scale.md#how-the-consumption-and-premium-plans-work) arka plandaki tüm görev hub kuyruklarını düzenli aralıklarla yoklar. Bir işlev uygulaması hafif ve orta ölçekli ölçeğe sahip ise, yalnızca tek bir ölçek denetleyicisi örneği bu kuyrukları yoklayacaktır. İşlev uygulaması çok sayıda örneğe ölçeklenirken, daha fazla ölçek denetleyicisi örneği eklenebilir. Bu ek ölçek denetleyicisi örnekleri toplam kuyruk işlem maliyetlerini artırabilir.
* Her işlev uygulama örneği bir blob kiraları kümesi için. Bu örnekler, tutulan kiraları yenilemek veya yeni kiralamalar edinmeyi denemek için düzenli aralıklarla Azure Blob hizmetine çağrı yapar. Görev merkezinin yapılandırılan bölüm sayısı, blob kiralamaları sayısını belirler. Çok sayıda işlev uygulaması örneğine genişleme, büyük olasılıkla bu kira işlemleriyle ilişkili Azure depolama işlem maliyetlerini artırır.

Azure [depolama fiyatlandırma belgelerindeki Azure](https://azure.microsoft.com/pricing/details/storage/) Depolama fiyatlandırması hakkında daha fazla bilgi edinebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Işlevleri fiyatlandırması hakkında daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/functions/)
