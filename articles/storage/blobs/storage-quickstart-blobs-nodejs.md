---
title: 'Hızlı başlangıç: Azure Blob depolama kitaplığı V12-JavaScript'
description: Bu hızlı başlangıçta, blob (nesne) deposunda bir kapsayıcı ve BLOB oluşturmak için JavaScript için Azure Blob depolama istemci kitaplığı sürüm 12 ' yi nasıl kullanacağınızı öğrenirsiniz. Ardından, blob’u yerel bilgisayarınıza indirmeyi ve bir kapsayıcıdaki tüm blobların listesini görüntülemeyi öğreneceksiniz.
author: twooley
ms.author: twooley
ms.date: 09/17/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 6e26a375ebdc97026de09511b0bc0f11fe073e41
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280131"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Hızlı başlangıç: Node.js içindeki JavaScript V12 SDK ile Blobları yönetme

Bu hızlı başlangıçta, Node.js kullanarak blob 'ları yönetmeyi öğreneceksiniz. Blob 'lar, görüntüler, belgeler, akış ortamları ve arşiv verileri gibi büyük miktarlarda metin veya ikili veri içerebilen nesnelerdir. Blobları karşıya yükleyebilir, indirebilir ve listetireceksiniz ve kapsayıcı oluşturup sileceksiniz.

Ek kaynaklar:

* [API başvuru belgeleri](/javascript/api/@azure/storage-blob)
* [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Paket (düğüm Paket Yöneticisi)](https://www.npmjs.com/package/@azure/storage-blob)
* [Örnekler](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Depolama hesabı. [Depolama hesabı oluşturun](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Ayarlanıyor

Bu bölümde, bir projeyi, JavaScript için Azure Blob depolama istemci kitaplığı V12 ile çalışacak şekilde hazırlama adımları gösterilmektedir.

### <a name="create-the-project"></a>Proje oluşturma

*BLOB-QuickStart-V12* adlı bir JavaScript uygulaması oluşturun.

1. Konsol penceresinde (cmd, PowerShell veya Bash gibi), proje için yeni bir dizin oluşturun.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Yeni oluşturulan *BLOB-hızlı başlangıç-V12* dizinine geçiş yapın.

    ```console
    cd blob-quickstart-v12
    ```

1. *Üzerindepackage.js* adlı yeni bir metin dosyası oluşturun. Bu dosya Node.js projesi tanımlar. Bu dosyayı *BLOB-QuickStart-V12* dizinine kaydedin. Dosyanın içeriği aşağıdadır:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    İsterseniz alanı için kendi adınızı koyabilirsiniz `author` .

### <a name="install-the-package"></a>Paketi yükler

Hala *BLOB-QuickStart-V12* dizininde, komutunu kullanarak JavaScript Için Azure Blob Storage istemci kitaplığı 'nı yükleyebilirsiniz `npm install` . Bu komut, dosyadaki *package.js* okur ve JavaScript paketi Için Azure Blob Storage istemci kitaplığı V12 ve bağımlı olduğu tüm kitaplıkları kurar.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Proje dizininden:

1. Kod Düzenleyicinizde başka bir yeni metin dosyası açın
1. `require`Azure ve Node.js modüllerini yüklemek için çağrılar ekleme
1. Temel özel durum işleme dahil olmak üzere programın yapısını oluşturma

    Kod şu şekildedir:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const { v1: uuidv1} = require('uuid');

    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Yeni dosyayı *BLOB-QuickStart-V12* dizinine *blob-quickstart-v12.js* olarak kaydedin.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Nesne modeli

Azure Blob depolama, büyük miktarlarda yapılandırılmamış verileri depolamak için iyileştirilmiştir. Yapılandırılmamış veriler, metin veya ikili veriler gibi belirli bir veri modeline veya tanıma bağlı olmayan verilerdir. BLOB depolama üç tür kaynak sunar:

* Depolama hesabı
* Depolama hesabındaki bir kapsayıcı
* Kapsayıcıda bir blob

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![BLOB depolama mimarisi diyagramı](./media/storage-blobs-introduction/blob1.png)

Şu kaynaklarla etkileşim kurmak için aşağıdaki JavaScript sınıflarını kullanın:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` sınıfı, Azure depolama kaynaklarını ve BLOB kapsayıcılarını değiştirmenize olanak sağlar.
* [Containerclient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` sınıfı, Azure depolama kapsayıcılarını ve bunların bloblarını değiştirmenize olanak sağlar.
* [Blobclient](/javascript/api/@azure/storage-blob/blobclient): `BlobClient` sınıfı, Azure Storage bloblarını değiştirmenize izin verir.

## <a name="code-examples"></a>Kod örnekleri

Bu örnek kod parçacıkları, JavaScript için Azure Blob depolama istemci kitaplığı ile aşağıdakilerin nasıl gerçekleştirileceğini göstermektedir:

* [Bağlantı dizesini alma](#get-the-connection-string)
* [Kapsayıcı oluşturma](#create-a-container)
* [Blobları bir kapsayıcıya yükleme](#upload-blobs-to-a-container)
* [Kapsayıcıdaki blobları listeleme](#list-the-blobs-in-a-container)
* [Blob’ları indirme](#download-blobs)
* [Kapsayıcı silme](#delete-a-container)

### <a name="get-the-connection-string"></a>Bağlantı dizesini alma

Aşağıdaki kod, depolama [bağlantı dizesini yapılandırma](#configure-your-storage-connection-string) bölümünde oluşturulan ortam değişkeninden depolama hesabının bağlantı dizesini alır.

Bu kodu işlevin içine ekleyin `main` :

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Kapsayıcı oluşturma

Yeni kapsayıcı için bir ad belirleyin. Aşağıdaki kod, benzersiz olduğundan emin olmak için kapsayıcı adına bir UUID değeri ekler.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcıları ve blobları adlandırma hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

[Fromconnectionstring](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) metodunu çağırarak [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) sınıfının bir örneğini oluşturun. Ardından, bir kapsayıcıya başvuru almak için [Getcontainerclient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) metodunu çağırın. Son olarak, depolama hesabınızda kapsayıcıyı gerçekten oluşturmak için [Oluştur](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) ' u çağırın.

Bu kodu işlevin sonuna ekleyin `main` :

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Blobları bir kapsayıcıya yükleme

Aşağıdaki kod parçacığı:

1. Bir bloba yüklemek için bir metin dizesi oluşturur.
1. [Kapsayıcı oluşturma](#create-a-container) bölümünde [Containerclient](/javascript/api/@azure/storage-blob/containerclient) üzerinde [getblockblobclient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) metodunu çağırarak bir [blockblobclient](/javascript/api/@azure/storage-blob/blockblobclient) nesnesine bir başvuru alır.
1. [Karşıya yükleme](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) yöntemini çağırarak metin dizesi verilerini bloba yükler.

Bu kodu işlevin sonuna ekleyin `main` :

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>Kapsayıcıdaki blobları listeleme

[Listblobsflat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) yöntemini çağırarak kapsayıcıdaki Blobları listeleyin. Bu durumda, kapsayıcıya yalnızca bir blob eklenmiş, bu nedenle listeleme işlemi yalnızca bir BLOB döndürüyor.

Bu kodu işlevin sonuna ekleyin `main` :

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Blob’ları indirme

[Download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) metodunu çağırarak, önceden oluşturulmuş blobu indirin. Örnek kod `streamToString` , Node.js okunabilir bir akışı bir dizeye okumak için kullanılan adlı bir yardımcı işlevi içerir.

Bu kodu işlevin sonuna ekleyin `main` :

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

İşlevden *sonra* bu yardımcı işlevi ekleyin `main` :

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Kapsayıcı silme

Aşağıdaki kod, [Delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) yöntemi kullanılarak kapsayıcının tamamını kaldırarak uygulamanın oluşturduğu kaynakları temizler. Ayrıca, isterseniz yerel dosyaları silebilirsiniz.

Bu kodu işlevin sonuna ekleyin `main` :

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Kodu çalıştırma

Bu uygulama bir metin dizesi oluşturur ve BLOB depolamaya yükler. Örnek daha sonra kapsayıcıdaki Blobları listeler, blobu indirir ve indirilen verileri görüntüler.

Konsol isteminde *blob-quickstart-v12.js* dosyasını içeren dizine gidin, ardından `node` uygulamayı çalıştırmak için aşağıdaki komutu yürütün.

```console
node blob-quickstart-v12.js
```

Uygulamanın çıktısı aşağıdaki örneğe benzer:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Hata Ayıklayıcıdaki kodda adım adım ilerleyin ve işlem boyunca [Azure Portal](https://portal.azure.com) denetleyin. Kapsayıcının oluşturulup oluşturulmakta olup olmadığını kontrol edin. Blobu kapsayıcının içinde açabilir ve içeriği görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, JavaScript kullanarak Blobları karşıya yükleme, indirme ve listeleme hakkında daha fazla öğrendiniz.

Öğreticiler, örnekler, hızlı başlangıçler ve diğer belgeler için şu adresi ziyaret edin:

> [!div class="nextstepaction"]
> [JavaScript için Azure Geliştirici Merkezi](/azure/developer/javascript/)

* Azure Blob depolama kullanan bir Web uygulamasını dağıtmayı öğrenmek için bkz [. Öğretici: Azure Storage ile buluta görüntü verileri yükleme](./storage-upload-process-images.md?preserve-view=true&tabs=javascript)
* BLOB depolama örnek uygulamalarını görmek için [Azure Blob depolama istemci kitaplığı V12 JavaScript örnekleri](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)' ne geçin.
* Daha fazla bilgi edinmek için bkz. [JavaScript Için Azure Blob depolama istemci kitaplığı](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
