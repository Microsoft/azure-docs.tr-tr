---
title: .NET Standard SDK kullanarak Tablo API'si Azure Cosmos DB
description: Azure Cosmos DB Tablo API'si hesabında yapılandırılmış verileri nasıl depolayacağınızı ve sorguleyeceğinizi öğrenin
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: f4817e53cb36bc96add19d855750a73a151b4891
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772306"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>.NET SDK'sı ile Azure Cosmos DB Tablo API'si'ni ve Azure Tablo depolama alanını kullanmaya başlama
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Yapılandırılmış NoSQL verilerini bulutta depolamak için Azure Cosmos DB Tablo API'si veya Azure Tablo depolama alanını kullanabilir, böylece şema daha az tasarıma sahip bir anahtar/öznitelik deposu sağlayabilirsiniz. Azure Cosmos DB Tablo API'si ve tablo depolaması şema daha az olduğundan, uygulamanızın ihtiyaçları geliştikçe verilerinizi kolayca uyarlayabilirsiniz. Web uygulamaları için Kullanıcı verileri, adres defterleri, cihaz bilgileri veya hizmetinizin gerektirdiği diğer meta veri türleri gibi esnek veri kümelerini depolamak için Azure Cosmos DB Tablo API'si veya tablo depolama alanını kullanabilirsiniz. 

Bu öğreticide, Azure Cosmos DB Tablo API'si ve Azure Tablo depolama senaryolarıyla [.NET için Microsoft Azure Cosmos db tablo kitaplığının](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) nasıl kullanılacağını gösteren bir örnek açıklanmaktadır. Azure hizmetine özel bağlantıyı kullanmanız gerekir. Bu senaryolar, tablo oluşturmayı, veri eklemeyi/güncelleştirmeyi, verileri sorgulamayı ve tabloları silmeyi gösteren C# örnekleri kullanılarak araştırılabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu örneği başarıyla tamamlamak için aşağıdakiler gerekir:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [.Net Için CosmosDB tablo kitaplığı Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) -bu kitaplık, .NET Standard ve .NET Framework 'te Şu anda kullanılabilir. 

* [Azure Cosmos DB tablo API'si hesabı](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Tablo API’si hesabı oluşturma

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>.NET konsol projesi oluşturma

Visual Studio 'da yeni bir .NET konsol uygulaması oluşturun. Aşağıdaki adımlarda, Visual Studio 2019 ' de bir konsol uygulamasının nasıl oluşturulacağı gösterilmektedir. Azure Cosmos DB tablo kitaplığını, bir Azure bulut hizmeti veya Web uygulaması, masaüstü ve mobil uygulamalar dahil olmak üzere herhangi bir türde .NET uygulamasında kullanabilirsiniz. Bu kılavuzda, sadeleştirmek için konsol uygulaması kullanmaktayız.

1. **Dosya**  >  **Yeni**  >  **Proje**' yi seçin.

1. **Konsol uygulaması (.NET Core)** öğesini seçin ve ardından **İleri**' yi seçin.

1. **Proje adı** alanında, uygulamanız Için **Cosmostablesamples** gibi bir ad girin. (Gerektiğinde farklı bir ad sağlayabilirsiniz.)

1. **Oluştur**’u seçin.

Bu örnekteki tüm kod örnekleri konsol uygulamanızın **program. cs** dosyasının Main () yöntemine eklenebilir.

## <a name="install-the-required-nuget-package"></a>Gerekli NuGet paketini yükler

NuGet paketini edinmek için aşağıdaki adımları izleyin:

1. **Çözüm Gezgini**'nde projenize sağ tıklayın ve **NuGet Paketlerini Yönet**’i seçin.

1. ,, Ve için çevrimiçi olarak arama yapın [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) ve Microsoft Azure Cosmos db tablo kitaplığını yüklemek için **yüklemeyi** seçin.

## <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma

1. [Azure Portal](https://portal.azure.com/)Azure Cosmos hesabınıza veya tablo depolama hesabına gidin. 

1. **Bağlantı dizesini** veya **erişim tuşları** bölmesini açın. Pencerenin sağ tarafındaki kopyala düğmesini kullanarak **PRIMARY CONNECTION STRING**'i kopyalayın.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Bağlantı Dizesi bölmesindeki PRIMARY CONNECTION STRING’i görüntüleyin ve kopyalayın":::
   
1. Bağlantı dizenizi yapılandırmak için, Visual Studio 'dan projenizin **Cosmostablesamples** öğesine sağ tıklayın.

1. **Ekle** ' yi ve ardından **Yeni öğe**' yi seçin. Dosya türü ile **TYPESCRIPT JSON yapılandırma** dosyası olarak **Settings.js** yeni bir dosya oluşturun. 

1. Dosyadaki Settings.jskodundaki kodu aşağıdaki kodla değiştirin ve birincil Bağlantı dizenizi atayın:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Projenizin **Cosmostablesamples** öğesine sağ tıklayın. **Ekle**, **Yeni öğe** ' yi seçin ve **appSettings. cs** adlı bir sınıf ekleyin.

1. Aşağıdaki kodu AppSettings. cs dosyasına ekleyin. Bu dosya Settings.jsdosyadaki bağlantı dizesini okur ve onu yapılandırma parametresine atar:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/AppSettings.cs":::

## <a name="parse-and-validate-the-connection-details"></a>Bağlantı ayrıntılarını ayrıştırma ve doğrulama

1. Projenizin **Cosmostablesamples** öğesine sağ tıklayın. **Ekle**, **Yeni öğe** ' yi seçin ve **ortak. cs** adlı bir sınıf ekleyin. Bağlantı ayrıntılarını doğrulamak ve bu sınıf içinde bir tablo oluşturmak için kod yazacaksınız.

1. `CreateStorageAccountFromConnectionString`Aşağıda gösterildiği gibi bir yöntem tanımlayın. Bu yöntem, bağlantı dizesi ayrıntılarını ayrıştırır ve "Settings.json" dosyasında sunulan hesap adı ve hesap anahtarı ayrıntılarının geçerli olduğunu doğrular.

   :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="createStorageAccount":::

## <a name="create-a-table"></a>Tablo oluşturma 

[CloudTableClient](/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) sınıfı, Tablo Depolamada depolanan tabloları ve varlıkları almanızı sağlar. Cosmos DB Tablo API'si hesabında hiç tablo olmadığı `CreateTableAsync` için, bir tablo oluşturmak için metodu **Common. cs** sınıfına ekleyelim:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

"503 hizmeti kullanılamayan özel durum" hatası alırsanız, bağlantı modu için gereken bağlantı noktaları bir güvenlik duvarı tarafından engelleniyor olabilir. Bu sorunu onarmak için gerekli bağlantı noktalarını açın ya da aşağıdaki kodda gösterildiği gibi ağ geçidi modu bağlantısını kullanın:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Varlığı tanımlama 

Varlıklar, [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity)’den oluşturulan özel bir sınıf kullanarak C# nesneleriyle eşlenir. Tabloya bir varlık eklemek için varlığınızın özelliklerini tanımlayan bir sınıf oluşturun.

Projenizin **Cosmostablesamples** öğesine sağ tıklayın. **Ekle**, **Yeni klasör** ' ü seçin ve **model** olarak adlandırın. Model klasörü içinde **Customerentity. cs** adlı bir sınıf ekleyin ve bu sınıfa aşağıdaki kodu ekleyin.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Model/CustomerEntity.cs":::

Bu kod, satır anahtarı olarak müşterinin adını ve bölüm anahtarı olarak soyadı ' nı kullanan bir varlık sınıfını tanımlar. Birlikte, bir varlığın bölüm ve sıra anahtarı varlığı tabloda benzersiz şekilde tanımlar. Aynı bölüm anahtarına sahip varlıklar farklı bölüm anahtarları olan varlıklardan daha hızlı sorgulanabilir ancak farklı bölüm anahtarlarının kullanılması paralel işlemler için daha fazla ölçeklenebilirlik sağlar. Tablolarda depolanacak varlıklar, [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity) sınıfından türetilen türler gibi desteklenen bir türde olmalıdır. Bir tabloda depolamak istediğiniz varlık özellikleri, türün genel özellikleri olmalı ve değerleri hem almayı hem de ayarlamayı desteklemelidir. Bununla birlikte varlık türü parametresiz bir oluşturucu olmalıdır.

## <a name="insert-or-merge-an-entity"></a>Varlık ekleme veya birleştirme

Aşağıdaki kod örneği bir varlık nesnesi oluşturur ve onu tabloya ekler. [Tableoperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) sınıfı Içindeki ınsertormerge yöntemi bir varlık eklemek veya birleştirmek için kullanılır. İşlemi yürütmek için [CloudTable.ExecuteAsync](/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync) yöntemi çağırılır. 

Projenizin **Cosmostablesamples** öğesine sağ tıklayın. **Ekle**, **Yeni öğe** ' yi seçin ve **samplesutils. cs** adlı bir sınıf ekleyin. Bu sınıf, varlıklarda CRUD işlemleri gerçekleştirmek için gereken tüm kodu depolar. 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

## <a name="get-an-entity-from-a-partition"></a>Bir bölümden varlık edinme

[Tableoperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) sınıfının altındaki Retrieve metodunu kullanarak bir bölümden varlık elde edebilirsiniz. Aşağıdaki kod örneği, bir müşteri varlığının bölüm anahtarı satır anahtarını, e-posta ve telefon numarasını alır. Bu örnek ayrıca varlık için sorgulamak üzere tüketilen istek birimlerini de yazdırır. Bir varlığı sorgulamak için **Samplesutils. cs** dosyasına aşağıdaki kodu ekleyin:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

## <a name="delete-an-entity"></a>Bir varlığı silme

Bir varlığı güncelleştirmek için gösterilen aynı yöntemi kullanarak, bir varlığı aldıktan sonra kolayca silebilirsiniz. Aşağıdaki kod bir müşteri girişini alır ve siler. Bir varlığı silmek için **Samplesutils. cs** dosyasına aşağıdaki kodu ekleyin: 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="execute-the-crud-operations-on-sample-data"></a>Örnek verilerde CRUD işlemlerini yürütün

Tablo oluşturma, varlık ekleme veya birleştirme yöntemlerini tanımladıktan sonra, bu yöntemleri örnek verilerde çalıştırın. Bunu yapmak için, projenizin **Cosmostablesamples** öğesine sağ tıklayın. **Ekle**, **Yeni öğe** ' yi seçin ve **basicsamples. cs** adlı bir sınıf ekleyin ve bu sınıfa aşağıdaki kodu ekleyin. Bu kod, bir tablo oluşturur ve ona varlıklar ekler.

Projenin sonundaki varlık ve tabloyu silmek istemiyorsanız, `await table.DeleteIfExistsAsync()` aşağıdaki koddan ve yöntemlerini açıklama olarak yapın `SamplesUtils.DeleteEntityAsync(table, customer)` . Tabloyu silmeden önce bu yöntemleri açıklamaya ve verileri doğrulamaya en iyisidir.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/BasicSamples.cs":::

Önceki kod, "Demo" ile başlayan bir tablo oluşturur ve oluşturulan GUID tablo adına eklenir. Ardından, ilk ve son adı "Harp Walter" olarak bir müşteri varlığı ekler ve daha sonra bu kullanıcının telefon numarasını günceller. 

Bu öğreticide, Tablo API'si hesapta depolanan veriler üzerinde temel CRUD işlemlerini gerçekleştirmek için kod derlediniz. Ayrıca, – toplu veri ekleme, bir bölümdeki tüm verileri sorgulama, bir bölüm içindeki veri aralığını sorgulama ve adları belirtilen önekle başlayan hesaptaki tabloları listeleyen gibi gelişmiş işlemleri de gerçekleştirebilirsiniz. Tam örnek form olan [Azure-Cosmos-Table-DotNet-Core-Başlarken](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub deposunu indirebilirsiniz. [Advancedsamples. cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/main/CosmosTableSamples/AdvancedSamples.cs) sınıfında veri üzerinde gerçekleştirebileceğiniz daha fazla işlem vardır.  

## <a name="run-the-project"></a>Projeyi çalıştırma

Proje **Cosmostablesamples**. **Program. cs** adlı sınıfı açın ve proje çalışırken basicsamples ' i çağırmak için aşağıdaki kodu ekleyin.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Program.cs":::

Şimdi Çözümü derleyin ve F5 tuşuna basarak projeyi çalıştırın. Proje çalıştırıldığında, komut isteminde aşağıdaki çıktıyı görürsünüz:

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Komut isteminden çıkış":::

Projeyi çalıştırırken Settings.jsdosya bulunamıyorsa bir hata alırsanız, proje ayarlarına aşağıdaki XML girişini ekleyerek bu sorunu çözebilirsiniz. CosmosTableSamples öğesine sağ tıklayın, CosmosTableSamples. csproj öğesini Düzenle ' yi seçin ve şu ItemGroup 'u ekleyin: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Artık Azure portal oturum açabilir ve verilerin tabloda var olduğunu doğrulayabilirsiniz. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Portalda sonuçlar":::

## <a name="next-steps"></a>Sonraki adımlar

Artık sonraki öğreticiye devam edebilir ve verileri Azure Cosmos DB Tablo API'si hesabına geçirmeyi öğrenebilirsiniz. 

> [!div class="nextstepaction"]
>[Verileri Azure Cosmos DB Tablo API'si geçirme](../cosmos-db/table-import.md)
