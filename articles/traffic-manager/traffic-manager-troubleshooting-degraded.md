---
title: Azure Traffic Manager 'da azaltılmış durum sorunlarını giderme
description: Düşürülmüş durum olarak görüntülendiğinde Traffic Manager profillerinin sorunlarını giderme.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: dcscontentpm
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: rohink
ms.openlocfilehash: 8f043b11c9319d61c4413d01b008b324103ca6c3
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155208"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Azure Traffic Manager 'da azaltılmış durum sorunlarını giderme

Bu makalede, düzeyi düşürülmüş bir durumu gösteren bir Azure Traffic Manager profilinde nasıl sorun giderileceği açıklanır. Azure Traffic Manager indirgenme durumunun giderilmesi için ilk bir adım olarak, tanılama günlüğünü etkinleştirmektir.  Daha fazla bilgi için [tanılama günlüklerini etkinleştirme](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs) bölümüne bakın. Bu senaryo için cloudapp.net tarafından barındırılan hizmetlerden bazılarını işaret eden bir Traffic Manager profili yapılandırdığınıza dikkat edin. Traffic Manager sistem durumu **düşürülmüş** bir durum görüntülüyorsa, bir veya daha fazla uç noktanın durumu **azaltılabilir**olabilir:

![düşürülmüş uç nokta durumu](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Traffic Manager sistem durumu **etkin olmayan** bir durum görüntülüyorsa, her iki uç noktası da **devre dışı**bırakılabilir:

![Etkin olmayan Traffic Manager durumu](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Traffic Manager araştırmalarını anlama

* Traffic Manager, yalnızca araştırma bir HTTP 200 yanıtını araştırma yolundan geri aldığında bir uç noktanın ÇEVRIMIÇI olduğunu varsayar. Uygulamanız başka bir HTTP yanıt kodu döndürürse, bu yanıt kodunu Traffic Manager profilinizin [beklenen durum kodu aralıklarına](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) eklemeniz gerekir.
* Bu işlemi Traffic Manager profilinizin [beklenen durum kodu aralıklarında](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) geçerli bir yanıt kodu olarak belirtmediğiniz sürece, 30 kata yeniden yönlendirme yanıtı hata olarak değerlendirilir. Traffic Manager yeniden yönlendirme hedefini araştırmaz.
* HTTPs araştırmaları için Sertifika hataları yok sayılır.
* Bir 200 döndürüldüğünde, araştırma yolunun gerçek içeriği ne kadar büyük değildir. "/Ayrıcalıklı icon.exe" gibi bazı statik içeriklere bir URL 'YI yoklama yaygın bir tekniktir. ASP sayfaları gibi dinamik içerik, uygulama sağlıklı olsa bile her zaman 200 döndürmeyebilir.
* En iyi uygulama, araştırma yolunu, sitenin yukarı veya aşağı olduğunu anlamak için yeterli mantığa sahip bir şeye ayarlamaya yönelik bir uygulamadır. Önceki örnekte, yolu "/ayrıcalıklı icon.exe" olarak ayarlayarak yalnızca W3wp. exe ' nin yanıt verdiğini test edersiniz. Bu araştırma Web uygulamanızın sağlıklı olduğunu göstermeyebilir. Daha iyi bir seçenek, sitenin sistem durumunu belirleme mantığı olan "/Probe.aspx" gibi bir şey için bir yol ayarlamak olacaktır. Örneğin, performans sayaçlarını CPU kullanımı için kullanabilir veya başarısız isteklerin sayısını ölçebilir. Ya da Web uygulamasının çalıştığından emin olmak için veritabanı kaynaklarına veya oturum durumuna erişmeyi deneyebilirsiniz.
* Bir profildeki tüm uç noktalar düşerse, Traffic Manager tüm uç noktaları sağlıklı olarak değerlendirir ve trafiği tüm uç noktalara yönlendirir. Bu davranış, araştırma mekanizmasıyla ilgili sorunların hizmetinizin tamamen kesintiye neden olmamasını sağlar.

## <a name="troubleshooting"></a>Sorun giderme

Bir araştırma hatasıyla ilgili sorunları gidermek için, araştırma URL 'sinden döndürülen HTTP durum kodunu gösteren bir araca ihtiyacınız vardır. Ham HTTP yanıtını gösteren birçok araç mevcuttur.

* [Fiddler](https://www.telerik.com/fiddler)
* [kıvr](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Ayrıca, HTTP yanıtlarını görüntülemek için Internet Explorer 'daki F12 hata ayıklama araçlarının ağ sekmesini de kullanabilirsiniz.

Bu örnekte, araştırma URL 'imizden gelen yanıtı görmek istiyoruz: http:\//watestsdp2008r2.cloudapp.net:80/Probe. Aşağıdaki PowerShell örneğinde sorun gösterilmektedir.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Örnek çıktı:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Yeniden yönlendirme yanıtı aldığınızı fark edin. Daha önce belirtildiği gibi, 200 dışındaki tüm StatusCode bir hata olarak değerlendirilir. Traffic Manager uç nokta durumunu çevrimdışı olarak değiştirir. Sorunu çözmek için, araştırma yolundan doğru StatusCode 'nin döndürüldüğünden emin olmak için Web sitesi yapılandırmasını denetleyin. Traffic Manager araştırmasını, 200 döndüren bir yolu işaret edecek şekilde yeniden yapılandırın.

Araştırmanız HTTPS protokolünü kullanıyorsa, testiniz sırasında SSL/TLS hatalarından kaçınmak için sertifika denetimini devre dışı bırakmanız gerekebilir. Aşağıdaki PowerShell deyimleri geçerli PowerShell oturumu için sertifika doğrulamayı devre dışı bırakır:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Sonraki Adımlar

[Traffic Manager trafik yönlendirme yöntemleri hakkında](traffic-manager-routing-methods.md)

[Traffic Manager nedir?](traffic-manager-overview.md)

[Cloud Services](https://go.microsoft.com/fwlink/?LinkId=314074)

[Azure uygulama hizmeti](https://azure.microsoft.com/documentation/services/app-service/web/)

[Traffic Manager üzerindeki işlemler (REST API Başvurusu)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager cmdlet 'Leri][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
