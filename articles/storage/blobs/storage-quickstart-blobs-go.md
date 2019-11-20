---
title: Azure Hızlı Başlangıç - Go kullanarak nesne depolamada blob oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta, nesne (Blob) depolamada depolama hesabı ve kapsayıcı oluşturursunuz. Sonra, Azure Depolama’ya blob yüklemek, blob indirmek ve bir kapsayıcıdaki blobları listelemek amacıyla Go için depolama istemcisi kitaplığını kullanırsınız.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: f4016349e354c84e9e096ac6d5072a4870e9ef29
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726450"
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>Hızlı Başlangıç: Go kullanarak Blobları karşıya yükleme, indirme ve listeleme

Bu hızlı başlangıçta, Azure Blob depolamadaki bir kapsayıcıda blok bloblarını karşıya yüklemek, indirmek ve listelemek için Go programlama dilini nasıl kullanabileceğinizi öğreneceksiniz. 

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Aşağıdaki ek önkoşulların yüklü olduğundan emin olun:
 
* [Git 1,8 veya üzeri](https://golang.org/dl/)
* Aşağıdaki komutu kullanarak [Go IÇIN SDK Azure Depolama Blobu](https://github.com/azure/azure-storage-blob-go/):

    ```
    go get -u github.com/Azure/azure-storage-blob-go/azblob
    ``` 

    > [!NOTE]
    > SDK ile çalışırken, büyük `Azure` /küçük harfe ilgili içeri aktarma sorunlarından kaçınmak için URL 'de büyük harfle aldığınızdan emin olun. Ayrıca içeri `Azure` aktarma deyimlerinizi de büyük harfle yapın.
    
## <a name="download-the-sample-application"></a>Örnek uygulamayı indirin:
Bu [hızlı başlangıçta](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) kullanılan örnek uygulama, temel bir Go uygulamasıdır.  

Uygulamanın bir kopyasını geliştirme ortamınıza indirmek için [Git](https://git-scm.com/)'i kullanın. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

Bu komut, depoyu yerel Git klasörünüze kopyalar. Blob depolama alanının Go örneğini açmak için, storage-quickstart.go dosyasını bulun.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma
Bu çözüm, depolama hesabı adınızın ve anahtarınızın çözümü çalıştıran makinede yerel olarak bulunan ortam değişkenlerinde güvenli bir şekilde depolanmasını gerektirir. Ortam değişkenlerini oluşturmak için işletim sisteminize bağlı olarak aşağıdaki örneklerden birini izleyin.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>Örneği çalıştırma
Bu örnek geçerli klasörde bir test dosyası oluşturur, test dosyasını Blob depolamaya yükler, kapsayıcıdaki blobları listeler ve dosyayı bir arabelleğe indirir. 

Örneği çalıştırmak için aşağıdaki komutu yürütün: 

```go run storage-quickstart.go```

Aşağıdaki çıktı, uygulama çalıştırılırken döndürülen çıktının bir örneğidir:
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
Devam etmek için tuşa bastığınızda, örnek program depolama kapsayıcısını ve dosyaları siler. 

> [!TIP]
> Ayrıca, Blob depolamadaki dosyaları görüntülemek için, [Azure Depolama Gezgini](https://storageexplorer.com) gibi bir araç da kullanabilirsiniz. Azure Depolama Gezgini, depolama hesabı bilgilerinize erişmenize olanak tanıyan ücretsiz ve platformlar arası bir araçtır. 
>

## <a name="understand-the-sample-code"></a>Örnek kodu anlama

Sonraki aşamada, nasıl çalıştığını anlayabilmeniz için örnek kodu inceleyeceğiz.

### <a name="create-containerurl-and-bloburl-objects"></a>ContainerURL ve BlobURL nesnelerini oluşturma
İlk yapılacak olan, Blob depolamaya erişmek ve Blob depolamayı yönetmek için kullanılan ContainerURL ve BlobURL nesnelerine başvuru oluşturmaktır. Bu nesneler, REST API'lerini göndermek için Create, Upload ve Download gibi alt düzey API'ler sunar.

* Kimlik bilgilerinizi depolamak için [**SharedKeyCredential**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#SharedKeyCredential) struct'ını kullanın. 

* Kimlik bilgilerini ve seçenekleri kullanarak bir [**işlem hattı**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#NewPipeline) oluşturun. İşlem hattı yeniden deneme ilkeleri, günlük ve HTTP yanıtı iş yüklerini seri durumdan çıkarma gibi daha birçok öğeyi belirtir.  

* Kapsayıcı (Create) ve bloblar (Upload ve Download) üzerinde işlemleri çalıştırmak için yeni [**ContainerURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#ContainerURL)'yi ve yeni [**BlobURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#BlobURL) nesnesini başlatın.


ContainerURL'niz olduğunda, bir bloba işaret eden **BlobURL** nesnesini başlatabilir ve karşıya yükleme, indirme ve kopyalama gibi işlemler yapabilirsiniz.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcılar ve blob adları hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Bu bölümde, yeni bir kapsayıcı oluşturursunuz. Bu, **quickstartblobs-[random string]** adlı kapsayıcıdır. 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential, err := azblob.NewSharedKeyCredential(accountName, accountKey)
if err != nil {
    log.Fatal("Invalid credentials with error: " + err.Error())
}
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err = containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Blobları kapsayıcıya yükleme

Blob depolama blok blobları, ekleme bloblarını ve sayfa bloblarını destekler. Blok blobları en sık kullanılan bloblardır ve bu hızlı başlangıçta bu bloblar kullanılmıştır.  

Bloba dosya yüklemek için, **os.Open** kullanarak dosyayı açın. Daha sonra REST API 'Lerinden birini kullanarak belirtilen yola dosyayı yükleyebilirsiniz: Karşıya yükleme (PutBlob), StageBlock/CommitBlockList (PutBlock/PutBlockList). 

Alternatif olarak, SDK alt düzey REST API'lerinin üstüne yapılandırılmış [üst düzey API'ler](https://github.com/Azure/azure-storage-blob-go/blob/master/azblob/highlevel.go) sağlar. Örnek vermek gerekirse, ***UploadFileToBlockBlob*** işlevi, aktarım hızını iyileştirmek için StageBlock (PutBlock) işlemlerini kullanarak bir dosyayı öbekler halinde eşzamanlı olarak karşıya yükler. Dosya 256 MB'den küçükse, aktarımı tek işlemde tamamlamak için onun yerine Upload (PutBlob) kullanır.

Aşağıdaki örnek, dosyayı **quickstartblobs-[randomstring]** adlı kapsayıcınıza yükler.

```go
// Create a file to test the upload and download.
fmt.Printf("Creating a dummy file to test the upload and download\n")
data := []byte("hello world this is a blob\n")
fileName := randomString()
err = ioutil.WriteFile(fileName, data, 0700)
handleErrors(err)

// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level Upload (PutBlob) API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that Upload can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// To upload more than 256MB, use StageBlock (PutBlock) and CommitBlockList (PutBlockList) functions. 
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.Upload(ctx, file, azblob.BlobHTTPHeaders{ContentType: "text/plain"}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls StageBlock/CommitBlockList for files larger 256 MBs, and calls Upload for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

**ContainerURL** üzerinde **ListBlobs** yöntemini kullanarak kapsayıcıdaki dosyaların listesini alın. ListBlobs, belirtilen **Marker**'dan başlayarak tek bir blob segmenti (en çok 5000 blob) döndürür. Sabit listenin en baştan başlatılması için boş bir Marker kullanın. Blob adları, sözlük sıralamasına göre döndürülür. Segmenti aldıktan sonra işleyin ve ardından daha önce döndürülen Marker'ı geçirerek ListBlobs yöntemini yeniden çağırın.  

```go
// List the container that we have created above
fmt.Println("Listing the blobs in the container:")
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobsFlatSegment(ctx, marker, azblob.ListBlobsSegmentOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Segment.BlobItems {
        fmt.Print(" Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Blobu indirme

BlobURL'de alt düzey **Download** işlevini kullanarak blobları indirin. Bu bir **DownloadResponse** struct’ı döndürür. Verileri okumak üzere bir **RetryReader** akışı almak için struct’ta **Body** işlevi çalıştırın. Okurken bir bağlantı başarısız olursa, bir bağlantıyı yeniden kurmak ve okumaya devam etmek için ek istekler yapılır. MaxRetryRequests’i 0 (varsayılan) olarak ayarlanmış bir RetryReaderOption belirtildiğinde orijinal yanıt gövdesi döndürülür ve hiçbir yeniden deneme gerçekleştirilmez. Alternatif olarak kodunuzu basitleştirmek için yüksek düzeyli API’ler olarak **DownloadBlobToBuffer** veya **DownloadBlobToFile**’ı kullanın.

Aşağıdaki kod, **Download** işlevini kullanarak blobu indirir. Blobun içeriği arabelleğe yazılır ve konsolda gösterilir.

```go
// Here's how to download the blob
downloadResponse, err := blobURL.Download(ctx, 0, azblob.CountToEnd, azblob.BlobAccessConditions{}, false)

// NOTE: automatically retries are performed if the connection fails
bodyStream := downloadResponse.Body(azblob.RetryReaderOptions{MaxRetryRequests: 20})

// read the body into a buffer
downloadedData := bytes.Buffer{}
_, err = downloadedData.ReadFrom(bodyStream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Kaynakları temizleme
Bu hızlı başlangıçta karşıya yüklenen bloblara artık ihtiyacınız kalmadığında, **Delete** yöntemini kullanarak kapsayıcının tamamını silebilirsiniz. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>Bloblarla Go uygulamaları geliştirme kaynakları

Blob depolama ile Go geliştirmeye yönelik şu ek kaynaklara bakın:

- GitHub’da Azure Depolama için [Go istemci kitaplığı kaynak kodunu](https://github.com/Azure/azure-storage-blob-go) görüntüleyin ve yükleyin.
- Go istemci kitaplığını kullanarak yazılmış [Blob depolama örneklerini](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#pkg-examples) araştırın.

## <a name="next-steps"></a>Sonraki adımlar
 
Bu hızlı başlangıçta, dosyaları Go kullanarak yerel bir disk ile Azure blob depolama arasında aktarmayı öğrendiniz. Azure Depolama Blobu SDK'sı hakkında daha fazla bilgi için, [Kaynak Kodu](https://github.com/Azure/azure-storage-blob-go/) ve [API Başvurusu](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob) konularına bakın.
