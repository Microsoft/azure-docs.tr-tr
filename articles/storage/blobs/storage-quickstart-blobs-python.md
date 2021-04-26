---
title: 'Hızlı başlangıç: Azure Blob depolama kitaplığı V12-Python'
description: Bu hızlı başlangıçta, blob (nesne) deposunda bir kapsayıcı ve BLOB oluşturmak için Python için Azure Blob depolama istemci kitaplığı sürüm 12 ' yi nasıl kullanacağınızı öğreneceksiniz. Ardından, blob’u yerel bilgisayarınıza indirmeyi ve bir kapsayıcıdaki tüm blobların listesini görüntülemeyi öğreneceksiniz.
author: twooley
ms.author: twooley
ms.date: 01/28/2021
ms.topic: quickstart
ms.service: storage
ms.subservice: blobs
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 9a6ccbe5482ac8ffe484c3155334cba1b50ba0dc
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535646"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Hızlı başlangıç: Python V12 SDK ile Blobları yönetme

Bu hızlı başlangıçta, Python kullanarak blob 'ları yönetmeyi öğreneceksiniz. Blob 'lar, görüntüler, belgeler, akış ortamları ve arşiv verileri gibi büyük miktarlarda metin veya ikili veri içerebilen nesnelerdir. Blobları karşıya yükleyebilir, indirebilir ve listetireceksiniz ve kapsayıcı oluşturup sileceksiniz.

Daha fazla kaynak:

* [API başvuru belgeleri](/python/api/azure-storage-blob)
* [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Paket (Python paket dizini)](https://pypi.org/project/azure-storage-blob/)
* [Örnekler](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Depolama hesabı. [Depolama hesabı oluşturun](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2,7 veya 3.6 +.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Ayarlanıyor

Bu bölümde, bir projeyi Python için Azure Blob Storage istemci kitaplığı V12 ile çalışacak şekilde hazırlama adımları gösterilmektedir.

### <a name="create-the-project"></a>Proje oluşturma

*BLOB-QuickStart-V12* adlı bir Python uygulaması oluşturun.

1. Konsol penceresinde (cmd, PowerShell veya Bash gibi), proje için yeni bir dizin oluşturun.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Yeni oluşturulan *BLOB-hızlı başlangıç-V12* dizinine geçiş yapın.

    ```console
    cd blob-quickstart-v12
    ```

1. *BLOB-QuickStart-V12* dizininde, *veri* adlı başka bir dizin oluşturun. Bu dizin, blob veri dosyalarının oluşturulup depolanacağı yerdir.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Paketi yükler

Hala uygulama dizininde, komutunu kullanarak Python paketi için Azure Blob depolama istemci kitaplığı 'nı yükleyebilirsiniz `pip install` .

```console
pip install azure-storage-blob
```

Bu komut, Python paketi için Azure Blob depolama istemci kitaplığını ve bağımlı olduğu tüm kitaplıkları kurar. Bu durumda, yalnızca Python için Azure çekirdek kitaplığı vardır.

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Proje dizininden:

1. Kod Düzenleyicinizde yeni bir metin dosyası açın
1. `import`Deyim Ekle
1. Temel özel durum işleme dahil olmak üzere programın yapısını oluşturma

    Kod şu şekildedir:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. *BLOB-QuickStart-V12* dizinine yeni dosyayı *BLOB-QuickStart-v12.py* olarak kaydedin.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Nesne modeli

Azure Blob depolama, büyük miktarlarda yapılandırılmamış verileri depolamak için iyileştirilmiştir. Yapılandırılmamış veriler, metin veya ikili veriler gibi belirli bir veri modeline veya tanımına bağlı olmayan bir veri. BLOB depolama üç tür kaynak sunar:

* Depolama hesabı
* Depolama hesabındaki bir kapsayıcı
* Kapsayıcıda bir blob

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![BLOB depolama mimarisi diyagramı](./media/storage-blobs-introduction/blob1.png)

Şu kaynaklarla etkileşim kurmak için aşağıdaki Python sınıflarını kullanın:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): `BlobServiceClient` sınıfı, Azure depolama kaynaklarını ve BLOB kapsayıcılarını değiştirmenize olanak sağlar.
* [Containerclient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): `ContainerClient` sınıfı, Azure depolama kapsayıcılarını ve bunların bloblarını değiştirmenize olanak sağlar.
* [Blobclient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): `BlobClient` sınıfı, Azure Storage bloblarını değiştirmenize izin verir.

## <a name="code-examples"></a>Kod örnekleri

Bu örnek kod parçacıkları, Python için Azure Blob depolama istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [Bağlantı dizesini alma](#get-the-connection-string)
* [Kapsayıcı oluşturma](#create-a-container)
* [Blobları bir kapsayıcıya yükleme](#upload-blobs-to-a-container)
* [Kapsayıcıdaki blobları listeleme](#list-the-blobs-in-a-container)
* [Blob’ları indirme](#download-blobs)
* [Kapsayıcı silme](#delete-a-container)

### <a name="get-the-connection-string"></a>Bağlantı dizesini alma

Aşağıdaki kod, depolama [bağlantı dizesini yapılandırma](#configure-your-storage-connection-string) bölümünde oluşturulan ortam değişkeninden depolama hesabı bağlantı dizesini alır.

Bu kodu bloğunun içine ekleyin `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Kapsayıcı oluşturma

Yeni kapsayıcı için bir ad belirleyin. Aşağıdaki kod, benzersiz olduğundan emin olmak için kapsayıcı adına bir UUID değeri ekler.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcıları ve blobları adlandırma hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

[From_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) yöntemini çağırarak [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) sınıfının bir örneğini oluşturun. Ardından, depolama hesabınızda kapsayıcıyı gerçekten oluşturmak için [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) yöntemini çağırın.

Bu kodu bloğunun sonuna ekleyin `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Blobları bir kapsayıcıya yükleme

Aşağıdaki kod parçacığı:

1. Veri dosyalarını tutmak için yerel bir dizin oluşturur.
1. Yerel dizinde bir metin dosyası oluşturur.
1. [Kapsayıcı oluşturma](#create-a-container) bölümünde [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) üzerinde [Get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) yöntemini çağırarak bir [blobclient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) nesnesine bir başvuru alır.
1. [Upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) yöntemini çağırarak yerel metin dosyasını bloba yükler.

Bu kodu bloğunun sonuna ekleyin `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Kapsayıcıdaki blobları listeleme

[List_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) yöntemini çağırarak kapsayıcıdaki Blobları listeleyin. Bu durumda, kapsayıcıya yalnızca bir blob eklenmiş, bu nedenle listeleme işlemi yalnızca bir BLOB döndürüyor.

Bu kodu bloğunun sonuna ekleyin `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Blob’ları indirme

[Download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) yöntemini çağırarak önceden oluşturulmuş blobu indirin. Örnek kod, yerel dosya sisteminde her iki dosyayı da görebilmeniz için dosya adına "ındır" adlı bir sonek ekler.

Bu kodu bloğunun sonuna ekleyin `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Kapsayıcı silme

Aşağıdaki kod, [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) yöntemi kullanılarak kapsayıcının tamamını kaldırarak uygulamanın oluşturduğu kaynakları temizler. Ayrıca, isterseniz yerel dosyaları silebilirsiniz.

Uygulama, `input()` BLOB, kapsayıcı ve yerel dosyaları silmeden önce çağırarak kullanıcı girişi için duraklatılır. Kaynakların silinmeden önce doğru şekilde oluşturulduğunu doğrulayın.

Bu kodu bloğunun sonuna ekleyin `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>Kodu çalıştırma

Bu uygulama yerel klasörünüzde bir sınama dosyası oluşturur ve Azure Blob depolama alanına yükler. Örnek daha sonra kapsayıcıdaki Blobları listeler ve dosyayı yeni bir adla indirir. Eski ve yeni dosyaları karşılaştırabilirsiniz.

*BLOB-QuickStart-v12.py* dosyasını içeren dizine gidin ve `python` uygulamayı çalıştırmak için aşağıdaki komutu yürütün.

```console
python blob-quickstart-v12.py
```

Uygulamanın çıktısı aşağıdaki örneğe benzer:

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Temizleme işlemine başlamadan önce, iki dosya için *veri* klasörünüzü kontrol edin. Bunları açabilir ve aynı olduğunu gözlemleyebilirsiniz.

Dosyaları doğruladıktan sonra, test dosyalarını silmek ve tanıtımı sona almak için **ENTER** tuşuna basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Python kullanarak Blobları karşıya yükleme, indirme ve listeleme hakkında daha fazla öğrendiniz.

BLOB depolama örnek uygulamalarını görmek için devam edin:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 Python örnekleri](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Daha fazla bilgi edinmek için bkz. [Python Için Azure Storage istemci kitaplıkları](/azure/developer/python/sdk/storage/overview).
* Öğreticiler, örnekler, hızlı başlangıçler ve diğer belgelerde, [Python geliştiricileri Için Azure](/azure/python/)' u ziyaret edin.
