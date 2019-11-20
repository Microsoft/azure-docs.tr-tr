---
title: Azure Hızlı Başlangıç - Ruby kullanarak nesne depolamada blob oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta, nesne (Blob) depolamada depolama hesabı ve kapsayıcı oluşturursunuz. Sonra, Azure Depolama’ya blob yüklemek, blob indirmek ve bir kapsayıcıdaki blobları listelemek amacıyla Ruby için depolama istemcisi kitaplığını kullanırsınız.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 8c24c5f043d17b5f0e54ca1c2c6cf41a0d3fe9bc
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726362"
---
# <a name="quickstart-upload-download-and-list-blobs-using-ruby"></a>Hızlı Başlangıç: Ruby kullanarak Blobları karşıya yükleme, indirme ve listeleme

Bu hızlı başlangıçta, Azure Blob depolamadaki bir kapsayıcıda blok bloblarını karşıya yüklemek, indirmek ve listelemek için Ruby'yi nasıl kullanabileceğinizi öğreneceksiniz. 

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Aşağıdaki ek önkoşulların yüklü olduğundan emin olun:

* [Ruby](https://www.ruby-lang.org/en/downloads/)
* Rubygeı paketini kullanarak [Ruby Için Azure depolama kitaplığı](https://docs.microsoft.com/azure/storage/blobs/storage-ruby-how-to-use-blob-storage): 

    ```
    gem install azure-storage-blob
    ```
    
## <a name="download-the-sample-application"></a>Örnek uygulamayı indirin:
Bu [hızlı başlangıçta](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) kullanılan örnek uygulama, temel bir Ruby uygulamasıdır.  

Uygulamanın bir kopyasını geliştirme ortamınıza indirmek için [Git](https://git-scm.com/)'i kullanın. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

Bu komut, depoyu yerel Git klasörünüze kopyalar. Örnek Ruby uygulamasını indirmek için storage-blobs-ruby-quickstart klasörünün içindeki example.rb dosyasını açın.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma
Uygulamada, uygulamanız için `BlobService` örneği oluşturmak amacıyla depolama hesabı adınızı ve hesap anahtarınızı sağlamanız gerekir. IDE'nizdeki Çözüm Gezgini'nde `example.rb` dosyasını açın. **accountname** ve **accountkey** değerlerini, hesap adınız ve anahtarınızla değiştirin. 

```ruby 
blob_client = Azure::Storage::Blob::BlobService.create(
            storage_account_name: account_name,
            storage_access_key: account_key
          )
```

## <a name="run-the-sample"></a>Örneği çalıştırma
Bu örnek, "Belgeler" klasöründe bir sınama dosyası oluşturur. Örnek program sınama dosyasını Blob depolamaya yükler, kapsayıcıdaki blobları listeler ve dosyayı yeni bir adla indirir. 

Örnek uygulamayı çalıştırın. Aşağıdaki çıktı, uygulama çalıştırılırken döndürülen çıktının bir örneğidir:
  
```
Creating a container: quickstartblobs7b278be3-a0dd-438b-b9cc-473401f0c0e8

Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Devam etmek için herhangi bir tuşa bastığınızda, örnek program depolama kapsayıcısını ve dosyaları siler. Devam etmeden önce, iki dosya için "Belgeler" klasörünüzü kontrol edin. Dosyaları açarak aynı olduklarını görebilirsiniz.

Ayrıca, Blob depolamadaki dosyaları görüntülemek için, [Azure Depolama Gezgini](https://storageexplorer.com) gibi bir araç da kullanabilirsiniz. Azure Depolama Gezgini, depolama hesabı bilgilerinize erişmenize olanak tanıyan ücretsiz ve platformlar arası bir araçtır. 

Dosyaları doğruladıktan sonra, tanıtımı tamamlamak ve test dosyalarını silmek için herhangi bir tuşa basın. Artık örnek dosyanın işlevini gördüğünüze göre, koda göz atmak için example.rb dosyasını açabilirsiniz. 

## <a name="understand-the-sample-code"></a>Örnek kodu anlama

Sonraki aşamada, nasıl çalıştığını anlayabilmeniz için örnek kodu inceleyeceğiz.

### <a name="get-references-to-the-storage-objects"></a>Depolama nesneleriyle ilgili başvuruları alma
İlk önce, Blob depolamaya erişmek ve Blob depolamayı yönetmek için kullanılan nesnelere başvuru oluşturmaktır. Bu nesneler birbirleri üzerinde derlenir ve her bir dosya, listede yanında yer alan dosya tarafından kullanılır.

* Azure depolama örneğini oluşturmak **BlobService** nesne bağlantı kimlik bilgilerini ayarlayın. 
* Eriştiğiniz kapsayıcıyı ifade eden bir **Container** nesnesi oluşturun. Kapsayıcılar, tıpkı bilgisayarınızdaki dosyaları düzenlemek için klasörleri kullandığınız gibi blobları düzenlemek için kullanılır.

Bulut Blob kapsayıcısına sahip olduğunuzda, dilediğiniz bloba işaret eden **Block** blob nesnesi oluşturabilir ve karşıya yükleme, indirme ve kopyalama işlemleri gerçekleştirebilirsiniz.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcılar ve blob adları hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Bu bölümde Azure depolama istemcisi örneği kurdunuz, blob hizmeti nesnesini oluşturdunuz, yeni bir kapsayıcı oluşturdunuz ve ardından kapsayıcıdaki izinleri bloblar herkese açık olacak şekilde ayarladınız. Bu kapsayıcının adı **quickstartblobs**'dur. 

```ruby 
# Create a BlobService object
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs' + SecureRandom.uuid
container = blob_client.create_container(container_name)   

# Set the permission so the blobs are public.
blob_client.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>Blobları kapsayıcıya yükleme

Blob depolama blok blobları, ekleme bloblarını ve sayfa bloblarını destekler. Blok blobları en sık kullanılan bloblardır ve bu hızlı başlangıçta bu bloblar kullanılmıştır.  

Bir dosyayı bloba yüklemek için, yerel diskinizdeki dizin adıyla dosya adını birleştirerek dosyanın tam yolunu alın. Sonra, dosyayı belirtilen yola **create\_block\_blob()** metoduyla yükleyebilirsiniz. 

Örnek kod, karşıya yükleme ve indirme için kullanılacak yerel bir dosya oluşturur, karşıya yüklenecek dosyayı **file\_path\_to\_file** olarak ve blob adını **local\_file\_name** olarak depolar. Aşağıdaki örnek, dosyayı **quickstartblobs** adlı kapsayıcınıza yükler.

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_client.create_block_blob(container.name, local_file_name, full_path_to_file)
```

Bir blok blobunun içeriğini kısmen güncelleştirmek için **create\_block\_list()** metodunu kullanın. Blok bloblarının boyutu 4,7 TB'yi bulabilir ve bu bloblar Excel elektronik tablolarından büyük video dosyalarına kadar birçok türde olabilir. Sayfa blobları öncelikli olarak IaaS VM'leri desteklemek için kullanılan VHD dosyalarında kullanılır. Ekleme blobları, bir dosyaya yazıp daha sonradan daha fazla bilgi eklemek istediğiniz durumlarda günlüğe kaydetme için kullanılır. Ekleme blobu tek bir yazar modelinde kullanılabilir. Blob depolamada depolanan nesnelerin çoğu blok blobudur.

### <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

**list\_blobs()** metodunu kullanarak kapsayıcıdaki dosyaların listesini alabilirsiniz. Aşağıdaki kod blob listesini alır, ardından bu bloblarda döngü yapar ve kapsayıcıda bulunan blobların adlarını gösterir.  

```ruby
# List the blobs in the container
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-the-blobs"></a>Blobları indirme

**get\_blob()** metodunu kullanarak blobları yerel diskinize indirin. Aşağıdaki kod, önceki bir bölüme yüklenen blobu indirir. Yerel diskte yer alan iki dosyayı da görebilmeniz için, blob adının sonuna "_DOWNLOADED" son eki getirilir. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_client.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Kaynakları temizleme
Bu hızlı başlangıçta karşıya yüklenen bloblara artık ihtiyacınız kalmadığında, **delete\_container()** metodunu kullanarak kapsayıcının tamamını silebilirsiniz. Oluşturulan dosyalara artık ihtiyaç duymuyorsanız dosyaları silmek için **delete\_blob()** metodunu kullanırsınız.

```ruby
# Clean up resources. This includes the container and the temp files
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```
## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Bloblarla Ruby uygulamaları geliştirme kaynakları

Blob depolama ile Ruby geliştirmeye yönelik şu ek kaynaklara bakın:

- GitHub’da Azure Depolama için [Ruby istemci kitaplığı kaynak kodunu](https://github.com/Azure/azure-storage-ruby) görüntüleyin ve indirin.
- Ruby istemci kitaplığı kullanılarak yazılmış [Blob depolama örneklerini](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=ruby&term=blob) araştırın.

## <a name="next-steps"></a>Sonraki adımlar
 
Bu hızlı başlangıçta, dosyaları Ruby kullanarak yerel bir disk ve Azure blob depolama arasında aktarmayı öğrendiniz. Blob depolamayla çalışma hakkında daha fazla bilgi edinmek için, Blob depolama nasıl yapılır öğreticisiyle devam edin.

> [!div class="nextstepaction"]
> [Blob Depolama İşlemleri Nasıl Yapılır](./storage-ruby-how-to-use-blob-storage.md)


Depolama Gezgini ve Bloblar hakkında daha fazla bilgi için bkz. [Azure Blob depolama kaynaklarını Depolama Gezgini'yle yönetme](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
