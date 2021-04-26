---
title: Application Insights kullanılabilirliğine genel bakış
description: Uygulamanızın veya Web sitenizin kullanılabilirliğini ve yanıt hızını izlemek için yinelenen Web testleri ayarlayın.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 3c2d248df98fcb9f75f5de9c05b9f600955cbac7
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600803"
---
# <a name="availability-tests-overview"></a>Kullanılabilirlik testlerine genel bakış

Web uygulamanızı/Web sitenizi dağıttıktan sonra, kullanılabilirliği ve yanıt hızını izlemek için yinelenen testler ayarlayabilirsiniz. [Application Insights](./app-insights-overview.md) , Web isteklerini, dünyanın dört bir yanındaki noktalarda düzenli aralıklarla uygulamanıza gönderir. Uygulamanız yanıt vermiyorsa veya çok yavaş yanıt verirse sizi uyarır.

Genel İnternet'ten erişilebilen herhangi bir HTTP veya HTTPS uç noktası için kullanılabilirlik testleri ayarlayabilirsiniz. Test ettiğiniz web sitesinde herhangi bir değişiklik yapmanız gerekmez. Aslında, sahip olduğunuz bir site bile olması gerekmez. Hizmetinizin bağlı olduğu REST API kullanılabilirliğini test edebilirsiniz.

## <a name="types-of-availability-tests"></a>Kullanılabilirlik testi türleri

Üç tür kullanılabilirlik testi vardır:

* [URL ping testi](monitor-web-app-availability.md): Bu kategoride Portal aracılığıyla oluşturabileceğiniz iki basit test vardır.
* [Çok adımlı Web testi](availability-multistep.md): daha karmaşık senaryoları test etmek için oynatılabilecek Web istekleri dizisinin bir kaydı. Çok adımlı Web testleri Visual Studio Enterprise oluşturulur ve yürütülmek üzere portala yüklenir.
* [Özel Izleme kullanılabilirlik testleri](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability): kullanılabilirlik testlerini çalıştırmak için özel bir uygulama oluşturmaya karar verirseniz, bu `TrackAvailability()` Yöntem sonuçları Application Insights göndermek için kullanılabilir.

> [!IMPORTANT]
> Her ikisi de, [URL ping testi](monitor-web-app-availability.md) ve [çok adımlı Web testi](availability-multistep.md) , sınanan uç noktaların etki alanı adlarını çözümlemek için genel internet DNS altyapısını kullanır. Bu, Özel DNS kullanıyorsanız, testinizin her etki alanı adının aynı zamanda genel etki alanı adı sunucuları tarafından çözümlenebilmesini veya mümkün olmadığında, bunun yerine [özel izleme kullanılabilirlik testlerini](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) kullanabilirsiniz.

**Application Insights kaynak başına en çok 100 kullanılabilirlik testi oluşturabilirsiniz.**

## <a name="troubleshooting"></a>Sorun giderme

Adanmış [sorun giderme makalesi](troubleshoot-availability.md).

## <a name="next-step"></a>Sonraki adım

* [Kullanılabilirlik uyarıları](availability-alerts.md)
* [Çok adımlı Web testleri](availability-multistep.md)
* [URL testleri](monitor-web-app-availability.md)
* [Azure Işlevleri 'ni kullanarak özel kullanılabilirlik testleri oluşturun ve çalıştırın.](availability-azure-functions.md)