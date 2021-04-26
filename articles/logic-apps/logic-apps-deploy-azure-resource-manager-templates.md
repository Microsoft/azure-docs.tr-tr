---
title: Mantıksal uygulama şablonları dağıtma
description: Azure Logic Apps için oluşturulan Azure Resource Manager şablonlarını dağıtmayı öğrenin
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5f5db3fd88f04e7fe569cd675936445dcf730288
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705345"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Azure Logic Apps için Azure Resource Manager şablonu oluşturma

Mantıksal uygulamanız için bir Azure Resource Manager şablonu oluşturduktan sonra, şablonunuzu şu yollarla dağıtabilirsiniz:

* [Azure portalı](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API’leri](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Azure portal üzerinden dağıtma

Mantıksal uygulama şablonunu Azure 'a otomatik olarak dağıtmak için, aşağıdaki **Azure 'A dağıt** düğmesini seçebilirsiniz ve bu, Azure Portal oturumunuzu açar ve mantıksal uygulamanız hakkında bilgi ister. Daha sonra mantıksal uygulama şablonunda veya parametrelerde gerekli değişiklikleri yapabilirsiniz.

[![Azure’a dağıtın](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Örneğin, Azure portal oturum açtıktan sonra aşağıdaki bilgiler istenir:

* Azure abonelik adı
* Kullanmak istediğiniz kaynak grubu
* Mantıksal uygulama konumu
* Mantıksal uygulamanızın adı
* Test URI 'SI
* Belirtilen hüküm ve koşulları kabul etme

Daha fazla bilgi için şu konulara bakın:

* [Genel Bakış: Azure Resource Manager şablonlarıyla Logic Apps için dağıtımı otomatikleştirin](logic-apps-azure-resource-manager-templates-overview.md)
* [Kaynakları Azure Resource Manager şablonlarıyla ve Azure portal dağıtma](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Visual Studio ile dağıtma

Visual Studio kullanarak oluşturduğunuz bir Azure Kaynak grubu projesinden bir Logic App şablonu dağıtmak için, [mantıksal uygulamanızı Azure 'a el ile dağıtmak üzere aşağıdaki adımları](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) izleyin.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Azure PowerShell ile dağıtma

Belirli bir *Azure Kaynak grubuna* dağıtmak için aşağıdaki komutu kullanın:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Daha fazla bilgi için şu konulara bakın:

* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Azure CLI ile dağıtma

Belirli bir *Azure Kaynak grubuna* dağıtmak için aşağıdaki komutu kullanın:

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Daha fazla bilgi için şu konulara bakın:

* [Kaynakları Resource Manager şablonları ve Azure CLI ile dağıtma](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Azure DevOps ile dağıtma

Mantıksal uygulama şablonları dağıtmak ve ortamları yönetmek için, ekipler genellikle [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services)'da [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) gibi bir araç kullanır. Azure Pipelines, herhangi bir derleme veya yayın ardışık düzenine ekleyebileceğiniz bir [Azure Kaynak grubu dağıtım görevi](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) sağlar. Yayın işlem hattını dağıtma ve oluşturma yetkilendirmesi için Azure Active Directory (AD) [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md)da gerekir. [Azure Pipelines ile hizmet sorumlularını kullanma](/azure/devops/pipelines/library/connect-to-azure)hakkında daha fazla bilgi edinin.

Azure Pipelines olan Azure Resource Manager şablonlar için sürekli tümleştirme ve sürekli dağıtım (CI/CD) hakkında daha fazla bilgi için şu konulara ve örneklere bakın:

* [Kaynak Yöneticisi şablonlarını Azure Pipelines ile tümleştirme](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Öğretici: Azure Pipelines Azure Resource Manager şablonlarının sürekli tümleştirilmesi](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Örnek: Azure DevOps 'da Azure Logic Apps Azure Service Bus kuyruklara bağlanma ve Azure Pipelines ile dağıtma](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps 'den Azure Storage hesaplarına bağlanma ve Azure DevOps 'da Azure Pipelines ile dağıtma](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps için bir işlev uygulama eylemi ayarlama ve Azure DevOps 'da Azure Pipelines ile dağıtma](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps 'ten bir tümleştirme hesabına bağlanın ve Azure DevOps 'da Azure Pipelines ile dağıtın](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps kullanarak Azure Pipelines düzenleme](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Azure Pipelines kullanmaya yönelik genel üst düzey adımlar aşağıda verilmiştir:

1. Azure Pipelines ' de boş bir işlem hattı oluşturun.

1. İşlem hattı için ihtiyaç duyduğunuz kaynakları, örneğin, el ile veya yapı sürecinin bir parçası olarak oluşturduğunuz mantıksal uygulama şablonu ve şablon parametreleri dosyaları gibi seçin.

1. Aracı işiniz için, **Azure Kaynak grubu dağıtım** görevini bulun ve ekleyin.

   !["Azure Kaynak grubu dağıtımı" görevi Ekle](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. [Hizmet sorumlusu](/azure/devops/pipelines/library/connect-to-azure)ile yapılandırma.

1. Mantıksal uygulama şablonunuz ve şablon parametreleri dosyalarınıza başvurular ekleyin.

1. Gerektiğinde başka herhangi bir ortam, otomatik test veya onaylayan için yayın işlemindeki adımları oluşturmaya devam edin.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>OAuth bağlantılarını yetkilendir

Dağıtımdan sonra mantıksal uygulamanız geçerli parametrelerle uçtan uca çalışabilir, ancak [kimlik bilgilerinizi](../active-directory/develop/authentication-vs-authorization.md)doğrulamaya yönelik geçerli erişim belirteçleri oluşturmak için, önceden yetkilendirme yetkisi olan OAuth bağlantıları yetkilendirmeniz veya kullanmanız gerekir. Ancak, API bağlantısı kaynaklarını yalnızca bir kez dağıtmanız ve kimlik doğrulaması yapmanız yeterlidir, yani bağlantı bilgilerini güncelleştirmeniz gerekmediği takdirde bu bağlantı kaynaklarını sonraki dağıtımlarda eklemeniz gerekmez. Sürekli tümleştirme ve sürekli dağıtım işlem hattı kullanıyorsanız, yalnızca güncelleştirilmiş Logic Apps kaynakları dağıtır ve her seferinde bağlantıları yeniden Yetkilendir.

Yetkilendirme bağlantılarını işlemek için birkaç öneri aşağıda verilmiştir:

* Ön kimlik doğrulama, API bağlantısı kaynaklarını aynı bölgedeki Logic Apps genelinde Yetkilendir ve paylaşır. API bağlantıları, Logic Apps 'ten bağımsız olarak Azure kaynakları olarak mevcuttur. Mantıksal uygulamalar API bağlantı kaynakları üzerinde bağımlılıklara sahip olsa da, API bağlantı kaynakları Logic Apps üzerinde bağımlılıklara sahip değildir ve bağımlı Logic Apps 'i sildikten sonra kalır. Ayrıca Logic Apps, diğer kaynak gruplarında bulunan API bağlantılarını kullanabilir. Ancak, Logic App Designer, yalnızca mantıksal uygulamalarınızla aynı kaynak grubunda API bağlantıları oluşturulmasını destekler.

  > [!NOTE]
  > API bağlantılarını paylaşmayı düşünüyorsanız, çözümünüzün [olası azaltma sorunlarını işleyebileceği](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling)konusunda emin olun. Kısıtlama, bağlantı düzeyinde gerçekleşir, bu nedenle birden çok Logic Apps genelinde aynı bağlantının yeniden kullanılabilmesi, azaltma sorunlarını ortadan kaldırmak için potansiyelini artırabilir.

* Senaryonuz, çok faktörlü kimlik doğrulaması gerektiren hizmetler ve sistemler içermiyorsa, bir PowerShell betiği kullanarak her bir OAuth bağlantısı için, etkin tarayıcı oturumlarına sahip olan ve önceden sağlanmış olan bir sanal makinede normal bir kullanıcı hesabı olarak çalışır. Örneğin, [Logic Apps GitHub deposunda Logicappconnectionauth projesi](https://github.com/logicappsio/LogicAppConnectionAuth)tarafından verilen örnek betiği yeniden amaçlandırın sağlayabilirsiniz.

* Mantıksal uygulamanızı mantıksal uygulama tasarımcısında Azure portal veya Visual Studio 'da açarak, OAuth bağlantılarını el ile yetkilendirin.

* Bağlantıları yetkilendirmek için bir Azure Active Directory (Azure AD) [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md) kullanıyorsanız, [mantıksal uygulama şablonunuzda hizmet sorumlusu parametrelerini belirtmeyi](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Mantıksal uygulamaları izleme](../logic-apps/monitor-logic-apps.md)
