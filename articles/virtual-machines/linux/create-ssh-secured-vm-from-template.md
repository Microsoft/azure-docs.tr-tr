---
title: Bir şablondan Azure 'da bir Linux VM oluşturma
description: Azure CLı kullanarak Kaynak Yöneticisi şablondan bir Linux sanal makinesi oluşturma
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e32e9f17b5c48e18453724eb683ba2e86dd0cdb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036495"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarıyla Linux sanal makinesi oluşturma

Azure Cloud Shell 'den bir Azure Resource Manager şablonu ve Azure CLı kullanarak bir Linux sanal makinesi (VM) oluşturmayı öğrenin. Bir Windows sanal makinesi oluşturmak için, bkz. [Kaynak Yöneticisi şablondan Windows sanal makinesi oluşturma](../windows/ps-template.md).

## <a name="templates-overview"></a>Şablonlara genel bakış

Azure Resource Manager şablonlar, Azure çözümünüzün altyapısını ve yapılandırmasını tanımlayan JSON dosyalarıdır. Bir şablon kullanarak çözümünü yaşam döngüsü boyunca defalarca dağıtabilir ve kaynaklarınızın tutarlı bir durumda dağıtıldığından emin olabilirsiniz. Şablon biçimi ve nasıl oluşturacağınız hakkında daha fazla bilgi edinmek için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Resource Manager şablonları oluşturma ve dağıtma](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md). Kaynak türleri için JSON söz dizimini görüntülemek üzere bkz. [Azure Resource Manager şablonlarında kaynak tanımlama](/azure/templates/microsoft.compute/allversions).

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Azure sanal makinesi oluşturma genellikle iki adım içerir:

1. Bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Bir sanal makineden önce bir kaynak grubu oluşturulmalıdır.
1. Sanal makine oluşturur.

Aşağıdaki örnek bir [Azure hızlı başlangıç şablonundan](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)bir VM oluşturur. Bu dağıtım için yalnızca SSH kimlik doğrulamasına izin veriliyor. İstendiğinde,, *~/. ssh/id_rsa. pub*içeriği gıbı kendi SSH ortak anahtarınızın değerini sağlayın. Bir SSH anahtar çifti oluşturmanız gerekiyorsa bkz. [Azure 'Da Linux VM 'ler IÇIN SSH anahtar çifti oluşturma ve kullanma](mac-create-ssh-keys.md). Şablonun bir kopyası aşağıda verilmiştir:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

CLı betiğini çalıştırmak için, Azure Cloud Shell 'i açmak üzere **deneyin** ' i seçin. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından **Yapıştır**' ı seçin:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Son Azure CLı komutu, yeni oluşturulan VM 'nin genel IP adresini gösterir. Sanal makineye bağlanmak için genel IP adresine ihtiyacınız vardır. Bu makalenin sonraki bölümüne bakın.

Önceki örnekte, GitHub 'da depolanan bir şablon belirttiniz. Ayrıca, bir şablon indirebilir veya oluşturabilir ve `--template-file` parametresiyle yerel yolu belirtebilirsiniz.

Bazı ek kaynaklar aşağıda verilmiştir:

- Kaynak Yöneticisi şablonlarının nasıl geliştirileceği hakkında bilgi edinmek için bkz. [Azure Resource Manager belgeleri](/azure/azure-resource-manager/).
- Azure sanal makine şemalarını görmek için bkz. [Azure şablon başvurusu](/azure/templates/microsoft.compute/allversions).
- Daha fazla sanal makine şablonu örneği görmek için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Sanal makineye bağlanma

Daha sonra sanal makinenize normal olarak SSH ekleyebilirsiniz. Yukarıdaki komuttan kendi genel IP adresinizi sağlayın:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekte, temel bir Linux sanal makinesi oluşturdunuz. Uygulama çerçeveleri içeren veya daha karmaşık ortamlar oluşturan daha fazla Kaynak Yöneticisi şablonları için [Azure hızlı başlangıç şablonlarına](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)gözatamazsınız.

Şablon oluşturma hakkında daha fazla bilgi edinmek için, dağıttığınız kaynak türlerinin JSON sözdizimini ve özelliklerini görüntüleyin:

- [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft. Network/Publicıpaddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft. Network/NetworkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
