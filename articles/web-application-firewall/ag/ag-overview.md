---
title: Azure Web uygulaması güvenlik duvarı 'na giriş
titleSuffix: Azure Web Application Firewall
description: Bu makalede, Application Gateway Web uygulaması güvenlik duvarı 'na (WAF) genel bir bakış sunulmaktadır
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 4ba2772ffb89809c2262880385207a12c74882cc
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74067140"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway"></a>Azure Application Gateway Azure Web uygulaması güvenlik duvarı

Azure Application Gateway Azure Web uygulaması güvenlik duvarı (WAF), Web uygulamalarınızın genel güvenlik açıklarından ve güvenlik açıklarından merkezi bir şekilde korunmasını sağlar. Web uygulamaları, yaygın olarak bilinen güvenlik açıklarından yararlanan kötü amaçlı saldırılara karşı giderek daha da yöneliktir. SQL ekleme ve siteler arası komut dosyası oluşturma en yaygın saldırı arasındadır.

Application Gateway WAF, açık Web uygulaması güvenlik projesinden (OWASP) [çekirdek kural kümesi (sp_configure)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1, 3,0 veya 2.2.9 tabanlıdır. WAF, ek yapılandırma gerekmeden yeni güvenlik açıklarına karşı koruma içerecek şekilde otomatik olarak güncelleştirilir. 

Aşağıda listelenen tüm WAF özellikleri bir WAF Ilkesi içinde mevcuttur. Birden çok ilke oluşturabilirsiniz ve bir Application Gateway, tek tek dinleyiciler veya bir Application Gateway yol tabanlı yönlendirme kuralları ile ilişkilendirilebilen. Bu şekilde, gerekirse Application Gateway ardındaki her bir site için ayrı ilkelere sahip olabilirsiniz. WAF Ilkeleri hakkında daha fazla bilgi için bkz. [WAF Ilkesi oluşturma](create-waf-policy-ag.md).

   > [!NOTE]
   > Site başına ve URI başına WAF Ilkeleri genel önizlemede. Bu, bu özelliğin Microsoft 'un ek kullanım koşulları 'na tabi olduğu anlamına gelir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Application Gateway WAF diyagramı](../media/ag-overview/waf1.png)

Application Gateway, uygulama teslim denetleyicisi (ADC) olarak çalışır. Güvenli Yuva Katmanı (SSL) sonlandırma, tanımlama bilgisi tabanlı oturum benzeşimi, hepsini bir kez deneme yük dağıtımı, içerik tabanlı yönlendirme, birden fazla Web sitesi barındırma yeteneği ve güvenlik geliştirmeleri sunar.

Application Gateway güvenlik geliştirmeleri, SSL ilkesi yönetimi ve uçtan uca SSL desteği içerir. Uygulama güvenliği, Application Gateway ' ye WAF tümleştirmesi tarafından Güçbir hale döndürüldü. Birleşim, Web uygulamalarınızı yaygın güvenlik açıklarına karşı korur. Ve yönetmek için kolay yapılandırma merkezi bir konum sağlar.

## <a name="benefits"></a>Avantajlar

Bu bölümde, Application Gateway için WAF 'nin sağladığı temel avantajlar açıklanmaktadır.

### <a name="protection"></a>Koruma

* Web uygulamalarınızı, arka uç kodunda değişiklik yapmadan Web güvenlik açıklarına ve saldırılarına karşı koruyun.

* Birden çok Web uygulamasını aynı anda koruyun. Bir Application Gateway örneği, bir Web uygulaması güvenlik duvarı tarafından korunan en fazla 40 Web sitesi barındırabilir.

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
- Coğrafi filtreleme, belirli ülkelerin uygulamalarınıza erişim kazanmasıyla izin vermek veya engellemek için trafiği filtreleyin. (önizleme)
- Botları hafifletme kural kümesiyle uygulamalarınızı koruyun. (önizleme)

## <a name="waf-policy"></a>WAF Ilkesi

Bir Application Gateway Web uygulaması güvenlik duvarını etkinleştirmek için bir WAF Ilkesi oluşturmanız gerekir. Bu Ilke, tüm yönetilen kuralların, özel kuralların, dışlamaların ve dosya karşıya yükleme sınırı gibi diğer özelleştirmelerin bulunduğu yerdir. 

### <a name="core-rule-sets"></a>Çekirdek kural kümeleri

Application Gateway üç kural kümesini destekler: yukarı ve 3,1, yukarı 3,0 ve yukarı 2.2.9. Bu kurallar web uygulamalarınızı kötü amaçlı etkinlikten korur.

Daha fazla bilgi için bkz. [Web uygulaması güvenlik duvarı Ayrıntılar kural grupları ve kuralları](application-gateway-crs-rulegroups-rules.md).

### <a name="custom-rules"></a>Özel kurallar

Application Gateway özel kuralları da destekler. Özel kurallarla, WAF üzerinden geçen her istek için değerlendirilen kendi kurallarınızı oluşturabilirsiniz. Bu kurallar, yönetilen kural kümelerindeki kuralların geri kalanından daha yüksek bir öncelik tutar. Bir koşul kümesi karşılanıyorsa, izin vermek veya engellemek için bir eylem yapılır. 

Daha fazla bilgi için bkz [. özel kurallar Application Gateway.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Bot azaltma (Önizleme)

Yönetilen bir bot koruma kuralı kümesi, WAF 'nizin yönetilen RuleSet 'in yanı sıra bilinen kötü amaçlı IP adreslerinden gelen istekleri engellemek veya günlüğe kaydetmek için etkinleştirilebilir. IP adresleri Microsoft Threat Intelligence akışından kaynaklıdır. Microsoft tehdit bilgileri, Azure Güvenlik Merkezi dahil olmak üzere çeşitli hizmetlerin kullandığı Intelligent Security Graph tarafından desteklenir.

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

Anomali Puanlama modunda, güvenlik duvarı önleme modundayken, herhangi bir kuralla eşleşen trafik hemen engellenmez. Kurallar belirli bir önem derecesine sahiptir: *kritik*, *hata*, *Uyarı*veya *bildirim*. Bu önem derecesi, istek için anomali puanı olarak adlandırılan sayısal bir değeri etkiler. Örneğin, bir *Uyarı* kuralı eşleşmesi 3 ' e katkıda bulunur. Bir *kritik* kural eşleşmesi 5. katkıda bulunur.

|Severity  |Değer  |
|---------|---------|
|Kritik     |5|
|Hata        |4|
|Uyarı      |3|
|Değiştirilebileceğini       |2|

Trafiği engellemek için anomali puanı 5 ' lik bir eşik değeri vardır. Bu nedenle, Application Gateway WAF 'nin önleme modunda bile bir isteği engellemesini engellemek için tek bir *kritik* kural eşleşmesi yeterlidir. Ancak, bir *Uyarı* kuralı eşleşmesi yalnızca 3 Ile anomali puanı artırır ve bu, trafiği engellemek için kendisi tarafından yeterli değildir.

> [!NOTE]
> Bir WAF kuralı trafik ile eşleştiğinde günlüğe kaydedilen ileti, "engellendi" eylem değerini içerir. Ancak trafik aslında yalnızca 5 veya üzeri bir anomali puanı için engellenmiştir.  

### <a name="waf-monitoring"></a>WAF izleme

Uygulama ağ geçidinizin durumunu izlemek önemlidir. WAF 'nizin sistem durumunu izleme ve koruduğu uygulamalar Azure Güvenlik Merkezi, Azure Izleyici ve Azure Izleyici günlükleri ile tümleştirme tarafından desteklenir.

![Application Gateway WAF tanılaması diyagramı](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure İzleyici

Application Gateway Günlükler [Azure izleyici](../../azure-monitor/overview.md)ile tümleşiktir. Bu, WAF uyarıları ve günlükleri dahil olmak üzere tanılama bilgilerini izlemenizi sağlar. Bu özelliğe portalda Application Gateway kaynağındaki **Tanılama** sekmesinden veya doğrudan Azure izleyici aracılığıyla erişebilirsiniz. Günlükleri etkinleştirme hakkında daha fazla bilgi için bkz. [Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Güvenlik Merkezi

[Güvenlik Merkezi](../../security-center/security-center-intro.md) , tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Azure kaynaklarınızın güvenliği üzerinde daha fazla görünürlük ve denetim sağlar. Application Gateway, [Güvenlik Merkezi ile tümleşiktir](../../application-gateway/application-gateway-integration-security-center.md). Güvenlik Merkezi, korumasız Web uygulamalarını algılamak için ortamınızı tarar. Bu güvenlik açığı bulunan kaynakları korumak için WAF Application Gateway önerilir. Güvenlik duvarlarını doğrudan güvenlik merkezi 'nden oluşturursunuz. Bu WAF örnekleri güvenlik merkezi ile tümleşiktir. Raporlama için Güvenlik Merkezi 'ne uyarılar ve sistem durumu bilgileri gönderir.

![Güvenlik Merkezi genel bakış penceresi](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel, ölçeklenebilir, bulutta yerel, güvenlik bilgileri olay yönetimi (SıEM) ve güvenlik Orchestration otomatik yanıtı (SOAR) çözümüdür. Azure Sentinel, bir uyarı algılama, tehdit görünürlüğü, proaktif arama ve tehdit yanıtı için tek bir çözüm sunarak kuruluş genelinde akıllı güvenlik Analizi ve tehdit bilgileri sunar.

Yerleşik Azure WAF güvenlik duvarı olayları çalışma kitabı ile WAF 'nizin güvenlik olaylarına genel bir bakış edinebilirsiniz. Bu, olayları, eşleşen ve engellenen kuralları ve güvenlik duvarı günlüklerinde günlüğe kaydedilen diğer her şeyi içerir. Aşağıda günlüğe kaydetme hakkında daha fazla bilgi bulabilirsiniz. 


![Sentinel](../media/ag-overview/sentinel.png)

#### <a name="logging"></a>Günlüğe kaydetme

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

## <a name="next-steps"></a>Sonraki adımlar

- [BIR WAF Ilkesi oluşturarak](create-waf-policy-ag.md) başlayın
- [WAF tarafından yönetilen kurallar](application-gateway-crs-rulegroups-rules.md) hakkında daha fazla bilgi edinin
- [Özel kurallar](custom-waf-rules-overview.md) hakkında daha fazla bilgi edinin
- [Azure ön kapıda Web uygulaması güvenlik duvarı](../afds/afds-overview.md) hakkında bilgi edinin

