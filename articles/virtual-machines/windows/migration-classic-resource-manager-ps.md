---
title: PowerShell ile Kaynak Yöneticisi 'e geçiş
description: Bu makalede, Azure PowerShell komutlarını kullanarak, sanal makineler (VM 'Ler), sanal ağlar (VNet) ve Azure Resource Manager Klasik depolama hesapları gibi IaaS kaynaklarının platform tarafından desteklenen geçişi (ARM) gösterilmektedir
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 99650b090a84fd417fca5bbeb1ce3f58d32729bb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033205"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>IaaS kaynaklarını klasik 'dan Azure Resource Manager Azure PowerShell kullanarak geçirme
Bu adımlarda, klasik dağıtım modelinden bir hizmet olarak altyapı (IaaS) kaynaklarını Azure Resource Manager dağıtım modeline geçirmek için Azure PowerShell komutlarının nasıl kullanılacağı gösterilmektedir.

İsterseniz, [Azure komut satırı arabirimi (Azure CLI)](../linux/migration-classic-resource-manager-cli.md)kullanarak da kaynakları geçirebilirsiniz.

* Desteklenen geçiş senaryolarında arka plan için bkz. [Klasik 'dan Azure Resource Manager IaaS kaynaklarının platform tarafından desteklenen geçişi](migration-classic-resource-manager-overview.md).
* Ayrıntılı yönergeler ve bir geçiş kılavuzu için bkz. [Klasik platformda Azure Resource Manager Için Teknik kapsamlı](migration-classic-resource-manager-deep-dive.md)bakış.
* [En sık karşılaşılan geçiş hatalarını gözden geçirme](migration-classic-resource-manager-errors.md)

<br>
Bir geçiş işlemi sırasında hangi adımların yürütülmesi gerektiği sırayı belirlemek için bir akış çizelgesi aşağıda verilmiştir

![Geçiş adımlarını gösteren ekran görüntüsü](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>1\. Adım: geçiş planlaması
IaaS kaynaklarını klasik ' ten Kaynak Yöneticisi geçirmeyi değerlendirirken önerdiğimiz birkaç en iyi yöntem aşağıda verilmiştir:

* [Desteklenen ve desteklenmeyen özellikleri ve konfigürasyonları](migration-classic-resource-manager-overview.md)okuyun. Desteklenmeyen yapılandırmalar veya özellikler kullanan sanal makineleriniz varsa, yapılandırma/Özellik desteğinin duyurulmalarını beklemeniz önerilir. Alternatif olarak, gereksinimlerinize uygun değilse, geçişi etkinleştirmek için bu özelliği kaldırın veya bu yapılandırmanın dışına geçin.
* Altyapınızı ve uygulamalarınızı hemen dağıtan otomatikleştirilmiş betikleriniz varsa, geçiş için bu betikleri kullanarak benzer bir test kurulumu oluşturmayı deneyin. Alternatif olarak, Azure portal kullanarak örnek ortamları da ayarlayabilirsiniz.

> [!IMPORTANT]
> Application Gateway 'ler, klasik ' ten Kaynak Yöneticisi geçiş için şu anda desteklenmiyor. Bir Application Gateway ile klasik bir sanal ağı geçirmek için, ağı taşımak üzere hazırlama işlemini çalıştırmadan önce ağ geçidini kaldırın. Geçişi tamamladıktan sonra, Azure Resource Manager ağ geçidini yeniden bağlayın.
>
>Başka bir abonelikte ExpressRoute devrelerine bağlanan ExpressRoute ağ geçitleri otomatik olarak geçirilemez. Bu gibi durumlarda, ExpressRoute ağ geçidini kaldırın, sanal ağı geçirin ve ağ geçidini yeniden oluşturun. Daha fazla bilgi için lütfen bkz. [ExpressRoute devreleri ve ilişkili sanal ağları klasik Kaynak Yöneticisi dağıtım modeline geçirme](../../expressroute/expressroute-migration-classic-resource-manager.md) .

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>2\. Adım: Azure PowerShell en son sürümünü yükler
Azure PowerShell yüklemek için iki ana seçenek vardır: [PowerShell Galerisi](https://www.powershellgallery.com/profiles/azure-sdk/) veya [Web Platformu Yükleyicisi (WebPI)](https://aka.ms/webpi-azps). WebPI aylık güncelleştirmeleri alır. PowerShell Galerisi güncelleştirmeleri sürekli olarak alır. Bu makale, Azure PowerShell sürüm 2.1.0 ' i temel alır.

Yükleme yönergeleri için bkz. [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>3\. Adım: abonelik için yönetici olduğunuzdan emin olun Azure portal
Bu geçişi gerçekleştirmek için, [Azure Portal](https://portal.azure.com)abonelik için ortak yönetici olarak eklenmeleri gerekir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Hub menüsünde, **abonelik**' ı seçin. Bunu görmüyorsanız, **tüm hizmetler**' i seçin.
3. Uygun abonelik girişini bulun ve **rol alanım** ' a bakın. Ortak yönetici için, değer _Hesap Yöneticisi_olmalıdır.

Ortak yönetici ekleyemediğinizde, abonelik için bir hizmet yöneticisiyle veya ortak yönetici ile iletişime geçerek daha sonra eklenirsiniz.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>4\. Adım: aboneliğinizi ayarlama ve geçiş için kaydolma
İlk olarak bir PowerShell istemi başlatın. Geçiş için ortamınızı hem klasik hem de Kaynak Yöneticisi için ayarlamanız gerekir.

Kaynak Yöneticisi modeli için hesabınızda oturum açın.

```powershell
    Connect-AzAccount
```

Aşağıdaki komutu kullanarak kullanılabilir abonelikleri alın:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Geçerli oturum için Azure aboneliğinizi ayarlayın. Bu örnek, varsayılan abonelik adını **Azure aboneliğim**olarak ayarlar. Örnek abonelik adını kendi adınızla değiştirin.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Kayıt tek seferlik bir adımdır, ancak geçişi denemeden önce bunu bir kez yapmanız gerekir. Kaydolmadan aşağıdaki hata iletisini görürsünüz:
>
> *Rozet Isteği: abonelik geçiş için kaydedilmemiş.*

Aşağıdaki komutu kullanarak geçiş kaynak sağlayıcısına kaydolun:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Lütfen kaydın tamamlanmasını beş dakika bekleyin. Aşağıdaki komutu kullanarak onay durumunu kontrol edebilirsiniz:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Devam etmeden önce RegistrationState `Registered` olduğundan emin olun.

Şimdi klasik model için hesabınızda oturum açın.

```powershell
    Add-AzureAccount
```

Aşağıdaki komutu kullanarak kullanılabilir abonelikleri alın:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Geçerli oturum için Azure aboneliğinizi ayarlayın. Bu örnekte, varsayılan abonelik **Azure aboneliğime**ayarlanır. Örnek abonelik adını kendi adınızla değiştirin.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>5\. Adım: geçerli dağıtımınızın veya VNET 'nizin Azure bölgesinde yeterli sayıda Azure Resource Manager sanal makineye sahip olduğunuzdan emin olun
Azure Resource Manager ' de mevcut vCPU sayısını denetlemek için aşağıdaki PowerShell komutunu kullanabilirsiniz. VCPU kotaları hakkında daha fazla bilgi için bkz. [sınırlara ve Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager).

Bu örnek **Batı ABD** bölgesindeki kullanılabilirliği denetler. Örnek bölge adını kendi adınızla değiştirin.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>6\. Adım: IaaS kaynaklarınızı geçirmek için komutları çalıştırın
* [VM 'Leri bir bulut hizmetinde geçirme (Sanal ağda değil)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Sanal ağdaki VM 'Leri geçirme](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Depolama hesabını geçirme](#step-62-migrate-a-storage-account)

> [!NOTE]
> Burada açıklanan tüm işlemler ıdempotent. Desteklenmeyen bir özellik veya yapılandırma hatası dışında bir sorununuz varsa, hazırlama, durdurma veya işleme işlemini yeniden denemeniz önerilir. Platform daha sonra eylemi yeniden dener.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Adım 6,1: seçenek 1-sanal makineleri bir bulut hizmetinde geçirme (Sanal ağda değil)
Aşağıdaki komutu kullanarak bulut hizmetleri listesini alın ve ardından geçirmek istediğiniz bulut hizmetini seçin. Bulut hizmetindeki VM 'Ler bir sanal ağda yer alıyorsa veya Web veya çalışan rolleri varsa, komut bir hata mesajı döndürür.

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

* **1. seçenek. VM 'Leri platform tarafından oluşturulan bir sanal ağa geçirme**

    İlk olarak, aşağıdaki komutları kullanarak bulut hizmetini geçirebilmeniz gerektiğini doğrulayın:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Aşağıdaki komut, geçişi engelleyen tüm uyarıları ve hataları görüntüler. Doğrulama başarılı olursa, **hazırla** adımına geçebilirsiniz:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Seçenek 2. Kaynak Yöneticisi dağıtım modelinde var olan bir sanal ağa geçiş**

    Bu örnek, kaynak grubu adını **Myresourcegroup**olarak, sanal ağ adını **myVirtualNetwork** ve alt ağ adını **mysubnet**olarak ayarlar. Örnekteki adları kendi kaynaklarınızın adlarıyla değiştirin.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    İlk olarak, aşağıdaki komutu kullanarak sanal ağı geçirebiliyorsanız doğrulayın:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Aşağıdaki komut, geçişi engelleyen tüm uyarıları ve hataları görüntüler. Doğrulama başarılı olursa, aşağıdaki hazırlama adımıyla devam edebilirsiniz:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Hazırlama işlemi önceki seçeneklerden biriyle başarılı olduktan sonra, VM 'lerin geçiş durumunu sorgulayın. `Prepared` durumunda olduklarından emin olun.

Bu örnekte VM adı **myvm**olarak ayarlanır. Örnek adı kendi VM adınızla değiştirin.

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

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Adım 6,1: seçenek 2-sanal ağdaki sanal makineleri geçirme

Sanal bir ağdaki sanal makineleri geçirmek için, sanal ağı geçirolursunuz. Sanal makineler sanal ağla otomatik olarak geçirilir. Geçirmek istediğiniz sanal ağı seçin.
> [!NOTE]
> Sanal makinenin VHD (OS ve veri) dosyalarını kullanarak yönetilen disklere sahip yeni bir Kaynak Yöneticisi sanal makine oluşturarak [tek bir klasik sanal makineyi geçirin](migrate-single-classic-to-resource-manager.md) .
<br>

> [!NOTE]
> Sanal ağ adı, yeni portalda gösterilenden farklı olabilir. Yeni Azure portalı adı `[vnet-name]` olarak görüntüler, ancak gerçek sanal ağ adı `Group [resource-group-name] [vnet-name]`türündedir. Geçirmeden önce, komutunu kullanarak gerçek sanal ağ adını ara `Get-AzureVnetSite | Select -Property Name` veya eski Azure portalında görüntüleyin. 

Bu örnek, sanal ağ adını **Myvnet**olarak ayarlar. Örnek sanal ağ adını kendi adınızla değiştirin.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Sanal ağ web veya çalışan rolleri ya da desteklenmeyen yapılandırmalara sahip VM 'Ler içeriyorsa, bir doğrulama hata iletisi alırsınız.

İlk olarak, aşağıdaki komutu kullanarak sanal ağı geçirebiliyorsanız doğrulayın:

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

### <a name="step-62-migrate-a-storage-account"></a>Adım 6,2 depolama hesabını geçirme
Sanal makineleri geçirmeyi tamamladıktan sonra, depolama hesaplarını geçirmeden önce aşağıdaki önkoşul denetimlerini gerçekleştirmenizi öneririz.

> [!NOTE]
> Depolama hesabınızda ilişkili disk veya VM verisi yoksa, **Depolama hesabını doğrula ve geçişi Başlat** bölümüne doğrudan atlayabilirsiniz.

* **Tüm VM 'Leri geçirdiyseniz veya depolama hesabınızda disk kaynakları varsa önkoşul denetimleri**
    * **Diskleri depolama hesabında depolanan klasik sanal makineleri geçirin**

        Aşağıdaki komut, depolama hesabındaki tüm klasik VM disklerinin RoleName ve DiskName özelliklerini döndürür. RoleName, bir diskin eklendiği sanal makinenin adıdır. Bu komut diskler döndürürse, bu disklerin eklendiği sanal makinelerin depolama hesabı geçirilmeden önce geçirildiğinden emin olun.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **Depolama hesabında depolanan ekli olmayan klasik VM disklerini silme**

        Aşağıdaki komutu kullanarak, depolama hesabında eklenmemiş klasik VM disklerini bulun:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Yukarıdaki komut diskleri döndürürse, aşağıdaki komutu kullanarak bu diskleri silin:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **Depolama hesabında depolanan VM görüntülerini silme**

        Aşağıdaki komut, depolama hesabında depolanan işletim sistemi diskine sahip tüm VM görüntülerini döndürür.
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
        Yukarıdaki komutlar tarafından döndürülen tüm VM görüntülerini bu komutu kullanarak silin:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **Depolama hesabını doğrula ve geçişi Başlat**

    Aşağıdaki komutu kullanarak her bir depolama hesabını geçiş için doğrulayın. Bu örnekte, depolama hesabı adı **Mystorageaccount**' dır. Örnek adı kendi depolama hesabınızın adıyla değiştirin.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Sonraki adım, depolama hesabını geçiş için hazırlamaktır

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
* [IaaS kaynaklarının klasik ile Azure Resource Manager geçişine genel bakış](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarının Klasik’ten Azure Resource Manager’a geçişini planlama](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için CLı kullanma](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişine yardımcı olacak topluluk araçları](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En sık karşılaşılan geçiş hatalarını gözden geçirme](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarını klasik konumundan Azure Resource Manager geçirme hakkında en sık sorulan soruları gözden geçirin](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
