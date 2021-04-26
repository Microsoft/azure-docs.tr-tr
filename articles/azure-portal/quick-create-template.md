---
title: Azure Resource Manager şablonu kullanarak Azure portal panosu oluşturma
description: Azure Resource Manager şablonu kullanarak Azure portal panosu oluşturmayı öğrenin.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/15/2021
ms.openlocfilehash: a3ab8767e09256ed8235dbd980ea3336a6f0fb1d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658338"
---
# <a name="quickstart-create-a-dashboard-in-the-azure-portal-by-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak Azure portal bir pano oluşturma

Azure portal bir Pano, bulut kaynaklarınızın odaklanmış ve düzenlenmiş bir görünümüdür. Bu hızlı başlangıç, bir pano oluşturmak için bir Azure Resource Manager şablonu dağıtma işlemine odaklanır (ARM şablonu). Pano, bir sanal makinenin (VM) ve bazı statik bilgi ve bağlantıların performansını gösterir.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Var olan bir VM.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Bu hızlı başlangıç bölümünde oluşturduğunuz Pano, mevcut bir VM gerektirir. Bu adımları izleyerek bir VM oluşturun.

1. Azure portal **Cloud Shell**' ni seçin.

    ![Azure portal şeritten Cloud Shell ' i seçin](media/quick-create-template/cloud-shell.png)

1. **Cloud Shell** penceresinde **PowerShell**' i seçin.

    ![Terminal penceresinde PowerShell ' i seçin](media/quick-create-template/powershell.png)

1. Aşağıdaki komutu kopyalayın ve bir kaynak grubu oluşturmak için komut istemine girin.

    ```powershell
    New-AzResourceGroup -Name SimpleWinVmResourceGroup -Location EastUS
    ```

    ![Komut istemine bir komut kopyalama](media/quick-create-template/command-prompt.png)

1. Aşağıdaki komutu kopyalayın ve kaynak grubunda bir VM oluşturmak için komut istemine girin.

    ```powershell
    New-AzVm `
        -ResourceGroupName "SimpleWinVmResourceGroup" `
        -Name "SimpleWinVm" `
        -Location "East US" 
    ```

1. VM için bir Kullanıcı adı ve parola girin. Bu yeni bir Kullanıcı adı ve paroladır; Örneğin, Azure 'da oturum açmak için kullandığınız hesap değildir. Daha fazla bilgi için bkz. [Kullanıcı adı gereksinimleri](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) ve [Parola gereksinimleri](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

    VM dağıtımı artık başlar ve genellikle birkaç dakika sürer. Dağıtım tamamlandıktan sonra bir sonraki bölüme geçin.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-azure-portal-dashboard/) alınmıştır. Bu makalenin şablonu burada görüntülenemeyecek kadar uzun. Şablonu görüntülemek için bkz. [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-portal-dashboard/azuredeploy.json). [Microsoft. Portal/panolar](/azure/templates/microsoft.portal/dashboards) -şablonda bir Azure kaynağı tanımlanmıştır. Azure Portal bir pano oluşturun.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın.

    [![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

1. Aşağıdaki değerleri seçin veya girin ve ardından **gözden geçir + oluştur**' u seçin.

    ![ARM şablonu, pano oluşturma, Portal dağıtma](media/quick-create-template/create-dashboard-using-template-portal.png)

    Belirtilmediği takdirde, Panoyu oluşturmak için varsayılan değerleri kullanın.

    * **Abonelik**: Bir Azure aboneliği seçin.
    * **Kaynak grubu**: **Simplewinvmresourcegroup** öğesini seçin.
    * **Konum**: **Doğu ABD** seçin.
    * **Sanal makine adı**: **simplewinvm** girin.
    * **Sanal makine kaynak grubu**: **Simplewinvmresourcegroup** girin.

1. **Oluştur** veya **satın al**' ı seçin. Pano başarıyla dağıtıldıktan sonra bir bildirim alırsınız:

    ![ARM şablonu, pano oluşturma, Portal bildirimi dağıtma](media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure portal şablonu dağıtmak için kullanıldı. Azure portalının yanı sıra Azure PowerShell, Azure CLI ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

VM 'yi ve ilişkili panoyu kaldırmak istiyorsanız, bunları içeren kaynak grubunu silin.

1. Azure portal, **Simplewinvmresourcegroup** öğesini arayın ve arama sonuçlarında bunu seçin.

1. **Simplewınvmresourcegroup** sayfasında, **kaynak grubunu sil**' i seçin, onaylamak için kaynak grubunun adını girin ve **Sil**' i seçin.

    ![Kaynak grubunu silme](media/quick-create-template/delete-resource-group.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure portal panolar hakkında daha fazla bilgi için bkz.:

> [!div class="nextstepaction"]
> [Azure portalında pano oluşturma ve paylaşma](azure-portal-dashboards.md)