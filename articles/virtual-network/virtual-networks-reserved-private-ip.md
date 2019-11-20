---
title: Statik iç özel IP-Azure VM-klasik
description: Statik iç IP 'Leri (DIP 'Ler) ve bunların nasıl yönetileceğini anlama
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c37c49d8f7e09334014af290bf3a8c8e6d35f04b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058361"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>PowerShell kullanarak statik bir iç özel IP adresi ayarlama (klasik)
Çoğu durumda, sanal makineniz için statik bir iç IP adresi belirtmeniz gerekmez. Bir sanal ağdaki VM 'Ler, belirttiğiniz bir aralıktan otomatik olarak bir iç IP adresi alır. Ancak belirli durumlarda, belirli bir sanal makine için statik IP adresi belirtmek mantıklı olur. Örneğin, VM 'niz DNS çalıştıracaksanız veya bir etki alanı denetleyicisi olur. Statik bir iç IP adresi, bir durdurma/sağlamayı kaldırma durumunda bile VM ile kalır. 

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır:  [Kaynak Yöneticisi ve klasik](../azure-resource-manager/resource-manager-deployment-model.md). Bu makale klasik dağıtım modelini incelemektedir. Microsoft, en yeni dağıtımların [Kaynak Yöneticisi dağıtım modelini](virtual-networks-static-private-ip-arm-ps.md)kullanmasını önerir.
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>Azure PowerShell Service Management modülünü yükler

Aşağıdaki komutları çalıştırmadan önce, [Azure PowerShell hizmet yönetimi modülünün](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) makinede yüklü olduğundan emin olun. Azure PowerShell hizmet yönetimi modülünün sürüm geçmişi için [PowerShell Galerisi Azure modülü](https://www.powershellgallery.com/packages/Azure/5.3.0)' ne bakın.

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Belirli bir IP adresinin kullanılabilir olup olmadığını doğrulama
*KıSMıNA 10.0.0.7* IP adresinin *testvnet*adlı bir VNET 'te kullanılabilir olup olmadığını doğrulamak için aşağıdaki PowerShell komutunu çalıştırın ve *IsAvailable*için değeri doğrulayın.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Yukarıdaki komutu güvenli bir ortamda test etmek isterseniz, *testvnet* adlı bir VNET oluşturmak ve *10.0.0.0/8* adres alanını kullandığından emin olmak için [sanal ağ oluşturma (klasik)](virtual-networks-create-vnet-classic-pportal.md) konusundaki yönergeleri izleyin.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>VM oluştururken statik bir iç IP belirtme
Aşağıdaki PowerShell betiği *TestService*adlı yeni bir bulut hizmeti oluşturur, ardından Azure 'dan bir görüntü alır ve ardından alınan görüntüyü kullanarak yeni bulut hizmetinde *TESTVM* adlı BIR VM oluşturur, VM 'yi *alt ağ-1*adlı bir alt ağda olacak şekilde ayarlar, ve VM için *kısmına 10.0.0.7* bir STATIK iç IP olarak ayarlar:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>VM için statik iç IP bilgilerini alma
Yukarıdaki betikle oluşturulan VM 'nin statik iç IP bilgilerini görüntülemek için aşağıdaki PowerShell komutunu çalıştırın ve *IPAddress*değerlerini gözlemleyin:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Bir VM 'den statik bir iç IP 'yi kaldırma
Yukarıdaki betikte bulunan VM 'ye eklenen statik iç IP 'yi kaldırmak için aşağıdaki PowerShell komutunu çalıştırın:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Var olan bir VM 'ye statik bir iç IP ekleme
Yukarıdaki betiği kullanarak oluşturulan VM 'ye statik bir iç IP eklemek için aşağıdaki komutu çalıştırın:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Sonraki adımlar
[Ayrılmış IP](virtual-networks-reserved-public-ip.md)

[Örnek düzeyi genel IP (ıLPıP)](virtual-networks-instance-level-public-ip.md)

[REST API 'Leri Ayrılmış IP](https://msdn.microsoft.com/library/azure/dn722420.aspx)

