---
title: 'Hızlı başlangıç: tarayıcıda JavaScript ile v10 arasındaki için Azure Blob depolama'
description: JavaScript ile v10 arasındaki SDK kullanarak blob 'ları bir HTML sayfasında karşıya yüklemeyi, listeleyeceğinizi ve silmeyi öğrenin.
services: storage
author: twooley
ms.custom: mvc, devx-track-js
ms.service: storage
ms.author: twooley
ms.date: 07/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: b3cca61fe272b61f2227e7546b37e4ea37cfd693
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280148"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Hızlı başlangıç: tarayıcıda JavaScript ile v10 arasındaki SDK ile Blobları yönetme

Bu hızlı başlangıçta, tamamen tarayıcıda çalışan JavaScript kodunu kullanarak Blobları yönetmeyi öğreneceksiniz. Blob 'lar, görüntüler, belgeler, akış ortamları ve arşiv verileri gibi büyük miktarlarda metin veya ikili veri içerebilen nesnelerdir. BLOB depolama hesabınıza korumalı erişim sağlamak için gerekli güvenlik önlemlerini kullanacaksınız.

> [!NOTE]
> Bu hızlı başlangıç, Azure Blob depolama istemci kitaplığı 'nın eski bir sürümünü kullanır. En son sürümü kullanmaya başlamak için bkz. [hızlı başlangıç: bir tarayıcıda JavaScript V12 SDK ile Blobları yönetme](quickstart-blobs-javascript-browser.md).

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Depolama hesabı. [Depolama hesabı oluşturun](../common/storage-account-create.md).
- Yerel bir Web sunucusu. Bu makalede temel bir sunucu açmak için [Node.js](https://nodejs.org) kullanılır.
- [Visual Studio Code](https://code.visualstudio.com).
- Tarayıcı hata ayıklaması için, [Microsoft Edge Için Chrome veya hata ayıklayıcı](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge) [için hata ayıklayıcı](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) gibi vs Code uzantısı.

## <a name="setting-up-storage-account-cors-rules"></a>Depolama hesabı CORS kurallarını ayarlama

Web uygulamanızın istemciden bir blob depolamaya erişebilmesi için, hesabınızı, [çıkış noktaları arası kaynak paylaşımı](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)veya CORS 'yi etkinleştirecek şekilde yapılandırmanız gerekir.

Azure portalına geri dönün depolama hesabınızı seçin. Yeni bir CORS kuralı tanımlamak için, **Ayarlar** bölümüne gidin ve **CORS** bağlantısına tıklayın. Ardından, **Ekle** düğmesine tıklayarak **CORS kuralı ekle** penceresini açın. Bu hızlı başlangıçta bir açık CORS kuralı oluşturacaksınız:

![Azure Blob Depolama Hesabı CORS ayarları](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

Aşağıdaki tabloda her bir CORS ayarı açıklanmakta ve kuralı tanımlamak için kullanılan değerler anlatılmaktadır.

|Ayar  |Değer  | Açıklama |
|---------|---------|---------|
| İzin verilen çıkış noktaları | * | Kabul edilebilir çıkış noktaları olarak etki alanları kümesinin virgülle ayrılmış bir listesini kabul eder. Değerin `*` olarak ayarlanması, depolama hesabına tüm etki alanlarının erişmesine izin verir. |
| İzin verilen Yöntemler     | silme, alma, başlık, birleştirme, yayınlama, seçenekler ve yerleştirme | Depolama hesabına göre yürütülmesine izin verilen HTTP fiillerini listeler. Bu hızlı başlangıçta tüm kullanılabilir seçenekleri işaretleyin. |
| İzin verilen üst bilgiler | * | Depolama hesabı tarafından izin verilen istek üst bilgilerinin (ön ekli üst bilgiler dahil) listesini tanımlar. Değerin `*` olarak ayarlanması tüm üst bilgilere erişim izni verir. |
| Kullanıma sunulan üst bilgiler | * | Hesaba göre izin verilen yanıt üst bilgilerini listeler. Değerin `*` olarak ayarlanması hesabın herhangi bir üst bilgiyi göndermesine izin verir.  |
| En fazla yaş (saniye) | 86400 | Tarayıcının denetim öncesi SEÇENEKLER isteğini önbelleğe aldığı en uzun süre. *86400* değeri, önbelleğin bir tam gün boyunca kalmasına izin verir. |

> [!IMPORTANT]
> Üretimde kullandığınız tüm ayarların, güvenli erişim sağlamak için depolama hesabınıza gereken en düşük erişim miktarını kullanıma sunduğundan emin olun. Burada açıklanan CORS ayarları esnek bir güvenlik ilkesini tanımladığı için hızlı başlangıç için uygundur. Ancak bu ayarlar gerçek bir bağlam için önerilmez.

Bundan sonra, Azure Cloud Shell hizmetini kullanarak bir güvenlik belirteci oluşturacaksınız.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Paylaşılan erişim imzası oluşturma

Paylaşılan erişim imzası (SAS), Blob depolama alanına gönderilen isteklerini yetkilendirmek için tarayıcıda çalışan kod tarafından kullanılır. İstemci, SAS kullanarak hesap erişim anahtarı veya bağlantı dizesi kullanmadan depolama kaynaklarına erişimi yetkilendirebilir. SAS hakkında daha fazla bilgi edinmek için bkz. [Paylaşılan erişim imzaları (SAS) kullanma](../common/storage-sas-overview.md).

Azure Cloud Shell aracılığıyla veya Azure portal ya da Azure Depolama Gezgini ile Azure CLı kullanarak bir SAS oluşturabilirsiniz. Aşağıdaki tabloda CLı ile SAS oluşturmak için değer sağlamanız gereken parametreler açıklanmaktadır.

| Parametre      |Açıklama  | Yer tutucu |
|----------------|-------------|-------------|
| *kaç*       | Erişim belirtecinin YYYY-AA-GG biçimindeki sona erme tarihi. Bu hızlı başlangıçta kullanmak için yarının tarihini girin. | *FUTURE_DATE* |
| *hesap-adı* | Depolama hesabı adı. Daha önceki bir adımda ayrılan adı kullanın. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | Depolama hesabı anahtarı. Daha önceki bir adımda ayrılan anahtarı kullanın. | *YOUR_STORAGE_ACCOUNT_KEY* |

JavaScript kodunuzda kullanabileceğiniz bir SAS oluşturmak için, her bir yer tutucu için gerçek değerlerle aşağıdaki CLı komutunu kullanın.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

Her parametreden sonraki değer serisini biraz şifreli bulabilirsiniz. Bu parametre değerleri, kendi ilgili izninin ilk harfinden alınır. Aşağıdaki tabloda değerlerin nereden geldiği açıklanmıştır:

| Parametre        | Değer   | Açıklama  |
|------------------|---------|---------|
| *izinleri*    | racwdl  | Bu SAS *read*, *append*, *create*, *write*, *delete* ve *list* özelliklerine izin verir. |
| *resource-types* | sco     | SAS’den etkilenen kaynaklar *service*, *container* ve *object* kaynaklarıdır. |
| *servislere*       | b       | SAS’den etkilenen hizmet *blob* hizmetidir. |

SAS oluşturuldığına göre, geri dönüş değerini kopyalayın ve yaklaşan bir adımda kullanmak üzere bir yere kaydedin. Azure CLı dışında bir yöntem kullanarak SAS oluşturduysanız, varsa ilk öğeyi kaldırmanız gerekecektir `?` . Bu karakter, daha sonra SAS kullanıldığı bu konunun URL şablonunda zaten sağlanmış olan bir URL ayırıcısıdır.

> [!IMPORTANT]
> Üretimde, her zaman TLS kullanarak SAS belirteçleri geçirin. Ayrıca, SAS belirteçleri sunucu üzerinde oluşturulmalı ve Azure Blob Depolama’ya geri geçirmek için HTML sayfasına gönderilmelidir. Düşünebileceğiniz bir yaklaşım, SAS belirteçleri oluşturmak için bir sunucusuz bir işlev kullanmaktır. Azure Portal, bir JavaScript işlevi ile SAS oluşturma özelliğine sahip işlev şablonları içerir.

## <a name="implement-the-html-page"></a>HTML sayfasını uygulama

Bu bölümde, temel bir Web sayfası oluşturacak ve sayfayı başlatıp hata ayıklamanın VS Code yapılandıracaksınız. Bununla birlikte, başlamadan önce yerel bir Web sunucusunu başlatmak için Node.js kullanmanız ve tarayıcınız istediğinde sayfaya sunmanı gerekir. Ardından, çeşitli BLOB depolama API 'Lerini çağırmak ve sonuçları sayfada göstermek için JavaScript kodu ekleyeceksiniz. Ayrıca, bu çağrıların sonuçlarını VS Code için [Azure Portal](https://portal.azure.com), [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer)ve [Azure Storage uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) 'nda da görebilirsiniz.

### <a name="set-up-the-web-application"></a>Web uygulamasını ayarlama

İlk olarak, *Azure-Bloblar-JavaScript* adlı yeni bir klasör oluşturun ve vs Code açın. Ardından VS Code yeni bir dosya oluşturun, aşağıdaki HTML 'yi ekleyin ve *Azure-Bloblar-JavaScript* klasörüne *index.html* olarak kaydedin.

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>Hata ayıklayıcıyı yapılandırma

VS Code hata ayıklayıcı uzantısını ayarlamak için, **hata ayıkla > yapılandırma Ekle...**' yi seçin, ardından ön koşullar bölümüne yüklediğiniz uzantıya bağlı olarak **Chrome** veya **Edge**' i seçin. Bu eylem dosyada bir *launch.js* oluşturur ve düzenleyicide açar.

Sonra, dosyadaki *launch.js* , `url` değeri gösterildiği gibi içerecek şekilde değiştirin `/index.html` :

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

Bu yapılandırma, hangi tarayıcının başlatılacağı ve hangi URL 'nin yükleneceğini VS Code söyler.

### <a name="launch-the-web-server"></a>Web sunucusunu Başlat

Yerel Node.js Web sunucusunu başlatmak için, VS Code içinde bir konsol penceresi açmak üzere **> Terminal görüntüle** ' yi seçin ve ardından aşağıdaki komutu girin.

```console
npx http-server
```

Bu komut, *http-Server* paketini yükleyecek ve sunucuyu başlatarak, önceki adımda gösterilenler de dahil olmak üzere geçerli klasörü varsayılan URL 'ler aracılığıyla kullanılabilir hale getirir.

### <a name="start-debugging"></a>Hata ayıklamayı Başlat

VS Code hata ayıklayıcı eklenmiş bir tarayıcıda *index.html* 'yi başlatmak Için **Hata Ayıkla > hata ayıklamayı Başlat** ' ı seçin veya vs Code F5 tuşuna basın.

Görüntülenecek kullanıcı arabirimi henüz hiçbir şey yapmaz, ancak gösterilen her işlevi uygulamak için aşağıdaki bölüme JavaScript kodu eklersiniz. Daha sonra, kodunuzda durakladığında kesme noktaları ayarlayabilir ve hata ayıklayıcı ile etkileşim kurabilirsiniz.

*index.html*'de değişiklikler yaptığınızda, tarayıcıdaki değişiklikleri görmek için sayfayı yeniden yüklediğinizden emin olun. VS Code, hata **ayıklamayı yeniden başlatmak > hata ayıklamayı** seçebilir ya da CTRL + SHIFT + F5 tuşlarına basabilirsiniz.

### <a name="add-the-blob-storage-client-library"></a>BLOB depolama istemci kitaplığını ekleme

Blob Storage API 'sine yapılan çağrıları etkinleştirmek için önce [JavaScript-blob istemci kitaplığı Için Azure depolama SDK 'Sını indirin](https://aka.ms/downloadazurestoragejsblob), zip içeriğini ayıklayın ve *azure-storage-blob.js* dosyasını *Azure-Bloblar-JavaScript* klasörüne yerleştirin.

Sonra, yer tutucu yorumunu değiştirerek, kapanış etiketinden sonra *index.html* ' ye aşağıdaki HTML 'yi yapıştırın `</body>` .

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Bu kod, betik dosyasına bir başvuru ekler ve kendi JavaScript kodunuz için bir yer sağlar. Bu hızlı başlangıç için *azure-storage-blob.js* betik dosyası kullanarak vs Code açabilir, içeriğini okuyabilir ve kesme noktaları ayarlayabilirsiniz. Üretimde, ZIP dosyasında da sağlanmış olan daha Compact *azure-storage.blob.min.js* dosyasını kullanmanız gerekir.

[Başvuru belgelerindeki](/javascript/api/%40azure/storage-blob/index)her bir BLOB depolama işlevi hakkında daha fazla bilgi edinebilirsiniz. SDK 'daki işlevlerin bazılarının yalnızca Node.js veya tarayıcıda kullanılabilir olduğunu unutmayın.

*azure-storage-blob.js* kod, `azblob` BLOB depolama API 'Lerine erişmek için JavaScript kodunuzda kullanacağınız adlı genel bir değişkeni dışarı aktarır.

### <a name="add-the-initial-javascript-code"></a>İlk JavaScript kodunu ekleme

Sonra, `<script>` yer tutucu yorumunu değiştirerek, önceki kod bloğunda gösterilen öğeye aşağıdaki kodu yapıştırın.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

Bu kod, aşağıdaki kodun kullanacağı her HTML öğesi için alanlar oluşturur ve `reportStatus` çıktıyı göstermek için bir işlev uygular.

Aşağıdaki bölümlerde, bir önceki bloğundan sonra her bir JavaScript kodu bloğunu ekleyin.

### <a name="add-your-storage-account-info"></a>Depolama hesabı bilgilerinizi ekleyin

Daha sonra, depolama hesabınıza erişmek için kod ekleyin ve yer tutucuları, hesap adınızla ve önceki adımda oluşturduğunuz SAS ile değiştirin.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Bu kod, bir depolama kapsayıcısını oluşturmak ve işlemek için yararlı olan bir [Containerurl](/javascript/api/@azure/storage-blob/ContainerURL) örneği oluşturmak için hesap BILGILERINIZI ve SAS 'yi kullanır.

### <a name="create-and-delete-a-storage-container"></a>Depolama kapsayıcısı oluşturma ve silme

Ardından, ilgili düğmeye bastığınızda depolama kapsayıcısını oluşturmak ve silmek için kod ekleyin.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

Bu kod, [Aborter](/javascript/api/@azure/storage-blob/aborter) örneği kullanmadan kapsayıcı URL 'si [oluşturma](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) ve [silme](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) işlevlerini çağırır. Bu hızlı başlangıçta şeyleri basit tutmak için, bu kod depolama hesabınızın oluşturulduğunu ve etkinleştirildiğini varsayar. Üretim kodunda, zaman aşımı işlevselliği eklemek için bir Aborter örneği kullanın.

### <a name="list-blobs"></a>Blobları listeleme

Ardından, **liste dosyaları** düğmesine bastığınızda depolama kapsayıcısının içeriğini listelemek için kod ekleyin.

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

Bu kod, tüm segmentlerin alındığından emin olmak için bir döngüde [Containerurl. Listblobyataysegment](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) işlevini çağırır. Her segment için, içerdiği blob öğeleri listesi üzerinde döngü yapılır ve **dosyalar** listesini güncelleştirir.

### <a name="upload-blobs"></a>Blobları karşıya yükleme

Sonra, **dosyaları seç ve karşıya yükle** düğmesine bastığınızda dosyaları depolama kapsayıcısına yüklemek için kod ekleyin.

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

Bu kod, **dosyaları seç ve karşıya yükle** düğmesini gizli öğesine bağlar `file-input` . Bu şekilde, düğme `click` olayı dosya girişi `click` olayını tetikler ve dosya seçiciyi görüntüler. Dosyaları seçtikten ve iletişim kutusunu kapattıktan sonra, `input` olay oluşur ve `uploadFiles` işlev çağrılır. Bu işlev, seçtiğiniz her dosya için yalnızca Browser- [Uploadbrowserdatatoblockblob](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) işlevini çağırır. Her çağrı, bir listeye eklenen bir Promise döndürür, böylece dosyalar paralel olarak karşıya yüklenmeye neden olur.

### <a name="delete-blobs"></a>Blob’ları silme

Ardından, **Seçili dosyaları sil** düğmesine bastığınızda depolama kapsayıcısından dosyaları silmek için kod ekleyin.

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

Bu kod, listede seçilen her dosyayı kaldırmak için [Bloburl. Delete](/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) işlevini çağırır. Daha sonra `listFiles` **Dosya** listesinin içeriğini yenilemek için daha önce gösterilen işlevi çağırır.

### <a name="run-and-test-the-web-application"></a>Web uygulamasını çalıştırma ve test etme

Bu noktada, blob Storage 'ın nasıl çalıştığına ilişkin bir fikir almak için sayfayı ve denemeyi başlatabilirsiniz. Herhangi bir hata oluşursa (örneğin, kapsayıcıyı oluşturmadan önce dosyaları listeledikten sonra), **durum** bölmesinde alınan hata iletisi görüntülenir. Ayrıca, JavaScript kodunda, depolama API 'Leri tarafından döndürülen değerleri incelemek için kesme noktaları da ayarlayabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç sırasında oluşturulan kaynakları temizlemek için [Azure Portal](https://portal.azure.com) gidin ve Önkoşullar bölümünde oluşturduğunuz kaynak grubunu silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, tarayıcı tabanlı JavaScript 'ten blob depolamaya erişen basit bir Web sitesi oluşturdunuz. Blob depolamada bir Web sitesinin kendisini nasıl barındırabileceğinizi öğrenmek için aşağıdaki öğreticiye geçin:

> [!div class="nextstepaction"]
> [Blob depolamada statik bir Web sitesi barındırma](./storage-blob-static-website-host.md)