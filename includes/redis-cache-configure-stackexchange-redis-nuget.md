---
author: yegu-ms
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: yegu
ms.openlocfilehash: 61e93e3700b9a396d2ac4fdcbb51fc5c874cf9cb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286325"
---
.NET uygulamaları, önbellek istemcisi uygulamalarının yapılandırmasını basitleştiren bir NuGet paketi kullanarak Visual Studio’da yapılandırılabilecek **StackExchange.Redis** Cache istemcisi kullanabilir. 

> [!NOTE]
> Daha fazla bilgi için bkz. [StackExchange. redsıs](https://github.com/StackExchange/StackExchange.Redis) GitHub sayfası ve [redsıs Istemci belgeleri için StackExchange. Azure önbelleği](https://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Visual Studio’da StackExchange.Redis NuGet paketi kullanarak bir istemci uygulamasını yapılandırmak için, **Çözüm Gezgini**’nde sağ tıklayın ve **NuGet Paketlerini Yönet**’i seçin. 

![NuGet paketlerini yönetme](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Arama metin kutusuna **StackExchange.Redis** ya da **StackExchange.Redis.StrongName** yazın, sonuçlardan istediğiniz sürümü seçin ve **Yükle**’ye tıklayın.

> [!NOTE]
> **StackExchange.Redis** istemci kitaplığının kesin adlandırılmış bir sürümünü kullanmak isterseniz, **StackExchange.Redis.StrongName** seçeneğin seçin; istemezseniz **StackExchange.Redis** seçeneğin seçin.
>
>

![StackExchange.Redis NuGet paketi](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

NuGet paketi indirir ve istemci uygulamanızı Azure Cache Redis StackExchange.Azure önbelleği için Redis istemcisini ile erişmek için gerekli derleme başvurularını ekler.

> [!NOTE]
> Daha önce projenizi StackExchange.Redis kullanacak şekilde yapılandırdıysanız **NuGet Paket Yöneticisi**’nden paket güncelleştirmelerini denetleyebilirsiniz. Denetleyip StackExchange.Redis NuGet paketinin güncelleştirilmiş sürümlerini yüklemek için tıklayın **güncelleştirmeleri** içinde **NuGet Paket Yöneticisi** penceresi. StackExchange.Redis NuGet paketin bir güncelleştirme varsa projenizi güncelleştirilmiş sürüme yükseltebilirsiniz.
>
>

Ayrıca, **Araçlar** menüsünden **NuGet Paket Yöneticisi**, **Paket Yöneticisi Konsolu**’na tıklayıp **Paket Yöneticisi Konsolu** penceresinde aşağıdaki komutu çalıştırarak StackExchange.Redis NuGet paketini yükleyebilirsiniz.

```
Install-Package StackExchange.Redis
```
