---
title: 'Hızlı başlangıç: Azure Blob depolama kitaplığı V12-Python'
description: Bu hızlı başlangıçta, blob (nesne) deposunda bir kapsayıcı ve BLOB oluşturmak için Python için Azure Blob depolama istemci kitaplığı sürüm 12 ' yi nasıl kullanacağınızı öğreneceksiniz. Ardından, blob’u yerel bilgisayarınıza indirmeyi ve bir kapsayıcıdaki tüm blobların listesini görüntülemeyi öğreneceksiniz.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 397464a70164ef06642a991fb8470ddfa2922647
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73809920"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-python"></a>Hızlı başlangıç: Python için Azure Blob depolama istemci kitaplığı V12

Python için Azure Blob depolama istemci kitaplığı V12 ile çalışmaya başlayın. Azure Blob depolama, Microsoft’un buluta yönelik nesne depolama çözümüdür. Paketi yüklemek ve temel görevler için örnek kodu denemek için adımları izleyin. Blob depolama, çok miktarda yapılandırılmamış veriyi depolamak için iyileştirilmiştir.

> [!NOTE]
> Önceki SDK sürümünü kullanmaya başlamak için bkz. [hızlı başlangıç: Python Için Azure Blob depolama istemci kitaplığı](storage-quickstart-blobs-python-legacy.md).

Azure Blob depolama istemci kitaplığını kullanarak şunları yapın:

* Bir kapsayıcı oluşturma
* Azure depolama 'ya blob yükleme
* Bir kapsayıcıdaki tüm Blobları listeleme
* Blobu yerel bilgisayarınıza indirme
* Kapsayıcı silme

[API başvuru belgeleri](/python/api/azure-storage-blob) | [kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [paketi (Python paket dizini)](https://pypi.org/project/azure-storage-blob/) | [örnekleri](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* Azure depolama hesabı- [depolama hesabı oluşturma](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* İşletim sisteminiz için [Python](https://www.python.org/downloads/) -2,7, 3,5 veya üzeri

## <a name="setting-up"></a>Ayarlanıyor

Bu bölümde, bir projeyi Python için Azure Blob Storage istemci kitaplığı V12 ile çalışacak şekilde hazırlama adımları gösterilmektedir.

### <a name="create-the-project"></a>Proje oluşturma

*BLOB-QuickStart-V12*adlı bir Python uygulaması oluşturun.

1. Konsol penceresinde (cmd, PowerShell veya Bash gibi), proje için yeni bir dizin oluşturun.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Yeni oluşturulan *BLOB-hızlı başlangıç-V12* dizinine geçiş yapın.

    ```console
    cd blob-quickstart-v12
    ```

1. *BLOB-QuickStart-V12* dizininde, *veri*adlı başka bir dizin oluşturun. Blob veri dosyalarının oluşturulup depolanacağı yerdir.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Paketi yükler

Hala uygulama dizininde, `pip install` komutunu kullanarak Python için Azure Blob Storage istemci kitaplığı 'nı yükleyebilirsiniz.

```console
pip install azure-storage-blob
```

Bu komut, Python paketi için Azure Blob depolama istemci kitaplığını ve bağımlı olduğu tüm kitaplıkları kurar. Bu durumda, yalnızca Python için Azure çekirdek kitaplığı vardır.

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Proje dizininden:

1. Kod Düzenleyicinizde yeni bir metin dosyası açın
1. `import` deyimleri Ekle
1. Çok temel özel durum işleme dahil olmak üzere programın yapısını oluşturma

    Kod şu şekildedir:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. *BLOB-QuickStart-V12* dizinine yeni dosyayı *BLOB-QuickStart-v12.py* olarak kaydedin.

### <a name="copy-your-credentials-from-the-azure-portal"></a>Azure portalından kimlik bilgilerinizi kopyalama

Örnek uygulama, Azure depolama 'ya istek yaptığında yetkilendirilmiş olmalıdır. Bir isteği yetkilendirmek için, depolama hesabı kimlik bilgilerinizi uygulamaya bağlantı dizesi olarak ekleyin. Bu adımları izleyerek depolama hesabı kimlik bilgilerinizi görüntüleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Depolama hesabınızı bulun.
3. Depolama hesabına genel bakışın **Ayarlar** bölümünde **Erişim anahtarları**’nı seçin. Burada, hesap erişim anahtarlarınızı ve her anahtar için tam bağlantı dizesini görüntüleyebilirsiniz.
4. **key1** bölümünde **Bağlantı dizesi** değerini bulun ve **Kopyala** düğmesini seçerek bağlantı dizesini kopyalayın. Sonraki adımda bir ortam değişkenine bağlantı dizesini ekleyeceksiniz.

    ![Azure portalından bağlantı dizesinin kopyalanmasını gösteren ekran görüntüsü](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma

Bağlantı dizenizi kopyaladıktan sonra uygulamayı çalıştıran yerel makine üzerindeki yeni bir ortam değişkenine yazın. Ortam değişkenini ayarlamak için bir konsol penceresi açın ve işletim sisteminizin yönergelerini izleyin. `<yourconnectionstring>` gerçek bağlantı dizeniz ile değiştirin.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Windows 'a ortam değişkenini ekledikten sonra, komut penceresinin yeni bir örneğini başlatmanız gerekir.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Programları yeniden Başlat

Ortam değişkenini ekledikten sonra, ortam değişkenini okumak için gereken tüm çalışan programları yeniden başlatın. Örneğin, devam etmeden önce geliştirme ortamınızı veya düzenleyiciyi yeniden başlatın.

## <a name="object-model"></a>Nesne modeli

Azure Blob depolama, büyük miktarlarda yapılandırılmamış verileri depolamak için iyileştirilmiştir. Yapılandırılmamış veriler, metin veya ikili veriler gibi belirli bir veri modeline veya tanımına bağlı olmayan bir veri. BLOB depolama üç tür kaynak sunar:

* Depolama hesabı
* Depolama hesabındaki bir kapsayıcı
* Kapsayıcıda bir blob

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![BLOB depolama mimarisi diyagramı](./media/storage-blob-introduction/blob1.png)

Şu kaynaklarla etkileşim kurmak için aşağıdaki Python sınıflarını kullanın:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): `BlobServiceClient` sınıfı, Azure depolama kaynaklarını ve BLOB kapsayıcılarını değiştirmenize izin verir.
* [Containerclient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): `ContainerClient` sınıfı, Azure depolama kapsayıcıları ve bloblarını değiştirmenize olanak sağlar.
* [Blobclient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): `BlobClient` sınıfı, Azure depolama bloblarını değiştirmenize izin verir.

## <a name="code-examples"></a>Kod örnekleri

Bu örnek kod parçacıkları, Python için Azure Blob depolama istemci kitaplığı ile aşağıdakilerin nasıl gerçekleştirileceğini göstermektedir:

* [Bağlantı dizesini al](#get-the-connection-string)
* [Kapsayıcı oluşturma](#create-a-container)
* [Blobları bir kapsayıcıya yükleme](#upload-blobs-to-a-container)
* [Bir kapsayıcıdaki Blobları listeleme](#list-the-blobs-in-a-container)
* [Blob 'ları indir](#download-blobs)
* [Kapsayıcı silme](#delete-a-container)

### <a name="get-the-connection-string"></a>Bağlantı dizesini alma

Aşağıdaki kod, depolama [bağlantı dizesini yapılandırma](#configure-your-storage-connection-string) bölümünde oluşturulan ortam değişkeninden depolama hesabının bağlantı dizesini alır.

Bu kodu `try` bloğunun içine ekleyin:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called CONNECT_STR. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('CONNECT_STR')
```

### <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Yeni kapsayıcı için bir ad belirleyin. Aşağıdaki kod, benzersiz olduğundan emin olmak için kapsayıcı adına bir UUID değeri ekler.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcıları ve blobları adlandırma hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

[From_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) yöntemini çağırarak [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) sınıfının bir örneğini oluşturun. Ardından, depolama hesabınızda kapsayıcıyı gerçekten oluşturmak için [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) yöntemini çağırın.

`try` bloğunun sonuna bu kodu ekleyin:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Blobları bir kapsayıcıya yükleme

Aşağıdaki kod parçacığı:

1. Yerel dizinde bir metin dosyası oluşturur.
1. [Kapsayıcı oluşturma](#create-a-container) bölümünde [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) üzerinde [Get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) yöntemini çağırarak bir [blobclient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) nesnesine bir başvuru alır.
1. [Upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) yöntemini çağırarak yerel metin dosyasını bloba yükler.

`try` bloğunun sonuna bu kodu ekleyin:

```python
# Create a file in local Documents directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

[List_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) yöntemini çağırarak kapsayıcıdaki Blobları listeleyin. Bu durumda, kapsayıcıya yalnızca bir blob eklenmiş, bu nedenle listeleme işlemi yalnızca bir BLOB döndürüyor.

`try` bloğunun sonuna bu kodu ekleyin:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Blob’ları indirme

[Download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) yöntemini çağırarak önceden oluşturulmuş blobu indirin. Örnek kod, yerel dosya sisteminde her iki dosyayı da görebilmeniz için dosya adına "ındır" adlı bir sonek ekler.

`try` bloğunun sonuna bu kodu ekleyin:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in Documents
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Kapsayıcı silme

Aşağıdaki kod, [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) yöntemi kullanılarak kapsayıcının tamamını kaldırarak uygulamanın oluşturduğu kaynakları temizler. Ayrıca, isterseniz yerel dosyaları silebilirsiniz.

Uygulama, blob, kapsayıcı ve yerel dosyaları silmeden önce `input()` çağırarak kullanıcı girişi için duraklatılır. Bu, kaynakların silinmeden önce gerçekten doğru şekilde oluşturulduğunu doğrulamak iyi bir şansınız olur.

`try` bloğunun sonuna bu kodu ekleyin:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>Kodu çalıştırma

Bu uygulama yerel klasörünüzde bir sınama dosyası oluşturur ve BLOB depolamaya yükler. Örnek daha sonra kapsayıcıdaki Blobları listeler ve eski ve yeni dosyaları karşılaştırabilmeniz için dosyayı yeni bir adla indirir.

*BLOB-QuickStart-v12.py* dosyasını içeren dizine gidin, ardından uygulamayı çalıştırmak için aşağıdaki `python` komutunu yürütün.

```console
python blob-quickstart-v12.py
```

Uygulamanın çıktısı aşağıdaki örneğe benzer:

```output
Azure Blob storage v12 - Python quickstart sample

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

Temizleme işlemine başlamadan önce, iki dosya için *Belgeler* klasörünüzü denetleyin. Dosyaları açarak aynı olduklarını görebilirsiniz.

Dosyaları doğruladıktan sonra, test dosyalarını silmek ve tanıtımı sona almak için **ENTER** tuşuna basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Python kullanarak Blobları karşıya yükleme, indirme ve listeleme hakkında daha fazla öğrendiniz.

BLOB depolama örnek uygulamalarını görmek için devam edin:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 Python örnekleri](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Daha fazla bilgi için bkz. [Python Için Azure SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md).
* Öğreticiler, örnekler, hızlı ve diğer belgelerde, [Python geliştiricileri Için Azure](/azure/python/)' u ziyaret edin.
