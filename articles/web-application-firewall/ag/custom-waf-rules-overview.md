---
title: Application Gateway Azure Web uygulaması güvenlik duvarı (WAF) v2 özel kuralları
description: Bu makalede, Azure Application Gateway 'de Web uygulaması güvenlik duvarı (WAF) v2 özel kurallarına genel bir bakış sunulmaktadır.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 9a5f64687937479d65f94010bbe4f0a5f1cf5ca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548232"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Azure Application Gateway Web uygulaması güvenlik duvarı v2 için özel kurallar

Azure Application Gateway Web uygulaması güvenlik duvarı (WAF) v2, birçok farklı saldırı türünden koruma sağlayan, önceden yapılandırılmış, platform tarafından yönetilen bir kural kümesiyle birlikte gelir. Bu saldırılar siteler arası betik oluşturma, SQL ekleme ve diğer kişiler içerir. Bir WAF yöneticisiyseniz, çekirdek kural kümesi (sp_configure) kurallarını genişletmek için kendi kurallarınızı yazmak isteyebilirsiniz. Kurallarınız, eşleşen ölçütlere göre istenen trafiği engelleyebilir veya izin verebilir.

Özel kurallar, WAF üzerinden geçen her istek için değerlendirilen kendi kurallarınızı oluşturmanızı sağlar. Bu kurallar, yönetilen kural kümelerindeki kuralların geri kalanından daha yüksek bir öncelik tutar. Özel kurallar bir kural adı, kural önceliği ve eşleşen koşulların bir dizisini içerir. Bu koşullar karşılanıyorsa, bir eylem yapılır (izin vermek veya engellemek için).

Örneğin, 192.168.5.4/24 aralığındaki bir IP adresinden gelen tüm istekleri engelleyebilirsiniz. Bu kuralda, işleç *Ipmatch* Ise, MATCHVALUES değeri IP adres aralığıdır (192.168.5.4/24) ve işlem trafiği engeller. Kuralın adını ve önceliğini de ayarlarsınız.

Özel kurallar, güvenlik gereksinimlerinizi karşılayan daha gelişmiş kurallar oluşturmak için bileşik mantık kullanımını destekler. Örneğin, (koşul 1 **ve** koşul 2) **veya** koşul 3). Bu, koşul 1 **ve** koşul 2 karşılanırsa **veya** koşul 3 karşılanıyorsa, WAF 'nin özel kuralda belirtilen eylemi yapması gerektiği anlamına gelir.

Aynı kural içindeki farklı eşleşen koşullar, **ve** kullanarak her zaman bileşik bir şekilde yapılır. Örneğin, belirli bir IP adresinden gelen trafiği ve yalnızca belirli bir tarayıcıyı kullanıyorsa engelleyin.

İki farklı **koşul kullanmak istiyorsanız** , iki koşulun farklı kurallarda olması gerekir. Örneğin, belirli bir IP adresinden gelen trafiği engelleyin veya belirli bir tarayıcı kullanılıyorsa trafiği engelleyin.

> [!NOTE]
> WAF özel kural sayısı üst sınırı 100 ' dir. Application Gateway limitleri hakkında daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

Normal ifadeler Ayrıca, benzer RuleSets gibi özel kurallarda de desteklenir. Örnekler için bkz. örnek 3 ve 5 [özel Web uygulaması güvenlik duvarı kuralları oluşturma ve kullanma](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Vs. engellemeye izin verme

Özel kurallar sayesinde trafiğin izin verilmesi ve engellenmesi basittir. Örneğin, bir IP adresi aralığından gelen tüm trafiği engelleyebilirsiniz. İstek belirli bir tarayıcıdan geliyorsa, trafiğe izin vermek için başka bir kural yapabilirsiniz.

Bir şeye izin vermek için, `-Action` parametrenin **izin ver**' e ayarlandığından emin olun. Bir şeyi engellemek için, `-Action` parametresinin **Block** olarak ayarlandığından emin olun.

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Önceki `$BlockRule` Azure Resource Manager aşağıdaki özel kurala eşlenir:

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationCondition": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Bu özel kural bir ad, öncelik, bir eylem ve eylemin gerçekleşmesi için karşılanması gereken eşleşen koşulların dizisini içerir. Bu alanların daha ayrıntılı açıklaması için aşağıdaki alan açıklamalarına bakın. Örneğin özel kurallar için bkz. [özel Web uygulaması güvenlik duvarı kuralları oluşturma ve kullanma](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Özel kurallar için alanlar

### <a name="name-optional"></a>Ad [isteğe bağlı]

Kuralın adı.  Günlüklerde görüntülenir.

### <a name="priority-required"></a>Öncelik [gerekli]

- Kural değerlendirme sırasını belirler. Değer ne kadar düşükse, kural değerlendirme daha erken yapılır. İzin verilen Aralık 1-100 ' dir. 
- Tüm özel kurallar arasında benzersiz olmalıdır. Öncelik 40 olan bir kural, öncelik 80 olan bir kuraldan önce değerlendirilir.

### <a name="rule-type-required"></a>Kural türü [gerekli]

Şu anda **Matchrule** olmalıdır.

### <a name="match-variable-required"></a>Match değişkeni [gerekli]

Değişkenlerden biri olmalıdır:

- RemoteAddr – uzak bilgisayar bağlantısının IP adresi/ana bilgisayar adı
- RequestMethod – HTTP Istek yöntemi (GET, POST, PUT, DELETE vb.)
- QueryString – URI 'de değişken
- PostArgs: POST gövdesinde gönderilen bağımsız değişkenler. Bu Match değişkenini kullanan özel kurallar yalnızca ' Content-Type ' üst bilgisi ' Application/x-www-form-urlencoded ' ve ' multipart/form-Data ' olarak ayarlandıysa geçerlidir.
- RequestUri – isteğin URI 'SI
- RequestHeaders – isteğin üst bilgileri
- RequestBody: Bu, tüm istek gövdesini bir bütün olarak içerir. Bu Match değişkenini kullanan özel kurallar yalnızca ' Content-Type ' üst bilgisi ' Application/x-www-form-urlencoded ' olarak ayarlandığında uygulanır. 
- RequestCookies – isteğin tanımlama bilgileri

### <a name="selector-optional"></a>Seçici [isteğe bağlı]

MatchVariable koleksiyonunun alanını açıklar. Örneğin, matchVariable, RequestHeaders ise, seçici *Kullanıcı Aracısı* üstbilgisinde olabilir.

### <a name="operator-required"></a>İşleç [gerekli]

Aşağıdaki işleçlerden biri olmalıdır:

- Ipmatch-yalnızca Match değişkeni *Remoteaddr* olduğunda kullanılır
- Eşittir – giriş, MatchValue ile aynıdır
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- Coğrafi eşleşme (Önizleme)

### <a name="negate-condition-optional"></a>Negate koşulu [isteğe bağlı]

Geçerli koşulu geçersiz kılar.

### <a name="transform-optional"></a>Dönüştürme [isteğe bağlı]

Eşleşmesinden önce yapılacak dönüşümlerdeki adlara sahip dizelerin listesi. Bunlar aşağıdaki dönüşümler olabilir:

- Küçük harf
- Trim
- URL kod çözme
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Değerleri Eşleştir [gerekli]

Eşleştirilecek değerler listesi, *veya*' Ed olarak düşünülebilir. Örneğin, bu IP adresleri veya diğer dizeler olabilir. Değer biçimi Previous işlecine bağlıdır.

### <a name="action-required"></a>Eylem [gerekli]

- Allow: işlemi, diğer tüm kuralları atlayarak verir. Belirtilen istek izin verilenler listesine eklenir ve eşleştirdikten sonra, istek daha fazla değerlendirmeyi durduruyor ve arka uç havuzuna gönderilir. İzin verilenler listesindeki kurallar, başka özel kurallar veya yönetilen kurallar için değerlendirilmez.
- Block: *Secdefaultaction* (algılama/önleme modu) temelinde işlemi engeller. Izin verme eyleminde olduğu gibi, istek değerlendirildikten ve blok listesine eklendikten sonra, değerlendirme durdurulur ve istek engellenir. Aynı koşulları karşıladıktan sonra herhangi bir istek değerlendirilmeyecek ve yalnızca engellenmeyecektir. 
- GNLK – kuralın günlüğe yazmasına Izin verir, ancak kuralların geri kalanının değerlendirme için çalışmasına izin verir. Diğer özel kurallar öncelik sırasına göre değerlendirilir ve ardından yönetilen kurallar gelir.

## <a name="geomatch-custom-rules-preview"></a>Geomatch özel kuralları (Önizleme)

Özel kurallar, uygulamalarınızın ve Güvenlik ilkelerinizin tam ihtiyaçlarını karşılamak için özel kurallar oluşturmanıza olanak sağlar. Web uygulamalarınıza olan erişimi ülkeye/bölgeye göre kısıtlayabilirsiniz. Daha fazla bilgi için bkz. [Geomatch özel kuralları (Önizleme)](geomatch-custom-rules.md).

## <a name="next-steps"></a>Sonraki adımlar

Özel kurallar hakkında bilgi aldıktan sonra [kendi özel kurallarınızı oluşturun](create-custom-waf-rules.md).
