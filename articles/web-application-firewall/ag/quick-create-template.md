---
title: 'Hızlı başlangıç: Application Gateway Azure Resource Manager şablonunda Azure WAF v2 oluşturma'
titleSuffix: Azure Application Gateway
description: Azure Application Gateway 'de Web uygulaması güvenlik duvarı v2 oluşturmak için Azure Resource Manager hızlı başlangıç şablonu (ARM şablonu) kullanmayı öğrenin.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: victorh
ms.custom: subject-armqs
ms.openlocfilehash: 4c5d5c4cafee9402e2afd7ddc047fd3b68e84540
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92091330"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak Application Gateway Azure WAF v2 oluşturma

Bu hızlı başlangıçta, Application Gateway üzerinde bir Azure Web uygulaması güvenlik duvarı v2 oluşturmak için bir Azure Resource Manager şablonu (ARM şablonu) kullanırsınız.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu şablon Azure Application Gateway basit bir Web uygulaması güvenlik duvarı v2 oluşturur. Buna genel bir IP ön uç IP adresi, HTTP ayarları, bağlantı noktası 80 üzerinde temel dinleyici içeren bir kural ve arka uç havuzu dahildir. Bir IP adresi eşleşme türüne dayalı olarak arka uç havuzuna giden trafiği engellemek için özel kurala sahip bir WAF ilkesi oluşturulur.

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/ag-docs-wafv2/) alınmıştır.

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json":::

Şablonda birden çok Azure kaynağı tanımlanmış:

- [**Microsoft. Network/applicationgateway 'ler**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft. Network/Publicıpaddresses**](/azure/templates/microsoft.network/publicipaddresses) : uygulama ağ geçidi için bir tane ve sanal makineler için iki.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. COMPUTE/virtualmachines**](/azure/templates/microsoft.compute/virtualmachines) : iki sanal makine
- [**Microsoft. Network/NetworkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : sanal makineler için iki
- [**Microsoft. COMPUTE/virtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : IIS ve Web sayfalarını yapılandırmak için

## <a name="deploy-the-template"></a>Şablonu dağıtma

ARM şablonunu Azure 'a dağıtma:

1. Azure 'da oturum açmak için **Azure 'A dağıt** ' ı seçin ve şablonu açın. Şablon, IIS çalıştıran arka uç havuzunda bir uygulama ağ geçidi, ağ altyapısı ve iki sanal makine oluşturur.

   [![Azure’a dağıtın](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Kaynak grubunuzu seçin veya oluşturun.
3. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum** ' u seçin ve ardından **satın al**' ı seçin. Dağıtımın tamamlanması 10 dakika veya daha uzun sürebilir.

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Uygulama ağ geçidi oluşturmak için IIS gerekli olmasa da, Azure 'ın uygulama ağ geçidinde bir WAF v2 'yi başarıyla oluşturduğunu doğrulamak üzere arka uç sunucularına yüklenir.

Uygulama ağ geçidini test etmek için IIS kullanın:

1. Uygulama ağ geçidinin genel IP adresini **genel bakış** sayfasında bulabilirsiniz. ![ Uygulama Ağ Geçidi genel IP adresini kaydedin ](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) veya **tüm kaynaklar**' ı seçip Arama kutusuna *Myagpublicıpaddress* girebilir ve arama sonuçlarında bunu seçebilirsiniz. Azure **genel bakış** SAYFASıNDA genel IP adresini görüntüler.
2. Genel IP adresini kopyalayın ve ardından bu IP adresine gözatabilmek için tarayıcınızın adres çubuğuna yapıştırın.
3. Yanıtı denetleyin. **403 yasaklanmış** bir yanıt, WAF 'nin başarıyla oluşturulduğunu doğrular ve arka uç havuzuna yapılan bağlantıları engeller.
4. Özel kuralı **trafiğe Izin verecek** şekilde değiştirin.
  Kaynak grubu adınızı değiştirerek aşağıdaki Azure PowerShell betiği çalıştırın:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Tarayıcınızı birden çok kez yenileyin ve hem myVM1 hem de myVM2 bağlantıları görmeniz gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulama ağ geçidiyle oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu silin. Bu, uygulama ağ geçidini ve tüm ilgili kaynakları kaldırır.

Kaynak grubunu silmek için `Remove-AzResourceGroup` cmdlet 'ini çağırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Azure portal kullanarak bir Web uygulaması güvenlik duvarı ile uygulama ağ geçidi oluşturma](application-gateway-web-application-firewall-portal.md)