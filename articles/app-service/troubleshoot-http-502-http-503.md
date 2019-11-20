---
title: 502 hatalı ağ geçidini çözme, 503 hizmeti kullanılamıyor hatası-Azure App Service | Microsoft Docs
description: Azure App Service 'de barındırılan uygulamanızda 502 hatalı ağ geçidi ve 503 hizmeti kullanılamayan hata sorunlarını giderin.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 502 hatalı ağ geçidi, 503 Hizmet kullanılamıyor, hata 503, hata 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fdbd77db349eed62af2eb8cf539ef749217a187a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066676"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Azure App Service 'de "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme
"502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" uygulamanızda [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)barındırılan yaygın hatalardır. Bu makale, bu hataları gidermenize yardımcı olur.

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**'a tıklayın.

## <a name="symptom"></a>Belirti
Uygulamaya gözattığınızda, HTTP "502 hatalı Ağ Geçidi" hatası veya HTTP "503 hizmeti kullanılamıyor" hatası döndürür.

## <a name="cause"></a>Nedeni
Bu sorun genellikle uygulama düzeyi sorunlarından kaynaklanır, örneğin:

* uzun zaman alan istekler
* yüksek bellek/CPU kullanan uygulama
* uygulama bir özel durum nedeniyle çökme.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>"502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" hatalarını gidermek için sorun giderme adımları
Sorun giderme üç farklı göreve sıralı sırayla ayrılabilir:

1. [Uygulama davranışını gözlemleyin ve izleyin](#observe)
2. [Veri topla](#collect)
3. [Sorunu azaltma](#mitigate)

[App Service](overview.md) her adımda çeşitli seçenekler sunar.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Uygulama davranışını gözlemleyin ve izleyin
#### <a name="track-service-health"></a>Hizmet durumunu izleme
Her bir hizmet kesintisi veya performans düşüşü olduğunda Microsoft Azure publicleştirir. Hizmetin sistem durumunu [Azure portalında](https://portal.azure.com/)izleyebilirsiniz. Daha fazla bilgi için bkz. [hizmet durumunu izleme](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Uygulamanızı izleme
Bu seçenek, uygulamanızda herhangi bir sorun olup olmadığını bulmanıza olanak sağlar. Uygulamanızın dikey penceresinde **İstekler ve hatalar** kutucuğuna tıklayın. **Ölçüm** dikey penceresi, ekleyebileceğiniz tüm ölçümleri gösterir.

Uygulamanız için izlemek isteyebileceğiniz bazı ölçümler şunlardır

* Ortalama bellek çalışma kümesi
* Ortalama yanıt süresi
* CPU süresi
* Bellek çalışma kümesi
* İstekler

![502 hatalı ağ geçidi ve 503 hizmeti için HTTP hatalarını çözmeye yönelik uygulamayı izleme](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Daha fazla bilgi için bkz.

* [Azure App Service uygulamaları izleme](web-sites-monitor.md)
* [Uyarı bildirimleri alma](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Veri toplama
#### <a name="use-the-diagnostics-tool"></a>Tanılama aracını kullanma
App Service, yapılandırma gerekmeden uygulamanızdaki sorunları gidermenize yardımcı olacak akıllı ve etkileşimli bir deneyim sağlar. Uygulamanız ile ilgili sorunlar yaşıyorsanız, Tanılama Aracı, sorunu daha kolay ve hızlı bir şekilde gidermeye ve çözmeye yönelik doğru bilgilere kılavuzluk etmek için nelerin yanlış olduğunu gösterir.

App Service tanılama 'ya erişmek için App Service uygulamanıza veya [Azure portal](https://portal.azure.com)App Service ortamı gidin. Sol gezinti bölmesinde, **sorunları Tanıla ve çöz**' e tıklayın.

#### <a name="use-the-kudu-debug-console"></a>Kudu hata ayıklama konsolunu kullanma
App Service, hata ayıklama, keşfetme, yükleme dosyaları ve ortamınız hakkında bilgi almak için JSON uç noktaları için kullanabileceğiniz bir hata ayıklama konsoluyla birlikte gelir. Bu, *kudu konsolu* veya uygulamanız Için *SCM panosu* olarak adlandırılır.

Bu panoya, **>. scm. azurewebsites. net/&lt;adlı uygulamanızın adını https://** bağlantısına giderek erişebilirsiniz.

Kudu 'nin sağladığı işlemlerden bazıları şunlardır:

* uygulamanız için ortam ayarları
* günlük akışı
* Tanılama dökümü
* PowerShell cmdlet 'lerini ve temel DOS komutlarını çalıştırabileceğiniz hata ayıklama konsolu.

Kudu 'nin başka bir faydalı özelliği de, uygulamanızın ilk şans özel durumlarını oluşturması durumunda kudu ve SysInternals aracı ProcDump ' i kullanarak bellek dökümleri oluşturabilir. Bu bellek dökümleri işlemin anlık görüntüleridir ve genellikle uygulamanızla ilgili karmaşık sorunları gidermenize yardımcı olabilir.

Kudu 'de kullanılabilen özellikler hakkında daha fazla bilgi için, bkz. [Azure Web siteleri çevrimiçi araçları hakkında bilmeniz gereken](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Sorunu azaltma
#### <a name="scale-the-app"></a>Uygulamayı ölçeklendirme
Azure App Service, daha fazla performans ve verimlilik için uygulamanızı çalıştırdığınız ölçeği ayarlayabilirsiniz. Bir uygulamanın ölçeğini değiştirmek, iki ilgili eylemi içerir: App Service planınızı daha yüksek bir fiyatlandırma katmanına değiştirme ve belirli ayarları daha yüksek fiyatlandırma katmanına geçtikten sonra yapılandırma.

Ölçeklendirme hakkında daha fazla bilgi için bkz. [Azure App Service bir uygulamayı ölçeklendirme](manage-scale-up.md).

Ayrıca, uygulamanızı birden fazla örnekte çalıştırmayı seçebilirsiniz. Bu, size daha fazla işleme özelliği sağlamaz, ancak size bazı hata toleransı sağlar. İşlem bir örnek üzerinde daha fazla olursa, diğer örnek isteklere hizmet vermeye devam eder.

Ölçeklendirmeyi El Ile veya otomatik olarak ayarlayabilirsiniz.

#### <a name="use-autoheal"></a>Oto Heal kullanma
Oto Heal, seçtiğiniz ayarlara göre (yapılandırma değişiklikleri, istekler, bellek tabanlı sınırlar veya bir isteği yürütmek için gereken süre gibi) uygulamanızın çalışan işlemini geri dönüştürür. Çoğu zaman, bir sorunu kurtarmanın en hızlı yoludur. Uygulamayı her zaman doğrudan Azure portalından yeniden başlatabilseniz de, Otomatik Heal bunu sizin için otomatik olarak kullanacaktır. Yapmanız gereken tek şey, uygulamanız için kök Web. config dosyasında bazı Tetikleyiciler eklemektir. Bu ayarların, uygulamanız .NET One olmasa bile aynı şekilde çalıştığını unutmayın.

Daha fazla bilgi için bkz. [Otomatik Düzeltme Azure Web siteleri](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Uygulamayı yeniden başlatma
Bu çoğunlukla, tek seferlik sorunlardan kurtulmanın en kolay yoludur. [Azure portalında](https://portal.azure.com/)uygulamanızın dikey penceresinde uygulamanızı durdurma veya yeniden başlatma seçenekleriniz vardır.

 ![502 hatalı ağ geçidi ve 503 hizmeti için HTTP hatalarını gidermek üzere uygulamayı yeniden Başlat](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Uygulamanızı Azure PowerShell kullanarak da yönetebilirsiniz. Daha fazla bilgi için bkz. [Azure PowerShell'i Azure Resource Manager ile kullanma](../powershell-azure-resource-manager.md).

