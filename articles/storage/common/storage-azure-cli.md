---
title: Azure depolama ile Azure CLı kullanma | Microsoft Docs
description: Azure komut satırı arabirimi (Azure CLı) ile Azure depolama hesabı oluşturup bunları yönetme ve Azure Blob 'ları ile dosyalarla çalışma hakkında bilgi edinin.
services: storage
author: tamram
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 46ae70bf4f1c2fe0276a3327ff37650dd57341d0
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259397"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>Azure Storage ile Azure CLI kullanma

Açık kaynaklı, platformlar arası Azure CLı, Azure platformuyla çalışmaya yönelik bir dizi komut sağlar. Zengin veri erişimi de dahil olmak üzere, [Azure Portal](https://portal.azure.com)bulunan işlevlerin çoğunu sağlar.

Bu kılavuzda, Azure Depolama hesabınızdaki kaynaklarla çalışan çeşitli görevleri gerçekleştirmek için [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) 'yı nasıl kullanacağınızı göstereceğiz. Bu kılavuzu kullanmadan önce CLı 'nın en son sürümünü indirip yüklemenizi veya yükseltmenizi öneririz.

Kılavuzdaki örnekler Ubuntu üzerinde bash kabuğu kullanımını varsayar, ancak diğer platformların benzer şekilde gerçekleştirmesi gerekir. 

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Önkoşullar
Bu kılavuzda, Azure depolama 'nın temel kavramlarını anladığınızı varsaymaktadır. Ayrıca, Azure ve depolama hizmeti için aşağıda belirtilen hesap oluşturma gereksinimlerini karşılayabildiğinizi varsaymaktadır.

### <a name="accounts"></a>Hesaplar
* **Azure hesabı**: Henüz bir Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı oluşturun](https://azure.microsoft.com/free/).
* **Depolama hesabı**: Bkz. [Azure depolama hesapları hakkında](storage-create-storage-account.md)bölümünde [depolama hesabı oluşturma](storage-quickstart-create-account.md) .

### <a name="install-the-azure-cli"></a>Azure CLI'yı yükleme

Azure [CLI 'Yı yükleme](/cli/azure/install-az-cli2)bölümünde özetlenen yönergeleri IZLEYEREK Azure CLI 'yı indirip yükleyin.

> [!TIP]
> Yükleme ile ilgili sorun yaşıyorsanız, makalenin [yükleme sorunlarını giderme](/cli/azure/install-az-cli2) bölümüne bakın ve GitHub 'Da [sorun giderme kılavuzunu yükleyebilirsiniz](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) .
>

## <a name="working-with-the-cli"></a>CLı ile çalışma

CLI 'yı yükledikten sonra, Azure CLI komutlarına erişmek için komut `az` satırı arabiriminizdeki (Bash, Terminal, komut istemi) komutunu kullanabilirsiniz. Temel komutların tam listesini görmek için komutunuyazın(Aşağıdakiörnekçıktıkesildi):`az`

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

Komut satırı arabiriminizdeki komutu, komut alt grupları `az storage --help` `storage` listelemek için yürütün. Alt grupların açıklamaları, Azure CLı 'nın depolama kaynaklarınızla çalışma sağladığı işlevlere genel bir bakış sağlar.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>CLı 'yı Azure aboneliğinize bağlama

Azure aboneliğinizdeki kaynaklarla çalışmak için öncelikle ile `az login`Azure hesabınızda oturum açmalısınız. Oturum açabilmenizin birkaç yolu vardır:

* **Etkileşimli oturum açma**:`az login`
* **Kullanıcı adı ve parolayla oturum açın**:`az login -u johndoe@contoso.com -p VerySecret`
  * Bu, çok faktörlü kimlik doğrulaması kullanan Microsoft hesaplarıyla veya hesaplarıyla çalışmaz.
* **Hizmet sorumlusu Ile oturum açın**:`az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>Azure CLı örnek betiği

Daha sonra, Azure depolama kaynaklarıyla etkileşimde bulunmak için birkaç temel Azure CLı komutu veren küçük bir kabuk betiği ile çalışacağız. Betik ilk olarak depolama hesabınızda yeni bir kapsayıcı oluşturur, ardından var olan bir dosyayı (blob olarak) o kapsayıcıya yükler. Ardından, kapsayıcıdaki tüm Blobları listeler ve son olarak dosyayı belirttiğiniz yerel bilgisayarınızdaki bir hedefe indirir.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**Betiği yapılandırma ve çalıştırma**

1. En sevdiğiniz metin düzenleyicinizi açın, ardından önceki betiği kopyalayıp düzenleyiciye yapıştırın.

2. Sonra, betiklerin değişkenlerini yapılandırma ayarlarınızı yansıtacak şekilde güncelleştirin. Aşağıdaki değerleri belirtilen şekilde değiştirin:

   * storage_account_name depolama hesabınızın adını. **\<\>**
   * depolama hesabınız için birincil veya ikincil erişim anahtarını storage_account_key. **\> \<**
   * container_name A, "Azure-CLI-Sample-Container" gibi oluşturulacak yeni kapsayıcıyı adlandırın. **\<\>**
   * BLOB_NAME, kapsayıcıda hedef blobu için bir ad. **\<\>**
   * Yerel bilgisayarınızdaki küçük dosyanın yolunu **file_to_upload\> (örneğin, "~/images/HelloWorld.exe"). \<**
   * "~/downloadedImage.png" gibi hedef dosya yolunu destination_file. **\> \<**

3. Gerekli değişkenleri güncelleştirdikten sonra, betiği kaydedin ve düzenleyiciden çıkın. Sonraki adımlarda, **my_storage_sample. sh**betiğinizi adlandırdığınızı varsayalım.

4. Gerekirse betiği çalıştırılabilir olarak işaretleyin:`chmod +x my_storage_sample.sh`

5. Betiği yürütün. Örneğin, bash:`./my_storage_sample.sh`

Aşağıdakine benzer bir çıktı görmeniz gerekir ve **\<\>** betikte belirttiğiniz destination_file yerel bilgisayarınızda görünmelidir.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> Önceki çıktı **tablo** biçimindedir. CLI komutlarınızın `--output` bağımsız değişkenini belirterek kullanılacak çıkış biçimini belirtebilir veya bunu kullanarak `az configure`genel olarak ayarlayabilirsiniz.
>

## <a name="manage-storage-accounts"></a>Depolama hesaplarını yönetme

### <a name="create-a-new-storage-account"></a>Yeni depolama hesabı oluştur
Azure Depolama kullanmak için bir depolama hesabınız olması gerekir. Bilgisayarınızı aboneliğinize bağlanacak şekilde yapılandırdıktan sonra yeni bir Azure depolama hesabı oluşturabilirsiniz.

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location`[Gerekli]: Konumuna. Örneğin, "Batı ABD".
* `--name`[Gerekli]: Depolama hesabı adı. Ad 3 ile 24 karakter uzunluğunda olmalı ve yalnızca küçük harf alfasayısal karakterler kullanmalıdır.
* `--resource-group`[Gerekli]: Kaynak grubunun adı.
* `--sku`[Gerekli]: Depolama hesabı SKU 'SU. İzin verilen değerler:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`
  * `Standard_GZRS`Önizle
  * `Standard_RAGZRS`Önizle

### <a name="set-default-azure-storage-account-environment-variables"></a>Varsayılan Azure depolama hesabı ortam değişkenlerini ayarlama

Azure aboneliğinizde birden çok depolama hesabınız olabilir. Sonraki tüm depolama komutlarında kullanılacak birini seçmek için, bu ortam değişkenlerini ayarlayabilirsiniz:

Öncelikle [az storage account keys list](/cli/azure/storage/account/keys) komutunu kullanarak depolama hesabı anahtarlarınızı görüntüleyin:

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

Artık anahtara sahip olduğunuza göre, bunu ve hesap adını ortam değişkenleri olarak tanımlayabilirsiniz:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

Varsayılan depolama hesabını ayarlamak için bir diğer yol ise bağlantı dizesi kullanmaktır. İlk olarak, bağlantı dizesini `show-connection-string` şu komutla alın:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Ardından çıkış bağlantı dizesini kopyalayın ve `AZURE_STORAGE_CONNECTION_STRING` ortam değişkenini ayarlayın (bağlantı dizesini tırnak içine almanız gerekebilir):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Bu makalenin aşağıdaki bölümlerindeki tüm örnekler, `AZURE_STORAGE_ACCOUNT` ve `AZURE_STORAGE_KEY` ortam değişkenlerini ayarlamış olduğunuz varsayılmaktadır.

## <a name="create-and-manage-blobs"></a>Bloblar oluşturma ve yönetme
Azure Blob depolama, HTTP veya HTTPS aracılığıyla dünyanın her yerinden erişilebilen metin veya ikili veriler gibi büyük miktarda yapılandırılmamış veriyi depolamaya yönelik bir hizmettir. Bu bölümde, Azure Blob depolama kavramlarını zaten bildiğiniz varsayılmaktadır. Ayrıntılı bilgi için bkz. .NET ve [BLOB hizmeti kavramlarını](/rest/api/storageservices/blob-service-concepts) [kullanarak Azure Blob depolamayı kullanmaya başlama](../blobs/storage-dotnet-how-to-use-blobs.md) .

### <a name="create-a-container"></a>Bir kapsayıcı oluşturma
Azure Storage 'daki her blob bir kapsayıcıda olmalıdır. Şu `az storage container create` komutu kullanarak bir kapsayıcı oluşturabilirsiniz:

```azurecli
az storage container create --name <container_name>
```

İsteğe bağlı `--public-access` bağımsız değişkeni belirterek, yeni bir kapsayıcı için üç okuma erişimi düzeyinden birini ayarlayabilirsiniz:

* `off`(varsayılan): Kapsayıcı verileri, hesap sahibine özeldir.
* `blob`: Bloblar için genel okuma erişimi.
* `container`: Genel okuma ve kapsayıcının tamamına erişimi listeleme.

Daha fazla bilgi için bkz. [Kapsayıcılara ve bloblara anonim okuma erişimini yönetme](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Bir kapsayıcıya blob yükleme
Azure Blob depolama, blok, ekleme ve sayfa bloblarını destekler. Şu `blob upload` komutu kullanarak blob 'ları bir kapsayıcıya yükleyin:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

Depolama hesabınızdaki kapsayıcısı içindeki bir klasöre doğrudan yüklemek isterseniz, ile `--name <blob_name>` `--name <folder/blob_name>`değiştirin.

 Varsayılan olarak, `blob upload` komut *. vhd dosyalarını sayfa bloblarına yükler veya aksi takdirde blob 'ları engeller. Bir blobu karşıya yüklerken başka bir tür belirtmek `--type` için bağımsız değişkenini kullanabilirsiniz--izin verilen `append`değerler, `block`, ve `page`.

 Farklı blob türleri hakkında daha fazla bilgi için bkz. [blok bloblarını, ekleme bloblarını ve sayfa Bloblarını anlama](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Kapsayıcıdan blob indirme
Bu örnek, bir kapsayıcıdan bir Blobun nasıl indirileceğini gösterir:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

[Az Storage blob List](/cli/azure/storage/blob) komutuyla bir kapsayıcıdaki Blobları listeleyin.

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Blobları kopyalama
Blobları, depolama hesaplarıyla bölgeler içinde veya bunların arasında zaman uyumsuz olarak kopyalayabilirsiniz.

Aşağıdaki örnekte blobların bir depolama hesabından diğerine nasıl kopyalandığı gösterilmektedir. Önce kaynak depolama hesabında bir kapsayıcı oluşturacağız ve blobları için genel okuma erişimi belirteceğiz. Ardından, kapsayıcıya bir dosya yükleyip son olarak da bu kapsayıcıdan blobu hedef depolama hesabındaki bir bloba kopyalayacağız.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

Yukarıdaki örnekte, kopyalama işleminin başarılı olması için hedef kapsayıcının hedef depolama hesabında zaten mevcut olması gerekir. Ayrıca, `--source-uri` bağımsız değişkeninde belirtilen kaynak blobun bir paylaşılan erişim imzası (SAS) içermesi veya bu örnekte olduğu gibi genel olarak erişilebilir olması gerekir.

### <a name="delete-a-blob"></a>Blob silme
Bir blobu silmek için şu `blob delete` komutu kullanın:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

### <a name="set-the-content-type"></a>İçerik türünü ayarlama

Aynı zamanda MIME türü olarak da bilinen içerik türü, blob verilerinin biçimini belirler. Tarayıcılar ve diğer yazılımlar, verilerin nasıl işleneceğini belirlemek için içerik türünü kullanır. Örneğin, PNG görüntülerinin `image/png`içerik türü. İçerik türünü ayarlamak için şu `blob update` komutu kullanın:

```azurecli
az storage blob update
    --container-name <container_name> 
    --name <blob_name>
    --content-type <content_type>
```

## <a name="create-and-manage-file-shares"></a>Dosya paylaşımları oluşturma ve yönetme
Azure dosyaları, sunucu Ileti bloğu (SMB) protokolünü kullanan uygulamalar için paylaşılan depolama alanı sunar. Sanal makineler ve bulut hizmetlerinin yanı sıra şirket içi uygulamalar Microsoft Azure, bağlı paylaşımlar aracılığıyla dosya verilerini paylaşabilir. Azure CLı aracılığıyla dosya paylaşımlarını ve dosya verilerini yönetebilirsiniz. Azure dosyaları hakkında daha fazla bilgi için bkz. [Azure dosyaları 'Na giriş](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Dosya paylaşımı oluşturma
Azure dosya paylaşma, Azure 'da bir SMB dosya paylaşımıdır. Tüm dizinlerin ve dosyaların bir dosya paylaşımında oluşturulması gerekir. Bir hesap sınırsız sayıda paylaşım içerebilir ve bir paylaşım, depolama hesabının kapasite sınırlarına kadar sınırsız sayıda dosyayı depolayabilirler. Aşağıdaki örnek **myshare**adlı bir dosya paylaşımının oluşturur.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Dizin oluşturma
Bir dizin, Azure dosya paylaşımında hiyerarşik bir yapı sağlar. Aşağıdaki örnek dosya paylaşımında **mydir** adlı bir dizin oluşturur.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Bir dizin yolu birden çok düzey içerebilir, örneğin **dir1/dir2**. Ancak, bir alt dizin oluşturmadan önce tüm üst dizinlerin var olduğundan emin olmanız gerekir. Örneğin, **dir1/dir2**yolu için önce Dizin **dir1**oluşturmanız ve ardından dizin **dir2**oluşturmanız gerekir.

### <a name="upload-a-local-file-to-a-share"></a>Bir paylaşıma yerel bir dosya yükleme
Aşağıdaki örnek, **myshare** dosya paylaşımının köküne **~/Temp/sampledosya.txt** konumundan bir dosya yükler. `--source` Bağımsız değişkeni karşıya yüklenecek var olan yerel dosyayı belirtir.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Dizin oluştururken olduğu gibi, dosyayı paylaşımdaki mevcut bir dizine yüklemek için paylaşımdaki bir dizin yolu belirtebilirsiniz:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Paylaşımdaki bir dosya boyutu en fazla 1 TB olabilir.

### <a name="list-the-files-in-a-share"></a>Bir paylaşımdaki dosyaları listeleme
Şu `az storage file list` komutu kullanarak bir paylaşımdaki dosyaları ve dizinleri listeleyebilirsiniz:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Dosyaları kopyalama      
Bir dosyayı başka bir dosyaya, bir blob 'a veya bir dosyaya blob 'a kopyalayabilirsiniz. Örneğin, bir dosyayı farklı bir paylaşımdaki dizine kopyalamak için:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Paylaşma anlık görüntüsü oluştur
Şu `az storage share snapshot` komutu kullanarak bir paylaşma anlık görüntüsü oluşturabilirsiniz:

```cli
az storage share snapshot -n <share name>
```

Örnek çıkış
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Paylaşım anlık görüntülerini listeleme

Kullanarak belirli bir paylaşımın paylaşılan anlık görüntülerini listeleyebilirsiniz`az storage share list --include-snapshots`

```cli
az storage share list --include-snapshots
```

**Örnek çıkış**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Paylaşım anlık görüntülerine göz atma
Ayrıca, içeriğini kullanarak `az storage file list`görüntülemek için belirli bir paylaşılan anlık görüntüye de gidebilirsiniz. Birinin, paylaşma adını `--share-name <snare name>` ve zaman damgasını belirtmesi`--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Örnek çıkış**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Paylaşma anlık görüntülerinden geri yükleme

Komutu kullanarak `az storage file download` bir dosya paylaşımından bir dosyayı kopyalayarak veya indirerek dosyayı geri yükleyebilirsiniz

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Örnek çıkış**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Paylaşma anlık görüntüsünü Sil
Paylaşılan anlık görüntü zaman damgasıyla parametre sağlayarak `az storage share delete` `--snapshot` komutu kullanarak bir paylaşma anlık görüntüsünü silebilirsiniz:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Örnek çıkış
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Sonraki adımlar
Azure CLı ile çalışma hakkında daha fazla bilgi edinmek için bazı ek kaynaklar aşağıda verilmiştir. 

* [Azure CLı ile çalışmaya başlama](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Azure CLı komut başvurusu](/cli/azure)
* [GitHub 'da Azure CLı](https://github.com/Azure/azure-cli)
