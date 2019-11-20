---
title: Azure PowerShell kullanarak Azure Traffic Manager alt ağı geçersiz kılma | Microsoft Docs
description: Bu makale, Azure kullanarak uç nokta eşlemelerine önceden tanımlı IP aralığı ile trafiği bir uç noktaya yönlendirmek üzere bir Traffic Manager profilinin yönlendirme yöntemini geçersiz kılmak için Traffic Manager alt ağ geçersiz kılmanın nasıl kullanıldığını anlamanıza yardımcı olur. PowerShell.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 93362c0e1b359583e30886172f8ccb155c5ffaf4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348882"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Azure PowerShell kullanarak alt ağ geçersiz kılma Traffic Manager

Traffic Manager alt ağ geçersiz kılma, bir profilin yönlendirme yöntemini değiştirmenize izin verir.  Bir geçersiz kılma eklenmesi, son kullanıcının IP adresine göre trafiği uç nokta eşleme için önceden tanımlanmış bir IP aralığına göre yönlendirdirecektir. 

## <a name="how-subnet-override-works"></a>Alt ağ geçersiz kılma çalışması

Bir Traffic Manager profiline alt ağ geçersiz kılmaları eklendiğinde, Traffic Manager önce son kullanıcının IP adresi için bir alt ağ geçersiz kılma olup olmadığını kontrol eder. Bir tane bulunursa, kullanıcının DNS sorgusu ilgili uç noktaya yönlendirilir.  Bir eşleme bulunmazsa, Traffic Manager profilin özgün yönlendirme yöntemine geri dönecektir. 

IP adresi aralıkları CıDR aralıkları (örneğin, 1.2.3.0/24) veya adres aralıkları (örneğin, 1.2.3.4-5.6.7.8) olarak belirtilebilir. Her bitiş noktasıyla ilişkili IP aralıkları bu uç nokta için benzersiz olmalıdır. Farklı uç noktalar arasındaki IP aralıklarının çakışması, profilin Traffic Manager tarafından reddedilmesine neden olur.

Alt ağ geçersiz kılmalarını destekleyen iki tür yönlendirme profili vardır:

* **Coğrafi** -TRAFFIC Manager, DNS sorgusunun IP adresi için bir alt ağ geçersiz kılma işlemi bulursa, uç noktanın sistem durumunun ne olduğunu her ne olursa sorguyu uç noktaya yönlendirir.
* **Performans** -Traffic Manager DNS sorgusunun IP adresi için bir alt ağ geçersiz kılma bulduğunda, trafiği yalnızca sağlıklı olması durumunda uç noktaya yönlendirir.  Alt ağ geçersiz kılma uç noktasının sağlıklı olmaması durumunda Traffic Manager, performans yönlendirmesi buluşsal noktasına geri dönecektir.

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager alt ağ geçersiz kılma oluşturma

Bir Traffic Manager alt ağ geçersiz kılma oluşturmak için Azure PowerShell kullanarak Traffic Manager uç noktasına geçersiz kılma için alt ağları ekleyebilirsiniz.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Cloud Shell](https://shell.azure.com/powershell)izleyen komutları veya bilgisayarınızdan PowerShell 'i çalıştırarak çalıştırabilirsiniz. Azure Cloud Shell, ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. PowerShell 'i bilgisayarınızdan çalıştırırsanız, Azure PowerShell Module, 1.0.0 veya sonraki bir sürümü gerekir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` ' i çalıştırabilirsiniz. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız Azure 'da oturum açmak için `Login-AzAccount` ' ı çalıştırmanız gerekir.


1. **Traffic Manager uç noktasını alın:**

    Alt ağ geçersiz kılmayı etkinleştirmek için, geçersiz kılmayı eklemek istediğiniz uç noktayı [alın ve Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)kullanarak bir değişkende depolayın.

    Name, ProfileName ve ResourceGroupName değerlerini değiştirmekte olduğunuz uç noktanın değerleriyle değiştirin.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **IP adresi aralığını uç noktaya ekleyin:**
    
    IP adresi aralığını uç noktaya eklemek için [Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) kullanarak aralığı ekleyebilirsiniz.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Aralıklar eklendikten sonra, uç noktayı güncelleştirmek için [set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) kullanın.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
IP adresi aralığını kaldırma, [Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0)kullanılarak tamamlanabilir.

1.  **Traffic Manager uç noktasını alın:**

    Alt ağ geçersiz kılmayı etkinleştirmek için, geçersiz kılmayı eklemek istediğiniz uç noktayı [alın ve Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)kullanarak bir değişkende depolayın.

    Name, ProfileName ve ResourceGroupName değerlerini değiştirmekte olduğunuz uç noktanın değerleriyle değiştirin.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **IP adresi aralığını uç noktadan kaldırın:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     Aralıklar kaldırıldıktan sonra, uç noktayı güncelleştirmek için [set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) kullanın.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Sonraki adımlar
Traffic Manager [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md)hakkında daha fazla bilgi edinin.

[Alt ağ trafiği-yönlendirme yöntemi](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method) hakkında bilgi edinin
