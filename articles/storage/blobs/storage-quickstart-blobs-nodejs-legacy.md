---
title: 'Hızlı başlangıç: JavaScript için Azure Blob depolama istemci kitaplığı v2'
description: Nesne (Blob) depolamada depolama hesabı ve kapsayıcı oluşturun. Ardından, Azure depolama 'ya blob yüklemek, blob indirmek ve bir kapsayıcıdaki Blobları listelemek için Node. js v2 için Azure Storage istemci kitaplığı 'nı kullanın.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/04/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 12078300b36412a1507991507a39654acdeb7c69
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825341"
---
# <a name="quickstart-azure-blob-storage-client-library-v2-for-javascript"></a>Hızlı başlangıç: JavaScript için Azure Blob depolama istemci kitaplığı v2

Bu nasıl yapılır kılavuzunda, Azure Blob depolama ile Blobları karşıya yüklemek, indirmek ve listelemek için Node. js v2 için istemci kitaplığı 'nı nasıl kullanacağınızı öğreneceksiniz.

> [!TIP]
> Node. js için Azure Storage istemci kitaplığı 'nın en son sürümü ile v10 arasındaki 'dir. Microsoft, mümkün olduğunda istemci kitaplığı 'nın en son sürümünü kullanmanızı önerir. İle v10 arasındaki kullanmaya başlamak için bkz. [hızlı başlangıç: JavaScript Için Azure Storage istemci kitaplığı 'nı kullanarak Blobları karşıya yükleme, indirme, listeleme ve silme (Önizleme)](storage-quickstart-blobs-nodejs-v10.md).

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[Azure Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM)bir Azure depolama hesabı oluşturun. Hesap oluşturmayla ilgili yardım için bkz. [Depolama hesabı oluşturma](../common/storage-quickstart-create-account.md).

## <a name="download-the-sample-application"></a>Örnek uygulamayı indirin:

[Örnek uygulama](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) basit bir Node. js konsol uygulamasıdır. Başlamak için, aşağıdaki komutu kullanarak depoyu makinenize kopyalayın:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Uygulamayı açmak için, *storage-blobs-node-quickstart* klasörünü arayın ve sık kullandığınız kod düzenleme ortamınızda açın.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma

Uygulamayı çalıştırmadan önce, depolama hesabınız için bağlantı dizesi sağlamanız gerekir. Örnek depo, *.env.example* adlı bir dosya içerir. *.example* uzantısını kaldırarak bu dosyayı yeniden adlandırabilirsiniz. Böylece *.env* adlı bir dosya elde edilir. *.env* dosyasının içinde, *AZURE_STORAGE_CONNECTION_STRING* anahtarının sonrasına bağlantı dizesi değerinizi ekleyin.

## <a name="install-required-packages"></a>Gerekli paketleri yükleme

Uygulama dizininde *npm install* komutunu çalıştırarak uygulama için gerekli paketleri yükleyin.

```bash
npm install
```

## <a name="run-the-sample"></a>Örneği çalıştırma
Bağımlılıklar yüklendiğine göre aşağıdaki komutu göndererek örneği çalıştırabilirsiniz:

```bash
npm start
```

Betikten çıkış şununla benzerlik gösterecektir:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Bu örnek için yeni bir depolama hesabı kullanıyorsanız, "*kapsayıcılar*" etiketi altında listelenen herhangi bir kapsayıcı adı göremeyebilirsiniz.

## <a name="understanding-the-code"></a>Kodu anlama
İlk ifade değerleri ortam değişkenlerine yüklemek için kullanılır.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

*dotenv* modülü uygulama hata ayıklama için yerel olarak çalıştırılırken ortam değişkenlerini yükler. Değerler *.env* adlı bir dosyada tanımlanır ve geçerli yürütme bağlamına yüklenir. Üretim bağlamlarında, bu değerleri sunucu yapılandırması sağlar ve bu nedenle bu kod yalnızca betik bir “üretim” bağlamı altında çalıştırılmadığında çalıştırılır.

```javascript
const path = require('path');
const storage = require('azure-storage');
```

Modüllerin amacı aşağıdaki gibidir: 

*.env* adlı dosya geçerli yürütme bağlamına
- *path*, blob depolamaya yüklenecek dosyanın mutlak dosya yolunu belirlemek için gereklidir
- *Azure-Storage* , Node. js Için [Azure depolama istemci kitaplığı](https://docs.microsoft.com/javascript/api/azure-storage) modülüdür

Daha sonra, **blobService** değişkeni Azure Blob hizmetinin yeni bir örneği olarak başlatılır.

```javascript
const blobService = storage.createBlobService();
```

Aşağıdaki uygulamada, *blobService* işlevlerinin her biri bir *Promise* içinde kaydırılır ve bu da *Azure Depolama API*’sinin geri çağrı yapısını kolaylaştırmak için JavaScript'in *async* işlevine ve [await](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) işlecine erişilmesini sağlar. Her bir işlev için başarılı bir yanıt döndürüldüğünde Promise, eyleme özgü bir iletiyle birlikte alakalı verilerle çözümleme gerçekleştirir.

### <a name="list-containers"></a>Kapsayıcıları listeleme

*listContainers* işlevi gruplar içindeki kapsayıcıların koleksiyonlarını döndüren [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) öğesini çağırır.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

Grupların boyutu [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest) ile yapılandırılabilir. *listContainersSegmented* çağrıldığında, [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest) örnekleri dizisi olarak blob meta verileri döndürülür. Sonuçlar, 5.000’er olarak artan toplu işler (segmentler) halinde döndürülür. Bir kapsayıcıda 5.000’den fazla blob varsa, sonuçlar *continuationToken* için bir değer içerir. Blob kapsayıcısında yer alan sonraki segmentleri listelemek için, ikinci bağımsız değişken olarak devamlılık belirtecini *listContainersSegment* öğesine geri gönderebilirsiniz.

### <a name="create-a-container"></a>Bir kapsayıcı oluşturma

*createContainer* işlevi, [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) öğesini çağırır ve blob için uygun erişim düzeyini ayarlar.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

*createContainerIfNotExists* için ikinci parametre (**options**), [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) için bir değer kabul eder. *publicAccessLevel* için *blob* değeri, belirli blob verilerinin genel kullanıma sunulduğunu belirtir. Bu ayar, kapsayıcının içeriklerini listeleme yeteneği sağlayan *kapsayıcı* düzeyinde erişimin tersidir.

**createContainerIfNotExists** kullanılması, kapsayıcı önceden mevcut olduğunda uygulamanın hata döndürmeden *createContainer* komutunu birçok defa çalıştırmasına olanak sağlar. Üretim ortamında, uygulama genelinde aynı kapsayıcı kullanıldığında çoğu zaman **createContainerIfNotExists** komutunu yalnızca bir defa çağırırsınız. Bu durumlarda, önceden portal veya Azure CLI aracılığıyla kapsayıcı oluşturabilirsiniz.

### <a name="upload-text"></a>Metni karşıya yükleme

*uploadString* işlevi [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) öğesini blob kapsayıcısına rastgele bir dize yazmak (veya dizenin üzerine yazmak) için çağırır.

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Yerel bir dosyayı karşıya yükleme

*uploadLocalFile* işlevi, dosya sistemindeki bir dosyayı blob depolamaya yüklemek ve yazmak veya söz konusu dosyanın üzerine yazmak için [createBlockBlobFromLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--) işlevini kullanır. 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
Bloblara içerik yüklemenin başka bir yolu da [text](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) ve [streams](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--) ile çalışmaktır. Dosyanın blob depolamanıza yüklendiğini doğrulamak için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)’ni kullanarak hesabınızdaki verileri görüntüleyebilirsiniz.

### <a name="list-the-blobs"></a>Blobları listeleme

*listBlobs* işlevi, bir kapsayıcıdaki blob meta verileri listesini döndürmek için [listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--) yöntemini çağırır. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

*listBlobsSegmented* çağrıldığında, [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest) örnekleri dizisi olarak blob meta verileri döndürülür. Sonuçlar, 5.000’er olarak artan toplu işler (segmentler) halinde döndürülür. Bir kapsayıcıda 5.000’den fazla blob varsa, sonuçlar **continuationToken** için bir değer içerir. Blob kapsayıcısında yer alan sonraki segmentleri listelemek için, ikinci bağımsız değişken olarak devamlılık belirtecini **listBlobSegmented** öğesine geri gönderebilirsiniz.

### <a name="download-a-blob"></a>Blob indirme

*downloadBlob* işlevi, belirtilen mutlak dosya yoluna blobun içeriklerini indirmek için [getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) öğesini kullanır.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
Burada gösterilen uygulama kaynağı değiştirir ve blob içeriklerini dize olarak döndürür. Blobu bir [akış](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) olarak veya doğrudan bir [yerel dosyaya](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) da indirebilirsiniz.

### <a name="delete-a-blob"></a>Blob silme

*deleteBlob* işlevi [deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--) işlevini çağırır. Adından da anlaşılacağı gibi bu işlev, blob önceden silindiyse bir hata döndürmez.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Kapsayıcı silme

Kapsayıcılar blob hizmetinden *deleteContainer* yöntemi çağrılıp kapsayıcı adı geçirilerek silinir.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Kodu çağırma

JavaScript'in *async/await* söz dizimini desteklemek için, tüm çağırma kodu *execute* adlı bir işlev içinde sarmalanır. Daha sonra yürütme çağrılır ve bir promise olarak işlenir.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Aşağıdaki tüm kod `// commands` açıklamasının yerleştirildiği yürütme işlevinin içinde çalıştırılır.

İlk olarak, Blob deposuna karşıya yüklemek için ad ve örnek içerik atamak ve yerel dosyaya işaret etmek üzere ilgili değişkenler bildirilir.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

Depolama hesabındaki kapsayıcıları listelemek için, listContainers işlevi çağrılır ve döndürülen kapsayıcı listesi çıkış penceresine kaydedilir.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

Kapsayıcılar listesi kullanılabilir duruma geldikten sonra, oluşturmak istediğiniz kapsayıcının zaten mevcut olup olmadığını öğrenmek için *findIndex* Dizisi yöntemini kullanabilirsiniz. Kapsayıcı mevcut değilse oluşturulur.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Daha sonra, bir dize ve yerel bir dosya Blob depolamaya yüklenir.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
Blobları listelemek için işlem, kapsayıcıları listeleme ile aynıdır. *listBlobs* çağrısı kapsayıcıdaki blobların bir dizisini döndürür ve bunlar çıkış penceresine kaydedilir.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Bir blobu indirmek için, yanıt yakalanır ve blob değerine erişmek için kullanılır. Yanıttan readableStreamBody bir dizeye dönüştürülür ve çıkış penceresine kaydedilir.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

Son olarak, blob ve kapsayıcı depolama hesabından silinir.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Kaynakları temizleme
Depolama hesabına yazılan tüm veriler kod örneğinin sonunda otomatik olarak silinir. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Bloblarla Node.js uygulamaları geliştirme kaynakları

Blob depolama ile Node.js geliştirmeye yönelik şu ek kaynaklara bakın:

### <a name="binaries-and-source-code"></a>İkili dosyalar ve kaynak kodu

- GitHub’da Azure Depolama için [Node.js istemci kitaplığı kaynak kodunu](https://github.com/Azure/azure-storage-node) görüntüleyin ve yükleyin.

### <a name="client-library-reference-and-samples"></a>İstemci kitaplığı başvurusu ve örnekleri

- Node.js istemci kitaplığı hakkında daha fazla bilgi için bkz. [Node.js API başvurusu](https://docs.microsoft.com/javascript/api/overview/azure/storage).
- Node.js istemci kitaplığı kullanılarak yazılmış [Blob depolama örneklerini](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) araştırın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Node. js kullanarak yerel bir disk ve Azure Blob depolama arasında bir dosya yükleme işleminin nasıl yapılacağı gösterilir. Blob depolamayla çalışma hakkında daha fazla bilgi edinmek için, GitHub deposuyla devam edin.

> [!div class="nextstepaction"]
> [For Node. js ve tarayıcılar için JavaScript Microsoft Azure Depolama SDK 'Sı](https://github.com/Azure/azure-storage-node)
