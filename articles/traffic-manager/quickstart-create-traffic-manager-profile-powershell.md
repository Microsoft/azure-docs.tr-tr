---
title: 'Hızlı başlangıç: uygulamalar için yüksek kullanılabilirlik için bir profil oluşturma-Azure PowerShell-Azure Traffic Manager'
description: Bu hızlı başlangıç makalesinde, yüksek oranda kullanılabilir bir Web uygulaması oluşturmak için bir Traffic Manager profili oluşturma açıklanmaktadır.
services: traffic-manager
author: duongau
ms.author: duau
manager: kumud
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: 96580a56abaffcc11180a406e00aaabb1cb1e2e7
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727889"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak yüksek oranda kullanılabilir bir Web uygulaması için Traffic Manager profili oluşturma

Bu hızlı başlangıçta, Web uygulamanız için yüksek kullanılabilirlik sunan bir Traffic Manager profilinin nasıl oluşturulacağı açıklanmaktadır.

Bu hızlı başlangıçta, bir Web uygulamasının iki örneğini oluşturacaksınız. Bunların her biri farklı bir Azure bölgesinde çalışmaktadır. [Uç nokta önceliğine](traffic-manager-routing-methods.md#priority-traffic-routing-method)göre bir Traffic Manager profili oluşturacaksınız. Profil, Kullanıcı trafiğini Web uygulamasını çalıştıran birincil siteye yönlendirir. Traffic Manager Web uygulamasını sürekli izler. Birincil site kullanılamıyorsa, yedekleme sitesine otomatik yük devretme sağlar.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Azure PowerShell kullanarak Traffic Manager dağıtım ortamının diyagramı." border="false":::

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa şimdi [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell'i yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure PowerShell modülü 5.4.1 veya sonraki bir sürümünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Connect-AzAccount` Azure ile bir bağlantı oluşturmak için öğesini de çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak Grubu oluşturma
[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)kullanarak bir kaynak grubu oluşturun.

```azurepowershell-interactive

# Variables
$Location1="EastUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma

Kullanıcı trafiğini uç nokta önceliğine göre yönlendiren [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) kullanarak bir Traffic Manager profili oluşturun.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Web Apps oluştur

Bu hızlı başlangıçta iki farklı Azure bölgesinde (*Batı ABD* ve *Doğu ABD*) dağıtılan bir Web uygulamasının iki örneğine ihtiyacınız olacaktır. Her biri, Traffic Manager için birincil ve yük devretme uç noktaları olarak görev yapar.

### <a name="create-web-app-service-plans"></a>Web App Service planları oluşturma
İki farklı Azure bölgesinde dağıtacağınız Web uygulamasının iki örneği için [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) kullanarak Web App Service planları oluşturun.

```azurepowershell-interactive

# Variables
$Location1="EastUS"
$Location2="WestEurope"

# Create an App service plan
New-AzAppservicePlan -Name "myAppServicePlanEastUS" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "myAppServicePlanEastUS" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>App Service planında bir Web uygulaması oluşturma
*Doğu ABD* ve Azure bölgelerindeki *Batı Avrupa* App Service planlarında [New-azwebapp](/powershell/module/az.websites/new-azwebapp) kullanarak Web uygulaması için iki örnek oluşturun.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name myWebAppEastUS -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "myAppServicePlanEastUS").Id
$App2ResourceId=(New-AzWebApp -Name myWebAppWestEurope -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "myAppServicePlanWestEurope").Id

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager uç noktalarını ekleme
Aşağıdaki şekilde Traffic Manager profile [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) kullanarak iki Web Apps uç nokta Traffic Manager olarak ekleyin:
- Tüm Kullanıcı trafiğini yönlendirmek için *Doğu ABD* Azure bölgesinde bulunan Web uygulamasını birincil uç nokta olarak ekleyin. 
- *Batı Avrupa* Azure bölgesinde bulunan Web uygulamasını yük devretme uç noktası olarak ekleyin. Birincil uç nokta kullanılamadığında, trafik otomatik olarak yük devretme uç noktasına yönlendirir.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "myPrimaryEndpoint" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "myFailoverEndpoint" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Traffic Manager profilini test etme

Bu bölümde, Traffic Manager profilinizin etki alanı adını kontrol edeceksiniz. Ayrıca birincil uç noktayı kullanılamaz olarak yapılandıracaksınız. Son olarak, Web uygulamasının hala kullanılabilir olduğunu görmeniz gerekir. Bunun nedeni, trafiği yük devretme uç noktasına gönderiyor Traffic Manager.

### <a name="determine-the-dns-name"></a>DNS adını belirleme

[Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)kullanarak TRAFFIC Manager profilin DNS adını belirleme.

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

**Relativednsname** değerini kopyalayın. Traffic Manager profilinizin DNS adı *http://<* relativednsname *>. trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Traffic Manager'ın nasıl çalıştığını görün
1. Web tarayıcısında, Web uygulamanızın varsayılan Web sitesini görüntülemek için Traffic Manager profilinizin DNS adını (*http://<* relativednsname *>. trafficmanager.net*) girin.

    > [!NOTE]
    > Bu hızlı başlangıç senaryosunda, tüm istekler birincil uç noktaya yönlendirir. **Öncelik 1** olarak ayarlanır.
2. Traffic Manager yük devretmeyi eylemde görüntülemek için [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint)kullanarak birincil sitenizi devre dışı bırakın.

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name "myPrimaryEndpoint" `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Web sitesini yeni bir Web tarayıcısı oturumunda görüntülemek için Traffic Manager profilinizin DNS adını (*http://<* relativednsname *>. trafficmanager.net*) kopyalayın.
4. Web uygulamasının hala kullanılabilir olduğunu doğrulayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

İşiniz bittiğinde, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)komutunu kullanarak kaynak gruplarını, Web uygulamalarını ve tüm ilgili kaynakları silin.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Web uygulamanız için yüksek kullanılabilirlik sağlayan bir Traffic Manager profili oluşturdunuz. Yönlendirme trafiği hakkında daha fazla bilgi edinmek için Traffic Manager öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Traffic Manager öğreticileri](tutorial-traffic-manager-improve-website-response.md)
