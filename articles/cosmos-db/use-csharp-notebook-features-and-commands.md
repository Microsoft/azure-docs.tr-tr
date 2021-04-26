---
title: Azure Cosmos DB C# not defterlerinde yerleşik Not defteri komutlarını ve özelliklerini kullanma (Önizleme)
description: Azure Cosmos DB yerleşik C# not defterlerini kullanarak genel işlemleri yapmak için yerleşik komutları ve özellikleri nasıl kullanacağınızı öğrenin.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 67fe71d8e2d6ab239989cb30e9bf5a1b4d731037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340490"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Azure Cosmos DB C# not defterlerinde yerleşik Not defteri komutlarını ve özelliklerini kullanma (Önizleme)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 'daki yerleşik jupi Not defterleri, Azure portal verilerinizi analiz etmenize ve görselleştirmenize olanak tanır. Bu makalede, C# not defterlerinde yaygın işlemler yapmak için yerleşik Not defteri komutlarının ve özelliklerinin nasıl kullanılacağı açıklanır.

## <a name="install-a-new-nuget-package"></a>Yeni bir NuGet paketi yükler
Azure Cosmos hesaplarınız için Not defteri desteğini etkinleştirdikten sonra, yeni bir not defteri açıp bir paket yükleyebilirsiniz.

Yeni bir kod hücresinde, ``PackageToBeInstalled`` Istenen NuGet paketiyle ve isterseniz ``optionalVersion`` paketin belirli bir sürümüyle değiştirerek aşağıdaki kodu ekleyin ve çalıştırın. 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

Aynı hücrede birden çok NuGet paketi yükleyebilirsiniz. Paketler, Azure Cosmos hesap çalışma alanındaki herhangi bir not defterinden kullanılabilir. 

Şu anda C# Not defterleri çalışma alanı NuGet paketlerinin özyinelemeli çözümlemesini desteklemez. Bir NuGet paketi şu anda yüklü olmayan diğer NuGet paketlerine bağımlılıklar içeriyorsa, bunlara ana paket ile birlikte açıkça başvurmanız gerekir.

> [!TIP]
> Not defteriniz için özel bir paket gerekiyorsa, paketi yüklemek ve ilk hücre yapmak için Not defterinize bir hücre eklemenizi öneririz. Bu, varsayılan olarak Azure Cosmos DB yüklediği diğer paketlerle çakışmalar olasılığını azaltır. [Çalışma alanını sıfırlarsanız](#reset-notebooks-workspace)tüm paketleri kaldıran paketleri yeniden yüklemek de kolaydır. 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>Yerleşik Azure Cosmos DB .NET SDK 'sını kullanma
[SQL API için Azure Cosmos DB .NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) 'nın 3. sürümü yüklü ve Azure Cosmos hesabı için Not defteri ortamına dahildir.

``CosmosClient``Herhangi BIR SDK işlemini çalıştırmak için örneğini oluşturun. 

Örnek:

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
Daha fazla bilgi için bkz. [.net v3 SDK örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage). 

> [!IMPORTANT]
> Yerleşik Azure Cosmos DB .NET SDK yalnızca SQL (çekirdek) API hesapları için desteklenir. Diğer API 'Ler için, API 'ye karşılık gelen [ilgili .net sürücüsünü yüklemeniz](#install-a-new-nuget-package) gerekir. 

## <a name="set-custom-options-using-cosmosclientoptions"></a>Kullanarak özel seçenekleri ayarla ``CosmosClientOptions``
Daha fazla esneklik için özel ``CosmosClientOptions`` özelliği ayarlayabilir ve Örneğinizde geçirebilirsiniz ``CosmosClient`` . Bu özelliği kullanarak şunları yapabilirsiniz:

- Kullanıcı Aracısı sonekine, her istekte dahil etmek için bir uygulama adı ayarlayın.
- Hizmet için işlemler çalıştırılırken kullanılacak tercih edilen bölgeyi ayarlayın.
- Hız sınırlı istekleri işlemek için özel bir yeniden deneme ilkesi ayarlayın.

Desteklenen tüm ayarlar için [Cosmosclientoptions API başvurusu](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) makalesine bakın. Aşağıda özelliği ayarlamayı gösteren bir örnek verilmiştir `cosmosClientOptions` :

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>Hesap uç noktasına ve birincil anahtar değişkenlerine erişin
Not defterinizin bulunduğu Azure Cosmos hesabının yerleşik uç noktasına ve anahtarına erişebilirsiniz.

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> ``Cosmos.Key``Ve ``Cosmos.Endpoint`` DEĞIŞKENLERI yalnızca SQL API için geçerlidir. Diğer API 'Ler için, Azure Cosmos hesabınızdaki **bağlantı dizeleri** veya **anahtarlar** dikey penceresinde uç noktasını ve anahtarı bulun.  

## <a name="print-console-output-in-c-code"></a>Konsol çıktısını C# kodunda Yazdır
C# kodunuzda, hücreyi çalıştırdığınızda görünecek konsol çıktısına yönelik [dize ilişkilendirme](/dotnet/csharp/language-reference/tokens/interpolated) ile Display. asmarku () sözdizimini kullanabilirsiniz. 

Örnek: 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> Console. WriteLine () sözdizimi, C# not defterlerinde Şu anda desteklenmemektedir. Programınızdaki konsol çıkışını yazdırmak için Display. Asmarku kullanın. 

## <a name="use-built-in-nteract-data-explorer"></a>Yerleşik nteryasası Veri Gezgini 'ni kullanma
Yerleşik [nteryasası Veri Gezgini](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) 'ni kullanarak bir öğe koleksiyonunu filtreleyebilir ve görselleştirebilirsiniz. Bir hücrede, son satırda görselleştirmek istediğiniz değişkeni koyun. Bu, hücreyi çalıştırdığınızda nteryasası içinde otomatik olarak görüntülenir.

Örneğin, *GetingStarted_Csharp. ipynb* örneğinde, sonuçumuzu kullanarak değişkeni yazdırabiliriz ``telemetryEvents`` . Tüm örnek için [GettingStarted_Csharp. ipynb not defterine](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb) bakın. 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="CSharp sorgu hücresi":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="nteryasası Veri Gezgini":::

## <a name="use-built-in-dictionary-viewer"></a>Yerleşik sözlük görüntüleyicisini kullan
Bir değişkeni görüntülemek için yerleşik sözlük görüntüleyicisini kullanabilirsiniz. Bir hücrede, son satırda görselleştirmek istediğiniz değişkeni koyun ve bu, hücre çalıştırıldığında otomatik olarak görüntülenir.

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="Yerleşik sözlük Görüntüleyicisi":::

## <a name="upload-json-items-to-a-container"></a>JSON öğelerini bir kapsayıcıya yükleme
``%%upload``BIR JSON dosyasındaki verileri belirtilen Azure Cosmos kapsayıcısına yüklemek için Magic komutunu kullanabilirsiniz. Öğeleri karşıya yüklemek için aşağıdaki komutu kullanın:

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- ``{database_id}``Ve ' i ``{container_id}`` Azure Cosmos hesabınızdaki veritabanı ve kapsayıcının adıyla değiştirin. 
- ``{url_location_of_file}``JSON dosyanızın konumuyla değiştirin. Dosya geçerli bir JSON nesneleri dizisi olmalıdır ve bu, genel Internet üzerinden erişilebilir olmalıdır.

Örnek:

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

Çıkış istatistikleriyle, öğeleri karşıya yüklemek için kullanılan geçerli RU/s 'yi hesaplayabilirsiniz. Örneğin, 38 saniyenin üzerinde 25.000 ru kullanılıyorsa, geçerli RU/s, 25.000 ru/38 saniye = 658 RU/s olur.

## <a name="run-another-notebook-in-current-notebook"></a>Geçerli not defterinde başka bir not defteri Çalıştır 
``%%run``Geçerli not defterinizdeki çalışma alanınızda başka bir not defteri çalıştırmak için Magic komutunu kullanabilirsiniz. Sözdizimini kullanın:

```csharp
%%run ./path/to/{notebookName}.ipynb
```
``{notebookName}``Çalıştırmak istediğiniz Not defterinin adıyla değiştirin. Not defteri, geçerli ' My not defterim ' çalışma alanınızda olmalıdır. 

## <a name="reset-notebooks-workspace"></a>Not defteri çalışma alanını Sıfırla
Not defteri çalışma alanını varsayılan ayarlara sıfırlamak için komut çubuğundan **çalışma alanını Sıfırla** ' yı seçin. Bu, tüm özel yüklü paketleri kaldıracak ve jupi sunucusunu yeniden başlatacak. Not defterleriniz, dosyalarınız ve Azure Cosmos kaynaklarınızın etkilenmemesi gerekir.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Not defteri çalışma alanını Sıfırla":::

## <a name="next-steps"></a>Sonraki adımlar

- [Jupyıter Not defterlerinin Azure Cosmos DB](cosmosdb-jupyter-notebooks.md) avantajları hakkında bilgi edinin
- [SQL API için Azure Cosmos DB .NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) hakkında bilgi edinin
