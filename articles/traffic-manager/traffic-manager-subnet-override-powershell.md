---
title: Azure PowerShell kullanarak Azure Traffic Manager alt ağı geçersiz kılma | Microsoft Docs
description: Bu makale, bir Traffic Manager profilinin yönlendirme yöntemini geçersiz kılmak için Traffic Manager alt ağ geçersiz kılmayı nasıl kullanacağınızı anlamanıza yardımcı olur. Bu işlem, Azure PowerShell kullanarak uç nokta eşlemelerine ön tanımlı IP aralığı aracılığıyla Son Kullanıcı IP adresini temel alarak trafiği bir uç noktaya yönlendirir.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 7dd7f43044a9643eb7e9d5296dfb209e425d5fb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504792"
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

[Azure Cloud Shell](https://shell.azure.com/powershell)izleyen komutları veya bilgisayarınızdan PowerShell 'i çalıştırarak çalıştırabilirsiniz. Azure Cloud Shell, ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. PowerShell 'i bilgisayarınızdan çalıştırırsanız, Azure PowerShell Module, 1.0.0 veya sonraki bir sürümü gerekir. `Get-Module -ListAvailable Az`Yüklü sürümü bulmak için ' i çalıştırabilirsiniz. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Login-AzAccount` Azure 'da oturum açmak için çalıştırmanız da gerekir.


1. **Traffic Manager uç noktasını alın:**

    Alt ağ geçersiz kılmayı etkinleştirmek için, geçersiz kılmayı eklemek istediğiniz uç noktayı [alın ve Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint)kullanarak bir değişkende depolayın.

    Name, ProfileName ve ResourceGroupName değerlerini değiştirmekte olduğunuz uç noktanın değerleriyle değiştirin.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **IP adresi aralığını uç noktaya ekleyin:**
    
    IP adresi aralığını uç noktaya eklemek için [Add-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange) kullanarak aralığı ekleyebilirsiniz.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Aralıklar eklendikten sonra, uç noktayı güncelleştirmek için [set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint) kullanın.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
IP adresi aralığını kaldırma, [Remove-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange)kullanılarak tamamlanabilir.

1.  **Traffic Manager uç noktasını alın:**

    Alt ağ geçersiz kılmayı etkinleştirmek için, geçersiz kılmayı eklemek istediğiniz uç noktayı [alın ve Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint)kullanarak bir değişkende depolayın.

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
     Aralıklar kaldırıldıktan sonra, uç noktayı güncelleştirmek için [set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint) kullanın.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Sonraki adımlar
Traffic Manager [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md)hakkında daha fazla bilgi edinin.

[Alt ağ trafiği-yönlendirme yöntemi](./traffic-manager-routing-methods.md#subnet-traffic-routing-method) hakkında bilgi edinin