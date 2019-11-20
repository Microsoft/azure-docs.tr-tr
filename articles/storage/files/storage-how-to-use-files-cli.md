---
title: Azure CLI kullanarak Azure dosya paylaşımlarını yönetme için hızlı başlangıç
description: Bu hızlı başlangıcı Azure dosyalarını yönetmek için Azure CLI kullanma hakkında bilgi edinmek için kullanın.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 93baf275e93c28283836a92c71eb9b24151392fc
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699588"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Hızlı Başlangıç: Azure CLı kullanarak Azure dosya paylaşımları oluşturma ve yönetme
Bu kılavuzda, Azure CLI kullanarak [Azure dosya paylaşımları](storage-files-introduction.md) ile çalışmanın temel kuralları gösterilmektedir. Azure dosya paylaşımları diğer dosya paylaşımları gibidir, ancak bulutta depolanır ve Azure platformu tarafından desteklenir. Azure dosya paylaşımları endüstri standardı SMB protokolünü destekler ve birden çok makine, uygulama ve örnek arasında dosya paylaşmayı olanaklı kılar. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI’yı yerel olarak yükleyip kullanmaya karar verirseniz, bu makaledeki adımlar için Azure CLI 2.0.4 veya sonraki bir sürümü çalıştırıyor olmanız gerekir. Azure CLI sürümünüzü bulmak için **az --version** komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI 2.0 yükleme](/cli/azure/install-azure-cli). 

Varsayılan olarak, Azure CLI komutları JavaScript Nesne Gösterimi (JSON) döndürür. JSON, REST API'lerinden ileti gönderip almanın standart yoludur. JSON yanıtlarıyla çalışmayı kolaylaştırmak için, bu kılavuzdaki bazı örneklerde Azure CLI komutları üzerinde *query* parametresi kullanılır. Bu parametre, JSON ayrıştırmak için [JMESPath sorgu dilini](http://jmespath.org/) kullanır. JMESPath sorgu dilini takip ederek Azure CLI komutlarının sonuçlarını kullanma hakkında daha fazla bilgi almak için bkz. [JMESPath öğreticisi](http://jmespath.org/tutorial.html).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma
Azure CLI'yı yerel olarak kullanıyorsanız, bir komut istemi açın ve henüz yapmadıysanız Azure'da oturum açın.

```bash 
az login
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Henüz bir Azure kaynak grubunuz yoksa, [az group create](/cli/azure/group) komutunu kullanarak bir tane oluşturabilirsiniz. 

Aşağıdaki örnek *EastUS* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma
Depolama hesabı, Azure dosya paylaşımlarını veya bloblar veya sorgular gibi diğer depolama kaynaklarını dağıtabileceğiniz, paylaşılan bir depolama havuzudur. Bir depolama hesabında sınırsız sayıda dosya paylaşımı olabilir. Bir paylaşım, depolama hesabının kapasite limitlerine kadar sınırsız sayıda dosyayı depolayabilir.

Aşağıdaki örnekte, [az storage account create](/cli/azure/storage/account) komutu kullanılarak *mystorageaccount\<random number\>* adlı bir depolama hesabı oluşturulur ve bu depolama hesabının adı `$STORAGEACCT` değişkenine yerleştirilir. Depolama hesabı adları benzersiz olmalıdır; bu nedenle "mystorageacct" öğesini benzersiz bir adla değiştirdiğinizden emin olun.

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Depolama hesabı anahtarını alma
Depolama hesabı anahtarları, depolama hesabındaki kaynaklara erişimi denetler. Bu anahtarlar, depolama hesabını oluşturduğunuzda otomatik olarak oluşturulur. [az storage account keys list](/cli/azure/storage/account/keys) komutunu kullanarak depolama hesabınızın depolama hesabı anahtarlarını alabilirsiniz: 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma
Artık ilk Azure dosya paylaşımınızı oluşturabilirsiniz. Dosya paylaşımlarını oluşturmak için [az storage share create](/cli/azure/storage/share) komutunu kullanın. Bu örnekte *myshare* adlı bir Azure dosya paylaşımı oluşturulur: 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

Paylaşım adları yalnızca küçük harf, sayı ve tek kısa çizgi içerebilir (ancak kısa çizgiyle başlayamaz). Dosya paylaşımlarının ve dosyaların adlandırılması hakkında tüm ayrıntılara ulaşmak için bkz. [Paylaşım, dizin, dosya ve meta verileri adlandırma ve bunlara başvuruda bulunma](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Azure dosya paylaşımınızı kullanma
Azure Dosyaları, Azure dosya paylaşımınızdaki dosya ve klasörler ile çalışmak için iki yöntem sunar: sektör standardı [Sunucu İleti Bloğu (SMB) protokolü](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) ve [Dosya REST protokolü](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Bir dosya paylaşımını SMB ile bağlayabilmeniz için işletim sisteminize göre aşağıdaki belgeye bakın:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Dosya REST protokolü ile bir Azure dosya paylaşımını kullanma 
Dosya REST protokolü doğrudan ile doğrudan çalışabilir (el ile REST HTTP çağrıları), ancak dosya REST protokolünü kullanmanın en yaygın yolu Azure CLı, [Azure PowerShell modülünü](storage-how-to-use-files-powershell.md)veya bir Azure depolama SDK 'sını, hepsi de seçtiğiniz komut dosyası/programlama dilinde Dosya REST Protokolü etrafında iyi bir sarmalayıcı.  

Kullanabilmeyi umdukları mevcut uygulama ve araçlarını kullanmalarına izin vereceği için Azure Dosyaları kullanıcılarının çoğunluğunun Azure dosya paylaşımları ile SMP protokolü üzerinden çalışmasını bekliyoruz, ancak SMB yerine Dosya REST API'si kullanmanın aşağıdaki gibi bazı avantajları bulunmaktadır:

- Dosya paylaşımınıza (SMB üzerinden dosya paylaşımı bağlayamayan) Azure Bash Cloud Shell'den göz atıyorsanız.
- 445 numaralı bağlantı noktasına sahip olmayan şirket içi istemciler gibi bir SMB paylaşımının takılamaz bir istemciden bir betiği veya uygulamayı yürütmeniz gerekir.
- [Azure İşlevleri](../../azure-functions/functions-overview.md) gibi sunucusuz kaynaklardan yararlanıyorsanız. 

Aşağıdaki örneklerde Azure CLı kullanarak Azure dosya paylaşımınızı Dosya REST protokolüyle nasıl işleyebileceğiniz gösterilmektedir. 

### <a name="create-a-directory"></a>Dizin oluşturma
Azure dosya paylaşımınızın kökünde *myDirectory* adlı yeni bir dizin oluşturmak için [`az storage directory create`](/cli/azure/storage/directory) komutunu kullanın:

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Karşıya dosya yükle
[`az storage file upload`](/cli/azure/storage/file) komutunu kullanarak bir dosyayı karşıya yükleme işlemini göstermek için öncelikle Cloud Shell karalama sürücüsünde karşıya yüklenecek bir dosya oluşturun. Aşağıdaki örnekte dosyayı oluşturup karşıya yüklersiniz:

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Azure CLI'yi yerel olarak çalıştırıyorsanız, `~/clouddrive` değerini makinenizde var olan bir yolla değiştirin.

Dosyayı karşıya yükledikten sonra, [`az storage file list`](/cli/azure/storage/file) komutunu kullanarak dosyanın Azure dosya paylaşımınıza yüklendiğinden emin olabilirsiniz:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Dosya indir
Cloud Shell karalama sürücünüze yüklediğiniz dosyanın bir kopyasını indirmek için [`az storage file download`](/cli/azure/storage/file) komutunu kullanabilirsiniz:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Dosyaları kopyalama
Yaygın görevlerden biri dosyaları bir dosya paylaşımından diğerine veya Azure Blob depolama kapsayıcısına/kapsayıcısından kopyalamaktır. Bu işlevselliği göstermek için yeni bir paylaşım oluşturun. Bu yeni paylaşıma yüklediğiniz dosyayı [az storage file copy](/cli/azure/storage/file/copy) komutunu kullanarak kopyalayın: 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Şimdi, yeni paylaşımdaki dosyaları listelerseniz kopyalanan dosyanızı görmeniz gerekir:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

`az storage file copy start` komutu Azure dosya paylaşımları ile Azure Blob depolama kapsayıcıları arasında dosya taşıma işlemleri için kullanışlı olsa da, daha büyük taşıma işlemlerinde AzCopy kullanmanız önerilir. (Taşınan dosyaların sayısı veya boyutu bakımından daha büyük.) [Linux için AzCopy](../common/storage-use-azcopy-linux.md) ve [Windows için AzCopy](../common/storage-use-azcopy.md) hakkında daha fazla bilgi edinin. AzCopy yerel olarak yüklü olmalıdır. AzCopy, Cloud Shell’de kullanılamaz. 

## <a name="create-and-manage-share-snapshots"></a>Paylaşım anlık görüntülerini oluşturma ve yönetme
Azure dosya paylaşımıyla yerine getirebileceğiniz bir diğer yararlı görev ise paylaşım anlık görüntüleri oluşturmaktır. Anlık görüntü, Azure dosya paylaşımının zamanın bir noktasındaki kopyasını saklar. Paylaşım anlık görüntüleri, zaten tanıyor olabileceğiniz bazı işletim sistemi teknolojilerine benzerdir:

- Linux sistemleri için [Mantıksal Birim Yöneticisi (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) anlık görüntüleri
- macOS için [Apple Dosya Sistemi (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) anlık görüntüleri
- NTFS ve ReFS gibi Windows dosya sistemleri için [Birim Gölge Kopyası Hizmeti (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal). [`az storage share snapshot`](/cli/azure/storage/share) komutunu kullanarak bir paylaşım anlık görüntüsü oluşturabilirsiniz:

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Paylaşım anlık görüntüsünün içeriğine göz atma
`$SNAPSHOT` değişkeninde yakaladığınız paylaşım anlık görüntüsünün zaman damgasını `az storage file list` komutuna geçirerek, paylaşım anlık görüntüsünün içeriğine göz atabilirsiniz:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Paylaşım anlık görüntülerini listeleme
Paylaşımınız için aldığınız anlık görüntülerin listesini görmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Paylaşım anlık görüntüsünden geri yükleme
Daha önce kullandığınız `az storage file copy start` komutunu kullanarak dosyayı geri yükleyebilirsiniz. İlk olarak, anlık görüntüden geri yükleyebilmek için, karşıya yüklediğiniz SampleUpload.txt dosyasını silin:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"
 # Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')
 URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT
 # Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Paylaşım anlık görüntüsünü silme
[`az storage share delete`](/cli/azure/storage/share) komutunu kullanarak bir paylaşım anlık görüntüsünü silebilirsiniz. `--snapshot` parametresine `$SNAPSHOT` başvurusunu içeren değişkeni kullanın:

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Kaynakları temizleme
İşiniz bittiğinde, [`az group delete`](/cli/azure/group) komutunu kullanarak kaynak grubunu ve tüm ilgili kaynakları kaldırabilirsiniz: 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Alternatif olarak, kaynakları ayrı ayrı kaldırabilirsiniz.
- Bu makale boyunca oluşturduğunuz Azure dosya paylaşımlarını kaldırmak için:

    ```azurecli-interactive
    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare" \
        --delete-snapshots include

    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare2" \
        --delete-snapshots include
    ```

- Depolama hesabını kaldırmak için. (Bu işlem, oluşturduğunuz Azure dosya paylaşımlarını ve bir Azure Blob depolama kapsayıcısı gibi oluşturmuş olabileceğiniz diğer depolama kaynaklarını örtülü olarak kaldırır.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Dosyaları nedir?](storage-files-introduction.md)
