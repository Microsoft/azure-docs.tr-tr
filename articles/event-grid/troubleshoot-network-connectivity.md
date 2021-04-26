---
title: Ağ bağlantısı sorunlarını giderme-Azure Event Grid | Microsoft Docs
description: Bu makale, Azure Event Grid ile ilgili ağ bağlantısı sorunlarını giderme hakkında bilgi sağlar.
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: fa119784715b8c88ef3c9f2700b2cac1cc467234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96339754"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>Bağlantı sorunlarını giderme-Azure Event Grid

İstemci uygulamalarının bir Event Grid konu/etki alanına bağlanamamasının çeşitli nedenleri vardır. Karşılaşabileceğiniz bağlantı sorunları kalıcı veya geçici olabilir. Sorun her zaman (kalıcı) olursa, kuruluşunuzun güvenlik duvarı ayarlarını, IP güvenlik duvarı ayarlarını, hizmet etiketlerini, Özel uç noktaları ve daha fazlasını denetlemek isteyebilirsiniz. Geçici sorunlar için, bırakılan paketleri denetlemeye yönelik komutları çalıştırın ve ağ izlemelerinin alınması sorunları gidermenize yardımcı olabilir.

Bu makalede Azure Event Grid bağlantı sorunlarını gidermeye yönelik ipuçları sunulmaktadır.

## <a name="troubleshoot-permanent-connectivity-issues"></a>Kalıcı bağlantı sorunlarını giderme

Uygulama, olay kılavuzuna hiç bağlanamıyorsa, sorunu gidermek için bu bölümdeki adımları izleyin.

### <a name="check-if-theres-a-service-outage"></a>Hizmet kesintisi olup olmadığını denetleyin

[Azure hizmet durumu sitesinde](https://azure.microsoft.com/status/)Azure Event Grid hizmet kesintisi olup olmadığını denetleyin.

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Event Grid ile iletişim kurmak için gereken bağlantı noktalarının kuruluşun güvenlik duvarı tarafından engellenmediğinden emin olun

Azure Event Grid ile iletişim kurmak için kullanılan bağlantı noktalarının kuruluşunuzun güvenlik duvarında engellenmediğinden emin olun. Azure Event Grid ile iletişim kurmak için açmanız gereken giden bağlantı noktaları için aşağıdaki tabloya bakın.

| Protokol | Bağlantı noktaları |
| -------- | ----- |
| HTTPS    | 443   |

Aşağıda, 443 bağlantı noktasının engellenip engellenmeyeceğini denetleyen örnek bir komut verilmiştir.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Linux 'ta:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Şirket güvenlik duvarınızdaki IP adreslerine izin verildiğini doğrulayın

Azure ile çalışırken, bazı durumlarda kurumsal güvenlik duvarınızdaki veya ara ortamınızdaki belirli IP adresi aralıklarına veya URL 'Lerine, kullanmakta olduğunuz veya kullanmaya çalıştığınız tüm Azure hizmetlerine erişim izni vermeniz gerekir. Event Grid tarafından kullanılan IP adreslerinde trafiğe izin verildiğini doğrulayın. Azure Event Grid tarafından kullanılan IP adresleri için: bkz. [Azure IP aralıkları ve hizmet etiketleri-genel bulut](https://www.microsoft.com/download/details.aspx?id=56519) ve [hizmet etiketi-AzureEventGrid](network-security.md#service-tags).

[Azure IP aralıkları ve hizmet etiketleri-genel bulut](https://www.microsoft.com/download/details.aspx?id=56519) belgesi ayrıca **bölgeye göre** IP adreslerini listeler. **Konunun bölgesi** ve şirket güvenlik duvarınız veya ara sunucunuzda **eşleştirilmiş bölge** için adres aralıklarına izin verebilirsiniz. Bölgenin eşleştirilmiş bir bölgesi için bkz. [iş sürekliliği ve olağanüstü durum kurtarma (BCDR): Azure eşleştirilmiş bölgeleri](../best-practices-availability-paired-regions.md). 

> [!NOTE]
> Yeni IP adresleri AzureEventGrid Service etiketine eklenebilse de, her zamanki gibi. Bu nedenle, hizmet etiketlerinde haftalık bir denetim yapmanız iyi olur.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Ağ güvenlik gruplarında AzureEventGrid Service etiketine izin verildiğini doğrulayın

Uygulamanız bir alt ağ içinde çalışıyorsa ve ilişkili bir ağ güvenlik grubu varsa, internet giden ya da AzureEventGrid hizmet etiketine izin verilip verilmeyeceğini onaylayın. Bkz. [hizmet etiketleri](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>Konağınız/etki alanınız için IP güvenlik duvarı ayarlarını denetleyin

Uygulamanın üzerinde çalıştığı makinenin genel IP adresinin EventGrid konusu/etki alanı IP güvenlik duvarı tarafından engellenmediğinden emin olun.

Varsayılan olarak, Event Grid konular/etki alanları, istek geçerli kimlik doğrulaması ve yetkilendirmeyle geldiği sürece internet 'ten erişilebilir. IP güvenlik duvarı ile bunu, [CIDR (sınıfsız Inter-Domain yönlendirme)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) gösteriminde yalnızca bir IPv4 adresi veya IPv4 adres aralıkları kümesiyle sınırlayabilirsiniz.

IP güvenlik duvarı kuralları Event Grid konusu/etki alanı düzeyinde uygulanır. Bu nedenle, kurallar desteklenen herhangi bir protokolü kullanarak istemcilerden gelen tüm bağlantılara uygulanır. Event Grid konusu/etki alanında izin verilen bir IP kuralıyla eşleşmeyen bir IP adresinden gelen bağlantı girişimleri yasaklanmış olarak reddedilir. Yanıt, IP kuralından bahsetmez.

Daha fazla bilgi için bkz. [bir Azure Event Grid konusu/etki alanı IÇIN IP güvenlik duvarı kurallarını yapılandırma](configure-firewall.md).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>IP güvenlik duvarı tarafından engellenen IP adreslerini bulma

Tanılama günlüklerini [etkinleştirmek](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic)Event Grid konu/etki alanı tanılama günlüklerini etkinleştirin. Reddedilen bağlantının IP adresini görürsünüz.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>EventGrid konusuna/etki alanına yalnızca özel bir uç nokta kullanılarak erişilip erişilemeyeceğini denetleyin

Event Grid konusu/etki alanı yalnızca özel uç nokta aracılığıyla erişilebilir olacak şekilde yapılandırıldıysa, istemci uygulamanın konuya/etki alanına özel uç nokta üzerinden eriştiğini doğrulayın. Onaylamak için, istemci uygulamasının bir alt ağ içinde çalışıp çalışmadığını ve ilgili alt ağdaki Event Grid konu/etki alanı için özel bir uç nokta olduğunu denetleyin.

[Azure özel bağlantı hizmeti](../private-link/private-link-overview.md) , sanal ağınızdaki **özel bir uç nokta** üzerinden Azure Event Grid erişmenizi sağlar. Özel uç nokta, Azure özel bağlantısı tarafından desteklenen bir hizmete özel ve güvenli bir şekilde bağlanan bir ağ arabirimidir. Özel uç nokta, sanal ağınızdan bir özel IP adresi kullanarak hizmeti sanal ağınıza etkin bir şekilde getiriyor. Hizmete giden tüm trafik özel uç nokta aracılığıyla yönlendirilebilir, bu nedenle ağ geçitleri, NAT cihazları, ExpressRoute veya VPN bağlantıları ya da genel IP adresleri gerekmez. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden geçer ve genel İnternet’ten etkilenme olasılığı ortadan kaldırılır. Bir Azure kaynağı örneğine bağlanarak, erişim denetimi için en yüksek düzeyde ayrıntı düzeyi sağlayabilirsiniz.

Daha fazla bilgi için bkz. [Özel uç noktaları yapılandırma](configure-private-endpoints.md).

## <a name="troubleshoot-transient-connectivity-issues"></a>Geçici bağlantı sorunlarını giderme

Aralıklı bağlantı sorunlarıyla karşılaşıyorsanız, sorun giderme ipuçları için aşağıdaki bölümlere bakın.

### <a name="run-the-command-to-check-dropped-packets"></a>Bırakılan paketleri denetlemek için komutunu çalıştırın

Aralıklı bağlantı sorunları olduğunda, bırakılan herhangi bir paket olup olmadığını denetlemek için aşağıdaki komutu çalıştırın. Bu komut, hizmeti ile her 1 saniyede 25 farklı TCP bağlantısı kurmaya çalışacaktır. Sonra, kaç tane başarılı/başarısız olduğunu denetleyebilir ve ayrıca TCP bağlantı gecikmesini de görebilirsiniz. `psping`Aracı [buradan](/sysinternals/downloads/psping)indirebilirsiniz.

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

tcpping.exegibi başka araçlar kullanıyorsanız, eşdeğer komutları kullanabilirsiniz `tcpping` [ ](https://www.elifulkerson.com/projects/tcping.php).

Önceki adımlar [Wireshark](https://www.wireshark.org/)gibi araçları kullanarak yardımcı değilse ve analiz yoksa bir ağ izlemesi elde edin. Gerekirse [Microsoft desteği](https://support.microsoft.com/) başvurun.

### <a name="service-upgradesrestarts"></a>Hizmet yükseltmeleri/yeniden başlatmalar

Geçici bağlantı sorunları, arka uç hizmeti yükseltmeleri ve yeniden başlatmalar nedeniyle oluşabilir. Oluştuğunda, aşağıdaki belirtilerle karşılaşabilirsiniz:

- Gelen iletilerde/isteklerde bir bırakma olabilir.
- Günlük dosyasında hata iletileri bulunabilir.
- Uygulamaların birkaç saniye boyunca hizmetle bağlantısı kesilebilir.
- İstekler, geçici olarak kısıtlanabilir.

Bu geçici hataları yakalamak, kapatmak ve sonra çağrıyı yeniden denemek, kodunuzun bu geçici sorunlara dayanıklı olmasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla yardıma ihtiyacınız varsa sorununuzu [Stack Overflow forumuna](https://stackoverflow.com/questions/tagged/azure-eventgrid) gönderin veya bir [destek bileti](https://azure.microsoft.com/support/options/)açın.