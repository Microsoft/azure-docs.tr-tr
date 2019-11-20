---
title: Bulut hizmetini özel bir etki alanı denetleyicisine bağlama | Microsoft Docs
description: PowerShell ve AD etki alanı uzantısını kullanarak Web/çalışan rollerinizi özel bir AD etki alanına bağlamayı öğrenin
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 97a24720e65539a68745a5a1bb3f13ce1cafb9be
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359176"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Azure Cloud Services rollerini Azure 'da barındırılan özel bir AD etki alanı denetleyicisine bağlama
Önce Azure 'da bir sanal ağ (VNet) ayarlayacağız. Bundan sonra sanal ağa bir Active Directory Etki Alanı Denetleyicisi (Azure sanal makinesinde barındırılan) ekleyeceğiz. Daha sonra, önceden oluşturulmuş VNet 'e mevcut bulut hizmeti rollerini ekleyecek ve ardından bunları etki alanı denetleyicisine bağlayacağız.

Başlamadan önce göz önünde bulundurmanız gereken birkaç şey vardır:

1. Bu öğretici PowerShell kullanır, bu nedenle Azure PowerShell yüklü olduğundan ve başlamaya hazırlandığınızdan emin olun. Azure PowerShell ayarlama hakkında yardım almak için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/overview).
2. AD etki alanı denetleyiciniz ve web/çalışan rolü örneklerinizin VNet 'te olması gerekir.

Bu adım adım kılavuzu izleyin ve herhangi bir sorunla karşılaşırsanız makalenin sonunda bize bir yorum bırakın. Birisi sizi geri alacak (Evet, yorum okuyacağız).

Bulut hizmeti tarafından başvurulan ağ, **Klasik bir sanal ağ**olmalıdır.

## <a name="create-a-virtual-network"></a>Sanal Ağ Oluştur
Azure portal veya PowerShell kullanarak Azure Ağa gelen bir sanal oluşturabilirsiniz. Bu öğretici için PowerShell kullanılır. Azure portal kullanarak bir sanal ağ oluşturmak için, bkz. [sanal ağ oluşturma](../virtual-network/quick-create-portal.md). Makale bir sanal ağ (Kaynak Yöneticisi) oluşturmayı ele alır, ancak bulut hizmetleri için bir sanal ağ (klasik) oluşturmanız gerekir. Bunu yapmak için, portalda **kaynak oluştur**' u seçin, **arama** kutusuna *sanal ağ* yazın ve ardından **ENTER**tuşuna basın. Arama sonuçlarında, **her şey**altında **sanal ağ**' ı seçin. **Dağıtım modeli seçin**altında **Klasik**' i seçin ve ardından **Oluştur**' u seçin. Daha sonra makalesindeki adımları izleyebilirsiniz.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Bir Sanal Makine Oluşturun
Sanal ağı ayarlamayı tamamladıktan sonra, bir AD etki alanı denetleyicisi oluşturmanız gerekecektir. Bu öğreticide, Azure sanal makinesinde bir AD etki alanı denetleyicisi ayarlayacağız.

Bunu yapmak için aşağıdaki komutları kullanarak PowerShell aracılığıyla bir sanal makine oluşturun:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Sanal makinenizi bir etki alanı denetleyicisine yükseltme
Sanal makineyi bir AD etki alanı denetleyicisi olarak yapılandırmak için, VM 'de oturum açmanız ve yapılandırmanız gerekecektir.

VM 'de oturum açmak için, RDP dosyasını PowerShell aracılığıyla alabilir, aşağıdaki komutları kullanın:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

VM 'de oturum açtıktan sonra, [MÜŞTERI ad etki alanı denetleyicinizi ayarlama](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)hakkında adım adım Kılavuzu Izleyerek sanal MAKINENIZI bir ad etki alanı denetleyicisi olarak ayarlayın.

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Bulut hizmetinizi sanal ağa ekleyin
Ardından, bulut hizmeti dağıtımınızı yeni VNet 'e eklemeniz gerekir. Bunu yapmak için, Visual Studio 'Yu veya seçtiğiniz düzenleyiciyi kullanarak cscfg 'nize ilgili bölümleri ekleyerek bulut hizmeti cscfg 'nizi değiştirin.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Sonra bulut hizmetleri projenizi derleyin ve Azure 'a dağıtın. Bulut hizmetleri paketinizi Azure 'a dağıtmaya yönelik yardım almak için bkz. [bulut hizmeti oluşturma ve dağıtma](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Web/çalışan rollerinizi etki alanına bağlama
Bulut hizmeti projeniz Azure 'da dağıtıldıktan sonra, AD etki alanı uzantısını kullanarak rol örneklerinizi özel AD etki alanına bağlayın. AD etki alanı uzantısını mevcut bulut hizmetleri dağıtımınıza eklemek ve özel etki alanına katmak için PowerShell 'de aşağıdaki komutları yürütün:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

İşte bu kadar.

Bulut hizmetlerinizin özel etki alanı denetleyicinize katılması gerekir. AD etki alanı uzantısının nasıl yapılandırılacağı hakkında daha fazla bilgi edinmek istiyorsanız PowerShell yardımı 'nı kullanın. Birkaç örnek aşağıda verilmiştir:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
