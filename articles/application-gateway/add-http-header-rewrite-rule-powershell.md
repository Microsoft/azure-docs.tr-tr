---
title: HTTP üst bilgilerini Azure Application Gateway yeniden yazma
description: Bu makalede Azure PowerShell kullanarak Azure Application Gateway HTTP üstbilgilerini yeniden yazma hakkında bilgi sağlanır
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: b674bdf59959715b666c521c0d0631f86f71b1d3
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093733"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>HTTP isteği ve yanıt üst bilgilerini Azure Application Gateway yeniden yazma-Azure PowerShell

Bu makalede, istek ve yanıtlara HTTP üstbilgilerini yeniden yazmak için [Application Gateway v2 SKU](./application-gateway-autoscaling-zone-redundant.md) örneğini yapılandırmak üzere Azure PowerShell nasıl kullanılacağı açıklanır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

- Bu makaledeki adımları tamamlayabilmeniz için Azure PowerShell yerel olarak çalıştırmanız gerekir. Ayrıca, az Module Version 1.0.0 veya daha yeni bir sürümün yüklü olması gerekir. `Import-Module Az`' İ çalıştırın ve ardından `Get-Module Az` yüklediğiniz sürümü tespit edin. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell sürümünü doğruladıktan sonra, Azure ile bağlantı oluşturmak için `Login-AzAccount` komutunu çalıştırın.
- Application Gateway v2 SKU örneğiniz olması gerekir. V1 SKU 'sunda üstbilgileri yeniden yazma desteklenmiyor. V2 SKU 'SU yoksa, başlamadan önce bir [Application Gateway v2 SKU](./tutorial-autoscale-ps.md) örneği oluşturun.

## <a name="create-required-objects"></a>Gerekli nesneleri oluştur

HTTP üstbilgisini yeniden yazmayı yapılandırmak için, bu adımları gerçekleştirmeniz gerekir.

1. HTTP üstbilgisi yeniden yazma için gereken nesneleri oluşturun:

   - **Requestheaderconfiguration**: yeniden yazmayı düşündüğünüz istek üst bilgisi alanlarını ve üst bilgilerin yeni değerini belirtmek için kullanılır.

   - **Responseheaderconfiguration**: yeniden yazmayı düşündüğünüz yanıt üst bilgisi alanlarını ve üst bilgilerin yeni değerini belirtmek için kullanılır.

   - **Actionset**: daha önce belirtilen istek ve yanıt üst bilgilerinin yapılandırmasını içerir.

   - **Koşul**: isteğe bağlı bir yapılandırma. Yeniden yazma koşulları, HTTP (S) isteklerinin ve yanıtlarının içeriğini değerlendirir. HTTP (S) isteği veya yanıtı yeniden yazma koşuluyla eşleşiyorsa, yeniden yazma eylemi gerçekleşir.

     Birden fazla koşulu bir eylemle ilişkilendirirseniz, eylem yalnızca tüm koşullar karşılandığında oluşur. Diğer bir deyişle, işlem mantıksal ve işlemdir.

   - **RewriteRule**: birden çok yeniden yazma eylemi/yeniden yazma koşulu kombinasyonu içeriyor.

   - **Rulesequence**: yeniden yazma kurallarının yürütülme sırasını belirlemesine yardımcı olan isteğe bağlı bir yapılandırma. Bu yapılandırma, bir yeniden yazma kümesinde birden fazla yeniden yazma kuralına sahip olduğunuzda yararlıdır. Daha düşük bir kural sırası değeri olan bir yeniden yazma kuralı önce çalışır. Aynı kural sırası değerini iki yeniden yazma kuralına atarsanız, yürütme sırası belirleyici değildir.

     RuleSequence öğesini açıkça belirtmezseniz, varsayılan 100 değeri ayarlanır.

   - **Rewriterutaset**: istek yönlendirme kuralıyla ilişkilendirilecek çoklu yeniden yazma kuralları içerir.

2. Rewriterulet kümesini bir yönlendirme kuralına ekleyin. Yeniden yazma yapılandırması, kaynak dinleyicisine yönlendirme kuralı aracılığıyla eklenir. Temel bir yönlendirme kuralı kullandığınızda, üst bilgi yeniden yazma yapılandırması bir kaynak dinleyicisi ile ilişkilendirilir ve genel üst bilgi yeniden yazma işlemi olur. Yol tabanlı bir yönlendirme kuralı kullandığınızda, üst bilgi yeniden yazma yapılandırması URL yol eşlemesinde tanımlanmıştır. Bu durumda, yalnızca bir sitenin belirli yol alanı için geçerlidir.

Birden çok HTTP üst bilgisi yeniden yazma kümesi oluşturabilir ve her bir yeniden yazma kümesini birden çok dinleyiciyle uygulayabilirsiniz. Ancak belirli bir dinleyiciye yalnızca bir yeniden yazma kümesi uygulayabilirsiniz.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>HTTP üstbilgisi yeniden yazma kuralı yapılandırmasını belirtin

Bu örnekte, konum üstbilgisi azurewebsites.net öğesine bir başvuru içerdiğinde HTTP yanıtında konum üstbilgisini yeniden yazarak bir yeniden yönlendirme URL 'sini değiştireceksiniz. Bunu yapmak için, yanıttaki konum üstbilgisinin azurewebsites.net içerip içermediğini değerlendirmek için bir koşul ekleyeceğiz. Bu kalıbı kullanacağız `(https?)://.*azurewebsites.net(.*)$` . `{http_resp_Location_1}://contoso.com{http_resp_Location_2}`Üst bilgi değeri olarak kullanacağız. Bu değer, *azurewebsites.net* konum üstbilgisindeki *contoso.com* ile değiştirilir.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet -Condition $condition
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Uygulama ağ geçidinizin yapılandırmasını alma

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>İstek yönlendirme kuralınızın yapılandırmasını alma

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>HTTP üstbilgilerini yeniden yazma yapılandırması ile uygulama ağ geçidini güncelleştirme

Bu örnekte, yeniden yazma kümesi basit bir yönlendirme kuralına göre anında ilişkilendirilir. Yol tabanlı yönlendirme kuralı söz konusu olduğunda, ilişkilendirme varsayılan olarak etkinleştirilmez. Yeniden yazma kümesi, Portal aracılığıyla uygulanması gereken yolların denetlenmesi yoluyla ya da her yol için yeniden Writerulet kümesini belirten bir URL yol haritası yapılandırması sağlayarak etkinleştirilebilir.  

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name $rewriteRuleSet.Name  -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name $reqRoutingRule.Name -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Yeniden yazma kuralını Sil

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Sonraki adımlar

Bazı yaygın kullanım örneklerini ayarlama hakkında daha fazla bilgi edinmek için bkz. [ortak üst bilgi yeniden yazma senaryoları](./rewrite-http-headers.md).