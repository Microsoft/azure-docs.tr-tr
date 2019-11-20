---
title: Birden çok NIC ile VM (klasik) oluşturma-Azure PowerShell | Microsoft Docs
description: PowerShell kullanarak birden çok NIC ile VM (klasik) oluşturmayı öğrenin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: af22bc43a06be74c7a4b6c869725a19fc87a0f3e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058737"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>PowerShell kullanarak birden çok NIC ile VM (klasik) oluşturma

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Azure 'da sanal makineler (VM) oluşturabilir ve VM 'lerinizden her birine birden çok ağ arabirimi (NIC) ekleyebilirsiniz. Birden çok NIC, NIC 'lerde trafik türlerinin ayrılmasını sağlar. Örneğin, bir NIC Internet ile iletişim kurabiliyorsa, diğeri yalnızca Internet 'e bağlı olmayan iç kaynaklarla iletişim kurar. Uygulama teslimi ve WAN iyileştirme çözümleri gibi birçok ağ sanal aygıtı için ağ trafiğini birden çok NIC arasında ayırma özelliği gereklidir.

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır:  [Kaynak Yöneticisi ve klasik](../resource-manager-deployment-model.md). Bu makale klasik dağıtım modelini incelemektedir. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir. [Kaynak Yöneticisi dağıtım modelini](../virtual-machines/windows/multiple-nics.md)kullanarak bu adımları nasıl gerçekleştireceğinizi öğrenin.

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Aşağıdaki adımlarda, WEB sunucuları için *ıaasöykü* adlı bir kaynak grubu ve db sunucuları Için *ıaasöykü arka ucu* adlı bir kaynak grubu kullanılır.

## <a name="prerequisites"></a>Önkoşullar

DB sunucularını oluşturmadan önce, bu senaryo için gerekli tüm kaynaklarla *ıaasöykü* kaynak grubunu oluşturmanız gerekir. Bu kaynakları oluşturmak için, aşağıdaki adımları tamamlayın. [Sanal ağ oluşturma](virtual-networks-create-vnet-classic-netcfg-ps.md) makalesindeki adımları izleyerek bir sanal ağ oluşturun.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Arka uç VM 'Leri oluşturma
Arka uç VM 'Leri aşağıdaki kaynakların oluşturulmasına bağlıdır:

* **Arka uç alt ağı**. Veritabanı sunucuları, trafiği ayırt etmek için ayrı bir alt ağın parçası olacaktır. Aşağıdaki komut dosyası, *Wtestvnet*adlı bir VNET 'te bu alt ağın mevcut olmasını bekler.
* **Veri diskleri Için depolama hesabı**. Daha iyi performans için, veritabanı sunucularındaki veri diskleri, Premium depolama hesabı gerektiren katı hal sürücüsü (SSD) teknolojisini kullanacaktır. Premium depolamayı desteklemek için dağıttığınız Azure konumunun bulunduğundan emin olun.
* **Kullanılabilirlik kümesi**. Tüm veritabanı sunucuları tek bir kullanılabilirlik kümesine eklenecek ve bu VM 'lerden en az birinin bakım sırasında çalıştığından emin olmanız gerekir.

### <a name="step-1---start-your-script"></a>1\. adım-betiğinizi başlatın
[Burada](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)kullanılan tam PowerShell betiğini indirebilirsiniz. Komut dosyasını ortamınızda çalışacak şekilde değiştirmek için aşağıdaki adımları izleyin.

1. [Ön](#prerequisites)koşullarda yukarıda dağıtılan mevcut kaynak grubunuza göre aşağıdaki değişkenlerin değerlerini değiştirin.

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Aşağıdaki değişkenlerin değerlerini, arka uç dağıtımınız için kullanmak istediğiniz değerlere göre değiştirin.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>2\. adım-sanal makinelerinize gereken kaynakları oluşturma
Tüm VM 'Ler için veri diskleri için yeni bir bulut hizmeti ve depolama hesabı oluşturmanız gerekir. VM 'Ler için bir görüntü ve yerel yönetici hesabı da belirtmeniz gerekir. Bu kaynakları oluşturmak için aşağıdaki adımları izleyin:

1. Yeni bir bulut hizmeti oluşturun.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Yeni bir Premium depolama hesabı oluşturun.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Yukarıda oluşturulan depolama hesabını aboneliğiniz için geçerli depolama hesabı olarak ayarlayın.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. VM için bir görüntü seçin.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Yerel yönetici hesabı kimlik bilgilerini ayarlayın.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>3\. adım-VM oluşturma
İstediğiniz sayıda VM oluşturmak için bir döngü kullanmanız ve döngü içinde gerekli NIC 'leri ve VM 'Leri oluşturmanız gerekir. NIC 'leri ve VM 'Leri oluşturmak için aşağıdaki adımları yürütün.

1. Değişkenin`$numberOfVMs` değerine `for` bağlı olarak, bir VM 'yi ve iki NIC 'yi gerektiği kadar çok kez oluşturmak için komutları yinelemek üzere bir döngü başlatın.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. VM için `VMConfig` görüntü, boyut ve kullanılabilirlik kümesini belirten bir nesne oluşturun.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. VM 'yi bir Windows sanal makinesi olarak sağlayın.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Varsayılan NIC 'yi ayarlayın ve statik bir IP adresi atayın.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Her VM için ikinci bir NIC ekleyin.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Her VM için veri disklerine oluşturun.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Her VM oluşturun ve döngüyü sonlandırın.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>4\. adım-betiği çalıştırma
Artık gereksinimlerinize göre betiği indirip değiştirdiğinize göre, birden çok NIC ile arka uç veritabanı VM 'Leri oluşturmak için betiği çalıştırabilirsiniz.

1. Komut dosyanızı kaydedin ve **PowerShell** komut Isteminden veya **PowerShell ISE**'den çalıştırın. İlk çıktıyı aşağıda gösterildiği gibi göreceksiniz.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Kimlik bilgileri isteminde gereken bilgileri doldurun ve **Tamam**' ı tıklatın. Aşağıdaki çıktı döndürülür.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>5\. adım-VM 'nin işletim sistemi içinde yönlendirmeyi yapılandırma

Azure DHCP, sanal makineye bağlı ilk (birincil) ağ arabirimine bir varsayılan ağ geçidi atar. Azure, bir sanal makineye bağlı ek (ikincil) ağ arabirimlerine varsayılan ağ geçidi atamaz. Bu nedenle varsayılan olarak, alt ağın dışında kalan ve ikincil bir ağ arabirimi içeren kaynaklarla iletişim kurulamaz. Ancak, ikincil ağ arabirimleri kendi alt ağlarının dışında kalan kaynaklarla iletişim kurabilir. İkincil ağ arabirimleri için yönlendirmeyi yapılandırmak üzere aşağıdaki makalelere bakın:

- [Birden çok NIC için Windows VM yapılandırma](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Birden çok NIC için Linux VM yapılandırma](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
