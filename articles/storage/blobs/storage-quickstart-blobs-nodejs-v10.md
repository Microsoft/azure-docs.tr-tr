---
title: 'Hızlı başlangıç: JavaScript için Azure Blob depolama istemci kitaplığı ile v10 arasındaki'
description: JavaScript için Azure Storage istemci kitaplığı ile v10 arasındaki ile Node. js ' de blob ve kapsayıcılar oluşturma, karşıya yükleme ve silme
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/24/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b909da5da0d7352d4086222fe0411ac37668bd92
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825333"
---
# <a name="quickstart-azure-blob-storage-client-library-v10-for-javascript"></a>Hızlı başlangıç: JavaScript için Azure Blob depolama istemci kitaplığı ile v10 arasındaki

Bu hızlı başlangıçta blob yükleme, indirme, listeleme ve silme ile kapsayıcıları yönetme amacıyla Node.js'de [JavaScript için Azure Storage v10 SDK'sını](https://github.com/Azure/azure-sdk-for-js) kullanmayı öğreneceksiniz.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="download-the-sample-application"></a>Örnek uygulamayı indirin:

Bu hızlı başlangıçtaki [örnek uygulama](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git), basit bir Node.js konsol uygulamasıdır. Başlamak için, aşağıdaki komutu kullanarak depoyu makinenize kopyalayın:

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Ardından uygulama klasörüne geçin:

```bash
cd azure-storage-js-v10-quickstart
```

Şimdi klasörü sık kullandığınız kod düzenleme ortamında açın.

## <a name="configure-your-storage-credentials"></a>Depolama kimlik bilgilerini yapılandırma

Uygulamayı çalıştırmadan önce, depolama hesabınız için güvenlik kimlik bilgilerini sağlamanız gerekir. Örnek depo, *.env.example* adlı bir dosya içerir. *.example* uzantısını kaldırarak bu dosyayı yeniden adlandırın. Böylece *.env* adlı bir dosya elde edilir. *.env* dosyasında *AZURE_STORAGE_ACCOUNT_NAME* ve *AZURE_STORAGE_ACCOUNT_ACCESS_KEY* anahtarlarının altına hesap adı ve erişim anahtarı değerlerinizi ekleyin.

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

Uygulamadan çıkış şu örnekle benzerlik gösterecektir:

```bash
Container "demo" is created
Containers:
 - container-one
 - container-two
 - demo
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Bu hızlı başlangıç için yeni bir depolama hesabı kullanıyorsanız, yalnızca "*kapsayıcılar:* " etiketinin altında listelenen *demo* kapsayıcısını görebilirsiniz.

## <a name="understanding-the-code"></a>Kodu anlama

Örnek, Azure Blob depolama ad alanından bir dizi sınıfı ve işlevi içeri aktararak başlar. İçeri aktarılan öğelerin her biri örnekte kullanıldıkları noktada açıklanmıştır.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

Kimlik bilgileri, uygun bağlama göre ortam değişkenlerinden okunur.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

*dotenv* modülü uygulama hata ayıklama için yerel olarak çalıştırılırken ortam değişkenlerini yükler. Değerler *.env* adlı bir dosyada tanımlanır ve geçerli yürütme bağlamına yüklenir. Üretim ortamında, bu değerleri sunucu yapılandırması sağlar ve bu nedenle bu kod yalnızca betik bir “üretim” ortamında *çalıştırılmadığında* çalışır.

Bir sonraki modül bloğu, arabirimine dosya sistemi desteği sunmak için içeri aktarılır.

```javascript
const fs = require('fs');
const path = require('path');
```

Bu modüllerin amacı aşağıdaki gibidir: 

- *fs*, dosya sistemiyle birlikte çalışmak için kullanılan yerel Node.js modülüdür

- *path*, Blob depolamaya dosya yükleme sırasında kullanılan dosya mutlak yolunu belirlemek için gereklidir

Ardından ortam değişkeni değerleri okunur ve sabit değerler halinde ayarlanır.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
Sonraki sabit değer kümesi, yükleme işlemleri sırasında dosya boyutu hesaplama amacının gerçekleştirilmesine yardımcı olur.

```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```

API tarafından yapılan istekler belirli bir sürenin sonunda zaman aşımına uğrayacak şekilde ayarlanabilir. [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-preview) sınıfı isteklerin zaman aşımının yönetilmesinden sorumludur ve arkasından gelen sabit, bu örnekte kullanılan zaman aşımlarını tanımlamak için kullanılır.

```javascript
const ONE_MINUTE = 60 * 1000;
```

### <a name="calling-code"></a>Kodu çağırma

JavaScript'in *async/await* söz dizimini desteklemek için, tüm çağırma kodu *execute* adlı bir işlev içinde sarmalanır. Daha sonra *execute* çağrılır ve bir promise olarak işlenir.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```

Aşağıdaki tüm kod `// commands...` açıklamasının yerleştirildiği yürütme işlevinin içinde çalıştırılır.

İlk olarak, Blob deposuna karşıya yüklemek için ad ve örnek içerik atamak ve yerel dosyaya işaret etmek üzere ilgili değişkenler bildirilir.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

Hesap kimlik bilgileri bir işlem hattı oluşturmak için kullanılır ve bu işlem hattı isteklerin REST API'sine gönderilme şeklini yönetir. İşlem hatları iş parçacığı güvenli bileşenlerdir ve yeniden deneme ilkeleri, günlüğe kaydetme, HTTP yanıtı seri durumdan çıkarma kuralları ve daha fazlası için gerekli mantığı belirtir.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```

Bu kod bloğunda aşağıdaki sınıflar kullanılmıştır:

- [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-preview) sınıfı istek işlem hattına göndermek üzere depolama hesabı kimlik bilgilerinin sarmalanmasından sorumludur.

- [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-preview) sınıfı yeni bir işlem hattı oluşturulmasından sorumludur.

- [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-preview), REST API'sinde kullanılan bir URL modeller. Bu sınıfın örnekleri kapsayıcıları listeleme gibi eylemler gerçekleştirmenizi ve kapsayıcı URL'lerini oluşturmak için bağlam bilgisi sunmanızı sağlar.

*ServiceURL* örneği, depolama hesabınızdaki kapsayıcıları ve blobları yönetmek için [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-preview) ve [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-preview) örnekleriyle birlikte kullanılır.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```

*containerURL* ve *blockBlobURL* değişkenleri, depolama hesabında işlem gerçekleştirmek için örnek boyunca yeniden kullanılmıştır. 

Bu noktada kapsayıcı, depolama hesabında mevcut değildir. *ContainerURL* örneği, işlem gerçekleştirebileceğiniz URL'yi temsil eder. Bu örneği kullanarak kapsayıcı oluşturabilir ve silebilirsiniz. Bu kapsayıcının konumu, şunun gibi bir konuma karşılık gelir:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

*blockBlobURL*, tek bir blobu yönetmek ve bu sayede blob içeriği yüklemek, indirmek ve silmek için kullanılır. Burada gösterilen URL şu konuma benzerdir:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```

Kapsayıcıda olduğu gibi blok blobu da henüz mevcut değildir. *blockBlobURL* değişkeni daha sonra içerik yükleyerek blobu oluşturmak için kullanılır.

### <a name="using-the-aborter-class"></a>Aborter sınıfını kullanma

API tarafından yapılan istekler belirli bir sürenin sonunda zaman aşımına uğrayacak şekilde ayarlanabilir. *Aborter* sınıfı, isteklerin zaman aşımına uğradığını yönetmekten sorumludur. Aşağıdaki kod, bir istek kümesinin yürütülmesi için 30 dakika verildiği bir bağlam oluşturur.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```

Aborters, şu işlevleri sunarak istekler üzerinde denetim sahibi olmanızı sağlar:

- belirli bir istek grubu için verilen süreyi belirleme
- grup içindeki tek bir isteğin ne kadar yürütülmesi gerektiğini belirleme
- istekleri iptal etme
- isteklerinizin tamamının zaman aşımına uğramasını önlemek için *Aborter.none* statik üyesini kullanma

### <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Kapsayıcı oluşturmak için *ContainerURL*'nin *create* metodu kullanılır.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```

Kapsayıcı adı *ContainerURL.fromServiceURL(serviceURL, containerName)* çağrıldığında tanımlandığından kapsayıcıyı oluşturmak için *create* metodunun çağrılması yeterlidir.

### <a name="show-container-names"></a>Kapsayıcı adlarını gösterme

Hesaplarda çok sayıda kapsayıcı depolanabilir. Aşağıdaki kod kapsayıcıları bölümlenmiş bir şekilde listeleyerek çok sayıda kapsayıcı arasında geçiş yapmayı göstermektedir. *showContainerNames* işlevine *ServiceURL* ve *Aborter* örnekleri geçirilmiştir.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```

*showContainerNames* işlevi *listContainersSegment* metodunu kullanarak depolama hesabından kapsayıcı adı gruplarını ister.

```javascript
async function showContainerNames(aborter, serviceURL) {
    let marker = undefined;

    do {
        const listContainersResponse = await serviceURL.listContainersSegment(aborter, marker);
        marker = listContainersResponse.nextMarker;
        for(let container of listContainersResponse.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```

Yanıt döndürüldüğünde *containerItems* yinelenerek ad konsola kaydedilir. 

### <a name="upload-text"></a>Metni karşıya yükleme

Metni bloba yüklemek için *upload* metodunu kullanın.

```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```

Burada metin ve uzunluğu metoda geçirilmiştir.

### <a name="upload-a-local-file"></a>Yerel bir dosyayı karşıya yükleme

Yerel bir dosyayı kapsayıcıya yüklemek için kapsayıcı URL'sine ve dosya yoluna ihtiyacınız vardır.

```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```

*uploadLocalFile* işlevi dosya yolunu ve blok blobu hedefini bağımsız değişken olarak alan *uploadFileToBlockBlob* işlevini çağırır.

```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```

### <a name="upload-a-stream"></a>Bir akışı karşıya yükleme

Akışların karşıya yüklenmesi de desteklenir. Bu örnek bir yerel dosyayı upload metoduna geçirilmek üzere bir akış olarak açar.

```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```

*uploadStream* işlevi akışı depolama kapsayıcısına yüklemek için *uploadStreamToBlockBlob* işlevini çağırır.

```javascript
async function uploadStream(aborter, containerURL, filePath) {
    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```

Karşıya yükleme sırasında *uploadStreamToBlockBlob* işlevi yeniden deneme olasılığına karşı akış verilerini önbelleğe almak için arabellek ayırır. *maxBuffers* değeri en fazla kaç arabellek kullanılacağını belirler. Her arabellek ayrı bir karşıya yükleme isteği oluşturur. Ne kadar çok arabellek kullanılırsa hız da o kadar artar ancak bellek kullanımı da aynı ölçüde artış gösterir. Arabellek sayısı performans sorununun istemci yerine ağ veya diske aktarılacağı kadar yüksek olduğunda yükleme hızı sabitlenir.

### <a name="show-blob-names"></a>Blob adlarını gösterme

Hesaplarda birden fazla kapsayıcı bulunabileceği gibi her bir kapsayıcıda da çok sayıda blob bulunabilir. Bir kapsayıcı içindeki bloblara erişmek için *ContainerURL* sınıfı örneği kullanılabilir.

```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```

*showBlobNames* işlevi kapsayıcıdaki blob gruplarını istemek için *listBlobFlatSegment* işlevini çağırır.

```javascript
async function showBlobNames(aborter, containerURL) {
    let marker = undefined;

    do {
        const listBlobsResponse = await containerURL.listBlobFlatSegment(Aborter.none, marker);
        marker = listBlobsResponse.nextMarker;
        for (const blob of listBlobsResponse.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```

### <a name="download-a-blob"></a>Blob indirme

Blob oluşturulduktan sonra içeriği *download* metodu kullanılarak indirilebilir.

```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = await streamToString(downloadResponse.readableStreamBody);
console.log(`Downloaded blob content: "${downloadedContent}"`);
```

Yanıt bir akış olarak döndürülür. Bu örnekte, akış aşağıdaki *Streamtostring* yardımcı işlevini kullanarak bir dizeye dönüştürülür.

```javascript
// A helper method used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", data => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
}
```

### <a name="delete-a-blob"></a>Blob silme

*BlockBlobURL* örneğinden gelen *delete* metodu, kapsayıcıdaki bir blobu siler.

```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```

### <a name="delete-a-container"></a>Kapsayıcı silme

*ContainerURL* örneğinden gelen *delete* metodu, depolama hesabındaki bir kapsayıcıyı siler.

```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Depolama hesabına yazılan tüm veriler kod örneğinin sonunda otomatik olarak silinir. 

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Node.js kullanarak Azure Blob depolamadaki blobları ve kapsayıcıları yönetme adımları gösterilmektedir. Bu SDK ile çalışma hakkında daha fazla bilgi edinmek için, GitHub deposunu inceleyin.

> [!div class="nextstepaction"]
> [JavaScript deposu için Azure Depolama v10 SDK’sı](https://github.com/Azure/azure-storage-js)
> [JavaScript API’si Başvurusu](https://docs.microsoft.com/javascript/api/overview/azure/storage/client)
