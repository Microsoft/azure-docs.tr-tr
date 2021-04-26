---
title: 'Hızlı başlangıç: Azure Blob depolama istemci kitaplığı-Ruby'
description: Azure Blob depolamada bir depolama hesabı ve kapsayıcı oluşturun. Bir blob oluşturmak, blob indirmek ve bir kapsayıcıdaki Blobları listelemek için Ruby için depolama istemcisi kitaplığı 'nı kullanın.
author: twooley
ms.author: twooley
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 96b47afb11a0105e8f6d6b58e8862994493389f4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277986"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>Hızlı başlangıç: Ruby için Azure Blob depolama istemci kitaplığı

Ruby kullanarak Microsoft Azure Blob depolamada bulunan bir kapsayıcıda Blobları oluşturma, indirme ve listeleme hakkında bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Aşağıdaki ek önkoşulların yüklü olduğundan emin olun:

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Rubygeı paketini](https://rubygems.org/gems/azure-storage-blob)kullanarak [Ruby için Azure depolama kitaplığı](https://github.com/azure/azure-storage-ruby):

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>Örnek uygulamayı indirin:

Bu [hızlı başlangıçta](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) kullanılan örnek uygulama, temel bir Ruby uygulamasıdır.

Uygulamanın bir kopyasını geliştirme ortamınıza indirmek için [Git](https://git-scm.com/) ' i kullanın. Bu komut, depoyu yerel makinenize kopyalar:

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

*Storage-Blobları-Ruby-QuickStart* klasörüne gidin ve kod Düzenleyicinizde *example. RB* dosyasını açın.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma

Uygulamanız için bir [Blobservice](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) örneği oluşturmak üzere depolama hesabı adınızı ve hesap anahtarınızı girin.

*Örnek. RB* dosyasının aşağıdaki kodu yeni bir [blobservice](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) nesnesi oluşturur. *accountname* ve *accountkey* değerlerini, hesap adınız ve anahtarınızla değiştirin.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>Örneği çalıştırma

Örnek, blob depolamada bir kapsayıcı oluşturur, kapsayıcıda yeni bir blob oluşturur, kapsayıcıdaki Blobları listeler ve blobu yerel bir dosyaya indirir.

Örnek uygulamayı çalıştırın. Uygulamayı çalıştırmanın çıkışının bir örneği aşağıda verilmiştir:

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Devam etmek için ENTER tuşuna bastığınızda, örnek program depolama kapsayıcısını ve yerel dosyayı siler. Devam etmeden önce, indirilen dosya için *Belgeler* klasörünüzü kontrol edin.

Depolama hesabınızdaki dosyaları görüntülemek için [Azure Depolama Gezgini](https://storageexplorer.com) de kullanabilirsiniz. Azure Depolama Gezgini, depolama hesabı bilgilerinize erişmenize olanak tanıyan ücretsiz ve platformlar arası bir araçtır.

Dosyaları doğruladıktan sonra, test dosyalarını silmek ve tanıtımı sonlandırmak için ENTER tuşuna basın. Koda bakmak için *example. RB* dosyasını açın.

## <a name="understand-the-sample-code"></a>Örnek kodu anlama

Daha sonra, nasıl çalıştığını anlayabilmeniz için örnek kodda geziniriz.

### <a name="get-references-to-the-storage-objects"></a>Depolama nesneleriyle ilgili başvuruları alma

Yapılacak ilk şey, blob depolamaya erişmek ve bunları yönetmek için kullanılan nesnelerin örneklerini oluşturmaktır. Bu nesneler birbirlerinin üzerinde oluşturulur. Her bir listedeki bir sonraki nesne tarafından kullanılır.

- Azure depolama örneğini oluşturmak [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) nesne bağlantı kimlik bilgilerini ayarlayın.
- Erişmekte olduğunuz kapsayıcıyı temsil eden [kapsayıcı](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container) nesnesini oluşturun. Kapsayıcılar, tıpkı bilgisayarınızdaki dosyaları düzenlemek için klasörleri kullandığınız gibi blobları düzenlemek için kullanılır.

Kapsayıcı nesnesine sahip olduktan sonra, ilgilendiğiniz belirli bir bloba işaret eden bir [blok](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) blobu nesnesi oluşturabilirsiniz. Blob oluşturmak, indirmek ve kopyalamak için [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) nesnesini kullanın.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcı ve BLOB adları hakkında daha fazla bilgi için bkz. [kapsayıcıları, Blobları ve meta verileri adlandırma ve başvuru](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Aşağıdaki örnek kod:

- Yeni bir kapsayıcı oluşturur
- Blobların ortak olması için kapsayıcıda izinleri ayarlar. Kapsayıcıya, benzersiz bir ID eklenmiş olan *quickstartblob* adı verilir.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>Kapsayıcıda blob oluşturma

Blob Storage blok Blobları, ekleme Blobları ve sayfa bloblarını destekler. Blob oluşturmak için, blob verilerini geçirerek [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) yöntemi çağırın.

Aşağıdaki örnek, daha önce oluşturulan kapsayıcıda benzersiz bir KIMLIĞE ve *. txt* dosya uzantısına sahip *QuickStart_* adlı bir blob oluşturur.

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

Blok Blobları 4,7 TB kadar büyük olabilir ve elektronik tablolardan büyük video dosyalarına kadar herhangi bir şey olabilir. Sayfa Blobları birincil olarak IaaS sanal makinelerini geri yükleyen VHD dosyaları için kullanılır. Ekleme Blobları genellikle günlüğe kaydetme için kullanılır (örneğin, bir dosyaya yazmak istediğinizde) ve daha fazla bilgi eklemeye devam edebilirsiniz.

### <a name="list-the-blobs-in-a-container"></a>Kapsayıcıdaki blobları listeleme

[list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method) metodunu kullanarak kapsayıcıdaki dosya listesini alın. Aşağıdaki kod blob listesini alır, ardından adlarını görüntüler.

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Blobu indirme

[Get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method) yöntemi kullanarak yerel diskinize bir blob indirin. Aşağıdaki kod, önceki bir bölümde oluşturulan blobu indirir.

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Kaynakları temizleme

Blob artık gerekmiyorsa, kaldırmak için [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) kullanın. [Delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method) yöntemini kullanarak kapsayıcının tamamını silin. Kapsayıcının silinmesi, kapsayıcıda depolanan Blobları da siler.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Bloblarla Ruby uygulamaları geliştirme kaynakları

Ruby geliştirmesi için şu ek kaynaklara bakın:

- GitHub’da Azure Depolama için [Ruby istemci kitaplığı kaynak kodunu](https://github.com/Azure/azure-storage-ruby) görüntüleyin ve indirin.
- Ruby istemci kitaplığı kullanılarak yazılmış [Azure örneklerini](/samples/browse/?products=azure&languages=ruby) gezin.
- [Örnek: Ruby 'de Azure depolama ile çalışmaya başlama](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Ruby kullanarak dosyaları Azure Blob depolama ve yerel disk arasında aktarmayı öğrendiniz. BLOB depolama ile çalışma hakkında daha fazla bilgi edinmek için depolama hesabına genel bakış ' a geçin.

> [!div class="nextstepaction"]
> [Depolama hesabına genel bakış](../common/storage-account-overview.md)

Depolama Gezgini ve BLOB 'Lar hakkında daha fazla bilgi için bkz. [Depolama Gezgini Ile Azure Blob depolama kaynaklarını yönetme](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
