---
title: Azure Hızlı Başlangıç - Tarayıcıda HTML ve JavaScript kullanarak nesne depolamada blob oluşturma
description: Bir HTML sayfasında JavaScript kullanarak blobları karşıya yüklemek, listelemek ve silmek için bir BlobService örneğini kullanma hakkında bilgi edinin.
keywords: depolama, javascript, html
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/29/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 3dbd78f9c233515c675cd511924b3d4600b72fbd
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173112"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-upload-list-and-delete-blobs-using-javascripthtml-in-the-browser"></a>Hızlı Başlangıç: Tarayıcıda JavaScript/HTML kullanarak Blobları karşıya yükleme, listeleme ve silme

Bu hızlı başlangıçta, tamamen tarayıcıdan çalıştırılan koddan blobların nasıl yönetileceği gösterilmektedir. Burada kullanılan yaklaşım, blob depolama hesabınıza korumalı erişimi güvence altına almak için gerekli güvenlik önlemlerinin nasıl kullanılacağını göstermektedir. Bu hızlı başlangıcı tamamlamak bir [Azure aboneliğinizin](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) olması gerekir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="setting-up-storage-account-cors-rules"></a>Depolama hesabı CORS kurallarını ayarlama 
Web uygulamanızın istemciden bir blob depolamaya erişebilmesi için hesabın [çıkış noktaları arası kaynak paylaşma](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) veya CORS özelliğini etkinleştirecek şekilde yapılandırılmış olması gerekir. 

Azure portalına geri dönün depolama hesabınızı seçin. Yeni bir CORS kuralı oluşturmak için **Ayarlar** bölümüne geri dönüp **CORS** bağlantısına tıklayın. Ardından, **Ekle** düğmesine tıklayarak **CORS kuralı ekle** penceresini açın. Bu hızlı başlangıçta bir açık CORS kuralı oluşturacaksınız:

![Azure Blob Depolama Hesabı CORS ayarları](media/storage-quickstart-blobs-javascript-client-libraries/azure-blob-storage-cors-settings.png)

Aşağıdaki tabloda her bir CORS ayarı açıklanmakta ve kuralı tanımlamak için kullanılan değerler anlatılmaktadır.

|Ayar  |Değer  | Açıklama |
|---------|---------|---------|
| İzin verilen çıkış noktaları | * | Kabul edilebilir çıkış noktaları olarak etki alanları kümesinin virgülle ayrılmış bir listesini kabul eder. Değerin `*` olarak ayarlanması, depolama hesabına tüm etki alanlarının erişmesine izin verir. |
| İzin verilen fiiller     | silme, alma, başlık, birleştirme, yayınlama, seçenekler ve yerleştirme | Depolama hesabına göre yürütülmesine izin verilen HTTP fiillerini listeler. Bu hızlı başlangıçta tüm kullanılabilir seçenekleri işaretleyin. |
| İzin verilen üst bilgiler | * | Depolama hesabı tarafından izin verilen istek üst bilgilerinin (ön ekli üst bilgiler dahil) listesini tanımlar. Değerin `*` olarak ayarlanması tüm üst bilgilere erişim izni verir. |
| Kullanıma sunulan üst bilgiler | * | Hesaba göre izin verilen yanıt üst bilgilerini listeler. Değerin `*` olarak ayarlanması hesabın herhangi bir üst bilgiyi göndermesine izin verir.  |
| En uzun geçerlilik süresi (saniye) | 86400 | Tarayıcının denetim öncesi SEÇENEKLER isteğini önbelleğe aldığı en uzun süre. *86400* değeri, önbelleğin bir tam gün boyunca kalmasına izin verir. |

> [!IMPORTANT]
> Güvenli erişimi sürdürmek için, üretimde kullandığınız tüm ayarların depolama hesabınız için gereken en az erişim miktarını kullanıma sunduğundan emin olun. Burada açıklanan CORS ayarları esnek bir güvenlik ilkesini tanımladığı için hızlı başlangıç için uygundur. Ancak bu ayarlar gerçek bir bağlam için önerilmez.

Bundan sonra, Azure Cloud Shell hizmetini kullanarak bir güvenlik belirteci oluşturacaksınız.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Paylaşılan Erişim İmzası oluşturma
Paylaşılan erişim imzası (SAS), Blob depolama alanına gönderilen isteklerini yetkilendirmek için tarayıcıda çalışan kod tarafından kullanılır. İstemci, SAS kullanarak hesap erişim anahtarı veya bağlantı dizesi kullanmadan depolama kaynaklarına erişimi yetkilendirebilir. SAS hakkında daha fazla bilgi edinmek için bkz. [Paylaşılan erişim imzaları (SAS) kullanma](../common/storage-sas-overview.md).

Azure Cloud Shell aracılığıyla veya Azure Depolama Gezgini ile Azure CLI kullanarak bir SAS oluşturabilirsiniz. Aşağıdaki tabloda, CLI ile bir SAS oluşturmak için değer sağlamanız gereken parametreler açıklanmıştır.

| Parametre      |Açıklama  | Yer tutucu |
|----------------|-------------|-------------|
| *expiry*       | Erişim belirtecinin YYYY-AA-GG biçimindeki sona erme tarihi. Bu hızlı başlangıçta kullanmak için yarının tarihini girin. | *FUTURE_DATE* |
| *account-name* | Depolama hesabı adı. Daha önceki bir adımda ayrılan adı kullanın. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | Depolama hesabı anahtarı. Daha önceki bir adımda ayrılan anahtarı kullanın. | *YOUR_STORAGE_ACCOUNT_KEY* |

Aşağıdaki betikte, bir JavaScript blob hizmetine geçirebileceğiniz bir SAS oluşturmak için Azure CLI kullanılmıştır.

> [!NOTE]
> En iyi sonuçlar için komutu Azure Cloud Shell’e yapıştırmadan önce parametreler arasındaki fazladan boşlukları kaldırın.

```bash
az storage account generate-sas
                    --permissions racwdl
                    --resource-types sco
                    --services b
                    --expiry FUTURE_DATE
                    --account-name YOUR_STORAGE_ACCOUNT_NAME
                    --account-key YOUR_STORAGE_ACCOUNT_KEY
```
Her parametreden sonraki değer serisini biraz şifreli bulabilirsiniz. Bu parametre değerleri, kendi ilgili izninin ilk harfinden alınır. Aşağıdaki tabloda değerlerin nereden geldiği açıklanmıştır: 

| Parametre        | Value   | Açıklama  |
|------------------|---------|---------|
| *izinleri*    | racwdl  | Bu SAS *read*, *append*, *create*, *write*, *delete* ve *list* özelliklerine izin verir. |
| *resource-types* | sco     | SAS’den etkilenen kaynaklar *service*, *container* ve *object* kaynaklarıdır. |
| *services*       | b       | SAS’den etkilenen hizmet *blob* hizmetidir. |

SAS oluşturulduktan sonra konsolda döndürülen değeri metin düzenleyicinize kopyalayın. Bu değeri gelecek bir adımda kullanacaksınız.

> [!IMPORTANT]
> Üretimde her zaman SSL kullanarak SAS belirteçlerini geçirin. Ayrıca, SAS belirteçleri sunucu üzerinde oluşturulmalı ve Azure Blob Depolama’ya geri geçirmek için HTML sayfasına gönderilmelidir. Düşünebileceğiniz bir yaklaşım, SAS belirteçleri oluşturmak için bir sunucusuz bir işlev kullanmaktır. Azure Portal, bir JavaScript işlevi ile SAS oluşturma özelliğine sahip işlev şablonları içerir.

## <a name="implement-the-html-page"></a>HTML sayfasını uygulama

### <a name="set-up-the-web-application"></a>Web uygulamasını ayarlama
Azure Depolama JavaScript istemci kitaplıkları doğrudan dosya sisteminde çalışmaz ve bir web sunucusundan sunulmalıdır. Bu nedenle, aşağıdaki adımlar Node.js ile basit bir yerel web sunucusunu kullanmaya ilişkin ayrıntılı bilgi vermektedir.

> [!NOTE]
> Bu bölümde, bilgisayarınızda Node.js’nin yüklü olmasını gerektiren bir yerel web sunucusu oluşturma işlemi gösterilmektedir. Node.js yüklemek istemiyorsanız, yerel web sunucusu çalıştırmanın başka bir yolunu kullanabilirsiniz.

İlk olarak, projeniz için yeni bir klasör oluşturun ve *azure-blobs-javascript* olarak adlandırın. Ardından, *azure-blobs-javascript* klasöründe bir komut istemi açın ve aşağıdaki komutu girerek web sunucusu modülünü yüklemek üzere uygulamayı hazırlayın:

```bash
npm init -y
```
*init* çalıştırmak, bir web sunucusu modülü yüklemeye yardımcı olmak için gereken dosyaları ekler. Modülü yüklemek için aşağıdaki komutu girin:

```bash
npm i http-server
```
Ardından, *package.json* dosyasını düzenleyin ve mevcut *scripts* tanımını aşağıdaki kod parçacığı ile değiştirin:

```javascript
"scripts": {
    "start": "http-server"
}
```
Son olarak, komut isteminde `npm start` girerek web sunucusunu başlatın:

```bash
npm start
```

### <a name="get-the-blob-storage-client-library"></a>Blob depolama istemci kitaplığını alma
[JavaScript istemci kitaplıklarını indirin](https://aka.ms/downloadazurestoragejs), zip dosyasının içeriğini ayıklayın ve *bundle* klasöründeki betik dosyalarını *scripts* adlı bir klasöre yerleştirin.

### <a name="add-the-client-script-reference-to-the-page"></a>İstemci betik başvurusunu sayfaya ekleme
*azure-blobs-javascript* klasörünün kökünde bir HTML sayfası oluşturup *index.html* olarak adlandırın. Sayfa oluşturulduktan sonra sayfaya aşağıdaki işaretlemeyi ekleyin.

```html
<!DOCTYPE html>
<html>
    <body>
        <button id="create-button">Create Container</button>

        <input type="file" id="fileinput" />
        <button id="upload-button">Upload</button>

        <button id="list-button">List</button>
        
        <button id="delete-button">Delete</button>
    </body>
    <script src="scripts/azure-storage.blob.min.js" charset="utf-8"></script>
    <script>
        // Blob-related code goes here
    </script>
</html>
```
Bu işaretleme, sayfaya aşağıdakileri ekler:

- bir *scripts/azure-storage.blob.js* başvurusu
- Kapsayıcı oluşturmak, blobları karşıya yüklemek, listelemek ve silmek için kullanılan düğmeler
- bir dosyayı karşıya yüklemek için kullanılan *INPUT* öğesi
- depolamaya özgü kod için bir yer tutucu

### <a name="create-an-instance-of-blobservice"></a>BlobService örneği oluşturma 
[BlobService](https://azure.github.io/azure-storage-node/BlobService.html), Azure Blob Depolama hizmeti için bir arabirim sağlar. Hizmetin bir örneğini oluşturmak için, önceki bir adımda oluşturulan depolama hesabı adını ve SAS’yi sağlamanız gerekir.

```javascript
const account = {
    name: YOUR_STORAGE_ACCOUNT_NAME,
    sas:  YOUR_SAS
};

const blobUri = 'https://' + account.name + '.blob.core.windows.net';
const blobService = AzureStorage.Blob.createBlobServiceWithSas(blobUri, account.sas);
```

### <a name="create-a-blob-container"></a>Blob kapsayıcısı oluşturma
Blob hizmeti oluşturulduktan sonra, karşıya yüklenen bir blobu tutmak üzere yeni bir kapsayıcı oluşturabilirsiniz. [createContainerIfNotExists](https://azure.github.io/azure-storage-node/BlobService.html#createContainerIfNotExists__anchor) yöntemi yeni bir kapsayıcı oluşturur ve kapsayıcı zaten mevcutsa bir hata döndürmez.

```javascript
document.getElementById('create-button').addEventListener('click', () => {

    blobService.createContainerIfNotExists('mycontainer',  (error, container) => {
        if (error) {
            // Handle create container error
        } else {
            console.log(container.name);
        }
    });

});
```

### <a name="upload-a-blob"></a>Blobu karşıya yükleme
Bir HTML formundan blob karşıya yüklemek için, *INPUT* öğesinden seçilen dosyaya bir başvuru alırsınız. Öğenin *türü* *dosya* olarak ayarlandığında, seçilen dosya `files` dizisi aracılığıyla kullanılabilir.

Betikten HTML öğesine başvurabilir ve seçili dosyayı blob hizmetine geçirebilirsiniz.

```javascript
document.getElementById('upload-button').addEventListener('click', () => {

    const file = document.getElementById('fileinput').files[0];

    blobService.createBlockBlobFromBrowserFile('mycontainer', 
                                                file.name, 
                                                file, 
                                                (error, result) => {
                                                    if(error) {
                                                        // Handle blob error
                                                    } else {
                                                        console.log('Upload is successful');
                                                    }
                                                });

});
```

[createBlockBlobFromBrowserFile](https://azure.github.io/azure-storage-node/BlobService.html#createBlockBlobFromBrowserFile__anchor) yöntemi, bir blob kapsayıcısını karşıya yüklemek için doğrudan tarayıcı dosyasını kullanır.

### <a name="list-blobs"></a>Blobları listeleme
Blob kapsayıcısına bir dosya yükledikten sonra [listBlobsSegmented](https://azure.github.io/azure-storage-node/BlobService.html#listBlobsSegmented__anchor) yöntemini kullanarak kapsayıcıdaki blobların listesine erişebilirsiniz.

```javascript
document.getElementById('list-button').addEventListener('click', () => {

    blobService.listBlobsSegmented('mycontainer', null, (error, results) => {
        if (error) {
            // Handle list blobs error
        } else {
            results.entries.forEach(blob => {
                console.log(blob.name);
            });
        }
    });
    
});
```

*listBlobsSegmented* yöntemi, blob’ların koleksiyonunu döndürür. Varsayılan olarak koleksiyon miktarı 5000 blobdur, ancak bu değeri gereksinimlerinize uyacak şekilde ayarlayabilirsiniz. [Devamlılık örneği](https://github.com/Azure/azure-storage-node/blob/master/examples/samples/continuationsample.js#L132), çok sayıda blob ile nasıl çalışılacağını ve istemci kitaplığının sayfalamayı nasıl desteklediğini göstermektedir. 


### <a name="delete-blobs"></a>Blob’ları silme
Karşıya yüklediğiniz blobu, [deleteBlobIfExists](https://azure.github.io/azure-storage-node/BlobService.html#deleteBlobIfExists__anchor) öğesini çağırarak silebilirsiniz.

```javascript
document.getElementById('delete-button').addEventListener('click', () => {

    var blobName = YOUR_BLOB_NAME;
    blobService.deleteBlobIfExists('mycontainer', blobName, (error, result) => {
        if (error) {
            // Handle delete blob error
        } else {
            console.log('Blob deleted successfully');
        }
    });
    
});
```
> [!WARNING]
> Bu kod örneğinin çalışması için *blobName*’e ilişkin bir dize değeri sağlamanız gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu hızlı başlangıç sırasında oluşturulan kaynakları temizlemek için [Azure portalına](https://portal.azure.com) geri dönün ve depolama hesabınızı seçin. Seçildikten sonra, şu hesaba giderek depolama hesabını silebilirsiniz: **> Depolama hesabını silmek Için genel bakış**.

## <a name="next-steps"></a>Sonraki adımlar
Dosya yüklemeleri sırasında blobları ve rapor ilerleme durumunu indirme hakkında bilgi almak için örnekleri araştırın.

> [!div class="nextstepaction"]
> [Blob depolama istemci kitaplıkları](https://github.com/Azure/azure-storage-node/tree/master/browser)
