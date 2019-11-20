---
title: Azure Işlevleri bağlama uzantılarını el ile yükler veya güncelleştirir
description: Dağıtılan işlev uygulamaları için Azure Işlevleri bağlama uzantılarını yüklemeyi veya güncelleştirmeyi öğrenin.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure işlevleri, işlevler, bağlama uzantıları, NuGet, güncelleştirmeler
ms.service: azure-functions
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: 7686a9b2df6df6b54851e9c9957186f76be3fafd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085065"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Portaldan Azure Işlevleri bağlama uzantılarını el ile yüklemeyi veya güncelleştirmeyi güncelleştirme

Azure Işlevleri sürüm 2. x çalışma zamanı, Tetikleyiciler ve bağlamalar için kod uygulamak üzere bağlama uzantıları kullanır. Bağlama uzantıları NuGet paketlerinde sağlanır. Bir uzantıyı kaydetmek için aslında bir paket yüklersiniz. İşlevleri geliştirirken, bağlama uzantılarını yüklediğiniz yol geliştirme ortamına bağlıdır. Daha fazla bilgi için bkz. Tetikleyiciler ve bağlamalar için [bağlama uzantılarını kaydetme](./functions-bindings-register.md) makalesi.

Bazen Azure portal bağlama uzantılarınızı el ile yüklemeniz veya güncelleştirmeniz gerekir. Örneğin, kayıtlı bir bağlamayı daha yeni bir sürüme güncelleştirmeniz gerekebilir. Ayrıca, portaldaki **tümleştir** sekmesine yüklenebilen desteklenen bir bağlamayı kaydetmeniz gerekebilir.

## <a name="install-a-binding-extension"></a>Bağlama uzantısı yükler

Uzantıları portala el ile yüklemek veya güncelleştirmek için aşağıdaki adımları kullanın.

1. [Azure Portal](https://portal.azure.com), işlev uygulamanızı bulun ve seçin. **Genel bakış** sekmesini seçin ve **Durdur**' u seçin.  İşlev uygulamasının durdurulması, değişikliklerin yapılabilmesi için dosyaların kilidini açar.

1. **Platform özellikleri** sekmesini seçin ve **geliştirme araçları** altında **Gelişmiş araçlar (kudu)** öğesini seçin. Kudu uç noktası (`https://<APP_NAME>.scm.azurewebsites.net/`) yeni bir pencerede açılır.

1. Kudu penceresinde **Hata Ayıkla konsol** > **cmd**' yi seçin.  

1. Komut penceresinde öğesine `D:\home\site\wwwroot` gidin ve klasörü silmek `bin` için yanındaki Sil simgesini seçin. Silmeyi onaylamak için **Tamam ' ı** seçin.

1. `extensions.csproj` Dosyanın yanında, işlev uygulaması için bağlama uzantılarını tanımlayan Düzenle simgesini seçin. Proje dosyası çevrimiçi düzenleyicide açılır.

1. **ItemGroup**Içinde, **packagereference** öğelerinin gerekli eklemelerini ve güncelleştirmelerini yapıp **Kaydet**' i seçin. Desteklenen paket sürümlerinin geçerli listesi, [hangi paketlere](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) ihtiyacım var? wiki makalesinde bulunabilir. Üç Azure depolama bağlaması da Microsoft. Azure. WebJobs. Extensions. Storage paketini gerektirir.

1. Klasörde, başvurulan derlemeleri `bin` yeniden derlemek için aşağıdaki komutu çalıştırın. `wwwroot`

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Portalda **genel bakış** sekmesine döndüğünüzde, işlev uygulamasını yeniden başlatmak için **Başlat** ' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)
