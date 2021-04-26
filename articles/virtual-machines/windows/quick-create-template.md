---
title: 'Hızlı başlangıç: bir Windows VM oluşturmak için Kaynak Yöneticisi şablonu kullanma'
description: Bu hızlı başlangıçta, bir Windows sanal makinesi oluşturmak için Kaynak Yöneticisi şablonu kullanmayı öğreneceksiniz
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: b2856c51ccc31b57ea567c5d0940cd260797f770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560761"
---
# <a name="quickstart-create-a-windows-virtual-machine-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak Windows sanal makinesi oluşturma

Bu hızlı başlangıçta, Azure 'da bir Windows sanal makinesi (VM) dağıtmak için bir Azure Resource Manager şablonunun (ARM şablonu) nasıl kullanılacağı gösterilmektedir.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-vm-simple-windows/azuredeploy.json":::


Şablonda birkaç kaynak tanımlanmıştır:

- [**Microsoft. Network/virtualNetworks/alt ağlar**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): bir alt ağ oluşturun.
- [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): depolama hesabı oluşturun.
- [**Microsoft. Network/Publicıpaddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): genel bir IP adresi oluşturun.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): bir ağ güvenlik grubu oluşturun.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): bir sanal ağ oluşturun.
- [**Microsoft. Network/NetworkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): bir NIC oluşturun.
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): sanal makine oluşturun.



## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon bir anahtar kasası ve gizli dizi oluşturur.

    [![Azure’a dağıtma](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

1. Aşağıdaki değerleri seçin veya girin. Kullanılabilir olduğunda varsayılan değerleri kullanın.

    - **Abonelik**: Bir Azure aboneliği seçin.
    - **Kaynak grubu**: açılan listeden var olan bir kaynak grubunu seçin veya **Yeni oluştur**' u seçin, kaynak grubu için benzersiz bir ad girin ve ardından **Tamam**' a tıklayın.
    - **Konum**: Bir konum seçin.  Örneğin **Orta ABD**.
    - **Yönetici Kullanıcı adı**: *azureuser* gibi bir Kullanıcı adı sağlayın.
    - **Yönetici parolası**: yönetici hesabı için kullanılacak bir parola belirtin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](faq.md#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.
    - **DNS etiketi öneki**: DNS etiketinin bir parçası olarak kullanmak üzere benzersiz bir tanımlayıcı girin.
    - **Windows işletim sistemi sürümü**: VM 'de hangi Windows sürümünü çalıştırmak istediğinizi seçin.
    - **VM boyutu**: VM için kullanılacak [boyutu](../sizes.md) seçin.
    - **Konum**: zaten varsa, varsayılan olarak kaynak grubuyla aynı konumdadır.
1. **Gözden geçir ve oluştur**’u seçin. Doğrulama tamamlandıktan sonra, sanal makineyi oluşturmak ve dağıtmak için **Oluştur** ' u seçin.


Şablonu dağıtmak için Azure portalı kullanılır. Azure portal ek olarak, Azure PowerShell, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Sanal makineyi ve oluşturulan diğer kaynağı denetlemek için Azure portal kullanabilirsiniz. Dağıtım tamamlandıktan sonra, VM 'yi ve diğer kaynakları görmek için **kaynak grubuna git** ' i seçin.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında, kaynak grubundaki VM 'yi ve tüm kaynakları silen kaynak grubunu silin. 

1. **Kaynak grubunu** seçin.
1. Kaynak grubunun sayfasında **Sil**' i seçin.
1. İstendiğinde, kaynak grubunun adını yazın ve **Sil**' i seçin.


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, ARM şablonunu kullanarak basit bir sanal makine dağıttınız. Azure sanal makineleri hakkında daha fazla bilgi için Linux VM’lerine yönelik öğreticiye geçin.


> [!div class="nextstepaction"]
> [Azure Windows sanal makine öğreticileri](./tutorial-manage-vm.md)