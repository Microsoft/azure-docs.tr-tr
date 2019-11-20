---
title: Python ile Azure dosyaları için geliştirme | Microsoft Docs
description: Dosya verilerini depolamak için Azure dosyaları kullanan Python uygulamalarını ve hizmetlerini geliştirmeyi öğrenin.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699388"
---
# <a name="develop-for-azure-files-with-python"></a>Python ile Azure dosyaları için geliştirme
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Bu öğreticide, dosya verilerini depolamak için Azure dosyaları kullanan uygulamalar veya hizmetler geliştirmek üzere Python kullanmanın temelleri gösterilir. Bu öğreticide, basit bir konsol uygulaması oluşturacağız ve Python ve Azure dosyaları ile nasıl temel eylemler gerçekleştirileceğini göstereceğiz:

* Azure dosya paylaşımları oluşturma
* Dizin oluşturma
* Azure dosya paylaşımındaki dosyaları ve dizinleri listeleme
* Dosya yükleme, indirme ve silme

> [!Note]  
> Azure dosyalarına SMB üzerinden erişilebildiğinden, standart Python g/ç sınıfları ve işlevleri kullanılarak Azure dosya paylaşımında erişim sağlayan basit uygulamalar yazmak mümkündür. Bu makalede, Azure dosyaları ile iletişim sağlamak için [REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) Azure Storage Python SDK 'sını kullanan uygulamaların nasıl yazılacağı açıklanır.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Python için Azure depolama SDK 'sını indirme ve yükleme

[Python Için Azure depolama SDK 'sı](https://github.com/azure/azure-storage-python) , Python 2,7, 3,3, 3,4, 3,5 veya 3,6 gerektirir.
 
## <a name="install-via-pypi"></a>PyPi aracılığıyla yüklemesi

Python paket dizini (Pypı) aracılığıyla yüklemek için şunu yazın:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Python sürüm 0,36 veya önceki bir sürümü için Azure Storage SDK 'dan yükseltiyorsanız, en son paketi yüklemeden önce kullanarak `pip uninstall azure-storage` eski SDK 'yı kaldırın.

Alternatif yükleme yöntemleri için [GitHub 'Da Python Için Azure depolama SDK 'sını](https://github.com/Azure/azure-storage-python/)ziyaret edin.

## <a name="view-the-sample-application"></a>Örnek uygulamayı görüntüleme
f Azure dosyaları ile Python 'un nasıl kullanılacağını gösteren örnek bir uygulamayı görüntüleyip çalıştırmak için bkz [. Azure Storage: Python](https://github.com/Azure-Samples/storage-file-python-getting-started)'da Azure dosyaları ile çalışmaya başlama. 

Örnek uygulamayı çalıştırmak için hem `azure-storage-file` hem `azure-storage-common` de paketlerini yüklediğinizden emin olun.

## <a name="set-up-your-application-to-use-azure-files"></a>Uygulamanızı Azure dosyalarını kullanacak şekilde ayarlama
Azure Storage 'a programlı bir şekilde erişmek istediğiniz herhangi bir Python kaynak dosyasının en üstüne aşağıdakileri ekleyin.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Azure dosyaları ile bağlantı kurma 
`FileService` Nesnesi paylaşımlar, dizinler ve dosyalarla çalışmanıza olanak sağlar. Aşağıdaki kod, depolama hesabı `FileService` adını ve hesap anahtarını kullanarak bir nesnesi oluşturur. `<myaccount>` ve `<mykey>` ifadelerini hesap adınız ve anahtarınız ile değiştirin.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma
Aşağıdaki kod örneğinde, yoksa, paylaşma oluşturmak için bir `FileService` nesnesi kullanabilirsiniz.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Dizin oluşturma
Ayrıca, dosyaları kök dizinde kullanmak yerine alt dizinlerin içine yerleştirerek depolamayı da düzenleyebilirsiniz. Azure dosyaları, hesabınızın izin verdiği sayıda dizin oluşturmanıza olanak sağlar. Aşağıdaki kod, kök dizin altında **sampledir** adlı bir alt dizin oluşturacak.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure dosya paylaşımındaki dosyaları ve dizinleri listeleme
Bir paylaşımdaki dosya ve dizinleri listelemek için **dizinleri\_\_\_ve dosyaları Listele** yöntemini kullanın. Bu yöntem bir oluşturucu döndürür. Aşağıdaki kod, bir paylaşımdaki her bir dosya ve dizinin **adını** konsola verir.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Karşıya dosya yükle 
Azure dosya paylaşımında, dosyaların bulunabileceği bir kök dizin en azından bulunur. Bu bölümde yerel depolama alanından bir paylaşımın kök dizinine dosya yüklemeyi öğreneceksiniz.

Bir dosya oluşturmak ve verileri karşıya yüklemek `create_file_from_path`için, `create_file_from_bytes` veya `create_file_from_text` yöntemlerini `create_file_from_stream`kullanın. Bunlar, verilerin boyutu 64 MB 'ı aştığında gerekli parçalama gerçekleştiren üst düzey yöntemlerdir.

`create_file_from_path`Belirtilen yoldan bir dosyanın içeriğini karşıya yükler ve `create_file_from_stream` içeriği zaten açık olan bir dosyadan/akıştan karşıya yükler. `create_file_from_bytes`bir bayt dizisini karşıya yükler ve `create_file_from_text` belirtilen kodlamayı kullanarak belirtilen metin değerini yükler (varsayılan olarak UTF-8).

Aşağıdaki örnek, **batımı. png** dosyasının içeriğini **Dosyam** dosyasına yükler.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Dosya indir
Bir dosyadaki verileri indirmek için,, veya `get_file_to_path` `get_file_to_text`kullanın `get_file_to_stream` `get_file_to_bytes`. Bunlar, verilerin boyutu 64 MB 'ı aştığında gerekli parçalama gerçekleştiren üst düzey yöntemlerdir.

Aşağıdaki örnek **, dosya** dosyasının `get_file_to_path` içeriğini indirmek ve **Out-Sunset. png** dosyasında depolamak için kullanımını gösterir.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Dosyayı silme
Son olarak, bir dosyayı silmek için çağrısı `delete_file`yapın.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Paylaşma anlık görüntüsü oluştur
Tüm dosya paylaşımınızın zaman içinde bir noktasını oluşturabilirsiniz.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Meta verilerle paylaşma anlık görüntüsü oluşturma**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Paylaşımları ve anlık görüntüleri listeleme 
Belirli bir paylaşıma ait tüm anlık görüntüleri listeleyebilirsiniz.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Paylaşma anlık görüntüsüne gözatamıyorum
Bu zaman noktasından dosya ve dizinleri almak için her bir paylaşılan anlık görüntünün içeriğine gözatabilmeniz gerekir.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Paylaşma anlık görüntüsünden dosya al
Geri yükleme senaryonuz için bir paylaşma anlık görüntüsünden dosya indirebilirsiniz.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Tek bir Share anlık görüntüsünü silme  
Tek bir Share anlık görüntüsünü silebilirsiniz.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Paylaşma anlık görüntüleri varsa, paylaşma silme
Tüm anlık görüntüler önce silinmediği takdirde anlık görüntü içeren bir paylaşma silinemez.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Sonraki adımlar
Azure dosyalarını Python ile nasıl işleyebileceğinizi öğrendiğinize göre, daha fazla bilgi edinmek için bu bağlantıları izleyin.

* [Python Geliştirici Merkezi](https://azure.microsoft.com/develop/python/)
* [Azure Depolama Hizmetleri REST API'si](https://msdn.microsoft.com/library/azure/dd179355)
* [Python için SDK Microsoft Azure Depolama](https://github.com/Azure/azure-storage-python)
