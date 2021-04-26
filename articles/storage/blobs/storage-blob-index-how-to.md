---
title: Blob dizini etiketlerini kullanarak Azure Blob Storage 'da verileri yönetme ve bulma
description: Blob nesnelerini kategorilere ayırmak, yönetmek ve sorgulamak için blob Dizin etiketlerinin nasıl kullanılacağına ilişkin örneklere bakın.
author: twooley
ms.author: twooley
ms.date: 03/05/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 6f7ae7af0f2764bd3f157ad5ca3166c9989cd9bf
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276799"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Blob Dizin etiketlerini (Önizleme) kullanarak Azure Blob depolamada verileri yönetme ve bulma

Blob dizin etiketleri, anahtar-değer etiketi özniteliklerini kullanarak Depolama hesabınızdaki verileri kategorilere ayırın. Bu Etiketler otomatik olarak dizinlenir ve verileri kolayca bulmak için aranabilir çok boyutlu bir dizin olarak sunulur. Bu makalede blob Dizin etiketlerini kullanarak verileri ayarlama, alma ve bulma işlemlerinin nasıl yapılacağı gösterilir.

> [!IMPORTANT]
> Blob dizini etiketleri şu anda **Önizleme** aşamasındadır ve tüm genel bölgelerde kullanılabilir. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan yasal koşullara yönelik [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Bu özellik hakkında bilinen sorunlar ve sınırlamalar hakkında daha fazla bilgi edinmek için bkz. [blob dizini etiketleriyle Azure blob verilerini yönetme ve bulma (Önizleme)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Önkoşullar

# <a name="portal"></a>[Portal](#tab/azure-portal)

- Blob dizini önizlemesine erişim için kaydedilen ve onaylanan bir Azure aboneliği
- [Azure Portal](https://portal.azure.com/) erişim

# <a name="net"></a>[.NET](#tab/net)

Blob dizini önizlemede olduğu için, .NET depolama paketi önizleme NuGet akışında serbest bırakılır. Bu kitaplık, önizleme döneminde değiştirilebilir.

1. .NET için Azure Blob Storage istemci kitaplığı V12 kullanmaya başlamak için Visual Studio projenizi ayarlayın. Daha fazla bilgi için bkz. [.net hızlı başlangıç](storage-quickstart-blobs-dotnet.md)

2. NuGet Paket Yöneticisi ' nde, **Azure. Storage. blobu** paketini bulun ve **12.7.0-Preview. 1** sürümünü veya daha yeni sürümünü projenize uygulayın. PowerShell komutunu da çalıştırabilirsiniz: `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1`

   Nasıl yapılacağını öğrenmek için bkz. [paket bulma ve yüklemeyi oluşturma](/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

3. Aşağıdaki using deyimlerini, kod dosyanızın en üstüne ekleyin.

    ```csharp
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    using Azure.Storage.Blobs.Specialized;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

---

## <a name="upload-a-new-blob-with-index-tags"></a>Dizin etiketlerine sahip yeni bir blobu karşıya yükleme

Bu görev, bir [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) veya `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure Kaynak sağlayıcısı Işlemi](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) için özel bir Azure rolü aracılığıyla izin verilen bir güvenlik sorumlusu tarafından gerçekleştirilebilir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)depolama hesabınızı seçin 

2. **BLOB hizmeti** altındaki **kapsayıcılar** seçeneğine gidin, kapsayıcınızı seçin

3. Bir Blok Blobu olarak karşıya yüklenecek dosyayı bulmak için **karşıya yükle** düğmesini seçin ve yerel dosya sisteminize gidin.

4. **Gelişmiş** açılan listeyi genişletin ve **blob dizini etiketleri** bölümüne gidin

5. Verilerinize uygulanmasını istediğiniz anahtar/değer blobu Dizin etiketlerini girin

6. Blobu karşıya yüklemek için **karşıya yükle** düğmesini seçin

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="Dizin etiketleriyle blob yükleme işleminin nasıl yapılacağını gösteren Azure portal ekran görüntüsü.":::

# <a name="net"></a>[.NET](#tab/net)

Aşağıdaki örnek, oluşturma sırasında etiketleri ayarlanmış bir ekleme blobu oluşturmayı gösterir.

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Blob dizini etiketlerini al, ayarla ve Güncelleştir

Blob dizini etiketlerini alma, bir [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) veya `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` [Azure Kaynak sağlayıcısı Işlemi](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) için özel bir Azure rolü aracılığıyla izin verilen bir güvenlik sorumlusu tarafından gerçekleştirilebilir.

Blob dizini etiketlerini ayarlama ve güncelleştirme, bir [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) veya `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure Kaynak sağlayıcısı Işlemi](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) için özel bir Azure rolü aracılığıyla izin verilen bir güvenlik sorumlusu tarafından gerçekleştirilebilir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)depolama hesabınızı seçin 

2. **BLOB hizmeti** altındaki **kapsayıcılar** seçeneğine gidin, kapsayıcınızı seçin

3. Seçili kapsayıcı içindeki blob 'lar listesinden blobu seçin

4. Blob Genel Bakış sekmesi, blob **Dizin etiketleri** dahil olmak üzere Blobun özelliklerini görüntüler

5. Blobun anahtar/değer dizini etiketlerini alabilir, ayarlayabilir, değiştirebilir veya silebilirsiniz

6. Bloba ilgili tüm güncelleştirmeleri onaylamak için **Kaydet** düğmesini seçin

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Bloblarda Dizin etiketlerinin nasıl alınacağını, ayarlanacağını, güncelleşileceğini ve silineceğini gösteren Azure portal ekran görüntüsü.":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Blob dizini etiketleriyle verileri filtreleme ve bulma

Bu görev, bir [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) veya `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` [Azure Kaynak sağlayıcısı Işlemi](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) için özel bir Azure rolü aracılığıyla izin verilen bir güvenlik sorumlusu tarafından gerçekleştirilebilir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal içinde, blob dizini etiketleri filtresi, `@container` parametreyi seçtiğiniz kapsayıcının kapsamına otomatik olarak uygular. Tüm depolama hesabınızda etiketlendirilmiş verileri filtrelemek ve bulmak istiyorsanız REST API, SDK 'larımızı veya araçlarınızı kullanın.

1. [Azure Portal](https://portal.azure.com/)depolama hesabınızı seçin. 

2. **BLOB hizmeti** altındaki **kapsayıcılar** seçeneğine gidin, kapsayıcınızı seçin

3. Seçili kapsayıcı içinde filtrelemek için **BLOB dizin etiketleri filtresi** düğmesini seçin

4. Blob dizini etiketi anahtarı ve etiket değeri girin

5. Ek etiket filtreleri eklemek için **blob dizini Etiketleri filtre** düğmesini seçin (10 ' a kadar)

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="Dizin etiketlerini kullanarak etiketli Blobların nasıl filtreleneceğini ve bulunacağını gösteren Azure portal ekran görüntüsü":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Blob dizini etiket filtreleriyle yaşam döngüsü yönetimi

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)depolama hesabınızı seçin. 

2. **BLOB hizmeti** altındaki **yaşam döngüsü yönetimi** seçeneğine gidin

3. *Kural Ekle* ' yi seçin ve ardından eylem kümesi form alanlarını doldurun

4. Önek eşleşmesi ve blob dizini eşleşmesi için isteğe bağlı filtre eklemek üzere **filtre** ayarla ' yı seçin

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="Yaşam döngüsü yönetimi için Dizin etiketlerinin nasıl ekleneceğini gösteren Azure portal ekran görüntüsü.":::

5. Kural ayarlarını gözden geçirmek için **gözden geçir + Ekle** ' yi seçin

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="Blob dizin etiketleri filtresi örneği ile bir yaşam döngüsü yönetimi kuralını gösteren Azure portal ekran görüntüsü":::

6. Yeni kuralı yaşam döngüsü yönetimi ilkesine uygulamak için **Ekle** ' yi seçin

# <a name="net"></a>[.NET](#tab/net)

[Yaşam döngüsü yönetimi](storage-lifecycle-management-concepts.md) ilkeleri, her depolama hesabı için denetim düzlemi düzeyinde uygulanır. .NET için [Microsoft Azure Management Storage kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) sürüm 16.0.0 veya üstünü yüklemelisiniz.

---

## <a name="next-steps"></a>Sonraki adımlar

 - Blob dizini etiketleri hakkında daha fazla bilgi için bkz. [blob dizini etiketleriyle Azure blob verilerini yönetme ve bulma (Önizleme)](storage-manage-find-blobs.md )
 - Yaşam döngüsü yönetimi hakkında daha fazla bilgi [için bkz. Azure Blob depolama yaşam döngüsünü yönetme](storage-lifecycle-management-concepts.md)
