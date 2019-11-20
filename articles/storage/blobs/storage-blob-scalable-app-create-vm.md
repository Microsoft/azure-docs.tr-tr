---
title: Azure’da ölçeklenebilir bir uygulama için VM ve depolama hesabı oluşturma | Microsoft Docs
description: Azure blob depolama kullanarak ölçeklenebilir bir uygulama çalıştırmak için kullanılacak bir VM’yi dağıtmayı öğrenme
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 49689585b3f52bbee300ff68d2c2653f5e7ff8d0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699073"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Ölçeklenebilir bir uygulama için sanal makine ve depolama hesabı oluşturma

Bu öğretici, bir dizinin birinci bölümüdür. Bu öğretici, Azure depolama hesabı ile büyük miktarda rastgele veri yükleyen ve indiren bir uygulamayı nasıl dağıtacağınızı gösterir. İşinizi tamamladığınızda, bir sanal makine üzerinde bir depolama hesabına büyük miktarlarda veri yükleyip indirdiğiniz bir konsol uygulaması çalıştırırsınız.

Serinin birinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Depolama hesabı oluşturma
> * Sanal makine oluşturma
> * Özel betik uzantısı yapılandırma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici, Azure PowerShell modülü az 0,7 veya üzeri bir sürümü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Ile bir Azure Kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma
 
Örnek, 50 büyük dosyayı bir Azure Depolama hesabındaki blob kapsayıcısına yükler. Depolama hesabı, Azure Storage veri nesnelerinizi depolamak ve bunlara erişmek için benzersiz ad alanı sağlar. [Yeni-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount) komutunu kullanarak oluşturduğunuz kaynak grubunda bir depolama hesabı oluşturun.

Aşağıdaki komutta, Blob depolama hesabına ilişkin kendi genel benzersiz adınızı `<blob_storage_account>` yer tutucusunu gördüğünüz yere yerleştirin.

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Sanal makine yapılandırması oluşturun. Bu yapılandırma, sanal makineyi dağıtırken kullanılan sanal makine görüntüsü, boyutu ve kimlik doğrulama yapılandırması gibi ayarları içerir. Bu adımı çalıştırırken kimlik bilgileri istenir. Girdiğiniz değerler, sanal makinenin kullanıcı adı ve parolası olarak yapılandırılır.

[New-AzVM](/powershell/module/az.compute/new-azvm)ile sanal makineyi oluşturun.

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Yapılandırmayı dağıtma

Bu öğretici için, sanal makine üzerinde yüklenmesi gereken önkoşullar bulunur. Özel betik uzantısı aşağıdaki görevleri tamamlayan bir PowerShell betiği çalıştırmak için kullanılır:

> [!div class="checklist"]
> * .NET core 2.0’ı yükleme
> * chocolatey’i yükleme
> * GIT’i yükleme
> * Örnek depoyu kopyalama
> * NuGet paketlerini geri yükleme
> * Rastgele veriler ile 50 adet 1 GB dosya oluşturur

Sanal makinenin yapılandırmasını tamamlamak için aşağıdaki cmdlet’i çalıştırın. Bu adımın tamamlanması 5-15 dakika sürer.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Sonraki adımlar

Serinin birinci bölümünde, bir depolama hesabı oluşturmayı, bir sanal makine dağıtmayı ve sanal makineyi aşağıdakiler gibi gerekli önkoşullar ile yapılandırmayı öğrendiniz:

> [!div class="checklist"]
> * Depolama hesabı oluşturma
> * Sanal makine oluşturma
> * Özel betik uzantısı yapılandırma

Üstel yeniden deneme ve paralelliği kullanarak depolama hesabına büyük miktarlarda veri yüklemek için serinin ikinci bölümüne ilerleyin.

> [!div class="nextstepaction"]
> [Bir depolama hesabına paralel şekilde büyük miktarlarda dosyaları yükleme](storage-blob-scalable-app-upload-files.md)
