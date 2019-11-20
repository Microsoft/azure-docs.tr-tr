---
title: V2 özel kuralları oluşturma ve kullanma
titleSuffix: Azure Web Application Firewall
description: Bu makalede, Azure Application Gateway 'de Web uygulaması güvenlik duvarı (WAF) v2 özel kuralları oluşturma hakkında bilgi sağlanır.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 4efa60a48a540efdd835b106afa5872057ae3d53
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74046426"
---
# <a name="create-and-use-web-application-firewall-v2-custom-rules-on-application-gateway"></a>Web uygulaması güvenlik duvarı v2 özel kurallarını oluşturma ve kullanma Application Gateway

Azure Application Gateway Web uygulaması güvenlik duvarı (WAF) v2, Web uygulamaları için koruma sağlar. Bu koruma, Open Web Application Security Project (OWASP) çekirdek kural kümesi (CCR) tarafından sağlanır. Bazı durumlarda, özel gereksinimlerinizi karşılamak için kendi özel kurallarınızı oluşturmanız gerekebilir. WAF özel kuralları hakkında daha fazla bilgi için bkz. [özel Web uygulaması güvenlik duvarı kurallarına genel bakış](custom-waf-rules-overview.md).

Bu makalede, v2 WAF 'niz ile oluşturabileceğiniz ve kullanabileceğiniz bazı örnek özel kurallar gösterilmektedir. Azure PowerShell kullanarak bir WAF 'yi özel bir kuralla dağıtmayı öğrenmek için, bkz. [Azure PowerShell kullanarak Web uygulaması güvenlik duvarı özel kurallarını yapılandırma](configure-waf-custom-rules.md).

>[!NOTE]
> Uygulama ağ geçidiniz WAF katmanını kullanmıyor ise, uygulama ağ geçidini WAF katmanına yükseltme seçeneği sağ bölmede görüntülenir.

![WAF 'yi etkinleştir][fig1]

## <a name="example-1"></a>Örnek 1

Web sitenizde gezinmeyi engellemek istediğiniz *evilbot* adlı bir bot olduğunu bilirsiniz. Bu durumda, istek üst bilgilerinde Kullanıcı Aracısı *evbotu* 'yı engellemelisiniz.

Logic: p

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Buna karşılık gelen JSON şu şekildedir:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

Bu özel kural kullanılarak dağıtılan bir WAF 'yi görmek için bkz. [Azure PowerShell kullanarak Web uygulaması güvenlik duvarı özel kuralını yapılandırma](configure-waf-custom-rules.md).

### <a name="example-1a"></a>Örnek 1a

Bir normal ifade kullanarak aynı şeyi gerçekleştirebilirsiniz:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Regex `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Ve karşılık gelen JSON:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

### <a name="example-2"></a>Örnek 2

GeoMatch işlecini kullanarak BIZIMLE gelen trafiğe izin vermek istiyorsunuz:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr `

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator GeoMatch `
   -MatchValue "US" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name "allowUS" `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow
```

Ve karşılık gelen JSON:

```json
  {
    "customRules": [
      {
        "name": "allowUS",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Allow",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```



## <a name="example-3"></a>Örnek 3

198.168.5.0/24 aralığındaki IP adreslerinden gelen tüm istekleri engellemek istiyorsunuz.

Bu örnekte, bir IP adres aralığından gelen tüm trafiği engellemelisiniz. Kuralın adı *myrule1* ve öncelik 10 olarak ayarlanır.

Logic: p

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Buna karşılık gelen JSON şu şekildedir:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "IPMatch",
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      }
    ]
  }
```

Karşılık gelen yukarı ve kural: `SecRule REMOTE_ADDR "@ipMatch 192.168.5.0/24" "id:7001,deny"`

## <a name="example-4"></a>Örnek 4

Bu örnek için, Kullanıcı Aracısı *evbotbot*'ı ve 192.168.5.0/24 aralığındaki trafiği engellemek istiyorsunuz. Bunu gerçekleştirmek için iki ayrı eşleştirme koşulu oluşturabilir ve bunları aynı kurala yerleştirebilirsiniz. Bu, 192.168.5.0/24 aralığındaki Kullanıcı Aracısı üst bilgisinde **ve** IP adreslerinde bulunan her iki taraf da *eşleştiriliyorsa* , isteğin engellenmesini sağlar.

Logic: p **ve** q

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

 $variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

 $rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1, $condition2 `
   -Action Block
```

Buna karşılık gelen JSON şu şekildedir:

```json
{ 

    "customRules": [ 
      { 
        "name": "myrule", 
        "ruleType": "MatchRule", 
        "priority": 10, 
        "action": "block", 
        "matchConditions": [ 
            { 
              "matchVariable": "RemoteAddr", 
              "operator": "IPMatch", 
              "negateCondition": false, 
              "matchValues": [ 
                "192.168.5.0/24" 
              ] 
            }, 
            { 
              "matchVariable": "RequestHeaders", 
              "selector": "User-Agent", 
              "operator": "Contains", 
              "transforms": [ 
                "Lowercase" 
              ], 
              "matchValues": [ 
                "evilbot" 
              ] 
            } 
        ] 
      } 
    ] 
  } 
```

## <a name="example-5"></a>Örnek 5

Bu örnekte, isteğin *192.168.5.0/24*IP adresi aralığının dışında veya Kullanıcı Aracısı dizesinin *Chrome* olmadığından (Kullanıcı Chrome tarayıcısını kullanmıyor anlamına gelir) engellemek isteyebilirsiniz. Bu mantık **veya**kullandığından, iki koşul aşağıdaki örnekte görüldüğü gibi ayrı kurallarda bulunur. trafiği engellemek için *myrule1* ve *myrule2* öğelerinin her ikisinin de eşleşmesi gerekir.

Logic: **Not** (p **ve** q) = p değil **veya** q değil.

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $True

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "chrome" `
   -Transform Lowercase `
   -NegationCondition $True

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block
```

Ve karşılık gelen JSON:

```json
{
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "IPMatch",
            "negateCondition": true,
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "selector": "User-Agent",
            "operator": "Contains",
            "negateCondition": true,
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "chrome"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-6"></a>Örnek 6

Özel SQLI 'ı engellemek istiyorsunuz. Burada kullanılan mantık **veya**, tüm değerler *RequestUri*'de olduğundan, tüm *matchvalues* , virgülle ayrılmış bir listede olabilir.

Logic: p **veya** q **ya da** r

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri 
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1", "drop tables", "'—" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule4 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Karşılık gelen JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule4",
        "ruleType": "MatchRule",
        “priority”: 10
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1",
              "drop tables",
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

Alternatif Azure PowerShell:

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
-Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "drop tables" `
   -NegationCondition $False

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block

$variable3 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition3 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable3 `
   -Operator Contains `
   -MatchValue "’—" `
   -NegationCondition $False

$rule3 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule3 `
   -Priority 30 `
   -RuleType MatchRule `
   -MatchCondition $condition3 `
   -Action Block
```

Karşılık gelen JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "drop tables"
            ]
          }
        ]
      },
      {
        "name": "myrule3",
        "ruleType": "MatchRule",
        "priority": 30,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="next-steps"></a>Sonraki adımlar

Özel kurallarınızı oluşturduktan sonra WAF günlüklerinizi görüntülemeyi öğrenebilirsiniz. Daha fazla bilgi için bkz. [tanılama Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/create-custom-waf-rules/1.png
