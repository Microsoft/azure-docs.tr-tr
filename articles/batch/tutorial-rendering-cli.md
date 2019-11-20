---
title: Bulutta bir sahneyi işleme - Azure Batch
description: Öğretici - Batch Renderin Hizmetini ve Azure Komut Satırı Arabirimini kullanarak Autodesk 3ds Max sahnesini Arnold ile işleme
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: tutorial
ms.date: 12/11/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 28914244f7ea84ec133821d4b125cbd3b0378348
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272328"
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>Öğretici: Azure Batch bir sahneyi işleme 

Azure Batch, kullanım başına ödeme temelinde bulut ölçekli işleme özellikleri sağlar. Azure Batch; Autodesk Maya, 3ds Max, Arnold ve V-Ray gibi işleme uygulamalarını destekler. Bu öğreticide, Azure Komut Satırı Arabirimi kullanılarak Batch ile küçük bir sahneyi işleme adımları gösterilir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Azure depolamasına sahne yükleme
> * İşleme için Batch havuzu oluşturma
> * Tek kareli bir sahneyi işleme
> * Havuzu ölçeklendirme ve çok kareli bir sahneyi işleme
> * İşlenmiş çıkışı indirme

Bu öğreticide, ışın izleme işleyicisi [Arnold](https://www.autodesk.com/products/arnold/overview)'ı kullanarak Batch ile bir 3ds Max sahnesini işleyeceksiniz. Batch havuzu, önceden yüklenen grafikler ve kullandığın kadar öde lisansı sağlayan işleme uygulamalar içeren bir Azure Marketi resmi kullanır.

## <a name="prerequisites"></a>Önkoşullar

Batch’teki işleme uygulamalarını kullandığın kadar öde esasıyla kullanmak için bir kullandıkça öde aboneliğine veya diğer Azure satın alma seçeneğine ihtiyacınız vardır. **Para kredi sağlayan ücretsiz bir Azure teklifi kullanıyorsanız, kullandığın kadar öde lisansı desteklenmez.**

Bu öğretici için örnek 3ds Max sahnesi, bir örnek Batch betiği ve JSON yapılandırma dosyalarıyla birlikte [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene)'dadır. 3ds Max sahnesi, [Autodesk 3ds Max örnek dosyalarından](https://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe) alınmıştır. (Autodesk 3ds Max örnek dosyaları, Creative Commons Attribution-NonCommercial-Share Alike lisansı kapsamında sağlanır. Telif Hakkı © Autodesk, Inc.)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.20 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli).

## <a name="create-a-batch-account"></a>Batch hesabı oluşturma

Henüz yapmadıysanız, aboneliğinizde bir kaynak grubu, Batch hesabı ve bağlı depolama hesabı oluşturun. 

[az group create](/cli/azure/group#az-group-create) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus2* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

[az storage account create](/cli/azure/storage/account#az-storage-account-create) komutuyla kaynak grubunuzda Azure Depolama hesabı oluşturun. Bu öğreticide, giriş 3ds Max sahnesini ve işlenen çıkışı depolamak için depolama hesabını kullanırsınız.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```
[az batch account create](/cli/azure/batch/account#az-batch-account-create) komutuyla bir Batch hesabı oluşturun. Aşağıdaki örnek, *myResourceGroup* kaynak grubu içinde *mybatchaccount* adlı bir Batch hesabı oluşturur ve oluşturduğunuz depolama hesabını bağlar.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

İşlem havuzlarını ve işlerini oluşturmak ve yönetmek için, Batch ile kimlik doğrulaması yapmalısınız. [az batch account login](/cli/azure/batch/account#az-batch-account-login) komutuyla hesapta oturum açın. Oturumunuz açıldıktan sonra, `az batch` komutlarınız bu hesabın bağlamını kullanır. Aşağıdaki örnekte, Batch hesabı adı ve anahtarı temelinde paylaşılan anahtar kimlik doğrulaması kullanılır. Batch ayrıca bireysel kullanıcıların ya da katılımsız bir uygulamanın kimlik doğrulamasını yapmak için [Azure Active Directory](batch-aad-auth.md) aracılığıyla kimlik doğrulamayı destekler.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```
## <a name="upload-a-scene-to-storage"></a>Depolamaya sahne yükleme

Giriş sahnesini depolama alanına yüklemek için, önce depolama hesabına erişmeli ve bloblar için bir hedef kapsayıcı oluşturmalısınız. Azure depolama hesabına erişmek için, `AZURE_STORAGE_KEY` ve `AZURE_STORAGE_ACCOUNT` ortam değişkenlerini dışarı aktarın. İlk Bash kabuk komutu, ilk hesap anahtarını almak için [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) komutunu kullanır. Bu ortam değişkenlerini ayarladıktan sonra, depolama komutlarınız bu hesabın bağlamını kullanır.

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

Şimdi, depolama hesabında sahne dosyaları için bir blob kapsayıcısı oluşturun. Aşağıdaki örnekte, genel okuma erişimine izin veren *scenefiles* adlı bir blob kapsayıcısı oluşturmak için [az storage container create](/cli/azure/storage/container#az-storage-container-create) komutu kullanılır.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

`MotionBlur-Dragon-Flying.max` sahnesini [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max)'dan yerel çalışma dizinine indirin. Örneğin:

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

Sahne dosyasını yerel çalışma dizininizden blob kapsayıcısına yükleyin. Aşağıdaki örnekte, birden çok dosyayı karşıya yükleyebilen [az storage blob upload-batch](/cli/azure/storage/blob#az-storage-blob-upload-batch) komutu kullanılır:

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>İşleme havuzu oluşturma

[az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) komutunu kullanarak işleme için bir Batch havuzu oluşturun. Bu örnekte, havuz ayarlarını bir JSON dosyasında belirtirsiniz. Geçerli kabuğunuzun içinde, *mypool.json* adlı bir dosya oluşturun ve aşağıdaki içeriği kopyalayıp yapıştırın. Metnin tamamının doğru kopyalandığından emin olun. (Dosyayı [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json)'dan indirebilirsiniz.)


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.3.2"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```
Batch, adanmış düğümleri ve [düşük öncelikli düğümleri](batch-low-pri-vms.md) destekler ve havuzlarınızda bunlardan birini ya da her ikisini birden kullanabilirsiniz. Adanmış düğümler, havuzunuz için ayrılmıştır. Düşük öncelikli düğümler ise Azure’daki fazlalık VM kapasitesinden indirimli bir fiyat karşılığında sunulur. Azure’da yeterli kapasite yoksa düşük öncelikli düğümler kullanılamaz duruma gelir. 

Belirtilen havuz Batch Rendering hizmetinin yazılımıyla birlikte bir Windows Server görüntüsü çalıştıran tek bir düşük öncelikli düğüm içerir. Bu havuz, 3ds Max ve Arnold ile işlenmek üzere lisanslanmıştır. Sonraki adımlardan birinde, havuzu daha fazla düğüm sayısıyla ölçeklendireceksiniz.

JSON dosyasını `az batch pool create` komutuna geçirerek havuzu oluşturun:

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
``` 
Havuzun hazırlanması birkaç dakika sürer. Havuzun durumunu görmek için [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) komutunu çalıştırın. Aşağıdaki komut havuzun ayırma durumunu alır:

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

Havuzun durumu değişirken iş ve görevleri oluşturmak için aşağıdaki adımlarla devam edin. Ayırma durumu `steady` olduğunda ve düğümler çalıştığında havuz tamamen hazırlanmış olur.  

## <a name="create-a-blob-container-for-output"></a>Çıkış için blob kapsayıcısı oluşturma

Bu öğreticideki örneklerde, işleme işi kapsamındaki her görev bir çıkış dosyası oluşturur. İşi zamanlamadan önce, depolama hesabınızda çıkış dosyalarının hedefi olarak bir blob kapsayıcısı oluşturun. Aşağıdaki örnekte, genel okuma erişimiyle *job-myrenderjob* kapsayıcısını oluşturmak için [az storage container create](/cli/azure/storage/container#az-storage-container-create) komutu kullanılır. 

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

Çıkış dosyalarını kapsayıcıya yazmak için, Batch'in Paylaşılan Erişim İmzası (SAS) belirteci kullanması gerekir. [az storage account generate-sas](/cli/azure/storage/account#az-storage-account-generate-sas) komutuyla belirteci oluşturun. Bu örnekte, hesaptaki herhangi bir blob kapsayıcısına yazmak için bir belirteç oluşturulur ve 15 Kasım 2020 ' de belirtecin süresi dolar:

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2020-11-15
```

Komut tarafından döndürülen belirteci not alın; aşağıdakine benzer olacaktır. Sonraki bir adımda bu belirteci kullanacaksınız.

```
se=2020-11-15&sp=rw&sv=2019-09-24&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## <a name="render-a-single-frame-scene"></a>Tek kareli bir sahneyi işleme

### <a name="create-a-job"></a>Bir iş oluşturma

[az batch job create](/cli/azure/batch/job#az-batch-job-create) komutunu kullanarak havuzda çalıştırılacak bir işleme işi oluşturun. Başlangıçta iş hiçbir görev içermez.

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>Görev oluşturma

[az batch task create](/cli/azure/batch/task#az-batch-task-create) komutunu kullanarak işin içinde bir işleme görevi oluşturun. Bu örnekte, görev ayarlarını bir JSON dosyasında belirtirsiniz. Geçerli kabuğunuzun içinde, *myrendertask.json* adlı bir dosya oluşturun ve aşağıdaki içeriği kopyalayıp yapıştırın. Metnin tamamının doğru kopyalandığından emin olun. (Dosyayı [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json)'dan indirebilirsiniz.)

Görev, *MotionBlur-DragonFlying.max* sahnesinin tek bir karesini işlemek için bir 3ds Max komutu belirtir.

JSON dosyasındaki `blobSource` ve `containerURL` öğelerini, depolama hesabınızın adını ve SAS belirtecinizi içerecek şekilde değiştirin. 

> [!TIP]
> `containerURL` değeriniz SAS belirtecinizle biter ve şuna benzer:
> 
> ```
> https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
> ```

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "httpUrl": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

Aşağıdaki komutu kullanarak görevi işe ekleyin:

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

Batch görevin zamanlamasını yapar ve havuzdaki bir düğüm kullanılabilir duruma geldiği anda görev çalıştırılır.


### <a name="view-task-output"></a>Görev çıktısını görüntüleme

Görevin çalıştırılması birkaç dakika sürer. Görev hakkındaki ayrıntıları görüntülemek için [az batch task show](/cli/azure/batch/task#az-batch-task-show) komutunu kullanın.

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

Görev, işlem düğümünde *dragon0001.jpg* dosyasını oluşturur ve bunu depolama hesabınızdaki *job-myrenderjob* kapsayıcısına yükler. Çıkışı görüntülemek için, [az storage blob download](/cli/azure/storage/blob#az-storage-blob-download) komutunu kullanarak dosyayı depolama alanından yerel bilgisayarınıza indirin.

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

Bilgisayarınızda *dragon.jpg* dosyasını açın. İşlenmiş resim aşağıdakine benzer:

![İşlenmiş ejderha karesi 1](./media/tutorial-rendering-cli/dragon-frame.png) 


## <a name="scale-the-pool"></a>Havuzu ölçeklendirme

Şimdi, birden çok karesi olan daha büyük bir işleme işine hazırlanmak için havuzu değiştirin. Batch, işlem kaynaklarını ölçeklendirmek için bir dizi yol sağlar ve görev değişiklik talep ettiğinde düğümleri ekleyen ve kaldıran [otomatik ölçeklendirme](batch-automatic-scaling.md) de bu yollardan biridir. Bu temel örnek için, [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) komutunu kullanarak havuzdaki düşük öncelikli düğümlerin sayısını *6*'ya çıkarın:

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

Havuzun yeniden boyutlandırılması birkaç dakika sürer. Bu işlem gerçekleştirildiği sırada, mevcut işleme işindeki sonraki görevlerin çalıştırılmasını ayarlayın.

## <a name="render-a-multiframe-scene"></a>Çok kareli bir sahneyi işleme

Tek kare örneğinde olduğu gibi, *myrenderjob* adlı işin içinde işleme görevlerini oluşturmak için [az batch task create](/cli/azure/batch/task#az-batch-task-create) komutunu kullanın. Burada, görev ayarlarını *myrendertask_multi.json* adlı JSON dosyasında belirtin. (Dosyayı [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json)'dan indirebilirsiniz.) Altı görevin her biri, *MotionBlur-DragonFlying.max* adlı 3ds Max sahnesinin tek karesini işlemek için bir Arnold komut satırı belirtir.

Geçerli kabuğunuzda *myrendertask_multi.json* adlı bir dosya oluşturun ve indirilen dosyanın içeriğini kopyalayıp buraya yapıştırın. JSON dosyasındaki `blobSource` ve `containerURL` öğelerini, depolama hesabınızın adını ve SAS belirtecinizi içermesini sağlayacak şekilde değiştirin. Altı görevden her biri için ayarları değiştirdiğinizden emin olun. Dosyayı kaydedin ve görevleri kuyruğa almak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>Görev çıkışını görüntüleme

Görevin çalıştırılması birkaç dakika sürer. Görevlerin durumunu görüntülemek için [az batch task list](/cli/azure/batch/task#az-batch-task-list) komutunu kullanın. Örneğin:

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

Tek tek görevler hakkındaki ayrıntıları görüntülemek için [az batch task show](/cli/azure/batch/task#az-batch-task-show) komutunu kullanın. Örneğin:

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```
 
Görevler, işlem düğümlerinde *dragon0002.jpg* - *dragon0007.jpg* adlı çıkış dosyalarını oluşturur ve bu dosyaları depolama hesabınızdaki *job-myrenderjob* kapsayıcısına yükler. Çıkışı görüntülemek için, [az storage blob download-batch](/cli/azure/storage/blob) komutunu kullanarak dosyaları yerel bilgisayarınızdaki bir klasöre indirin. Örneğin:

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

Bilgisayarınızda dosyalardan birini açın. İşlenmiş 6. kare aşağıdakine benzer:

![İşlenmiş ejderha karesi 6](./media/tutorial-rendering-cli/dragon-frame6.png) 


## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az group delete](/cli/azure/group#az-group-delete) komutunu kullanarak kaynak grubunu, Batch hesabını, havuzları ve tüm ilgili kaynakları kaldırabilirsiniz. Kaynakları aşağıda gösterildiği gibi silin:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunlar hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Sahneleri Azure depolamaya yükleme
> * İşleme için Batch havuzu oluşturma
> * Arnold ile tek kareli bir sahneyi işleme
> * Havuzu ölçeklendirme ve çok kareli bir sahneyi işleme
> * İşlenmiş çıkışı indirme

Bulut ölçekli işleme hakkında daha fazla bilgi edinmek için, Batch Rendering hizmetinin seçeneklerine bakın. 

> [!div class="nextstepaction"]
> [Toplu İşleme hizmeti](batch-rendering-service.md)
