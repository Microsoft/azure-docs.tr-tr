---
title: Azure Blob 'larında AI kullanan C# öğreticisi
titleSuffix: Azure Cognitive Search
description: C# ve Azure Bilişsel Arama .NET SDK kullanarak blob depolamada içerik üzerinde metin ayıklama ve doğal dil işleme örneğini adım adım yapın.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cbfa01788f1897264b4197798aa5879cf724db3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104770963"
---
# <a name="tutorial-use-net-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Öğretici: Azure Bloblarından aranabilir içerik oluşturmak için .NET ve AI kullanma

Azure Blob depolamada yapılandırılmamış metin veya görüntü varsa, bir [AI zenginleştirme işlem hattı](cognitive-search-concept-intro.md) , tam metin arama veya bilgi araştırma senaryoları için bilgileri ayıklayabilir ve yeni içerik oluşturabilir. 

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Geliştirme ortamı ayarlama
> * OCR, dil algılama ve varlık ve anahtar tümceciği tanımayı kullanan bir işlem hattı tanımlayın.
> * Dönüşümleri çağırmak ve bir arama dizini oluşturmak ve yüklemek için işlem hattını yürütün.
> * Tam metin aramasını ve zengin sorgu söz dizimini kullanarak sonuçları keşfedebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) açın.

## <a name="overview"></a>Genel Bakış

Bu öğretici, bir veri kaynağı, dizin, Dizin Oluşturucu ve beceri oluşturmak için C# ve [ **Azure.Search.Documstalar** istemci kitaplığını](/dotnet/api/overview/azure/search.documents-readme) kullanır.

Dizin Oluşturucu, veri kaynağı nesnesinde belirtilen bir blob kapsayıcısına bağlanır ve tüm dizine alınmış içeriği mevcut bir arama dizinine gönderir.

Beceri, dizin oluşturucuya eklenir. Bilgileri bulmak ve ayıklamak için Microsoft 'un yerleşik yeteneklerini kullanır. İşlem hattındaki adımlarda, resimlerde optik karakter tanıma (OCR), metinde dil algılama, anahtar ifade ayıklama ve varlık tanıma (kuruluşlar) bulunur. İşlem hattı tarafından oluşturulan yeni bilgiler, bir dizindeki yeni alanlara depolanır. Dizin doldurulduktan sonra sorgular, modeller ve filtrelerdeki alanları kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)
* [Azure.Search.Doc10. x NuGet paketi](https://www.nuget.org/packages/Azure.Search.Documents) 
* [Azure Depolama](https://azure.microsoft.com/services/storage/)
* [Azure Bilişsel Arama](https://azure.microsoft.com/services/search/)

> [!Note]
> Bu öğretici için ücretsiz arama hizmetini kullanabilirsiniz. Ücretsiz arama hizmeti, sizi üç Dizin, üç Dizin Oluşturucu ve üç veri kaynağı ile sınırlandırır. Bu öğreticide hepsinden birer tane oluşturulur. Başlamadan önce, hizmetinize yeni kaynakları kabul etmek için yeriniz olduğundan emin olun.

## <a name="download-sample-data"></a>Örnek verileri indirme

Örnek veriler, daha sonraki bir adımda Azure Blob depolamaya karşıya yükleyeceğiniz, karışık içerik türünden oluşan 14 dosyadan oluşur.

1. Bu [OneDrive klasörünü](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) açın ve sol üst köşedeki dosyaları bilgisayarınıza kopyalamak için **İndir** ' e tıklayın. 

1. ZIP dosyasına sağ tıklayın ve **Tümünü Ayıkla**' yı seçin. Çeşitli türlerde 14 dosya vardır. Bu alıştırma için 7 kullanacaksınız.

Ayrıca, Bu öğreticinin kaynak kodunu indirebilirsiniz. Kaynak kodu, [Azure-Search-DotNet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) deposundaki **öğretici-AI-Enrichment/v11** klasöründedir.

## <a name="1---create-services"></a>1-hizmet oluşturma

Bu öğreticide dizin oluşturma ve sorgular, AI zenginleştirme için arka uçta bilişsel hizmetler ve verileri sağlamak üzere Azure Blob depolama için Azure Bilişsel Arama kullanılmaktadır. Bu öğretici, bilişsel hizmetler 'de her gün Dizin Oluşturucu başına 20 işlem için ücretsiz ayırma kapsamında kalır, bu nedenle yalnızca oluşturmanız gereken hizmetler arama ve depolama amaçlıdır.

Mümkünse, yakınlık ve yönetilebilirlik için aynı bölgede ve kaynak grubunda her ikisini de oluşturun. Uygulamada, Azure depolama hesabınız herhangi bir bölgede olabilir.

### <a name="start-with-azure-storage"></a>Azure Storage 'ı kullanmaya başlama

1. [Azure Portal oturum açın](https://portal.azure.com/) ve **+ kaynak oluştur**' a tıklayın.

1. *Depolama hesabı* araması yapın ve Microsoft 'un depolama hesabı teklifi ' ni seçin.

   ![Depolama hesabı oluştur](media/cognitive-search-tutorial-blob/storage-account.png "Depolama hesabı oluştur")

1. Temel bilgiler sekmesinde, aşağıdaki öğeler gereklidir. Diğer her şey için varsayılanları kabul edin.

   * **Kaynak grubu**. Mevcut bir tane seçin veya yeni bir tane oluşturun, ancak bunları topluca yönetebilmeniz için tüm hizmetler için aynı grubu kullanın.

   * **Depolama hesabı adı**. Aynı türde birden fazla kaynağınız olabileceğini düşünüyorsanız, tür ve bölgeye göre belirsizliği ortadan kaldırmak için adı kullanın, örneğin *blobstoragewestus*. 

   * **Konum**. Mümkünse, Azure Bilişsel Arama ve bilişsel hizmetler için kullanılan aynı konumu seçin. Tek bir konum, bant genişliği ücretlerini oylar.

   * **Hesap türü**. Varsayılan, *StorageV2 (genel amaçlı v2)* seçeneğini belirleyin.

1. Hizmeti oluşturmak için **gözden geçir + oluştur** ' a tıklayın.

1. Oluşturulduktan sonra genel bakış sayfasını açmak için **Kaynağa Git** ' e tıklayın.

1. **Bloblar** hizmeti ' ne tıklayın.

1. Bir kapsayıcı oluşturmak ve *COG-Search-demo* olarak adlandırmak Için **+ kapsayıcı** ' ya tıklayın.

1. *COG-Search-demo* ' i seçin ve ardından yükleme dosyalarını kaydettiğiniz klasörü açmak Için **karşıya yükle** ' ye tıklayın. Tüm on dört dosyayı seçip karşıya yüklemek üzere **Tamam** ' a tıklayın.

   ![Örnek dosyaları karşıya yükle](media/cognitive-search-quickstart-blob/sample-data.png "Örnek dosyaları karşıya yükle")

1. Azure depolama alanını kapatmadan önce Azure Bilişsel Arama bir bağlantıyı formülleştirmek için bir bağlantı dizesi alın. 

   1. Depolama hesabınızın genel bakış sayfasına geri gidin (örnek olarak *blobstoragewestus* kullandık). 

   1. Sol gezinti bölmesinde **erişim anahtarları** ' nı seçin ve bağlantı dizelerinden birini kopyalayın. 

   Bağlantı dizesi, aşağıdaki örneğe benzer bir URL 'dir:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Bağlantı dizesini Not defteri 'ne kaydedin. Daha sonra veri kaynağı bağlantısını ayarlarken gerekecektir.

### <a name="cognitive-services"></a>Bilişsel Hizmetler

AI zenginleştirme, doğal dil ve görüntü işleme için Metin Analizi ve Görüntü İşleme dahil bilişsel hizmetler tarafından desteklenir. Amacınız gerçek bir prototipi veya projeyi tamamlayacaksa, bu noktada bilişsel hizmetler sağlama (Azure Bilişsel Arama ile aynı bölgede), böylece dizin oluşturma işlemlerine iliştirebilirsiniz.

Bununla birlikte, Azure Bilişsel Arama, arka planda bilişsel hizmetlere bağlanıp Dizin Oluşturucu başına 20 ücretsiz işlem sunabileceğinden kaynak sağlamayı atlayabilirsiniz. Bu öğretici 14 işlem kullandığından, ücretsiz ayırma yeterlidir. Daha büyük projeler için, Kullandıkça öde, bu hizmetleri, Kullandıkça öde, bu hizmetler için sağlama bölümüne planlayın. Daha fazla bilgi için bkz. bilişsel [Hizmetler iliştirme](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Bilişsel Arama

Üçüncü bileşen, [portalda oluşturabileceğiniz](search-create-service-portal.md) veya aboneliğinizde [mevcut bir arama hizmeti bulabileceğiniz](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Azure bilişsel arama.

Bu izlenecek yolu tamamlamak için ücretsiz katmanı kullanabilirsiniz. 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Bilişsel Arama için bir yönetici API-anahtarı ve URL 'SI kopyalama

Azure Bilişsel Arama hizmetiyle etkileşim kurmak için hizmet URL 'SI ve erişim anahtarı gerekir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

1. **Ayarlar**  >  **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı kopyalayın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

   Sorgu anahtarını da alın. Salt okuma erişimiyle sorgu istekleri vermek en iyi uygulamadır.

   ![Hizmet adı ve yönetici ve sorgu anahtarlarını alın](media/search-get-started-javascript/service-name-and-keys.png)

İstek başına geçerli bir anahtara sahip olmak, isteği gönderen uygulama ve bunu işleyen hizmet arasında güven oluşturur.

## <a name="2---set-up-your-environment"></a>2-ortamınızı ayarlama

Visual Studio 'Yu açıp .NET Core üzerinde çalışabilen yeni bir konsol uygulama projesi oluşturarak başlayın.

### <a name="install-azuresearchdocuments"></a>Azure.Search.Documtları yüklensin

[Azure bilişsel arama .NET SDK](/dotnet/api/overview/azure/search) , dizinlerinizi, veri kaynaklarınızı, Dizin Oluşturucularınızı ve becerileri yönetmenizi sağlayan bir istemci kitaplığından oluşur ve bu sayede, belgeleri karşıya yükleyip yönetebilir ve sorguları YÜRÜTEBILIR, http ve JSON ayrıntıları ile uğraşmanıza gerek kalmadan yapılır. Bu istemci kitaplığı, bir NuGet paketi olarak dağıtılır.

Bu proje için, `Azure.Search.Documents` ve en son sürümünün sürüm 11 ' i ve sonraki sürümlerini yükler `Microsoft.Extensions.Configuration` .

1. Visual Studio 'da **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **çözüm için NuGet Paketlerini Yönet...** ' i seçin.

1. [Azure.Search.Doc](https://www.nuget.org/packages/Azure.Search.Documents)için göz atabilirsiniz.

1. En son sürümü seçip, ardından **yükler**' i tıklatın.

1. [Microsoft.Extensions.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) ve [Microsoft.Extensions.Configuration.Js](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json)yüklemek için önceki adımları tekrarlayın.

### <a name="add-service-connection-information"></a>Hizmet bağlantı bilgileri ekleme

1. Çözüm Gezgini projenize sağ tıklayıp   >  **Yeni öğe Ekle...** seçeneğini belirleyin. 

1. Dosyayı adlandırın `appsettings.json` ve **Ekle**' yi seçin. 

1. Bu dosyayı çıkış dizininize dahil edin.
    1. Sağ tıklayın `appsettings.json` ve **Özellikler**' i seçin. 
    1. **Daha yeniyse**, **çıkış dizinine kopyalanacak kopya** değerini değiştirin.

1. Aşağıdaki JSON dosyasını yeni JSON dosyanıza kopyalayın.

    ```json
    {
      "SearchServiceUri": "Put your search service URI here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Arama hizmetinizi ve BLOB depolama hesabı bilgilerinizi ekleyin. Bu bilgileri, önceki bölümde belirtilen hizmet sağlama adımlarından alabileceğiniz şekilde geri çekin.

**Searchserviceuri** için tam URL 'yi girin.

### <a name="add-namespaces"></a>Ad alanı Ekle

İçinde `Program.cs` , aşağıdaki ad alanlarını ekleyin.

```csharp
using Azure;
using Azure.Search.Documents.Indexes;
using Azure.Search.Documents.Indexes.Models;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.Linq;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>İstemci oluşturma

' A ve ' ın bir örneğini oluşturun `SearchIndexClient` `SearchIndexerClient` `Main` .

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    string searchServiceUri = configuration["SearchServiceUri"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];
    string cognitiveServicesKey = configuration["CognitiveServicesKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
    SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
}
```

> [!NOTE]
> İstemciler, arama hizmetinize bağlanır. Çok fazla bağlantı açmamak için, mümkünse uygulamanızda tek bir örnek paylaşmayı denemelisiniz. Bu tür paylaşımları etkinleştirmek için iş parçacığı güvenlidir.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Hata sırasında programdan çıkmak için işlev Ekle

Bu öğretici, dizin oluşturma işlem hattının her adımını anlamanıza yardımcı olmak için tasarlanmıştır. Programın veri kaynağı, Beceri, dizin veya dizin oluşturucuyu oluşturmasını önleyen kritik bir sorun varsa, sorun anlaşılması ve giderilmesi için, program hata iletisini ve çıkış çıkışını çıktı olarak çıkar.

`ExitProgram` `Main` Programın çıkmasına gerek duyduğu senaryoları işlemek için öğesine ekleyin.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3-işlem hattını oluşturma

Azure Bilişsel Arama 'de, dizin oluşturma (veya veri alımı) sırasında AI işleme oluşur. İzlenecek yolun bu bölümü dört nesne oluşturur: veri kaynağı, Dizin tanımı, Beceri, Dizin Oluşturucu. 

### <a name="step-1-create-a-data-source"></a>1. Adım: Veri kaynağı oluşturma

`SearchIndexerClient` , [`DataSourceName`](/dotnet/api/azure.search.documents.indexes.models.searchindexer.datasourcename) nesnesine ayarlayabileceğiniz bir özelliğine sahiptir `SearchIndexerDataSourceConnection` . Bu nesne, Azure Bilişsel Arama veri kaynaklarını oluşturmak, listelemek, güncelleştirmek veya silmek için gereken tüm yöntemleri sağlar.

Çağırarak yeni bir `SearchIndexerDataSourceConnection` örnek oluşturun `indexerClient.CreateOrUpdateDataSourceConnection(dataSource)` . Aşağıdaki kod, türünde bir veri kaynağı oluşturur `AzureBlob` .

```csharp
private static SearchIndexerDataSourceConnection CreateOrUpdateDataSource(SearchIndexerClient indexerClient, IConfigurationRoot configuration)
{
    SearchIndexerDataSourceConnection dataSource = new SearchIndexerDataSourceConnection(
        name: "demodata",
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["AzureBlobConnectionString"],
        container: new SearchIndexerDataContainer("cog-search-demo"))
    {
        Description = "Demo files to demonstrate cognitive search capabilities."
    };

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

Başarılı bir istek için, yöntem oluşturulan veri kaynağını döndürür. İstekle ilgili bir sorun varsa (geçersiz parametre gibi), yöntem bir özel durum oluşturur.

Şimdi `Main` yeni eklediğiniz işlevi çağırmak için içine bir satır ekleyin `CreateOrUpdateDataSource` .

```csharp
// Create or Update the data source
Console.WriteLine("Creating or updating the data source...");
SearchIndexerDataSourceConnection dataSource = CreateOrUpdateDataSource(indexerClient, configuration);
```

Çözümü derleyin ve çalıştırın. Bu ilk isteğiniz olduğundan, veri kaynağının Azure Bilişsel Arama oluşturulduğunu onaylamak için Azure portal denetleyin. Arama hizmeti genel bakış sayfasında, veri kaynakları listesinin yeni bir öğe olduğunu doğrulayın. Portal sayfasının yenilenmesi için birkaç dakika beklemeniz gerekebilir.

  ![Portalda veri kaynakları kutucuğu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Portalda veri kaynakları kutucuğu")

### <a name="step-2-create-a-skillset"></a>2. Adım: beceri oluşturma

Bu bölümde, verilerinize uygulamak istediğiniz bir zenginleştirme adımları kümesi tanımlarsınız. Her bir zenginleştirme adımını bir *yetenek* ve bir *beceri*, enzenginleştirme adımları kümesi olarak adlandırılır. Bu öğretici, beceri için yerleşik bilişsel [becerileri](cognitive-search-predefined-skills.md) kullanır:

* Görüntü dosyalarında yazdırılmış ve el yazısı metinleri tanımak için [optik karakter tanıma](cognitive-search-skill-ocr.md) .

* Bir alanlar koleksiyonundan tek bir alanda metin birleştirmek için [metin Merkli](cognitive-search-skill-textmerger.md) .

* İçeriğin dilini tanımlamak için [Dil Algılama](cognitive-search-skill-language-detection.md).

* Anahtar ifadesi ayıklama yeteneği ve varlık tanıma yeteneği çağrılmadan önce büyük içeriği daha küçük parçalara bölmek için [metin bölme](cognitive-search-skill-textsplit.md) . Anahtar tümceciği ayıklama ve varlık tanıma, 50.000 veya daha az karakter girişi kabul eder. Bu sınıra uymak için örnek dosyaların birkaç tanesinin bölünmesi gerekir.

* Blob kapsayıcısında bulunan içerikten kuruluşların adlarını ayıklamak için [varlık tanıma](cognitive-search-skill-entity-recognition.md) .

* Üst anahtar tümcecikleri çekmek için [Anahtar İfade Ayıklama](cognitive-search-skill-keyphrases.md).

Azure Bilişsel Arama, ilk işleme sırasında her bir belgeyi, farklı dosya biçimlerinden içerik ayıklayarak çıkartır. Kaynak dosyadaki kaynaklı metin `content` , her belge için bir tane olmak üzere oluşturulan bir alana yerleştirilir. Bu nedenle, girdiyi `"/document/content"` Bu metni kullanacak şekilde ayarlayın. Görüntü içeriği `normalized_images` , beceri olarak belirtilen oluşturulmuş bir alana yerleştirilir `/document/normalized_images/*` .

Çıktılar bir dizine eşlenebilir, aşağı akış becerisine yönelik giriş olarak kullanılır veya dil kodunda olduğu gibi her iki şekilde de kullanılabilir. Dizinde bir dil kodu, filtreleme için yararlıdır. Giriş olarak dil kodu, sözcük bölünmesiyle ilgili dilbilgisi kurallarını bildirmek için metin analizi becerileri tarafından kullanılır.

Beceri kümesi temelleri hakkında daha fazla bilgi için bkz. [Beceri kümesini tanımlama](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>OCR becerisi

**OCR** becerisi görüntülerden metin ayıklar. Bu beceri bir normalized_images alanının bulunduğunu varsayar. Bu alanı oluşturmak için, öğreticide daha sonra, ```"imageAction"``` Dizin Oluşturucu tanımındaki yapılandırmayı olarak ayarlayacağız ```"generateNormalizedImages"``` .

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("image")
    {
        Source = "/document/normalized_images/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("text")
    {
        TargetName = "text"
    });

    OcrSkill ocrSkill = new OcrSkill(inputMappings, outputMappings)
    {
        Description = "Extract text (plain and structured) from image",
        Context = "/document/normalized_images/*",
        DefaultLanguageCode = OcrSkillLanguage.En,
        ShouldDetectOrientation = true
    };

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Birleştirme yeteneği

Bu bölümde, belge içerik alanını OCR becerisi tarafından üretilen metinle birleştiren bir **birleştirme** yeteneği oluşturacaksınız.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/content"
    });
    inputMappings.Add(new InputFieldMappingEntry("itemsToInsert")
    {
        Source = "/document/normalized_images/*/text"
    });
    inputMappings.Add(new InputFieldMappingEntry("offsets")
    {
        Source = "/document/normalized_images/*/contentOffset"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("mergedText")
    {
        TargetName = "merged_text"
    });

    MergeSkill mergeSkill = new MergeSkill(inputMappings, outputMappings)
    {
        Description = "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        Context = "/document",
        InsertPreTag = " ",
        InsertPostTag = " "
    };

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Dil algılama yeteneği

**Dil algılama** Beceri, giriş metninin dilini algılar ve istekte gönderilen her belge için tek bir dil kodu bildirir. **Dil algılama** beceriye ait çıktıyı **metin bölünmüş** beceriye girdinin bir parçası olarak kullanacağız.

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("languageCode")
    {
        TargetName = "languageCode"
    });

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(inputMappings, outputMappings)
    {
        Description = "Detect the language used in the document",
        Context = "/document"
    };

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Metin bölünmüş yetenek

Aşağıdaki **bölünmüş** Beceri, metni sayfalara göre böler ve sayfa uzunluğu ile 4.000 karakter arasında bir şekilde sınırlayacaktır `String.Length` . Algoritma, metni en fazla boyuttaki parçalara bölmeye çalışır `maximumPageLength` . Bu durumda, algoritma bir cümle sınırında tümceyi bölmek en iyi şekilde yapılır, bu nedenle öbek boyutu daha az olabilir `maximumPageLength` .

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("textItems")
    {
        TargetName = "pages",
    });

    SplitSkill splitSkill = new SplitSkill(inputMappings, outputMappings)
    {
        Description = "Split content into pages",
        Context = "/document",
        TextSplitMode = TextSplitMode.Pages,
        MaximumPageLength = 4000,
        DefaultLanguageCode = SplitSkillLanguage.En
    };

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Varlık tanıma yeteneği

Bu `EntityRecognitionSkill` örnek kategori türünü tanımak üzere ayarlanır `organization` . **Varlık tanıma** yeteneği de kategori türlerini `person` ve kullanabilirsiniz `location` .

"Bağlam" alanının bir yıldız işaretiyle ayarlanmış olduğuna ```"/document/pages/*"``` ve altındaki her sayfa için zenginleştirme adımının çağrıldığı anlamına gelir ```"/document/pages"``` .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("organizations")
    {
        TargetName = "organizations"
    });

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(inputMappings, outputMappings)
    {
        Description = "Recognize organizations",
        Context = "/document/pages/*",
        DefaultLanguageCode = EntityRecognitionSkillLanguage.En
    };
    entityRecognitionSkill.Categories.Add(EntityCategory.Organization);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Anahtar tümceciği ayıklama yeteneği

`EntityRecognitionSkill`Yeni oluşturulan örnek gibi **anahtar ifade ayıklama** Beceri, belgenin her sayfası için çağrılır.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("keyPhrases")
    {
        TargetName = "keyPhrases"
    });

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(inputMappings, outputMappings)
    {
        Description = "Extract the key phrases",
        Context = "/document/pages/*",
        DefaultLanguageCode = KeyPhraseExtractionSkillLanguage.En
    };

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Beceri oluşturma ve oluşturma

`Skillset`Oluşturduğunuz becerileri kullanarak oluşturun.

```csharp
private static SearchIndexerSkillset CreateOrUpdateDemoSkillSet(SearchIndexerClient indexerClient, IList<SearchIndexerSkill> skills,string cognitiveServicesKey)
{
    SearchIndexerSkillset skillset = new SearchIndexerSkillset("demoskillset", skills)
    {
        Description = "Demo skillset",
        CognitiveServicesAccount = new CognitiveServicesAccountKey(cognitiveServicesKey)
    };

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateSkillset(skillset);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Aşağıdaki satırları öğesine ekleyin `Main` .

```csharp
// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();
EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
SplitSkill splitSkill = CreateSplitSkill();
KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

### <a name="step-3-create-an-index"></a>3. Adım: Dizin oluşturma

Bu bölümde, aranabilir dizine dahil edilecek alanları ve her bir alana ilişkin arama özniteliklerini belirterek dizin şemasını tanımlarsınız. Alanlar bir türe sahiptir ve alanın nasıl kullanıldığını (aranabilir, sıralanabilir vb.) belirleyen öznitelikleri alabilir. Bir dizindeki alan adlarının, kaynaktaki alan adlarıyla tamamen aynı olması gerekmez. Sonraki bir adımda, kaynak-hedef alanlarını bağlamak için dizin oluşturucuda alan eşlemeleri eklersiniz. Bu adım için, arama uygulamanızla ilgili alan adlandırma kurallarını kullanarak dizini tanımlayın.

Bu çalışmada aşağıdaki alanlar ve alan türleri kullanılır:

| Alan adları | Alan türleri |
| --- | --- |
| `id` | Edm.String |
| `content` | Edm.String |
| `languageCode` | Edm.String |
| `keyPhrases` | List<Edm.String> |
| `organizations` | List<Edm.String> |

#### <a name="create-demoindex-class"></a>Demoındex sınıfı oluşturma

Bu dizinin alanları bir model sınıfı kullanılarak tanımlanır. Model sınıfının her özelliği karşılık gelen dizin alanının aramayla ilgili davranışlarını belirleyen özniteliklere sahiptir. 

Model sınıfını yeni bir C# dosyasına ekleyeceğiz. Projenize sağ tıklayın ve   >  **Yeni öğe Ekle...** seçeneğini belirleyin, "sınıf" seçeneğini belirleyip dosyayı adlandırın ve `DemoIndex.cs` ardından **Ekle**' yi seçin.

`Azure.Search.Documents.Indexes`Ve ad alanlarından türleri kullanmak istediğinizi belirttiğinizden emin olun `System.Text.Json.Serialization` .

Aşağıdaki model sınıfı tanımını öğesine ekleyin `DemoIndex.cs` ve dizini oluşturacağınız aynı ad alanına ekleyin.

```csharp
using Azure.Search.Documents.Indexes;
using System.Text.Json.Serialization;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase is currently unsupported as of this writing. 
    // Replace it with JsonPropertyName
    public class DemoIndex
    {
        [SearchableField(IsSortable = true, IsKey = true)]
        [JsonPropertyName("id")]
        public string Id { get; set; }

        [SearchableField]
        [JsonPropertyName("content")]
        public string Content { get; set; }

        [SearchableField]
        [JsonPropertyName("languageCode")]
        public string LanguageCode { get; set; }

        [SearchableField]
        [JsonPropertyName("keyPhrases")]
        public string[] KeyPhrases { get; set; }

        [SearchableField]
        [JsonPropertyName("organizations")]
        public string[] Organizations { get; set; }
    }
}
```

Artık bir model sınıfı tanımladığınıza göre, bir `Program.cs` Dizin tanımını oldukça kolay bir şekilde oluşturabilirsiniz. Bu dizinin adı olacaktır `demoindex` . Bu ada sahip bir dizin zaten varsa, silinir.

```csharp
private static SearchIndex CreateDemoIndex(SearchIndexClient indexClient)
{
    FieldBuilder builder = new FieldBuilder();
    var index = new SearchIndex("demoindex")
    {
        Fields = builder.Build(typeof(DemoIndex))
    };

    try
    {
        indexClient.GetIndex(index.Name);
        indexClient.DeleteIndex(index.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified index not exist, 404 will be thrown.
    }

    try
    {
        indexClient.CreateIndex(index);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Sınama sırasında, dizini birden çok kez oluşturmayı denediğinizden emin olabilirsiniz. Bu nedenle, oluşturmak üzere olduğunuz dizinin oluşturmayı denemeden önce zaten var olup olmadığını denetleyin.

Aşağıdaki satırları öğesine ekleyin `Main` .

```csharp
// Create the index
Console.WriteLine("Creating the index...");
SearchIndex demoIndex = CreateDemoIndex(indexClient);
```

Ayırt eden başvuruyu çözümlemek için aşağıdaki using ifadesini ekleyin.

```csharp
using Index = Azure.Search.Documents.Indexes.Models;
```

Dizin kavramları hakkında daha fazla bilgi edinmek için bkz. [Dizin oluşturma (REST API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>4. Adım: Dizin Oluşturucu oluşturma ve çalıştırma

Şu ana kadar bir veri kaynağı, beceri kümesi ve dizin oluşturdunuz. Bu üç bileşen, her bir parçayı birlikte tek bir çok aşamalı işleme çeken [dizin oluşturucunun](search-indexer-overview.md) parçası olur. Bunları bir dizin oluşturucu birbirine bağlamak için alan eşlemeleri tanımlamanız gerekir.

* FieldMappings, Beceri öğesinden önce işlenir, kaynak alanları veri kaynağından bir dizindeki hedef alanlara eşleniyor. Alan adları ve türleri her iki uçta da aynıysa, hiçbir eşleme gerekmez.

* OutputFieldMappings, Beceri sonrasında işlenir ve belge çözme veya zenginleştirene kadar mevcut olmayan sourceFieldNames öğesine başvuruda bulunur. TargetFieldName, dizindeki bir alandır.

Çıkışlara girişlerin takılmalarının yanı sıra, veri yapılarını düzleştirmek için alan eşlemelerini de kullanabilirsiniz. Daha fazla bilgi için bkz. [zenginleştirilmiş alanları aranabilir bir dizine eşleme](cognitive-search-output-field-mapping.md).

```csharp
private static SearchIndexer CreateDemoIndexer(SearchIndexerClient indexerClient, SearchIndexerDataSourceConnection dataSource, SearchIndexerSkillset skillSet, SearchIndex index)
{
    IndexingParameters indexingParameters = new IndexingParameters()
    {
        MaxFailedItems = -1,
        MaxFailedItemsPerBatch = -1,
    };
    indexingParameters.Configuration.Add("dataToExtract", "contentAndMetadata");
    indexingParameters.Configuration.Add("imageAction", "generateNormalizedImages");

    SearchIndexer indexer = new SearchIndexer("demoindexer", dataSource.Name, index.Name)
    {
        Description = "Demo Indexer",
        SkillsetName = skillSet.Name,
        Parameters = indexingParameters
    };

    FieldMappingFunction mappingFunction = new FieldMappingFunction("base64Encode");
    mappingFunction.Parameters.Add("useHttpServerUtilityUrlTokenEncode", true);

    indexer.FieldMappings.Add(new FieldMapping("metadata_storage_path")
    {
        TargetFieldName = "id",
        MappingFunction = mappingFunction

    });
    indexer.FieldMappings.Add(new FieldMapping("content")
    {
        TargetFieldName = "content"
    });

    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/organizations/*")
    {
        TargetFieldName = "organizations"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/keyPhrases/*")
    {
        TargetFieldName = "keyPhrases"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/languageCode")
    {
        TargetFieldName = "languageCode"
    });

    try
    {
        indexerClient.GetIndexer(indexer.Name);
        indexerClient.DeleteIndexer(indexer.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified indexer not exist, 404 will be thrown.
    }

    try
    {
        indexerClient.CreateIndexer(indexer);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```

Aşağıdaki satırları öğesine ekleyin `Main` .

```csharp
// Create the indexer, map fields, and execute transformations
Console.WriteLine("Creating the indexer and executing the pipeline...");
SearchIndexer demoIndexer = CreateDemoIndexer(indexerClient, dataSource, skillset, demoIndex);
```

Dizin Oluşturucu işleminin tamamlanması biraz zaman alacak şekilde bekliyor. Veri kümesi küçük olsa da, analiz becerileri bilgi işlem açısından yoğundur. Görüntü analizi gibi bazı beceriler uzun sürer.

> [!TIP]
> Bir dizin oluşturucu oluşturulduğunda, işlem hattı çağrılır. Verilere ulaşılırken, eşleme girişleri ve çıktıları veya işlemlerin sırası ile ilgili sorun olursa bunlar bu aşamada görüntülenir.

### <a name="explore-creating-the-indexer"></a>Dizin oluşturucuyu oluşturmayı keşfet

Kodu `"maxFailedItems"`  -1 olarak ayarlanır. Bu, dizin oluşturma altyapısının veri içeri aktarma sırasında hataları yoksaymasına olanak verir. Demo veri kaynağında çok az belge olduğundan bu yararlıdır. Daha büyük bir veri kaynağı için değeri, 0’dan daha büyük bir değere ayarlarsınız.

Ayrıca, `"dataToExtract"` olarak ayarlandığını unutmayın `"contentAndMetadata"` . Bu deyim, dizin oluşturucuya, farklı dosya biçimlerinden içeriği ve her bir dosyayla ilgili meta verileri otomatik olarak ayıklamasını bildirir.

İçerik ayıklandığında, veri kaynağında bulunan görüntülerden metni ayıklamak için `imageAction` değerini ayarlayabilirsiniz. `"imageAction"`Yapılandırma olarak ayarlanan `"generateNormalizedImages"` , OCR yeteneği ve metin birleştirme beceriyle birlikte, dizin oluşturucunun görüntüden metin ayıklamasını söyler (örneğin, trafiğin "Durdur" sözcüğünün oturum açmasını durdurur) ve içerik alanının bir parçası olarak katıştırmasını söyler. Bu davranış hem belgelerde gömülü olan görüntüler (örneğin, bir PDF’teki görüntü) hem de veri kaynağında bulunan görüntüler (örneğin, bir JPG dosyası) için geçerlidir.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4-dizin oluşturmayı izleme

Dizin oluşturucu tanımlandıktan sonra, isteği gönderdiğinizde otomatik olarak çalıştırılır. Tanımladığınız bilişsel becerilere bağlı olarak dizin oluşturma beklediğinizden uzun sürebilir. Dizin oluşturucunun hala çalışır durumda olup olmadığını öğrenmek için `GetStatus` yöntemini kullanın.

```csharp
private static void CheckIndexerOverallStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        var demoIndexerExecutionInfo = indexerClient.GetIndexerStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Value.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", ex.Message);
    }
}
```

`demoIndexerExecutionInfo` bir dizin oluşturucunun geçerli durum ve yürütme geçmişini temsil eder.

Uyarılar bazı kaynak dosya ve beceri birleşimleri için geneldir ve her zaman bir sorunu belirtmez. Bu öğreticide, uyarılar zararsızdır (örneğin, JPEG dosyalarında bir metin girişi yok).

Aşağıdaki satırları öğesine ekleyin `Main` .

```csharp
// Check indexer overall status
Console.WriteLine("Check the indexer overall status...");
CheckIndexerOverallStatus(indexerClient, demoIndexer);
```

## <a name="5---search"></a>5-arama

Azure Bilişsel Arama öğretici konsol uygulamalarında, sonuçları döndüren sorguları çalıştırmadan önce genellikle 2 saniyelik bir gecikme ekledik, ancak zenginleştirme işleminin tamamlanmak üzere birkaç dakika sürmesi nedeniyle konsol uygulamasını kapatacak ve bunun yerine başka bir yaklaşım kullanacağız.

En kolay seçenek portalda [Arama Gezgini](search-explorer.md) ' dir. İlk olarak, tüm belgeleri döndüren boş bir sorgu ya da işlem hattı tarafından oluşturulan yeni alan içeriğini döndüren daha hedeflenmiş bir arama çalıştırabilirsiniz. 

1. Azure portal, Genel Bakış sayfasında, **dizinler**' i seçin.

1. **`demoindex`** Listede bulun. 14 belge olmalıdır. Belge sayısı sıfırsa, Dizin Oluşturucu hala çalışıyor ya da sayfa henüz yenilenmedi. 

1. **`demoindex`** öğesini seçin. Arama Gezgini ilk sekmedir.

1. İlk belge yüklendikten hemen sonra içerik aranabilir olur. İçeriğin mevcut olduğunu doğrulamak için, **Ara**' ya tıklayarak belirtilmeyen bir sorgu çalıştırın. Bu sorgu, şu anda dizine alınmış tüm belgeleri döndürür ve bu, dizinin neleri içerdiğini bir fikir verir.

1. Daha sonra, daha yönetilebilir sonuçlar için aşağıdaki dizeyi yapıştırın: `search=*&$select=id, languageCode, organizations`

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Sıfırlama ve yeniden çalıştırma

Geliştirmede erken deneysel aşamalarda, tasarım yinelemesi için en pratik yaklaşım, nesneleri Azure Bilişsel Arama silmek ve kodunuzun bunları yeniden oluşturması için izin verir. Kaynak adları benzersizdir. Bir nesneyi sildiğinizde, aynı adı kullanarak nesneyi yeniden oluşturabilirsiniz.

Bu öğreticinin örnek kodu mevcut nesneleri denetler ve kodunuzu yeniden çalıştırabilmeniz için onları siler. Ayrıca, dizinleri, Dizin oluşturucuyu, veri kaynaklarını ve becerileri silmek için portalını de kullanabilirsiniz.

## <a name="takeaways"></a>Paketler

Bu öğreticide, bileşen bölümlerinin oluşturulması yoluyla zenginleştirilmiş bir dizin oluşturma işlem hattı oluşturmaya yönelik temel adımlar gösterilmiştir: bir veri kaynağı, Beceri, dizin ve Dizin Oluşturucu.

[Yerleşik yetenekler](cognitive-search-predefined-skills.md) , Beceri tanımıyla birlikte tanıtılmıştır ve giriş ve çıkışlarla becerilerin zincirlerinin bir araya getirilmiştir. Ayrıca, `outputFieldMappings` bir Azure bilişsel arama hizmetinde işlem hattındaki aranabilir bir dizine yönlendirme değerlerini yönlendirme için Dizin Oluşturucu tanımında gerekli olduğunu öğrenmiş olursunuz.

Son olarak, daha fazla yineleme için sonuçların nasıl test edileceğini ve sistemin nasıl sıfırlanacağını öğrendiniz. Dizine karşı sorgular düzenlendiğinde, zenginleştirilmiş dizin oluşturma işlem hattı tarafından oluşturulan çıktının döndürüldüğünü öğrendiniz. Ayrıca dizin oluşturucu durumunun nasıl denetleneceğini ve işlem hattı yeniden çalıştırılmadan önce hangi nesnelerin silineceğini de öğrendiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir projenin sonunda kendi aboneliğinizde çalışırken, artık ihtiyaç duyulmadığınızda kaynakları kaldırmak iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki tüm kaynaklar veya kaynak grupları bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık bir AI zenginleştirme işlem hattındaki tüm nesneler hakkında bilgi sahibi olduğunuza göre, Beceri tanımlarına ve bireysel becerilere daha yakından göz atalım.

> [!div class="nextstepaction"]
> [Beceri oluşturma](cognitive-search-defining-skillset.md)