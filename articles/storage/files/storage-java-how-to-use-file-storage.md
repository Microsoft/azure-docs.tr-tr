---
title: Java ile Azure dosyaları için geliştirme | Microsoft Docs
description: Dosya verilerini depolamak için Azure dosyaları kullanan Java uygulamaları ve hizmetleri geliştirmeyi öğrenin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 115c55a5833906aa0dcc616a5b1b659468647282
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814559"
---
# <a name="develop-for-azure-files-with-java"></a>Java ile Azure Dosyaları için geliştirme

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Verileri depolamak için Azure dosyaları kullanan Java uygulamaları geliştirme temellerini öğrenin. Bir konsol uygulaması oluşturun ve Azure dosyaları API 'Lerini kullanarak temel eylemleri öğrenin:

- Azure dosya paylaşımları oluşturma ve silme
- Dizin oluşturma ve silme
- Azure dosya paylaşımındaki dosyaları ve dizinleri listeleme
- Dosya yükleme, indirme ve silme

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Java uygulaması oluşturma

Örnekleri derlemek için Java Geliştirme Seti (JDK) ve [Java Için Azure depolama SDK 'sı](https://github.com/azure/azure-sdk-for-java)gerekir. Ayrıca bir Azure depolama hesabı oluşturmuş olmanız gerekir.

## <a name="set-up-your-application-to-use-azure-files"></a>Uygulamanızı Azure dosyalarını kullanacak şekilde ayarlama

Azure dosyaları API 'Lerini kullanmak için aşağıdaki kodu, Azure dosyalarına erişmeyi planladığınız Java dosyasının en üstüne ekleyin.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Azure depolama bağlantı dizesi ayarlama

Azure dosyalarını kullanmak için Azure depolama hesabınıza bağlanmanız gerekir. Bir bağlantı dizesi yapılandırın ve depolama hesabınıza bağlanmak için kullanın. Bağlantı dizesini tutacak bir statik değişken tanımlayın.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK V12](#tab/java)

*\<storage_account_name\>* Ve *\<storage_account_key\>* değerlerini depolama hesabınızın gerçek değerleriyle değiştirin.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

*Your_storage_account_name* ve *your_storage_account_key* değerini depolama hesabınızın gerçek değerleriyle değiştirin.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-an-azure-file-share"></a>Azure dosya paylaşımıyla erişme

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK V12](#tab/java)

Azure dosyalarına erişmek için bir [Shareclient](/java/api/com.azure.storage.file.share.shareclient) nesnesi oluşturun. Yeni bir **Shareclient** nesnesi derlemek için [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) sınıfını kullanın.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Depolama hesabınıza erişmek için, **Cloudstorageaccount** nesnesini kullanarak bağlantı dizesini **ayrıştırma** yöntemine geçirerek.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**Cloudstorageaccount. Parse** bir ınvalidkeyexception oluşturur, bu nedenle bunu bir try/catch bloğunun içine koymanız gerekir.

---

## <a name="create-a-file-share"></a>Dosya paylaşımı oluşturma

Azure dosyalarındaki tüm dosyalar ve dizinler, Share adlı bir kapsayıcıda depolanır.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK V12](#tab/java)

Paylaşım zaten varsa, [Shareclient. Create](/java/api/com.azure.storage.file.share.shareclient.create) yöntemi bir özel durum oluşturur. Bir blokta **oluşturma** çağrısını koyun `try/catch` ve özel durumu işleyin.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Bir paylaşıma ve içeriğine erişim elde etmek için bir Azure dosyaları istemcisi oluşturun.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Azure dosyaları istemcisini kullanarak bir paylaşıma yönelik bir başvuru elde edebilirsiniz.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Paylaşımı gerçekten oluşturmak için **Cloudfileshare** nesnesinin **Createifnotexists** metodunu kullanın.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Bu noktada **Share** , **örnek paylaşma** adlı bir paylaşımın başvurusunu tutar.

---

## <a name="delete-a-file-share"></a>Dosya paylaşımını silme

Aşağıdaki örnek kod bir dosya paylaşımından siler.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK V12](#tab/java)

[Shareclient. Delete](/java/api/com.azure.storage.file.share.shareclient.delete) yöntemini çağırarak bir paylaşım silin.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Bir **Cloudfileshare** nesnesinde **deleteifexists** metodunu çağırarak bir paylaşımı silin.

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

---

## <a name="create-a-directory"></a>Dizin oluşturma

Dosyaları kök dizinde kullanmak yerine alt dizinlere yerleştirerek depolamayı düzenleyin.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK V12](#tab/java)

Aşağıdaki kod, bir dizin [oluşturur.](/java/api/com.azure.storage.file.share.sharedirectoryclient.create) Örnek yöntem, `Boolean` dizinin başarıyla oluşturulup oluşturulmadığını gösteren bir değer döndürür.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Aşağıdaki kod, kök dizin altında **sampledir** adlı bir alt dizin oluşturur.

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

---

## <a name="delete-a-directory"></a>Bir dizini silme

Dizin silindiğinde basit bir görevdir. Hala dosya veya alt dizinler içeren bir dizini silemezsiniz.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK V12](#tab/java)

Dizin yoksa veya boş değilse, [Sharedirectoryclient. Delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) yöntemi bir özel durum oluşturur. Bir blokta **Delete** çağrısını koyun `try/catch` ve özel durumu işleyin.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

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

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure dosya paylaşımındaki dosyaları ve dizinleri listeleme

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK V12](#tab/java)

Dosya ve dizinlerin bir listesini, [Sharedirectoryclient. Listfilesanddizinleri](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories)çağırarak alın. Yöntemi, üzerinde yineleyebilir olan [Sharefileıtem](/java/api/com.azure.storage.file.share.models.sharefileitem) nesnelerinin bir listesini döndürür. Aşağıdaki kod, *dirname* parametresi tarafından belirtilen dizin içindeki dosya ve dizinleri listeler.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Bir **Cloudfiledirectory** başvurusunda **listfilesanddizinler** çağırarak dosya ve dizinlerin listesini alın. Yöntemi, üzerinde yineleyebilir **Listfileıtem** nesnelerinin bir listesini döndürür. Aşağıdaki kod, kök dizin içindeki dosya ve dizinleri listeler.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Dosyayı karşıya yükleme

Yerel depolama alanından bir dosyayı karşıya yüklemeyi öğrenin.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK V12](#tab/java)

Aşağıdaki kod, [Sharefileclient. uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) yöntemini çağırarak Azure dosya depolama 'ya yerel bir dosya yükler. Aşağıdaki örnek yöntemi, `Boolean` belirtilen dosyayı başarıyla karşıya yüklendiğini gösteren bir değer döndürür.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Share nesnesinde **Getrootdirectoryreference** metodunu çağırarak dosyanın karşıya yükleneceği dizine bir başvuru alın.

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

---

## <a name="download-a-file"></a>Dosya indirme

Daha sık gerçekleştirilen işlemlerden biri dosyaları bir Azure dosya paylaşımından indirmenin bir kopyası.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK V12](#tab/java)

Aşağıdaki örnek, belirtilen dosyayı *DestDir* parametresinde belirtilen yerel dizine indirir. Örnek yöntem, karşıdan yüklenen dosya adının tarih ve saati ön bekliyor olarak benzersiz hale getirir.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Aşağıdaki örnek SampleFile.txt indirir ve içeriğini görüntüler.

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

---

## <a name="delete-a-file"></a>Dosyayı silme

Başka bir yaygın Azure dosyaları işlemi dosya silme işlemidir.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK V12](#tab/java)

Aşağıdaki kod belirtilen belirtilen dosyayı siler. İlk olarak, örnek, *dirname* parametresine göre bir [parça edirectoryclient](/java/api/com.azure.storage.file.share.sharedirectoryclient) oluşturur. Ardından, kod *dosya adı* parametresine bağlı olarak dizin Istemcisinden bir [sharefileclient](/java/api/com.azure.storage.file.share.sharefileclient) alır. Son olarak, örnek yöntemi dosyayı silmek için [Sharefileclient. Delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) ' i çağırır.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Aşağıdaki kod, **sampledir** adlı bir dizinin içinde depolanan SampleFile.txt adlı bir dosyayı siler.

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

---

## <a name="next-steps"></a>Sonraki adımlar

Diğer Azure depolama API 'Leri hakkında daha fazla bilgi edinmek istiyorsanız bu bağlantıları izleyin.

- [Java geliştiricileri için Azure](/azure/developer/java)
- [Java için Azure SDK](https://github.com/azure/azure-sdk-for-java)
- [Android için Azure SDK](https://github.com/azure/azure-sdk-for-android)
- [Java SDK 'Sı için Azure dosya paylaşma istemci kitaplığı başvurusu](/java/api/overview/azure/storage-file-share-readme)
- [Azure Storage Hizmetleri REST API’si](/rest/api/storageservices/)
- [Azure Depolama Ekibi Blogu](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [AzCopy Command-Line yardımcı programıyla veri aktarma](../common/storage-use-azcopy-v10.md)
- [Azure Dosyaları sorunlarını giderme - Windows](storage-troubleshoot-windows-file-connection-problems.md)
