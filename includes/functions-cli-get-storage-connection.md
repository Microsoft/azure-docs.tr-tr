---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673349"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Azure depolama bağlantı dizesini alma

Daha önce, işlev uygulaması tarafından kullanılmak üzere bir Azure depolama hesabı oluşturdunuz. Bu hesabın bağlantı dizesi, Azure 'daki uygulama ayarlarında güvenli bir şekilde depolanır. Bu ayarı dosyadaki *local.settings.js* indirerek, bu bağlantıyı, işlevi yerel olarak çalıştırırken aynı hesaptaki bir depolama kuyruğuna yazma ' yı kullanabilirsiniz. 

1. Projenin kökünden, `<app_name>` önceki hızlı başlangıçta işlev uygulamanızın adıyla değiştirerek aşağıdaki komutu çalıştırın. Bu komut, dosyadaki varolan tüm değerlerin üzerine yazar.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. *local.settings.js* açın ve `AzureWebJobsStorage` depolama hesabı bağlantı dizesi olan adlı değeri bulun. `AzureWebJobsStorage`Bu makalenin diğer bölümlerinde adı ve bağlantı dizesini kullanırsınız.

> [!IMPORTANT]
> *local.settings.js* , Azure 'dan indirilen gizli dizileri içerdiğinden, bu dosyayı her zaman kaynak denetiminden dışlayın. Yerel işlevler projesiyle oluşturulan *. gitignore* dosyası, varsayılan olarak dosyayı dışlar.