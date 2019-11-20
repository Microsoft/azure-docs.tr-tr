---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950010"
---
## <a name="create-the-local-function-app-project"></a>Yerel işlev uygulaması projesi oluşturma

Geçerli yerel dizinin `MyFunctionProj` klasöründe bir işlev uygulaması projesi oluşturmak için komut satırından aşağıdaki komutu çalıştırın. `MyFunctionProj`’de bir GitHub deposu da oluşturulur.

```command
func init MyFunctionProj
```

İstendiğinde, aşağıdaki dil seçimlerinden bir worker çalışma zamanı seçin:

+ `dotnet`: yeni bir .NET Core sınıf kitaplığı projesi (.csproj) oluşturur.
+ `node`: node. js tabanlı bir proje oluşturur. `javascript` Ya`typescript`da seçin. 
+ `python`: bir Python projesi için lütfen [Azure 'DA http ile tetiklenen bir Işlev oluştur](../articles/azure-functions/functions-create-first-function-python.md)' u doldurun.
+ `powershell`: bir PowerShell projesi için lütfen [Azure 'da Ilk PowerShell işlevinizi oluşturma](../articles/azure-functions/functions-create-first-function-powershell.md)' yı doldurun. 


Proje oluşturulduktan sonra, yeni `MyFunctionProj` proje klasörüne gitmek için aşağıdaki komutu kullanın.

```command
cd MyFunctionProj
```
