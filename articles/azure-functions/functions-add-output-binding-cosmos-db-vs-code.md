---
title: Azure Işlevlerini Visual Studio Code kullanarak Azure Cosmos DB bağlama
description: Visual Studio Code projenize bir çıkış bağlaması ekleyerek Azure Işlevlerini bir Azure Cosmos DB hesabına bağlamayı öğrenin.
author: ThomasWeiss
ms.date: 03/23/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 0a0c63ee54699185bcd02104b1a3f4d0070ea808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023257"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Azure Işlevlerini Visual Studio Code kullanarak Azure Cosmos DB bağlama

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Bu makalede, önceki hızlı başlangıç makalesinde oluşturduğunuz işleve [Azure Cosmos DB](../cosmos-db/introduction.md) bağlanmak için Visual Studio Code nasıl kullanılacağı gösterilmektedir. Bu işleve eklediğiniz çıkış bağlaması, HTTP isteğinden verileri Azure Cosmos DB kapsayıcısında depolanan bir JSON belgesine yazar. 

::: zone pivot="programming-language-csharp"
Başlamadan önce, [Visual Studio Code kullanarak Azure 'da bir C# işlevi oluşturma hızlı](create-first-function-vs-code-csharp.md)başlangıcı ' nı doldurmanız gerekir. Bu makalenin sonunda kaynakları zaten temizlediğinizde, işlev uygulamasını ve ilgili kaynakları Azure 'da yeniden oluşturmak için adımlara tekrar gidin.
::: zone-end
::: zone pivot="programming-language-javascript"  
Başlamadan önce, [Visual Studio Code kullanarak Azure 'da bir JavaScript Işlevi oluşturma hızlı](create-first-function-vs-code-node.md)başlangıcı ' nı doldurmanız gerekir. Bu makalenin sonunda kaynakları zaten temizlediğinizde, işlev uygulamasını ve ilgili kaynakları Azure 'da yeniden oluşturmak için adımlara tekrar gidin.  
::: zone-end

## <a name="configure-your-environment"></a>Ortamınızı yapılandırma

Başlamadan önce, Visual Studio Code için [Azure veritabanları uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) yüklediğinizden emin olun.

## <a name="create-your-azure-cosmos-db-account"></a>Azure Cosmos DB hesabınızı oluşturun

> [!IMPORTANT]
> [Azure Cosmos DB sunucusuz](../cosmos-db/serverless.md) artık önizlemede kullanılabilir. Bu tüketim tabanlı mod sunucusuz iş yükleri için güçlü bir seçenek Azure Cosmos DB. Sunucusuz modda Azure Cosmos DB kullanmak için hesabınızı oluştururken **Kapasite modu** olarak **sunucusuz** ' ı seçin.

1. Yeni bir tarayıcı penceresinde [Azure portalında](https://portal.azure.com/) oturum açın.

2. **Kaynak oluştur** > **Veritabanları** > **Azure Cosmos DB** seçeneğine tıklayın.
   
    :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png" alt-text="Azure portalındaki Veritabanları bölmesi" border="true":::

3. **Azure Cosmos DB hesabı oluştur** sayfasında, yeni Azure Cosmos DB hesabınıza ilişkin ayarları girin. 
 
    Ayar|Değer|Açıklama
    ---|---|---
    Abonelik|*Aboneliğiniz*|[Önceki makalede](./create-first-function-vs-code-csharp.md)işlev uygulaması oluşturduğunuz Azure aboneliğini seçin.
    Kaynak Grubu|*Kaynak grubunuz*|[Önceki makalede](./create-first-function-vs-code-csharp.md)işlev uygulaması oluşturduğunuz kaynak grubunu seçin.
    Hesap Adı|*Benzersiz bir ad girin*|Azure Cosmos DB hesabınızı tanımlayan benzersiz bir ad girin.<br><br>Hesap adı yalnızca küçük harf, sayı ve kısa çizgi (-) kullanabilir ve 3 ila 31 karakter uzunluğunda olmalıdır.
    API|Çekirdek (SQL)|Bir SQL söz dizimi kullanarak Sorgulayabileceğiniz bir belge veritabanı oluşturmak için **Core (SQL)** seçeneğini belirleyin. [SQL apı Azure Cosmos DB hakkında daha fazla bilgi edinin](../cosmos-db/introduction.md).|
    Konum|*Konumunuza en yakın bölgeyi seçin*|Azure Cosmos DB hesabınızın barındırılacağı coğrafi konumu seçin. Verilerinize en hızlı erişimi sağlamak için size veya kullanıcılarınıza en yakın konumu kullanın.
    Kapasite modu|Sunucusuz veya sağlanan aktarım hızı|[Sunucusuz modda bir](../cosmos-db/serverless.md) hesap oluşturmak Için **sunucusuz** ' ı seçin. [Sağlanan aktarım](../cosmos-db/set-throughput.md) hızı modunda bir hesap oluşturmak için **sağlanan aktarım hızı** ' nı seçin.<br><br>Azure Cosmos DB kullanmaya başladıysanız **sunucusuz** ' ı seçin.

4. **Gözden geçir ve oluştur**’a tıklayın. **Ağ** ve **Etiketler** bölümünü atlayabilirsiniz. 

5. Özet bilgilerini gözden geçirin ve **Oluştur**' a tıklayın. 

6. Yeni Azure Cosmos DB oluşturulmasını bekleyin, sonra **Kaynağa Git**' i seçin.

    :::image type="content" source="../cosmos-db/media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Azure Cosmos DB hesabının oluşturulması tamamlanmıştır" border="true":::

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Azure Cosmos DB veritabanı ve kapsayıcı oluşturma

Azure Cosmos DB hesabınızdan **Veri Gezgini** ve ardından **yeni kapsayıcı**' yı seçin. My- *Container* adlı yeni bir kapsayıcı olan *My-Database* adlı yeni bir veritabanı oluşturun ve `/id` [bölüm anahtarı](../cosmos-db/partitioning-overview.md)olarak öğesini seçin.

:::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Azure portal yeni bir Azure Cosmos DB kapsayıcısı oluşturma" border="true":::

## <a name="update-your-function-app-settings"></a>İşlev uygulaması ayarlarınızı güncelleştirme

[Önceki hızlı başlangıç makalesinde](./create-first-function-vs-code-csharp.md)Azure 'da bir işlev uygulaması oluşturdunuz. Bu makalede, İşlev Uygulaması, yukarıda oluşturduğunuz Azure Cosmos DB kapsayıcısında JSON belgelerini yazmak için güncelleştiniz. Azure Cosmos DB hesabınıza bağlanmak için, bağlantı dizesini uygulama ayarlarınıza eklemeniz gerekir. Daha sonra, yerel olarak çalışırken Azure Cosmos DB hesabınıza bağlanabilmeniz için local.settings.jsyeni ayarı dosyaya indirebilirsiniz.

1. Visual Studio Code, az önce oluşturduğunuz Azure Cosmos DB hesabını bulun. Adına sağ tıklayın ve **bağlantı dizesini Kopyala**' yı seçin.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Azure Cosmos DB bağlantı dizesi kopyalanıyor" border="true":::

1. <kbd>F1</kbd> tuşuna basarak komut paletini açın, sonra komutunu arayıp çalıştırın `Azure Functions: Add New Setting...` .

1. Önceki makalede oluşturduğunuz işlev uygulamasını seçin. İstemlerde aşağıdaki bilgileri sağlayın:

    + **Yeni uygulama ayarı adı girin**: tür `CosmosDbConnectionString` .

    + **"CosmosDbConnectionString" için değer girin**: daha önce kopyalanmış Azure Cosmos DB hesabınızın bağlantı dizesini yapıştırın.

1. Bir kez <kbd>F1</kbd> tuşuna basarak komut paletini açın, sonra komutunu arayıp çalıştırın `Azure Functions: Download Remote Settings...` . 

1. Önceki makalede oluşturduğunuz işlev uygulamasını seçin. Var olan yerel ayarların üzerine yazmak için **Tümüne Evet** ' i seçin. 

## <a name="register-binding-extensions"></a>Bağlama uzantılarını kaydetme

Azure Cosmos DB bir çıkış bağlaması kullandığınızdan, projeyi çalıştırmadan önce karşılık gelen bağlama uzantısının yüklü olması gerekir. 

::: zone pivot="programming-language-csharp"

HTTP ve Zamanlayıcı Tetikleyicileri dışında, bağlamalar uzantı paketleri olarak uygulanır. Depolama uzantısı paketini projenize eklemek için Terminal penceresinde aşağıdaki [DotNet paket Ekle](/dotnet/core/tools/dotnet-add-package) komutunu çalıştırın.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

Projeniz, önceden tanımlanmış bir uzantı paketleri kümesini otomatik olarak yüklediği [uzantı](functions-bindings-register.md#extension-bundles)paketlerini kullanmak üzere yapılandırılmıştır. 

Uzantı demeti kullanımı, projenin kökündeki host.jsaşağıdaki gibi görünen dosya üzerinde etkinleştirilir:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

Şimdi projenize Azure Cosmos DB çıktı bağlamayı ekleyebilirsiniz.

## <a name="add-an-output-binding"></a>Çıktı bağlaması ekleme

Işlevlerde, her bağlama türü `direction` , `type` `name` dosyasında function.jstanımlanması için bir, ve benzersiz gerektirir. Bu öznitelikleri tanımlama yöntemi, işlev uygulamanızın diline bağlıdır.

::: zone pivot="programming-language-csharp"

C# sınıf kitaplığı projesinde bağlamalar, işlev yönteminde bağlama öznitelikleri olarak tanımlanır. Işlevler için gereken dosya *function.js* , bu özniteliklere göre otomatik olarak oluşturulur.

*Httpexample. cs* proje dosyasını açın ve Yöntem tanımına aşağıdaki parametreyi ekleyin `Run` :

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

`documentsOut`Parametresi, <T> işlev tamamlandığında Azure Cosmos DB KAPSAYıCıNıZA yazılacak JSON belgelerinin koleksiyonunu temsil eden bir ıasynccollector türüdür. Belirli öznitelikler kapsayıcının adını ve üst veritabanının adını belirtir. Azure Cosmos DB hesabınız için bağlantı dizesi tarafından ayarlanır `ConnectionStringSettingAttribute` .

Çalıştırma yöntemi tanımı artık aşağıdaki gibi görünmelidir:  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

Bağlama öznitelikleri, dosyasında function.jsdoğrudan tanımlanmıştır. Bağlama türüne bağlı olarak ek özellikler gerekli olabilir. [Azure Cosmos DB çıkış yapılandırması](./functions-bindings-cosmosdb-v2-output.md#configuration) , bir Azure Cosmos DB çıkış bağlaması için gereken alanları açıklar. Uzantı, dosyadaki function.jsbağlama eklemeyi kolaylaştırır. 

Bir bağlama oluşturmak için, HttpTrigger klasörünüzdeki dosyayı sağ tıklatın (macOS üzerinde CTRL + tıklama) `function.json` ve **bağlama Ekle...** seçeneğini belirleyin. Yeni bağlama yönelik aşağıdaki bağlama özelliklerini tanımlamak için istemleri izleyin:

| İstem | Değer | Açıklama |
| -------- | ----- | ----------- |
| **Bağlama yönünü seçin** | `out` | Bağlama bir çıkış bağlamadır. |
| **"Out" yönünde bağlamayı seçin** | `Azure Cosmos DB` | Bağlama bir Azure Cosmos DB bağlamadır. |
| **Kodunuzda bu bağlamayı tanımlamak için kullanılan ad** | `outputDocument` | Kodunuzda başvurulan bağlama parametresini tanımlayan ad. |
| **Verilerin yazılacağı Cosmos DB veritabanı** | `my-database` | Hedef kapsayıcıyı içeren Azure Cosmos DB veritabanının adı. |
| **Verilerin yazılacağı veritabanı koleksiyonu** | `my-container` | JSON belgelerinin yazılacağı Azure Cosmos DB kapsayıcısının adı. |
| **True ise, Cosmos DB veritabanı ve koleksiyonunu oluşturur** | `false` | Hedef veritabanı ve kapsayıcı zaten var. |
| **"local.setting.json" ayarını seçin** | `CosmosDbConnectionString` | Azure Cosmos DB hesabının bağlantı dizesini içeren bir uygulama ayarının adı. |
| **Bölüm anahtarı (isteğe bağlı)** | *boş bırakın* | Yalnızca çıkış bağlaması kapsayıcıyı oluşturduğunda gereklidir. |
| **Koleksiyon işleme hızı (isteğe bağlı)** | *boş bırakın* | Yalnızca çıkış bağlaması kapsayıcıyı oluşturduğunda gereklidir. |

Üzerinde function.jsdizisine eklenen bir bağlama `bindings` , aşağıdaki gibi görünmelidir:

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Çıkış bağlaması kullanan kod ekleme

::: zone pivot="programming-language-csharp"  

`documentsOut`JSON belgesi oluşturmak için çıkış bağlama nesnesini kullanan kodu ekleyin. Metodun döndürüldüğünden önce bu kodu ekleyin.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

Bu noktada, işleviniz aşağıdaki gibi görünmelidir:

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

`outputDocument`JSON belgesi oluşturmak için çıkış bağlama nesnesini kullanan kodu ekleyin `context.bindings` . Bu kodu `context.res` deyimden önce ekleyin.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

Bu noktada, işleviniz aşağıdaki gibi görünmelidir:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

1. Önceki makalede olduğu gibi, uygulama projesi ve çekirdek araçları işlevine başlamak için <kbd>F5</kbd> ' e basın. 

1. Temel araçlar çalışırken **Azure: Functions** alanına gidin. **İşlevler** altında **Yerel proje**  >  **işlevleri**' ni genişletin. İşlevi sağ tıklatın (Mac üzerinde CTRL + tıklama) `HttpExample` ve **Şimdi işlevi Çalıştır...** seçeneğini belirleyin.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="İşlevi şimdi Visual Studio Code Çalıştır":::

1. **İstek gövdesi gir** bölümünde istek iletisi gövdesi değerini görürsünüz `{ "name": "Azure" }` . Bu istek iletisini işlevinizde göndermek için ENTER tuşuna basın.  
 
1. Bir yanıt döndürüldüğünde, temel araçları durdurmak için <kbd>CTRL + C</kbd> tuşlarına basın.

### <a name="verify-that-a-json-document-has-been-created"></a>JSON belgesinin oluşturulduğunu doğrulama

1. Azure portal, Azure Cosmos DB hesabınıza dönüp **Veri Gezgini**' yı seçin.

1. Veritabanınızı ve kapsayıcınızı genişletin ve kapsayıcıda oluşturulan belgeleri listelemek için **öğeler** ' i seçin.

1. Çıkış bağlaması tarafından yeni bir JSON belgesi oluşturulduğunu doğrulayın.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Azure Cosmos DB kapsayıcısında yeni bir belgenin oluşturulduğunu doğrulama" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>Güncelleştirilmiş uygulamayı yeniden dağıtın ve doğrulayın

1. Visual Studio Code ' de, F1 tuşuna basarak komut paletini açın. Komut paletinde, araması yapın ve seçin `Azure Functions: Deploy to function app...` .

1. İlk makalede oluşturduğunuz işlev uygulamasını seçin. Projenizi aynı uygulamaya yeniden **dağıtmakta** olduğunuzdan, dosyaların üzerine yazma uyarısını kapatmak için Dağıt ' ı seçin.

1. Dağıtım tamamlandıktan sonra, işlevi Azure 'da tetiklemek için **Şimdi Çalıştır...** özelliğini kullanabilirsiniz.

1. Daha sonra çıkış bağlamasının yeni bir JSON belgesi oluşturduğunu doğrulamak için [Azure Cosmos DB kapsayıcıda oluşturulan belgeleri denetleyin](#verify-that-a-json-document-has-been-created) .

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure 'da, *kaynaklar* işlev uygulamalarına, işlevlere, depolama hesaplarına ve benzeri bir kaynağa başvurur. Bunlar *kaynak grupları* halinde gruplandırılır ve grubu silerek bir gruptaki her şeyi silebilirsiniz.

Bu hızlı başlangıçları tamamlamak için kaynaklar oluşturdunuz. [Hesap durumunuza](https://azure.microsoft.com/account/) ve [hizmet fiyatlandırmanıza](https://azure.microsoft.com/pricing/) bağlı olarak size bu kaynakların ücretleri yansıtılabilir. Kaynaklara artık ihtiyacınız yoksa, şunları yaparak silebilirsiniz:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen işlevinizi JSON belgelerini bir Azure Cosmos DB kapsayıcısına yazacak şekilde güncelleştirdiniz. Artık Visual Studio Code kullanarak Işlevleri geliştirme hakkında daha fazla bilgi edinebilirsiniz:

+ [Visual Studio Code kullanarak Azure Işlevleri geliştirme](functions-develop-vs-code.md)

+ [Azure işlevleri Tetikleyicileri ve bağlamaları](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [C# ' de tamamen işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=csharp).

+ [Azure Işlevleri C# Geliştirici Başvurusu](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript 'teki bütün işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Işlevleri JavaScript Geliştirici Kılavuzu](functions-reference-node.md)  
::: zone-end  