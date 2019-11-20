---
title: Sorun giderme-Azure Web uygulaması güvenlik duvarı
description: Bu makalede, Azure Application Gateway Web uygulaması güvenlik duvarı (WAF) için sorun giderme bilgileri sağlanmaktadır
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74046186"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Azure Application Gateway Web uygulaması güvenlik duvarı (WAF) sorunlarını giderme

Web uygulaması güvenlik duvarınız (WAF) üzerinden geçmesi gereken istekler engelleniyorsa, yapabileceğiniz birkaç nokta vardır.

İlk olarak, [WAF genel bakış](ag-overview.md) ve [WAF yapılandırma](application-gateway-waf-configuration.md) belgelerini okuduğunuzdan emin olun. Ayrıca, [WAF izlemesini](../../application-gateway/application-gateway-diagnostics.md) etkinleştirdiğinizden emin olun. bu makalelerde WAF işlevlerinin nasıl, WAF kural kümelerinin nasıl çalıştığı ve WAF günlüklerine nasıl erişebileceğiniz açıklanmıştır.

## <a name="understanding-waf-logs"></a>WAF günlüklerini anlama

WAF günlüklerinin amacı, WAF tarafından eşlenen veya engellenen her isteği göstermek için kullanılır. Bu, eşleşen veya engellenen tüm değerlendirilen isteklerin bir muhasebendir. WAF 'nin olmayan bir isteği (yanlış pozitif bir değer) engellediğini fark ederseniz, birkaç şey yapabilirsiniz. Birincisi, daraltın ve belirli isteği bulun. Belirli URI, zaman damgası veya isteğin işlem KIMLIĞINI bulmak için günlüklere bakın. İlişkili günlük girdilerini bulduğunuzda, yanlış pozitif sonuçlar üzerinde işlem yapmaya başlayabilirsiniz.

Örneğin, WAF 'niz üzerinden geçirmek istediğiniz *1 = 1* dizesini içeren yasal bir trafiğinizin olduğunu varsayalım. İsteği denerseniz, WAF herhangi bir parametre veya alanda *1 = 1* dizenizi içeren trafiği engeller. Bu bir SQL ekleme saldırısından genellikle ilişkili bir dizedir. Günlüklere bakabilir ve isteğin zaman damgasını ve engellenen/eşleştirilen kuralları görebilirsiniz.

Aşağıdaki örnekte, aynı istek sırasında (TransactionId alanı kullanılarak) dört kuralın tetiklendiğini görebilirsiniz. Kullanıcı istek için bir sayısal/IP URL 'SI kullandığından, bir uyarı olduğundan, anomali puanı üç kez arttığı için Birincisi onu eşleştirdiğini söyler. Eşleşen bir sonraki kural, aradığınız bir sonraki kuraldır 942130 ' dir. `details.data` alanında *1 = 1* ' i görebilirsiniz. Bu, aynı zamanda bir uyarı olduğundan anomali Puanını üç kez daha arttırır. Genellikle, eylemi **eşleşen** her kural anomali Puanını arttırır ve bu noktada anomali puanı altı olur. Daha fazla bilgi için bkz. [anomali Puanlama modu](ag-overview.md#anomaly-scoring-mode).

En son iki günlük girdisi, anomali puanı yeterince yüksek olduğu için isteğin engellendiğini gösterir. Bu girişlerin diğer iki farklı eylemi vardır. Bunlar, isteği gerçekten *engellediği* gibi gösterir. Bu kurallar zorunludur ve devre dışı bırakılamaz. Bunlar kural olarak düşünülmemelidir, ancak WAF iç yapısının çekirdek altyapısı olarak daha fazla.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Hatalı pozitif durumlar düzeltiliyor

Bu bilgilerle ve 942130 kuralı, *1 = 1* dizesiyle eşleşen bir bilgi ile, bunu, trafiğinizi engellemesini durdurmak için birkaç şey yapabilirsiniz:

- Dışlama listesi kullan

   Dışlama listeleri hakkında daha fazla bilgi için bkz. [WAF yapılandırması](application-gateway-waf-configuration.md#waf-exclusion-lists) .
- Kuralı devre dışı bırakın.

### <a name="using-an-exclusion-list"></a>Dışlama listesi kullanma

Yanlış olumlu işleme hakkında bilinçli bir karar vermek için, uygulamanızın kullandığı teknolojilere alışmak önemlidir. Örneğin, teknoloji yığınınıza bir SQL Server yok deyin ve bu kurallarla ilgili yanlış pozitif sonuçlar elde edersiniz. Bu kuralların devre dışı bırakılması, güveninizi zayıflatmak zorunda değildir.

Dışlama listesi kullanmanın bir avantajı, isteğin yalnızca belirli bir bölümünün devre dışı bırakılmakta olmasından biridir. Bununla birlikte, bu, belirli bir dışlamanın, bir genel ayar olduğu için WAF 'niz üzerinden geçen tüm trafik için geçerli olduğu anlamına gelir. Örneğin, bu, *1 = 1* ' in gövdede belirli bir uygulama için geçerli bir istek olması, ancak diğerleri için değil, bir soruna neden olabilir. Bu, tüm isteği dışlamamak yerine belirli bir koşul karşılanırsa, gövde, üst bilgiler ve tanımlama bilgileri arasında seçim yapabileceğiniz bir avantajdır.

Bazen, belirli parametrelerin WAF 'ye, sezgisel olmayan bir şekilde geçirilme durumları vardır. Örneğin, Azure Active Directory kullanılarak kimlik doğrulanırken geçirilen bir belirteç vardır. Bu belirteç *__RequestVerificationToken*, genellikle Istek tanımlama bilgisi olarak geçirilir. Ancak, tanımlama bilgilerinin devre dışı bırakıldığı bazı durumlarda, bu belirteç istek özniteliği veya "arg" olarak da geçirilir. Bu durumda, *__RequestVerificationToken* dışlama listesine bir **istek özniteliği adı** olarak da eklendiğinden emin olmanız gerekir.

![İstisnalar](../media/web-application-firewall-troubleshoot/exclusion-list.png)

Bu örnekte, bir *Metin1*değerine eşit olan **istek özniteliği adını** dışlamak istiyorsunuz. Bu, güvenlik duvarı günlüklerinde öznitelik adını görebileceğiniz için görünür: **veri: eşleşen veriler: 1 = 1 bağımsız değişkenler içinde bulundu: Metin1:1 = 1**. Özniteliği **Metin1**. Bu öznitelik adını birkaç farklı yolla de bulabilirsiniz, bkz. [istek özniteliği adlarını bulma](#finding-request-attribute-names).

![WAF dışlama listeleri](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Kuralları devre dışı bırakma

Yanlış pozitif bir değer almanın başka bir yolu da, WAF 'nin kötü amaçlı olduğu girişte eşleşen kuralı devre dışı bırakmasıdır. WAF günlüklerini ayrıştırmış ve kuralı 942130 ' e doğru bir şekilde yaptıktan sonra, Azure portal devre dışı bırakabilirsiniz. Bkz. [Azure Portal aracılığıyla Web uygulaması güvenlik duvarı kurallarını özelleştirme](application-gateway-customize-waf-rules-portal.md).

Bir kuralı devre dışı bırakmanın bir avantajı, normalde engellenecek olan belirli bir koşulu içeren tüm trafiğin geçerli trafik olduğunu biliyorsanız, tüm WAF için bu kuralı devre dışı bırakabilirsiniz. Ancak, belirli bir kullanım durumunda yalnızca geçerli trafik varsa, genel bir ayar olduğundan tüm WAF için bu kuralı devre dışı bırakarak bir güvenlik açığı açarsınız.

Azure PowerShell kullanmak istiyorsanız, bkz. [PowerShell aracılığıyla Web uygulaması güvenlik duvarı kurallarını özelleştirme](application-gateway-customize-waf-rules-powershell.md). Azure CLı 'yı kullanmak istiyorsanız, bkz. [Azure CLI aracılığıyla Web uygulaması güvenlik duvarı kurallarını özelleştirme](application-gateway-customize-waf-rules-cli.md).

![WAF kuralları](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>İstek özniteliği adlarını bulma

[Fiddler](https://www.telerik.com/fiddler)'ın yardımıyla, bireysel istekleri inceleyeceksiniz ve bir Web sayfasındaki belirli alanları belirlersiniz. Bu, belirli alanların dışlama listeleri kullanılarak incelemeden dışlanmasına yardımcı olabilir.

Bu örnekte, *1 = 1* dizesinin girildiği alanın **Metin1**olarak adlandırıldığını görebilirsiniz.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

Bu, dışarıda bırakabilmeniz için bir alandır. Dışlama listeleri hakkında daha fazla bilgi edinmek için bkz. [Web uygulaması güvenlik duvarı istek boyutu sınırları ve dışlama listeleri](application-gateway-waf-configuration.md#waf-exclusion-lists). Aşağıdaki dışlamayı yapılandırarak bu durumda değerlendirmeyi dışarıda bırakabilirsiniz:

![WAF dışlaması](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Ayrıca, dışlama listesine eklemek için gerekenleri görmek üzere bilgileri almak için güvenlik duvarı günlüklerini inceleyebilirsiniz. Günlüğe kaydetmeyi etkinleştirmek için bkz. [Application Gateway Için arka uç sistem durumu, tanılama günlükleri ve ölçümler](../../application-gateway/application-gateway-diagnostics.md).

Güvenlik Duvarı günlüğünü inceleyin ve denetlemek istediğiniz isteğin gerçekleştiği saat için PT1H. json dosyasını görüntüleyin.

Bu örnekte, aynı TransactionId ile dört kuralın olduğunu ve tümünün tam olarak aynı anda gerçekleştiğini görebilirsiniz:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Bir anomali Puanlama sistemi ile birlikte çalışma hakkında bilgi sahibi olmak için ve 3,0 bu nesnelerin bir anomali Puanlama sistemiyle çalışması (bkz. [Azure Application Gateway Için Web uygulaması güvenlik duvarı](ag-overview.md)) **: engellendi** özelliği , toplam anomali puanına göre engelleniyor. Üzerine odaklanmak için kullanılan kurallar en üstteki iki.

Kullanıcı, Application Gateway gezinmek için sayısal bir IP adresi kullandığından, ilk giriş günlüğe kaydedilir ve bu durumda yok sayılabilir.

İkinci bir tane (kural 942130) ilginç bir değer. Ayrıntıların bir Düzenle eşleştiğini (1 = 1) görebilirsiniz ve alan **Metin1**olarak adlandırılır. **1 = 1** **değerine eşit** olan **istek özniteliği adını** dışlamak için aynı önceki adımları izleyin.

## <a name="finding-request-header-names"></a>İstek üst bilgisi adlarını bulma

Fiddler, istek üst bilgisi adlarını bulmak için bir kez yararlı bir araçtır. Aşağıdaki ekran görüntüsünde, *Içerik türü*, *Kullanıcı-aracı*vb. dahIl olmak üzere bu GET isteğinin üst bilgilerini görebilirsiniz.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

İstek ve yanıt üst bilgilerini görüntülemenin bir başka yolu da Chrome Geliştirici araçlarının içine bakmedir. F12 tuşuna basabilir veya sağ tıklama > -> **Geliştirici Araçları** **inceleyebilirsiniz** ve **ağ** sekmesini seçebilirsiniz. bir Web sayfası yükleyin ve incelemek istediğiniz isteğe tıklayın.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>İstek tanımlama bilgisi adlarını bulma

İstek tanımlama bilgileri içeriyorsa, bunları Fiddler 'da görüntülemek için **tanımlama bilgileri** sekmesi seçilebilir.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Yanlış pozitifleri ortadan kaldırmak için genel parametreleri kısıtla

- İstek gövdesi incelemesini devre dışı bırak

   **İnceleme isteği gövdesini** kapalı olarak ayarlayarak tüm trafiğin istek gövdeleri WAF 'niz tarafından değerlendirilmeyecektir. Bu, istek gövdelerinin uygulamanızda kötü amaçlı olmadığını bildiğiniz durumlarda yararlı olabilir.

   Bu seçeneği devre dışı bırakarak yalnızca istek gövdesi incelenemiyor. Bireysel durumlar, dışlama listesi işlevselliği kullanılarak dışlanmamışsa, üst bilgiler ve tanımlama bilgileri incede kalır.

- Dosya boyutu sınırları

   WAF 'niz için dosya boyutunu sınırlayarak, web sunucularınızda saldırı olasılığını sınırlandırırsınız. Büyük dosyaların karşıya yüklenmesine izin vererek, arka ucunuzun çok fazla olması riskini artırır. Dosya boyutunu uygulamanız için normal kullanım durumuyla sınırlamak, saldırıları önlemenin yalnızca başka bir yoludur.

   > [!NOTE]
   > Uygulamanızın belirli bir boyutun üzerine herhangi bir dosya yüklemesine gerek duymayacağını biliyorsanız, bir sınır ayarlayarak bunu kısıtlayabilirsiniz.

## <a name="firewall-metrics-waf_v1-only"></a>Güvenlik Duvarı ölçümleri (yalnızca WAF_v1)

V1 Web uygulaması güvenlik duvarları için aşağıdaki ölçümler portalda kullanılabilir: 

1. Web uygulaması güvenlik duvarı engellenen Istek sayısı engellenen isteklerin sayısı
2. Web uygulaması güvenlik duvarı engellenen kural sayısı, eşleşen tüm kuralların **ve** isteğin engellenmiş olduğu
3. Web uygulaması güvenlik duvarı toplam kural dağıtımı değerlendirme sırasında eşleşen tüm kurallar
     
Ölçümleri etkinleştirmek için portalda **ölçümler** sekmesini seçin ve üç ölçüden birini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bkz. [Application Gateway Web uygulaması güvenlik duvarını yapılandırma](tutorial-restrict-web-traffic-powershell.md).
