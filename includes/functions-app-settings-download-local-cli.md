---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329574"
---
Azure 'da gerekli depolama hesabıyla birlikte bir işlev uygulaması oluşturdunuz. Bu hesabın bağlantı dizesi, Azure 'daki uygulama ayarlarında güvenli bir şekilde depolanır. Bu makalede, aynı hesaptaki bir depolama kuyruğuna iletiler yazarsınız. İşlevi yerel olarak çalıştırırken depolama hesabınıza bağlanmak için, uygulama ayarlarını yerel. Settings. JSON dosyasına indirmeniz gerekir. 

Projenin kökünden, ayarları yerel. Settings. json ' a indirmek için aşağıdaki Azure Functions Core Tools komutunu çalıştırın ve `<APP_NAME>` ' ı önceki makaledeki işlev uygulamanızın adıyla değiştirin:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Azure hesabınızda oturum açmanız gerekebilir.

> [!IMPORTANT]  
> Bu komut, Azure 'daki işlev uygulamanızdan gelen değerlerle mevcut ayarların üzerine yazar.  
>
> Gizli dizi içerdiğinden, Local. Settings. JSON dosyası asla yayımlanmaz ve kaynak denetiminden dışlanmalıdır.  
> 

Depolama hesabı bağlantı dizesi olan `AzureWebJobsStorage` değerine ihtiyacınız vardır. Çıkış bağlamasının beklendiği gibi çalıştığını doğrulamak için bu bağlantıyı kullanın.
