---
title: İşlevleri kullanarak bir Azure Depolama kuyruğuna ileti ekleme
description: Bir HTTP isteği tarafından çağrılan ve bir Azure Depolama kuyruğunda iletiler oluşturan sunucusuz işlev oluşturmak için Azure İşlevlerini kullanın.
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d2821a16e0b72b32cc392b7ae626d782734458a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98674210"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>İşlevleri kullanarak bir Azure Depolama kuyruğuna ileti ekleme

Azure İşlevleri’nde giriş ve çıkış bağlamaları, kodunuzda kullanılabilen dış hizmetlerden veri oluşturmanın bildirim temelli bir yöntemini sağlar. Bu hızlı başlangıçta, bir HTTP isteği tarafından işlev tetiklendiğinde kuyrukta bir ileti oluşturmak üzere çıkış bağlaması kullanırsınız. İşlevinizin oluşturduğu sıra iletilerini görüntülemek için Azure Storage kapsayıcısını kullanırsınız.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için:

- Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- [Azure portalından ilk işlevinizi oluşturma](./functions-get-started.md) bölümündeki yönergeleri izleyin ve **Kaynakları temizleme** adımını uygulamayın. Bu hızlı başlangıç, burada kullandığınız işlev uygulamasını ve işlevi oluşturur.

## <a name="add-an-output-binding"></a><a name="add-binding"></a>Çıkış bağlaması ekleme

Bu bölümde, daha önce oluşturduğunuz işleve bir kuyruk depolama çıkış bağlaması eklemek üzere portal kullanıcı arabirimini kullanın. Bu bağlama, bir kuyrukta ileti oluşturmak için en az kod yazmayı mümkün kılar. Depolama bağlantısı açma, kuyruk oluşturma veya bir kuyruk başvurusu alma gibi görevler için kod yazmanız gerekmez. Azure İşlevleri çalışma zamanı ve kuyruk çıkış bağlaması bu görevleri sizin için gerçekleştirir.

1. Azure portalında, [Azure portalından ilk işlevinizi oluşturma](./functions-get-started.md) bölümünde oluşturduğunuz işlev uygulamasına ait işlev uygulaması sayfasını açın. Bu sayfayı açmak için arama yapın ve **işlev uygulaması** seçin. Ardından, işlev uygulamanızı seçin.

1. İşlev uygulamanızı seçin ve ardından önceki hızlı başlangıçta oluşturduğunuz işlevi seçin.

1. **Tümleştirme**' i seçin ve **+ Çıkış Ekle**' yi seçin.

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="İşleviniz için bir çıkış bağlaması oluşturun." border="true":::

1. **Azure kuyruk depolama** bağlama türünü seçin ve bu ekran görüntüsünü izleyen tabloda belirtilen ayarları ekleyin: 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="Azure portalındaki bir işleve Kuyruk depolama çıkış bağlaması ekleyin." border="true":::
    
    | Ayar      |  Önerilen değer   | Açıklama                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **İleti parametre adı** | outputQueueItem | Çıkış bağlama parametresinin adı. | 
    | **Kuyruk adı**   | outqueue  | Depolama hesabınızdaki bağlantı kurulacak kuyruğun adı. |
    | **Depolama hesabı bağlantısı** | AzureWebJobsStorage | İşlev uygulamanız tarafından kullanılmakta olan depolama hesabı bağlantısını kullanabilir veya yeni bir bağlantı oluşturabilirsiniz.  |

1. Bağlamayı eklemek için **Tamam ' ı** seçin.

Bir çıkış bağlaması tanımladığınıza göre, bir kuyruğa ileti eklemek üzere bağlamayı kullanmak için kodu güncelleştirmeniz gerekir.  

## <a name="add-code-that-uses-the-output-binding"></a>Çıkış bağlaması kullanan kod ekleme

Bu bölümde, çıkış kuyruğuna bir ileti yazan kodu ekleyeceksiniz. İleti, sorgu dizesinde HTTP tetikleyicisine geçirilen değeri içerir. Örneğin, sorgu dizesi `name=Azure` değerini içeriyorsa, kuyruk iletisi *İşleve geçirilen ad: Azure* şeklinde olur.

1. İşlevinizde işlev kodunu düzenleyicide göstermek için **Code + test** ' i seçin.

1. İşlev dilinize uygun işlev kodunu kullanın:

    # <a name="c"></a>[,\#](#tab/csharp)

    Yöntem imzasına aşağıdaki örnekte gösterildiği gibi bir **outputQueueItem** parametresi ekleyin.

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    İşlevin gövdesine `return` deyiminden hemen önce, kuyruk iletisi oluşturmak için parametreyi kullanan kodu ekleyin.

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    Kuyruk iletisi oluşturmak üzere `context.bindings` nesnesinde çıkış bağlaması kullanan kodu ekleyin. Bu kodu `context.done` deyiminden önce ekleyin.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. Değişiklikleri kaydetmek için **Kaydet**'i seçin.

## <a name="test-the-function"></a>İşlevi test etme

1. Kod değişiklikleri kaydedildikten sonra **Test**' i seçin.
1. Testinizin aşağıdaki görüntüyle eşleştiğinden emin olun ve **Çalıştır**' ı seçin. 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="Azure portal kuyruk depolama bağlamasını test edin." border="true":::

    **İstek gövdesinin**`name` değeri *Azure*’u içerdiğine dikkat edin. Bu değer, işlev çağrıldığında oluşturulan kuyruk iletisinde görüntülenir.
    
    Burada **Çalıştır**’ı seçmeye alternatif olarak, bir tarayıcıya URL girerek ve sorgu dizesinde `name` değerini belirterek işlevi çağırabilirsiniz. Tarayıcı yöntemi [önceki hızlı başlangıç](./functions-get-started.md) içinde gösterilmiştir.

1. İşlevin başarılı olduğundan emin olmak için günlükleri denetleyin. 

Çıkış bağlaması ilk kez kullanıldığında, Depolama hesabınızda İşlevler çalışma zamanı tarafından **outqueue** adlı yeni bir kuyruk oluşturulur. Kuyruğun ve içindeki bir iletinin oluşturulduğunu doğrulamak için depolama hesabı ' nı kullanacaksınız.

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>AzureWebJobsStorage 'e bağlı depolama hesabını bulma


1. İşlev uygulamanıza gidin ve **yapılandırma**' yı seçin.

1. **Uygulama ayarları** altında **AzureWebJobsStorage** öğesini seçin.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="Ekran görüntüsü AzureWebJobsStorage seçili olan yapılandırma sayfasını gösterir." border="true":::

1. Hesap adını bulun ve aklınızda olun.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="AzureWebJobsStorage 'e bağlı depolama hesabını bulun." border="true":::

### <a name="examine-the-output-queue"></a>Çıkış kuyruğunu inceleme

1. İşlev uygulamanızın kaynak grubunda, bu hızlı başlangıç için kullandığınız depolama hesabını seçin.

1. **Kuyruk hizmeti** altında **Kuyruklar** ' ı seçin ve **outqueue** adlı kuyruğu seçin. 

   Kuyruk, HTTP ile tetiklenen işlevi çalıştırdığınızda kuyruk çıkış bağlamasının oluşturduğu iletiyi içerir. İşlevi varsayılan `name`*Azure* değeri ile çağırdıysanız, kuyruk iletisi *İşleve geçirilen ad: Azure* şeklinde olur.

1. İşlevi yeniden çalıştırdığınızda kuyrukta yeni bir iletinin göründüğünü göreceksiniz.  

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bar olan bir işleve çıkış bağlaması eklediniz. Kuyruk depolamaya bağlama hakkında daha fazla bilgi için bkz. [Azure İşlevleri Depolama kuyruğu bağlamaları](functions-bindings-storage-queue.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]
