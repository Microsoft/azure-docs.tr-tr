---
title: "Hızlı başlangıç: Veri Bilimi VM'si Kaynak Yöneticisi şablonu oluşturma"
titleSuffix: Azure Data Science Virtual Machine
description: Bu hızlı başlangıçta, bir Veri Bilimi Sanal Makinesi hızlı bir şekilde dağıtmak için Azure Resource Manager şablonu kullanacaksınız
services: machine-learning
author: lobrien
ms.author: laobri
ms.date: 06/10/2020
ms.topic: quickstart
ms.service: data-science-vm
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 0683634223a63281ce2b42ebb02f87f9211a589e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530654"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak Ubuntu Veri Bilimi Sanal Makinesi oluşturma

Bu hızlı başlangıçta, bir Azure Resource Manager şablonu (ARM şablonu) kullanarak Ubuntu 18,04 Veri Bilimi Sanal Makinesi nasıl oluşturacağınız gösterilmektedir. Veri bilimi sanal makineleri, bir veri bilimi ve makine öğrenimi çerçeveleri ve araçları paketiyle önceden yüklenmiş bulut tabanlı sanal makinelerdir. GPU destekli işlem kaynaklarına dağıtıldığında, tüm araçlar ve kitaplıklar GPU 'YU kullanacak şekilde yapılandırılır.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/services/machine-learning/) oluşturun.

* **Yerel ortamınızdan** bu belgedeki CLI komutlarını kullanmak IÇIN [Azure CLI](/cli/azure/install-azure-cli)gerekir.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

Aşağıdaki kaynaklar şablonda tanımlanmıştır:

* [Microsoft. Network/NetworkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/Publicıpaddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): bulut tabanlı bir sanal makine oluşturun. Bu şablonda, sanal makine Ubuntu 18,04 çalıştıran bir Veri Bilimi Sanal Makinesi olarak yapılandırılır.

## <a name="deploy-the-template"></a>Şablonu dağıtma

Azure CLı 'dan şablonu kullanmak için oturum açın ve aboneliğinizi seçin (bkz. [Azure CLI Ile oturum açma](/cli/azure/authenticate-azure-cli)). Ardından şunu çalıştırın:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

Yukarıdaki komutu çalıştırdığınızda şunu girin:

1. DSVM ve ilişkili kaynakları içerecek şekilde oluşturmak istediğiniz kaynak grubunun adı.
1. Dağıtımı yapmak istediğiniz Azure konumu.
1. Kullanmak istediğiniz kimlik doğrulama türü (dizeyi girin `password` veya `sshPublicKey` ).
1. Yönetici hesabının oturum açma adı (Bu değer olmayabilir `admin` ).
1. Hesap için parola veya SSH ortak anahtarının değeri.

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Veri Bilimi Sanal Makinesi görmek için:

1. [Azure portal](https://portal.azure.com)'a gidin
1. Oturum açın.
1. Az önce oluşturduğunuz kaynak grubunu seçin.

Kaynak grubunun bilgilerini görürsünüz:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="DSVM içeren temel bir kaynak grubunun ekran görüntüsü":::

Bilgi sayfasına gitmek için sanal makine kaynağına tıklayın. Burada, bağlantı ayrıntıları dahil olmak üzere VM hakkındaki bilgileri bulabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu sanal makineyi kullanmak istemiyorsanız, silin. DSVM, depolama hesabı gibi diğer kaynaklarla ilişkilendirildiğinden, muhtemelen oluşturduğunuz kaynak grubunun tamamını silmek isteyeceksiniz. **Sil** düğmesine tıklayıp onay yazarak kaynak grubunu Portal kullanarak silebilirsiniz. Ya da, kaynak grubunu CLı 'dan ile silebilirsiniz:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta ARM şablonundan bir Veri Bilimi Sanal Makinesi oluşturdunuz.

> [!div class="nextstepaction"]
> [Örnek programlar & ML izlenecek yolları](dsvm-samples-and-walkthroughs.md)
