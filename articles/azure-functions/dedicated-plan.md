---
title: Azure Işlevleri adanmış barındırma
description: Adanmış bir App Service barındırma planında Azure Işlevleri çalıştırmanın avantajları hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: a9f976eda4a551c302ba7df92fbdbbf7a4fce1d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704574"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Azure Işlevleri için adanmış barındırma planları

Bu makale, işlev uygulamanızı bir App Service Ortamı (Ao) dahil olmak üzere bir App Service planında barındırmakla ilgilidir. Diğer barındırma seçenekleri için [barındırma planı makalesine](functions-scale.md)bakın.

App Service planı, bir uygulamanın çalışması için bir işlem kaynakları kümesi tanımlar. Bu işlem kaynakları geleneksel barındırma içindeki [_sunucu grubuna_](https://wikipedia.org/wiki/Server_farm) benzerdir. Bir veya daha fazla işlev uygulaması, Web uygulamaları gibi diğer App Service uygulamalarla aynı bilgi işlem kaynaklarında (App Service planında) çalışacak şekilde yapılandırılabilir. Bu planlar temel, standart, Premium ve yalıtılmış SKU 'Ları içerir. App Service planının nasıl çalıştığı hakkında daha fazla bilgi için bkz. [ayrıntılı genel bakış Azure App Service planları](../app-service/overview-hosting-plans.md).

Aşağıdaki durumlarda bir App Service planı düşünün:

* Zaten başka App Service örnekleri çalıştıran, az önce kullanılan VM 'Ler var.
* İşlevlerinizin çalıştırılacağı özel bir görüntü sağlamak istiyorsunuz.

## <a name="billing"></a>Faturalandırma

İşlev uygulamaları için, diğer App Service kaynakları için yaptığınız gibi App Service bir planda ödeme yaparsınız. Bu, tüketim tabanlı maliyet bileşenlerine sahip olan Azure Işlevleri [Tüketim planı](consumption-plan.md) veya [Premium plan](functions-premium-plan.md) barındırmakla farklılık gösterir. Plan için, planda kaç işlev uygulaması veya Web uygulaması çalıştırıldığına bakılmaksızın yalnızca plan için faturalandırılırsınız. Daha fazla bilgi edinmek için [App Service fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/app-service/windows/)bakın. 

## <a name="always-on"></a><a name="always-on"></a> Her zaman açık

App Service bir planda çalıştırırsanız, işlev uygulamanızın doğru çalışması için **Always on** ayarını etkinleştirmeniz gerekir. Bir App Service planında, işlevler çalışma zamanı birkaç dakikadan sonra boş kalır, bu nedenle yalnızca HTTP Tetikleyicileri "uyandırır" olur. **Her zaman açık** ayarı yalnızca bir App Service planında kullanılabilir. Tüketim planında, platform işlev uygulamalarını otomatik olarak etkinleştirir.

Her zaman etkinleştirilmiş olsa bile, tek tek işlevlerde yürütme zaman aşımı, `functionTimeout` Proje dosyasındaki [host.js](functions-host-json.md#functiontimeout) ayarıyla denetlenir.

## <a name="scaling"></a>Ölçeklendirme

App Service planı kullanarak daha fazla sanal makine örneği ekleyerek ölçeği el ile değiştirebilirsiniz. Otomatik ölçeklendirmeyi de etkinleştirebilirsiniz, ancak otomatik ölçeklendirme Premium planın esnek ölçeğinden daha yavaş olur. Daha fazla bilgi için bkz. [örnek sayısını el ile veya otomatik olarak ölçeklendirme](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Ayrıca, farklı bir App Service planı seçerek ölçeği genişletebilirsiniz. Daha fazla bilgi için bkz. [Azure 'da bir uygulamayı ölçeklendirme](../app-service/manage-scale-up.md). 

> [!NOTE] 
> JavaScript (Node.js) işlevlerini bir App Service planında çalıştırırken, daha az vCPU içeren bir plan seçmelisiniz. Daha fazla bilgi için bkz. [tek çekirdekli App Service planlarını seçme](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>App Service ortamları

App Service Ortamı (ASA), işlevlerinizi tamamen yalıtmanıza ve bir App Service planından daha fazla sayıda örnek avantajına sahip olmanızı sağlar. Başlamak için bkz. [App Service ortamlarına giriş](../app-service/environment/intro.md).

Yalnızca işlev uygulamanızı bir sanal ağda çalıştırmak istiyorsanız, bunu [Premium planı](functions-premium-plan.md)kullanarak yapabilirsiniz. Daha fazla bilgi için bkz. [Azure işlevleri özel site erişimi oluşturma](functions-create-private-site-access.md). 

## <a name="next-steps"></a>Sonraki adımlar

+ [Azure Işlevleri barındırma seçenekleri](functions-scale.md)
+ [Azure App Service planına genel bakış](../app-service/overview-hosting-plans.md)
