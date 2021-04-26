---
title: Azure Resource Manager şablonu kullanarak bir Azure kanıtlama sertifikası oluşturma
description: Azure Resource Manager şablonu kullanarak Azure kanıtlama sertifikası oluşturmayı öğrenin.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 7d70f9ebd071d6699412f56e9dca1abcebb38105
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834255"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Hızlı başlangıç: ARM şablonuyla Azure kanıtlama sağlayıcısı oluşturma

[Microsoft Azure kanıtlama](overview.md) , güvenilir yürütme ortamlarının (TEEs) atlaması için bir çözümdür. Bu hızlı başlangıç, Microsoft Azure kanıtlama ilkesi oluşturmak için bir Azure Resource Manager şablonu dağıtma işlemine odaklanır (ARM şablonu).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure'a Dağıt](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-attestation-provider-create) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

Şablonda tanımlanan Azure kaynakları:

- Microsoft. kanıtlama/attestationProviders

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure 'da oturum açmak ve şablonu açmak için aşağıdaki görüntüyü seçin.

    [![Azure'a Dağıt](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. Aşağıdaki değerleri seçin veya girin.

    Belirtilmediği takdirde, kanıtlama sağlayıcısını oluşturmak için varsayılan değeri kullanın.

    - **Kanıtlama sağlayıcı adı**: Azure kanıtlama sağlayıcınız için bir ad seçin.
    - **Konum**: bir konum seçin. Örneğin **Orta ABD**.
    - **Etiketler**: bir konum seçin. Örneğin **Orta ABD**.

1. **Satın al**'ı seçin. Kanıtlama kaynağı başarıyla dağıtıldıktan sonra bir bildirim alırsınız.

Şablonu dağıtmak için Azure portalı kullanılır. Azure portal ek olarak, Azure PowerShell, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Kanıtlama kaynağını denetlemek için Azure portal kullanabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç sonrasında diğer Azure kanıtlama derlemesi. Sonraki hızlı başlangıç ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakmanız yararlı olabilir.

Artık gerekli değilse, kanıtlama kaynağını silen kaynak grubunu silin. Azure CLı veya Azure PowerShell kullanarak kaynak grubunu silmek için:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir ARM şablonu kullanarak bir kanıtlama kaynağı oluşturdunuz ve dağıtımı doğruladı. Azure kanıtlama hakkında daha fazla bilgi edinmek için bkz. [Azure kanıtlama 'Ya genel bakış](overview.md).
