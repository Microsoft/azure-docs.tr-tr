---
title: 'Öğretici: karşıya yüklenen görüntüleri yeniden boyutlandırmayı otomatikleştirmek için Azure Event Grid kullanma'
description: "Öğretici: Azure Event Grid Azure Storage 'daki blob karşıya yüklemelere tetiklenebilir. Bu hizmeti kullanarak, Azure Depolama’ya yüklenmiş görüntü dosyalarını, yeniden boyutlandırma ve diğer iyileştirmeler için Azure İşlevleri gibi diğer hizmetlere gönderebilirsiniz."
ms.topic: tutorial
ms.date: 07/07/2020
ms.openlocfilehash: ca231fc65162fe38f4dcb8b8d5677ef42c7807bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550530"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Öğretici: Event Grid kullanarak karşıya yüklenen görüntüleri yeniden boyutlandırmayı otomatikleştirme

[Azure Event Grid](overview.md) , bulut için bir olay hizmetidir. Event Grid, Azure hizmetleri veya üçüncü taraf kaynaklar tarafından başlatılan olaylara abonelikler oluşturmanızı sağlar.  

Bu öğretici, Depolama öğreticileri serisinin ikinci bölümüdür. [Önceki Depolama öğreticisine][previous-tutorial], Azure Event Grid ve Azure İşlevleri’ni kullanarak sunucusuz otomatik küçük resim oluşturma işlemini ekler. Event Grid, [Azure İşlevleri](../azure-functions/functions-overview.md)’nin [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md) olaylarına yanıt vermesini ve karşıya yüklenen görüntülerin küçük resimlerini oluşturmasını sağlar. Blob depolama oluşturma olayına karşı bir olay aboneliği oluşturulur. Belirli bir Blob depolama kapsayıcısına blob eklendiğinde bir işlev uç noktası çağrılır. Event Grid’den işlev bağlamaya geçirilen veriler, bloba erişmek ve küçük resim görüntüsünü oluşturmak için kullanılır.

Var olan bir görüntü yükleme uygulamasına yeniden boyutlandırma işlevini eklemek için Azure CLI ve Azure portalını kullanabilirsiniz.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

![Yayınlanan bir Web uygulamasını net V12 SDK için bir tarayıcıda gösteren ekran görüntüsü \. .](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js ILE V10 ARASıNDAKI SDK](#tab/nodejsv10)

![Yayınlanan bir Web uygulamasını net ile v10 arasındaki SDK için bir tarayıcıda gösteren ekran görüntüsü \. .](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure Depolama hesabı oluşturma
> * Azure İşlevleri’ni kullanarak sunucusuz kod dağıtma
> * Event Grid’de bir Blob depolama olayı aboneliği oluşturma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğreticiyi tamamlamak için:

Önceki Blob depolama öğreticisini tamamlamış olmanız gerekir: [Azure Storage ile görüntü verilerini buluta yükleme][previous-tutorial].

Bir [Azure aboneliğine](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)ihtiyacınız vardır. Bu öğretici **ücretsiz** abonelikle birlikte çalışmaz. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.14 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

Cloud Shell kullanmıyorsanız önce `az login` kullanarak oturum açmanız gerekir.

Event Grid kaynak sağlayıcısını önceden aboneliğinize kaydetmediyseniz burada kaydettiğinizden emin olun.

```bash
az provider register --namespace Microsoft.EventGrid
```

```powershell
az provider register --namespace Microsoft.EventGrid
```

## <a name="create-an-azure-storage-account"></a>Azure Depolama hesabı oluşturma

Azure İşlevleri, genel bir depolama hesabı gerektirir. Önceki öğreticide oluşturduğunuz BLOB depolama hesabına ek olarak, [az Storage Account Create](/cli/azure/storage/account) komutunu kullanarak kaynak grubunda ayrı bir genel depolama hesabı oluşturun. Depolama hesabı adları 3 ile 24 karakter arasında olmalı ve yalnızca sayıyla küçük harf içermelidir.

1. Bir değişkeni, önceki öğreticide oluşturduğunuz kaynak grubunun adını tutacak şekilde ayarlayın.

    ```bash
    resourceGroupName="myResourceGroup"
    ```

    ```powershell
    $resourceGroupName="myResourceGroup"
    ```

1. Oluşturulacak kaynakların konumunu tutacak bir değişken ayarlayın. 

    ```bash
    location="eastus"
    ```

    ```powershell
    $location="eastus"
    ```

1. Azure Işlevlerinin gerektirdiği yeni depolama hesabının adı için bir değişken ayarlayın.

    ```bash
    functionstorage="<name of the storage account to be used by the function>"
    ```

    ```powershell
    $functionstorage="<name of the storage account to be used by the function>"
    ```

1. Azure işlevi için depolama hesabı oluşturun.

    ```bash
    az storage account create --name $functionstorage --location $location \
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

    ```powershell
    az storage account create --name $functionstorage --location $location `
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

## <a name="create-a-function-app"></a>İşlev uygulaması oluşturma  

İşlevinizin yürütülmesini barındıran bir işlev uygulamasına sahip olmanız gerekir. İşlev uygulaması, işlev kodunuzun sunucusuz yürütülmesine yönelik bir ortam sağlar. [az functionapp create](/cli/azure/functionapp) komutunu kullanarak bir işlev uygulaması oluşturun.

Aşağıdaki komutta kendi benzersiz işlev uygulamanızın adını sağlayın. İşlev uygulaması adı, işlev uygulamasının varsayılan DNS etki alanı olarak kullanılacağı için adın Azure’daki tüm uygulamalarda benzersiz olması gerekir.

1. Oluşturulacak işlev uygulaması için bir ad belirtin.

    ```bash
    functionapp="<name of the function app>"
    ```

    ```powershell
    $functionapp="<name of the function app>"
    ```

1. Azure işlevini oluşturun.

    ```bash
    az functionapp create --name $functionapp --storage-account $functionstorage \
      --resource-group $resourceGroupName --consumption-plan-location $location \
      --functions-version 2
    ```

    ```powershell
    az functionapp create --name $functionapp --storage-account $functionstorage `
      --resource-group $resourceGroupName --consumption-plan-location $location `
      --functions-version 2
    ```

Şimdi, işlev uygulamasını [önceki öğreticide][previous-tutorial]oluşturduğunuz BLOB depolama hesabına bağlanacak şekilde yapılandırın.

## <a name="configure-the-function-app"></a>İşlev uygulamasını yapılandırma

İşlevin, [az functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) komutu kullanılarak işlev uygulamasının uygulama ayarlarına eklenen BLOB depolama hesabı için kimlik bilgileri gerekir.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

```bash
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

```powershell
$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails `
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[Node.js ILE V10 ARASıNDAKI SDK](#tab/nodejsv10)

```bash
blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
  -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName `
  -n $blobStorageAccount --query [0].value --output tsv)

$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails `
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey `
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

`FUNCTIONS_EXTENSION_VERSION=~2` ayarı işlev uygulamasının Azure İşlevleri çalışma zamanının 2.x sürümünde çalışmasını sağlar.

Bu işlev uygulamasına bir işlev kodu projesi dağıtabilirsiniz.

## <a name="deploy-the-function-code"></a>İşlev kodunu dağıtma 

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

Örnek C# Resize işlevi [GitHub](https://github.com/Azure-Samples/function-image-upload-resize)' da kullanılabilir. [Az functionapp Deployment Source config](/cli/azure/functionapp/deployment/source) komutunu kullanarak bu kod projesini işlev uygulamasına dağıtın.

```bash
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

```powershell
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[Node.js ILE V10 ARASıNDAKI SDK](#tab/nodejsv10)

Örnek Node.js yeniden boyutlandırma işlevi [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10) üzerinde mevcuttur. [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) komutunu kullanarak bu İşlevler kod projesini işlev uygulamasına dağıtın.

```bash
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

```powershell
az functionapp deployment source config --name $functionapp `
  --resource-group $resourceGroupName --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

---

Görüntüyü yeniden boyutlandırma işlevi, Event Grid hizmetinden gönderilen HTTP istekleriyle tetiklenir. Bir olay aboneliği oluşturarak Event Grid'e işlevinizin URL'sinde bu bildirimleri almak istediğinizi bildirirsiniz. Bu öğreticide, blob tarafından oluşturulan olaylara abone oluyorsunuz.

Event Grid bildiriminden işleve geçirilen veriler, blobun URL'sini içerir. Ardından karşıya yüklenen görüntüyü Blob depolamadan almak için bu URL giriş bağlamasına geçirilir. İşlev bir küçük resim görüntüsü oluşturur ve elde edilen akışı Blob depolama içinde ayrı bir kapsayıcıya yazar.

Bu proje, tetikleyici türü olarak `EventGridTrigger` kullanır. Genel HTTP tetikleyicileri yerine Event Grid tetikleyicisinin kullanılması önerilir. Event Grid, Event Grid İşlevi tetikleyicilerini otomatik olarak doğrular. Genel HTTP tetikleyicileri ile [doğrulama yanıtını](security-authentication.md) uygulamanız gerekir.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

Bu işlev hakkında daha fazla bilgi edinmek için bkz. [function.json ve run.csx dosyaları](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v10-sdk"></a>[Node.js ILE V10 ARASıNDAKI SDK](#tab/nodejsv10)

Bu işlev hakkında daha fazla bilgi edinmek için, [function.jsve index.js dosyalarına](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail)bakın.

---

İşlev proje kodu, doğrudan ortak örnek depodan dağıtılır. Azure İşlevleri’ne ilişkin dağıtım seçenekleri hakkında daha fazla bilgi için bkz. [Azure İşlevleri için sürekli dağıtım](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Olay aboneliği oluşturma

Olay aboneliği, belirli bir uç noktaya gönderilmesini istediğiniz, sağlayıcı tarafından oluşturulmuş olayları gösterir. Bu örnekte uç nokta, işleviniz tarafından kullanıma sunulur. Azure Portal'da işlevinize bildirimler gönderen bir olay aboneliği oluşturmak için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com), sayfanın üst kısmında arama yapın ve `Function App` Yeni oluşturduğunuz işlev uygulamasını seçip seçin. **İşlevler** ' i seçin ve **küçük resim** işlevini seçin.

    :::image type="content" source="media/resize-images-on-storage-blob-upload-event/choose-thumbnail-function.png" alt-text="Portalda küçük resim işlevini seçin":::

1.  **Tümleştirme** Seç ' i seçin ve **Event Grid tetikleyiciyi** seçin ve **Event Grid aboneliği oluştur**' u seçin.

    :::image type="content" source="./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png" alt-text="Azure portal Event Grid abonelik ekleme sayfasına gidin" :::

1. Tabloda belirtilen olay aboneliği ayarlarını kullanın.
    
    ![Azure portalında işlevden olay aboneliği oluşturma](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Ayar      | Önerilen değer  | Açıklama                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **Ad** | imageresizersub | Yeni olay aboneliğinizi tanımlayan ad. |
    | **Konu türü** | Depolama hesapları | Depolama hesabı olay sağlayıcısını seçin. |
    | **Abonelik** | Azure aboneliğiniz | Varsayılan olarak, geçerli Azure aboneliğiniz seçili durumdadır. |
    | **Kaynak grubu** | myResourceGroup | **Var olanı kullan**’ı seçin ve bu öğreticide kullandığınız kaynak grubunu belirleyin. |
    | **Kaynak** | Blob depolama hesabınız | Oluşturduğunuz Blob depolama hesabını seçin. |
    | **Sistem konu adı** | ımatoragesyıstopic | Sistem konusu için bir ad belirtin. Sistem konuları hakkında bilgi edinmek için bkz. [sistem konularına genel bakış](system-topics.md). |    
    | **Olay türleri** | Oluşturulan blob | **Oluşturulan blob** dışındaki tüm türlerin işaretini kaldırın. Yalnızca `Microsoft.Storage.BlobCreated` türündeki olaylar işleve geçirilir. |
    | **Uç nokta türü** | otomatik oluşturulmuş | **Azure işlevi** olarak önceden tanımlanmış. |
    | **Uç Nokta** | otomatik oluşturulmuş | İşlevin adı. Bu durumda, bunun **küçük resmi**. |

1. **Filtreler** sekmesine geçin ve aşağıdaki işlemleri yapın:
    1. **Konu filtrelemeyi etkinleştir** seçeneğini belirleyin.
    1. **Konu için ile başlıyorsa**, şu değeri girin: **/Blobservices/default/containers/images/**.

        ![Olay aboneliği için filtre belirtin](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

1. Olay aboneliğini eklemek için **Oluştur** ' u seçin. Bu, `Thumbnail` kapsayıcıya bir blob eklendiğinde işlevi tetikleyen bir olay aboneliği oluşturur `images` . İşlev görüntüleri yeniden boyutlandırır ve `thumbnails` kapsayıcıya ekler.

Arka uç hizmetleri yapılandırıldıktan sonra, görüntü yeniden boyutlandırma işlevini örnek web uygulamasında test edin.

## <a name="test-the-sample-app"></a>Örnek uygulamayı test etme

Web uygulamasında görüntü yeniden boyutlandırmayı test etmek için, yayımlanan uygulamanızın URL'sine gidin. Web uygulamasının varsayılan URL'si `https://<web_app>.azurewebsites.net` şeklindedir.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

**Karşıya fotoğraf yükle** bölgesine tıklayarak bir dosyayı seçip karşıya yükleyin. Ayrıca bu bölgeye fotoğraf sürükleyebilirsiniz.

Karşıya yüklenen görüntü kaybolduktan sonra, **oluşturulan küçük resimlerde** , karşıya yüklenen görüntünün bir kopyasının görüntülendiğini unutmayın. Bu görüntü, işlev tarafından yeniden boyutlandırılmış, *thumbnails* kapsayıcısına eklenmiş ve web istemcisi tarafından indirilmiştir.

![NET V12 SDK için bir tarayıcıda "ImageResizer" başlıklı yayımlanmış bir Web uygulamasını gösteren ekran görüntüsü \. .](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js ILE V10 ARASıNDAKI SDK](#tab/nodejsv10)

Dosyayı seçmek için **Dosya Seç** ' e tıklayın ve ardından **görüntüyü karşıya yükle**' ye tıklayın. Karşıya yükleme başarılı olduğunda, tarayıcı başarı sayfasına gider. Giriş sayfasına geri dönmek için bağlantıya tıklayın. Yüklenen görüntünün bir kopyası **oluşturulan küçük resimler** alanında görüntülenir. (Görüntü ilk başta görünmezse, sayfayı yeniden yüklemeyi deneyin.) Bu görüntü, işlev tarafından yeniden boyutlandırılmış, *küçük resim* kapsayıcısına eklenmiş ve Web istemcisi tarafından indirilmişti.

![Tarayıcıda yayınlanan web uygulaması](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Genel bir Azure Depolama hesabı oluşturma
> * Azure İşlevleri’ni kullanarak sunucusuz kod dağıtma
> * Event Grid’de bir Blob depolama olayı aboneliği oluşturma

Depolama hesabına erişimin güvenliğini sağlama hakkında bilgi almak için Depolama öğreticisi serisinin üçüncü bölümüne geçin.

> [!div class="nextstepaction"]
> [Buluttaki bir uygulama verilerine güvenli erişim](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Event Grid hakkında daha fazla bilgi için bkz. [Azure Event Grid’e giriş](overview.md).
+ Azure İşlevleri’ni ön plana çıkaran başka bir öğretici denemek için bkz. [Azure Logic Apps ile tümleşen işlev oluşturma](../azure-functions/functions-twitter-email.md).

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
