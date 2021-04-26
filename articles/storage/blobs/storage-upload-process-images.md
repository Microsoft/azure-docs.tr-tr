---
title: Azure Depolama ile buluta görüntü verileri yükleme | Microsoft Docs
description: Uygulama verilerini bir depolama hesabına depolamak için Azure Blob depolamayı Web Apps ile birlikte kullanın. Bu öğretici, Azure depolama 'daki görüntüleri depolayan ve görüntüleyen bir Web uygulaması oluşturur.
author: twooley
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: twooley
ms.reviewer: dineshm
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: f751b93a7d298763da5a37a54f400d6fc56a58e3
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277989"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Öğretici: Azure depolama ile buluta görüntü verileri yükleme

Bu öğretici, bir dizinin birinci bölümüdür. Bu öğreticide, bir Web uygulamasını dağıtmayı öğreneceksiniz. Web uygulaması, görüntüleri bir depolama hesabına yüklemek için Azure Blob depolama istemci kitaplığını kullanır. İşiniz bittiğinde, Azure depolama 'daki görüntüleri depolayan ve görüntüleyen bir Web uygulamasına sahip olursunuz.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

![.NET 'teki görüntü yeniden Oluşturucu uygulaması](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

![JavaScript 'te görüntü yeniden Oluşturucu uygulaması](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Serinin birinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]

> * Depolama hesabı oluşturma
> * Kapsayıcı oluşturma ve izinleri ayarlama
> * Erişim anahtarı alma
> * Bir web uygulamasını Azure’a dağıtma
> * Uygulama ayarlarını yapılandırma
> * Web uygulamasıyla etkileşim kurma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için bir Azure aboneliğinizin olması gerekir. Başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLı 'yi yerel olarak yüklemek ve kullanmak için Azure CLı sürüm 2.0.4 veya üstünü çalıştırın. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.  

Aşağıdaki örnek `myResourceGroup` adlı bir kaynak grubu oluşturur.

```azurecli
az group create --name myResourceGroup --location southeastasia
```

```powershell
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Örnek, görüntüleri bir Azure depolama hesabındaki blob kapsayıcısına yükler. Depolama hesabı, Azure Storage veri nesnelerinizi depolamak ve bunlara erişmek için benzersiz ad alanı sağlar. Oluşturduğunuz kaynak grubunda [az storage account create](/cli/azure/storage/account) komutunu kullanarak bir depolama hesabı oluşturun.

> [!IMPORTANT]
> Öğreticinin 2. bölümünde blob depolamayla Azure Event Grid kullanırsınız. Depolama hesabınızı, Event Grid destekleyen bir Azure bölgesinde oluşturduğunuzdan emin olun. Desteklenen bölgelerin listesi için bkz. [bölgeye göre Azure ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Aşağıdaki komutta, yer tutucuyu gördüğünüz BLOB depolama hesabı için kendi genel benzersiz adınızı değiştirin `<blob_storage_account>` .

```azurecli
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

```powershell
$blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia `
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>BLOB depolama kapsayıcıları oluşturma

Uygulama, Blob depolama hesabında iki kapsayıcı kullanır. Kapsayıcılar, klasörlere ve depo bloblarına benzer. *Images* kapsayıcısı, uygulamanın tam çözünürlüklü görüntüleri yüklediği yerdir. Serinin sonraki bölümlerinde bir Azure işlev uygulaması, yeniden boyutlandırılan küçük resimleri *thumbnails* kapsayıcısına yükler.

[az storage account keys list](/cli/azure/storage/account/keys) komutunu kullanarak depolama hesabı anahtarını alın. Ardından, [az Storage Container Create](/cli/azure/storage/container) komutuyla iki kapsayıcı oluşturmak için bu anahtarı kullanın.

*Görüntüler* kapsayıcısının genel erişimi olarak ayarlanır `off` . *Küçük resim* kapsayıcısının genel erişimi olarak ayarlanır `container` . `container`Genel erişim ayarı, Web sayfasını ziyaret eden kullanıcıların küçük resimleri görüntülemesini sağlar.

```bash
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey --public-access container
```

BLOB depolama hesabı adınızı ve anahtarınızı bir yere göz önüne alın. Örnek uygulama, görüntüleri karşıya yüklemek için depolama hesabına bağlanmak üzere bu ayarları kullanır. 

## <a name="create-an-app-service-plan"></a>App Service planı oluşturma

[App Service planı](../../app-service/overview-hosting-plans.md), uygulamanızı barındıran web sunucusu grubunun konumunu, boyutunu ve özelliklerini belirtir.

[az appservice plan create](/cli/azure/appservice/plan) komutu ile bir App Service planı oluşturun.

Aşağıdaki örnekte, **Ücretsiz** fiyatlandırma katmanı kullanılarak `myAppServicePlan` adlı bir App Service planı oluşturulmaktadır:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

```powershell
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

Web uygulaması, GitHub örnek deposundan dağıtılan örnek uygulama kodu için bir barındırma alanı sağlar. [az webapp create](/cli/azure/webapp) komutuyla `myAppServicePlan` App Service planında bir [web uygulaması](../../app-service/overview.md) oluşturun.  

Aşağıdaki komutta, öğesini `<web_app>` benzersiz bir adla değiştirin. Geçerli karakterler: `a-z`, `0-9` ve `-`. `<web_app>`Benzersiz değilse, şu hata iletisini alırsınız: *Verilen ada sahip Web sitesi `<web_app>` zaten var.* Web uygulamasının varsayılan URL'si `https://<web_app>.azurewebsites.net` şeklindedir.  

```azurecli
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

```powershell
$webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>GitHub deposundan örnek uygulamayı dağıtma

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

App Service bir web uygulamasına içerik dağıtmanın birkaç yolunu destekler. Bu öğreticide, web uygulamasını bir [genel GitHub örnek deposundan](https://github.com/Azure-Samples/storage-blob-upload-from-webapp) dağıtırsınız. [az webapp deployment source config](/cli/azure/webapp/deployment/source) komutuyla Git dağıtımını web uygulamasında gerçekleşecek şekilde yapılandırın.

Örnek proje bir [ASP.NET MVC](https://www.asp.net/mvc) uygulaması içerir. Uygulama bir görüntüyü kabul eder, bir depolama hesabına kaydeder ve küçük resim kapsayıcılarından görüntüleri görüntüler. Web uygulaması, Azure depolama hizmetiyle etkileşim kurmak için [Azure. Storage](/dotnet/api/azure.storage), [Azure. Storage. blob 'ları](/dotnet/api/azure.storage.blobs)ve [Azure. Storage. blob. model](/dotnet/api/azure.storage.blobs.models) ad alanlarını kullanır.

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

App Service bir web uygulamasına içerik dağıtmanın birkaç yolunu destekler. Bu öğreticide, web uygulamasını bir [genel GitHub örnek deposundan](https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs) dağıtırsınız. [az webapp deployment source config](/cli/azure/webapp/deployment/source) komutuyla Git dağıtımını web uygulamasında gerçekleşecek şekilde yapılandırın.

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

---

## <a name="configure-web-app-settings"></a>Web uygulaması ayarlarını yapılandırma

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Örnek Web uygulaması, görüntüleri karşıya yüklemek için [.net Için Azure depolama API 'lerini](/dotnet/api/overview/azure/storage) kullanır. Depolama hesabı kimlik bilgileri, Web uygulaması için uygulama ayarları 'nda ayarlanır. Uygulama ayarlarını, [az WebApp config appSettings set](/cli/azure/webapp/config/appsettings) komutuyla dağıtılan uygulamaya ekleyin.

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AzureStorageConfig__AccountName=$blobStorageAccount `
    AzureStorageConfig__ImageContainer=images `
    AzureStorageConfig__ThumbnailContainer=thumbnails `
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Örnek Web uygulaması, görüntüleri karşıya yüklemek için [JavaScript Için Azure Storage istemci kitaplığını](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage) kullanır. Depolama hesabı kimlik bilgileri, Web uygulaması için uygulama ayarları 'nda ayarlanır. Uygulama ayarlarını, [az WebApp config appSettings set](/cli/azure/webapp/config/appsettings) komutuyla dağıtılan uygulamaya ekleyin.

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Web uygulamasını dağıttıktan ve yapılandırdıktan sonra, uygulamanın görüntü karşıya yükleme işlevini test edebilirsiniz.

## <a name="upload-an-image"></a>Bir görüntüyü karşıya yükleme

Web uygulamasını test etmek için, yayımlanan uygulamanızın URL'sine gidin. Web uygulamasının varsayılan URL'si `https://<web_app>.azurewebsites.net` şeklindedir.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Bir dosya belirtmek ve karşıya yüklemek için **Fotoğrafları karşıya yükle** bölgesini seçin ya da bir dosyayı bölgenin üzerine sürükleyin. Görüntü başarıyla karşıya yüklenirse kaybolur. **Oluşturulan küçük resimler** bölümü, bu öğreticide daha sonra Test edilene kadar boş kalır.

![.NET 'e fotoğraf yükleme](media/storage-upload-process-images/figure1.png)

Örnek kodda, `UploadFileToStorage` *storagehelper. cs* dosyasındaki görev, görüntüleri [uploadasync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) yöntemini kullanarak depolama hesabı içindeki *görüntüler* kapsayıcısına yüklemek için kullanılır. Aşağıdaki kod örneği `UploadFileToStorage` görevini içerir.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

Aşağıdaki sınıflar ve yöntemler, yukarıdaki görevde kullanılır:

| Sınıf | Yöntem |
|-------|--------|
| [Kullanılmamışsa](/dotnet/api/system.uri) | [URI Oluşturucusu](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential (dize, dize) Oluşturucusu](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Dosya **Seç ' i seçin ve** ardından **görüntüyü karşıya yükle**' ye tıklayın. **Oluşturulan küçük resimler** bölümü, bu öğreticide daha sonra Test edilene kadar boş kalır.

![Node.js fotoğraf yükleme](media/storage-upload-process-images/upload-app-nodejs.png)

Örnek kodda `post` yolu, görüntüyü blob kapsayıcısına yüklemeden sorumludur. Yol, modülleri karşıya yükleme işlemine yardımcı olması için kullanır:

- [Multer](https://github.com/expressjs/multer) , yol işleyicisi için karşıya yükleme stratejisi uygular.
- [Stream 'e](https://github.com/sindresorhus/into-stream) , arabelleği [UploadStream](/javascript/api/%40azure/storage-blob/blockblobclient#uploadstream-readable--number--number--blockblobuploadstreamoptions-)'in gerektirdiği şekilde bir akışa dönüştürür.

Dosya rotaya gönderilirken dosyanın içeriği, dosya BLOB kapsayıcısına yükleninceye kadar bellekte kalır.

> [!IMPORTANT]
> Büyük dosyaların belleğe yüklenmesi Web uygulamanızın performansı üzerinde olumsuz bir etkiye sahip olabilir. Kullanıcıların büyük dosyaları nakletmesini beklemeniz durumunda, Web sunucusu dosya sisteminde dosyaları hazırlama ve sonra blob depolamaya yükleme zamanlama işlemlerini düşünebilirsiniz. Dosyalar BLOB depolama alanından olduktan sonra bunları sunucu dosya sisteminden kaldırabilirsiniz.

```javascript
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}

const {
  BlobServiceClient,
  StorageSharedKeyCredential,
  newPipeline
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const containerName1 = 'thumbnails';
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName2 = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };

const sharedKeyCredential = new StorageSharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = newPipeline(sharedKeyCredential);

const blobServiceClient = new BlobServiceClient(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, '');
  return `${identifier}-${originalName}`;
};

router.get('/', async (req, res, next) => {

  let viewData;

  try {
    const containerClient = blobServiceClient.getContainerClient(containerName1);
    const listBlobsResponse = await containerClient.listBlobFlatSegment();

    for await (const blob of listBlobsResponse.segment.blobItems) {
      console.log(`Blob: ${blob.name}`);
    }

    viewData = {
      title: 'Home',
      viewName: 'index',
      accountName: process.env.AZURE_STORAGE_ACCOUNT_NAME,
      containerName: containerName1
    };

    if (listBlobsResponse.segment.blobItems.length) {
      viewData.thumbnails = listBlobsResponse.segment.blobItems;
    }
  } catch (err) {
    viewData = {
      title: 'Error',
      viewName: 'error',
      message: 'There was an error contacting the blob storage container.',
      error: err
    };
    res.status(500);
  } finally {
    res.render(viewData.viewName, viewData);
  }
});

router.post('/', uploadStrategy, async (req, res) => {
  const blobName = getBlobName(req.file.originalname);
  const stream = getStream(req.file.buffer);
  const containerClient = blobServiceClient.getContainerClient(containerName2);;
  const blockBlobClient = containerClient.getBlockBlobClient(blobName);

  try {
    await blockBlobClient.uploadStream(stream,
      uploadOptions.bufferSize, uploadOptions.maxBuffers,
      { blobHTTPHeaders: { blobContentType: "image/jpeg" } });
    res.render('success', { message: 'File uploaded to Azure Blob storage.' });
  } catch (err) {
    res.render('error', { message: err.message });
  }
});

module.exports = router;
```

---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Depolama hesabında görüntünün gösterildiğini doğrulayın

[Azure portalında](https://portal.azure.com) oturum açın. Soldaki menüden **Depolama hesapları**’nı ve sonra depolama hesabınızın adını seçin. **Kapsayıcılar**' ı seçin ve ardından **görüntüler** kapsayıcısını seçin.

Kapsayıcıda görüntünün gösterildiğini doğrulayın.

![Görüntüler kapsayıcısının listesini Azure portal](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Küçük resim görüntülemeyi test etme

Küçük resim görüntülemeyi test etmek için **, küçük resim kapsayıcısına bir** görüntü yükleyerek uygulamanın **küçük** resim kapsayıcısını okuyup okuyamadığını kontrol edebilirsiniz.

[Azure portalında](https://portal.azure.com) oturum açın. Soldaki menüden **Depolama hesapları**’nı ve sonra depolama hesabınızın adını seçin. **Kapsayıcılar**' ı seçin ve ardından **küçük resim** kapsayıcısını seçin. **Karşıya Yükle**’yi seçerek **Blobu karşıya yükle** bölmesini açın.

Dosya seçicisine sahip bir dosya seçin ve **karşıya yükle**' yi seçin.

**thumbnails** kapsayıcısına yüklenen görüntünün görünür olduğunu doğrulamak için uygulamanıza geri gidin.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

![Yeni görüntü görüntülenirken .NET görüntü yeniden Oluşturucu uygulaması](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

![Görüntü yeniden Oluşturucu uygulaması yeni görüntüyle Node.js](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Serinin ikinci bölümünde, küçük resim oluşturmayı otomatikleştirerek bu görüntüye ihtiyacınız olmaz. **Küçük resimler** kapsayıcısında, karşıya yüklediğiniz görüntüyü seçin ve **Sil** ' i seçerek görüntüyü kaldırın.

Azure Depolama hesabınızdaki içerikleri önbelleğe almak için Content Delivery Network (CDN) etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [Azure CDN ile bir Azure depolama hesabını tümleştirme](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Sonraki adımlar

Serinin birinci bölümünde, bir Web uygulamasını depolama ile etkileşimde bulunmak üzere nasıl yapılandıracağınızı öğrendiniz.

Bir görüntüyü yeniden boyutlandırmak üzere bir Azure işlevi tetiklemek için Event Grid kullanmayı öğrenmek üzere serinin ikinci bölümüne gidin.

> [!div class="nextstepaction"]
> [Karşıya yüklenen bir görüntüyü yeniden boyutlandırmak üzere bir Azure İşlevi’ni tetiklemek için Event Grid kullanma](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
