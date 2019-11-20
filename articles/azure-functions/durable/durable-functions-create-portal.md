---
title: Azure portal kullanarak Dayanıklı İşlevler oluşturun
description: Portal geliştirme için Azure Işlevleri Dayanıklı İşlevler uzantısını yüklemeyi öğrenin.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 238969b320608c08491c3d3e85870d57f41c1589
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614972"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Azure portal kullanarak Dayanıklı İşlevler oluşturun

Azure Işlevleri için [dayanıklı işlevler](durable-functions-overview.md) uzantısı, [Microsoft. Azure. WebJobs. Extensions. durabletask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)NuGet paketinde sunulmaktadır. Bu uzantının işlev uygulamanızda yüklü olması gerekir. Bu makalede, Azure portal dayanıklı işlevler geliştirebilmeniz için bu paketin nasıl yükleneceği gösterilmektedir.

> [!NOTE]
> 
> * ' De C#dayanıklı işlevler geliştiriyorsanız, bunun yerine [Visual Studio 2019 geliştirmeyi](durable-functions-create-first-csharp.md)göz önünde bulundurmanız gerekir.
> * JavaScript 'te dayanıklı işlevler geliştiriyorsanız bunun yerine [Visual Studio Code geliştirmeyi](./quickstart-js-vscode.md)göz önünde bulundurmanız gerekir.

## <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

Herhangi bir işlevin yürütülmesini barındırmak için bir işlev uygulamasına sahip olmanız gerekir. İşlev uygulaması, kaynakların daha kolay yönetilmesi, dağıtılması ve paylaşılması için işlevlerinizi bir mantıksal birim olarak gruplandırmanıza olanak tanır. .NET veya JavaScript uygulaması oluşturabilirsiniz.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Oluşturulan işlev uygulaması, varsayılan olarak Azure Işlevleri çalışma zamanının 2. x sürümünü kullanır. Dayanıklı İşlevler uzantısı, içindeki C#Azure işlevleri çalışma zamanının 1. x ve 2. x sürümlerinde ve JavaScript 'te sürüm 2. x üzerinde çalışır. Ancak, şablonlar yalnızca seçili dilden bağımsız olarak çalışma zamanının 2. x 'i hedeflenirken kullanılabilir.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Dayanıklı işlevler NPM paketini (yalnızca JavaScript) yükler

JavaScript Dayanıklı İşlevler oluşturuyorsanız, [`durable-functions` NPM paketini](https://www.npmjs.com/package/durable-functions)yüklemeniz gerekecektir.

1. İşlev uygulamanızın adını, ardından **platform özelliklerini**ve **Gelişmiş Araçlar 'ı (kudu)** seçin.

   ![İşlevler platform özellikleri kudu seçme](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Kudu konsolunun içinde **Hata Ayıkla Konsolu** sonra **cmd**' yi seçin.

   ![Kudu hata ayıklama konsolu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. İşlev uygulamanızın dosya dizini yapısı görüntülenmelidir. `site/wwwroot` klasörüne gidin. Buradan dosya dizini penceresine sürükleyip bırakarak bir `package.json` dosyasını karşıya yükleyebilirsiniz. Örnek bir `package.json` aşağıda verilmiştir:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Kudu yükleme paketi. JSON](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. `package.json` karşıya yüklendikten sonra, kudu uzaktan yürütme konsolundan `npm install` komutunu çalıştırın.

   ![Kudu çalıştırma NPM yüklemesi](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Orchestrator işlevi oluşturma

1. İşlev uygulamanızı genişletin ve **İşlevler+'in yanındaki**  düğmesine tıklayın. Bu, işlev uygulamanızdaki ilk işlevse **Portalda**'yı ve ardından **Devam**'ı seçin. Aksi takdirde üçüncü adıma geçin.

   ![Azure portalındaki İşlevler hızlı başlangıç sayfası](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. **Diğer şablonlar**'ı ve ardından **Sonlandır ve şablonları görüntüle**'yi seçin.

    ![İşlevler hızlı başlangıcı diğer şablonlar](./media/durable-functions-create-portal/add-first-function.png)

1. Arama alanına `durable` yazın ve ardından **DAYANıKLı işlevler http başlangıç** şablonunu seçin.

1. İstendiğinde, Azure DurableTask uzantısını işlev uygulamasına herhangi bir bağımlılığı **yüklemek için,** ' ı seçin. Bir verme işlevi uygulaması için uzantıyı yalnızca bir kez yüklemeniz gerekir. Yükleme başarılı olduktan sonra **Devam**'ı seçin.

    ![Bağlama uzantılarını yükleme](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Yükleme tamamlandıktan sonra, yeni işlevi `HttpStart` adlandırın ve **Oluştur**' u seçin. Oluşturulan işlev Düzenleme başlatmak için kullanılır.

1. İşlev uygulamasında, bu kez **dayanıklı işlevler Orchestrator** şablonunu kullanarak başka bir işlev oluşturun. Yeni düzenleme işlevinizi `HelloSequence`olarak adlandırın.

1. **Dayanıklı işlevler etkinlik** şablonunu kullanarak `Hello` adlı üçüncü bir işlev oluşturun.

## <a name="test-the-durable-function-orchestration"></a>Dayanıklı işlev düzenlemesini test etme

1. **Httpstart** işlevine geri dönün **</> Işlev URL 'sini al** ' ı seçin ve URL 'yi **kopyalayın** . **Merhaba sıra** işlevini başlatmak IÇIN bu URL 'yi kullanın.

1. Kopyaladığınız URL 'ye bir POST isteği göndermek için Postman veya kıvrık gibi bir HTTP aracını kullanın. Aşağıdaki örnek, dayanıklı işleve POST isteği gönderen bir kıvrımlı komuttur:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    Bu örnekte, `{your-function-app-name}` işlev uygulamanızın adı olan etki alanıdır. Yanıt iletisi, yürütmeyi izlemek ve yönetmek için kullanabileceğiniz bir dizi URI uç noktası içerir ve aşağıdaki örnekte olduğu gibi görünür:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. `statusQueryGetUri` uç noktası URI 'sini çağırın ve dayanıklı işlevin geçerli durumunu görürsünüz ve bu örnek aşağıdaki gibi görünebilir:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Durum **tamamlanana**kadar `statusQueryGetUri` uç noktasını çağırmaya devam edin ve aşağıdaki örnekte olduğu gibi bir yanıt görürsünüz:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

İlk dayanıklı işleviniz artık Azure 'da çalışıyor ve çalışıyor.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Ortak dayanıklı işlev desenleri hakkında bilgi edinin](durable-functions-overview.md#application-patterns)
