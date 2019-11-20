---
title: Azure Işlevleri 'nde işlevleri devre dışı bırakma
description: Azure Işlevleri 1. x ve 2. x içindeki işlevleri devre dışı bırakmayı ve etkinleştirmeyi öğrenin.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: 498bb8c0f1e7bb674605d4a98f0be0f3e0b9a7c9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650488"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Azure Işlevleri 'nde işlevleri devre dışı bırakma

Bu makalede, Azure Işlevlerinde bir işlevin nasıl devre dışı bırakılacağı açıklanır. Bir işlevi *devre dışı bırakmak* , çalışma zamanının işlev için tanımlanan otomatik tetikleyiciyi yoksayması anlamına gelir. Bunu yapmanız, çalışma zamanı sürümüne ve programlama diline bağlıdır:

* İşlevler 2. x:
  * Tüm diller için bir yol
  * Sınıf kitaplıkları için C# isteğe bağlı yol
* 1\. x işlevleri:
  * Komut dosyası dilleri
  * C#sınıf kitaplıkları

## <a name="functions-2x---all-languages"></a>İşlevler 2. x-tüm diller

2\. x fonksiyonlarının biçimindeki `AzureWebJobs.<FUNCTION_NAME>.Disabled`bir uygulama ayarı kullanarak bir işlevi devre dışı bırakabilirsiniz. Bu uygulama ayarını [Azure CLI](/cli/azure/) kullanarak ve işlevinizin [Azure Portal](https://portal.azure.com)içindeki **Yönet** sekmesinden bir dizi şekilde oluşturabilir ve değiştirebilirsiniz. 

### <a name="azure-cli"></a>Azure CLI

Azure CLI 'da, uygulama ayarını oluşturmak ve [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) değiştirmek için komutunu kullanın. Aşağıdaki komut, adlı bir işlevi `QueueTrigger` olarak `true`ayarla adlı `AzureWebJobs.QueueTrigger.Disabled` bir uygulama ayarı oluşturarak devre dışı bırakır. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

İşlevi yeniden etkinleştirmek için, bir değeriyle `false`aynı komutu yeniden çalıştırın.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portal

İşlevin **Yönet** sekmesinde **işlev durumu** anahtarını da kullanabilirsiniz. Anahtar, `AzureWebJobs.<FUNCTION_NAME>.Disabled` uygulama ayarı oluşturup silerek işe yarar.

![İşlev durum anahtarı](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>İşlevler 2. x C# sınıfı kitaplıklar

Işlevler 2. x sınıf kitaplığında, tüm diller için çalışır olan yöntemi kullanmanızı öneririz. Ancak isterseniz, [Disable özniteliğini 1. x işlevleri içinde kullanabilirsiniz](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>İşlevler 1. x-betik dilleri

C# Betik ve JavaScript gibi komut dosyası dilleri için, çalışma zamanına bir `disabled` işlev tetikleyemediğinden, *function. JSON* dosyasının özelliğini kullanırsınız. Bu özellik, bir uygulama ayarının `true` adına veya adına ayarlanabilir:

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

İkinci örnekte, IS_DISABLED adlı bir uygulama ayarı olduğunda ve veya 1 olarak `true` ayarlandığında işlev devre dışıdır.

Dosyayı Azure portal düzenleyebilir veya işlevin **Yönet** sekmesindeki **işlev durumu** anahtarını kullanabilirsiniz. Portal anahtarı, *function. JSON* dosyası değiştirilerek çalışır.

![İşlev durum anahtarı](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>İşlevler 1. x- C# sınıf kitaplıkları

İşlevler 1. x sınıf kitaplığında, bir işlevin tetiklenmesi için `Disable` bir öznitelik kullanırsınız. Aşağıdaki örnekte gösterildiği gibi, özniteliğini bir oluşturucu parametresi olmadan kullanabilirsiniz:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Oluşturucu parametresi olmayan özniteliği, işlevin devre dışı durumunu değiştirmek için projeyi yeniden derlemenize ve yeniden dağıtmanıza gerek duyar. Bu özniteliği kullanmanın daha esnek bir yolu, aşağıdaki örnekte gösterildiği gibi bir Boole uygulama ayarına başvuran bir oluşturucu parametresi dahil maktır:

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

> [!IMPORTANT]
> `Disabled` Özniteliği, bir sınıf kitaplığı işlevini devre dışı bırakmak için tek yoldur. Bir sınıf kitaplığı işlevi için oluşturulan *function. JSON* dosyası doğrudan düzenlenmemelidir. Bu dosyayı düzenlerseniz, `disabled` özelliği üzerinde yaptığınız her şey hiçbir etkiye sahip olmaz.
>
> , *Function. JSON* dosyasını değiştirerek çalıştığından, **Yönetim** sekmesindeki **işlev durumu** anahtarı için de aynı olur.
>
> Ayrıca, portalda işlevin devre dışı bırakıldığını belirtebileceğini unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makale, otomatik Tetikleyicileri devre dışı bırakmaya yönelik bir. Tetikleyiciler hakkında daha fazla bilgi için bkz. [Tetikleyiciler ve bağlamalar](functions-triggers-bindings.md).
