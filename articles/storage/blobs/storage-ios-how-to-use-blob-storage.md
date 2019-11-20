---
title: İOS-Azure ' t e nesne (blob) depolaması kullanma | Microsoft Docs
description: Azure Blob Storage (nesne depolama) ile bulutta yapılandırılmamış veri depolayın.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 54085d602246d38adb970ed02f451241ca7ba19d
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726415"
---
# <a name="how-to-use-blob-storage-from-ios"></a>İOS 'dan blob depolamayı kullanma

Bu makalede Microsoft Azure Blob Storage kullanarak yaygın senaryoların nasıl gerçekleştirileceği gösterilmektedir. Örnekler, amaç-C ' d e yazılır ve [iOS Için Azure Storage Istemci kitaplığı](https://github.com/Azure/azure-storage-ios)' nı kullanır. Kapsanan senaryolar, Blobları karşıya yükleme, listeleme, indirme ve silmeyi içerir. Blob 'lar hakkında daha fazla bilgi için [sonraki adımlar](#next-steps) bölümüne bakın. Azure depolama 'nın bir iOS uygulamasında kullanımını hızlıca görmek için [Örnek uygulamayı](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) da indirebilirsiniz.

BLOB depolama hakkında daha fazla bilgi için bkz. [Azure Blob depolamaya giriş](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Azure Storage iOS kitaplığını uygulamanıza aktarın

Azure Storage [CocoaPod](https://cocoapods.org/pods/AZSClient) 'i kullanarak veya **çerçeve** dosyasını Içeri aktararak Azure Storage iOS kitaplığını uygulamanıza aktarabilirsiniz. CocoaPod, kitaplığın daha kolay tümleştirilebilmesini sağlayan önerilen yoldur, ancak Framework dosyasından içeri aktarma mevcut projeniz için daha az zorlayıcıdır.

Bu kitaplığı kullanmak için şunlar gerekir:

- iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod

1. Daha önce yapmadıysanız, bir Terminal penceresi açıp aşağıdaki komutu çalıştırarak bilgisayarınıza [Cocoapods 'Yi yükleyebilirsiniz](https://guides.cocoapods.org/using/getting-started.html#toc_3)

    ```shell
    sudo gem install cocoapods
    ```

2. Ardından, proje dizininde (. XCODEPROJ dosyanızı içeren dizin), _pod dosyası_(dosya uzantısı olmadan) adlı yeni bir dosya oluşturun. Şunları _Pod dosyasına_ ekleyin ve kaydedin.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. Terminal penceresinde, proje dizinine gidin ve aşağıdaki komutu çalıştırın

    ```shell
    pod install
    ```

4. . XCODEPROJ, Xcode 'da açıksa kapatın. Proje dizininizde. xcworkspace uzantısına sahip olacak yeni oluşturulan proje dosyasını açın. Bu, şu anda üzerinde çalıştığınız dosyadır.

## <a name="framework"></a>Çerçevenin

Kitaplığı kullanmanın diğer yolu, çerçeveyi el ile oluşturmak için kullanılır:

1. İlk olarak, [Azure-Storage-iOS depoyu](https://github.com/azure/azure-storage-ios)indirin veya kopyalayın.
2. *Azure-Storage-iOS* -> *lib* -> *Azure depolama istemci kitaplığı*' na gidin ve Xcode 'da açın `AZSClient.xcodeproj` .
3. Xcode 'un sol üst kısmında, etkin düzeni "Azure Storage Istemci kitaplığı" iken "Framework" olarak değiştirin.
4. Projeyi derleyin (⌘ + B). Bu işlem masaüstünüzde bir `AZSClient.framework` dosya oluşturur.

Ardından, aşağıdaki işlemleri gerçekleştirerek Framework dosyasını uygulamanıza aktarabilirsiniz:

1. Yeni bir proje oluşturun veya mevcut projenizi Xcode 'da açın.
2. Öğesini Xcode proje `AZSClient.framework` Gezgininizi sürükleyip bırakın.
3. *Gerekirse öğeleri Kopyala*' yı seçin ve *son*' a tıklayın.
4. Sol taraftaki gezinmede projenize tıklayın ve proje Düzenleyicisi ' nin en üstündeki *genel* sekmesine tıklayın.
5. *Bağlantılı çerçeveler ve kitaplıklar* bölümünde Ekle düğmesine (+) tıklayın.
6. Zaten sağlanmış olan Kitaplıklar listesinde, öğesini arayıp `libxml2.2.tbd` projenize ekleyin.

## <a name="import-the-library"></a>Kitaplığı İçeri aktarma

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Swift kullanıyorsanız, bir köprü oluşturma üst bilgisi oluşturmanız ve AZSClient/AZSClient. h > \<içeri aktarmanız gerekir:

1. Üst bilgi dosyası `Bridging-Header.h`oluşturun ve yukarıdaki içeri aktarma ifadesini ekleyin.
2. *Derleme ayarları* sekmesine gidin ve *Amaç-C köprü oluşturma üst bilgisi*için arama yapın.
3. *Hedef-C köprüleme üst bilgisine* çift tıklayın ve yolu üst bilgi dosyanıza ekleyin:`ProjectName/Bridging-Header.h`
4. Köprü üstbilgisinin Xcode tarafından çekildiğini doğrulamak için projeyi derleyin (⌘ + B).
5. Kitaplığı doğrudan herhangi bir Swift dosyasında kullanmaya başlayın, içeri aktarma deyimlerine gerek yoktur.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Zaman uyumsuz Işlemler

> [!NOTE]
> Hizmette bir istek gerçekleştiren tüm yöntemler zaman uyumsuz işlemlerdir. Kod örneklerinde, bu yöntemlerin bir tamamlama işleyicisine sahip olduğunu göreceksiniz. Tamamlama işleyicisinin içindeki kod, istek tamamlandıktan **sonra** çalışır. İstek yapıldığında tamamlama işleyiciden sonraki kod **çalışır.**

## <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Azure Storage 'daki her blob bir kapsayıcıda yer almalıdır. Aşağıdaki örnek, henüz yoksa depolama hesabınızda *newcontainer*adlı bir kapsayıcının nasıl oluşturulacağını gösterir. Kapsayıcınız için bir ad seçerken yukarıda belirtilen adlandırma kurallarından en az birini seçin.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

[Microsoft Azure Depolama Gezgini](https://storageexplorer.com) bakarak bu işlemi, *Newcontainer* 'ın depolama hesabınız için kapsayıcı listesinde olduğunu doğrulayabilirsiniz.

## <a name="set-container-permissions"></a>Kapsayıcı Izinlerini ayarla

Kapsayıcının izinleri, varsayılan olarak **özel** erişim için yapılandırılır. Ancak kapsayıcılar kapsayıcı erişimi için birkaç farklı seçenek sunar:

- **Özel**: Kapsayıcı ve blob verileri yalnızca hesap sahibi tarafından okunabilir.
- **BLOB**: Bu kapsayıcı içindeki blob verileri anonim istek aracılığıyla okunabilir, ancak kapsayıcı verileri kullanılamıyor. İstemciler, anonim istek aracılığıyla kapsayıcı içindeki Blobları numaralandıramaz.
- **Kapsayıcı**: Kapsayıcı ve blob verileri, anonim istek aracılığıyla okunabilir. İstemciler, anonim istek aracılığıyla kapsayıcı içindeki Blobları numaralandırabilir, ancak depolama hesabı içindeki kapsayıcıları numaralandıramaz.

Aşağıdaki örnek, Internet 'teki tüm kullanıcılar için genel, salt okuma erişimine izin veren **kapsayıcı** erişim izinleri ile bir kapsayıcının nasıl oluşturulacağını gösterir:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Bir kapsayıcıya bir blob yükleme

Blob hizmeti kavramları bölümünde belirtildiği gibi, blob Storage üç farklı tür blob sunar: blok Blobları, ekleme Blobları ve sayfa Blobları. Azure Storage iOS kitaplığı, her üç blob türünü destekler. Çoğu durumda, kullanılması önerilen blob türü blok blobudur.

Aşağıdaki örnek, bir NSString 'ten bir blok blobunun nasıl yükleneceğini gösterir. Aynı ada sahip bir blob zaten bu kapsayıcıda varsa, bu Blobun içeriğinin üzerine yazılır.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

[Microsoft Azure Depolama Gezgini](https://storageexplorer.com) bakarak ve *containerpublic*kapsayıcısının blob, *sampleblob*' u içerdiğini doğrulamak için bu işe yarar. Bu örnekte, bir ortak kapsayıcı kullandık, bu uygulamanın Bloblar URI 'sine giderek çalıştığını da doğrulayabilirsiniz.

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Bir NSString 'ten bir Blok Blobu yüklemeye ek olarak, NSData, NSInputStream veya yerel bir dosya için benzer yöntemler bulunur.

## <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

Aşağıdaki örnek, bir kapsayıcıdaki tüm Blobların nasıl ekleneceğini gösterir. Bu işlemi gerçekleştirirken aşağıdaki parametrelerin azının olması gerekir:

- **Continuationtoken** -devamlılık belirteci, listeleme işleminin başlayacağı yeri temsil eder. Hiçbir belirteç sağlanmazsa, Blobların başlangıçtan itibaren listelendirilecektir. Herhangi bir sayıda blob, sıfırdan büyük bir küme en fazla olacak şekilde listelenebilir. Bu yöntem sıfır sonuçları `results.continuationToken` döndürse bile, bu işlem Nil değilse, hizmette listelenmeyen daha fazla BLOB olabilir.
- **ön ek** -blob listesi için kullanılacak ön eki belirtebilirsiniz. Yalnızca bu önek ile başlayan Bloblar listelenecektir.
- **Useyataybloblisteleme** -kapsayıcı ve bloblara [başvurma](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) bölümünde belirtildiği gibi, blob hizmeti düz bir depolama düzeni olsa da, Bloblar yol bilgileriyle adlandırarak bir sanal hiyerarşi oluşturabilirsiniz. Ancak, düz olmayan listeleme Şu anda desteklenmiyor. Bu özellik yakında kullanıma sunulacak. Şimdilik, bu değer **Yes**olmalıdır.
- **Bloblistingdetails** -Blobları listelenirken hangi öğelerin ekleneceğini belirtebilirsiniz
  - _Azsbloblistingdetailsnone_: Yalnızca kaydedilmiş Blobları listeleyin ve BLOB meta verilerini döndürmez.
  - _Azsbloblistingdetailssnapgörüntülerini_: İşlenen Blobları ve BLOB anlık görüntülerini listeleyin.
  - _Azsbloblistingayrıntılar meta verileri_: Listede döndürülen her blob için blob meta verilerini alın.
  - _Azsbloblistingdetailsuncommittedbloblar_: İşlenmiş ve işlenmemiş Blobları listeleyin.
  - _Azsbloblistingayrıntılar kopyası_: Listeye kopyalama özelliklerini ekleyin.
  - _Azsbloblistingayrıntılar tümü_: Tüm kullanılabilir tüm blob 'ları, işlenmemiş blob 'ları ve anlık görüntüleri listeleyin ve bu Blobların tüm meta verilerini ve kopyalama durumunu geri döndürün.
- **MaxResults** -bu işlem için döndürülecek en fazla sonuç sayısı. Sınır ayarlamak için-1 kullanın.
- **Completionhandler** -listeleme işleminin sonuçlarıyla yürütülecek kod bloğu.

Bu örnekte bir yardımcı yöntem, bir devamlılık belirteci döndürüldüğünde, liste Blobları yöntemini yinelemeli olarak çağırmak için kullanılır.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Blob indirme

Aşağıdaki örnek bir blob 'un NSString nesnesine nasıl indirileceği gösterilmektedir.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Blob silme

Aşağıdaki örnek, bir Blobun nasıl silineceğini gösterir.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Blob kapsayıcısını silme

Aşağıdaki örnek, bir kapsayıcının nasıl silineceğini gösterir.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Sonraki adımlar

Artık iOS 'dan blob Storage 'ı nasıl kullanacağınızı öğrendiğinize göre, iOS kitaplığı ve depolama hizmeti hakkında daha fazla bilgi edinmek için bu bağlantıları izleyin.

- [İOS için Azure depolama Istemci kitaplığı](https://github.com/azure/azure-storage-ios)
- [Azure depolama iOS başvuru belgeleri](https://azure.github.io/azure-storage-ios/)
- [Azure Depolama Hizmetleri REST API'si](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Azure Depolama Ekibi Blog’u](https://blogs.msdn.com/b/windowsazurestorage)

Bu kitaplıkla ilgili sorularınız varsa, [MSDN Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) forumumuza veya [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files)göndermekten çekinmeyin.
Azure depolama için özellik önerileriniz varsa lütfen [Azure Storage geri bildirimlerine](https://feedback.azure.com/forums/217298-storage/)gönderin.
