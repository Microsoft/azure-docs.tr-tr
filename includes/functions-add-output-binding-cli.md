---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7ef3bd0f401ba54d56ed42df34cd2e761681dbc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904082"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-an-output-binding-definition-to-the-function"></a>İşleve çıkış bağlama tanımı ekleme

Bir işlev yalnızca bir tetikleyicisine sahip olsa da, özel tümleştirme kodu yazmadan diğer Azure hizmetlerine ve kaynaklarına bağlanmanızı sağlayan birden çok giriş ve çıkış bağlaması olabilir. 
::: zone-end
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Bu bağlamaları, işlev klasörünüzdeki dosyasında *function.js* bildirirsiniz. Önceki hızlı başlangıçta, *Httpexample* klasöründeki dosya *function.js* koleksiyonda iki bağlama içerir `bindings` :  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Her bağlamanın en az bir türü, yönü ve adı vardır. Yukarıdaki örnekte, ilk bağlama `httpTrigger` yönden bir tür `in` . Yönü için `in` , `name` Tetikleyici tarafından çağrıldığında işleve gönderilen giriş parametresinin adını belirtir.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Koleksiyondaki ikinci bağlamanın adı `res` . Bu `http` bağlama, `out` HTTP yanıtını yazmak için kullanılan bir çıkış bağlamadır (). 

Bu işlevden bir Azure depolama kuyruğuna yazmak için `out` `queue` `msg` aşağıdaki kodda gösterildiği gibi adıyla bir tür bağlama ekleyin:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Koleksiyondaki ikinci bağlama `http` yöndür ve `out` Bu durumda özel, `name` `$return` Bu bağlamanın bir giriş parametresi sağlamak yerine işlevin dönüş değerini kullandığını gösterir.

Bu işlevden bir Azure depolama kuyruğuna yazmak için `out` `queue` `msg` aşağıdaki kodda gösterildiği gibi adıyla bir tür bağlama ekleyin:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Koleksiyondaki ikinci bağlamanın adı `res` . Bu `http` bağlama, `out` HTTP yanıtını yazmak için kullanılan bir çıkış bağlamadır (). 

Bu işlevden bir Azure depolama kuyruğuna yazmak için `out` `queue` `msg` aşağıdaki kodda gösterildiği gibi adıyla bir tür bağlama ekleyin:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Bu durumda, `msg` işlevine çıkış bağımsız değişkeni olarak verilir. Bir `queue` tür için, içindeki kuyruğun adını da belirtmeniz `queueName` ve Içindeki Azure depolama bağlantısının ( *local.settings.json*) *adını* sağlamanız gerekir `connection` . 
::: zone-end  
