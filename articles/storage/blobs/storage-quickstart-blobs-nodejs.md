---
title: 'Hızlı başlangıç: Azure Blob depolama kitaplığı V12-JavaScript'
description: Bu hızlı başlangıçta, blob (nesne) deposunda bir kapsayıcı ve BLOB oluşturmak için JavaScript için Azure Blob depolama istemci kitaplığı sürüm 12 ' yi nasıl kullanacağınızı öğrenirsiniz. Ardından, blob’u yerel bilgisayarınıza indirmeyi ve bir kapsayıcıdaki tüm blobların listesini görüntülemeyi öğreneceksiniz.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/19/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 23cd292162a4c16c4b2b5b1f297d23eb2682013f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168272"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>Hızlı başlangıç: JavaScript için Azure Blob depolama istemci kitaplığı V12

JavaScript için Azure Blob depolama istemci kitaplığı V12 ile çalışmaya başlayın. Azure Blob depolama, Microsoft’un buluta yönelik nesne depolama çözümüdür. Paketi yüklemek ve temel görevler için örnek kodu denemek için adımları izleyin. Blob depolama, çok miktarda yapılandırılmamış veriyi depolamak için iyileştirilmiştir.

> [!NOTE]
> Önceki SDK sürümünü kullanmaya başlamak için bkz. [hızlı başlangıç: JavaScript Için Azure Blob depolama istemci kitaplığı](storage-quickstart-blobs-nodejs-v10.md).

JavaScript için Azure Blob depolama istemci kitaplığı V12 ' nı kullanarak şunları yapın:

* Bir kapsayıcı oluşturma
* Azure depolama 'ya blob yükleme
* Bir kapsayıcıdaki tüm Blobları listeleme
* Blobu yerel bilgisayarınıza indirme
* Kapsayıcı silme

[API başvuru belgeleri](/javascript/api/@azure/storage-blob) | [kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [paketi (düğüm paketi Yöneticisi)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [örnekleri](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* Azure depolama hesabı- [depolama hesabı oluşturma](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* İşletim sisteminiz için geçerli [Node. js](https://nodejs.org/en/download/) .

## <a name="setting-up"></a>Ayarlanıyor

Bu bölümde, bir projeyi, JavaScript için Azure Blob depolama istemci kitaplığı V12 ile çalışacak şekilde hazırlama adımları gösterilmektedir.

### <a name="create-the-project"></a>Proje oluşturma

*BLOB-QuickStart-V12*adlı bir JavaScript uygulaması oluşturun.

1. Konsol penceresinde (cmd, PowerShell veya Bash gibi), proje için yeni bir dizin oluşturun.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Yeni oluşturulan *BLOB-hızlı başlangıç-V12* dizinine geçiş yapın.

    ```console
    cd blob-quickstart-v12
    ```

1. *Package. JSON*adlı yeni bir metin dosyası oluşturun. Bu dosya Node. js projesini tanımlar. Bu dosyayı *BLOB-QuickStart-V12* dizinine kaydedin. Dosyanın içeriği aşağıdadır:

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
    
    İsterseniz `author` alanı için kendi adınızı yerleştirebilirsiniz.
   
### <a name="install-the-package"></a>Paketi yükler

Hala *BLOB-QuickStart-V12* dizininde, `npm install` komutunu kullanarak JavaScript Için Azure Blob Storage istemci kitaplığı 'nı yükleyebilirsiniz. Bu komut *Package. JSON* dosyasını okur ve JavaScript paketi Için Azure Blob Storage istemci kitaplığı V12 ve bağımlı olduğu tüm kitaplıkları kurar.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Proje dizininden:

1. Kod Düzenleyicinizde başka bir yeni metin dosyası açın
1. Azure ve Node. js modüllerini yüklemek için `require` çağrıları ekleme
1. Çok temel özel durum işleme dahil olmak üzere programın yapısını oluşturma

    Kod aşağıdaki gibidir:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Yeni dosyayı *BLOB-QuickStart-V12* dizinine *BLOB-QuickStart-v12. js* olarak kaydedin.

### <a name="copy-your-credentials-from-the-azure-portal"></a>Azure portalından kimlik bilgilerinizi kopyalama

Örnek uygulama, Azure depolama 'ya istek yaptığında yetkilendirilmiş olmalıdır. Bir isteği yetkilendirmek için, depolama hesabı kimlik bilgilerinizi uygulamaya bağlantı dizesi olarak ekleyin. Bu adımları izleyerek depolama hesabı kimlik bilgilerinizi görüntüleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Depolama hesabınızı bulun.
3. Depolama hesabına genel bakışın **Ayarlar** bölümünde **Erişim anahtarları**’nı seçin. Burada, hesap erişim anahtarlarınızı ve her anahtar için tam bağlantı dizesini görüntüleyebilirsiniz.
4. **key1** bölümünde **Bağlantı dizesi** değerini bulun ve **Kopyala** düğmesini seçerek bağlantı dizesini kopyalayın. Sonraki adımda bir ortam değişkenine bağlantı dizesini ekleyeceksiniz.

    ![Azure portalından bağlantı dizesinin kopyalanmasını gösteren ekran görüntüsü](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma

Bağlantı dizenizi kopyaladıktan sonra uygulamayı çalıştıran yerel makine üzerindeki yeni bir ortam değişkenine yazın. Ortam değişkenini ayarlamak için bir konsol penceresi açın ve işletim sisteminizin yönergelerini izleyin. `<yourconnectionstring>` gerçek bağlantı dizeniz ile değiştirin.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Windows 'a ortam değişkenini ekledikten sonra, komut penceresinin yeni bir örneğini başlatmanız gerekir.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Programları yeniden Başlat

Ortam değişkenini ekledikten sonra, ortam değişkenini okumak için gereken tüm çalışan programları yeniden başlatın. Örneğin, devam etmeden önce geliştirme ortamınızı veya düzenleyiciyi yeniden başlatın.

## <a name="object-model"></a>Nesne modeli

Azure Blob depolama, büyük miktarlarda yapılandırılmamış verileri depolamak için iyileştirilmiştir. Yapılandırılmamış veriler, metin veya ikili veriler gibi belirli bir veri modeline veya tanımına bağlı olmayan bir veri. BLOB depolama üç tür kaynak sunar:

* Depolama hesabı
* Depolama hesabındaki bir kapsayıcı
* Kapsayıcıda bir blob

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![BLOB depolama mimarisi diyagramı](./media/storage-blob-introduction/blob1.png)

Şu kaynaklarla etkileşim kurmak için aşağıdaki JavaScript sınıflarını kullanın:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` sınıfı, Azure depolama kaynaklarını ve BLOB kapsayıcılarını değiştirmenize izin verir.
* [Containerclient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` sınıfı, Azure depolama kapsayıcıları ve bloblarını değiştirmenize olanak sağlar.
* [Blobclient](/javascript/api/@azure/storage-blob/blobclient): `BlobClient` sınıfı, Azure depolama bloblarını değiştirmenize izin verir.

## <a name="code-examples"></a>Kod örnekleri

Bu örnek kod parçacıkları, JavaScript için Azure Blob depolama istemci kitaplığı ile aşağıdakilerin nasıl gerçekleştirileceğini göstermektedir:

* [Bağlantı dizesini al](#get-the-connection-string)
* [Kapsayıcı oluşturma](#create-a-container)
* [Blobları bir kapsayıcıya yükleme](#upload-blobs-to-a-container)
* [Bir kapsayıcıdaki Blobları listeleme](#list-the-blobs-in-a-container)
* [Blob 'ları indir](#download-blobs)
* [Kapsayıcı silme](#delete-a-container)

### <a name="get-the-connection-string"></a>Bağlantı dizesini alma

Aşağıdaki kod, depolama [bağlantı dizesini yapılandırma](#configure-your-storage-connection-string) bölümünde oluşturulan ortam değişkeninden depolama hesabının bağlantı dizesini alır.

Bu kodu `main` işlevi içine ekleyin:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Yeni kapsayıcı için bir ad belirleyin. Aşağıdaki kod, benzersiz olduğundan emin olmak için kapsayıcı adına bir UUID değeri ekler.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcıları ve blobları adlandırma hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

[Fromconnectionstring](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) metodunu çağırarak [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) sınıfının bir örneğini oluşturun. Ardından, bir kapsayıcıya başvuru almak için [Getcontainerclient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) metodunu çağırın. Son olarak, depolama hesabınızda kapsayıcıyı gerçekten oluşturmak için [Oluştur](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) ' u çağırın.

`main` işlevinin sonuna bu kodu ekleyin:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(CONNECT_STR);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Blobları bir kapsayıcıya yükleme

Aşağıdaki kod parçacığı:

1. Bir bloba yüklemek için bir metin dizesi oluşturur.
1. [Kapsayıcı oluşturma](#create-a-container) bölümünde [Containerclient](/javascript/api/@azure/storage-blob/containerclient) üzerinde [getblockblobclient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) metodunu çağırarak bir [blockblobclient](/javascript/api/@azure/storage-blob/blockblobclient) nesnesine bir başvuru alır.
1. [Karşıya yükleme](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) yöntemini çağırarak metin dizesi verilerini bloba yükler.

`main` işlevinin sonuna bu kodu ekleyin:

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

### <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

[Listblobsflat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) yöntemini çağırarak kapsayıcıdaki Blobları listeleyin. Bu durumda, kapsayıcıya yalnızca bir blob eklenmiş, bu nedenle listeleme işlemi yalnızca bir BLOB döndürüyor.

`main` işlevinin sonuna bu kodu ekleyin:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Blob’ları indirme

[Download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) metodunu çağırarak, önceden oluşturulmuş blobu indirin. Örnek kod, bir Node. js okunabilir akışını bir dizeye okumak için kullanılan `streamToString` adlı bir yardımcı işlevi içerir.

`main` işlevinin sonuna bu kodu ekleyin:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

`main` işlevinden *sonra* bu yardımcı işlevi ekleyin:

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

`main` işlevinin sonuna bu kodu ekleyin:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Kodu çalıştırma

Bu uygulama bir metin dizesi oluşturur ve BLOB depolamaya yükler. Örnek daha sonra kapsayıcıdaki Blobları listeler, blobu indirir ve indirilen verileri görüntüler.

Konsol isteminde, *BLOB-QuickStart-v12.py* dosyasını içeren dizine gidin, ardından uygulamayı çalıştırmak için aşağıdaki `node` komutunu yürütün.

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

Hata Ayıklayıcıdaki kodda adım adım ilerleyin ve işlem boyunca Azure portal denetleyin. Kapsayıcının oluşturulup oluşturulmakta olup olmadığını kontrol edin. Blobu kapsayıcının içinde açabilir ve içeriği görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, JavaScript kullanarak Blobları karşıya yükleme, indirme ve listeleme hakkında daha fazla öğrendiniz.

BLOB depolama örnek uygulamalarını görmek için devam edin:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 JavaScript örnekleri](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

* Daha fazla bilgi için bkz. [JavaScript Için Azure SDK](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md).
* Öğreticiler, örnekler, hızlı ve diğer belgeler için, [JavaScript Için Azure SDK belgelerini](/azure/javascript/)ziyaret edin.
