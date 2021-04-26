---
title: Hızlı başlangıç-Azure Resource Manager şablonuna göre Azure Stream Analytics işi oluşturma
description: Bu hızlı başlangıçta, bir Azure Stream Analytics işi oluşturmak için Azure Resource Manager şablonunun nasıl kullanılacağı gösterilmektedir.
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/28/2020
ms.openlocfilehash: 2e8e7890adcb6b91d79bbf4e119488e365410d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020086"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-by-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak Azure Stream Analytics işi oluşturma

Bu hızlı başlangıçta, bir Azure Stream Analytics işi oluşturmak için bir Azure Resource Manager şablonu (ARM şablonu) kullanırsınız. İş oluşturulduktan sonra dağıtımı doğrularsınız.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Bu makaleyi tamamlayabilmeniz için şunları yapmanız gerekir:

* Azure aboneliğiniz olmalıdır- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-streamanalytics-create/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-streamanalytics-create/azuredeploy.json":::

Şablonda tanımlanan Azure kaynağı [Microsoft. StreamAnalytics/StreamingJobs](/azure/templates/microsoft.streamanalytics/streamingjobs): Azure Stream Analytics işi oluşturma.

## <a name="deploy-the-template"></a>Şablonu dağıtma

Bu bölümde ARM şablonunu kullanarak bir Azure Stream Analytics işi oluşturursunuz.

1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon bir Azure Stream Analytics işi oluşturur.

   [![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

2. Azure Stream Analytics işinizi oluşturmak için gerekli değerleri sağlayın.

   ![Azure Resource Manager şablonu kullanarak Azure Stream Analytics işi oluşturma](./media/quick-create-azure-resource-manager/create-stream-analytics-job-resource-manager-template.png "Azure Resource Manager şablonu kullanarak Azure Stream Analytics işi oluşturma")

   Aşağıdaki değerleri sağlayın:

   |Özellik  |Açıklama  |
   |---------|---------|
   |**Abonelik**     | Açılan listeden Azure aboneliğinizi seçin.        |
   |**Kaynak grubu**     | Yeni bir kaynak grubu oluşturmayı veya mevcut bir kaynak grubunu kullanmayı seçin. Kaynak grubu, bir Azure çözümüne ilişkin kaynakları tutan bir kapsayıcıdır. Daha fazla bilgi için bkz. [Azure Kaynak Grubuna genel bakış](../azure-resource-manager/management/overview.md). |
   |**Bölge**     | **Doğu ABD**’yi seçin. Kullanılabilir diğer bölgeler için bkz. [Bölgeye göre kullanılabilir Azure hizmetleri](https://azure.microsoft.com/regions/services/).        |
   |**Stream Analytics Iş adı**     | Stream Analytics işiniz için bir ad sağlayın.      |
   |**Akış birimi sayısı**     |  İhtiyacınız olan akış birimi sayısını seçin. Daha fazla bilgi için bkz. [akış birimlerini anlama ve ayarlama](stream-analytics-streaming-unit-consumption.md).       |

3. **Gözden Geçir ve Oluştur**’u ve sonra **Oluştur**’u seçin.

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Azure Stream Analytics işini denetlemek için Azure portal kullanabilir ya da kaynağı listelemek için aşağıdaki Azure CLı veya Azure PowerShell komut dosyasını kullanabilirsiniz.

### <a name="azure-cli"></a>Azure CLI’si

```azurecli-interactive
echo "Enter your Azure Stream Analytics job name:" &&
read streamAnalyticsJobName &&
echo "Enter the resource group where the Azure Stream Analytics job exists:" &&
read resourcegroupName &&
az stream-analytics job show -g $resourcegroupName -n $streamAnalyticsJobName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Stream Analytics job exists"
(Get-AzResource -ResourceType "Microsoft.StreamAnalytics/StreamingJobs" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticilere devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakmak isteyebilirsiniz. Artık gerekli değilse, Azure Stream Analytics işini silen kaynak grubunu silin. Azure CLı veya Azure PowerShell kullanarak kaynak grubunu silmek için:

### <a name="azure-cli"></a>Azure CLI’si

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir ARM şablonu kullanarak bir Azure Stream Analytics işi oluşturdunuz ve dağıtımı doğruladı. VS Code kullanarak mevcut bir işin ARM şablonunu dışarı aktarmayı öğrenmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure Stream Analytics işi ARM şablonunu dışarı aktarma](resource-manager-export.md)
