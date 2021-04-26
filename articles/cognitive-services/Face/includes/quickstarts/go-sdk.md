---
title: Yüz yüze git istemci kitaplığı hızlı başlangıç
description: Yüzleri algılamak için yüz istemci kitaplığını kullanın, benzer (görüntüye göre arama), yüzleri (yüz tanıma arama) belirleyip yüz verilerinizi geçirin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: ab3fdd24446448e9c21a1e4867c26c960f814c7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958287"
---
Go için yüz istemci kitaplığını kullanarak yüz tanıma ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Yüz tanıma hizmeti, görüntülerdeki insan yüzlerini algılayıp tanımayı sağlayan gelişmiş algoritmalara erişmenizi sağlar.

Git için yüz hizmeti istemci kitaplığını kullan:

* [Bir görüntüdeki yüzleri algılama](#detect-faces-in-an-image)
* [Benzer yüzeyleri bulun](#find-similar-faces)
* [Bir PersonGroup oluşturma ve eğitme](#create-and-train-a-persongroup)
* [Yüz tanıma](#identify-a-face)

[Başvuru belgeleri](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face)  |  [SDK indirmesi](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Önkoşullar

* En son [Go](https://golang.org/dl/) sürümü
* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" bir yüz kaynağı oluşturun "  target="_blank"> </a> Azure Portal anahtar ve uç noktanıza ulaşmak için bir yüz kaynağı oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Yüz Tanıma API'si bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.
* Anahtar ve uç nokta aldıktan sonra, ve sırasıyla adlı anahtar ve uç nokta için [ortam değişkenleri oluşturun](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` .

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-go-project-directory"></a>Go proje dizini oluştur

Konsol penceresinde (cmd, PowerShell, Terminal, Bash), go projeniz için adlı yeni bir çalışma alanı oluşturun `my-app` ve buna gidin.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Çalışma alanınız üç klasör içerir:

* **src** -bu dizin, kaynak kodu ve paketleri içerir. Komutuyla yüklenen tüm paketler `go get` Bu klasörde olacaktır.
* **pkg** -bu dizin, derlenmiş go paketi nesnelerini içerir. Bu dosyaların hepsi bir `.a` uzantısı vardır.
* **bin** -bu dizin, çalıştırdığınızda oluşturulan ikili yürütülebilir dosyaları içerir `go install` .

> [!TIP]
> Go çalışma alanının yapısı hakkında daha fazla bilgi edinmek için [Go Language belgelerine](https://golang.org/doc/code.html#Workspaces)bakın. Bu kılavuz, ve ayarları hakkında bilgi içerir `$GOPATH` `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Go için istemci kitaplığını yükler

Ardından, Go için istemci kitaplığını yükleyeceksiniz:

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

ya da DEP kullanıyorsanız, deponuzda çalıştırın:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Go uygulaması oluşturma

Sonra, **src** dizininde adlı bir dosya oluşturun `sample-app.go` :

```bash
cd src
touch sample-app.go
```

`sample-app.go`Tercih ETTIĞINIZ IDE veya metin düzenleyicide açın. Ardından, paket adını ekleyin ve aşağıdaki kitaplıkları içeri aktarın:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Daha sonra, farklı yüz hizmeti işlemlerini gerçekleştirmek için kod eklemeye başlayacaksınız.

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, yüz hizmeti git istemci kitaplığı 'nın bazı önemli özelliklerini işler.

|Ad|Açıklama|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Bu sınıf, yüz hizmetini kullanma yetkinizi temsil eder ve tüm yüz işlevleri için buna ihtiyacınız vardır. Bunu Abonelik bilgileriniz ile birlikte başlatır ve diğer sınıfların örneklerini oluşturmak için kullanırsınız. |
|[İstemci](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Bu sınıf, insan yüzeyleri ile gerçekleştirebileceğiniz temel algılama ve tanıma görevlerini işler. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Bu sınıf, görüntüde tek bir yüz tarafından algılanan tüm verileri temsil eder. Yüz hakkında ayrıntılı bilgi almak için bu uygulamayı kullanabilirsiniz.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Bu sınıf, bir assıralanan yüz kümesini depolayan, bulutta depolanan çok **yönlü liste** yapılarını yönetir. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Bu sınıf, tek bir kişiye ait olan bir yüzey kümesini depolayan, bulutta depolanan **kişi** yapılarını yönetir.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Bu sınıf, bir dizi yönetilen **kişi** nesnesini depolayan, bulut ile depolanmış olan **persongroup** yapılarını yönetir. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Bu sınıf, anlık görüntü işlevselliğini yönetir. Tüm bulut tabanlı yüz verilerinizi geçici olarak kaydetmek ve bu verileri yeni bir Azure aboneliğine geçirmek için kullanabilirsiniz. |

## <a name="code-examples"></a>Kod örnekleri

Bu kod örnekleri, Go için yüz hizmeti istemci kitaplığını kullanarak temel görevleri nasıl tamamlayakullanacağınızı gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Bir görüntüdeki yüzleri algılama](#detect-faces-in-an-image)
* [Benzer yüzeyleri bulun](#find-similar-faces)
* [Bir PersonGroup oluşturma ve eğitme](#create-and-train-a-persongroup)
* [Yüz tanıma](#identify-a-face)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

> [!NOTE] 
> Bu hızlı başlangıçta, ve sırasıyla adlı yüz anahtarınız ve uç noktanız için [ortam değişkenleri oluşturduğunuz](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) varsayılır `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` .

Bir **ana** işlev oluşturun ve uç nokta ve anahtarınıza sahip bir istemci örneği oluşturmak için aşağıdaki kodu ekleyin. Anahtarınızla bir **[Biliveservicesauthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** nesnesi oluşturur ve bir **[istemci](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** nesnesi oluşturmak için bunu uç noktanızla birlikte kullanabilirsiniz. Bu kod ayrıca, istemci nesnelerinin oluşturulması için gerekli olan bir bağlam nesnesi oluşturur. Ayrıca, bu hızlı başlangıçta bulunan örnek görüntülerin bazılarının bulunduğu uzak konumu tanımlar.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Bir görüntüdeki yüzleri algılama

Aşağıdaki kodu **Main** yöntemine ekleyin. Bu kod, uzak bir örnek görüntüyü tanımlar ve görüntüden Ayıklanacak yüz özelliklerini belirtir. Ayrıca, algılanan yüzlerden veri ayıklamak için hangi AI modelinin kullanılacağını belirtir. Bu seçenekler hakkında bilgi için bkz. [bir tanıma modeli belirtme](../../Face-API-How-to-Topics/specify-recognition-model.md) . Son olarak, **[Detectwithurl](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** yöntemi görüntüde yüz algılama işlemini yapar ve sonuçları program belleğine kaydeder.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

> [!TIP]
> Ayrıca, yerel görüntüdeki yüzeyleri de algılayabilirsiniz. **Detectwithstream** gibi [istemci](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client) yöntemlerine bakın.

### <a name="display-detected-face-data"></a>Algılanan yüz verileri görüntüle

Sonraki kod bloğu, **[Detectedface](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** nesneleri dizisindeki ilk öğeyi alır ve özniteliklerini konsola yazdırır. Birden çok yüzü olan bir görüntü kullandıysanız, bunun yerine dizide yineleme yapmanız gerekir.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Benzer yüzleri bulma

Aşağıdaki kod, algılanan tek bir yüzeyi (kaynak) alır ve eşleşmeleri (yüz görüntüye göre arama) bulmak için bir dizi diğer yüzeyi (hedef) arar. Bir eşleşme bulduğunda, eşleşen yüzün KIMLIĞINI konsola yazdırır.

### <a name="detect-faces-for-comparison"></a>Karşılaştırılacak Yüzleri Algıla

İlk olarak, [bir görüntüde yüzeyleri Algıla](#detect-faces-in-an-image) bölümünde karşılaştığınız yüze bir başvuruyu kaydedin. Bu yüz kaynak olacaktır.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Ardından, farklı bir görüntüdeki bir yüzey kümesini algılamak için aşağıdaki kodu girin. Bu yüzlerin hedefi olacaktır.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Eşleşmeleri bul

Aşağıdaki kod, kaynak yüzü ile eşleşen tüm hedef yüzlerini bulmak için **[Findbenzerdir](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** yöntemini kullanır.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Yazdırma eşleşmeleri

Aşağıdaki kod, eşleşme ayrıntılarını konsola yazdırır.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-persongroup"></a>Bir PersonGroup oluşturma ve eğitme

Bu senaryoda ilerlemek için aşağıdaki görüntüleri projenizin kök dizinine kaydetmeniz gerekir: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

Bu görüntü grubu üç farklı kişiye karşılık gelen üç tek yüz görüntü kümesini içerir. Kod, üç **kişilik kişi** nesnesi tanımlar ve bunları, ve ile başlayan resim dosyalarıyla ilişkilendirir `woman` `man` `child` .

### <a name="create-persongroup"></a>Kişilik grubu oluştur

Görüntülerinizi indirdikten sonra, **ana** yönteminizin en altına aşağıdaki kodu ekleyin. Bu kod, bir **[Persongroupclient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** nesnesinin kimliğini doğrular ve ardından yeni bir **kişilik grubu** tanımlamak için kullanır.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>PersonGroup kişileri oluşturma

Sonraki kod bloğu bir **[Persongrouppersonclient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** kimliğini doğrular ve bunu üç yeni **persongroup kişi** nesnesi tanımlamak için kullanır. Bu nesnelerin her biri görüntüler kümesindeki tek bir kişiyi temsil eder.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Kişilere yüz atama

Aşağıdaki kod, görüntüleri ön ekine göre sıralar, yüzleri algılar ve görüntü dosyası adına göre her bir ilgili **kişi** nesnesine yüzleri atar.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

> [!TIP]
> URL tarafından başvurulan uzak görüntülerden de bir **Persongroup** oluşturabilirsiniz. **Addfacefromurl** gibi [Persongrouppersonclient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient) yöntemlerine bakın.

### <a name="train-persongroup"></a>Kişilik grubu eğitme

Yüzleri atadıktan sonra, kişi nesnelerinden her biriyle ilişkili görsel özelliklerini tanımlayabilmesi için **sorumlu** **grubunu** eğitebilirsiniz. Aşağıdaki kod, zaman uyumsuz **eğitme** yöntemini çağırır ve sonucu, durumu konsola yazdırarak tarar.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

> [!TIP]
> Yüz Tanıma API'si, doğası gereği statik olan önceden oluşturulmuş modeller kümesi üzerinde çalışır (modelin performansı, hizmet çalıştırıldığı sürece bu şekilde çalışmaz veya geliştirilecektir). Modelin ürettiği sonuçlar, Microsoft modelin arka ucunu tamamen yeni bir model sürümüne geçirmeden güncelleştirse de değişiklik gösterebilir. Bir modelin daha yeni bir sürümünden yararlanmak için, daha yeni modeli aynı kayıt görüntülerine sahip bir parametre olarak belirterek, **Personal grubunuzu** yeniden eğitebilirsiniz.

## <a name="identify-a-face"></a>Yüz tanıma

Tanımlama işlemi, bir kişinin (veya birden çok kişinin) bir görüntüsünü alır ve görüntüdeki her bir yüzün kimliğini bulmak için (yüz tanıma arama) arar. Algılanan her yüzü, yüz özellikleri bilinen farklı **kişi** nesnelerinin bir veritabanı olan bir **persongroup** ile karşılaştırır.

> [!IMPORTANT]
> Bu örneği çalıştırmak için, önce [bir PersonGroup oluşturma ve eğitme](#create-and-train-a-persongroup)içinde kodu çalıştırmanız gerekir.

### <a name="get-a-test-image"></a>Test görüntüsü al

Aşağıdaki kod, bir görüntü _test-image-person-group.jpg_ projenizin köküne bakar ve program belleğine yükler. Bu görüntüyü, [bir PersonGroup oluşturma ve eğitme](#create-and-train-a-persongroup)bölümünde kullanılan görüntülerle aynı depoya bulabilirsiniz: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Test görüntüsündeki kaynak Yüzleri Algıla

Sonraki kod bloğu, tüm yüzeyleri almak ve bunları bir diziye kaydetmek için test görüntüsünde sıradan yüz algılamayı yapar.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Yüzleri belirleme

**[Tanımla](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** yöntemi, algılanan yüzlerin dizisini alır ve bunları verilen **persongroup** ile karşılaştırır (daha önceki bölümde tanımlanan ve eğitilen). Algılanan bir yüzü gruptaki bir **kişiyle** eşleştirebilir, sonucu kaydeder.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Bu kod daha sonra ayrıntılı eşleşen sonuçları konsola yazdırır.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Yüzeyleri doğrula

Doğrulama işlemi bir yüz KIMLIĞI ve başka bir yüz KIMLIĞI ya da bir **kişi** nesnesi alır ve aynı kişiye ait olup olmadığını belirler.

Aşağıdaki kod, iki kaynak görüntüde yüzeyleri algılar ve her birini hedef görüntüden algılanan bir yüze karşı doğrular.

### <a name="get-test-images"></a>Test görüntülerini al

Aşağıdaki kod blokları, doğrulama işleminin hedef ve kaynak görüntülerini işaret edecek değişkenleri bildirir.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Doğrulama için yüzeyleri Algıla

Aşağıdaki kod, kaynak ve hedef görüntülerdeki yüzeyleri algılar ve bunları değişkenlere kaydeder.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Doğrulama sonuçlarını al

Aşağıdaki kod, kaynak görüntülerinin her birini hedef görüntüye karşılaştırır ve aynı kişiye ait olup olmadığını belirten bir ileti yazdırır.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Yüz tanıma uygulamanızı, komutuyla birlikte uygulama dizininden çalıştırın `go run <app-name>` .

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Bu hızlı başlangıçta bir **kişilik grubu** oluşturduysanız ve silmek Istiyorsanız, **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** yöntemini çağırın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, yapılacaklar tabanlı yüz tanıma görevlerine gitmek için yüz istemci kitaplığı 'nı nasıl kullanacağınızı öğrendiniz. Daha sonra, kitaplık hakkında daha fazla bilgi edinmek için başvuru belgelerini inceleyin.

> [!div class="nextstepaction"]
> [Yüz Tanıma API'si Başvurusu (git)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Yüz Tanıma hizmeti nedir?](../../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go)' da bulunabilir.