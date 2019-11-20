---
title: Azure 'dan C++ nesne (blob) depolaması kullanma | Microsoft Docs
description: Yapılandırılmamış verileri bulutta Azure Blob (nesne) depolaması ile depolayın.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/21/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: f64856d5b0140d529e939d92d4be61b074928d6e
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726421"
---
# <a name="how-to-use-blob-storage-from-c"></a>Öğesinden blob depolamayı kullanmaC++

Bu kılavuzda, Azure Blob depolamayı kullanarak yaygın senaryoların nasıl gerçekleştirileceği gösterilmektedir. Örneklerde Blobları karşıya yükleme, listeleme, indirme ve silme işlemlerinin nasıl yapılacağı gösterilmektedir. Örnekler C++ dilinde yazılmıştır ve [C++ için Azure Depolama İstemci Kitaplığı](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)’nı kullanır.   

BLOB depolama hakkında daha fazla bilgi için bkz. [Azure Blob depolamaya giriş](storage-blobs-introduction.md).

> [!NOTE]
> Bu kılavuz C++ için Azure Depolama İstemci Kitaplığı sürüm 1.0.0 ve üzerini hedefler. Microsoft, C++ [NuGet](https://www.nuget.org/packages/wastorage) veya [GitHub](https://github.com/Azure/azure-storage-cpp)aracılığıyla kullanılabilen için depolama istemci kitaplığı 'nın en son sürümünü kullanmanızı önerir.

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++ uygulaması oluşturma
Bu kılavuzda, bir C++ uygulama içinde çalıştırılabilen depolama özelliklerini kullanacaksınız.  

Bunu yapmak için, C++ için Azure Depolama İstemci Kitaplığı’nı yüklemeniz ve Azure aboneliğinizde bir Azure depolama hesabı oluşturmanız gerekir.   

C++ için Azure Depolama İstemci Kitaplığı’nı aşağıdaki yöntemleri kullanarak yükleyebilirsiniz:

* **'Un** [Benioku Için C++ Azure Storage istemci kitaplığı](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) sayfasında verilen yönergeleri izleyin.  
* **Windows:** Visual Studio'da, **Araçlar > NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu**’na tıklayın. [NuGet Paket Yöneticisi konsoluna](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) aşağıdaki komutu yazın ve **ENTER**tuşuna basın.  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Uygulamanızı blob depolamaya erişecek şekilde yapılandırma
Bloblara erişmek için Azure depolama API 'Lerini kullanmak istediğiniz C++ dosyanın en üstüne aşağıdaki Include deyimlerini ekleyin:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Azure depolama bağlantı dizesi ayarlama
Azure depolama istemcisi, veri yönetimi hizmetlerine erişmek üzere uç noktaları ve kimlik bilgilerini depolamak için bir depolama bağlantı dizesi kullanır. Bir istemci uygulamasında çalışırken, depolama hesabınızın adını ve *AccountName* Için [Azure portalında](https://portal.azure.com) listelenen depolama hesabı için depolama erişim anahtarını kullanarak depolama bağlantı dizesini aşağıdaki biçimde sağlamanız gerekir ve *Accountkey* değerleri. Depolama hesapları ve erişim anahtarları hakkında daha fazla bilgi için bkz. [Azure depolama hesapları hakkında](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Bu örnekte bağlantı dizesini tutmak için nasıl statik bir alan bildirebileceğiniz gösterilmektedir:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Uygulamanızı yerel Windows bilgisayarınızda test etmek için [Azure SDK](https://azure.microsoft.com/downloads/)ile birlikte yüklenen Microsoft Azure [depolama öykünücüsünü](../storage-use-emulator.md) kullanabilirsiniz. Depolama öykünücüsü, yerel geliştirme makinenizde Azure 'da bulunan BLOB, kuyruk ve tablo hizmetlerini taklit eden bir yardımcı programdır. Aşağıdaki örnekte bağlantı dizesini yerel depolama öykünücünüzde tutmak için nasıl statik bir alan bildirebileceğiniz gösterilmektedir:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Azure Storage öykünücüsü 'nü başlatmak için **Başlat** düğmesini seçin veya **Windows** tuşuna basın. **Azure Storage öykünücüsü**yazmaya başlayın ve uygulamalar listesinden **Microsoft Azure depolama öykünücüsü** ' yi seçin.  

Aşağıdaki örnekler, depolama bağlantı dizesini almak için bu iki yöntemden birini kullandığınızı varsayar.  

## <a name="retrieve-your-storage-account"></a>Depolama hesabınızı alma
Depolama hesabı bilgilerinizi temsil etmek için **cloud_storage_account** sınıfını kullanabilirsiniz. Depolama bağlantı dizesinden depolama hesabı bilgilerini almak için **parse** yöntemini kullanabilirsiniz.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Sonra, BLOB depolama alanında depolanan kapsayıcıları ve Blobları temsil eden nesneleri almanızı sağlayan bir **cloud_blob_client** sınıfına bir başvuru alın. Aşağıdaki kod, yukarıda elde ettiğimiz depolama hesabı nesnesini kullanarak bir **cloud_blob_client** nesnesi oluşturur:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Nasıl yapılır: Bir kapsayıcı oluşturma
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Bu örnek, zaten yoksa, nasıl bir kapsayıcı oluşturulacağını gösterir:  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Varsayılan olarak, yeni kapsayıcı özeldir ve bu kapsayıcıdan blob 'ları indirmek için depolama erişim anahtarınızı belirtmeniz gerekir. Kapsayıcı içindeki dosyaları (Blobları) herkese açık hale getirmek istiyorsanız, kapsayıcıyı aşağıdaki kodu kullanarak genel olacak şekilde ayarlayabilirsiniz:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Internet 'teki herkes blob 'ları ortak bir kapsayıcıda görebilir, ancak yalnızca uygun erişim anahtarınız varsa bunları değiştirebilir veya silebilirsiniz.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Nasıl yapılır: Bir kapsayıcıya bir blob yükleme
Azure Blob depolama, blok bloblarını ve sayfa bloblarını destekler. Çoğu durumda kullanılması önerilen blob türü blok blobudur.  

Bir dosyayı bir blok blobuna yüklemek için bir kapsayıcı başvurusu alın ve blok blob başvurusu almak için kullanın. Blob başvurunuz olduktan sonra, **upload_from_stream** yöntemini çağırarak herhangi bir veri akışını bu akışa yükleyebilirsiniz. Bu işlemle, eğer önceden oluşturulmadıysa bir blob oluşturulacaktır, aksi takdirde üzerine yazılacaktır. Aşağıdaki örnek kapsayıcının önceden oluşturulduğunu varsayarak bir blobun bir kapsayıcıya nasıl yükleneceğini gösterir.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Alternatif olarak, bir dosyayı blok blobuna yüklemek için **upload_from_file** yöntemini kullanabilirsiniz.

## <a name="how-to-list-the-blobs-in-a-container"></a>Nasıl yapılır: Blob’ları bir kapsayıcıda listeleme
Blob’ları bir kapsayıcıda listelemek için ilk olarak bir kapsayıcı başvurusu edinin. Daha sonra kapsayıcının **list_blobs** yöntemini kullanarak Blobları ve/veya dizinleri elde edebilirsiniz. Döndürülen bir **list_blob_item**için zengin özellik ve Yöntem kümesine erişmek için, bir **cloud_blob** nesnesi almak üzere **list_blob_item. as _blob** yöntemini veya bir cloud_blob_ almak için **list_blob. as _directory** metodunu çağırmanız gerekir Dizin nesnesi. Aşağıdaki kod, **My-Sample-Container** kapsayıcısındaki her BIR öğenin URI 'sini nasıl alacağınızı ve çıktısını gösterir:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Listeleme işlemleri hakkında daha fazla bilgi için bkz. [Azure depolama kaynaklarını C++listeleme ](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Nasıl yapılır: Blob’ları indirme
Blob 'ları indirmek için önce bir blob başvurusu alın ve ardından **download_to_stream** yöntemini çağırın. Aşağıdaki örnek, blob içeriğini bir Stream nesnesine aktarmak için **download_to_stream** yöntemini kullanır ve böylece yerel bir dosyaya devam edebilirsiniz.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Alternatif olarak, bir Blobun içeriğini bir dosyaya indirmek için **download_to_file** yöntemini kullanabilirsiniz.
Ayrıca, bir Blobun içeriğini bir metin dizesi olarak indirmek için **download_text** yöntemini de kullanabilirsiniz.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Nasıl yapılır: Blob’ları silme
Bir blobu silmek için önce bir blob başvurusu alın ve ardından **delete_blob** yöntemini çağırın.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Sonraki adımlar
BLOB depolama hakkında temel bilgileri öğrendiğinize göre, Azure depolama hakkında daha fazla bilgi edinmek için bu bağlantıları izleyin.  

* [Kuyruk depolamayı kullanmaC++](../storage-c-plus-plus-how-to-use-queues.md)
* [Öğesinden Tablo Depolamayı kullanmaC++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Azure depolama kaynaklarını listelemeC++](../storage-c-plus-plus-enumeration.md)
* [Başvuru için C++ depolama istemci kitaplığı](https://azure.github.io/azure-storage-cpp)
* [Azure Depolama Belgeleri](https://azure.microsoft.com/documentation/services/storage/)
* [AzCopy komut satırı yardımcı programı ile veri aktarımı](../storage-use-azcopy.md)

