---
title: Azure ön kapısının Web uygulaması güvenlik duvarını (WAF) ayarlama
description: Bu makalede, ön kapıda WAF 'yi ayarlama hakkında bilgi edineceksiniz.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: c0879edc0e3fbd6cf6bcadc26dd862f95ecf4fd4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872362"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Azure ön kapısının Web uygulaması güvenlik duvarını (WAF) ayarlama
 
Azure tarafından yönetilen varsayılan kural kümesi, [OWASP Core kural kümesine](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) dayalıdır ve kutudan katı olacak şekilde tasarlanmıştır. Genellikle WAF kurallarının, WAF kullanılarak uygulamanın veya kuruluşun belirli ihtiyaçlarına uyacak şekilde ayarlanmış olması gerekir. Bu, yaygın olarak kural dışlamaları tanımlayarak, özel kurallar oluşturarak ve hatta sorunlara veya hatalı pozitif sonuçlar oluşturulmasına neden olabilecek kuralları devre dışı bırakarak elde edilir. Web uygulaması güvenlik duvarınız (WAF) üzerinden geçmesi gereken istekler engelleniyorsa, yapabileceğiniz birkaç nokta vardır.

İlk olarak, ön [kapı WAF genel bakışını](afds-overview.md) ve [ön kapı belgeleri Için WAF ilkesini](waf-front-door-create-portal.md) okuduğunuzdan emin olun. Ayrıca, [WAF izleme ve günlüğe kaydetme](waf-front-door-monitor.md)özelliğinin etkinleştirildiğinden emin olun. Bu makalelerde WAF işlevleri, WAF kural kümelerinin nasıl çalıştığı ve WAF günlüklerine nasıl erişeceğiniz açıklanmaktadır.
 
## <a name="understanding-waf-logs"></a>WAF günlüklerini anlama
 
WAF günlüklerinin amacı, WAF tarafından eşlenen veya engellenen her isteği göstermek için kullanılır. Bu, eşleşen veya engellenen tüm değerlendirilen isteklerin bir koleksiyonudur. WAF 'nin olmayan bir isteği (yanlış pozitif bir değer) engellediğini fark ederseniz, birkaç şey yapabilirsiniz. Birincisi, daraltın ve belirli isteği bulun. İsterseniz, [](./waf-front-door-configure-custom-response-code.md) `trackingReference` olayı kolayca tanımlamak ve bu belirli değerde bir günlük sorgusu gerçekleştirmek için alanı dahil etmek üzere özel bir yanıt iletisi yapılandırabilirsiniz. Belirli URI, zaman damgası veya isteğin istemci IP 'sini bulmak için günlüklere bakın. İlgili günlük girdilerini bulduğunuzda, yanlış pozitif sonuçlar üzerinde işlem yapmaya başlayabilirsiniz. 
 
Örneğin, `1=1` WAF 'niz üzerinden geçirmek istediğiniz dizeyi içeren meşru bir trafiğinizin olduğunu varsayalım. İstek şöyle görünür:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

İsteği denerseniz, WAF herhangi bir parametre veya alanda *1 = 1* dizenizi içeren trafiği engeller. Bu bir SQL ekleme saldırısından genellikle ilişkili bir dizedir. Günlüklere bakabilir ve isteğin zaman damgasını ve engellenen/eşleştirilen kuralları görebilirsiniz.
 
Aşağıdaki örnekte, bir `FrontdoorWebApplicationFirewallLog` kural eşleşmesi nedeniyle oluşturulan bir günlüğün araştırıyoruz. Son 24 saat içinde engellenmiş istekleri bulmak için aşağıdaki Log Analytics sorgusu kullanılabilir:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
Bu `requestUri` alanda, isteğin özellikle yapıldığını görebilirsiniz `/api/Feedbacks/` . Daha fazla bilgi için, alan içinde kural KIMLIĞINI bulduk `942110` `ruleName` . Kural KIMLIĞINI bilmenin yanı sıra, [OWASP ModSecurity çekirdek kuralı resmi deposu](https://github.com/coreruleset/coreruleset) ' na gidebilir ve kodu gözden geçirmek ve bu kuralın ne eşleştiğini tam olarak anlamak için bu [kural kimliğine](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) göre arama yapabilirsiniz. 
 
Ardından, `action` alanı denetleyerek, bu kuralın eşleştirildiğinde istekleri engelleyecek şekilde ayarlandığını ve isteğin aslında olarak ayarlandığı için WAF tarafından engellendiğini doğrulamamız gerektiğini biliyoruz `policyMode` `prevention` . 
 
Şimdi, alanındaki bilgileri kontrol edelim `details` . Burada `matchVariableName` ve bilgileri görebileceğiniz yerdir `matchVariableValue` . Bu kuralın, Web uygulamasının alanında *1 = 1* girdisi girişi nedeniyle tetiklendiğini öğreniyoruz `comment` .
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
Ayrıca, belirli bir WAF olayı hakkında bilginizi genişletmek için erişim günlüklerini denetlerken de bir değer vardır. Aşağıda, `FrontdoorAccessLog` Yukarıdaki olaya yanıt olarak oluşturulan günlüğü gözden geçiririz.
 
Bunların aynı değere göre ilgili günlükleri görebilirsiniz `trackingReference` . Ve gibi genel Öngörüler sağlayan çeşitli alanlar arasında, `userAgent` `clientIP` ve alanlarına dikkat etmeniz gerekir `httpStatusCode` `httpStatusDetails` . Burada, istemcinin bu isteğin reddedildiğini ve engellendiğini kesinlikle doğrulayan bir HTTP 403 yanıtı aldığını doğrulayabiliriz. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>Hatalı pozitifleri çözme
 
Yanlış olumlu işleme hakkında bilinçli bir karar vermek için, uygulamanızın kullandığı teknolojilere alışmak önemlidir. Örneğin, teknoloji yığınınıza bir SQL Server yok deyin ve bu kurallarla ilgili yanlış pozitif sonuçlar elde edersiniz. Bu kuralların devre dışı bırakılması, güveninizi zayıflatmak zorunda değildir. 

Bu bilgilerle ve 942110 kuralı, örneğimizde dize ile eşleşen bir bilgi olduğunda, `1=1` Bu meşru isteğin engellenmesini durdurmak için birkaç şey yapabiliriz:
 
* Dışlama listelerini kullan
  * Dışlama listeleri hakkında daha fazla bilgi için bkz. [ön kapı hizmeti dışlama listeleriyle Web uygulaması güvenlik duvarı (WAF)](waf-front-door-exclusion.md) . 
* WAF eylemlerini değiştirme
  * Bir istek bir kuralla eşleştiğinde gerçekleştirilecek eylemler hakkında daha fazla bilgi için bkz. [WAF eylemleri](afds-overview.md#waf-actions) .
* Özel kuralları kullanma
  * Özel kurallar hakkında daha fazla bilgi için bkz. [Azure ön kapısına sahip Web uygulaması güvenlik duvarı Için özel kurallar](waf-front-door-custom-rules.md) .
* Kuralları devre dışı bırakma 

> [!TIP]
> WAF aracılığıyla meşru isteklere izin vermek için bir yaklaşım seçerken bunu, olabildiğince dar olarak yapmayı deneyin. Örneğin, bir kuralı tamamen devre dışı bırakarak dışlama listesini kullanmak daha iyidir.

### <a name="using-exclusion-lists"></a>Dışlama listelerini kullanma

Dışlama listesi kullanmanın bir avantajı, yalnızca dışlamak üzere seçtiğiniz Match değişkeninin, belirtilen istek için artık incelenmemesini sağlar. Diğer bir deyişle, tüm isteğin İncelenme dışında, belirli bir koşul karşılanırsa, belirli bir koşul karşılanıyorsa dışarıda tutulacak istek başlıkları, istek tanımlama bilgileri, sorgu dizesi bağımsız değişkenleri veya istek gövdesi gönderi bağımsız değişkenleri arasında seçim yapabilirsiniz. İsteğin diğer belirtilemeyen değişkenleri normal olarak incelenmeye devam edecektir.
 
Dışlamaları genel bir ayar olduğunu göz önünde bulundurmanız önemlidir. Bu, yapılandırılan dışlamanın yalnızca belirli bir Web uygulaması veya URI değil WAF 'niz aracılığıyla geçen tüm trafiğe uygulanacağını gösterir. Örneğin, bu bir sorun olabilir, çünkü *1 = 1* belirli bir Web uygulamasının gövdesinde geçerli bir istek ise, ancak aynı WAF ilkesi altında diğerleri için değildir. Farklı uygulamalar için farklı dışlama listeleri kullanmak mantıklı olursa, her bir uygulama için farklı bir WAF ilkesi kullanmayı ve bunları her bir uygulamanın ön ucunda uygulamayı düşünün.
 
Yönetilen kuralların dışlama listelerini yapılandırırken, bir kural kümesi içindeki tüm kuralları, bir kural grubundaki tüm kuralları veya tek bir kuralı hariç bırakmayı seçebilirsiniz. Dışlama listesi [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject), [Azure CLı](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add), [REST API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)veya Azure Portal kullanılarak yapılandırılabilir.

* Kural düzeyinde dışlamaları
  * Kural düzeyinde dışlamaları uygulamak, belirtilen dışlamaların yalnızca bu kurala göre çözümlenmeyeceği anlamına gelir, ancak hala kural kümesindeki diğer tüm kurallara göre çözümlenmeyecektir. Bu, Dışlamalar için en ayrıntılı düzeydir ve bir olayda sorun giderirken WAF günlüklerinde bulduğunuz bilgilere göre yönetilen kural kümesini ince ayar yapmak için kullanılabilir.
* Kural grubu düzeyinde dışlamaları
  * Kural grubu düzeyinde dışlamaları uygulamak, belirtilen dışlamaların ilgili belirli kural türü kümesiyle çözümlenmeyeceği anlamına gelir. Örneğin, hariç tutulan bir kural grubu olarak *SQLI* seçildiğinde, tanımlanan istek dışlamaları, SQLi 'a özgü kuralların herhangi biri tarafından denetlenmeyeceğini gösterir, ancak hala *php*, *Rfi* veya *XSS* gibi diğer gruplardaki kurallara göre incelenebilir. Bu tür dışlama, uygulamanın belirli saldırı türlerine maruz olmadığından emin olduğumuz zaman yararlı olabilir. Örneğin, herhangi bir SQL veritabanına sahip olmayan bir uygulama, güvenlik düzeyi olmadan hiçbir *SQLi* kuralı hariç tutulur.
* Kural kümesi düzeyinde dışlamaları 
  * Kural kümesi düzeyinde dışlamaları uygulamak, belirtilen dışlamaların söz konusu kural kümesinde kullanılabilir olan herhangi bir güvenlik kuralına göre çözümlenmeyeceği anlamına gelir. Bu kapsamlı bir dışlamasıdır ve dikkatle kullanılmalıdır.

Bu örnekte, en ayrıntılı düzeyde (tek bir kurala dışlamanın uygulanması) bir dışarıda bırakma işlemi gerçekleştirilecektir ve içeren Match değişken **İstek gövdesi Post args adını** hariç tutmak istiyoruz `comment` . Bu, güvenlik duvarı günlüğünde değişken ayrıntılarını Eşleştir ' i görebileceğiniz için görünür: `"matchVariableName": "PostParamValue:comment"` . Özniteliği `comment` . Bu öznitelik adını birkaç farklı yolla de bulabilirsiniz, bkz. [istek özniteliği adlarını bulma](#finding-request-attribute-names).

![Dışlama kuralları](../media/waf-front-door-tuning/exclusion-rules.png)

![Belirli bir kural için kural dışlama](../media/waf-front-door-tuning/exclusion-rule.png)

Bazen, belirli parametrelerin WAF 'ye, sezgisel olmayan bir şekilde geçirilme durumları vardır. Örneğin, Azure Active Directory kullanılarak kimlik doğrulanırken geçirilen bir belirteç vardır. Bu belirteç, `__RequestVerificationToken` genellikle istek tanımlama bilgisi olarak geçirilir. Ancak, tanımlama bilgilerinin devre dışı bırakıldığı bazı durumlarda bu belirteç bir istek Post bağımsız değişkeni olarak de geçirilir. Bu nedenle, Azure AD belirtecinin yanlış pozitiflerini ele almak için `__RequestVerificationToken` hem hem de için dışlama listesine eklendiğinden emin olmanız gerekir `RequestCookieNames` `RequestBodyPostArgsNames` .

Bir alan adında (*seçici*) Dışlamalar, değerin artık WAF tarafından değerlendirilmeyeceği anlamına gelir. Ancak, alan adının kendisi değerlendirilmeye devam eder ve nadir olarak bir WAF kuralıyla eşleştirebilir ve bir eylemi tetikleyebiliriz.

![Kural kümesi için kural dışlama](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>WAF eylemlerini değiştirme

WAF kurallarının davranışını işlemenin bir başka yolu da bir isteğin bir kuralla eşleştiğinde gerçekleştirilecek eylemi seçmektir. Kullanılabilir eylemler şunlardır: [Izin ver, engelle, günlük ve yeniden yönlendirme](afds-overview.md#waf-actions).

Bu örnekte, varsayılan eylem *bloğunu* kural 942110 ' daki *günlük* eylemine değiştirdik. Bu, WAF 'nin isteği günlüğe vermesine ve kalan düşük öncelikli kurallara göre aynı isteği değerlendirmeye devam etmesine neden olur.

![WAF eylemleri](../media/waf-front-door-tuning/actions.png)

Aynı isteği gerçekleştirdikten sonra, günlüklere geri başvurabileceğiniz ve bu isteğin kural KIMLIĞI 942110 ' de bir eşleşme olduğunu ve `action_s` alanın artık *blok* yerine *günlüğü* gösterdiğini görüyoruz. Daha sonra, `trackingReference_s` bilgileri içerecek ve bu istekle başka ne olduğunu görmek için günlük sorgusunu genişlettik.

![Birden çok kural eşleştirmelerinin gösterildiği günlük](../media/waf-front-door-tuning/actions-log.png)

İlginçtir ki, kural KIMLIĞI 942110 işlendikten sonra farklı bir SQLI kural eşleşmesi olduğunu biliyoruz. Aynı isteğin kural KIMLIĞI 942310 ' de ve varsayılan eylem *bloğunun* tetiklendiği bu kez aynı isteği eşleştirildiği.

WAF ayarlaması veya sorun giderme sırasında *günlük* eylemini kullanmanın başka bir avantajı, belirli bir kural grubundaki birden çok kuralın belirli bir isteği eşleştirmekte ve engelleyip engellemediğini belirleyebilmeniz olabilir. Daha sonra uygun düzeyde (örneğin, kural veya kural grubu düzeyinde) dışlamalarınızı oluşturabilirsiniz. 

### <a name="using-custom-rules"></a>Özel kuralları kullanma

Bir WAF kuralı eşleşmesinin ne olduğunu tanımladıktan sonra, WAF 'nin olaya nasıl yanıt vereceğini ayarlamak için özel kurallar kullanabilirsiniz. Özel kurallar yönetilen kuralların önüne işlenir, birden fazla koşul içerebilir ve eylemleri [Izin ver, Reddet, günlüğe kaydet veya yeniden yönlendirme](afds-overview.md#waf-actions)olabilir. Bir kural eşleşmesi olduğunda WAF altyapısı işlemeyi durduruyor. Bu, daha düşük öncelikli ve yönetilen kurallara sahip diğer özel kuralların artık yürütülmeyeceği anlamına gelir.

Aşağıdaki örnekte, İki koşuldan oluşan özel bir kural oluşturduk. İlk koşul, `comment` istek gövdesindeki değeri arıyor. İkinci koşul, `/api/Feedbacks/` istek URI 'sindeki değeri arıyor.

Özel bir kural kullanmak WAF kurallarınızı ince ayar yaparken ve hatalı pozitif sonuçlar ile ilgilenirken en ayrıntılı özelliği kullanmanıza olanak sağlar. Bu durumda, `comment` aynı WAF ilkesindeki birden çok sitede veya uygulamada olabilecek istek gövdesi değerine göre yalnızca eylem gerçekleşmiyor. Ayrıca, belirli bir istek URI 'SI ile eşleşmesi için başka bir koşul da ekleyerek `/api/Feedbacks/` , bu özel kuralın geliştirdiğimiz açık kullanım durumu için gerçekten geçerli olduğundan emin olmamız gerekir. Bu, farklı koşullara karşı gerçekleştirildiyse aynı saldırının WAF altyapısı tarafından incelenmesini ve engellenmesini sağlar.

![Günlük](../media/waf-front-door-tuning/custom-rule.png)

Günlüğü araştırırken, `ruleName_s` alanın oluşturduğumuz özel kurala verilen adı içerdiğini görebilirsiniz: `redirectcomment` . `action_s`Alanında, bu olay Için *yeniden yönlendirme* eyleminin alındığını görebilirsiniz. Bu `details_matches_s` alanda her iki koşulun de ayrıntılarını görebiliriz.

### <a name="disabling-rules"></a>Kuralları devre dışı bırakma

Yanlış pozitif bir değer almanın başka bir yolu da, WAF 'nin kötü amaçlı olduğu girişte eşleşen kuralı devre dışı bırakmasıdır. WAF günlüklerini ayrıştırmış ve kuralı 942110 ' e doğru bir şekilde yaptıktan sonra, Azure portal devre dışı bırakabilirsiniz. Bkz. [Azure Portal kullanarak Web uygulaması güvenlik duvarı kurallarını özelleştirme](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Bir kuralı devre dışı bırakmak, belirli bir koşulu karşılayan tüm isteklerin gerçekten meşru isteklerde olduğundan emin olduğunuzda veya kuralın ortamınız için geçerli olmadığından emin olduğunuzda (örneğin, SQL arka ucu olmayan bir SQL ekleme kuralını devre dışı bıraktığınızdan) bir avantajdır. 
 
Ancak, bir kuralı devre dışı bırakmak WAF ilkesiyle ilişkili tüm ön uç konakları için geçerli olan genel bir ayardır. Bir kuralı devre dışı bırakmayı seçtiğinizde, WAF ilkesiyle ilişkili diğer ön uç konakları için koruma veya algılama olmadan açığa çıkan güvenlik açıklarını terk edebilirsiniz.
 
Yönetilen bir kuralı devre dışı bırakmak için Azure PowerShell kullanmak istiyorsanız, [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) nesne belgelerine bakın. Azure CLı kullanmak istiyorsanız [`az network front-door waf-policy managed-rules override`](/cli/azure/network/front-door/waf-policy/managed-rules/override) belgelere bakın.

![WAF kuralları](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> WAF ilkenizde yaptığınız değişiklikleri belgelemek iyi bir fikirdir. Yanlış pozitif algılamayı göstermek için örnek istekleri ekleyin ve özel bir kural eklediğinizi, bir kuralı veya RuleSet 'i devre dışı bırakıldığını veya bir özel durum eklendiğini açıkça açıklayın. Uygulamanızı gelecekte yeniden tasarlayabilmeniz ve değişikliklerinizin hala geçerli olduğunu doğrulamanız gerekiyorsa bu belgeler yararlı olabilir. Ayrıca, herhangi bir zamanda denetleyeceğiniz ya da WAF ilkesini varsayılan ayarlarından neden yeniden yapılandırmanızın gerektiği konusunda da yardımcı olabilir.

## <a name="finding-request-fields"></a>İstek alanlarını bulma

[Fiddler](https://www.telerik.com/fiddler)gibi bir tarayıcı proxy 'si kullanarak, bireysel istekleri inceleyebilir ve bir Web sayfası için hangi alanların çağrıldığını belirleyebilirsiniz. Bu, bazı alanları WAF 'deki dışlama listelerini kullanarak incelemeden dışlamaya ihtiyaç duyduğumuz zaman yararlıdır.

### <a name="finding-request-attribute-names"></a>İstek özniteliği adlarını bulma
 
Bu örnekte, `1=1` dizenin girildiği alanı olarak görebilirsiniz `comment` . Bu veriler bir POST isteğinin gövdesinde geçirildi.

![Gövdeyi gösteren Fiddler isteği](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Bu, dışarıda bırakabilmeniz için bir alandır. Dışlama listeleri hakkında daha fazla bilgi edinmek için bkz. [Web uygulaması güvenlik duvarı dışlama listeleri](./waf-front-door-exclusion.md). Aşağıdaki dışlamayı yapılandırarak bu durumda değerlendirmeyi dışarıda bırakabilirsiniz:

![Dışlama kuralı](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

Ayrıca, dışlama listesine eklemek için gerekenleri görmek üzere bilgileri almak için güvenlik duvarı günlüklerini inceleyebilirsiniz. Günlüğe kaydetmeyi etkinleştirmek için bkz. [Azure ön kapıda ölçümleri ve günlükleri izleme](./waf-front-door-monitor.md).

`PT1H.json`Denetlemek istediğiniz isteğin gerçekleştiği saat için dosyadaki güvenlik duvarı günlüğünü inceleyin. `PT1H.json` dosyalar, `FrontDoorWebApplicationFirewallLog` ve tanılama günlüklerinin depolandığı depolama hesabı kapsayıcılarında kullanılabilir `FrontDoorAccessLog` .

Bu örnekte, isteği engelleyen kuralı (aynı Işlem başvurusuyla) ve tam olarak aynı zamanda gerçekleşeceğini görebilirsiniz:

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Azure tarafından yönetilen kural kümelerinin nasıl çalıştığı hakkında bilginiz ile (bkz. [Azure ön kapısındaki Web uygulaması güvenlik duvarı](afds-overview.md)), *Action: Block* özelliği olan kuralın istek gövdesinde eşleşen verilere göre engellediğini bilirsiniz. Ayrıntıların bir Düzenle ( `1=1` ) ile eşleştiğini ve alanın adlandırıldığını görebilirsiniz `comment` . İçindeki istek gövdesi Post args adını dışlamak için aynı önceki adımları izleyin `comment` .

### <a name="finding-request-header-names"></a>İstek üst bilgisi adlarını bulma

Fiddler, istek üst bilgisi adlarını bulmak için bir kez yararlı bir araçtır. Aşağıdaki ekran görüntüsünde, Içerik türü, Kullanıcı-aracı vb. dahil olmak üzere bu GET isteğinin üst bilgilerini görebilirsiniz. Ayrıca, WAF 'de dışlamaları ve özel kurallar oluşturmak için istek üst bilgilerini de kullanabilirsiniz.

![Üst bilgi gösteren Fiddler isteği](../media/waf-front-door-tuning/fiddler-request-header-name.png)

İstek ve yanıt üst bilgilerini görüntülemenin bir başka yolu da tarayıcınızın Edge veya Chrome gibi Geliştirici araçlarının içine bakmamız. F12 tuşuna basarak veya sağ tıklama >   ->  **Geliştirici Araçları** İnceleme ' ye basabilir ve **ağ** sekmesini seçebilirsiniz. bir Web sayfası yükleyin ve incelemek istediğiniz isteğe tıklayın.

![Ağ denetçisi isteği](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>İstek tanımlama bilgisi adlarını bulma

İstek tanımlama bilgileri içeriyorsa, bunları Fiddler 'da görüntülemek için tanımlama bilgileri sekmesi seçilebilir. Ayrıca, WAF 'de dışlamaları veya özel kurallar oluşturmak için tanımlama bilgisi bilgileri kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Web uygulaması güvenlik duvarı](../overview.md)hakkında bilgi edinin.
- [Front Door oluşturmayı](../../frontdoor/quickstart-create-front-door.md) öğrenin.
