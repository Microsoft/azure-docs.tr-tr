---
title: Dağıtım geçmişi
description: Portal, PowerShell, Azure CLı ve REST API Azure Resource Manager dağıtım işlemlerinin nasıl görüntüleneceğini açıklar.
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: d8daf7191bb22f7c7057f6ef6b220a18868872cc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149576"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Azure Resource Manager ile dağıtım geçmişini görüntüle

Azure Resource Manager, dağıtım geçmişinizi görüntülemenize ve geçmiş dağıtımlarda belirli işlemleri incelemenize olanak sağlar. Dağıtılan kaynakları görebilir ve hatalar hakkında bilgi alabilirsiniz.

Belirli dağıtım hatalarını çözümlemede yardım için bkz. [Azure 'a kaynak dağıtılırken sık karşılaşılan hataları çözümleme Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portal

Dağıtım geçmişinden bir dağıtım hakkındaki ayrıntıları almak için.

1. İncelemek istediğiniz kaynak grubunu seçin.

1. **Dağıtımlar**altındaki bağlantıyı seçin.

   ![Dağıtım geçmişini seçin](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Dağıtım geçmişinden dağıtımlardan birini seçin.

   ![Dağıtım seçin](./media/resource-manager-deployment-operations/select-details.png)

1. Dağıtılan kaynakların bir listesi de dahil olmak üzere dağıtımın bir özeti görüntülenir.

    ![Dağıtım Özeti](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Dağıtım için kullanılan şablonu görüntülemek için **şablon**' u seçin. Şablonu yeniden kullanmak için indirebilirsiniz.

    ![Şablonu göster](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Dağıtımınız başarısız olursa, bir hata iletisi görürsünüz. Daha fazla ayrıntı için hata iletisini seçin.

    ![Başarısız dağıtımı görüntüle](./media/resource-manager-deployment-operations/show-error.png)

1. Ayrıntılı hata iletisi görüntülenir.

    ![Hata ayrıntılarını görüntüle](./media/resource-manager-deployment-operations/show-details.png)

1. Bağıntı KIMLIĞI ilgili olayları izlemek için kullanılır ve bir dağıtımda sorun gidermeye yönelik teknik destekle çalışırken yararlı olabilir.

    ![Bağıntı KIMLIĞI al](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Başarısız olan adım hakkında daha fazla bilgi edinmek için **işlem ayrıntıları**' nı seçin.

    ![Dağıtım işlemlerini seçin](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Dağıtım adımının ayrıntılarını görürsünüz.

    ![İşlem ayrıntılarını göster](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bir dağıtımın genel durumunu almak için **Get-AzResourceGroupDeployment** komutunu kullanın.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Ya da sonuçları yalnızca başarısız olan dağıtımlar için filtreleyebilirsiniz.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

Bağıntı KIMLIĞI ilgili olayları izlemek için kullanılır ve bir dağıtımda sorun gidermeye yönelik teknik destekle çalışırken yararlı olabilir. Bağıntı KIMLIĞINI almak için şunu kullanın:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Her dağıtım birden çok işlem içerir. Her işlem, dağıtım işlemindeki bir adımı temsil eder. Dağıtım ile ilgili ne olduğunu öğrenmek için genellikle dağıtım işlemleriyle ilgili ayrıntıları görmeniz gerekir. **Get-AzResourceGroupDeploymentOperation**ile işlemlerin durumunu görebilirsiniz.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Her biri aşağıdaki biçimde olan birden çok işlem döndürür:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Başarısız işlemler hakkında daha fazla ayrıntı edinmek için **başarısız** durumundaki işlemler için özellikleri alın.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Her biri aşağıdaki biçimde olan tüm başarısız işlemleri döndürür:

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

İşlem için serviceRequestId ve Trackingıd değerlerini aklınızda edin. ServiceRequestId, bir dağıtımda sorun gidermeye yönelik teknik destekle çalışırken yararlı olabilir. Belirli bir işleme odaklanmak için sonraki adımda Trackingıd ' i kullanacaksınız.

Belirli bir başarısız işlemin durum iletisini almak için aşağıdaki komutu kullanın:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Şunu döndürür:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Azure 'daki her dağıtım işlemi istek ve yanıt içeriğini içerir. Dağıtım sırasında, istek ve/veya yanıtın günlüğe kaydedileceğini belirtmek için **Deploymentdebugloglevel** parametresini kullanabilirsiniz.

Bu bilgileri günlüğünden alır ve aşağıdaki PowerShell komutlarını kullanarak yerel olarak kaydedin:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

Bir dağıtımın genel durumunu almak için **Azure grup dağıtımı göster** komutunu kullanın.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
Bağıntı KIMLIĞI ilgili olayları izlemek için kullanılır ve bir dağıtımda sorun gidermeye yönelik teknik destekle çalışırken yararlı olabilir.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Bir dağıtımın işlemlerini görmek için şunu kullanın:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Bir dağıtım hakkında bilgi almak için, [bir şablon dağıtımı hakkında bilgi al](https://docs.microsoft.com/rest/api/resources/deployments) işlemini kullanın.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Yanıtta, özellikle **Provisioningstate**, **bağıntıkimliği**ve **Error** öğelerine göz önünde. **CorrelationId** , ilgili olayları izlemek için kullanılır ve bir dağıtımda sorun gidermeye yönelik teknik destekle çalışırken yararlı olabilir.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

Dağıtımlar hakkında bilgi almak için, [tüm şablon dağıtım Işlemlerini Listele](https://docs.microsoft.com/rest/api/resources/deployments)seçeneğini kullanın. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
Yanıt, dağıtım sırasında **Debugsetting** özelliğinde belirtdiklerinizi temel alarak istek ve/veya yanıt bilgilerini içerir.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>Sonraki adımlar
* Belirli dağıtım hatalarını çözümlemede yardım için bkz. [Azure 'a kaynak dağıtılırken sık karşılaşılan hataları çözümleme Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Diğer eylem türlerini izlemek için etkinlik günlüklerini kullanma hakkında bilgi edinmek için bkz. [Azure kaynaklarını yönetmek için etkinlik günlüklerini görüntüleme](resource-group-audit.md).
* Yürütmeden önce dağıtımınızı doğrulamak için, bkz. [Azure Resource Manager şablonuyla kaynak grubu dağıtma](resource-group-template-deploy.md).

