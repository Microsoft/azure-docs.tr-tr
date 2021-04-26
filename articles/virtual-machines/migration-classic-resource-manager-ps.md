---
title: PowerShell ile Kaynak Yöneticisi 'e geçiş
description: Bu makalede, Azure PowerShell komutlarını kullanarak, sanal makineler (VM 'Ler), sanal ağlar ve depolama hesapları gibi IaaS kaynaklarının, klasik 'dan Azure Resource Manager 'a yönelik platform tarafından desteklenen geçişi anlatılmaktadır
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c750e34e8081cf5a8b3d41cc8c52584a4353a336
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695167"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>IaaS kaynaklarını klasik bilgisayardan Azure Resource Manager PowerShell kullanarak geçirme

> [!IMPORTANT]
> Bugün, IaaS VM 'lerinin yaklaşık %90 ' u [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)kullanıyor. 28 Şubat 2020 itibariyle klasik VM 'Ler kullanımdan kaldırılmıştır ve 1 Mart 2023 tarihinde tamamen kullanımdan kaldırılacaktır. Bu kullanımdan kaldırma ve [nasıl etkilediği](classic-vm-deprecation.md#how-does-this-affect-me)hakkında [daha fazla bilgi edinin]( https://aka.ms/classicvmretirement) .

Bu adımlarda, klasik dağıtım modelinden bir hizmet olarak altyapı (IaaS) kaynaklarını Azure Resource Manager dağıtım modeline geçirmek için Azure PowerShell komutlarının nasıl kullanılacağı gösterilmektedir.

İsterseniz de [Azure CLI](migration-classic-resource-manager-cli.md)kullanarak kaynakları geçirebilirsiniz.

* Desteklenen geçiş senaryolarında arka plan için bkz. [Klasik 'dan Azure Resource Manager IaaS kaynaklarının platform tarafından desteklenen geçişi](migration-classic-resource-manager-overview.md).
* Ayrıntılı yönergeler ve bir geçiş kılavuzu için bkz. [Klasik platformda Azure Resource Manager Için Teknik kapsamlı](migration-classic-resource-manager-deep-dive.md)bakış.
* [En sık kullanılan geçiş hatalarını gözden geçirin](migration-classic-resource-manager-errors.md).

<br>
Bir geçiş işlemi sırasında hangi adımların yürütülmesi gerektiği sırayı belirlemek için bir akış çizelgesi aşağıda verilmiştir.

![Geçiş adımlarını gösteren ekran görüntüsü](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>1. Adım: geçiş planlaması
IaaS kaynaklarının klasik 'ten Kaynak Yöneticisi geçişe geçirilip geçirmeyeceğini değerlendirirken önerdiğimiz birkaç en iyi yöntem aşağıda verilmiştir:

* [Desteklenen ve desteklenmeyen özellikleri ve konfigürasyonları](migration-classic-resource-manager-overview.md)okuyun. Desteklenmeyen yapılandırmalar veya özellikler kullanan sanal makineleriniz varsa, yapılandırma veya özellik desteğinin duyurulduğu için bekleyin. Alternatif olarak, gereksinimlerinize uygun değilse, geçişi etkinleştirmek için bu özelliği kaldırın veya bu yapılandırmanın dışına geçin.
* Altyapınızı ve uygulamalarınızı hemen dağıtan otomatikleştirilmiş betikleriniz varsa, geçiş için bu betikleri kullanarak benzer bir test kurulumu oluşturmayı deneyin. Alternatif olarak, Azure portal kullanarak örnek ortamları da ayarlayabilirsiniz.

> [!IMPORTANT]
> Uygulama ağ geçitleri Şu anda klasik ' ten Kaynak Yöneticisi geçiş için desteklenmemektedir. Bir sanal ağı bir uygulama ağ geçidine geçirmek için, ağı taşımak üzere hazırlama işlemini çalıştırmadan önce ağ geçidini kaldırın. Geçişi tamamladıktan sonra, Azure Resource Manager ağ geçidini yeniden bağlayın.
>
> Başka bir abonelikteki ExpressRoute devrelerine bağlanan Azure ExpressRoute ağ geçitleri otomatik olarak geçirilemez. Bu gibi durumlarda, ExpressRoute ağ geçidini kaldırın, sanal ağı geçirin ve ağ geçidini yeniden oluşturun. Daha fazla bilgi için bkz. [ExpressRoute devreleri ve ilişkili sanal ağları klasik 'dan Kaynak Yöneticisi dağıtım modeline geçirme](../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>2. Adım: PowerShell 'in en son sürümünü yükler
Azure PowerShell yüklemek için iki ana seçenek vardır: [PowerShell Galerisi](https://www.powershellgallery.com/profiles/azure-sdk/) veya [Web Platformu Yükleyicisi (WebPI)](https://aka.ms/webpi-azps). WebPI aylık güncelleştirmeleri alır. PowerShell Galerisi güncelleştirmeleri sürekli olarak alır. Bu makale, Azure PowerShell sürüm 2.1.0 ' i temel alır.

Yükleme yönergeleri için bkz. [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>3. Adım: abonelik için yönetici olduğunuzdan emin olun
Bu geçişi gerçekleştirmek için, [Azure Portal](https://portal.azure.com)abonelik için bir ortak yönetici olarak eklenmeli.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Hub** menüsünde, **abonelik**' ı seçin. Bunu görmüyorsanız, **tüm hizmetler**' i seçin.
3. Uygun abonelik girişini bulun ve ardından **rol alanım** ' a bakın. Coadministrator için, değer _Hesap Yöneticisi_ olmalıdır.

Ortak yönetici ekleyemediğinizde, abonelik için bir hizmet yöneticisiyle veya ortak yönetici ile iletişim kurun.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>4. Adım: aboneliğinizi ayarlama ve geçiş için kaydolma
İlk olarak bir PowerShell istemi başlatın. Geçiş için ortamınızı hem klasik hem de Kaynak Yöneticisi için ayarlayın.

Kaynak Yöneticisi modeli için hesabınızda oturum açın.

```powershell
    Connect-AzAccount
```

Aşağıdaki komutu kullanarak kullanılabilir abonelikleri alın:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Geçerli oturum için Azure aboneliğinizi ayarlayın. Bu örnek, varsayılan abonelik adını **Azure aboneliğim** olarak ayarlar. Örnek abonelik adını kendi adınızla değiştirin.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Kayıt tek seferlik bir adımdır, ancak geçişe kalkışmadan önce bunu bir kez yapmanız gerekir. Kaydolmadan aşağıdaki hata iletisini görürsünüz:
>
> *Rozet Isteği: abonelik geçiş için kaydedilmemiş.*

Aşağıdaki komutu kullanarak geçiş kaynak sağlayıcısına kaydolun:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Kaydın tamamlanabilmesi için beş dakika bekleyin. Aşağıdaki komutu kullanarak onay durumunu kontrol edin:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Devam etmeden önce RegistrationState 'in olduğundan emin olun `Registered` .

Klasik dağıtım modeline geçmeden önce, geçerli dağıtımınızın veya sanal ağınızın Azure bölgesinde yeterli sayıda Azure Resource Manager sanal makineye sahip olduğunuzdan emin olun. Azure Resource Manager ' de mevcut vCPU sayısını denetlemek için aşağıdaki PowerShell komutunu kullanabilirsiniz. VCPU kotaları hakkında daha fazla bilgi için bkz. [sınırlara ve Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Bu örnek **Batı ABD** bölgesindeki kullanılabilirliği denetler. Örnek bölge adını kendi adınızla değiştirin.

```powershell
    Get-AzVMUsage -Location "West US"
```

Şimdi, klasik dağıtım modeli için hesabınızda oturum açın.

```powershell
    Add-AzureAccount
```

Aşağıdaki komutu kullanarak kullanılabilir abonelikleri alın:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Geçerli oturum için Azure aboneliğinizi ayarlayın. Bu örnekte, varsayılan abonelik **Azure aboneliğime** ayarlanır. Örnek abonelik adını kendi adınızla değiştirin.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>5. Adım: IaaS kaynaklarınızı geçirmek için komutları çalıştırın
* [VM 'Leri bir bulut hizmetinde geçirme (Sanal ağda değil)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Sanal ağdaki VM 'Leri geçirme](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Depolama hesabını geçirme](#step-52-migrate-a-storage-account)

> [!NOTE]
> Burada açıklanan tüm işlemler ıdempotent. Desteklenmeyen bir özellik veya yapılandırma hatası dışında bir sorununuz varsa, hazırlama, durdurma veya işleme işlemini yeniden denemeniz önerilir. Platform daha sonra eylemi yeniden dener.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Adım 5,1: seçenek 1-sanal makineleri bir bulut hizmetinde geçirme (Sanal ağda değil)
Aşağıdaki komutu kullanarak bulut hizmetleri listesini alın. Ardından, geçirmek istediğiniz bulut hizmetini seçin. Bulut hizmetindeki VM 'Ler bir sanal ağda yer alıyorsa veya Web veya çalışan rolleri varsa, komut bir hata mesajı döndürür.

```powershell
    Get-AzureService | ft Servicename
```

Bulut hizmeti için dağıtım adını alın. Bu örnekte hizmet adı **hizmetim**. Örnek hizmet adını kendi hizmet adınızla değiştirin.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Bulut hizmetindeki sanal makineleri geçiş için hazırlayın. Aralarından seçim yapabileceğiniz iki seçeneğiniz vardır.

* **Seçenek 1: VM 'Leri platform tarafından oluşturulan bir sanal ağa geçirin.**

    İlk olarak, aşağıdaki komutları kullanarak bulut hizmetini geçirebileceğiniz doğrulayın:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Aşağıdaki komut, geçişi engelleyen tüm uyarıları ve hataları görüntüler. Doğrulama iletileri hata türü ileti içermiyorsa, hazırlama adımına geçebilirsiniz.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **2. seçenek: Kaynak Yöneticisi dağıtım modelinde var olan bir sanal ağa geçiş yapın.**

    Bu örnekte kaynak grubu adı **Myresourcegroup**, sanal ağ adı **myVirtualNetwork** ve alt ağ adı **mysubnet** olarak ayarlanır. Örnekteki adları kendi kaynaklarınızın adlarıyla değiştirin.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    İlk olarak, aşağıdaki komutu kullanarak sanal ağı geçirebileceğiniz doğrulayın:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Aşağıdaki komut, geçişi engelleyen tüm uyarıları ve hataları görüntüler. Doğrulama iletileri hata içermiyorsa, aşağıdaki hazırlama adımıyla devam edebilirsiniz:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Hazırlama işlemi önceki seçeneklerden biriyle başarılı olduktan sonra, VM 'lerin geçiş durumunu sorgulayın. Durumunda olduklarından emin olun `Prepared` .

Bu örnekte VM adı **myvm** olarak ayarlanır. Örnek adı kendi VM adınızla değiştirin.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

PowerShell veya Azure portal kullanarak hazırlanan kaynaklar için yapılandırmayı denetleyin. Geçişe hazırsanız ve eski durumuna geri dönmek istiyorsanız aşağıdaki komutu kullanın:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Hazırlanan yapılandırma iyi görünüyorsa, aşağıdaki komutu kullanarak kaynakları ileriye doğru taşıyabilir ve kaydedebilirsiniz:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Adım 5,1: seçenek 2-sanal ağdaki sanal makineleri geçirme

Sanal bir ağdaki sanal makineleri geçirmek için, sanal ağı geçirolursunuz. Sanal makineler sanal ağla otomatik olarak geçirilir. Geçirmek istediğiniz sanal ağı seçin.
> [!NOTE]
> Klasik dağıtım modeli kullanılarak oluşturulan [tek bir sanal makineyi](./windows/create-vm-specialized-portal.md) , sanal makinenin VHD (OS ve veri) dosyalarını kullanarak yönetilen disklerle yeni bir kaynak yöneticisi sanal makine oluşturarak geçirin.
<br>

> [!NOTE]
> Sanal ağ adı, yeni portalda gösterilenden farklı olabilir. Yeni Azure portal adı olarak görüntüler `[vnet-name]` , ancak gerçek sanal ağ adı türündedir `Group [resource-group-name] [vnet-name]` . Geçişe başlamadan önce, komutu kullanarak gerçek sanal ağ adını bulun `Get-AzureVnetSite | Select -Property Name` veya eski Azure Portal görüntüleyin. 

Bu örnek, sanal ağ adını **Myvnet** olarak ayarlar. Örnek sanal ağ adını kendi adınızla değiştirin.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Sanal ağ web veya çalışan rolleri ya da desteklenmeyen yapılandırmalara sahip VM 'Ler içeriyorsa, bir doğrulama hata iletisi alırsınız.

İlk olarak, aşağıdaki komutu kullanarak sanal ağı geçirebileceğiniz doğrulayın:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Aşağıdaki komut, geçişi engelleyen tüm uyarıları ve hataları görüntüler. Doğrulama başarılı olursa, aşağıdaki hazırlama adımıyla devam edebilirsiniz:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Azure PowerShell veya Azure portal kullanarak hazırlanan sanal makinelerin yapılandırmasını denetleyin. Geçişe hazırsanız ve eski durumuna geri dönmek istiyorsanız aşağıdaki komutu kullanın:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Hazırlanan yapılandırma iyi görünüyorsa, aşağıdaki komutu kullanarak kaynakları ileriye doğru taşıyabilir ve kaydedebilirsiniz:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Adım 5,2: depolama hesabını geçirme
Sanal makineleri geçirmeyi tamamladıktan sonra, depolama hesaplarını geçirmeden önce aşağıdaki önkoşul denetimlerini gerçekleştirin.

> [!NOTE]
> Depolama hesabınızda ilişkili disk veya VM verisi yoksa, doğrudan "depolama hesaplarını doğrula ve geçişe başla" bölümüne atlayabilirsiniz. Ayrıca, klasik diskleri silmenin, VM görüntülerinin veya işletim sistemi görüntülerinin depolama hesabındaki kaynak VHD dosyalarını kaldırmadığını unutmayın. Ancak, bu VHD dosyalarındaki kiralamayı keser, böylece geçişten sonra ARM diskleri veya görüntüleri oluşturmak için yeniden kullanılabilir.

* Tüm VM 'Leri geçirdiyseniz veya depolama hesabınızda disk kaynakları varsa önkoşul denetimleri:
    * Diskleri depolama hesabında depolanan sanal makineleri geçirin.

        Aşağıdaki komut, depolama hesabındaki tüm VM disklerinin RoleName ve DiskName özelliklerini döndürür. RoleName, bir diskin eklendiği sanal makinenin adıdır. Bu komut diskler döndürürse, depolama hesabını geçirmeden önce bu disklerin eklendiği sanal makinelerin geçirildiğinden emin olun.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Depolama hesabında depolanan eklenmemiş VM disklerini silin.

        Aşağıdaki komutu kullanarak, depolama hesabında eklenmemiş VM disklerini bulun:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Önceki komut diskleri döndürürse, aşağıdaki komutu kullanarak bu diskleri silin:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Depolama hesabında depolanan VM görüntülerini silin.

        Aşağıdaki komut, depolama hesabında depolanan işletim sistemi disklerinin bulunduğu tüm VM görüntülerini döndürür.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Aşağıdaki komut, depolama hesabında depolanan veri disklerine sahip tüm VM görüntülerini döndürür.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Önceki komutlar tarafından döndürülen tüm VM görüntülerini şu komutu kullanarak silin:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Depolama hesaplarını doğrulayın ve geçişi başlatın.

    Aşağıdaki komutu kullanarak her bir depolama hesabını geçiş için doğrulayın. Bu örnekte, depolama hesabı adı **Mystorageaccount**' dır. Örnek adı kendi depolama hesabınızın adıyla değiştirin.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Sonraki adım, depolama hesabını geçiş için hazırlamaktır.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Azure PowerShell ya da Azure portal kullanarak hazırlanan depolama hesabının yapılandırmasını denetleyin. Geçişe hazırsanız ve eski durumuna geri dönmek istiyorsanız aşağıdaki komutu kullanın:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Hazırlanan yapılandırma iyi görünüyorsa, aşağıdaki komutu kullanarak kaynakları ileriye doğru taşıyabilir ve kaydedebilirsiniz:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Sonraki adımlar
* [IaaS kaynaklarının klasik ile Azure Resource Manager geçişine genel bakış](migration-classic-resource-manager-overview.md)
* [Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış](migration-classic-resource-manager-deep-dive.md)
* [IaaS kaynaklarının Klasik’ten Azure Resource Manager’a geçişini planlama](migration-classic-resource-manager-plan.md)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için CLı kullanma](migration-classic-resource-manager-cli.md)
* [IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişine yardımcı olacak topluluk araçları](migration-classic-resource-manager-community-tools.md)
* [En sık karşılaşılan geçiş hatalarını gözden geçirme](migration-classic-resource-manager-errors.md)
* [IaaS kaynaklarını klasik konumundan Azure Resource Manager geçirme hakkında en sık sorulan soruları gözden geçirin](migration-classic-resource-manager-faq.md)
