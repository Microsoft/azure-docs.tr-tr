---
title: Yüz JavaScript istemci kitaplığı hızlı başlangıç
description: Yüz algılaması için JavaScript için yüz istemci kitaplığını kullanın, benzer (görüntüye göre arama), yüzleri (yüz tanıma arama) belirleyip yüz verilerinizi geçirin.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: 8f968572a357bb3c98d9c3133a7ec0a0a94dbf93
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957997"
---
## <a name="quickstart-face-client-library-for-javascript"></a>Hızlı başlangıç: JavaScript için yüz istemci kitaplığı

JavaScript için yüz istemci kitaplığını kullanarak yüz tanıma ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Yüz tanıma hizmeti, görüntülerdeki insan yüzlerini algılayıp tanımayı sağlayan gelişmiş algoritmalara erişmenizi sağlar.

JavaScript için yüz istemci kitaplığını kullanarak şunları yapın:

* [Bir görüntüdeki yüzleri algılama](#detect-faces-in-an-image)
* [Benzer yüzeyleri bulun](#find-similar-faces)
* [Bir PersonGroup oluşturma](#create-a-persongroup)
* [Yüz tanıma](#identify-a-face)

[Başvuru belgeleri](/javascript/api/@azure/cognitiveservices-face/)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-face)  |  [Örnekler](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Node.js](https://nodejs.org/en/) en son sürümü
* Azure aboneliğiniz olduktan sonra, anahtarınızı ve uç noktanızı almak için Azure portal [bir yüz kaynağı oluşturun](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) . Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Yüz Tanıma API'si bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

`npm init`Bir dosya ile bir düğüm uygulaması oluşturmak için komutunu çalıştırın `package.json` . 

```console
npm init
```

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler 

`ms-rest-azure`Ve `azure-cognitiveservices-face` NPM paketlerini yüklerken:

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

Uygulamanızın `package.json` dosyası bağımlılıklarla güncelleştirilir.

Adlı bir dosya oluşturun `index.js` ve aşağıdaki kitaplıkları içeri aktarın:

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub]()'da bulabilirsiniz.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

Kaynağınızın Azure uç noktası ve anahtarı için değişkenler oluşturun. 

> [!IMPORTANT]
> Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz yüz kaynağı başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Anahtar ve uç noktanızı kaynağın **anahtar ve uç nokta** sayfasında, **kaynak yönetimi** altında bulabilirsiniz. 
>
> İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Daha fazla bilgi için bilişsel Hizmetler [güvenlik](../../../cognitive-services-security.md) makalesine bakın.

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, yüz .NET istemci kitaplığının bazı önemli özelliklerini işler:

|Ad|Açıklama|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | Bu sınıf, yüz hizmetini kullanma yetkinizi temsil eder ve tüm yüz işlevleri için buna ihtiyacınız vardır. Bunu Abonelik bilgileriniz ile birlikte başlatır ve diğer sınıfların örneklerini oluşturmak için kullanırsınız. |
|[Yüz Tanıma](/javascript/api/@azure/cognitiveservices-face/face)|Bu sınıf, insan yüzeyleri ile gerçekleştirebileceğiniz temel algılama ve tanıma görevlerini işler. |
|[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)|Bu sınıf, görüntüde tek bir yüz tarafından algılanan tüm verileri temsil eder. Yüz hakkında ayrıntılı bilgi almak için bu uygulamayı kullanabilirsiniz.|
|[Çok yönlü liste](/javascript/api/@azure/cognitiveservices-face/facelist)|Bu sınıf, bir assıralanan yüz kümesini depolayan, bulutta depolanan çok **yönlü liste** yapılarını yönetir. |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| Bu sınıf, tek bir kişiye ait olan bir yüzey kümesini depolayan, bulutta depolanan **kişi** yapılarını yönetir.|
|[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)| Bu sınıf, bir dizi yönetilen **kişi** nesnesini depolayan, bulut ile depolanmış olan **persongroup** yapılarını yönetir. |

## <a name="code-examples"></a>Kod örnekleri

Aşağıdaki kod parçacıkları, .NET için yüz istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını göstermektedir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Bir görüntüdeki yüzleri algılama](#detect-faces-in-an-image)
* [Benzer yüzeyleri bulun](#find-similar-faces)
* [Bir PersonGroup oluşturma](#create-a-persongroup)
* [Yüz tanıma](#identify-a-face)

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js)'da bulabilirsiniz.

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Uç noktanız ve anahtarınızla bir istemci örneği oluşturun. Anahtarınızla bir **[Apikeycredentials](/javascript/api/@azure/ms-rest-js/apikeycredentials)** nesnesi oluşturun ve bir **[faceclient](/javascript/api/@azure/cognitiveservices-face/faceclient)** nesnesi oluşturmak için bunu uç noktanızla birlikte kullanın.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>Genel değerleri ve yardımcı işlevi bildirme

Daha sonra ekleyeceğiniz birçok yüz işlemi için aşağıdaki genel değerler gereklidir.

URL, örnek görüntülerin bir klasörünü işaret eder. UUID, oluşturacağınız PersonGroup için hem ad hem de KIMLIK olarak görev yapar.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

Aşağıdaki işlevi, PersonGroup 'un eğitiminin tamamlanmasını beklemek için kullanacaksınız.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-faces-in-an-image"></a>Bir görüntüdeki yüzleri algılama

### <a name="get-detected-face-objects"></a>Algılanan yüz nesneleri Al

Yüzeyleri algılamak için yeni bir yöntem oluşturun. `DetectFaceExtract`Yöntemi, VERILEN URL 'nin üç görüntüsünü işler ve program belleğindeki **[algılayıcısı](/javascript/api/@azure/cognitiveservices-face/detectedface)** olan nesnelerin bir listesini oluşturur. **[Faceattributetype](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** değerlerinin listesi hangi özelliklerin ayıklanacağını belirtir. 

`DetectFaceExtract`Yöntemi ardından, algılanan her bir yüzey için öznitelik verilerini ayrıştırır ve yazdırır. Her öznitelik, özgün yüz algılama API çağrısında ( **[Faceattributetype](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** listesinde) ayrı olarak belirtilmelidir. Aşağıdaki kod her özniteliği işler, ancak büyük olasılıkla yalnızca bir veya birkaç tane kullanmanız gerekir.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> Ayrıca, yerel görüntüdeki yüzeyleri de algılayabilirsiniz. [Detectwithstreamasync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____)gibi [yüz](/javascript/api/@azure/cognitiveservices-face/face) yöntemlerine bakın.

## <a name="find-similar-faces"></a>Benzer yüzleri bulma

Aşağıdaki kod, algılanan tek bir yüzeyi (kaynak) alır ve eşleşmeleri (yüz görüntüye göre arama) bulmak için bir dizi diğer yüzeyi (hedef) arar. Bir eşleşme bulduğunda, eşleşen yüzün KIMLIĞINI konsola yazdırır.

### <a name="detect-faces-for-comparison"></a>Karşılaştırılacak Yüzleri Algıla

İlk olarak, ikinci bir yüz algılama yöntemi tanımlayın. Bunları karşılaştırabilmeniz için görüntülerdeki yüzeyleri saptamanız gerekir ve bu algılama yöntemi karşılaştırma işlemleri için iyileştirilmiştir. Yukarıdaki bölümde olduğu gibi ayrıntılı yüz özniteliklerini ayıklamaz ve farklı bir tanıma modeli kullanır.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>Eşleşmeleri bul

Aşağıdaki yöntem, bir hedef görüntüler kümesindeki yüzeyleri ve tek bir kaynak görüntüyü algılar. Ardından, bunları karşılaştırır ve kaynak görüntüye benzer tüm hedef görüntüleri bulur. Son olarak, eşleşme ayrıntılarını konsola yazdırır.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="identify-a-face"></a>Yüz tanıma

[Tanımlama](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) işlemi, bir kişinin (veya birden çok kişinin) bir görüntüsünü alır ve görüntüdeki her bir yüzün kimliğini bulmak için (yüz tanıma arama) arar. Algılanan her yüzü, yüz özellikleri bilinen farklı [kişi](/javascript/api/@azure/cognitiveservices-face/person) nesnelerinin bir veritabanı olan bir [persongroup](/javascript/api/@azure/cognitiveservices-face/persongroup)ile karşılaştırır. Bu işlemi tanımlamak için önce bir [Persongroup](/javascript/api/@azure/cognitiveservices-face/persongroup)oluşturmanız ve eğitmeniz gerekir.

### <a name="add-faces-to-persongroup"></a>Kişilik grubuna yüz ekleme

[Persongroup](/javascript/api/@azure/cognitiveservices-face/persongroup)'a yüzler eklemek için aşağıdaki işlevi oluşturun.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-persongroup"></a>PersonGroup eğitimi için bekleyin

**Persongroup** 'un eğitime son vermesini beklemek için aşağıdaki yardımcı işlevi oluşturun.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-persongroup"></a>Bir PersonGroup oluşturma

Aşağıdaki kod:
- Bir [Persongroup](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup) oluşturur
-  `AddFacesToPersonGroup` Daha önce tanımladığınız ' ı çağırarak personbuna yüz ekler.
- **Persono grubunu** TRAIN.
- **Persongroup**'taki yüzeyleri tanımlar.

Bu **Persongroup** ve ilişkili **kişi** nesneleri artık Verify, tanımla veya Gruplandır işlemlerinde kullanılmak üzere hazırdır.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> Yerel görüntülerden de bir **Persongroup** oluşturabilirsiniz. [Addfacefromstream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_)gibi [persongroupperson](/javascript/api/@azure/cognitiveservices-face/persongroupperson) yöntemlerine bakın.

## <a name="main"></a>Ana

Son olarak, `main` işlevini oluşturun ve çağırın.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `node` hızlı başlangıç dosyanızdaki komutla çalıştırın.

```console
node index.js
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, temel olarak yüz tanıma görevlerini yapmak için yüz istemci kitaplığını JavaScript için nasıl kullanacağınızı öğrendiniz. Daha sonra, kitaplık hakkında daha fazla bilgi edinmek için başvuru belgelerini inceleyin.

> [!div class="nextstepaction"]
> [Yüz Tanıma API'si Başvurusu (JavaScript)](/javascript/api/@azure/cognitiveservices-face/)

* [Yüz Tanıma hizmeti nedir?](../../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js)' da bulunabilir.