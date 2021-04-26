---
title: Azure Application Gateway Azure Web uygulaması güvenlik duvarı nedir?
titleSuffix: Azure Web Application Firewall
description: Bu makalede, Application Gateway Web uygulaması güvenlik duvarı 'na (WAF) genel bir bakış sunulmaktadır
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 12/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: babd628280ebaee408d44dfacfaf6a5e14f57019
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481833"
---
# <a name="what-is-azure-web-application-firewall-on-azure-application-gateway"></a>Azure Application Gateway Azure Web uygulaması güvenlik duvarı nedir?

Azure Application Gateway Azure Web uygulaması güvenlik duvarı (WAF), Web uygulamalarınızın genel güvenlik açıklarından ve güvenlik açıklarından merkezi bir şekilde korunmasını sağlar. Web uygulamaları, yaygın olarak bilinen güvenlik açıklarından yararlanan kötü amaçlı saldırılara karşı giderek daha da yöneliktir. SQL ekleme ve siteler arası komut dosyası oluşturma en yaygın saldırı arasındadır.

Application Gateway WAF, açık Web uygulaması güvenlik projesinden (OWASP) [çekirdek kural kümesi (sp_configure)](https://owasp.org/www-project-modsecurity-core-rule-set/) 3,1, 3,0 veya 2.2.9 tabanlıdır. WAF, ek yapılandırma gerekmeden yeni güvenlik açıklarına karşı koruma içerecek şekilde otomatik olarak güncelleştirilir. 

Aşağıda listelenen tüm WAF özellikleri bir WAF Ilkesi içinde mevcuttur. Birden çok ilke oluşturabilirsiniz ve bir Application Gateway, tek tek dinleyiciler veya bir Application Gateway yol tabanlı yönlendirme kuralları ile ilişkilendirilebilen. Bu şekilde, gerekirse Application Gateway ardındaki her bir site için ayrı ilkelere sahip olabilirsiniz. WAF Ilkeleri hakkında daha fazla bilgi için bkz. [WAF Ilkesi oluşturma](create-waf-policy-ag.md).

![Application Gateway WAF diyagramı](../media/ag-overview/waf1.png)

Application Gateway, uygulama teslim denetleyicisi (ADC) olarak çalışır. Daha önce Güvenli Yuva Katmanı (SSL), sonlandırma, tanımlama bilgisi tabanlı oturum benzeşimi, hepsini bir kez deneme yük dağıtımı, içerik tabanlı yönlendirme, birden fazla Web sitesi barındırma yeteneği ve güvenlik geliştirmeleri gibi bilinen Aktarım Katmanı Güvenliği (TLS) sağlar.

Application Gateway güvenlik geliştirmeleri TLS ilkesi yönetimi ve uçtan uca TLS desteği içerir. Uygulama güvenliği, Application Gateway ' ye WAF tümleştirmesi tarafından Güçbir hale döndürüldü. Birleşim, Web uygulamalarınızı yaygın güvenlik açıklarına karşı korur. Ve yönetmek için kolay yapılandırma merkezi bir konum sağlar.

## <a name="benefits"></a>Avantajlar

Bu bölümde, Application Gateway için WAF 'nin sağladığı temel avantajlar açıklanmaktadır.

### <a name="protection"></a>Koruma

* Web uygulamalarınızı, arka uç kodunda değişiklik yapmadan Web güvenlik açıklarına ve saldırılarına karşı koruyun.

* Birden çok Web uygulamasını aynı anda koruyun. Bir Application Gateway örneği, bir Web uygulaması güvenlik duvarı tarafından korunan 40 Web sitesini barındırabilir.

* Aynı WAF arkasındaki farklı siteler için özel WAF ilkeleri oluşturma 

* IP saygınlığı RuleSet (Önizleme) ile Web uygulamalarınızı kötü amaçlı botlardan koruyun

### <a name="monitoring"></a>İzleme

* Gerçek zamanlı bir WAF günlüğü kullanarak Web uygulamalarınıza yönelik saldırıları izleyin. Günlük, WAF uyarılarını izlemek ve eğilimleri kolayca izlemek için [Azure izleyici](../../azure-monitor/overview.md) ile tümleşiktir.

* WAF Application Gateway Azure Güvenlik Merkezi ile tümleşiktir. Güvenlik Merkezi, tüm Azure kaynaklarınızın güvenlik durumuna ilişkin merkezi bir görünüm sağlar.

### <a name="customization"></a>Özelleştirme

* WAF kurallarını ve kural gruplarını, uygulama gereksinimlerinize uyacak şekilde özelleştirin ve hatalı pozitif sonuçları ortadan kaldırın.

* WAF 'nin bir WAF Ilkesini siteye özgü yapılandırmaya izin vermek için WAF 'nin arkasındaki her bir site için ilişkilendirin

* Uygulamanızın ihtiyaçlarını karşılamak için özel kurallar oluşturun

## <a name="features"></a>Özellikler

- SQL ekleme koruması.
- Siteler arası betik koruması.
- Komut ekleme, HTTP isteği, HTTP yanıtı bölme ve uzak dosya ekleme gibi diğer yaygın web saldırılarına karşı koruma.
- HTTP protokol ihlallerine karşı koruma.
- Eksik konak Kullanıcı Aracısı ve kabul başlıkları gibi HTTP protokol anormallara karşı koruma.
- Gezginler ve tarayıcılara karşı koruma.
- Yaygın uygulama yapılandırmalarını algılama (örneğin, Apache ve IIS).
- Daha düşük ve üst sınırlara sahip yapılandırılabilir istek boyutu sınırları.
- Dışlama listeleri, bir WAF değerlendirmesinden belirli istek özniteliklerini atlamanızı sağlar. Ortak bir örnek, kimlik doğrulama veya parola alanları için kullanılan Active Directory eklenen belirteçlerdir.
- Uygulamalarınızın belirli ihtiyaçlarını karşılamak için özel kurallar oluşturun.
- Belirli ülkelerin/bölgelerin uygulamalarınıza erişim kazanmasıyla izin vermek veya engellemek için coğrafi filtreleme trafiği. (önizleme)
- Botları hafifletme kural kümesiyle uygulamalarınızı koruyun. (önizleme)
- İstek gövdesinde JSON ve XML 'yi İnceleme

## <a name="waf-policy-and-rules"></a>WAF ilkesi ve kuralları

Application Gateway bir Web uygulaması güvenlik duvarını etkinleştirmek için bir WAF ilkesi oluşturmanız gerekir. Bu ilke, tüm yönetilen kuralların, özel kuralların, dışlamaların ve dosya karşıya yükleme sınırı gibi diğer özelleştirmelerin bulunduğu yerdir.

Bir WAF ilkesi yapılandırabilir ve bu ilkeyi koruma için bir veya daha fazla uygulama ağ geçidi ile ilişkilendirebilirsiniz. Bir WAF ilkesi, iki tür güvenlik kuralından oluşur:

- Oluşturduğunuz özel kurallar

- Azure tarafından yönetilen önceden yapılandırılmış bir kural kümesi koleksiyonu olan yönetilen kural kümeleri

Her ikisi de varsa, yönetilen bir kural kümesindeki kuralları işlemeden önce özel kurallar işlenir. Bir kural eşleştirme koşulu, öncelik ve bir eylemden oluşur. Desteklenen eylem türleri şunlardır: ızın ver, engelle ve günlük. Yönetilen ve özel kuralları birleştirerek, belirli uygulama koruma gereksinimlerinizi karşılayan tam olarak özelleştirilmiş bir ilke oluşturabilirsiniz.

İlke içindeki kurallar öncelik sırasına göre işlenir. Öncelik, işlenecek kuralların sırasını tanımlayan benzersiz bir tamsayıdır. Daha küçük tamsayı değeri daha yüksek bir öncelik gösterir ve bu kurallar daha yüksek bir tamsayı değerine sahip kurallardan önce değerlendirilir. Bir kural eşleştiğinde, kuralda tanımlanan ilgili eylem isteğe uygulanır. Bu tür bir eşleşme işlendiğinde, daha düşük öncelikler olan kurallar daha fazla işlenmez.

Application Gateway tarafından sunulan bir Web uygulaması, genel düzeyde, site başına düzeyde veya URI başına düzeyde bir ile ilişkili bir WAF ilkesine sahip olabilir.

### <a name="core-rule-sets"></a>Çekirdek kural kümeleri

Application Gateway üç kural kümesini destekler: yukarı ve 3,1, yukarı 3,0 ve yukarı 2.2.9. Bu kurallar web uygulamalarınızı kötü amaçlı etkinlikten korur.

Daha fazla bilgi için bkz. [Web uygulaması güvenlik duvarı Ayrıntılar kural grupları ve kuralları](application-gateway-crs-rulegroups-rules.md).

### <a name="custom-rules"></a>Özel kurallar

Application Gateway özel kuralları da destekler. Özel kurallarla, WAF üzerinden geçen her istek için değerlendirilen kendi kurallarınızı oluşturabilirsiniz. Bu kurallar, yönetilen kural kümelerindeki kuralların geri kalanından daha yüksek bir öncelik tutar. Bir koşul kümesi karşılanıyorsa, izin vermek veya engellemek için bir eylem yapılır. 

Geomatch işleci artık özel kurallar için genel önizlemede kullanılabilir. Daha fazla bilgi için lütfen [geomatch özel kurallarına](custom-waf-rules-overview.md#geomatch-custom-rules-preview) bakın.

> [!NOTE]
> Özel kurallar için geomatch işleci Şu anda genel önizlemededir ve bir önizleme hizmet düzeyi sözleşmesi ile sunulmaktadır. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Özel kurallar hakkında daha fazla bilgi için bkz [. özel kurallar Application Gateway.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Bot azaltma (Önizleme)

Yönetilen bir bot koruma kuralı kümesi, WAF 'nizin yönetilen RuleSet 'in yanı sıra bilinen kötü amaçlı IP adreslerinden gelen istekleri engellemek veya günlüğe kaydetmek için etkinleştirilebilir. IP adresleri, Microsoft Tehdit Analizi akışından alınır. Microsoft Tehdit Analizi, Intelligent Security Graph tarafından desteklenir ve Azure Güvenlik Merkezi dahil olmak üzere birçok hizmet tarafından kullanılır.

> [!NOTE]
> Bot koruma kuralı kümesi şu anda genel önizleme aşamasındadır ve bir önizleme hizmet düzeyi sözleşmesi ile sunulmaktadır. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bot koruması etkinleştirilirse, kötü amaçlı bir bot 'ın istemci IP 'Leri ile eşleşen gelen istekler güvenlik duvarı günlüğüne kaydedilir, daha fazla bilgi için aşağıya bakın. WAF günlüklerine depolama hesabından, Olay Hub 'ından veya Log Analytics 'ten erişebilirsiniz. 

### <a name="waf-modes"></a>WAF modları

Application Gateway WAF aşağıdaki iki modda çalışacak şekilde yapılandırılabilir:

* **Algılama modu**: tüm tehdit uyarılarını izler ve günlüğe kaydeder. **Tanılama** bölümünde Application Gateway için günlük tanılamayı açın. Ayrıca, WAF günlüğünün seçili ve açık olduğundan emin olmanız gerekir. Web uygulaması güvenlik duvarı, algılama modunda çalışırken gelen istekleri engellemez.
* **Önleme modu**: kuralların algılamadığı yetkisiz ve saldırıları engeller. Saldırgan bir "403 yetkisiz erişim" özel durumu alır ve bağlantı kapatılır. Önleme modu WAF günlüklerinde bu tür saldırıları kaydeder.

> [!NOTE]
> Bir üretim ortamında kısa bir süre için yeni dağıtılan bir WAF algılama modunda çalıştırmanız önerilir. Bu, Önleme moduna geçişten önce [güvenlik duvarı günlüklerini](../../application-gateway/application-gateway-diagnostics.md#firewall-log) alma ve tüm özel durumları ya da [özel kuralları](./custom-waf-rules-overview.md) güncelleştirme fırsatını sağlar. Bu, beklenmeyen engellenen trafiğin oluşmasını azaltmaya yardımcı olabilir.

### <a name="anomaly-scoring-mode"></a>Anomali Puanlama modu

OWASP 'nin trafiğin engellenip engellenmeyeceğini belirleyerek iki modu vardır: geleneksel mod ve anomali Puanlama modu.

Geleneksel modda, herhangi bir kuralla eşleşen trafik diğer kural eşleştirmelerinin bağımsız olarak değerlendirilir. Bu modun anlaşılması kolaydır. Ancak belirli bir istekle eşleşen kuralların sayısı ile ilgili bilgi olmaması bir kısıtlamadır. Bu nedenle, anomali Puanlama modu sunuldu. OWASP 3 için varsayılandır. *x*.

Anomali Puanlama modunda, güvenlik duvarı önleme modundayken, herhangi bir kuralla eşleşen trafik hemen engellenmez. Kurallar belirli bir önem derecesine sahiptir: *kritik*, *hata*, *Uyarı* veya *bildirim*. Bu önem derecesi, istek için anomali puanı olarak adlandırılan sayısal bir değeri etkiler. Örneğin, bir *Uyarı* kuralı eşleşmesi 3 ' e katkıda bulunur. Bir *kritik* kural eşleşmesi 5. katkıda bulunur.

|Önem derecesi  |Değer  |
|---------|---------|
|Kritik     |5|
|Hata        |4|
|Uyarı      |3|
|Değiştirilebileceğini       |2|

Trafiği engellemek için anomali puanı 5 ' lik bir eşik değeri vardır. Bu nedenle, Application Gateway WAF 'nin önleme modunda bile bir isteği engellemesini engellemek için tek bir *kritik* kural eşleşmesi yeterlidir. Ancak, bir *Uyarı* kuralı eşleşmesi yalnızca 3 Ile anomali puanı artırır ve bu, trafiği engellemek için kendisi tarafından yeterli değildir.

> [!NOTE]
> Bir WAF kuralı trafik ile eşleştiğinde günlüğe kaydedilen ileti, "engellendi" eylem değerini içerir. Ancak trafik aslında yalnızca 5 veya üzeri bir anomali puanı için engellenmiştir. Daha fazla bilgi için bkz. [Azure Application Gateway Için Web uygulaması güvenlik duvarı (WAF) sorunlarını giderme](web-application-firewall-troubleshoot.md#understanding-waf-logs). 

### <a name="waf-monitoring"></a>WAF izleme

Uygulama ağ geçidinizin durumunu izlemek önemlidir. WAF 'nizin sistem durumunu izleme ve koruduğu uygulamalar Azure Güvenlik Merkezi, Azure Izleyici ve Azure Izleyici günlükleri ile tümleştirme tarafından desteklenir.

![Application Gateway WAF tanılaması diyagramı](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure İzleyici

Application Gateway Günlükler [Azure izleyici](../../azure-monitor/overview.md)ile tümleşiktir. Bu, WAF uyarıları ve günlükleri dahil olmak üzere tanılama bilgilerini izlemenizi sağlar. Bu özelliğe portalda Application Gateway kaynağındaki **Tanılama** sekmesinden veya doğrudan Azure izleyici aracılığıyla erişebilirsiniz. Günlükleri etkinleştirme hakkında daha fazla bilgi için bkz. [Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Güvenlik Merkezi

[Güvenlik Merkezi](../../security-center/security-center-introduction.md) , tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Azure kaynaklarınızın güvenliği üzerinde daha fazla görünürlük ve denetim sağlar. Application Gateway, [Güvenlik Merkezi ile tümleşiktir](../../security-center/security-center-partner-integration.md#integrated-azure-security-solutions). Güvenlik Merkezi, korumasız Web uygulamalarını algılamak için ortamınızı tarar. Bu güvenlik açığı bulunan kaynakları korumak için WAF Application Gateway önerilir. Güvenlik duvarlarını doğrudan güvenlik merkezi 'nden oluşturursunuz. Bu WAF örnekleri güvenlik merkezi ile tümleşiktir. Raporlama için Güvenlik Merkezi 'ne uyarılar ve sistem durumu bilgileri gönderir.

![Güvenlik Merkezi genel bakış penceresi](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel, ölçeklenebilir, bulutta yerel, güvenlik bilgileri olay yönetimi (SıEM) ve güvenlik Orchestration otomatik yanıtı (SOAR) çözümüdür. Azure Sentinel, bir uyarı algılama, tehdit görünürlüğü, proaktif arama ve tehdit yanıtı için tek bir çözüm sunarak kuruluş genelinde akıllı güvenlik Analizi ve tehdit bilgileri sunar.

Yerleşik Azure WAF güvenlik duvarı olayları çalışma kitabı ile WAF 'nizin güvenlik olaylarına genel bir bakış edinebilirsiniz. Bu, olayları, eşleşen ve engellenen kuralları ve güvenlik duvarı günlüklerinde günlüğe kaydedilen diğer her şeyi içerir. Aşağıda günlüğe kaydetme hakkında daha fazla bilgi bulabilirsiniz. 


![Azure WAF güvenlik duvarı olayları çalışma kitabı](../media/ag-overview/sentinel.png)


#### <a name="azure-monitor-workbook-for-waf"></a>WAF için Azure Izleyici çalışma kitabı

Bu çalışma kitabı, birkaç filtrelenebilir panel üzerinde güvenlikle ilgili WAF olaylarının özel görselleştirilmesini mümkün hale getiriyor. Application Gateway, ön kapı ve CDN dahil olmak üzere tüm WAF türleriyle birlikte çalışarak, WAF türüne veya belirli bir WAF örneğine göre filtrelenebilir. ARM şablonu veya Galeri şablonu aracılığıyla içeri aktarın. Bu çalışma kitabını dağıtmak için bkz. [WAF çalışma kitabı](https://aka.ms/AzWAFworkbook).

#### <a name="logging"></a>Günlüğe Kaydetme

WAF Application Gateway algıladığı her tehdit üzerinde ayrıntılı raporlama sağlar. Günlüğe kaydetme, Azure Tanılama günlükleriyle tümleşiktir. Uyarılar. JSON biçiminde kaydedilir. Bu Günlükler, [Azure izleyici günlükleri](../../azure-monitor/insights/azure-networking-analytics.md)ile tümleştirilebilir.

![Tanılama günlükleri pencerelerini Application Gateway](../media/ag-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.145",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343"
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/globalWafPolicy",
      "policyScope": "Global",
      "policyScopeName": " Global "
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Application Gateway WAF SKU fiyatlandırması

Fiyatlandırma modelleri WAF_v1 ve WAF_v2 SKU 'Ları için farklıdır. Daha fazla bilgi için lütfen [Application Gateway fiyatlandırma](https://azure.microsoft.com/pricing/details/application-gateway/) sayfasına bakın. 

## <a name="whats-new"></a>Yenilikler

Azure Web uygulaması güvenlik duvarı yenilikleri hakkında bilgi edinmek için bkz. [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=Web%20Application%20Firewall).

## <a name="next-steps"></a>Sonraki adımlar

- [WAF tarafından yönetilen kurallar](application-gateway-crs-rulegroups-rules.md) hakkında daha fazla bilgi edinin
- [Özel kurallar](custom-waf-rules-overview.md) hakkında daha fazla bilgi edinin
- [Azure ön kapıda Web uygulaması güvenlik duvarı](../afds/afds-overview.md) hakkında bilgi edinin
