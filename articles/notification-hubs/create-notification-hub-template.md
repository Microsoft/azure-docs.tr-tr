---
title: Azure Resource Manager şablonu kullanarak bir Azure Notification Hub 'ı oluşturma
description: Azure Resource Manager şablonu kullanarak bir Azure Notification Hub 'ı oluşturmayı öğrenin (ARM şablonu).
services: notification-hubs
author: sethmanheim
ms.author: sethm
ms.reviewer: thsomasu
ms.date: 08/04/2020
ms.lastreviewed: 05/15/2020
ms.topic: quickstart
ms.service: notification-hubs
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: a328d6b8942c3209e13dc91a2fb892e98e3016f6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533483"
---
# <a name="quickstart-create-a-notification-hub-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak bir Bildirim Hub 'ı oluşturma

Azure Notification Hubs, herhangi bir arka uçtan (bulut veya şirket içi) herhangi bir platforma (iOS, Android, Windows, e, vb.) bildirimler göndermenizi sağlayan kullanımı kolay ve ölçeği genişletilmiş bir gönderim altyapısı sağlar. Hizmet hakkında daha fazla bilgi için bkz. [Azure Notification Hubs nedir?](notification-hubs-push-notification-overview.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Bu hızlı başlangıç, bir Azure Notification Hubs ad alanı oluşturmak için bir Azure Resource Manager şablonu (ARM şablonu) kullanır ve bu ad alanı içinde **Myhub** adlı bir Bildirim Hub 'ı.

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-notification-hub/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json":::

* [Microsoft. Notificationhub 'Lar/ad alanları](/azure/templates/microsoft.notificationhubs/namespaces)
* [Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub 'Lar](/azure/templates/microsoft.notificationhubs/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>Şablonu dağıtma

Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon bir Notification Hubs ad alanı adını parametre olarak alır. Şablon daha sonra bu adı taşıyan bir ad alanı ve bu ad alanı içinde **Myhub** adlı bir Bildirim Hub 'ı oluşturur.

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Dağıtılan kaynakları denetlemek için Azure portal kullanabilir veya dağıtılan Notification Hubs ad alanını ve hub 'ı listelemek için Azure CLı veya Azure PowerShell betiği kullanabilirsiniz:

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
':::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::': null
---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse kaynak grubundaki kaynakları silen kaynak grubunu silin.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Sonraki adımlar

Şablon oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için, bkz.:

> [!div class="nextstepaction"]
> [Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
