---
title: Application Gateway Azure Web uygulaması güvenlik duvarı (WAF) v2 özel kuralları
description: Bu makalede, Azure Application Gateway 'de Web uygulaması güvenlik duvarı (WAF) v2 özel kurallarına genel bir bakış sunulmaktadır.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.openlocfilehash: 0ac37378797c59d79af5d026200b68154836c5ac
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585405"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Azure Application Gateway Web uygulaması güvenlik duvarı v2 için özel kurallar

Azure Application Gateway Web uygulaması güvenlik duvarı (WAF) v2, birçok farklı saldırı türünden koruma sağlayan, önceden yapılandırılmış, platform tarafından yönetilen bir kural kümesiyle birlikte gelir. Bu saldırılar siteler arası betik oluşturma, SQL ekleme ve diğer kişiler içerir. Bir WAF yöneticisiyseniz, çekirdek kural kümesi (sp_configure) kurallarını artırmak için size kendi kuralları yazmak isteyebilirsiniz. Kurallarınız, eşleşen ölçütlere göre istenen trafiği engelleyebilir veya izin verebilir.

Özel kurallar, WAF üzerinden geçen her istek için değerlendirilen kendi kurallarınızı oluşturmanızı sağlar. Bu kurallar, yönetilen kural kümelerindeki kuralların geri kalanından daha yüksek bir öncelik tutar. Özel kurallar bir kural adı, kural önceliği ve eşleşen koşulların bir dizisini içerir. Bu koşullar karşılanıyorsa, bir eylem yapılır (izin vermek veya engellemek için).

Örneğin, 192.168.5.4/24 aralığındaki bir IP adresinden gelen tüm istekleri engelleyebilirsiniz. Bu kuralda, işleç *Ipmatch*Ise, MATCHVALUES değeri IP adres aralığıdır (192.168.5.4/24) ve işlem trafiği engeller. Kuralın adını ve önceliğini de ayarlarsınız.

Özel kurallar, güvenlik gereksinimlerinizi karşılayan daha gelişmiş kurallar oluşturmak için bileşik mantık kullanımını destekler. Örneğin, (koşul 1 **ve** koşul 2) **veya** koşul 3).  Bu örnekte, koşul 1 **ve** koşul 2 karşılanırsa **veya** koşul 3 karşılanırsa, WAF 'nin özel kuralda belirtilen eylemi yapması gerekir.

Aynı kural içindeki farklı eşleşen koşullar, **ve**kullanarak her zaman bileşik bir şekilde yapılır. Örneğin, belirli bir IP adresinden gelen trafiği ve yalnızca belirli bir tarayıcıyı kullanıyorsa engelleyin.

**Ya** da iki farklı koşul istiyorsanız, iki koşulun farklı kurallarda olması gerekir. Örneğin, belirli bir IP adresinden gelen trafiği engelleyin veya belirli bir tarayıcı kullanılıyorsa trafiği engelleyin.

> [!NOTE]
> WAF özel kural sayısı üst sınırı 100 ' dir. Application Gateway limitleri hakkında daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../../azure-subscription-service-limits.md#application-gateway-limits).

Normal ifadeler Ayrıca, benzer RuleSets gibi özel kurallarda de desteklenir. Bunlara örnekler için bkz. örnek 3 ve 5 [özel Web uygulaması güvenlik duvarı kuralları oluşturma ve kullanma](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Vs. engellemeye izin verme

Özel kurallar sayesinde trafiğin izin verilmesi ve engellenmesi basittir. Örneğin, bir IP adresi aralığından gelen tüm trafiği engelleyebilirsiniz. İstek belirli bir tarayıcıdan geliyorsa, trafiğe izin vermek için başka bir kural yapabilirsiniz.

Bir şeye izin vermek için `-Action` parametresinin **Izin ver**olarak ayarlandığından emin olun. Bir şeyi engellemek için `-Action` parametresinin **Engelle**olarak ayarlandığından emin olun.

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

Önceki `$BlockRule`, Azure Resource Manager aşağıdaki özel kurala eşlenir:

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
            "negationConditon": false,
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

Kuralın adıdır. Bu ad günlüklerde görüntülenir.

### <a name="priority-required"></a>Öncelik [gerekli]

- Kural değerlendirme sırasını belirler. Değer ne kadar düşükse, kural değerlendirme daha erken yapılır. İzin verilen Aralık 1-100 ' dir. 
- Tüm özel kurallar arasında benzersiz olmalıdır. Öncelik 40 olan bir kural, öncelik 80 olan bir kuraldan önce değerlendirilir.

### <a name="rule-type-required"></a>Kural türü [gerekli]

Şu anda **Matchrule**olmalıdır.

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
- Ile başlıyor
- EndsWith
- Regex
- Coğrafi eşleşme (Önizleme)

### <a name="negate-condition-optional"></a>Negate koşulu [isteğe bağlı]

Geçerli koşulu geçersiz kılar.

### <a name="transform-optional"></a>Dönüştürme [isteğe bağlı]

Eşleşmesinden önce yapılacak dönüşümlerdeki adlara sahip dizelerin listesi. Bunlar aşağıdaki dönüşümler olabilir:

- Küçük harf
- Kırpma
- URL kod çözme
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Değerleri Eşleştir [gerekli]

Eşleştirilecek değerler listesi, *veya*' Ed olarak düşünülebilir. Örneğin, bu IP adresleri veya diğer dizeler olabilir. Değer biçimi Previous işlecine bağlıdır.

### <a name="action-required"></a>Eylem [gerekli]

- Allow: işlemi, sonraki tüm kuralları atlayarak bir şekilde yetkilendirir. Bu, belirtilen isteğin izin verilenler listesine eklendiği ve eşleştirildiği anlamına gelir, istek daha fazla değerlendirmeyi durduruyor ve arka uç havuzuna gönderilir. İzin verilenler listesindeki kurallar, başka özel kurallar veya yönetilen kurallar için değerlendirilmez.
- Block: *Secdefaultaction* (algılama/önleme modu) temelinde işlemi engeller. Izin verme eyleminde olduğu gibi, istek değerlendirildikten ve blok listesine eklendikten sonra, değerlendirme durdurulur ve istek engellenir. Aynı koşulları karşıladıktan sonra herhangi bir istek değerlendirilmeyecektir ve yalnızca engellenir. 
- GNLK – kuralın günlüğe yazmasına Izin verir, ancak kuralların geri kalanının değerlendirme için çalışmasına izin verir. Sonraki özel kurallar öncelik sırasına göre değerlendirilir ve ardından yönetilen kuralları izler.

## <a name="geomatch-custom-rules-preview"></a>Geomatch özel kuralları (Önizleme)

Özel kurallar, uygulamanızın tam ihtiyaçlarına ve Güvenlik ilkelerinize uyacak şekilde uyarlanmış kuralların oluşturulmasına olanak sağlar. Artık, Web uygulamalarınıza erişimi, genel önizlemede bulunan ülkeye/bölgeye göre kısıtlayabileceksiniz. Tüm özel kurallarda olduğu gibi, bu mantık uygulamanızın ihtiyaçlarını karşılamak için diğer kurallarla birlikte iade edilebilir. 

   > [!NOTE]
   > Geomatch özel kuralları Orta Güney ABD ve Kuzey Avrupa kullanılabilir. Portalda bunlara erişmek için lütfen herkes için etkin olana kadar [Bu bağlantıyı](https://aka.ms/AppGWWAFGeoMatch) kullanın. 

Geomatch işlecini kullanıyorsanız, seçiciler aşağıdaki iki basamaklı ülke kodlarından herhangi biri olabilir. 

|Ülke kodu | Ülke adı |
| ----- | ----- |
| AD | Andorra |
| AE | Birleşik Arap Emirlikleri|
| AF | Afganistan|
| AG | Antigua ve Barbuda|
| AL | Arnavutluk|
| AM | Ermenistan|
| AO | Angola|
| AR | Arjantin|
| AS | Amerikan Samoası|
| AT | Avusturya|
| AU | Avustralya|
| AZ | Azerbaycan|
| BA | Bosna Hersek|
| BB | Barbados|
| BD | Bangladeş|
| BE | Belçika|
| BF | Burkina Faso|
| BG | Bulgaristan|
| BH | Bahreyn|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthélimy|
| BN | Barış Yurdu Brunei Devleti|
| BO | Bolivya|
| BR | Brezilya|
| BS | Bahamalar|
| BT | Butan|
| BW | Botsvana|
| BY | Belarus|
| BZ | Beliz|
| CA | Kanada|
| CD | Kongo Demokratik Cumhuriyeti|
| CF | Orta Afrika Cumhuriyeti|
| CH | İsviçre|
| CI | Fildişi Kıyısı|
| CL | Şili|
| CM | Kamerun|
| CN | Çin|
| CO | Kolombiya|
| CR | Kosta Rika|
| CU | Küba|
| CV | Cabo Verde|
| CY | Kıbrıs|
| CZ | Çek Cumhuriyeti|
| DE | Almanya|
| DK | Danimarka|
| DO | Dominik Cumhuriyeti|
| DZ | Cezayir|
| EC | Ekvador|
| EE | Estonya|
| EG | Mısır|
| ES | İspanya|
| ET | Etiyopya|
| FI | Finlandiya|
| FJ | Fiji|
| FM | Mikronezya Federe Devletleri|
| GS | Fransa|
| GB | Birleşik Krallık|
| GE | Gürcistan|
| GF | Fransız Ginesi|
| GH | Gana|
| GN | Gine|
| GP | Guadalupe|
| GR | Yunanistan|
| GT | Guatemala|
| GY | Guyana|
| HK | Hong Kong ÖIB|
| HN | Honduras|
| HR | Hırvatistan|
| HT | Haiti|
| HU | Macaristan|
| Kimlik | Endonezya|
| IE | İrlanda|
| IL | İsrail|
| IN | Hindistan|
| IQ | Irak|
| IR | İran İslam Cumhuriyeti|
| IS | İzlanda|
| BT | İtalya|
| JM | Jamaika|
| JO | Ürdün|
| JP | Japonya|
| KE | Kenya|
| KG | Kırgızistan|
| KH | Kamboçya|
| KI | Kiribati|
| KN | Saint Kitts ve Nevis|
| KP | Kore Demokratik Halk Cumhuriyeti|
| KR | Kore Cumhuriyeti|
| KW | Kuveyt|
| KY | Kayman Adaları|
| KZ | Kazakistan|
| LA | Laos Demokratik Halk Cumhuriyeti|
| LB | Lübnan|
| LI | Lihtenştayn|
| LK | Sri Lanka|
| LR | Liberya|
| LS | Lesoto|
| LT | Litvanya|
| LU | Lüksemburg|
| LV | Letonya|
| LY | Libya |
| MA | Fas|
| MD | Moldova Cumhuriyeti|
| MG | Madagaskar|
| MK | Kuzey Makedonya|
| ML | Mali|
| MM | Myanmar|
| MN | Moğolistan|
| MO | Makao ÖIB|
| MQ | Martinik|
| MR | Moritanya|
| MT | Malta|
| MV | Maldivler|
| MW | Malavi|
| MX | Meksika|
| MY | Malezya|
| MZ | Mozambik|
| NA | Namibya|
| NE | Nijer|
| NG | Nijerya|
| NI | Nikaragua|
| NL | Hollanda|
| NO | Norveç|
| NP | Nepal|
| NR | Nauru|
| NZ | Yeni Zelanda|
| OM | Umman|
| PA | Panama|
| PE | Peru|
| PH | Filipinler|
| PK | Pakistan|
| PL | Polonya|
| PR | Porto Riko|
| PT | Portekiz|
| PW | Palau|
| PY | Paraguay|
| QA | Katar|
| RE | Reunion|
| RO | Romanya|
| RS | Sırbistan|
| RU | Rusya Federasyonu|
| RW | Rwanda|
| SA | Suudi Arabistan|
| SD | Sudan|
| SE | İsveç|
| SG | Singapur|
| SI | Slovenya|
| SK | Slovakya|
| SN | Senegal|
| SO | Somali|
| SR | Surinam|
| SS | Güney Sedan|
| SV | El Salvador|
| SY | Suriye Arap Cumhuriyeti|
| SZ | Svaziland|
| TC | Turks ve Caicos Adaları|
| TG | Togo|
| TH | Tayland|
| TN | Tunus|
| TR | Türkiye|
| TT | Trinidad ve Tobago|
| TW | Tayvan|
| TZ | Tanzanya Birleşik Cumhuriyeti|
| UA | Ukrayna|
| UG | Uganda|
| ABD | Amerika Birleşik Devletleri|
| UY | Uruguay|
| UZ | Özbekistan|
| VC | Saint Vincent ve Grenadinler|
| VE | Venezuela|
| VG | Virgin Adaları, İngiliz|
| VI | Virgin Adaları, ABD|
| VN | Vietnam|
| ZA | Güney Afrika|
| ZM | Zambiya|
| ZW | Zimbabve|

## <a name="next-steps"></a>Sonraki adımlar

Özel kurallar hakkında bilgi aldıktan sonra [kendi özel kurallarınızı oluşturun](create-custom-waf-rules.md).
