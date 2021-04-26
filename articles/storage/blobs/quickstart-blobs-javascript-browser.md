---
title: 'Hızlı başlangıç: Azure Blob depolama kitaplığı V12-bir tarayıcıda JavaScript'
description: Bu hızlı başlangıçta, bir tarayıcıda JavaScript için Azure Blob depolama istemci kitaplığı sürüm 12 ' yi nasıl kullanacağınızı öğreneceksiniz. Blob depolamada bir kapsayıcı ve bir nesne oluşturursunuz. Sonra, bir kapsayıcıdaki tüm Blobları listeleyeceğinizi öğrenirsiniz. Son olarak, Blobları silmeyi ve bir kapsayıcıyı silmeyi öğrenirsiniz.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: bc02f9c526ec9b0826bbe640efc4b474794f30ae
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278499"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-a-browser"></a>Hızlı başlangıç: tarayıcıda JavaScript V12 SDK ile Blobları yönetme

Azure Blob depolama, büyük miktarlarda yapılandırılmamış verileri depolamak için iyileştirilmiştir. Blob 'lar, görüntüler, belgeler, akış ortamları ve arşiv verileri dahil metin veya ikili veri içerebilen nesnelerdir. Bu hızlı başlangıçta, bir tarayıcıda JavaScript kullanarak blob 'ları yönetmeyi öğreneceksiniz. Blobları karşıya yükleyip listetireceksiniz ve kapsayıcı oluşturup sileceksiniz.

Ek kaynaklar:

* [API başvuru belgeleri](/javascript/api/@azure/storage-blob)
* [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Paket (NPM)](https://www.npmjs.com/package/@azure/storage-blob)
* [Örnekler](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Önkoşullar

* [Etkin aboneliği olan bir Azure hesabı](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Bir Azure depolama hesabı](../common/storage-account-create.md)
* [Node.js](https://nodejs.org)
* [Microsoft Visual Studio Code](https://code.visualstudio.com)
* Tarayıcı hata ayıklaması için bir Visual Studio Code uzantısı, örneğin:
    * [Microsoft Edge için hata ayıklayıcı](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge)
    * [Chrome için hata ayıklayıcı](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)
    * [Firefox hata ayıklayıcı](https://marketplace.visualstudio.com/items?itemName=firefox-devtools.vscode-firefox-debug)


[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="object-model"></a>Nesne modeli

BLOB depolama üç tür kaynak sunar:

* Depolama hesabı
* Depolama hesabındaki bir kapsayıcı
* Kapsayıcıda bir blob

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![BLOB depolama mimarisi diyagramı](./media/storage-blobs-introduction/blob1.png)

Bu hızlı başlangıçta, bu kaynaklarla etkileşim kurmak için aşağıdaki JavaScript sınıflarını kullanacaksınız:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` sınıfı, Azure depolama kaynaklarını ve BLOB kapsayıcılarını değiştirmenize olanak sağlar.
* [Containerclient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` sınıfı, Azure depolama kapsayıcılarını ve bunların bloblarını değiştirmenize olanak sağlar.
* [Blockblobclient](/javascript/api/@azure/storage-blob/blockblobclient): `BlockBlobClient` sınıfı, Azure Storage bloblarını değiştirmenize olanak sağlar.

## <a name="setting-up"></a>Ayarlanıyor

Bu bölümde, bir projeyi, JavaScript için Azure Blob depolama istemci kitaplığı V12 ile çalışacak şekilde hazırlama adımları gösterilmektedir.

### <a name="create-a-cors-rule"></a>CORS kuralı oluşturma

Web uygulamanızın istemciden blob depolamaya erişebilmesi için, hesabınızı, [çıkış noktaları arası kaynak paylaşımı](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)veya CORS 'yi etkinleştirecek şekilde yapılandırmanız gerekir.

Azure portal depolama hesabınızı seçin. Yeni bir CORS kuralı tanımlamak için **Ayarlar** bölümüne gidin ve **CORS**' yi seçin. Bu hızlı başlangıçta bir açık CORS kuralı oluşturacaksınız:

![Azure Blob Depolama Hesabı CORS ayarları](media/quickstart-blobs-javascript-browser/azure-blob-storage-cors-settings.png)

Aşağıdaki tabloda her bir CORS ayarı açıklanmakta ve kuralı tanımlamak için kullanılan değerler anlatılmaktadır.

|Ayar  |Değer  | Açıklama |
|---------|---------|---------|
| **IZIN VERILEN ÇıKıŞ NOKTALARı** | **\** _ | Kabul edilebilir çıkış noktaları olarak etki alanları kümesinin virgülle ayrılmış bir listesini kabul eder. Değerin `_` olarak ayarlanması, depolama hesabına tüm etki alanlarının erişmesine izin verir. |
| **IZIN VERILEN YÖNTEMLER** | **Silme**, **Get**, **Head**, **birleştirme**, **gönderi**, **Seçenekler** ve **yerleştirme** | Depolama hesabına göre yürütülmesine izin verilen HTTP fiillerini listeler. Bu hızlı başlangıçta tüm kullanılabilir seçenekleri işaretleyin. |
| **IZIN VERILEN ÜST BILGILER** | **\** _ | Depolama hesabı tarafından izin verilen istek üst bilgilerinin (ön ekli üst bilgiler dahil) listesini tanımlar. Değerin `_` olarak ayarlanması tüm üst bilgilere erişim izni verir. |
| **GÖSTERILEN ÜSTBILGILER** | **\** _ | Hesaba göre izin verilen yanıt üst bilgilerini listeler. Değerin `_` olarak ayarlanması hesabın herhangi bir üst bilgiyi göndermesine izin verir. |
| **MAKSIMUM YAŞ** | **86400** | Tarayıcının ön kontrol SEÇENEKLERI isteğini saniye cinsinden önbelleğe aldığı en uzun süre. *86400* değeri, önbelleğin bir tam gün boyunca kalmasına izin verir. |

Alanları bu tablodaki değerlerle doldurduktan sonra **Kaydet** düğmesine tıklayın.

> [!IMPORTANT]
> Üretimde kullandığınız tüm ayarların, güvenli erişim sağlamak için depolama hesabınıza gereken en düşük erişim miktarını kullanıma sunduğundan emin olun. Burada açıklanan CORS ayarları esnek bir güvenlik ilkesini tanımladığı için hızlı başlangıç için uygundur. Ancak bu ayarlar gerçek bir bağlam için önerilmez.

### <a name="create-a-shared-access-signature"></a>Paylaşılan erişim imzası oluşturma

Paylaşılan erişim imzası (SAS), tarayıcıda çalışan kod tarafından Azure Blob depolama isteklerini yetkilendirmek için kullanılır. İstemci, SAS kullanarak hesap erişim anahtarı veya bağlantı dizesi kullanmadan depolama kaynaklarına erişimi yetkilendirebilir. SAS hakkında daha fazla bilgi edinmek için bkz. [Paylaşılan erişim imzaları (SAS) kullanma](../common/storage-sas-overview.md).

Blob hizmeti SAS URL 'sini almak için aşağıdaki adımları izleyin:

1. Azure portal depolama hesabınızı seçin.
2. **Ayarlar** bölümüne gidin ve **paylaşılan erişim imzası**' nı seçin.
3. Aşağı kaydırın ve **SA ve bağlantı dizesi oluştur** düğmesine tıklayın.
4. Daha fazla ilerleyin ve **BLOB HIZMETI SAS URL 'si** alanını bulun
5. **BLOB HIZMETI SAS URL 'si** alanının en sağ ucundaki **Panoya Kopyala** düğmesine tıklayın.
6. Yakında çıkacak bir adımda, kopyalanmış URL 'YI kullanmak için bir yere kaydedin.

### <a name="add-the-azure-blob-storage-client-library"></a>Azure Blob depolama istemci kitaplığını ekleme

Yerel bilgisayarınızda, *Azure-Bloblar-js-Browser* adlı yeni bir klasör oluşturun ve Visual Studio Code açın.

Visual Studio Code içinde bir konsol penceresi açmak için **> terminali görüntüle** ' yi seçin. Dosya [ üzerinde birpackage.js](https://docs.npmjs.com/files/package.json) oluşturmak için Terminal penceresinde aşağıdaki Node.js Paket Yöneticisi (NPM) komutunu çalıştırın.

```console
npm init -y
```

Azure SDK birçok ayrı paketten oluşur. Kullanmayı düşündüğünüz hizmetlere göre hangi paketlere ihtiyacınız olduğunu seçebilirsiniz. `npm`Paketi yüklemek için Terminal penceresinde aşağıdaki komutu çalıştırın `@azure/storage-blob` .

```console
npm install --save @azure/storage-blob
```

#### <a name="bundle-the-azure-blob-storage-client-library"></a>Azure Blob depolama istemci kitaplığı 'nı paketleyin

Azure SDK kitaplıklarını bir Web sitesinde kullanmak için kodunuzu tarayıcı içinde çalışacak şekilde dönüştürün. Bunu, paketler adlı bir araç kullanarak yapabilirsiniz. Paketleme, [Node.js](https://nodejs.org) kuralları kullanılarak yazılmış JavaScript kodunu alır ve tarayıcılar tarafından anlaşılan bir biçime dönüştürür. Bu hızlı başlangıç makalesinde [paket](https://parceljs.org/) paketcisi kullanılmaktadır.

Terminal penceresinde aşağıdaki komutu çalıştırarak paket ' i yükler `npm` :

```console
npm install -g parcel-bundler
```

Visual Studio Code, dosya *üzerindepackage.js* açın ve `browserlist` ve girdileri arasına bir ekleyin `license` `dependencies` . Bu `browserlist` , popüler üç tarayıcının en son sürümünü hedefler. Dosyadaki tam *package.js* şu şekilde görünmelidir:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/package.json" highlight="12-16":::

*package.js* dosyaya kaydedin.

### <a name="import-the-azure-blob-storage-client-library"></a>Azure Blob depolama istemci kitaplığını içeri aktarma

JavaScript içinde Azure SDK kitaplıklarını kullanmak için paketini içeri aktarın `@azure/storage-blob` . Aşağıdaki JavaScript kodunu içeren Visual Studio Code yeni bir dosya oluşturun.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ImportLibrary":::

Dosyayı *Azure-Blobları-js-Browser* dizinine *index.js* olarak kaydedin.

### <a name="implement-the-html-page"></a>HTML sayfasını uygulama

Visual Studio Code yeni bir dosya oluşturun ve aşağıdaki HTML kodunu ekleyin.

:::code language="html" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.html":::

Dosyayı *Azure-Blobları-js-Browser* klasörüne *index.html* olarak kaydedin.

## <a name="code-examples"></a>Kod örnekleri

Örnek kod, JavaScript için Azure Blob depolama istemci kitaplığı ile aşağıdaki görevlerin nasıl gerçekleştirileceğini gösterir:

* [UI öğeleri için alanları bildirin](#declare-fields-for-ui-elements)
* [Depolama hesabı bilgilerinizi ekleyin](#add-your-storage-account-info)
* [İstemci nesneleri oluşturma](#create-client-objects)
* [Depolama kapsayıcısı oluşturma ve silme](#create-and-delete-a-storage-container)
* [Blobları listeleme](#list-blobs)
* [Blobları karşıya yükleme](#upload-blobs)
* [Blob’ları silme](#delete-blobs)

Tüm kod parçacıklarını *index.js* dosyasına ekledikten sonra kodu çalıştırırsınız.

### <a name="declare-fields-for-ui-elements"></a>UI öğeleri için alanları bildirin

*index.js* dosyasının sonuna aşağıdaki kodu ekleyin.

:::code language="JavaScript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeclareVariables":::

*index.js* dosyasını kaydedin.

Bu kod, her HTML öğesi için alanları bildirir ve `reportStatus` çıktıyı göstermek için bir işlev uygular.

Aşağıdaki bölümlerde, bir önceki bloğundan sonra her bir JavaScript kodu bloğunu ekleyin.

### <a name="add-your-storage-account-info"></a>Depolama hesabı bilgilerinizi ekleyin

Depolama hesabınıza erişmek için kod ekleyin. Yer tutucusunu, daha önce oluşturduğunuz blob hizmeti SAS URL 'niz ile değiştirin. *index.js* dosyasının sonuna aşağıdaki kodu ekleyin.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_StorageAcctInfo":::

*index.js* dosyasını kaydedin.

### <a name="create-client-objects"></a>İstemci nesneleri oluşturma

Azure Blob depolama hizmetiyle etkileşim kurmak için [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) ve [containerclient](/javascript/api/@azure/storage-blob/containerclient) nesneleri oluşturun. *index.js* dosyasının sonuna aşağıdaki kodu ekleyin.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateClientObjects":::

*index.js* dosyasını kaydedin.

### <a name="create-and-delete-a-storage-container"></a>Depolama kapsayıcısı oluşturma ve silme

Web sayfasındaki ilgili düğmeye tıkladığınızda depolama kapsayıcısını oluşturun ve silin. *index.js* dosyasının sonuna aşağıdaki kodu ekleyin.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateDeleteContainer":::

*index.js* dosyasını kaydedin.

### <a name="list-blobs"></a>Blobları listeleme

**Dosyaları Listele** düğmesine tıkladığınızda depolama kapsayıcısının içeriğini listeleyin. *index.js* dosyasının sonuna aşağıdaki kodu ekleyin.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ListBlobs":::

*index.js* dosyasını kaydedin.

Bu kod, [Containerclient. listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) işlevini çağırır ve döndürülen her [blobitem](/javascript/api/@azure/storage-blob/blobitem) adını almak için bir yineleyici kullanır. Her biri için `BlobItem` , **dosyalar** listesini [Name](/javascript/api/@azure/storage-blob/blobitem#name) özelliği değeri ile güncelleştirir.

### <a name="upload-blobs"></a>Blobları karşıya yükleme

**Dosyaları seçin ve karşıya yükle** düğmesine tıkladığınızda dosyaları depolama kapsayıcısına yükleyin. *index.js* dosyasının sonuna aşağıdaki kodu ekleyin.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_UploadBlobs":::

*index.js* dosyasını kaydedin.

Bu kod, **dosyaları seç ve karşıya yükle** düğmesini gizli öğesine bağlar `file-input` . Düğme `click` olayı, dosya girişi olayını tetikler `click` ve dosya seçiciyi görüntüler. Dosyaları seçtikten ve iletişim kutusunu kapattıktan sonra, `input` olay oluşur ve `uploadFiles` işlev çağrılır. Bu işlev bir [Blockblobclient](/javascript/api/@azure/storage-blob/blockblobclient) nesnesi oluşturur, ardından seçtiğiniz her dosya için yalnızca tarayıcı [uploadbrowserdata](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) işlevini çağırır. Her çağrı bir döndürür `Promise` . Her biri `Promise` bir listeye eklenir, böylece dosyalar paralel olarak karşıya yüklenebilmesini sağlar.

### <a name="delete-blobs"></a>Blob’ları silme

**Seçili dosyaları sil** düğmesine tıkladığınızda depolama kapsayıcısından dosyaları silin. *index.js* dosyasının sonuna aşağıdaki kodu ekleyin.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeleteBlobs":::

*index.js* dosyasını kaydedin.

Bu kod, listede seçilen her dosyayı kaldırmak için [Containerclient. deleteBlob](/javascript/api/@azure/storage-blob/containerclient#deleteblob-string--blobdeleteoptions-) işlevini çağırır. Daha sonra `listFiles` **Dosya** listesinin içeriğini yenilemek için daha önce gösterilen işlevi çağırır.

## <a name="run-the-code"></a>Kodu çalıştırma

Kodu Visual Studio Code hata ayıklayıcı içinde çalıştırmak için, tarayıcınızda *launch.js* dosyasını yapılandırın.

### <a name="configure-the-debugger"></a>Hata ayıklayıcıyı yapılandırma

Visual Studio Code hata ayıklayıcı uzantısını ayarlamak için:

1. **Çalıştır > yapılandırma Ekle** ' yi seçin
2. Daha önce [Önkoşullar](#prerequisites) bölümünde yüklediğiniz uzantıya bağlı olarak **Edge**, **Chrome** veya **Firefox**' u seçin.

Yeni bir yapılandırma eklemek dosyada *launch.js* oluşturur ve düzenleyicide açar. Dosyadaki *launch.js* , `url` değeri aşağıda gösterildiği gibi olacak şekilde değiştirin `http://localhost:1234/index.html` :

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/.vscode/launch.json" highlight="11":::

Güncelleştirme sonrasında *launch.js* dosyaya kaydedin. Bu yapılandırma hangi tarayıcının açılacağını ve hangi URL 'nin yükleneceğini Visual Studio Code söyler.

### <a name="launch-the-web-server"></a>Web sunucusunu Başlat

Yerel geliştirme Web sunucusunu başlatmak için, Visual Studio Code içinde bir konsol penceresi açmak üzere **> terminali görüntüle** ' yi seçin ve sonra aşağıdaki komutu girin.

```console
parcel index.html
```

Paket, kodunuzu paketler ve sayfanız için yerel bir geliştirme sunucusu başlatır `http://localhost:1234/index.html` . *index.js* yaptığınız değişiklikler, dosyayı her kaydettiğinizde geliştirme sunucusuna otomatik olarak oluşturulup yansıyacaktır.

**Yapılandırılmış bağlantı noktası 1234**' nin kullanılamayacağını belirten bir ileti alırsanız, komutunu çalıştırarak bağlantı noktasını değiştirebilirsiniz `parcel -p <port#> index.html` . Dosyadaki *launch.js* , URL yolundaki bağlantı noktasını eşleşecek şekilde güncelleştirin.

### <a name="start-debugging"></a>Hata ayıklamayı Başlat

Hata ayıklayıcıda sayfayı çalıştırın ve BLOB depolamanın nasıl çalıştığına ilişkin bir fikir alın. Herhangi bir hata oluşursa, Web sayfasındaki **durum** bölmesinde alınan hata iletisi görüntülenir.

Visual Studio Code hata ayıklayıcı eklenmiş bir tarayıcıda *index.html* 'yi açmak için Çalıştır ' ı seçin **> hata ayıklamayı başlatın** veya Visual Studio Code ' de F5 tuşuna basın.

### <a name="use-the-web-app"></a>Web uygulamasını kullanma

[Azure Portal](https://portal.azure.com), aşağıdaki ADıMLARı izleyerek API çağrılarının sonuçlarını doğrulayabilirsiniz.

#### <a name="step-1---create-a-container"></a>1. adım-kapsayıcı oluşturma

1. Web uygulamasında **kapsayıcı oluştur**' u seçin. Durum, bir kapsayıcının oluşturulduğunu gösterir.
2. Azure portal doğrulamak için depolama hesabınızı seçin. **Blob hizmeti** bölümünden **Kapsayıcılar**’ı seçin. Yeni kapsayıcının göründüğünü doğrulayın. ( **Yenile**' yi seçmeniz gerekebilir.)

#### <a name="step-2---upload-a-blob-to-the-container"></a>2. adım-bir blobu kapsayıcıya yükleme

1. Yerel bilgisayarınızda, *test.txt* gibi bir test dosyası oluşturun ve kaydedin.
2. Web uygulamasında, **Seç ve dosyaları karşıya yükle**' ye tıklayın.
3. Test dosyanıza gidin ve **Aç**' ı seçin. Durum, dosyanın karşıya yüklendiğini ve dosya listesinin alındığını gösterir.
4. Azure portal, daha önce oluşturduğunuz yeni kapsayıcının adını seçin. Test dosyasının göründüğünü doğrulayın.

#### <a name="step-3---delete-the-blob"></a>3. adım-blobu silme

1. Web uygulamasında, **dosyalar** altında, test dosyasını seçin.
2. **Seçili dosyaları sil**' i seçin. Durum, dosyanın silindiğini ve kapsayıcının dosya içermediğini belirtir.
3. Azure portal **Yenile**' yi seçin. **BLOB bulunamadığını** doğrulayın.

#### <a name="step-4---delete-the-container"></a>4. adım-kapsayıcıyı silme

1. Web uygulamasında **kapsayıcıyı sil**' i seçin. Durum, kapsayıcının silindiğini gösterir.
2. Azure portal, **\<account-name\> | Kapsayıcılar** bağlantısı portalı bölmesinin sol üst kısmında.
3. **Yenile**' yi seçin. Yeni kapsayıcı kaybolur.
4. Web uygulamasını kapatın.

### <a name="clean-up-resources"></a>Kaynakları temizleme

Visual Studio Code ' deki **Terminal** konsoluna tıklayın ve Web sunucusunu durdurmak için CTRL + C tuşlarına basın.

Bu hızlı başlangıç sırasında oluşturulan kaynakları temizlemek için [Azure Portal](https://portal.azure.com) gidin ve [Önkoşullar](#prerequisites) bölümünde oluşturduğunuz kaynak grubunu silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, JavaScript kullanarak Blobları karşıya yükleme, listeleme ve silme hakkında daha fazla öğrendiniz. Ayrıca bir BLOB depolama kapsayıcısı oluşturmayı ve silmeyi de öğrendiniz.

Öğreticiler, örnekler, hızlı başlangıçler ve diğer belgeler için şu adresi ziyaret edin:

> [!div class="nextstepaction"]
> [JavaScript için Azure belgeleri](/azure/developer/javascript/)

* Daha fazla bilgi edinmek için bkz. [JavaScript Için Azure Blob depolama istemci kitaplığı](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
* BLOB depolama örnek uygulamalarını görmek için [Azure Blob depolama istemci kitaplığı V12 JavaScript örnekleri](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)' ne geçin.