---
title: Dağıtım geçmişi
description: Portal, PowerShell, Azure CLı ve REST API Azure Resource Manager dağıtım işlemlerinin nasıl görüntüleneceğini açıklar.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e7ed2096a696efdc9a2654a8fd0c294c82cbd4f7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781874"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Azure Resource Manager ile dağıtım geçmişini görüntüle

Azure Resource Manager, dağıtım geçmişinizi görüntülemenize olanak sağlar. Önceki dağıtımlarda gerçekleştirilen işlemleri inceleyerek dağıtılan kaynakları görebilirsiniz. Bu geçmiş hatalar hakkında bilgi içerir.

Bir kaynak grubunun dağıtım geçmişi 800 dağıtım ile sınırlıdır. Sınıra yaklaştıklarında, dağıtımlar geçmişten otomatik olarak silinir. Daha fazla bilgi için bkz. [dağıtım geçmişinden otomatik silme işlemleri](deployment-history-deletions.md).

Belirli dağıtım hatalarını çözümlemede yardım için bkz. [Azure 'a kaynak dağıtılırken sık karşılaşılan hataları çözümleme Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Dağıtımları ve bağıntı KIMLIĞINI al

Azure portal, PowerShell, Azure CLı veya REST API bir dağıtım hakkındaki ayrıntıları görüntüleyebilirsiniz. Her dağıtımda ilgili olayları izlemek için kullanılan bir bağıntı KIMLIĞI vardır. [Bir Azure destek isteği oluşturursanız](../../azure-portal/supportability/how-to-create-azure-support-request.md), destek sızden bağıntı kimliği isteyebilir. Destek, başarısız dağıtıma yönelik işlemleri tanımlamak için bağıntı KIMLIĞINI kullanır.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. İncelemek istediğiniz kaynak grubunu seçin.

1. **Dağıtımlar** altındaki bağlantıyı seçin.

   ![Dağıtım geçmişini seçin](./media/deployment-history/select-deployment-history.png)

1. Dağıtım geçmişinden dağıtımlardan birini seçin.

   ![Dağıtım seçin](./media/deployment-history/select-details.png)

1. Bağıntı KIMLIĞI de dahil olmak üzere dağıtımın bir özeti görüntülenir.

    ![Dağıtım özeti](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bir kaynak grubunun tüm dağıtımlarını listelemek için [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) komutunu kullanın.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Bir kaynak grubundan belirli bir dağıtım almak için **DeploymentName** parametresini ekleyin.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Bağıntı KIMLIĞINI almak için şunu kullanın:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bir kaynak grubunun dağıtımını listelemek için [az Deployment Group List](/cli/azure/group/deployment#az_deployment_group_list)kullanın.

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Belirli bir dağıtımı almak için [az Deployment Group Show](/cli/azure/group/deployment#az_deployment_group_show)' ı kullanın.

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Bağıntı KIMLIĞINI almak için şunu kullanın:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Bir kaynak grubunun dağıtımlarını listelemek için aşağıdaki işlemi kullanın. İstekte kullanılacak en son API sürüm numarası için bkz.  [kaynak grubuna göre dağıtımlar-Listele](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Belirli bir dağıtımı almak için. aşağıdaki işlemi kullanın. İstekte kullanılacak en son API sürüm numarası için bkz. [dağıtımlar-al](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Yanıt bağıntı KIMLIĞINI içerir.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Dağıtım işlemlerini ve hata iletisini Al

Her dağıtım birden çok işlem içerebilir. Bir dağıtım hakkında daha fazla ayrıntı görmek için dağıtım işlemlerini görüntüleyin. Dağıtım başarısız olduğunda, dağıtım işlemleri bir hata iletisi içerir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Bir dağıtımın özetinde **işlem ayrıntıları**' nı seçin.

    ![İşlem ayrıntılarını Seç](./media/deployment-history/get-operation-details.png)

1. Dağıtım adımının ayrıntılarını görürsünüz. Bir hata oluştuğunda, Ayrıntılar hata mesajını içerir.

    ![İşlem ayrıntılarını göster](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bir kaynak grubuna dağıtıma yönelik dağıtım işlemlerini görüntülemek için [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) komutunu kullanın.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Başarısız işlemleri görüntülemek için, **başarısız** durumundaki işlemleri filtreleyin.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Başarısız işlemlerin durum iletisini almak için aşağıdaki komutu kullanın:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bir kaynak grubuna dağıtıma yönelik dağıtım işlemlerini görüntülemek için [az Deployment Operation Group List](/cli/azure/deployment/operation/group#az_deployment-operation-group-list) komutunu kullanın. Azure CLı 2.6.0 veya sonraki bir sürümü olmalıdır.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Başarısız işlemleri görüntülemek için, **başarısız** durumundaki işlemleri filtreleyin.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Başarısız işlemlerin durum iletisini almak için aşağıdaki komutu kullanın:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Dağıtım işlemlerini almak için aşağıdaki işlemi kullanın. İstekte kullanılacak en son API sürüm numarası için bkz. [dağıtım işlemleri-listesi](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Yanıt bir hata iletisi içerir.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Sonraki adımlar

* Belirli dağıtım hatalarını çözümlemede yardım için bkz. [Azure 'a kaynak dağıtılırken sık karşılaşılan hataları çözümleme Azure Resource Manager](common-deployment-errors.md).
* Dağıtımların geçmişte nasıl yönetildiği hakkında bilgi edinmek için bkz. [dağıtım geçmişinden otomatik silme işlemleri](deployment-history-deletions.md).
* Yürütmeden önce dağıtımınızı doğrulamak için, bkz. [Azure Resource Manager şablonuyla kaynak grubu dağıtma](deploy-powershell.md).
