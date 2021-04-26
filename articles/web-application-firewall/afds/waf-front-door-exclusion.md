---
title: Azure ön kapıdaki Web uygulaması güvenlik duvarı dışlama listeleri-Azure portal
description: Bu makalede, Azure portal ile Azure 'da dışlama listeleri yapılandırması hakkında bilgi sağlanır.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 83baf03c414d9b0f7acb6a93db03794a539a3c58
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860842"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Ön kapı hizmeti dışlama listeleriyle Web uygulaması güvenlik duvarı (WAF) 

Bazen Web uygulaması güvenlik duvarı (WAF), uygulamanız için izin vermek istediğiniz bir isteği engelleyebilirler. Örneğin Active Directory, kimlik doğrulaması için kullanılan belirteçleri ekler. Bu belirteçler, WAF kurallarından yanlış pozitif bir pozitif tetikleyebildiği özel karakterler içerebilir. WAF dışlama listeleri, bir WAF değerlendirmesinden belirli istek özniteliklerini atlamanızı sağlar.  Dışlama listesi  [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject), [Azure CLı](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add), [REST API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)veya Azure Portal kullanılarak yapılandırılabilir. Aşağıdaki örnek Azure portal yapılandırmasını gösterir. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Dışlama listelerini Azure portal kullanarak yapılandırma
Özel **durumları yönetme** işlemi, WAF portalından **yönetilen kurallar** altında erişilebilir

![Dışlama ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ yönetme exclusion_add yönetme](../media/waf-front-door-exclusion/exclusion2.png)

 Örnek bir dışlama listesi: ![ yönet exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

Bu örnek, *Kullanıcı* üstbilgisi alanındaki değeri dışlar. Geçerli bir istek, bir SQL ekleme kuralını tetikleyen bir dize içeren *Kullanıcı* alanı içerebilir. Bu durumda, WAF kuralının alanındaki herhangi bir şeyi değerlendirmemesi için *Kullanıcı* parametresini dışarıda bırakabilirsiniz.

Aşağıdaki öznitelikler ada göre dışlama listelerine eklenebilir. Kullandığınız alanların değerleri WAF kurallarına göre değerlendirilmez, ancak adları değerlendirilir. Dışlama Listeleri alanın değerinin incelemesini kaldırır.

* İstek üst bilgisi adı
* İstek tanımlama bilgisi adı
* Sorgu dizesi bağımsız değişkenleri adı
* İstek gövdesi Post bağımsız değişkenleri adı

Tam bir istek üst bilgisi, gövde, tanımlama bilgisi veya sorgu dizesi özniteliği eşleşmesi belirtebilirsiniz.  Ya da isteğe bağlı olarak kısmi eşleşmeler belirtebilirsiniz. Aşağıdaki işleçler desteklenen eşleşme ölçütleridir:

- **Eşittir**: Bu işleç tam eşleşme için kullanılır. Örneğin, **yataya** adı adlı bir üst bilgi seçmek için, Selector 'un **yataya** kümesi olarak ayarlandığı Equals işlecini kullanın.
- **Ile başlar**: Bu işleç, belirtilen Seçici değeriyle başlayan tüm alanlarla eşleşir.
- **Şununla biter**: Bu işleç, belirtilen Seçici değeriyle biten tüm istek alanlarıyla eşleşir.
- **Contains**: Bu işleç, belirtilen Seçici değerini içeren tüm istek alanlarıyla eşleşir.
- **Eşittir any**: Bu işleç tüm istek alanlarıyla eşleşir. * Seçici değeridir.

Üst bilgi ve tanımlama bilgisi adları büyük/küçük harfe duyarlıdır.

Bir üst bilgi değeri, tanımlama bilgisi değeri, post bağımsız değişkeni değeri veya sorgu bağımsız değişken değeri bazı kurallar için hatalı pozitif sonuçlar oluşturuyorsa, isteğin bu bölümünün kural tarafından dikkate alınması dışında bırakabilirsiniz:


|WAF günlüklerinden matchVariableName  |Portalda kural dışlama  |
|---------|---------|
|Tanımlama, ıevalue: SOME_NAME        |İstek tanımlama bilgisi adı eşittir SOME_NAME|
|HeaderValue: SOME_NAME        |İstek üst bilgisi adı SOME_NAME eşittir|
|PostParamValue: SOME_NAME     |İstek gövdesi Post args adı eşittir SOME_NAME|
|QueryParamValue: SOME_NAME    |Sorgu dizesi args adı eşittir SOME_NAME|


Şu anda yalnızca WAF günlüklerinde yukarıdaki matchVariableNames için kural dışlamalarını destekliyoruz. Diğer tüm Matchvariableadları için, hatalı pozitif sonuçlar veren kuralları devre dışı bırakmanız ya da açıkça bu isteklere izin veren özel bir kural oluşturmanız gerekir. Özellikle, matchVariableName, tanımlama bilgisi, HeaderName, PostParamName veya QueryParamName olduğunda, adın kendisi kuralın tetikleneceği anlamına gelir. Kural dışlamanın Şu anda bu matchVariableNames desteği yok.


*Foo* adlı bir istek gövdesi Post bağımsız değişkenlerini hariç tutdıysanız, hiçbir kural WAF günlüklerinizi matchVariableName olarak, PostParamValue: foo olarak göstermelidir. Ancak, post param değerlerinin ınitialbodycontents 'in bir parçası olduğundan, FOO Post parametresi değeri ile eşleşen matchVariableName ınitialbodycontents içeren bir kural görebilirsiniz.

Dışlama listelerini yönetilen kural kümesi içindeki tüm kurallara, belirli bir kural grubu için kurallara veya önceki örnekte gösterildiği gibi tek bir kurala uygulayabilirsiniz.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Web uygulaması güvenlik duvarı günlüklerine göre dışlama tanımlama
 [Azure Web uygulaması güvenlik duvarı izleme ve günlüğe kaydetme](waf-front-door-monitor.md) , engellenen bir istek ile eşleşen ayrıntıları gösterir. Bir üst bilgi değeri, tanımlama bilgisi değeri, post bağımsız değişkeni değeri veya sorgu bağımsız değişken değeri bazı kurallar için hatalı pozitif sonuçlar oluşturuyorsa, isteğin bu bölümünü kural tarafından dikkate alınacaksa hariç bırakabilirsiniz. Aşağıdaki tabloda, WAF günlüklerinden ve karşılık gelen dışlama koşullarında örnek değerler gösterilmektedir.

|WAF günlüklerinden matchVariableName    |Portalda kural dışlama|
|--------|------|
|Tanımlama, ıevalue: SOME_NAME  |İstek tanımlama bilgisi adı eşittir SOME_NAME|
|HeaderValue: SOME_NAME  |İstek üst bilgisi adı SOME_NAME eşittir|
|PostParamValue: SOME_NAME|  İstek gövdesi Post args adı eşittir SOME_NAME|
|QueryParamValue: SOME_NAME| Sorgu dizesi args adı eşittir SOME_NAME|


## <a name="next-steps"></a>Sonraki adımlar

WAF ayarlarınızı yapılandırdıktan sonra, WAF günlüklerinizi görüntülemeyi öğrenin. Daha fazla bilgi için bkz. [ön kapı tanılama](../afds/waf-front-door-monitor.md).