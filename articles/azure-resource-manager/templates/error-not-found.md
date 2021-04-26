---
title: Kaynak bulunamadı hataları
description: Bir kaynak bulunamadığında hataların nasıl çözümleneceğini açıklar. Bir Azure Resource Manager şablonu dağıtıldığında veya yönetim eylemleri yapılırken hata oluşabilir.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: 5e3a72eaad99721cec9500956179a3ae9d9cf8d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762146"
---
# <a name="resolve-resource-not-found-errors"></a>Kaynak bulunamadı hatalarını çözümle

Bu makalede bir işlem sırasında bir kaynak bulunamadığında gördüğünüz hata açıklanmaktadır. Genellikle, kaynakları dağıttığınızda bu hatayı görürsünüz. Ayrıca, yönetim görevleri gerçekleştirirken ve Azure Resource Manager gerekli kaynağı bulamadığınızda bu hatayı da görürsünüz. Örneğin, mevcut olmayan bir kaynağa Etiketler eklemeye çalışırsanız, bu hatayı alırsınız.

## <a name="symptom"></a>Belirti

Kaynağın bulunamadığını belirten iki hata kodu vardır. **NotFound** hatası şuna benzer bir sonuç döndürüyor:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

**Resourcenotfound** hatası şuna benzer bir sonuç döndürüyor:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Nedeni

Kaynak Yöneticisi bir kaynağın özelliklerini alması gerekir, ancak aboneliklerinizde kaynağı bulamaz.

## <a name="solution-1---check-resource-properties"></a>Çözüm 1-kaynak özelliklerini denetleme

Bir yönetim görevi gerçekleştirirken bu hatayı aldığınızda, kaynak için sağladığınız değerleri kontrol edin. Denetlenecek üç değer şunlardır:

* Kaynak adı
* Kaynak grubu adı
* Abonelik

PowerShell veya Azure CLı kullanıyorsanız, komutunu kaynağı içeren abonelikte çalıştırıp çalıştırmadığını kontrol edin. Aboneliği [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) veya [az Account set](/cli/azure/account#az_account_set)ile değiştirebilirsiniz. Birçok komut, geçerli bağlamdan farklı bir abonelik belirtmenize imkan tanıyan bir abonelik parametresi de sağlar.

Özellikleri doğrularken sorun yaşıyorsanız [portalda](https://portal.azure.com)oturum açın. Kullanmaya çalıştığınız kaynağı bulun ve kaynak adı, kaynak grubu ve aboneliği inceleyin.

## <a name="solution-2---set-dependencies"></a>Çözüm 2-bağımlılıkları ayarlama

Bir şablonu dağıttığınızda bu hatayı alırsanız, bir bağımlılık eklemeniz gerekebilir. Kaynak Yöneticisi, mümkün olduğunda, paralel olarak kaynakları oluşturarak dağıtımı iyileştirir. Bir kaynak başka bir kaynaktan sonra dağıtılması gerekiyorsa, şablonunuzda **Bağımlıdson** öğesini kullanmanız gerekir. Örneğin, bir Web uygulaması dağıtıldığında App Service planının mevcut olması gerekir. Web uygulamasının App Service planına bağlı olduğunu belirtmediyse Kaynak Yöneticisi her iki kaynak de aynı anda oluşturulur. Yalnızca Web uygulamasında bir özellik ayarlamaya çalışırken mevcut olmadığından App Service planı kaynağının bulunamadığını belirten bir hata alırsınız. Web uygulamasındaki bağımlılığı ayarlayarak bu hatayı önleyebilirsiniz.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Ancak, gerekmeyen bağımlılıkları ayarlamayı önlemek isteyebilirsiniz. Gereksiz bağımlılıklara sahip olduğunuzda, birbirlerine bağımlı olmayan kaynakların paralel olarak dağıtılmasını önleyecek şekilde dağıtım süresini uzattınız. Ayrıca, dağıtımı engelleyen döngüsel bağımlılıklar da oluşturabilirsiniz. [Başvuru](template-functions-resource.md#reference) işlevi ve [liste *](template-functions-resource.md#list) işlevleri, başvurulan kaynak üzerinde bu kaynak aynı şablonda dağıtıldığında ve adına (kaynak kimliği değil) başvurduğu zaman bir örtük bağımlılık oluşturur. Bu nedenle, **Bağımlıdson** özelliğinde belirtilen bağımlılıklardan daha fazla bağımlılığı olabilir. [RESOURCEID](template-functions-resource.md#resourceid) işlevi örtük bir bağımlılık oluşturmaz veya kaynağın mevcut olduğunu doğrular. [Başvuru](template-functions-resource.md#reference) işlevi ve [liste *](template-functions-resource.md#list) IŞLEVLERI, kaynağa kaynak kimliği tarafından bahsedildiğinde örtülü bir bağımlılık oluşturmaz. Örtük bir bağımlılık oluşturmak için, aynı şablonda dağıtılan kaynağın adını geçirin.

Bağımlılık sorunlarını gördüğünüzde, kaynak dağıtımının sırası hakkında öngörü elde etmeniz gerekir. Dağıtım işlemlerinin sırasını görüntülemek için:

1. Kaynak grubunuz için dağıtım geçmişini seçin.

   ![Dağıtım geçmişini seçin](./media/error-not-found/select-deployment.png)

2. Geçmişten bir dağıtım seçin ve **Olaylar**' ı seçin.

   ![Dağıtım olaylarını seçin](./media/error-not-found/select-deployment-events.png)

3. Her kaynak için olay sırasını inceleyin. Her işlemin durumuna dikkat edin. Örneğin, aşağıdaki görüntüde paralel olarak dağıtılan üç depolama hesabı gösterilmektedir. Üç depolama hesabının aynı anda başlatıldığını unutmayın.

   ![paralel dağıtım](./media/error-not-found/deployment-events-parallel.png)

   Sonraki görüntüde paralel olarak dağıtılmayan üç depolama hesabı gösterilmektedir. İkinci depolama hesabı, ilk depolama hesabına bağlıdır ve üçüncü depolama hesabı ikinci depolama hesabına bağlıdır. İlk depolama hesabı, bir sonraki başlatılmadan önce başlatılır, kabul edilir ve tamamlanır.

   ![sıralı dağıtım](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>Çözüm 3-dış kaynak al

Bir şablonu dağıtmakta ve farklı bir abonelikte veya kaynak grubunda bulunan bir kaynağı almanız gerektiğinde [RESOURCEID işlevini](template-functions-resource.md#resourceid)kullanın. Bu işlev, kaynağın tam adını almak için döndürür.

RESOURCEID işlevindeki abonelik ve kaynak grubu parametreleri isteğe bağlıdır. Bunları sağlamazsanız, bunlar varsayılan olarak geçerli abonelik ve kaynak grubuna göre yapılır. Farklı bir kaynak grubunda veya abonelikte bulunan bir kaynakla çalışırken, bu değerleri sağladığınızdan emin olun.

Aşağıdaki örnek, farklı bir kaynak grubunda bulunan bir kaynağın kaynak KIMLIĞINI alır.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Çözüm 4-kaynaktan yönetilen kimliği al

[Yönetilen kimliği](../../active-directory/managed-identities-azure-resources/overview.md)dolaylı olarak oluşturan bir kaynak dağıtıyorsanız, yönetilen kimlik değerlerini almadan önce bu kaynak dağıtılana kadar beklemeniz gerekir. Yönetilen kimlik adını [başvuru](template-functions-resource.md#reference) işlevine geçirirseniz, kaynak ve kimlik dağıtılmadan önce başvuruyu çözümlemeye çalışır Kaynak Yöneticisi. Bunun yerine, kimliğin uygulandığı kaynağın adını geçirin. Bu yaklaşım, başvuru işlevini çözümlediği Kaynak Yöneticisi önce kaynak ve yönetilen kimliğin dağıtılmasını sağlar.

Başvuru işlevinde, `Full` yönetilen kimlik dahil tüm özellikleri almak için kullanın.

Bu model:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Bu kalıbı kullanmayın:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> Şablonunuz başarısız olur.

Örneğin, bir sanal makineye uygulanan bir yönetilen kimliğin asıl KIMLIĞINI almak için şunu kullanın:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Ya da bir sanal makine ölçek kümesine uygulanan bir yönetilen kimliğin kiracı KIMLIĞINI almak için şunu kullanın:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Çözüm 5-işlevleri denetle

Bir şablonu dağıttığınızda, [başvuru](template-functions-resource.md#reference) veya [ListKeys](template-functions-resource.md#listkeys) işlevlerini kullanan ifadeler ' i arayın. Sağladığınız değerler, kaynağın aynı şablonda, kaynak grubunda ve abonelikte olup olmadığına göre farklılık gösterir. Senaryonuz için gerekli parametre değerlerini sağladığınızdan emin olun. Kaynak farklı bir kaynak grubsunda, tam kaynak KIMLIĞI sağlayın. Örneğin, başka bir kaynak grubundaki bir depolama hesabına başvurmak için şunu kullanın:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>Çözüm 6-kaynak silindikten sonra

Bir kaynağı sildiğinizde, kaynağın portalda hala devam ettiği ancak gerçekten kullanılamadığı kısa bir süre olabilir. Kaynağı seçerseniz, kaynağın bulunamadığını belirten bir hata alırsınız. En son görünümü almak için portalı yenileyin.

Sorun kısa bir bekleme sonrasında devam ederse [desteğe başvurun](https://azure.microsoft.com/support/options/).
