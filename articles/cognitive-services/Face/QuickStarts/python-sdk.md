---
title: 'Hızlı başlangıç: Python için yüz istemci kitaplığı | Microsoft Docs'
description: Bu makalede, Python için yüz istemci kitaplığı ile çalışmaya başlamanıza yardımcı olacak, benzer, daha fazla bilgi, kimlik doğrulama, doğrulama ve daha fazlasını bulabilirsiniz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: pafarley
ms.openlocfilehash: 9728ff86de8561c463886847494ecc4c1c007851
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73743204"
---
# <a name="quickstart-face-client-library-for-python"></a>Hızlı başlangıç: Python için yüz istemci kitaplığı

Python için yüz istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Yüz Tanıma API'si hizmeti, görüntülerdeki insan yüzlerini algılayıp tanımayı sağlayan gelişmiş algoritmalara erişmenizi sağlar.

Python için yüz istemci kitaplığını kullanarak şunları yapın:

* Bir görüntüdeki yüzleri algılama
* Benzer yüzleri bulma
* Kişi grubu oluşturma ve eğitme
* Yüz tanıma
* Yüzeyleri doğrula
* Veri geçişi için bir anlık görüntü alın

[Başvuru belgeleri](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [paketi (pipy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [örnekleri](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-face-azure-resource"></a>Yüz Azure kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. Yerel makinenizde [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) kullanarak yüz için bir kaynak oluşturun. Aşağıdakileri de yapabilirsiniz:

* Ücretsiz olarak yedi gün boyunca geçerli bir [deneme anahtarı](https://azure.microsoft.com/try/cognitive-services/#decision) alın. Kaydolduktan sonra [Azure Web sitesinde](https://azure.microsoft.com/try/cognitive-services/my-apis/)mevcut olacaktır.  
* [Azure Portal](https://portal.azure.com/) kaynağı görüntüleyin

Deneme aboneliğinizden veya kaynağından bir anahtar aldıktan sonra, `FACE_SUBSCRIPTION_KEY`adlı anahtar için [bir ortam değişkeni oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .
 
### <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

Örneğin, *quickstart-file.py*&mdash;yeni bir Python betiği oluşturun. Ardından bunu tercih ettiğiniz düzenleyicide veya IDE 'de açın ve aşağıdaki kitaplıkları içeri aktarın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Daha sonra, kaynağınızın Azure uç noktası ve anahtarı için değişkenler oluşturun. Aboneliğinizi eşleştirmek için uç noktanın (`westus`) ilk kısmını değiştirmeniz gerekebilir.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Uygulamayı başlattıktan sonra ortam değişkenini oluşturduysanız, değişkene erişmek için onu çalıştıran düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

İstemci kitaplığını ile yükleyebilirsiniz:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, yüz Python SDK 'sının önemli özelliklerinden bazılarını işler.

|Ad|Açıklama|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Bu sınıf, yüz hizmetini kullanma yetkinizi temsil eder ve tüm yüz işlevleri için buna ihtiyacınız vardır. Bunu Abonelik bilgileriniz ile birlikte başlatır ve diğer sınıfların örneklerini oluşturmak için kullanırsınız. |
|[Çok yönlü Işlemler](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Bu sınıf, insan yüzeyleri ile gerçekleştirebileceğiniz temel algılama ve tanıma görevlerini işler. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Bu sınıf, görüntüde tek bir yüz tarafından algılanan tüm verileri temsil eder. Yüz hakkında ayrıntılı bilgi almak için bu uygulamayı kullanabilirsiniz.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Bu sınıf, bir assıralanan yüz kümesini depolayan, bulutta depolanan çok **yönlü liste** yapılarını yönetir. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Bu sınıf, tek bir kişiye ait olan bir yüzey kümesini depolayan, bulutta depolanan **kişi** yapılarını yönetir.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Bu sınıf, bir dizi yönetilen **kişi** nesnesini depolayan, bulut ile depolanmış olan **persongroup** yapılarını yönetir. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Bu sınıf, anlık görüntü işlevlerini yönetir; Tüm bulut tabanlı yüz verilerinizi geçici olarak kaydetmek ve bu verileri yeni bir Azure aboneliğine geçirmek için kullanabilirsiniz. |

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Python için yüz istemci kitaplığıyla aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Görüntüdeki yüzeyleri algılama](#detect-faces-in-an-image)
* [Benzer yüzeyleri bulun](#find-similar-faces)
* [Kişi grubu oluşturma ve eğitme](#create-and-train-a-person-group)
* [Yüz tanıma](#identify-a-face)
* [Yüzeyleri doğrula](#verify-faces)
* [Veri geçişi için bir anlık görüntü alın](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

> [!NOTE]
> Bu hızlı başlangıç, `FACE_SUBSCRIPTION_KEY`adlı yüz anahtarınız için [bir ortam değişkeni oluşturduğunuzu](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) varsayar.

Uç noktanız ve anahtarınızla bir istemci örneği oluşturun. Anahtarınızla bir [Biliveservicescredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) nesnesi oluşturun ve bir [faceclient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) nesnesi oluşturmak için bunu uç noktanızla birlikte kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Bir görüntüdeki yüzleri algılama

Aşağıdaki kod, uzak görüntüde bir yüz algılar. Algılanan yüz KIMLIĞINI konsola yazdırır ve ayrıca program belleğine depolar. Daha sonra, birden çok kişiye sahip bir görüntüdeki yüzeyleri algılar ve kimliklerini konsola yazdırır. [Detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) yöntemindeki parametreleri değiştirerek, her bir [detectedface](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) nesnesiyle farklı bilgiler döndürebilirsiniz.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Daha fazla algılama senaryosu için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) 'daki örnek koda bakın.

### <a name="display-and-frame-faces"></a>Görüntü ve çerçeve yüzeyleri

Aşağıdaki kod, görüntüye verilen görüntünün çıktısını verir ve DetectedFace. faceRectangle özelliğini kullanarak yüzlerin etrafında dikdörtgenler çizer.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Yüz çevresinde çizilmiş kırmızı bir dikdörtgenle genç kadın](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Benzer yüzleri bulma

Aşağıdaki kod, algılanan tek bir yüzeyi alır ve eşleşmeleri bulmak için bir dizi diğer yüzü arar. Bir eşleşme bulduğunda, eşleşen yüzün dikdörtgen koordinatlarını konsola yazdırır. 

### <a name="find-matches"></a>Eşleşmeleri bul

İlk olarak, bir başvuruyu tek bir yüze kaydetmek için yukarıdaki bölümde ([görüntüdeki yüzeyleri algılayarak](#detect-faces-in-an-image)) kodu çalıştırın. Ardından, bir grup görüntüsündeki birkaç yüze başvuru almak için aşağıdaki kodu çalıştırın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Sonra gruptaki ilk yüzün örneklerini bulmak için aşağıdaki kod bloğunu ekleyin. Bu davranışı nasıl değiştireceğinizi öğrenmek için [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) yöntemine bakın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Yazdırma eşleşmeleri

Eşleştirme ayrıntılarını konsola yazdırmak için aşağıdaki kodu kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Kişi grubu oluşturma ve eğitme

Aşağıdaki kod, üç farklı **kişi** nesnesi Ile bir **persongroup** oluşturur. Her **kişiyi** örnek bir görüntü kümesiyle ilişkilendirir ve sonra her kişiyi tanıyabilecek. 

### <a name="create-persongroup"></a>Kişilik grubu oluştur

Bu senaryoya geçmek için aşağıdaki görüntüleri projenizin kök dizinine kaydetmeniz gerekir: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Bu görüntü grubu üç farklı kişiye karşılık gelen üç yüz görüntüsü kümesini içerir. Kod, üç **kişi** nesnesini tanımlar ve bunları `woman`, `man`ve `child`ile başlayan resim dosyalarıyla ilişkilendirir.

Görüntülerinizi ayarladıktan sonra, oluşturduğunuz **Persongroup** nesnesi için betiğinizin en üstünde bir etiket tanımlayın.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Ardından, betiğinizin en altına aşağıdaki kodu ekleyin. Bu kod, bir **Personggroup** ve üç **kişi** nesnesi oluşturur.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Kişilere yüz atama

Aşağıdaki kod, görüntülerinizi ön ekine göre sıralar, yüzeyleri algılar ve her bir **kişi** nesnesine yüzeyleri atar.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Kişilik grubu eğitme

Yüzleri atadıktan sonra, **kişi** nesnelerinin her biriyle ilişkili görsel özelliklerini tanımlayabilmesi Için **persongroup** 'u eğitmeniz gerekir. Aşağıdaki kod, zaman uyumsuz **eğitme** yöntemini çağırır ve sonucu, durumu konsola yazdırarak tarar.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Yüz tanıma

Aşağıdaki kod, birden çok yüzü olan bir görüntü alır ve görüntüdeki her kişinin kimliğini bulmak için arama yapar. Algılanan her yüzü, yüz özellikleri bilinen farklı **kişi** nesnelerinin bir veritabanı olan bir **persongroup**ile karşılaştırır.

> [!IMPORTANT]
> Bu örneği çalıştırmak için, önce [bir kişi grubu oluştur ve eğitme](#create-and-train-a-person-group)bölümünde kodu çalıştırmanız gerekir.

### <a name="get-a-test-image"></a>Test görüntüsü al

Aşağıdaki kod, _Test-image-Person-Group. jpg_ görüntüsü için projenizin köküne bakar ve görüntüdeki yüzeyleri algılar. Bu görüntüyü, **Persongroup** Management için kullanılan görüntülerle bulabilirsiniz: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Yüzleri belirleme

**Tanımla** yöntemi, algılanan yüzlerin bir dizisini alır ve bunları bir **persongroup**ile karşılaştırır. Algılanan bir yüzü bir **kişiye**eşleştirebilir, sonucu kaydeder. Bu kod, ayrıntılı eşleşen sonuçları konsola yazdırır.

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

## <a name="take-a-snapshot-for-data-migration"></a>Veri geçişi için bir anlık görüntü alın

Anlık görüntüler özelliği, eğitilen **Grup**gibi kayıtlı yüz verilerinizi farklı bir Azure bilişsel hizmetler aboneliğine taşımanızı sağlar. Örneğin, ücretsiz bir deneme aboneliği kullanarak bir **Persongroup** nesnesi oluşturduysanız ve şimdi bunu ücretli bir aboneliğe geçirmek istiyorsanız bu özelliği kullanmak isteyebilirsiniz. Anlık görüntüler özelliğine geniş bir genel bakış için [yüz verilerinizi geçirme](../Face-API-How-to-Topics/how-to-migrate-face-data.md) konusuna bakın.

Bu örnekte, [bir kişi grubu oluşturma ve eğitme](#create-and-train-a-person-group)bölümünde oluşturduğunuz **persono grubunu** geçirirsiniz. Önce bu bölümü tamamlayabilir ya da kendi yüz veri yapısını kullanabilirsiniz.

### <a name="set-up-target-subscription"></a>Hedef aboneliği ayarla

İlk olarak, yüz kaynağına sahip ikinci bir Azure aboneliğine sahip olmanız gerekir; Bunu, [Kurulum](#setting-up) bölümündeki adımları izleyerek yapabilirsiniz. 

Sonra, betiğinizin en üstüne yakın olan aşağıdaki değişkenleri oluşturun. Ayrıca, Azure hesabınızın abonelik KIMLIĞI için yeni ortam değişkenleri ve yeni (hedef) hesabınızın anahtar, uç noktası ve abonelik KIMLIĞI için de oluşturmanız gerekir. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Hedef istemcinin kimliğini doğrulama

Betiğinizin ilerleyen kısımlarında, geçerli istemci nesneniz kaynak istemci olarak kaydedin ve hedef aboneliğiniz için yeni bir istemci nesnesinin kimliğini doğrulayın. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Anlık görüntü kullanma

Anlık görüntü işlemlerinin geri kalanı zaman uyumsuz bir işlev içinde gerçekleşir. 

1. İlk adım, özgün aboneliğinizin yüz verilerini geçici bir bulut konumuna kaydeden anlık görüntüyü **almak** için kullanılır. Bu yöntem, işlemin durumunu sorgulamak için kullandığınız bir KIMLIK döndürür.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Sonra, işlem tamamlanana kadar KIMLIĞI sorgulayın.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Bu kod, ayrı olarak tanımlamanız gereken `wait_for_operation` işlevini kullanır:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Zaman uyumsuz işleviniz için geri dönün. Hedef aboneliğinize yüz verilerinizi yazmak için **Uygula** işlemini kullanın. Bu yöntem de bir KIMLIK döndürür.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Daha sonra, işlem tamamlanana kadar KIMLIĞI sorgulamak için `wait_for_operation` işlevini kullanın.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Bu adımları tamamladıktan sonra, yeni (hedef) aboneliğinizden yüz veri yapılarına erişebilirsiniz.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı hızlı başlangıç dosyanızdaki `python` komutuyla çalıştırın.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Bu hızlı başlangıçta bir **kişilik grubu** oluşturduysanız ve onu silmek istiyorsanız, betiğinizdeki aşağıdaki kodu çalıştırın:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Bu hızlı başlangıçta anlık görüntü özelliğini kullanarak veri geçirdiyseniz, hedef abonelikte kayıtlı olan **Persongroup grubunu** da silmeniz gerekir.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Python için yüz kitaplığı 'nı kullanarak temel görevleri nasıl kullanacağınızı öğrendiniz. Daha sonra, kitaplık hakkında daha fazla bilgi edinmek için başvuru belgelerini inceleyin.

> [!div class="nextstepaction"]
> [Yüz Tanıma API'si Başvurusu (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Yüz Tanıma API'si nedir?](../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)' da bulunabilir.