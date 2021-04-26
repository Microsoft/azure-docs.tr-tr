---
title: Python ile Azure dosyaları için geliştirme | Microsoft Docs
description: Dosya verilerini depolamak için Azure dosyaları kullanan Python uygulamalarını ve hizmetlerini geliştirmeyi öğrenin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 8739bfaf1a41758ef3267c71cba883ef2445c39d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817801"
---
# <a name="develop-for-azure-files-with-python"></a>Python ile Azure Dosyaları için geliştirme

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Dosya verilerini depolamak için Azure dosyaları kullanan uygulamaları veya hizmetleri geliştirmek üzere Python kullanma hakkında temel bilgileri öğrenin. Basit bir konsol uygulaması oluşturun ve Python ve Azure dosyaları ile temel eylemleri gerçekleştirmeyi öğrenin:

- Azure dosya paylaşımları oluşturma
- Dizinleri oluşturma
- Azure dosya paylaşımındaki dosyaları ve dizinleri listeleme
- Dosya yükleme, indirme ve silme
- Anlık görüntüler kullanarak dosya paylaşma yedeklemeleri oluşturma

> [!NOTE]
> Azure dosyalarına SMB üzerinden erişilebildiğinden, standart Python g/ç sınıfları ve işlevleri kullanılarak Azure dosya paylaşımında erişim sağlayan basit uygulamalar yazmak mümkündür. Bu makalede, Azure dosyaları ile iletişim sağlamak için [REST API](/rest/api/storageservices/file-service-rest-api) Azure dosyaları kullanan Azure dosyaları depolama Python SDK 'sını kullanan uygulamaların nasıl yazılacağı açıklanır.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Python için Azure depolama SDK 'sını indirme ve yükleme

> [!NOTE]
> Python sürüm 0,36 veya önceki bir sürümü için Azure Storage SDK 'dan yükseltiyorsanız, `pip uninstall azure-storage` en son paketi yüklemeden önce kullanarak eski SDK 'yı kaldırın.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

[Python Için Azure dosya depolama istemci kitaplığı v12. x](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) , Python 2,7 veya 3.6 + gerektirir.

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

[Python Için Azure depolama SDK 'sı](https://github.com/azure/azure-storage-python) , Python 2,7 veya 3.6 + gerektirir.

---

## <a name="install-via-pypi"></a>PyPI aracılığıyla yüklemesi

Python paket dizini (Pypı) aracılığıyla yüklemek için şunu yazın:

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>Örnek uygulamayı görüntüleme

Azure dosyaları ile Python 'un nasıl kullanılacağını gösteren örnek bir uygulamayı görüntülemek ve çalıştırmak için bkz. [Azure Storage: Python 'Da Azure dosyaları Ile çalışmaya](https://github.com/Azure-Samples/storage-file-python-getting-started)başlama.

Örnek uygulamayı çalıştırmak için hem hem de paketlerini yüklediğinizden emin olun `azure-storage-file` `azure-storage-common` .

---

## <a name="set-up-your-application-to-use-azure-files"></a>Uygulamanızı Azure dosyalarını kullanacak şekilde ayarlama

Bu makaledeki kod parçacıklarını kullanmak için bir Python kaynak dosyasının en üstüne yakınını ekleyin.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Azure dosyaları ile bağlantı kurma

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

[ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) , paylaşımlar, dizinler ve dosyalarla çalışmanıza olanak sağlar. Aşağıdaki kod, `ShareServiceClient` depolama hesabı bağlantı dizesi kullanarak bir nesnesi oluşturur.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

[FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) nesnesi paylaşımlar, dizinler ve dosyalarla çalışmanıza olanak sağlar. Aşağıdaki kod, `FileService` depolama hesabı adını ve hesap anahtarını kullanarak bir nesnesi oluşturur. `<myaccount>` ve `<mykey>` ifadelerini hesap adınız ve anahtarınız ile değiştirin.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

Aşağıdaki kod örneği, yoksa, paylaşım oluşturmak için bir [Shareclient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) nesnesi kullanır.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Aşağıdaki kod örneği, yoksa, paylaşma oluşturmak için bir [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) nesnesi kullanır.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Dizin oluşturma

Dosya dizinlerini kök dizinde kullanmak yerine alt dizinlere yerleştirerek depolamayı düzenleyebilirsiniz.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

Aşağıdaki yöntem, bir [Sharedirectoryclient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) nesnesi kullanarak belirtilen dosya paylaşımının kökünde bir dizin oluşturur.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Aşağıdaki kod, kök dizin altında *sampledir* adlı bir alt dizin oluşturacak.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Dosyayı karşıya yükleme

Bu bölümde, yerel depolama alanından Azure dosya depolama 'ya bir dosya yüklemeyi öğreneceksiniz.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

Aşağıdaki yöntem belirtilen dosyanın içeriğini belirtilen Azure dosya paylaşımında belirtilen dizine yükler.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Bir Azure dosya paylaşımında, en azından dosyaların bulunabileceği bir kök dizin bulunur. Bir dosya oluşturmak ve verileri karşıya yüklemek için [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-), [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)veya [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) yöntemlerini kullanın. Verilerin boyutu 64 MB 'ı aştığında, gereken parçalama gerçekleştiren yüksek düzeyli yöntemlerdir.

`create_file_from_path` Belirtilen yoldan bir dosyanın içeriğini karşıya yükler ve `create_file_from_stream` içeriği zaten açık olan bir dosyadan/akıştan karşıya yükler. `create_file_from_bytes` bir bayt dizisini karşıya yükler ve belirtilen `create_file_from_text` kodlamayı kullanarak belirtilen metin değerini yükler (varsayılan olarak UTF-8).

Aşağıdaki örnek, *sunset.png* dosyasının içeriğini **Dosyam** dosyasına yükler.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure dosya paylaşımındaki dosyaları ve dizinleri listeleme

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

Bir alt dizindeki dosya ve dizinleri listelemek için [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) yöntemini kullanın. Bu yöntem bir otomatik sayfalama yinelenebilir döndürür. Aşağıdaki kod, belirtilen dizindeki her bir dosyanın ve alt dizinin **adını** konsola verir.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Bir paylaşımdaki dosya ve dizinleri listelemek için [list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) yöntemi kullanın. Bu yöntem bir oluşturucu döndürür. Aşağıdaki kod, bir paylaşımdaki her bir dosya ve dizinin **adını** konsola verir.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Dosya indirme

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

Bir dosyadaki verileri indirmek için [Download_File](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-)kullanın.

Aşağıdaki örnek, `download_file` belirtilen dosyanın içeriğini almak için kullanımını gösterir ve dosya adı ' na **indirilir** .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Bir dosyadaki verileri indirmek için [get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)veya [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)kullanın. Verilerin boyutu 64 MB 'ı aştığında, gereken parçalama gerçekleştiren yüksek düzeyli yöntemlerdir.

Aşağıdaki örnek, `get_file_to_path` Dosya dosyasının içeriğini indirmek ve *out-sunset.png* dosyasına depolamak  için kullanımını gösterir.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Paylaşım anlık görüntüsü oluşturma

Tüm dosya paylaşımınızın zaman içinde bir noktasını oluşturabilirsiniz.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Meta verilerle paylaşma anlık görüntüsü oluşturma**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Paylaşımları ve anlık görüntüleri listeleme

Belirli bir paylaşıma ait tüm anlık görüntüleri listeleyebilirsiniz.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Paylaşma anlık görüntüsüne gözatamıyorum

Bu zaman noktasından dosya ve dizinleri almak için her bir paylaşılan anlık görüntüye gözatabilmeniz gerekir.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Paylaşma anlık görüntüsünden dosya al

Bir dosya paylaşma anlık görüntüsünden karşıdan yükleyebilirsiniz. Bu, bir dosyanın önceki bir sürümünü geri yüklemenize olanak sağlar.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Tek bir Share anlık görüntüsünü silme
Tek bir Share anlık görüntüsünü silebilirsiniz.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Dosyayı silme

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

Bir dosyayı silmek için [delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-)çağırın.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Bir dosyayı silmek için [delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-)çağırın.

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Paylaşma anlık görüntüleri varsa, paylaşma silme

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK V12](#tab/python)

Anlık görüntü içeren bir paylaşıma silmek için ile [delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) çağırın `delete_snapshots=True` .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Tüm anlık görüntüler önce silinmediği takdirde anlık görüntü içeren bir paylaşma silinemez.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Sonraki adımlar

Azure dosyalarını Python ile nasıl işleyebileceğinizi öğrendiğinize göre, daha fazla bilgi edinmek için bu bağlantıları izleyin.

- [Python Geliştirici Merkezi](/azure/developer/python/)
- [Azure Storage Hizmetleri REST API’si](/rest/api/azure/)
- [Python için SDK Microsoft Azure Depolama](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
