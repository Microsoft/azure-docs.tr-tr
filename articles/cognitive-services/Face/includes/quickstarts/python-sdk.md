---
title: Yüz Python istemci kitaplığı hızlı başlangıç
description: Yüzeyleri algılamak, benzer (görüntüye göre arama) ve yüzleri (yüz tanıma arama) belirlemek için Python için yüz istemci kitaplığını kullanın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/10/2020
ms.author: pafarley
ms.openlocfilehash: 0939216e1d96e898d7c45b9e7a3ec72453b263d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958040"
---
Python için yüz istemci kitaplığını kullanarak yüz tanıma ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Yüz tanıma hizmeti, görüntülerdeki insan yüzlerini algılayıp tanımayı sağlayan gelişmiş algoritmalara erişmenizi sağlar.

Python için yüz istemci kitaplığını kullanarak şunları yapın:

* [Bir görüntüdeki yüzleri algılama](#detect-faces-in-an-image)
* [Benzer yüzeyleri bulun](#find-similar-faces)
* [Bir PersonGroup oluşturma ve eğitme](#create-and-train-a-persongroup)
* [Yüz tanıma](#identify-a-face)
* [Yüzeyleri doğrula](#verify-faces)

[Başvuru belgeleri](/python/api/azure-cognitiveservices-vision-face/)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face)  |  [Paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/)  |  [Örnekler](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Python yüklemeniz [PIP](https://pip.pypa.io/en/stable/)'yi içermelidir. Komut satırında komutunu çalıştırarak PIP 'nin yüklenip yüklenmediğini kontrol edebilirsiniz `pip --version` . Python 'un en son sürümünü yükleyerek PIP 'yi alın.
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" bir yüz kaynağı oluşturun "  target="_blank"> </a> Azure Portal anahtar ve uç noktanıza ulaşmak için bir yüz kaynağı oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Yüz Tanıma API'si bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor
 
### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Python yükledikten sonra, ile istemci kitaplığını yükleyebilirsiniz:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

### <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

Örneğin, yeni bir Python betiği oluşturun &mdash; *QuickStart-File.py*. Ardından bunu tercih ettiğiniz düzenleyicide veya IDE 'de açın ve aşağıdaki kitaplıkları içeri aktarın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)'da bulabilirsiniz.

Daha sonra, kaynağınızın Azure uç noktası ve anahtarı için değişkenler oluşturun.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!IMPORTANT]
> Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz yüz kaynağı başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Anahtar ve uç noktanızı kaynağın **anahtar ve uç nokta** sayfasında, **kaynak yönetimi** altında bulabilirsiniz. 
>
> İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Örneğin, [Azure Anahtar Kasası](../../../../key-vault/general/overview.md).

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, yüz Python istemci kitaplığının önemli özelliklerinden bazılarını işler.

|Ad|Açıklama|
|---|---|
|[FaceClient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient) | Bu sınıf, yüz hizmetini kullanma yetkinizi temsil eder ve tüm yüz işlevleri için buna ihtiyacınız vardır. Bunu Abonelik bilgileriniz ile birlikte başlatır ve diğer sınıfların örneklerini oluşturmak için kullanırsınız. |
|[Çok yönlü Işlemler](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations)|Bu sınıf, insan yüzeyleri ile gerçekleştirebileceğiniz temel algılama ve tanıma görevlerini işler. |
|[DetectedFace](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface)|Bu sınıf, görüntüde tek bir yüz tarafından algılanan tüm verileri temsil eder. Yüz hakkında ayrıntılı bilgi almak için bu uygulamayı kullanabilirsiniz.|
|[FaceListOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations)|Bu sınıf, bir assıralanan yüz kümesini depolayan, bulutta depolanan çok **yönlü liste** yapılarını yönetir. |
|[PersonGroupPersonOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations)| Bu sınıf, tek bir kişiye ait olan bir yüzey kümesini depolayan, bulutta depolanan **kişi** yapılarını yönetir.|
|[PersonGroupOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations)| Bu sınıf, bir dizi yönetilen **kişi** nesnesini depolayan, bulut ile depolanmış olan **persongroup** yapılarını yönetir. |
|[ShapshotOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations)|Bu sınıf, anlık görüntü işlevlerini yönetir; Tüm bulut tabanlı yüz verilerinizi geçici olarak kaydetmek ve bu verileri yeni bir Azure aboneliğine geçirmek için kullanabilirsiniz. |

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Python için yüz istemci kitaplığıyla aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Bir görüntüdeki yüzleri algılama](#detect-faces-in-an-image)
* [Benzer yüzeyleri bulun](#find-similar-faces)
* [Bir PersonGroup oluşturma ve eğitme](#create-and-train-a-persongroup)
* [Yüz tanıma](#identify-a-face)
* [Yüzeyleri doğrula](#verify-faces)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Uç noktanız ve anahtarınızla bir istemci örneği oluşturun. Anahtarınızla bir [Biliveservicescredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) nesnesi oluşturun ve bir [faceclient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient) nesnesi oluşturmak için bunu uç noktanızla birlikte kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Bir görüntüdeki yüzleri algılama

Aşağıdaki kod, uzak görüntüde bir yüz algılar. Algılanan yüz KIMLIĞINI konsola yazdırır ve ayrıca program belleğine depolar. Daha sonra, birden çok kişiye sahip bir görüntüdeki yüzeyleri algılar ve kimliklerini konsola yazdırır. [Detect_with_url](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) yöntemindeki parametreleri değiştirerek, her bir [detectedface](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface) nesnesiyle farklı bilgiler döndürebilirsiniz.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

> [!TIP]
> Ayrıca, yerel görüntüdeki yüzeyleri de algılayabilirsiniz. **Detect_with_stream** gibi çok [yönlü işlemler](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations) yöntemlerine bakın.

### <a name="display-and-frame-faces"></a>Görüntü ve çerçeve yüzeyleri

Aşağıdaki kod, görüntüye verilen görüntünün çıktısını verir ve DetectedFace. faceRectangle özelliğini kullanarak yüzlerin etrafında dikdörtgenler çizer.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Yüz çevresinde çizilmiş kırmızı bir dikdörtgenle genç kadın](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Benzer yüzleri bulma

Aşağıdaki kod, algılanan tek bir yüzeyi (kaynak) alır ve eşleşmeleri (yüz görüntüye göre arama) bulmak için bir dizi diğer yüzeyi (hedef) arar. Bir eşleşme bulduğunda, eşleşen yüzün KIMLIĞINI konsola yazdırır.

### <a name="find-matches"></a>Eşleşmeleri bul

İlk olarak, bir başvuruyu tek bir yüze kaydetmek için yukarıdaki bölümde ([görüntüdeki yüzeyleri algılayarak](#detect-faces-in-an-image)) kodu çalıştırın. Ardından, bir grup görüntüsündeki birkaç yüze başvuru almak için aşağıdaki kodu çalıştırın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Sonra gruptaki ilk yüzün örneklerini bulmak için aşağıdaki kod bloğunu ekleyin. Bu davranışı nasıl değiştireceğinizi öğrenmek için [find_similar](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) yöntemine bakın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Yazdırma eşleşmeleri

Eşleştirme ayrıntılarını konsola yazdırmak için aşağıdaki kodu kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-persongroup"></a>Bir PersonGroup oluşturma ve eğitme

Aşağıdaki kod, üç farklı **kişi** nesnesi Ile bir **persongroup** oluşturur. Her **kişiyi** örnek bir görüntü kümesiyle ilişkilendirir ve sonra her kişiyi tanıyabilecek. 

### <a name="create-persongroup"></a>Kişilik grubu oluştur

Bu senaryoda ilerlemek için aşağıdaki görüntüleri projenizin kök dizinine kaydetmeniz gerekir: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

Bu görüntü grubu üç farklı kişiye karşılık gelen üç yüz görüntüsü kümesini içerir. Kod, üç **kişi** nesnesini tanımlar ve bunları, ve ile başlayan resim dosyalarıyla ilişkilendirir `woman` `man` `child` .

Görüntülerinizi ayarladıktan sonra, oluşturduğunuz **Persongroup** nesnesi için betiğinizin en üstünde bir etiket tanımlayın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Ardından, betiğinizin en altına aşağıdaki kodu ekleyin. Bu kod, bir **Persongroup** ve üç **kişi** nesnesi oluşturur.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Kişilere yüz atama

Aşağıdaki kod, görüntülerinizi ön ekine göre sıralar, yüzeyleri algılar ve her bir **kişi** nesnesine yüzeyleri atar.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

> [!TIP]
> URL tarafından başvurulan uzak görüntülerden de bir **Persongroup** oluşturabilirsiniz. **Add_face_from_url** gibi [Persongrouppersonoperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations) yöntemlerine bakın.

### <a name="train-persongroup"></a>Kişilik grubu eğitme

Yüzleri atadıktan sonra, **kişi** nesnelerinin her biriyle ilişkili görsel özelliklerini tanımlayabilmesi Için **persongroup** 'u eğitmeniz gerekir. Aşağıdaki kod, zaman uyumsuz **eğitme** yöntemini çağırır ve sonucu, durumu konsola yazdırarak tarar.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

> [!TIP]
> Yüz Tanıma API'si, doğası gereği statik olan önceden oluşturulmuş modeller kümesi üzerinde çalışır (modelin performansı, hizmet çalıştırıldığı sürece bu şekilde çalışmaz veya geliştirilecektir). Modelin ürettiği sonuçlar, Microsoft modelin arka ucunu tamamen yeni bir model sürümüne geçirmeden güncelleştirse de değişiklik gösterebilir. Bir modelin daha yeni bir sürümünden yararlanmak için, daha yeni modeli aynı kayıt görüntülerine sahip bir parametre olarak belirterek, **Personal grubunuzu** yeniden eğitebilirsiniz.

## <a name="identify-a-face"></a>Yüz tanıma

Tanımlama işlemi, bir kişinin (veya birden çok kişinin) bir görüntüsünü alır ve görüntüdeki her bir yüzün kimliğini bulmak için (yüz tanıma arama) arar. Algılanan her yüzü, yüz özellikleri bilinen farklı **kişi** nesnelerinin bir veritabanı olan bir **persongroup** ile karşılaştırır.

> [!IMPORTANT]
> Bu örneği çalıştırmak için, önce [bir PersonGroup oluşturma ve eğitme](#create-and-train-a-persongroup)içinde kodu çalıştırmanız gerekir.

### <a name="get-a-test-image"></a>Test görüntüsü al

Aşağıdaki kod, bir görüntü _test-image-person-group.jpg_ projenizin köküne bakar ve görüntüdeki yüzeyleri algılar. Bu görüntüyü, **Persongroup** Management için kullanılan görüntülerle bulabilirsiniz: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Yüzleri belirleme

**Tanımla** yöntemi, algılanan yüzlerin bir dizisini alır ve bunları bir **persongroup** ile karşılaştırır. Algılanan bir yüzü bir **kişiye** eşleştirebilir, sonucu kaydeder. Bu kod, ayrıntılı eşleşen sonuçları konsola yazdırır.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Yüzeyleri doğrula

Doğrulama işlemi bir yüz KIMLIĞI ve başka bir yüz KIMLIĞI ya da bir **kişi** nesnesi alır ve aynı kişiye ait olup olmadığını belirler.

Aşağıdaki kod, iki kaynak görüntüde yüzeyleri algılar ve sonra bunları hedef görüntüden algılanan bir yüze karşı doğrular.

### <a name="get-test-images"></a>Test görüntülerini al

Aşağıdaki kod blokları, doğrulama işlemi için kaynak ve hedef görüntüleri işaret edecek değişkenleri bildirir.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Doğrulama için yüzeyleri Algıla

Aşağıdaki kod, kaynak ve hedef görüntülerdeki yüzeyleri algılar ve bunları değişkenlere kaydeder.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Doğrulama sonuçlarını al

Aşağıdaki kod, kaynak görüntülerinin her birini hedef görüntüye karşılaştırır ve aynı kişiye ait olup olmadığını belirten bir ileti yazdırır.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Yüz tanıma uygulamanızı, komutuyla birlikte uygulama dizininden çalıştırın `python` .

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Bu hızlı başlangıçta bir **kişilik grubu** oluşturduysanız ve onu silmek istiyorsanız, betiğinizdeki aşağıdaki kodu çalıştırın:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, temel olarak yüz tanıma görevlerini yapmak için Python için yüz istemci kitaplığı 'nı nasıl kullanacağınızı öğrendiniz. Daha sonra, kitaplık hakkında daha fazla bilgi edinmek için başvuru belgelerini inceleyin.

> [!div class="nextstepaction"]
> [Yüz Tanıma API'si Başvurusu (Python)](/python/api/azure-cognitiveservices-vision-face/)

* [Yüz Tanıma hizmeti nedir?](../../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)' da bulunabilir.
