---
title: Azure 'da Packer ile Windows VM görüntüleri oluşturma
description: Azure 'da Windows sanal makinelerinin görüntülerini oluşturmak için Packer 'ı nasıl kullanacağınızı öğrenin
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: b2ff9869b0de7a0285644bea462101cd1dc80b99
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039230"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Azure 'da Windows sanal makine görüntüleri oluşturmak için Packer kullanma
Azure 'daki her sanal makine (VM), Windows Dağıtım ve işletim sistemi sürümünü tanımlayan bir görüntüden oluşturulur. Görüntüler, önceden yüklenmiş uygulamaları ve konfigürasyonları içerebilir. Azure Marketi, en yaygın işletim sistemi ve uygulama ortamları için pek çok birinci ve üçüncü taraf görüntü sağlar veya gereksinimlerinize uygun kendi özel görüntülerinizi de oluşturabilirsiniz. Bu makalede, Azure 'da özel görüntüler tanımlamak ve derlemek için açık kaynaklı araç [Packer](https://www.packer.io/) 'ın nasıl kullanılacağı açıklanır.

Bu makale, [az PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps) Version 1.3.0 ve [Packer](https://www.packer.io/docs/install/index.html) sürüm 1.3.4 kullanılarak 2/21/2019 ' de son test edilmiştir.

> [!NOTE]
> Azure 'da, kendi özel görüntülerinizi tanımlamak ve oluşturmak için Azure görüntü Oluşturucu (Önizleme) hizmeti artık vardır. Azure Image Builder, Packer üzerine kurulmuştur, bu nedenle mevcut Packer kabuğu hazırlayıcı betikleri ile birlikte kullanabilirsiniz. Azure Image Builder 'ı kullanmaya başlamak için bkz. [Azure Image Builder Ile WINDOWS VM oluşturma](image-builder.md).

## <a name="create-azure-resource-group"></a>Azure Kaynak grubu oluştur
Yapı işlemi sırasında Packer, kaynak VM 'yi oluşturduğunda geçici Azure kaynakları oluşturur. Bu kaynak VM 'yi bir görüntü olarak kullanılacak şekilde yakalamak için bir kaynak grubu tanımlamanız gerekir. Packer Build işleminin çıktısı bu kaynak grubunda saklanır.

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurepowershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Azure kimlik bilgilerini oluşturma
Packer hizmet sorumlusu kullanarak Azure ile kimlik doğrular. Azure hizmet sorumlusu, Packer gibi uygulamalar, hizmetler ve otomasyon araçlarıyla kullanabileceğiniz bir güvenlik kimliğidir. İzinleri, hizmet sorumlusunun Azure 'da gerçekleştirebileceği işlemlere göre kontrol edersiniz ve tanımlar.

New- [AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) ile bir hizmet sorumlusu oluşturun ve [Yeni-azroleatama](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)ile kaynakları oluşturmak ve yönetmek için hizmet sorumlusu için izinler atayın. `-DisplayName` değerinin benzersiz olması gerekir; gerekirse kendi değeri ile değiştirin.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Sonra parolayı ve uygulama KIMLIĞINI çıkış.

```powershell
$plainPassword
$sp.ApplicationId
```


Azure 'da kimlik doğrulaması yapmak için [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription)ile Azure kiracınızı ve abonelik kimliklerini de edinmeniz gerekir:

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Packer şablonunu tanımla
Görüntü oluşturmak için JSON dosyası olarak bir şablon oluşturursunuz. Şablonda, gerçek yapı işlemini yürüten oluşturucular ve hazırlayıcılar tanımlarsınız. Packer, Azure [için](https://www.packer.io/docs/builders/azure.html) , önceki adımda oluşturulan hizmet sorumlusu kimlik bilgileri gibi Azure kaynaklarını tanımlamanızı sağlayan bir Oluşturucu içerir.

*Windows. JSON* adlı bir dosya oluşturun ve aşağıdaki içeriği yapıştırın. Aşağıdaki değerleri girin:

| Parametre                           | Nereden alınır |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | `$sp.applicationId` hizmet sorumlusu KIMLIĞINI görüntüleme |
| *client_secret*                     | `$plainPassword` otomatik olarak oluşturulan parolayı görüntüleme |
| *tenant_id*                         | `$sub.TenantId` komutundan çıkış |
| *subscription_id*                   | `$sub.SubscriptionId` komutundan çıkış |
| *managed_image_resource_group_name* | İlk adımda oluşturduğunuz kaynak grubunun adı |
| *managed_image_name*                | Oluşturulan yönetilen disk görüntüsünün adı |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Bu şablon bir Windows Server 2016 VM oluşturur, IIS 'yi yüklüyor ve sonra VM 'yi Sysprep ile genelleştirir. IIS yüklemesi ek komutlar çalıştırmak için PowerShell hazırlayıcısı 'nı nasıl kullanabileceğinizi gösterir. Son Packer görüntüsü, gerekli yazılım yüklemesi ve yapılandırmasını içerir.


## <a name="build-packer-image"></a>Packer görüntüsü oluştur
Yerel makinenizde zaten Packer yüklü değilse, [Packer yükleme yönergelerini izleyin](https://www.packer.io/docs/install/index.html).

Bir komut istemi açıp Packer şablonu dosyanızı aşağıdaki gibi belirterek görüntüyü oluşturun:

```
./packer build windows.json
```

Yukarıdaki komutlardan çıkış örneği aşağıdaki gibidir:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer 'ın VM 'yi oluşturması, hazırlayıcılar çalıştırması ve dağıtımı temizlemesi birkaç dakika sürer.


## <a name="create-a-vm-from-the-packer-image"></a>Packer görüntüsünden VM oluşturma
Artık, [Yeni-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)ile görüntinizden bir VM oluşturabilirsiniz. Destekleyici ağ kaynakları, zaten mevcut değilse oluşturulur. İstendiğinde, VM 'de oluşturulacak bir Yönetici Kullanıcı adı ve parola girin. Aşağıdaki örnek *Mypackerımage*öğesinden *myvm* adlı bir VM oluşturur:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Sanal makineleri, Packer görüntüsünden farklı bir kaynak grubunda veya bölgede oluşturmak istiyorsanız, görüntü adı yerine görüntü KIMLIĞINI belirtin. [Get-azımage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage)Ile görüntü kimliğini elde edebilirsiniz.

Sanal makinenin Packer görüntüsünden oluşturulması birkaç dakika sürer.


## <a name="test-vm-and-webserver"></a>Test VM ve Web sunucusu
[Get-Azpublicıpaddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress)ile sanal MAKINENIZIN genel IP adresini alın. Aşağıdaki örnek, daha önce oluşturulan *myPublicIP* için IP adresini alır:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

SANAL makinenizin, Packer provisioner 'dan IIS yüklemesini de içeren bir Web tarayıcısına, ' ye genel IP adresini girin.

![Varsayılan IIS sitesi](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Sonraki adımlar
Ayrıca, mevcut Packer sağlayıcısı oluştur betiklerini [Azure Image Builder](image-builder.md)ile de kullanabilirsiniz.
