---
title: 'Hızlı başlangıç: Azure Blob depolama kitaplığı V12-C++'
description: Bu hızlı başlangıçta, blob (nesne) deposunda bir kapsayıcı ve BLOB oluşturmak için C++ için Azure Blob depolama istemci kitaplığı sürüm 12 ' nin nasıl kullanılacağını öğrenirsiniz. Ardından, blob’u yerel bilgisayarınıza indirmeyi ve bir kapsayıcıdaki tüm blobların listesini görüntülemeyi öğreneceksiniz.
author: twooley
ms.author: twooley
ms.date: 10/21/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: e6832cc835a464a2a3d17d5ed286e71b17bc980e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312289"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>Hızlı başlangıç: C++ için Azure Blob depolama istemci kitaplığı V12

C++ için Azure Blob depolama istemci kitaplığı V12 ile çalışmaya başlayın. Azure Blob depolama, Microsoft 'un bulut için nesne depolama çözümüdür. Paketi yüklemek ve temel görevler için örnek kodu denemek için adımları izleyin. BLOB depolama, büyük miktarlarda yapılandırılmamış verileri depolamak için iyileştirilmiştir.

C++ için Azure Blob depolama istemci kitaplığı V12 ' nı kullanarak şunları yapın:

- Kapsayıcı oluşturma
- Azure Depolama'ya blob yükleme
- Bir kapsayıcıdaki tüm Blobları listeleme
- Blobu yerel bilgisayarınıza indirme
- Kapsayıcı silme

Kaynaklar:

- [API başvuru belgeleri](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [Örnekler](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

- [Azure aboneliği](https://azure.microsoft.com/free/)
- [Azure depolama hesabı](../common/storage-account-create.md)
- [C++ derleyicisi](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg-C ve C++ Paket Yöneticisi](https://github.com/microsoft/vcpkg/blob/master/docs/README.md)
- [Libkıvrık](https://curl.haxx.se/libcurl/)
- [LibXML2](http://www.xmlsoft.org/)

## <a name="setting-up"></a>Ayarlanıyor

Bu bölüm, C++ için Azure Blob depolama istemci kitaplığı V12 ile çalışmak üzere bir proje hazırlama konusunda size yol gösterir.

### <a name="install-the-packages"></a>Paketleri yükler

Henüz yapmadıysanız, komutunu kullanarak Libkıvrık ve LibXML2 paketlerini yükleyemezsiniz `vcpkg install` .

```console
vcpkg.exe install libxml2:x64-windows curl:x64-windows
```

[C++ Için Azure SDK 'sını](https://github.com/Azure/azure-sdk-for-cpp/)edinmek ve derlemek için GitHub 'daki yönergeleri izleyin.

### <a name="create-the-project"></a>Proje oluşturma

Visual Studio 'da, *BlobQuickstartV12* adlı Windows için yeni bir C++ konsol uygulaması oluşturun.

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="Yeni bir C++ Windows konsol uygulamasını yapılandırmak için Visual Studio iletişim kutusu":::

Aşağıdaki kitaplıkları projeye ekleyin:

- libkıvrık. lib
- libxml2. lib
- bcrypt. lib
- Crypt32. lib
- WS2_32. lib
- Azure-Core. lib
- Azure-Storage-Common. lib
- Azure-Storage-Blobs. lib

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Nesne modeli

Azure Blob depolama, büyük miktarlarda yapılandırılmamış verileri depolamak için iyileştirilmiştir. Yapılandırılmamış veriler, metin veya ikili veriler gibi belirli bir veri modeline veya tanımına bağlı olmayan bir veri. BLOB depolama üç tür kaynak sunar:

- Depolama hesabı
- Depolama hesabındaki bir kapsayıcı
- Kapsayıcıda bir blob

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![BLOB depolama mimarisi diyagramı](./media/storage-blobs-introduction/blob1.png)

Aşağıdaki C++ sınıflarını kullanarak bu kaynaklarla etkileşim kurun:

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html): `BlobServiceClient` sınıfı, Azure depolama kaynaklarını ve BLOB kapsayıcılarını değiştirmenize olanak sağlar.
- [Blobcontainerclient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html): `BlobContainerClient` sınıfı, Azure depolama kapsayıcılarını ve bloblarını değiştirmenize olanak sağlar.
- [Blobclient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html): `BlobClient` sınıfı, Azure Storage bloblarını değiştirmenize izin verir. Bu, tüm özel blob sınıfları için temel sınıftır.
- [Blockblobclient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html): `BlockBlobClient` sınıfı, Azure Storage blok bloblarını değiştirmenize izin verir.

## <a name="code-examples"></a>Kod örnekleri

Bu örnek kod parçacıkları, C++ için Azure Blob depolama istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

- [İçerme dosyaları Ekle](#add-include-files)
- [Bağlantı dizesini alma](#get-the-connection-string)
- [Kapsayıcı oluşturma](#create-a-container)
- [Blobları bir kapsayıcıya yükleme](#upload-blobs-to-a-container)
- [Kapsayıcıdaki blobları listeleme](#list-the-blobs-in-a-container)
- [Blob’ları indirme](#download-blobs)
- [Kapsayıcı silme](#delete-a-container)

### <a name="add-include-files"></a>İçerme dosyaları Ekle

Proje dizininden:

1. *BlobQuickstartV12. sln* çözüm dosyasını Visual Studio 'da açın
1. Visual Studio içinde *BlobQuickstartV12. cpp* kaynak dosyasını açın
1. Otomatik olarak oluşturulan içindeki tüm kodları kaldırın `main`
1. `#include`Deyim Ekle

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_Includes":::

### <a name="get-the-connection-string"></a>Bağlantı dizesini alma

Aşağıdaki kod, depolama hesabınız için bağlantı dizesini [depolama Bağlantı dizenizi yapılandırma](#configure-your-storage-connection-string)bölümünde oluşturulan ortam değişkeninden alır.

Bu kodu içine ekleyin `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Kapsayıcı oluşturma

[Createfromconnectionstring](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ad103d1e3a7ce7c53a82da887d12ce6fe) Işlevini çağırarak [blobcontainerclient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) sınıfının bir örneğini oluşturun. Ardından, depolama hesabınızda gerçek kapsayıcıyı oluşturmak için [Oluştur](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a22a1e001068a4ec52bb1b6bd8b52c047) ' u çağırın.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcıları ve blobları adlandırma hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Bu kodu sonuna ekleyin `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Blobları bir kapsayıcıya yükleme

Aşağıdaki kod parçacığı:

1. "Hello Azure!" içeren bir dize bildirir.
1. Kapsayıcı [oluşturma](#create-a-container) bölümünde kapsayıcıda [getblockblobclient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f) çağırarak bir [blockblobclient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) nesnesine bir başvuru alır.
1. [Uploadfrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d) işlevini çağırarak dizeyi blob 'a yükler. Bu işlev, zaten yoksa blobu oluşturur veya varsa günceller.

Bu kodu sonuna ekleyin `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>Kapsayıcıdaki blobları listeleme

[Listblobsyataysegment](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ac7712bc46909dc061d6bf554b496550c) işlevini çağırarak kapsayıcıdaki Blobları listeleyin. Kapsayıcıya yalnızca bir blob eklenmiş, bu nedenle işlem yalnızca o blobu döndürüyor.

Bu kodu sonuna ekleyin `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Blob’ları indirme

Karşıya yüklenen Blobun özelliklerini alın. Ardından, `std::string` karşıya yüklenen Blobun özelliklerini kullanarak yeni bir nesne bildirin ve yeniden boyutlandırın. `std::string` [Blobclient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html) temel sınıfında [downloadto](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa844f37a8c216f3cb0f27912b114c4d2) işlevini çağırarak, önceden oluşturulmuş blobu yeni nesneye indirin. Son olarak, indirilen blob verilerini görüntüleyin.

Bu kodu sonuna ekleyin `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>Blob silme

Aşağıdaki kod, [Blobclient. Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#a621eabcc8d23893ca1eb106494198615) işlevini çağırarak blob 'U Azure Blob depolama kapsayıcısından siler.

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>Kapsayıcı silme

Aşağıdaki kod, [Blobcontainerclient. Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#aa6b1db52697ae92e9a1227e2e02a5178)kullanarak kapsayıcının tamamını silerek uygulamanın oluşturduğu kaynakları temizler.

Bu kodu sonuna ekleyin `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Kodu çalıştırma

Bu uygulama bir kapsayıcı oluşturur ve bir metin dosyasını Azure Blob depolama alanına yükler. Örnek, kapsayıcıdaki Blobları listeler, dosyayı indirir ve dosya içeriğini görüntüler. Son olarak, uygulama blobu ve kapsayıcıyı siler.

Uygulamanın çıktısı aşağıdaki örneğe benzer:

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, C++ kullanarak Blobları karşıya yükleme, indirme ve listeleme hakkında daha fazla öğrendiniz. Ayrıca, bir Azure Blob depolama kapsayıcısı oluşturmayı ve silmeyi de öğrendiniz.

C++ BLOB depolama örneğini görmek için devam edin:

> [!div class="nextstepaction"]
> [C++ için Azure Blob Storage SDK V12 örneği](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)
