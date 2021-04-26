---
title: 'Hızlı başlangıç: Azure ön kapılı Azure PowerShell yüksek kullanılabilirliği ayarlama'
description: Bu hızlı başlangıçta, Azure PowerShell kullanarak yüksek kullanılabilirlik ve yüksek performanslı küresel bir Web uygulaması oluşturmak için Azure ön kapısının nasıl kullanılacağı gösterilir.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: 17fa18e1f29622b941c281b9cdce27f6e72eb13a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739989"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak yüksek oranda kullanılabilir küresel bir Web uygulaması için ön kapı oluşturma

Yüksek düzeyde kullanılabilir ve yüksek performanslı bir genel Web uygulaması oluşturmak için Azure PowerShell kullanarak Azure ön kapısını kullanmaya başlayın.

Ön kapı, Web trafiğini bir arka uç havuzundaki belirli kaynaklara yönlendirir. Ön uç etki alanını tanımladınız, bir arka uç havuzuna kaynak ekleyecek ve bir yönlendirme kuralı oluşturacaksınız. Bu makalede, iki Web uygulaması kaynağına sahip bir arka uç havuzunun basit bir yapılandırması ve varsayılan yol eşleştirme "/*" kullanılarak tek bir yönlendirme kuralı kullanılmaktadır.

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="PowerShell kullanarak ön kapı ortamı diyagramı diyagramı." border="false":::

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell yerel olarak veya Azure Cloud Shell yüklendi

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Kaynak grubu oluşturma

Azure 'da, ilgili kaynakları bir kaynak grubuna ayırabilirsiniz. Var olan bir kaynak grubunu kullanabilir ya da yeni bir tane oluşturabilirsiniz.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Bir Web uygulamasının iki örneğini oluşturma

Bu hızlı başlangıç, farklı Azure bölgelerinde çalışan bir Web uygulamasının iki örneğini gerektirir. Her iki Web uygulaması örneği de etkin/etkin modda çalışır, bu nedenle bir tane trafik alabilir. Bu yapılandırma, biri yük devretme görevi gören bir etkin/bağımsız yapılandırmadan farklıdır.

Henüz bir Web uygulamanız yoksa, iki örnek Web uygulaması kurmak için aşağıdaki betiği kullanın.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-1" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-2" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanEastUS
```

## <a name="create-a-front-door"></a>Front Door oluşturma

Bu bölüm ön kapısının aşağıdaki bileşenlerini nasıl oluşturabileceğiniz ve yapılandırabileceğiniz hakkında ayrıntılı bilgi alabilirsiniz:
    
* Ön uç nesnesi, ön kapı varsayılan etki alanını içerir.
* Arka uç havuzu, ön kapı yükünün istemci isteğinizi dengeleyen bir eşdeğer arka uçları kümesidir.
* Yönlendirme kuralı, ön uç konunuzu ve eşleşen URL yolu modelini belirli bir arka uç havuzuna eşler.

### <a name="create-a-frontend-object"></a>Ön uç nesnesi oluşturma

Ön uç nesnesi ön kapı için ana bilgisayar adını yapılandırır. Varsayılan olarak, ana bilgisayar adı **. azurefd.net* sonekine sahip olur.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>Arka uç havuzunu oluşturma

Arka uç havuzu, bu hızlı başlangıç başlangıcında oluşturulan iki Web uygulamasından oluşur. Bu adımda tanımlanan durum araştırması ve Yük Dengeleme ayarları varsayılan değerleri kullanır.

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>Yönlendirme kuralı oluşturma

Yönlendirme kuralı, arka uç havuzunu ön uç etki alanına eşler ve varsayılan yol eşleştirme değerini "/*" olarak ayarlar.

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>Ön kapıyı oluşturma

Gerekli nesneleri oluşturduğdığınıza göre, ön kapıyı oluşturun:

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

Dağıtım başarılı olduktan sonra, sonraki bölümdeki adımları izleyerek test edebilirsiniz.

## <a name="test-the-front-door"></a>Ön kapıyı test etme

Ön kapıya ait ana bilgisayar adını almak için aşağıdaki komutu çalıştırın.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Bir Web tarayıcısı açın ve komutlardan al ana bilgisayar adını girin. Ön kapı, isteğinizi arka uç kaynaklarından birine yönlendirir. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Ön kapı sınama sayfası":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Ön kapıda oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu silin. Kaynak grubunu sildiğinizde, ön kapıyı ve ilgili tüm kaynakları da silersiniz. 

Kaynak grubunu silmek için `Remove-AzResourceGroup` cmdlet 'ini çağırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunu oluşturdunuz:
* Front Door
* İki Web uygulaması

Ön kapıya özel bir etki alanı eklemeyi öğrenmek için ön kapı öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Özel etki alanı ekleme](front-door-custom-domain.md)
