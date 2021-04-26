---
title: Blob depolamada eşzamanlılık yönetimi
titleSuffix: Azure Storage
description: Uygulamanızda iyimser veya Kötümser eşzamanlılık uygulayarak birden çok yazıcının bir bloba nasıl yönetileceğini öğrenin. İyimser eşzamanlılık bir blob için ETag değerini denetler ve belirtilen ETag ile karşılaştırır. Kötümser eşzamanlılık, blobu diğer yazıcılara kilitlemek için özel bir kira kullanır.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96523639"
---
# <a name="managing-concurrency-in-blob-storage"></a>Blob depolamada eşzamanlılık yönetimi

Modern uygulamalarda, verileri aynı anda görüntüleyen ve güncelleştiren birden fazla kullanıcı vardır. Uygulama geliştiricilerinin, özellikle birden çok kullanıcının aynı verileri güncelleştirebileceği senaryolar için, son kullanıcılarına öngörülebilir bir deneyim sağlama konusunda dikkatli olması gerekir. Geliştiricilerin genellikle göz önünde bulundurulması gereken üç ana veri eşzamanlılık stratejisi vardır:  

- **İyimser eşzamanlılık**: bir güncelleştirme gerçekleştiren bir uygulama, güncelleştirmesinin bir parçası olarak, uygulamanın bu verileri en son okumasından bu yana verilerin değişip değişmediğini tespit eder. Örneğin, bir wiki sayfasını görüntüleyen iki Kullanıcı bu sayfada bir güncelleştirme yapsa, wiki platformu ikinci güncelleştirmenin ilk güncelleştirmenin üzerine yazmadığından emin olmalıdır. Ayrıca, her iki kullanıcının güncelleştirmesinin başarılı olup olmadığını anlamasına de emin olmalısınız. Bu strateji çoğu zaman Web uygulamalarında kullanılır.

- **Kötümser eşzamanlılık**: bir güncelleştirme gerçekleştirmeye yönelik bir uygulama, diğer kullanıcıların kilit yayımlanıncaya kadar verileri güncelleştirmesini engellediği bir nesne üzerinde kilit alır. Örneğin, birincil/ikincil veri çoğaltma senaryosunda yalnızca birincil güncelleştirmeler gerçekleştirdiği zaman, bu, başka bir kimsenin güncelleştirememesini sağlamak için genellikle verilerin uzun bir süre boyunca dışlamalı bir kilit tutar.

- **Son yazıcı WINS**: daha önce başka bir uygulamanın okuduğundan bu yana verileri güncelleştirip güncelleştirmediğini belirlememeden güncelleştirme işlemlerinin devam etmesine olanak tanıyan bir yaklaşım. Bu yaklaşım genellikle veriler aynı anda birden çok kullanıcının aynı verilere erişemeyecek şekilde bölümlendiğinde kullanılır. Kısa süreli veri akışlarının işlendiği durumlar da yararlı olabilir.

Azure depolama, tüm üç stratejiyi destekler, ancak iyimser ve Kötümser eşzamanlılık için tam destek sağlama yeteneğine de sahiptir. Azure depolama, hizmet bir ekleme veya güncelleştirme işlemi gerçekleştirdikten sonra, sonraki okuma işlemleri en son güncelleştirmeyi geri döndürmekte olan güçlü bir tutarlılık modelini benimsemeye yönelik olarak tasarlanmıştır.

Uygun bir eşzamanlılık stratejisi seçmenin yanı sıra, geliştiriciler bir depolama platformunun değişiklikleri nasıl yalıtdığının farkında olmalıdır, özellikle de işlemler arasında aynı nesne üzerinde değişir. Azure depolama, tek bir bölüm içinde yazma işlemleriyle eşzamanlı olarak okuma işlemlerine izin vermek için anlık görüntü yalıtımını kullanır. Anlık görüntü yalıtımı, güncelleştirmeler gerçekleşirken bile tüm okuma işlemlerinin verilerin tutarlı bir anlık görüntüsünü döndürmesini güvence altına alır.

Bloblara ve kapsayıcılara erişimi yönetmek için iyimser veya Kötümser eşzamanlılık modellerini kullanmayı tercih edebilirsiniz. Açıkça bir strateji belirtmezseniz, varsayılan olarak son yazıcı kazanır.  

## <a name="optimistic-concurrency"></a>İyimser eşzamanlılık

Azure depolama, depolanan her nesneye bir tanımlayıcı atar. Bu tanımlayıcı, bir nesne üzerinde her yazma işlemi gerçekleştirildiğinde güncellenir. Tanımlayıcı, HTTP protokolü tarafından tanımlanan ETag üstbilgisindeki HTTP GET yanıtının bir parçası olarak istemciye döndürülür.

Bir güncelleştirme gerçekleştiren bir istemci, bir güncelleştirmenin yalnızca belirli bir koşul karşılanırsa meydana geldiğinden emin olmak için özgün ETag 'i bir koşullu üstbilgiyle birlikte gönderebilir. Örneğin, **IF-Match** üst bilgisi belirtilmişse, Azure depolama, güncelleştirme Isteğinde belirtilen ETag değerinin güncelleştirilmekte olan nesnenin ETag 'i ile aynı olduğunu doğrular. Koşullu üstbilgiler hakkında daha fazla bilgi için bkz. [BLOB hizmeti işlemleri için koşullu üstbilgiler belirtme](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).

Bu işlemin ana hattı aşağıdaki gibidir:  

1. Azure depolama 'dan blob alma. Yanıt, nesnenin geçerli sürümünü tanımlayan bir HTTP ETag üst bilgi değeri içerir.
1. Blobu güncelleştirdiğinizde, yazma isteğinin **IF-Match** koşullu üstbilgisinde adım 1 ' de aldığınız ETag değerini ekleyin. Azure depolama, istekteki ETag değerini Blobun geçerli ETag değeriyle karşılaştırır.
1. Blob 'un geçerli ETag değeri istekte belirtilen **IF-Match** koşullu üst bilgisinde belirtilenden farklıysa Azure Storage, 412 (Önkoşul başarısız oldu) http durum kodunu döndürür. Bu hata, istemci tarafından ilk kez alınmasından bu yana başka bir işlemin blobu güncelleştirdiğini gösterir.
1. Blob 'un geçerli ETag değeri, istekteki **IF-Match** koşullu üstbilgisindeki ETag ile aynı sürümse, Azure depolama, istenen işlemi gerçekleştirir ve Blobun geçerli ETag değerini güncelleştirir.  

Aşağıdaki kod örnekleri, bir blob için ETag değerini denetleyen yazma isteğinde bir **IF-Match** koşulunun nasıl oluşturulacağını göstermektedir. Azure depolama, blob 'un geçerli ETag öğesinin istekte belirtilen ETag ile aynı olup olmadığını değerlendirir ve yalnızca iki ETag değeri eşleşiyorsa yazma işlemini gerçekleştirir. Başka bir işlem geçici içindeki blobu güncelleştirdiyse, Azure Storage bir HTTP 412 (Önkoşul başarısız) durum iletisi döndürür.  

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Azure depolama, **If-Modified-Since**, **If-Modified-Since** ve **If-None-Match** gibi diğer koşullu üstbilgileri de destekler. Daha fazla bilgi için bkz. [BLOB hizmeti işlemleri Için koşullu üstbilgiler belirtme](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

## <a name="pessimistic-concurrency-for-blobs"></a>Blob 'lar için Kötümser eşzamanlılık

Bir blobu özel kullanım için kilitlemek üzere, üzerinde bir kira elde edebilirsiniz. Kirayı aldığınızda kira süresini belirtirsiniz. Sınırlı bir kira 15 ila 60 saniye arasında geçerli olabilir. Kira Ayrıca, özel bir kilit için de sınırsız olabilir. Sınırlı bir kirayı genişletmek için yenileyebilirsiniz ve bununla işiniz bittiğinde kirayı serbest bırakabilirsiniz. Azure Storage, süresi dolduğunda sınırlı kiraları otomatik olarak serbest bırakır.  

Kiralamalar, özel yazma/paylaşılan okuma işlemleri, özel yazma/dışlamalı okuma işlemleri ve paylaşılan yazma/özel okuma işlemleri dahil olmak üzere farklı eşitleme stratejilerini etkinleştirir. Kira mevcut olduğunda Azure Storage, kira tutucusuna yönelik yazma işlemlerine özel erişim uygular. Ancak, okuma işlemleri için denetim sağlamak, geliştiricinin tüm istemci uygulamalarının bir kira KIMLIĞI kullanmasını ve aynı anda yalnızca bir istemcinin geçerli bir kira KIMLIĞI içerdiğinden emin olmasını gerektirir. Paylaşılan okumaların kira KIMLIĞI sonucunu içermeyen okuma işlemleri.  

Aşağıdaki kod örnekleri bir blob üzerinde özel bir kiralamanın nasıl alınacağını, kira KIMLIĞINI sağlayarak Blobun içeriğini güncelleştirmeyi ve sonra kirayı serbest bırakmaya ilişkin bir örnek gösterir. Kira etkin ise ve bir yazma isteğinde Kiralama KIMLIĞI sağlanmazsa, yazma işlemi 412 hata koduyla başarısız olur (Önkoşul başarısız oldu).  

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>Kapsayıcılar için Kötümser eşzamanlılık

Kapsayıcılardaki kiralamalar, özel yazma/paylaşılan okuma, dışlamalı yazma/dışlamalı okuma ve paylaşılan yazma/dışlamalı okuma dahil olmak üzere bloblarda desteklenen eşitleme stratejilerini etkinleştirir. Ancak kapsayıcılar için, dışlamalı kilit yalnızca silme işlemlerinde zorlanır. Etkin bir kiralamaya sahip bir kapsayıcıyı silmek için, istemci, silme isteğiyle birlikte etkin Kiralama KIMLIĞINI içermelidir. Kira KIMLIĞI olmadan, kiralanan bir kapsayıcıda diğer tüm kapsayıcı işlemleri başarılı olur.  

## <a name="next-steps"></a>Sonraki adımlar

* [Blob hizmeti işlemleri için koşullu üstbilgileri belirtme](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Kira kapsayıcısı](/rest/api/storageservices/lease-container)
* [Blob Kiralama](/rest/api/storageservices/lease-blob)
