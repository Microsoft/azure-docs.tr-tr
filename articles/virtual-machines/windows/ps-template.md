---
title: Azure 'da bir şablondan Windows sanal makinesi oluşturma
description: Yeni bir Windows sanal makinesini kolayca oluşturmak için bir Kaynak Yöneticisi şablonu ve PowerShell kullanın.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99e292930414ae027c9cbbf3a901d550041899d2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065544"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Kaynak Yöneticisi şablonundan Windows sanal makinesi oluşturma

Azure Cloud Shell 'den bir Azure Resource Manager şablonu ve Azure PowerShell kullanarak Windows sanal makinesi oluşturmayı öğrenin. Bu makalede kullanılan şablon, tek bir alt ağa sahip yeni bir sanal ağda Windows Server çalıştıran tek bir sanal makine dağıtır. Linux sanal makinesi oluşturmak için bkz. [Azure Resource Manager şablonlarıyla Linux sanal makinesi oluşturma](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Azure sanal makinesi oluşturma genellikle iki adım içerir:

- Bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Bir sanal makineden önce bir kaynak grubu oluşturulmalıdır.
- Sanal makine oluşturur.

Aşağıdaki örnek bir [Azure hızlı başlangıç şablonundan](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)bir VM oluşturur. Şablonun bir kopyası aşağıda verilmiştir:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

PowerShell betiğini çalıştırmak için, Azure Cloud Shell 'i açmak üzere **deneyin** ' i seçin. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından **Yapıştır**' ı seçin:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

PowerShell 'i Azure Cloud Shell yerine yerel olarak yükleyip kullanmayı tercih ederseniz, bu öğretici Azure PowerShell modülünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız, aynı zamanda çalıştırmak ihtiyacınız `Connect-AzAccount` Azure ile bir bağlantı oluşturmak için.

Önceki örnekte, GitHub 'da depolanan bir şablon belirttiniz. Ayrıca, bir şablon indirebilir veya oluşturabilir ve `--template-file` parametresiyle yerel yolu belirtebilirsiniz.

Bazı ek kaynaklar aşağıda verilmiştir:

- Kaynak Yöneticisi şablonlarının nasıl geliştirileceği hakkında bilgi edinmek için bkz. [Azure Resource Manager belgeleri](/azure/azure-resource-manager/).
- Azure sanal makine şemalarını görmek için bkz. [Azure şablon başvurusu](/azure/templates/microsoft.compute/allversions).
- Daha fazla sanal makine şablonu örneği görmek için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Sanal makineye bağlanma

Önceki betiğin son PowerShell komutu sanal makine adını gösterir. Sanal makineye bağlanmak için bkz. [Windows çalıştıran bir Azure sanal makinesine bağlanma ve oturum açma](./connect-logon.md).

## <a name="next-steps"></a>Sonraki Adımlar

- Dağıtım ile ilgili sorunlar varsa, [Azure Resource Manager ile yaygın Azure dağıtım hatalarıyla Ilgili sorunları gidermeye](../../resource-manager-common-deployment-errors.md)göz atabilirsiniz.
- [Azure PowerShell modülü Ile Windows VM 'Leri oluşturma ve yönetme](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)bölümünde bir sanal makine oluşturma ve yönetme hakkında bilgi edinin.

Şablon oluşturma hakkında daha fazla bilgi edinmek için, dağıttığınız kaynak türlerinin JSON sözdizimini ve özelliklerini görüntüleyin:

- [Microsoft. Network/Publicıpaddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft. Network/NetworkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
