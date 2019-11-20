---
title: Java ile Azure dosyaları için geliştirme | Microsoft Docs
description: Dosya verilerini depolamak için Azure dosyaları kullanan Java uygulamaları ve hizmetleri geliştirmeyi öğrenin.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 28a280ea7c3bf9ef84a1fff05da5090ed526fb12
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837469"
---
# <a name="develop-for-azure-files-with-java"></a>Java ile Azure dosyaları için geliştirme
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Bu öğretici hakkında
Bu öğretici, dosya verilerini depolamak için Azure dosyaları kullanan uygulamaları veya hizmetleri geliştirmek üzere Java kullanmanın temellerini gösterir. Bu öğreticide, bir konsol uygulaması oluşturacağız ve Java ve Azure dosyaları ile nasıl temel eylemler gerçekleştirileceğini göstereceğiz:

* Azure dosya paylaşımları oluşturma ve silme
* Dizin oluşturma ve silme
* Azure dosya paylaşımındaki dosyaları ve dizinleri listeleme
* Dosya yükleme, indirme ve silme

> [!Note]  
> Azure dosyalarına SMB üzerinden erişilebildiğinden, standart Java g/ç sınıflarını kullanarak Azure dosya paylaşımında erişim sağlayan uygulamalar yazmak mümkündür. Bu makalede, Azure dosyaları ile iletişim sağlamak için [REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) Azure Storage Java SDK 'sını kullanan uygulamaların nasıl yazılacağı açıklanır.

## <a name="create-a-java-application"></a>Java uygulaması oluşturma
Örnekleri derlemek için Java Geliştirme Seti (JDK) ve [Java Için Azure depolama SDK 'sı](https://github.com/Azure/azure-storage-java)gerekir. Ayrıca bir Azure depolama hesabı oluşturmuş olmanız gerekir.

## <a name="set-up-your-application-to-use-azure-files"></a>Uygulamanızı Azure dosyalarını kullanacak şekilde ayarlama
Azure depolama API 'Lerini kullanmak için aşağıdaki ifadeyi, ' den depolama hizmetine erişmeyi planladığınız Java dosyasının en üstüne ekleyin.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure depolama bağlantı dizesi ayarlama
Azure dosyalarını kullanmak için Azure depolama hesabınıza bağlanmanız gerekir. İlk adım, depolama hesabınıza bağlanmak için kullanacağımız bir bağlantı dizesi yapılandırmak olacaktır. Bunu yapmak için bir statik değişken tanımlayalim.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Your_storage_account_name ve your_storage_account_key değerini depolama hesabınızın gerçek değerleriyle değiştirin.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Azure depolama hesabına bağlanma
Depolama hesabınıza bağlanmak için, **Cloudstorageaccount** nesnesini kullanarak bir bağlantı dizesini **ayrıştırma** yöntemine geçirerek kullanmanız gerekir.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**Cloudstorageaccount. Parse** bir ınvalidkeyexception oluşturur, bu nedenle bunu bir try/catch bloğunun içine koymanız gerekir.

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma
Azure dosyalarındaki tüm dosyalar ve dizinler, **Share**adlı bir kapsayıcıda bulunur. Depolama hesabınızda, hesap kapasiteniz izin verdiğinden çok sayıda paylaşım olabilir. Bir paylaşıma ve içeriğine erişim sağlamak için bir Azure dosyaları istemcisi kullanmanız gerekir.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Azure dosyaları istemcisini kullanarak bir paylaşıma yönelik bir başvuru elde edebilirsiniz.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Paylaşımı gerçekten oluşturmak için CloudFileShare nesnesinin **Createifnotexists** metodunu kullanın.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Bu noktada, **Share** **sampleshare**adlı bir paylaşımın başvurusunu barındırır.

## <a name="delete-an-azure-file-share"></a>Azure dosya paylaşımından silme
Bir paylaşımın silinmesi, CloudFileShare nesnesinde **Deleteifexists** yöntemi çağrılarak yapılır. Örnek kod aşağıda verilmiştir.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Dizin oluşturma
Ayrıca, dosyaları kök dizinde kullanmak yerine alt dizinlere yerleştirerek depolamayı düzenleyebilirsiniz. Azure dosyaları, hesabınızın izin verdiği sayıda dizin oluşturmanıza olanak sağlar. Aşağıdaki kod, kök dizin altında **sampledir** adlı bir alt dizin oluşturacak.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Bir dizini silme
Bir dizini silme işlemi, hala dosyalar veya diğer dizinler içeren bir dizini silememenizin belirtilemediği halde, basit bir görevdir.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure dosya paylaşımındaki dosyaları ve dizinleri listeleme
Bir paylaşımdaki dosya ve dizinlerin listesini almak, bir CloudFileDirectory başvurusunda **Listfilesanddizinler** çağrılarak kolayca yapılır. Yöntemi, yineleyebilir Listfileıtem nesnelerinin bir listesini döndürür. Örnek olarak, aşağıdaki kod kök dizinin içindeki dosya ve dizinleri listeler.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Dosyayı karşıya yükleme
Bu bölümde yerel depolama alanından bir paylaşımın kök dizinine dosya yüklemeyi öğreneceksiniz.

Bir dosyayı karşıya yüklemek için ilk adım, bulunması gereken dizine bir başvuru elde etmelidir. Bunu, Share nesnesinin **Getrootdirectoryreference** yöntemini çağırarak yapabilirsiniz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Artık paylaşımın kök dizinine başvurunuz olduğuna göre, aşağıdaki kodu kullanarak bir dosyayı üzerine yükleyebilirsiniz.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Dosya indirme
Azure dosyalarına karşı gerçekleştirdiğiniz daha sık gerçekleştirilen işlemlerden biri dosyaları indirilecektir. Aşağıdaki örnekte, kod SampleFile. txt ' yi indirir ve içeriğini görüntüler.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Dosyayı silme
Başka bir yaygın Azure dosyaları işlemi dosya silme işlemidir. Aşağıdaki kod SampleFile. txt adlı bir dosyayı **sampledir**adlı bir dizin içinde saklı olarak siler.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>Sonraki adımlar
Diğer Azure depolama API 'Leri hakkında daha fazla bilgi edinmek istiyorsanız bu bağlantıları izleyin.

* [Java geliştiricileri Için Azure](/java/azure)/)
* [Java için Azure depolama SDK 'Sı](https://github.com/azure/azure-storage-java)
* [Android için Azure depolama SDK 'Sı](https://github.com/azure/azure-storage-android)
* [Azure Depolama İstemcisi SDK Başvurusu](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [Azure Depolama Hizmetleri REST API'si](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Depolama Ekibi Blog’u](https://blogs.msdn.com/b/windowsazurestorage/)
* [AzCopy Komut Satırı Yardımcı Programı ile veri aktarımı](../common/storage-use-azcopy.md)
* [Azure Dosyaları sorunlarını giderme - Windows](storage-troubleshoot-windows-file-connection-problems.md)
