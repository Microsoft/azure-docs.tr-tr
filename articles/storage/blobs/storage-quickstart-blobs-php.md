---
title: Azure Hızlı Başlangıç - PHP kullanarak nesne depolamada blob oluşturma | Microsoft Docs
description: PHP kullanarak nesneleri Azure Blob depolama içine/dışına aktarmayı kısa sürede öğrenin
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 0453d7465479fb1410d1436cc3fa53914633ece5
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726385"
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>PHP kullanarak nesneleri Azure Blob depolama içine/dışına aktarma
Bu hızlı başlangıçta, Azure Blob depolamadaki bir kapsayıcıda blok bloblarını karşıya yüklemek, indirmek ve listelemek için PHP'yi nasıl kullanabileceğinizi öğreneceksiniz. 

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Aşağıdaki ek önkoşulların yüklü olduğundan emin olun:

* [PHP](https://php.net/downloads.php)
* [PHP için Azure depolama SDK 'Sı](https://github.com/Azure/azure-storage-php)

## <a name="download-the-sample-application"></a>Örnek uygulamayı indirin:
Bu hızlı başlangıçta kullanılan [örnek uygulama](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git), temel bir PHP uygulamasıdır.  

Uygulamanın bir kopyasını geliştirme ortamınıza indirmek için [Git](https://git-scm.com/)'i kullanın. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

Bu komut, depoyu yerel Git klasörünüze kopyalar. Örnek PHP uygulamasını indirmek için storage-blobs-php-quickstart klasörünün içindeki phpqa.php dosyasını açın.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma
Uygulamada, uygulamanız için **BlobRestProxy** örneği oluşturmak amacıyla depolama hesabı adınızı ve hesap anahtarınızı sağlamanız gerekir. Bu tanımlayıcıları uygulamayı çalıştıran yerel makine üzerindeki bir ortam değişkeninde depolamanız önerilir. Ortam değişkenini oluşturmak için İşletim Sisteminize bağlı olarak aşağıdaki örneklerden birini kullanın. **youraccountname** ve **accountkey** değerlerini, hesap adınız ve anahtarınızla değiştirin.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>Ortamınızı yapılandırma
Yerel git klasörünüzden klasörü alın ve PHP sunucunuzun hizmet verdiği bir dizine yerleştirin. Ardından, kapsamı aynı dizinde oluşturulmuş bir komut istemi açıp şunu girin: `php composer.phar install`

## <a name="run-the-sample"></a>Örneği çalıştırma
Bu örnek, ‘.’ klasöründe bir sınama dosyası oluşturur. Örnek program sınama dosyasını Blob depolamaya yükler, kapsayıcıdaki blobları listeler ve dosyayı yeni bir adla indirir. 

Örnek uygulamayı çalıştırın. Aşağıdaki çıktı, uygulama çalıştırılırken döndürülen çıktının bir örneğidir:
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
Ekrandaki tuşuna bastığınızda, örnek program depolama kapsayıcısını ve dosyaları siler. Devam etmeden önce, iki dosya için sunucunuzun klasörünü kontrol edin. Dosyaları açarak aynı olduklarını görebilirsiniz.

Ayrıca, Blob depolamadaki dosyaları görüntülemek için, [Azure Depolama Gezgini](https://storageexplorer.com) gibi bir araç da kullanabilirsiniz. Azure Depolama Gezgini, depolama hesabı bilgilerinize erişmenize olanak tanıyan ücretsiz ve platformlar arası bir araçtır. 

Dosyaları doğruladıktan sonra, tanıtımı tamamlamak ve test dosyalarını silmek için herhangi bir tuşa basın. Artık örnek dosyanın işlevini gördüğünüze göre, koda göz atmak için example.rb dosyasını açabilirsiniz. 

## <a name="understand-the-sample-code"></a>Örnek kodu anlama

Sonraki aşamada, nasıl çalıştığını anlayabilmeniz için örnek kodu inceleyeceğiz.

### <a name="get-references-to-the-storage-objects"></a>Depolama nesneleriyle ilgili başvuruları alma
İlk önce, Blob depolamaya erişmek ve Blob depolamayı yönetmek için kullanılan nesnelere başvuru oluşturmaktır. Bu nesneler birbirleri üzerinde derlenir ve her bir dosya, listede yanında yer alan dosya tarafından kullanılır.

* Azure depolama örneğini oluşturmak **BlobRestProxy** nesne bağlantı kimlik bilgilerini ayarlayın. 
* Depolama hesabınızdaki Blob hizmetine işaret eden bir **BlobService** nesnesi oluşturun. 
* Eriştiğiniz kapsayıcıyı ifade eden bir **Container** nesnesi oluşturun. Kapsayıcılar, tıpkı bilgisayarınızdaki dosyaları düzenlemek için klasörleri kullandığınız gibi blobları düzenlemek için kullanılır.

**blobClient** kapsayıcı nesnesini oluşturduktan sonra, ilgilendiğiniz belirli bir blobu işaret eden **Block** blob nesnesini oluşturabilirsiniz. Daha sonra karşıya yükleme, indirme ve kopyalama gibi işlemler gerçekleştirebilirsiniz.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcılar ve blob adları hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Bu bölümde Azure depolama istemcisi örneği kurdunuz, blob hizmeti nesnesini oluşturdunuz, yeni bir kapsayıcı oluşturdunuz ve kapsayıcıdaki izinleri bloblar herkese açık olacak şekilde ayarladınız. Bu kapsayıcının adı **quickstartblobs**'dur. 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>Blobları kapsayıcıya yükleme

Blob depolama blok blobları, ekleme bloblarını ve sayfa bloblarını destekler. Blok blobları en sık kullanılan bloblardır ve bu hızlı başlangıçta bu bloblar kullanılmıştır.  

Bir dosyayı bloba yüklemek için, yerel diskinizdeki dizin adıyla dosya adını birleştirerek dosyanın tam yolunu alın. Sonra, dosyayı belirtilen yola **createBlockBlob()** yöntemiyle yükleyebilirsiniz. 

Örnek kod bir yerel dosya alır ve Azure'a yükler. Dosya **myfile** olarak depolanır ve blob adı koda **fileToUpload** olarak kaydedilir. Aşağıdaki örnek, dosyayı **quickstartblobs** adlı kapsayıcınıza yükler.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

Bir blok blobunun içeriğini kısmen güncelleştirmek için **createblocklist()** yöntemini kullanın. Blok bloblarının boyutu 4,7 TB'yi bulabilir ve bu bloblar Excel elektronik tablolarından büyük video dosyalarına kadar birçok türde olabilir. Sayfa blobları öncelikli olarak IaaS VM'leri desteklemek için kullanılan VHD dosyalarında kullanılır. Ekleme blobları, bir dosyaya yazıp daha sonradan daha fazla bilgi eklemek istediğiniz durumlarda günlüğe kaydetme için kullanılır. Ekleme blobu tek bir yazar modelinde kullanılabilir. Blob depolamada depolanan nesnelerin çoğu blok blobudur.

### <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

**listBlobs()** yöntemini kullanarak kapsayıcıdaki dosyaların listesini alabilirsiniz. Aşağıdaki kod blob listesini alır, ardından bu bloblarda döngü yapar ve kapsayıcıda bulunan blobların adlarını gösterir.  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>Bloblarınızın içeriğini alma

**getBlob()** yöntemini kullanarak bloblarınızın içeriğini alın. Aşağıdaki kod, önceki bir bölümde karşıya yüklenen blobun içeriğini gösterir.

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>Kaynakları temizleme
Bu hızlı başlangıçta karşıya yüklenen bloblara artık ihtiyacınız kalmadığında, **deleteContainer()** yöntemini kullanarak kapsayıcının tamamını silebilirsiniz. Oluşturulan dosyalara artık ihtiyaç duymuyorsanız dosyaları silmek için **deleteBlob()** yöntemini kullanırsınız.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>Bloblarla PHP uygulamaları geliştirme kaynakları

Blob depolama ile PHP geliştirmeye yönelik şu ek kaynaklara bakın:

- GitHub’da Azure Depolama için [PHP istemci kitaplığı kaynak kodunu](https://github.com/Azure/azure-storage-php) görüntüleyin, indirin ve yükleyin.
- PHP istemci kitaplığı kullanılarak yazılmış [Blob depolama örneklerini](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob) araştırın.

## <a name="next-steps"></a>Sonraki adımlar
 
Bu hızlı başlangıçta, dosyaları PHP kullanarak yerel bir disk ile Azure blob depolama arasında aktarmayı öğrendiniz. PHP ile çalışma hakkında daha fazla bilgi edinmek için PHP Geliştirici merkezimize devam edin.

> [!div class="nextstepaction"]
> [PHP Geliştirici Merkezi](https://azure.microsoft.com/develop/php/)


Depolama Gezgini ve Bloblar hakkında daha fazla bilgi için bkz. [Azure Blob depolama kaynaklarını Depolama Gezgini'yle yönetme](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
