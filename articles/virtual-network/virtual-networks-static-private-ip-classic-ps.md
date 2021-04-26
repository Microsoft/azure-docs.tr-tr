---
title: VM 'Ler için özel IP adresleri yapılandırma (klasik)-Azure PowerShell | Microsoft Docs
description: PowerShell kullanarak sanal makineler (klasik) için özel IP adreslerini yapılandırmayı öğrenin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e798f81102017b4d34c4b1b219a9f23035b0359
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222845"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>PowerShell kullanarak bir sanal makine için özel IP adreslerini yapılandırma (klasik)

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Bu makale, klasik dağıtım modelini kapsamaktadır. Ayrıca [, Kaynak Yöneticisi dağıtım modelinde statik bir özel IP adresi yönetebilirsiniz](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Aşağıdaki örnek PowerShell komutları önceden oluşturulmuş basit bir ortam bekliyor. Komutları bu belgede görüntülendikleri gibi çalıştırmak istiyorsanız, önce [VNET oluşturma](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-netcfg-ps)bölümünde açıklanan test ortamını oluşturun.

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Belirli bir IP adresinin kullanılabilir olup olmadığını doğrulama
*192.168.1.101* IP adresinin *testvnet* adlı bir VNET 'te kullanılabilir olup olmadığını doğrulamak için aşağıdaki PowerShell komutunu çalıştırın ve *IsAvailable* için değeri doğrulayın:

```azurepowershell
Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 
```

Beklenen çıktı:

```output
IsAvailable          : True
AvailableAddresses   : {}
OperationDescription : Test-AzureStaticVNetIP
OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
OperationStatus      : Succeeded
```

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>VM oluştururken statik özel IP adresi belirtme
Aşağıdaki PowerShell betiği, *TestService* adlı yeni bir bulut hizmeti oluşturur ve Azure 'dan bir görüntü alır, alınan görüntüyü kullanarak yeni bulut hizmetinde *DNS01* adlı BIR VM oluşturur, VM 'yi *ön uç* adlı bir alt ağda olacak şekilde ayarlar ve *192.168.1.7* 'i VM için statik bir özel IP adresi olarak ayarlar:

```azurepowershell
New-AzureService -ServiceName TestService -Location "Central US"
$image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
    Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
    Set-AzureSubnet –SubnetNames FrontEnd |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    New-AzureVM -ServiceName TestService –VNetName TestVNet
```

Beklenen çıktı:

```output
WARNING: No deployment found in service: 'TestService'.
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  
```

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Bir VM için statik özel IP adresi bilgilerini alma
Yukarıdaki betikle oluşturulan VM 'nin statik özel IP adresi bilgilerini görüntülemek için aşağıdaki PowerShell komutunu çalıştırın ve *IPAddress* değerini gözlemleyin:

```azurepowershell
Get-AzureVM -Name DNS01 -ServiceName TestService
```

Beklenen çıktı:

```output
DeploymentName              : TestService
Name                        : DNS01
Label                       : 
VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
InstanceStatus              : Provisioning
IpAddress                   : 192.168.1.7
InstanceStateDetails        : Windows is preparing your computer for first use...
PowerState                  : Started
InstanceErrorCode           : 
InstanceFaultDomain         : 0
InstanceName                : DNS01
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
```

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Bir VM 'den statik özel IP adresi kaldırma
Yukarıdaki betikte bulunan VM 'ye eklenen statik özel IP adresini kaldırmak için aşağıdaki PowerShell komutunu çalıştırın:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Remove-AzureStaticVNetIP |
    Update-AzureVM
```

Beklenen çıktı:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded
```

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Var olan bir VM 'ye statik bir özel IP adresi ekleme
Yukarıdaki betiği kullanarak oluşturulan VM 'ye statik bir özel IP adresi eklemek için aşağıdaki komutu çalıştırın:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    Update-AzureVM
```

Beklenen çıktı:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 
```

## <a name="set-ip-addresses-within-the-operating-system"></a>İşletim sistemi içinde IP adreslerini ayarla

Gerekli değilse, bir VM 'nin işletim sistemi içinde Azure sanal makinesine atanan özel IP 'yi statik olarak atamanız önerilir. İşletim sistemi içinde özel IP adresini el ile ayarlarsanız, Azure VM 'ye atanan özel IP adresi ile aynı adres olduğundan emin olun veya sanal makine bağlantısını kaybedebilirsiniz. Sanal makinenin işletim sistemi içindeki bir Azure sanal makinesine atanan genel IP adresini asla el ile atamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar
* [Ayrılmış genel IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) adresleri hakkında bilgi edinin.
* [Örnek düzeyi genel IP (ıLPıP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) adresleri hakkında bilgi edinin.
* [Ayrılmış IP REST API 'lerine](/previous-versions/azure/reference/dn722420(v=azure.100))danışın.