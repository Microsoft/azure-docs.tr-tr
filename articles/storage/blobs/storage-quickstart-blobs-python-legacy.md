---
title: 'Hızlı başlangıç: Python için Azure Blob depolama istemci kitaplığı v 2.1'
description: Bu hızlı başlangıçta, nesne (Blob) depolamada depolama hesabı ve kapsayıcı oluşturursunuz. Daha sonra, Azure depolama 'ya blob yüklemek, blob indirmek ve bir kapsayıcıdaki Blobları listelemek için Python için depolama istemcisi kitaplığı v 2.1 kullanın.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 01ca2ee1bcd551baa471dda64d636e077f6e1a82
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275456"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Hızlı başlangıç: Python v 2.1 SDK ile Blobları yönetme

Bu hızlı başlangıçta, Python kullanarak blob 'ları yönetmeyi öğreneceksiniz. Blob 'lar, görüntüler, belgeler, akış ortamları ve arşiv verileri gibi büyük miktarlarda metin veya ikili veri içerebilen nesnelerdir. Blobları karşıya yükleyebilir, indirebilir ve listetireceksiniz ve kapsayıcı oluşturup sileceksiniz.

> [!NOTE]
> Bu hızlı başlangıç, Azure Blob depolama istemci kitaplığı 'nın eski bir sürümünü kullanır. En son sürümü kullanmaya başlamak için bkz. [hızlı başlangıç: Python V12 SDK ile Blobları yönetme](storage-quickstart-blobs-python.md).

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Depolama hesabı. [Depolama hesabı oluşturun](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/).
- [Python Için Azure depolama SDK 'sı](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>Örnek uygulamayı indirin:

Bu hızlı başlangıçtaki [örnek uygulama](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git), temel bir Python uygulamasıdır.  

Uygulamayı geliştirme ortamınıza indirmek için aşağıdaki [Git](https://git-scm.com/) komutunu kullanın. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Python programını gözden geçirmek için deponun kökündeki *example.py* dosyasını açın.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma

Uygulamada, `BlockBlobService` nesnesi oluşturmak için depolama hesabı adınızı ve hesap anahtarınızı sağlayın.

1. IDE'nizdeki Çözüm Gezgini'nde *example.py* dosyasını açın.

1. `accountname`Ve `accountkey` değerlerini depolama hesabı adınızla ve anahtarınızla değiştirin:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Dosyayı kaydedin ve kapatın.

## <a name="run-the-sample"></a>Örneği çalıştırma

Örnek program, *Belgeler* klasörünüzde bir test dosyası oluşturur, dosyayı blob depolamaya yükler, dosyadaki Blobları listeler ve dosyayı yeni bir adla indirir.

1. Bağımlılıkları yükler:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Örnek uygulamaya gidin:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Örneği çalıştırın:

    ```console
    python example.py
    ```

    Aşağıdaki çıkışa benzer iletiler görürsünüz:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. Devam etmeden önce *Belgeler* klasörünüze gidin ve iki dosyayı kontrol edin.

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_ \<universally-unique-identifier\> _DOWNLOADED*

1. Dosyaları açarak aynı olduklarını görebilirsiniz.

    [Azure Depolama Gezgini](https://storageexplorer.com)gibi bir araç da kullanabilirsiniz. Blob depolamada dosyaları görüntülemek iyidir. Azure Depolama Gezgini, depolama hesabı bilgilerinize erişmenize olanak tanıyan ücretsiz bir platformlar arası araçtır. 

1. Dosyalara bakdıktan sonra, örneği tamamladıktan sonra test dosyalarını silmek için herhangi bir tuşa basın.

## <a name="learn-about-the-sample-code"></a>Örnek kod hakkında bilgi edinin

Artık örnek dosyanın işlevini gördüğünüze göre, koda göz atmak için *example.py* dosyasını açabilirsiniz.

### <a name="get-references-to-the-storage-objects"></a>Depolama nesneleriyle ilgili başvuruları alma

Bu bölümde nesne örneği ve yeni bir kapsayıcı oluşturacak ve ardından kapsayıcıdaki izinleri bloblar herkese açık olacak şekilde ayarlayacaksınız. Kapsayıcıyı çağıracaksınız `quickstartblobs` . 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

İlk önce, Blob depolamaya erişmek ve Blob depolamayı yönetmek için kullanılan nesnelere başvuru oluşturursunuz. Bu nesneler birbirleri üzerinde derlenir ve her bir dosya, listede yanında yer alan dosya tarafından kullanılır.

* Depolama hesabınızdaki Blob hizmetine işaret eden bir **BlobService** nesne örneği oluşturun. 

* Eriştiğiniz kapsayıcıyı temsil eden bir **CloudBlobContainer** nesne örneği oluşturun. Sistem, dosyalarınızı düzenlemek için bilgisayarınızdaki klasörleri kullandığınız gibi bloblarınızı düzenlemek için kapsayıcıları kullanır.

Bulut Blobu kapsayıcınız olduktan sonra, ilgilendiğiniz bloba işaret eden **CloudBlockBlob** nesnesinin örneğini oluşturun. Ardından blobu gerektiği gibi karşıya yükleyebilir, indirebilir ve kopyalayabilirsiniz.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcı ve BLOB adları hakkında daha fazla bilgi için bkz. [kapsayıcıları, Blobları ve meta verileri adlandırma ve başvuru](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="upload-blobs-to-the-container"></a>Blobları kapsayıcıya yükleme

Blob depolama blok blobları, ekleme bloblarını ve sayfa bloblarını destekler. Blok bloblarının boyutu 4,7 TB’yi bulabilir ve bu bloblar Excel elektronik tablolarından büyük video dosyalarına kadar birçok türde olabilir. Bir dosyaya yazmak ve daha sonra daha fazla bilgi eklemeye devam etmek istediğinizde, günlüğe kaydetme için ekleme bloblarını kullanabilirsiniz. Sayfa Blobları birincil olarak hizmet sanal makineleri (IaaS VM 'Ler) olan sanal sabit disk (VHD) dosyaları için kullanılır. Blok blobları, en sık kullanılan bloblardır. Bu hızlı başlangıç blok bloblarını kullanır.

Bir dosyayı bloba yüklemek için, yerel diskinizdeki dizin adıyla dosya adını birleştirerek dosyanın tam yolunu alın. Sonra, dosyayı belirtilen yola `create_blob_from_path` yöntemiyle yükleyebilirsiniz. 

Örnek kod, sistemin karşıya yükleme ve indirme için kullandığı yerel bir dosya oluşturur, dosyayı *full_path_to_file* olarak sistem karşıya yükleme ve blob adı *local_file_name* olarak depolar. Bu örnek, dosyayı adlı kapsayıcıya yükler `quickstartblobs` :

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Blob depolamayla kullanabileceğiniz çeşitli karşıya yükleme yöntemleri vardır. Örneğin, bir bellek akışınız varsa `create_blob_from_path` yerine `create_blob_from_stream` yöntemini kullanabilirsiniz. 

### <a name="list-the-blobs-in-a-container"></a>Kapsayıcıdaki blobları listeleme

Aşağıdaki kod `generator` , yöntemi için bir oluşturur `list_blobs` . Kod, kapsayıcıdaki Blobların listesi boyunca döngü sağlar ve adlarını konsola yazdırır.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Blobları indirme


Yöntemini kullanarak Blobları yerel diskinize indirin `get_blob_to_path` .
Aşağıdaki kod, daha önce karşıya yüklediğiniz blobu indirir. Sistem, her iki dosyayı da yerel diskinizde görebileceğiniz şekilde blob adına *_DOWNLOADED* ekler.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Kaynakları temizleme
Bu hızlı başlangıçta karşıya yüklenen bloblara artık ihtiyacınız kalmadığında, `delete_container` yöntemini kullanarak kapsayıcının tamamını silebilirsiniz. Bunun yerine tek tek dosyaları silmek için `delete_blob` yöntemini kullanın.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Bloblarla Python uygulamaları geliştirme kaynakları

BLOB depolama ile Python geliştirme hakkında daha fazla bilgi için şu ek kaynaklara bakın:

### <a name="binaries-and-source-code"></a>İkili dosyalar ve kaynak kodu

- GitHub’da Azure Depolama için [Python istemci kitaplığı kaynak kodunu](https://github.com/Azure/azure-storage-python) görüntüleyin, indirin ve yükleyin.

### <a name="client-library-reference-and-samples"></a>İstemci kitaplığı başvurusu ve örnekleri

- Python istemci kitaplığı hakkında daha fazla bilgi için bkz. [Python Için Azure depolama kitaplıkları](/python/api/overview/azure/storage).
- Python istemci kitaplığı kullanılarak yazılmış [Blob depolama örneklerini](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) araştırın.

## <a name="next-steps"></a>Sonraki adımlar
 
Bu hızlı başlangıçta, dosyaları Python kullanarak yerel bir disk ve Azure Blob depolama arasında aktarmayı öğrendiniz. 

Depolama Gezgini ve BLOB 'Lar hakkında daha fazla bilgi için bkz. [Depolama Gezgini Ile Azure Blob depolama kaynaklarını yönetme](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).