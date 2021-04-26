---
title: 'Hızlı başlangıç: Java için Azure Blob depolama istemci kitaplığı V8'
description: Nesne (Blob) depolamada depolama hesabı ve kapsayıcı oluşturun. Ardından, Azure depolama 'ya blob yüklemek, blob indirmek ve bir kapsayıcıdaki Blobları listelemek için Azure depolama istemci kitaplığı v8 ' nı kullanın.
author: twooley
ms.custom: devx-track-java
ms.author: twooley
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 49c55a2b565100e370ce561537c96a96b896f355
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280607"
---
# <a name="quickstart-manage-blobs-with-java-v8-sdk"></a>Hızlı başlangıç: Java V8 SDK ile Blobları yönetme

Bu hızlı başlangıçta, Java kullanarak blob 'ları yönetmeyi öğreneceksiniz. Blob 'lar, görüntüler, belgeler, akış ortamları ve arşiv verileri gibi büyük miktarlarda metin veya ikili veri içerebilen nesnelerdir. Blobları karşıya yükleyebilir, indirebilir ve listeleyebilir. Ayrıca kapsayıcıları oluşturur, izinleri ayarlar ve siler.

> [!NOTE]
> Bu hızlı başlangıç, Azure Blob depolama istemci kitaplığı 'nın eski bir sürümünü kullanır. En son sürümü kullanmaya başlamak için bkz. [hızlı başlangıç: Java V12 SDK ile Blobları yönetme](storage-quickstart-blobs-java.md).

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Depolama hesabı. [Depolama hesabı oluşturun](../common/storage-account-create.md).
- Maven tümleştirmesi olan bir IDE. Bu kılavuz, "Java geliştiricileri için tutulma IDE" yapılandırması ile [Çakışan Küreler](https://www.eclipse.org/downloads/) kullanır.

## <a name="download-the-sample-application"></a>Örnek uygulamayı indirin:

[Örnek uygulama](https://github.com/Azure-Samples/storage-blobs-java-quickstart) , temel bir konsol uygulamasıdır.

Uygulamanın bir kopyasını geliştirme ortamınıza indirmek için [Git](https://git-scm.com/)'i kullanın.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Bu komut, depoyu yerel Git klasörünüze kopyalar. Projeyi açmak için, Eclipse’ı başlatın ve Karşılama ekranını kapatın. Dosya sistemi ' nden **Dosya** ' yı seçin ve ardından **projeleri açın**. **Proje niteliklerini algıla ve yapılandır** seçeneğinin belirlenmiş olduğundan emin olun. **Dizin**’i seçin ve ardından kopyalanan dizini depoladığınız konuma gidin. Kopyalanan deponun içinde **blobAzureApp** klasörünü seçin. **blobAzureApp** projesinin Eclipse projesi olarak göründüğünden emin olun ve ardından **Son**’u seçin.

Proje içeri aktarmayı tamamladıktan sonra, **AzureApp. Java** ' yı açın ( **src/Main/Java** Içinde **blobquickstart. blobAzureApp** içinde bulunur) ve `accountname` `accountkey` dizenin içindeki ve içindeki öğesini değiştirin `storageConnectionString` . Sonra, uygulamayı çalıştırın. Aşağıdaki bölümlerde bu görevleri tamamlamak için gereken özel yönergeler açıklanmıştır.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma

Bu uygulamada, depolama hesabınız için bağlantı dizesi sağlamanız gerekir. **AzureApp.Java** dosyasını açın. `storageConnectionString` değişkenini bulun ve önceki bölümde kopyaladığınız bağlantı dizesi değerini yapıştırın. `storageConnectionString` değişkeni, aşağıdaki kod örneğine benzer olmalıdır:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Örneği çalıştırma

Bu örnek uygulama, varsayılan dizininizdeki bir sınama dosyası oluşturur (*C:\Users \<user> \Appdata\local\temp*, Windows kullanıcıları Için), blob depolamaya yükler, kapsayıcıdaki Blobları listeler ve ardından eski ve yeni dosyaları karşılaştırabilmeniz için dosyayı yeni bir adla indirir.

Maven’i komut satırında kullanarak örneği çalıştırın. Bir kabuk açın ve kopyalanan dizininiz içindeki **blobAzureApp** öğesine gidin. Sonra `mvn compile exec:java` komutunu girin.

Aşağıdaki örnekte, uygulamayı Windows’da çalıştırmak istemeniz durumunda karşılaşacağınız çıkış gösterilir.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

Devam etmeden önce, örnek dosya için varsayılan dizininizi (Windows kullanıcıları için *C:\Users \<user> \Appdata\local\temp*) denetleyin. Blob depolamadaki dosyanın içeriğini görüntülemek için, blobun URL'sini konsol penceresinden kopyalayın ve tarayıcıya yapıştırın. Dizininizdeki örnek dosyayı Blob depolamada saklanan içerikle karşılaştırırsanız, bunların aynı olduğunu görürsünüz.

  >[!NOTE]
  >Ayrıca, Blob depolamadaki dosyaları görüntülemek için, [Azure Depolama Gezgini](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) gibi bir araç da kullanabilirsiniz. Azure Depolama Gezgini, depolama hesabı bilgilerinize erişmenize olanak tanıyan ücretsiz ve platformlar arası bir araçtır.

Dosyaları doğruladıktan sonra, tanıtımı tamamlayıp test dosyalarını silmek için **ENTER** tuşuna basın. Artık örnek dosyanın ne yaptığını gördüğünüze göre, koda göz atmak için **AzureApp.java** dosyasını açabilirsiniz.

## <a name="understand-the-sample-code"></a>Örnek kodu anlama

Sonraki aşamada, nasıl çalıştığını anlayabilmeniz için örnek kodu inceleyeceğiz.

### <a name="get-references-to-the-storage-objects"></a>Depolama nesneleriyle ilgili başvuruları alma

İlk önce, Blob depolamaya erişmek ve Blob depolamayı yönetmek için kullanılan nesnelere başvuru oluşturmaktır. Bu nesneler birbirleri üzerinde derlenir; her dosya, listede yanında yer alan dosya tarafından kullanılır.

* [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) nesnesinin depolama hesabına işaret eden bir örneğini oluşturun.

    **CloudStorageAccount** nesnesi, depolama hesabınızın bir gösterimidir ve programlamayla depolama hesabı özelliklerini ayarlayabilmenizi ve bu özelliklere erişebilmenizi sağlar. **CloudStorageAccount** nesnesini kullanarak, blob hizmetine erişmek için kullanılan **CloudBlobClient**’in bir örneğini oluşturabilirsiniz.

* **Cloudblobclient** nesnesinin, Depolama hesabınızdaki [BLOB hizmetine](/java/api/com.microsoft.azure.storage.blob.cloudblobclient) işaret eden bir örneğini oluşturun.

    **Cloudblobclient** , blob hizmetine bir erişim noktası sağlar ve bu sayede BLOB depolama özelliklerini program aracılığıyla ayarlamanıza ve erişmenize olanak tanır. **CloudBlobClient** nesnesini kullanarak, kapsayıcı oluşturmak için kullanılan **CloudBlobContainer** ıin bir örneğini oluşturabilirsiniz.

* [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer) nesnesinin, eriştiğiniz kapsayıcıyı temsil eden bir örneğini oluşturun. Tıpkı bilgisayarınızdaki dosyaları düzenlemek için klasörleri kullandığınız gibi blobları düzenlemek için de kapsayıcıları kullanın.

    **Cloudblobcontainer**' ı oluşturduktan sonra, ilgilendiğiniz belirli bir Blobun işaret eden bir [Cloudblockblob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob) nesnesi örneği oluşturabilir ve karşıya yükleme, indirme, kopyalama veya başka bir işlem gerçekleştirebilirsiniz.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcılar hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Kapsayıcı oluşturma

Bu bölümde, nesnelerin örneğini oluşturuyor, yeni kapsayıcı oluşturuyor ve ardından kapsayıcıdaki izinleri bloblar herkese açık olacak ve bloblara yalnızca bir URL ile erişilebilecek şekilde ayarlıyorsunuz. Bu kapsayıcının adı **quickstartcontainer**'dır.

Örnek her çalıştırıldığında yeni bir kapsayıcı oluşturmak istediğimizden, bu örnekte [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists) komutu kullanılır. Bir uygulamanın tamamında aynı kapsayıcıyı kullandığınız bir üretim ortamında, **Createıfnotexists** ' i yalnızca bir kez çağırmak daha iyi bir uygulamadır. Alternatif olarak, kapsayıcıyı önceden oluşturabilirsiniz. Böylece kapsayıcıyı kodda oluşturmanıza gerek kalmaz.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Blobları kapsayıcıya yükleme

Bir blok blobuna bir dosya yüklemek için hedef kapsayıcıda blob 'a bir başvuru alın. Blob başvurusunu aldıktan sonra, [CloudBlockBlob.Upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload) kullanarak verileri karşıya yükleyebilirsiniz. Bu işlemle, daha önce oluşturulmadıysa bir blob oluşturulur, blob zaten varsa blobun üzerine yazılır.

Örnek kod, karşıya yükleme ve indirme için kullanılacak yerel bir dosya oluşturur, karşıya yüklenecek dosyayı **kaynak** olarak ve blob adını **blob** olarak depolar. Aşağıdaki örnek, dosyayı **quickstartcontainer** adlı kapsayıcınıza yükler.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Blob depolama için kullanabileceğiniz [upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadstandardblobtier) ve [uploadText](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadtext)’in de dahil olduğu birkaç `upload` yöntemi vardır. Örneğin, dizelerinizi `Upload` yöntemi yerine `UploadText` yöntemiyle karşıya yükleyebilirsiniz.

Blok blobları herhangi bir metin veya ikili dosya türünde olabilir. Sayfa blobları öncelikli olarak IaaS VM'lerini destekleyen VHD dosyalarında kullanılır. Ekleme bloblarını, bir dosyaya yazıp daha sonradan daha fazla bilgi eklemek istediğiniz durumlarda günlüğe kaydetmek için kullanın. Blob depolamada depolanan nesnelerin çoğu blok blobudur.

### <a name="list-the-blobs-in-a-container"></a>Kapsayıcıdaki blobları listeleme

[CloudBlobContainer.ListBlobs](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobs) kullanarak kapsayıcıdaki dosyaların listesini alabilirsiniz. Aşağıdaki kod blob listesini alır, ardından bu bloblarda döngü yapar ve bulunan blobların URI değerlerini gösterir. Dosyayı görüntülemek için URI değerini komut penceresinden kopyalayıp tarayıcıya yapıştırabilirsiniz.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Blob’ları indirme

[CloudBlob.DownloadToFile](/java/api/com.microsoft.azure.storage.blob.cloudblob.downloadtofile) kullanarak blobları yerel diskinize indirin.

Aşağıdaki kod önceki bir bölümde karşıya yüklenmiş olan blobu indirir; yerel diskinizde her iki dosyayı da görebilmeniz için indirilen blobun adına "_DOWNLOADED" son ekini koyar.

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and
// haven't changed the blob we're interested in, so we can reuse it.
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Kaynakları temizleme

Karşıya yüklediğiniz bloblara artık ihtiyacınız yoksa [Cloudblobcontainer. Deleteıfexists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)kullanarak kapsayıcının tamamını silebilirsiniz. Bu yöntem, kapsayıcıdaki dosyaları da siler.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();

if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Java kullanarak dosyaları yerel bir disk ve Azure Blob depolama arasında aktarmayı öğrendiniz. Java ile çalışma hakkında daha fazla bilgi edinmek için GitHub kaynak kod depomuza devam edin.

> [!div class="nextstepaction"]
> [Java API başvurusu](/java/api/overview/azure/storage?view=azure-java-legacy&preserve-view=true) 
>  [Java Için kod örnekleri](../common/storage-samples-java.md)