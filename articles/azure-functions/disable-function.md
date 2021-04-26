---
title: Azure Işlevleri 'nde işlevleri devre dışı bırakma
description: Azure Işlevleri 'nde işlevleri devre dışı bırakmayı ve etkinleştirmeyi öğrenin.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: c4743603504639cba5c48af57046179a0680b371
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829889"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Azure Işlevleri 'nde işlevleri devre dışı bırakma

Bu makalede, Azure Işlevlerinde bir işlevin nasıl devre dışı bırakılacağı açıklanır. Bir işlevi *devre dışı bırakmak* , çalışma zamanının işlev için tanımlanan otomatik tetikleyiciyi yoksayması anlamına gelir. Bu, belirli bir işlevin işlev uygulamasının tamamını durdurmadan çalışmasını engellemenizi sağlar.

Bir işlevi devre dışı bırakmak için önerilen yol, olarak ayarlanan biçimdeki bir uygulama ayarıdır `AzureWebJobs.<FUNCTION_NAME>.Disabled` `true` . Bu uygulama ayarını [Azure CLI](/cli/azure/) kullanarak ve işlevinizin [Azure Portal](https://portal.azure.com) **genel bakış** sekmesinde bir dizi şekilde oluşturabilir ve değiştirebilirsiniz. 

> [!NOTE]  
> HTTP ile tetiklenen bir işlevi bu makalede açıklanan yöntemleri kullanarak devre dışı bıraktığınızda, yerel bilgisayarınızda çalışırken uç nokta hala erişilebilir olabilir.  

## <a name="disable-a-function"></a>İşlevi devre dışı bırakma

# <a name="portal"></a>[Portal](#tab/portal)

İşlevin **genel bakış** sayfasında **Etkinleştir** ve **devre dışı bırak** düğmelerini kullanın. Bu düğmeler, uygulama ayarının değeri değiştirilerek çalışır `AzureWebJobs.<FUNCTION_NAME>.Disabled` . Bu işleve özgü ayar, ilk kez devre dışı bırakıldığında oluşturulur. 

![İşlev durum anahtarı](media/disable-function/function-state-switch.png)

İşlev uygulamanızda yerel bir projeden yayımladığınızda bile, işlev uygulamasındaki işlevleri devre dışı bırakmak için portalını kullanmaya devam edebilirsiniz. 

> [!NOTE]  
> Portalın tümleşik test işlevselliği `Disabled` ayarı yoksayar. Bu, devre dışı bırakılan bir işlevin portalda **Test** penceresinden başlatıldığında hala çalıştığı anlamına gelir. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Azure CLı 'da, [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) uygulama ayarını oluşturmak ve değiştirmek için komutunu kullanın. Aşağıdaki komut adında bir `QueueTrigger` uygulama ayarı oluşturarak `AzureWebJobs.QueueTrigger.Disabled` ve olarak ayarlanarak adlı bir işlevi devre dışı bırakır `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

İşlevi yeniden etkinleştirmek için, bir değeriyle aynı komutu yeniden çalıştırın `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting)Komut bir uygulama ayarı ekler veya güncelleştirir. Aşağıdaki komut adında bir `QueueTrigger` uygulama ayarı oluşturarak `AzureWebJobs.QueueTrigger.Disabled` ve olarak ayarlanarak adlı bir işlevi devre dışı bırakır `true` . 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

İşlevi yeniden etkinleştirmek için, bir değeriyle aynı komutu yeniden çalıştırın `false` .

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Bir yuvadaki işlevler

Varsayılan olarak, uygulama ayarları dağıtım yuvalarında çalışan uygulamalar için de geçerlidir. Ancak, yuvaya özgü bir uygulama ayarı ayarlayarak yuva tarafından kullanılan uygulama ayarını geçersiz kılabilirsiniz. Örneğin, bir işlevin üretimde etkin olmasını, ancak Zamanlayıcı tarafından tetiklenen bir işlev gibi dağıtım testi sırasında değil, bir işlevin olmasını isteyebilirsiniz. 

Yalnızca hazırlama yuvasında bir işlevi devre dışı bırakmak için:

# <a name="portal"></a>[Portal](#tab/portal)

**Dağıtım** altında **dağıtım yuvaları** ' nı seçip yuvalarınızı seçip yuva örneğindeki **işlevleri** seçerek işlev uygulamanızın yuva örneğine gidin.  İşlevinizi seçin, sonra işlevin **genel bakış** sayfasında **Etkinleştir** ve **devre dışı bırak** düğmelerini kullanın. Bu düğmeler, uygulama ayarının değeri değiştirilerek çalışır `AzureWebJobs.<FUNCTION_NAME>.Disabled` . Bu işleve özgü ayar, ilk kez devre dışı bırakıldığında oluşturulur. 

Ayrıca, `AzureWebJobs.<FUNCTION_NAME>.Disabled` `true` yuva örneğinin **yapılandırmasında** değeri ile adlı uygulama ayarını doğrudan ekleyebilirsiniz. Yuvaya özgü bir uygulama ayarı eklediğinizde **dağıtım yuvası ayarı** kutusunu kontrol ettiğinizden emin olun. Böylece ayar değeri, takas sırasında yuva ile korunur.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
İşlevi yeniden etkinleştirmek için, bir değeriyle aynı komutu yeniden çalıştırın `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell Şu anda bu işlevi desteklemiyor.

---

Daha fazla bilgi için bkz. [Azure Işlevleri dağıtım yuvaları](functions-deployment-slots.md).

## <a name="localsettingsjson"></a>local.settings.json

İşlevler yerel olarak çalıştırılırken aynı şekilde devre dışı bırakılabilir. Adlı bir işlevi devre dışı bırakmak için `HttpExample` , dosyadaki local.settings.jsdeğerler koleksiyonuna aşağıdaki gibi bir giriş ekleyin:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Diğer Yöntemler

Uygulama ayarı yöntemi tüm diller ve tüm çalışma zamanı sürümleri için önerilse de, işlevleri devre dışı bırakmak için birkaç farklı yol vardır. Dil ve çalışma zamanı sürümüne göre farklılık gösteren bu yöntemler geriye dönük uyumluluk için korunur. 

### <a name="c-class-libraries"></a>C# sınıf kitaplıkları

Bir sınıf kitaplığı işlevinde, `Disable` işlevin tetiklenmasını engellemek için özniteliğini de kullanabilirsiniz. Bu öznitelik, işlevi devre dışı bırakmak için kullanılan ayarın adını özelleştirmenize olanak sağlar. Aşağıdaki örnekte gösterildiği gibi, bir Boolean uygulama ayarına başvuran bir oluşturucu parametresi tanımlamanıza olanak sağlayan özniteliğin sürümünü kullanın:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Bu yöntem, uygulamayı yeniden derlemeden veya yeniden dağıtmaya gerek kalmadan, uygulama ayarını değiştirerek işlevini etkinleştirmenizi ve devre dışı bırakmanızı sağlar. Bir uygulama ayarının değiştirilmesi, işlev uygulamasının yeniden başlatılmasına neden olur, bu nedenle devre dışı durum değişikliği hemen tanınır.

Ayrıca, ayar adı için bir dize kabul etmeyen parametre için bir Oluşturucu da vardır. Özniteliğin bu sürümü önerilmez. Bu sürümü kullanırsanız, işlevin devre dışı durumunu değiştirmek için projeyi yeniden derlemeniz ve yeniden dağıtmanız gerekir.

### <a name="functions-1x---scripting-languages"></a>İşlevler 1. x-betik dilleri

Sürüm 1. x ' de, `disabled` çalışma zamanının bir işlevi tetikleyemamasını söylemek için *function.js* dosyasındaki özelliğini de kullanabilirsiniz. Bu yöntem yalnızca C# betiği ve JavaScript gibi komut dosyası dilleri için geçerlidir. `disabled`Özelliği, `true` bir uygulama ayarının adına veya adına ayarlanabilir:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
veya 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

İkinci örnekte, IS_DISABLED adlı ve veya 1 olarak ayarlanmış bir uygulama ayarı olduğunda işlev devre dışıdır `true` .

>[!IMPORTANT]  
>Portal, v1. x işlevlerini devre dışı bırakmak için uygulama ayarlarını kullanır. Bir uygulama ayarı dosyadaki function.jsile çakışıyorsa bir hata oluşabilir. `disabled`Hataları engellemek için dosyadaki function.jsözelliğini kaldırmalısınız. 


## <a name="next-steps"></a>Sonraki adımlar

Bu makale, otomatik Tetikleyicileri devre dışı bırakmaya yönelik bir. Tetikleyiciler hakkında daha fazla bilgi için bkz. [Tetikleyiciler ve bağlamalar](functions-triggers-bindings.md).
