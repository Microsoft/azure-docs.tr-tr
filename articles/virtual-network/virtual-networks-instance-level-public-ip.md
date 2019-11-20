---
title: Azure örnek düzeyi genel IP (klasik) adresleri | Microsoft Docs
description: Örnek düzeyi genel IP (ıLPıP) adreslerini ve bunları PowerShell kullanarak yönetmeyi anlayın.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: d92832d1eee995e8883dc6c8ed0f58c9755e40f8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058419"
---
# <a name="instance-level-public-ip-classic-overview"></a>Örnek düzeyinde genel IP (klasik) genel bakış
Örnek düzeyi genel IP (ıLPıP), VM veya rol örneğinizin bulunduğu bulut hizmeti yerine, doğrudan bir VM 'ye veya Cloud Services rol örneğine atayabileceğiniz genel bir IP adresidir. ILPıP, bulut hizmetinize atanan sanal IP 'nin (VIP) yerini almaz. Bunun yerine, doğrudan VM 'niz veya rol örneğinizi bağlamak için kullanabileceğiniz ek bir IP adresidir.

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır:  [Kaynak Yöneticisi ve klasik](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Bu makale klasik dağıtım modelini incelemektedir. Microsoft, Kaynak Yöneticisi aracılığıyla VM oluşturmayı önerir. [IP adreslerinin](virtual-network-ip-addresses-overview-classic.md) Azure 'da nasıl çalıştığını anladığınızdan emin olun.

![ILZAR ve VIP arasındaki fark](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Şekil 1 ' de gösterildiği gibi, bulut hizmetine bir VIP kullanılarak erişilir, ancak tek tek VM 'ler normalde VIP:&lt;bağlantı noktası numarası&gt;kullanılarak erişilir. Belirli bir VM 'ye bir ıLPıP atayarak, bu VM 'ye doğrudan bu IP adresi kullanılarak erişilebilir.

Azure 'da bir bulut hizmeti oluşturduğunuzda, buna karşılık gelen DNS A kayıtları, gerçek VIP kullanmak yerine tam etki alanı adı (FQDN) üzerinden hizmete erişime izin vermek üzere otomatik olarak oluşturulur. Aynı işlem bir ıLPıP için gerçekleşir ve sanal makine ya da rol örneğine ıLPıP yerine FQDN ile erişim sağlar. Örneğin, *contosoadservice*adlı bir bulut hizmeti oluşturursanız ve iki örnekle *contosoweb* adlı bir Web rolü yapılandırırsanız ve. cscfg `domainNameLabel` ' de *webpublicıp*olarak ayarlanırsa, Azure aşağıdaki bir kayıtları larında


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* ...


> [!NOTE]
> Her VM veya rol örneği için yalnızca bir ILPıP atayabilirsiniz. Abonelik başına en fazla 5 Ilifs kullanabilirsiniz. Ipips, çok NIC VM 'Ler için desteklenmez.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Neden bir ıLPıN istemem gerekir?
VM 'niz veya rol örneğinizle doğrudan kendisine atanmış bir IP adresi ile bağlanmak istiyorsanız, bulut hizmeti VIP:&lt;bağlantı noktası numarası&gt;kullanmak yerine, VM 'niz veya rol örneğiniz için bir ılzar isteyin.

* **ETKIN FTP** -bir VM 'ye ılpıp atayarak, herhangi bir bağlantı noktasında trafik alabilir. VM 'nin trafik alabilmesi için uç noktalar gerekli değildir.  FTP protokolü hakkındaki ayrıntılar için bkz. [FTP protokolüne genel bakış](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) .
* **Gıden IP** -VM 'den kaynaklanan giden trafik, kaynak olarak ılzar ile EŞLENIR ve ıLPıP VM 'yi dış varlıklara benzersiz olarak tanımlar.

> [!NOTE]
> Geçmişte, bir ıLPıP adresi genel IP (PıP) adresi olarak adlandırılmıştır.
> 

## <a name="manage-an-ilpip-for-a-vm"></a>VM için bir ıLPıN yönetme
Aşağıdaki görevler VM 'lerden Ilifler oluşturmanıza, atamanıza ve kaldırmanıza olanak sağlar:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>PowerShell kullanarak VM oluşturma sırasında ıLPıP isteme
Aşağıdaki PowerShell betiği, *ftpservice*adlı bir bulut hizmeti oluşturur, Azure 'dan bir görüntü alır, alınan görüntüyü kullanarak *FTPıNSTANCE* adlı BIR VM oluşturur, VM 'yi bir ılpın kullanacak şekilde ayarlar ve VM 'yi yeni hizmete ekler:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

#Set "current" storage account for the subscription. It will be used as the location of new VM disk

Set-AzureSubscription -SubscriptionName <SubName> -CurrentStorageAccountName <StorageAccountName>

#Create a new VM configuration object

New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

```
Yeni VM diskinin konumu olarak başka bir depolama hesabı belirtmek istiyorsanız **MediaLocation** parametresini kullanabilirsiniz:

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Bir VM için ıLPıP bilgilerini alma
Önceki betikle oluşturulan VM 'nin ıLPıP bilgilerini görüntülemek için aşağıdaki PowerShell komutunu çalıştırın ve *Publicıpaddress* ve *publicıpname*değerlerini gözlemleyin:

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Beklenen çıktı:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Bir VM 'den ıLZAR kaldırma
Önceki betikteki VM 'ye eklenen ıLPıP 'yi kaldırmak için aşağıdaki PowerShell komutunu çalıştırın:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Var olan bir VM 'ye ıLZAR ekleme
Önceki betiği kullanılarak oluşturulan VM 'ye bir ıLPPT eklemek için aşağıdaki komutu çalıştırın:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Cloud Services rol örneği için ıLPıN yönetme

Bir Cloud Services rol örneğine ıLPıP eklemek için aşağıdaki adımları izleyin:

1. [Yapılandırma Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) makalesindeki adımları tamamlayarak bulut hizmeti için. cscfg dosyasını indirin.
2. `InstanceAddress` Öğesini ekleyerek. cscfg dosyasını güncelleştirin. Aşağıdaki örnek, *Mypublicıp* ADLı BIR ılpnokta *WebRole1*adlı bir rol örneğine ekler: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="WebPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. [Yapılandırma Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) makalesindeki adımları tamamlayarak bulut hizmeti için. cscfg dosyasını karşıya yükleyin.

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>Bulut hizmeti için ıLPıP bilgilerini alma
Rol örneği başına ıLPıP bilgilerini görüntülemek için aşağıdaki PowerShell komutunu çalıştırın ve *Publicıpaddress*, *publicipname*, *publicıpdomainnamelabel* ve *publicıpfqdn*değerlerini gözlemleyin:

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

Alt etki alanının A `nslookup` kaydını sorgulamak için de kullanabilirsiniz:

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>Sonraki adımlar
* [IP adreslemesinin](virtual-network-ip-addresses-overview-classic.md) klasik dağıtım modelinde nasıl çalıştığını anlayın.
* [Ayrılmış IP 'ler](virtual-networks-reserved-public-ip.md)hakkında bilgi edinin.
