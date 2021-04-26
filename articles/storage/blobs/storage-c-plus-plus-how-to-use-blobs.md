---
title: C++-Azure ' t e nesne (blob) depolaması kullanma | Microsoft Docs
description: C++ kullanarak, Azure Blob (nesne) depolama ile yapılandırılmamış verileri (blob 'lar) bulutta nasıl depolayacağınızı öğrenin.
author: twooley
ms.author: twooley
ms.date: 07/16/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 61c7dca25e90450c695a5137dd2ee900c1282bf0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278057"
---
# <a name="how-to-use-blob-storage-from-c"></a>C++ ' dan blob depolamayı kullanma

Bu kılavuzda, Azure Blob depolamayı kullanarak yaygın senaryoların nasıl gerçekleştirileceği gösterilmektedir. Örneklerde Blobları karşıya yükleme, listeleme, indirme ve silme işlemlerinin nasıl yapılacağı gösterilmektedir. Örnekler C++ dilinde yazılmıştır ve [C++ için Azure Depolama İstemci Kitaplığı](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)’nı kullanır.
BLOB depolama hakkında daha fazla bilgi için bkz. [Azure Blob depolamaya giriş](storage-blobs-introduction.md).

> [!NOTE]
> Bu kılavuz C++ için Azure Depolama İstemci Kitaplığı sürüm 1.0.0 ve üzerini hedefler. Microsoft, [NuGet](https://www.nuget.org/packages/wastorage) veya [GitHub](https://github.com/Azure/azure-storage-cpp)aracılığıyla kullanılabilen C++ için depolama istemcisi kitaplığı 'nın en son sürümünü kullanmanızı önerir.

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++ uygulaması oluşturma
Bu kılavuzda, bir C++ uygulaması içinde çalıştırılabilen depolama özelliklerini kullanacaksınız.

Bunu yapmak için, C++ için Azure Depolama İstemci Kitaplığı’nı yüklemeniz ve Azure aboneliğinizde bir Azure depolama hesabı oluşturmanız gerekir.

C++ için Azure Depolama İstemci Kitaplığı’nı aşağıdaki yöntemleri kullanarak yükleyebilirsiniz:

- **Linux:** [C++ Için Azure depolama Istemci KITAPLıĞı Benioku dosyasında verilen yönergeleri izleyin: Linux üzerinde çalışmaya](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) başlama sayfası.
- **Windows:** Windows üzerinde, Dependency Manager olarak [vcpkg](https://github.com/microsoft/vcpkg) kullanın. Vcpkg 'yi başlatmak için [hızlı](https://github.com/microsoft/vcpkg#quick-start) başlangıcı izleyin. Ardından, kitaplığı yüklemek için aşağıdaki komutu kullanın:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Kaynak kodu oluşturma ve [Benioku](https://github.com/Azure/azure-storage-cpp#download--install) dosyasında NuGet 'e aktarma hakkında bir kılavuz bulabilirsiniz.

## <a name="configure-your-application-to-access-blob-storage"></a>Uygulamanızı blob depolamaya erişecek şekilde yapılandırma
Bloblara erişmek için Azure depolama API 'Lerini kullanmak istediğiniz C++ dosyasının en üstüne aşağıdaki Include deyimlerini ekleyin:

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Azure depolama bağlantı dizesi ayarlama
Azure depolama istemcisi, veri yönetimi hizmetlerine erişmek üzere uç noktaları ve kimlik bilgilerini depolamak için bir depolama bağlantı dizesi kullanır. Bir istemci uygulamasında çalışırken, depolama hesabınızın adını ve *AccountName* ve *accountkey* değerleri için [Azure Portal](https://portal.azure.com) listelenen depolama hesabı için depolama erişim anahtarını kullanarak depolama bağlantı dizesini aşağıdaki biçimde sağlamanız gerekir. Depolama hesapları ve erişim anahtarları hakkında daha fazla bilgi için bkz. [Azure depolama hesapları hakkında](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Bu örnekte bağlantı dizesini tutmak için nasıl statik bir alan bildirebileceğiniz gösterilmektedir:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Uygulamanızı yerel Windows bilgisayarınızda test etmek için [Azurite depolama öykünücüsünü](../common/storage-use-azurite.md)kullanabilirsiniz. Azurite, yerel geliştirme makinenizde Azure 'da kullanılabilen blob ve sıra Hizmetleri benzetimi yapan bir yardımcı programdır. Aşağıdaki örnekte bağlantı dizesini yerel depolama öykünücünüzde tutmak için nasıl statik bir alan bildirebileceğiniz gösterilmektedir:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));
```

Azurite 'yi başlatmak için bkz. [Yerel Azure depolama geliştirmesi Için Azurite öykünücüsünü kullanma](../common/storage-use-azurite.md).

Aşağıdaki örnekler, depolama bağlantı dizesini almak için bu iki yöntemden birini kullandığınızı varsayar.

## <a name="retrieve-your-storage-account"></a>Depolama hesabınızı alma
Depolama hesabı bilgilerinizi temsil etmek için **cloud_storage_account** sınıfını kullanabilirsiniz. Depolama bağlantı dizesinden depolama hesabı bilgilerini almak için **parse** yöntemini kullanabilirsiniz.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Sonra, BLOB depolama alanında depolanan kapsayıcıları ve Blobları temsil eden nesneleri almanızı sağlayan **cloud_blob_client** sınıfına bir başvuru alın. Aşağıdaki kod, yukarıda elde ettiğimiz depolama hesabı nesnesini kullanarak bir **cloud_blob_client** nesnesi oluşturur:

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Nasıl yapılır: kapsayıcı oluşturma
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

## <a name="how-to-upload-a-blob-into-a-container"></a>Nasıl yapılır: bir blobu kapsayıcıya yükleme
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

## <a name="how-to-list-the-blobs-in-a-container"></a>Nasıl yapılır: bir kapsayıcıdaki Blobları listeleme
Blob’ları bir kapsayıcıda listelemek için ilk olarak bir kapsayıcı başvurusu edinin. Daha sonra kapsayıcının **list_blobs** yöntemini kullanarak Blobları ve/veya dizinleri elde edebilirsiniz. Döndürülen bir **list_blob_item** için zengin özellik ve Yöntem kümesine erişmek için, bir **cloud_blob** nesnesi almak üzere **list_blob_item. as _blob** yöntemini veya bir cloud_blob_directory nesnesi almak için **list_blob. as _directory** yöntemini çağırmanız gerekir. Aşağıdaki kod, **My-Sample-Container** kapsayıcısındaki her BIR öğenin URI 'sini nasıl alacağınızı ve çıktısını gösterir:

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

Listeleme işlemleri hakkında daha fazla bilgi için bkz. [C++ ' da Azure depolama kaynaklarını listeleme](../common/storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Nasıl yapılır: Blobları Indirme
Blob 'ları indirmek için önce bir blob başvurusu alın ve ardından **download_to_stream** yöntemi çağırın. Aşağıdaki örnek, blob içeriğini bir akış nesnesine aktarmak için **download_to_stream** yöntemini kullanır ve böylece yerel bir dosyaya devam edebilirsiniz.

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

## <a name="how-to-delete-blobs"></a>Nasıl yapılır: Blobları silme
Bir blobu silmek için önce bir blob başvurusu alın ve ardından **delete_blob** yöntemi çağırın.

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

- [C++’tan Kuyruk Depolama kullanma](../queues/storage-c-plus-plus-how-to-use-queues.md)
- [C++ ' dan Tablo Depolamayı kullanma](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [C++ ' da Azure depolama kaynaklarını listeleme](../common/storage-c-plus-plus-enumeration.md)
- [C++ için depolama Istemci kitaplığı başvurusu](https://azure.github.io/azure-storage-cpp)
- [Azure depolama belgeleri](https://azure.microsoft.com/documentation/services/storage/)
- [AzCopy komut satırı yardımcı programı ile veri aktarımı](../common/storage-use-azcopy-v10.md)