---
title: 'Öğretici: Android SDK görüntüdeki yüzeyleri Algıla ve çerçevele'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, bir görüntüdeki yüzeyleri algılamak ve çerçeveye eklemek için Yüz Tanıma API'si kullanan basit bir Android uygulaması oluşturacaksınız.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: pafarley
ms.openlocfilehash: 740b3fae81521fec2cba31e3b8fd161f767c4380
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858977"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Öğretici: Görüntüdeki yüzeyleri algılamak ve çerçeveye eklemek için bir Android uygulaması oluşturma

Bu öğreticide, bir görüntüdeki insan yüzlerini algılamak için, Java SDK 'Sı aracılığıyla Azure Yüz Tanıma API'si kullanan basit bir Android uygulaması oluşturacaksınız. Uygulama, seçilen görüntü görüntüler ve algılanan her yüz etrafında bir çerçeve çizer.

Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> - Android uygulaması oluşturma
> - Yüz Tanıma API'si istemci kitaplığını yükler
> - İstemci kitaplığını kullanarak resimdeki yüzleri algılama
> - Algılanan her yüzün çevresine bir çerçeve çizme

![Yüzleri kırmızı dikdörtgenle çerçeve içine alınmış bir fotoğrafın Android ekran görüntüsü](../Images/android_getstarted2.1.PNG)

Örnek kodun tamamı GitHub 'daki bilişsel [Hizmetler yüz Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) deposunda bulunur.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. 

## <a name="prerequisites"></a>Önkoşullar

- Yüz tanıma API'si abonelik anahtarı. Ücretsiz deneme aboneliği anahtarından alabilirsiniz [Bilişsel Hizmetler'i deneyin](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Veya yönergeleri [Bilişsel Hizmetler hesabı oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) yüz tanıma API'si hizmete abone ve anahtarınızı alın. Ardından, sırasıyla ve `FACE_ENDPOINT`olarak adlandırılan `FACE_SUBSCRIPTION_KEY` anahtar ve hizmet uç noktası dizesi için [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .
- [Visual Studio 2015 veya 2017](https://www.visualstudio.com/downloads/)'nin herhangi bir sürümü.
- [ANDROID STUDIO](https://developer.android.com/studio/) API düzeyi 22 veya üzeri (yüz istemci kitaplığı için gereklidir).

## <a name="create-the-android-studio-project"></a>Android Studio projesi oluşturma

Yeni bir Android uygulaması projesi oluşturmak için aşağıdaki adımları izleyin.

1. Android Studio **Yeni bir Android Studio projesi Başlat**' ı seçin.
1. **Android Projesi Oluştur** ekranında, gerekirse varsayılan alanları değiştirin ve sonra da **İleri**'ye tıklayın.
1. **Hedef Android cihazları** ekranında, **API 22** veya üzerini seçmek için açılan seçiciyi kullanın, ardından **İleri**' ye tıklayın.
1. **Boş Etkinlik** öğesini seçin ve **İleri**'ye tıklayın.
1. **Geriye Dönük Uyumluluk** öğesinin işaretini kaldırın ve **Son**'a tıklayın.

## <a name="add-the-initial-code"></a>Başlangıç kodunu ekleme

### <a name="create-the-ui"></a>Kullanıcı arabirimini oluşturma

*Activity_main. xml*' i açın. Düzen Düzenleyicisi 'nde **metin** sekmesini seçin, sonra içeriği aşağıdaki kodla değiştirin.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>Ana sınıfı oluşturma

*MainActivity. Java* ' yı açın ve var `import` olan deyimleri aşağıdaki kodla değiştirin.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Ardından, **MainActivity** sınıfının içeriğini aşağıdaki kodla değiştirin. Bu, kullanıcının bir resim seçmesine izin vermek için yeni bir etkinlik başlatan **düğme** üzerinde bir olay işleyicisi oluşturur. Resmi **ImageView**içinde görüntüler.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Uygulamayı deneyin

**Onactivityresult** yönteminde **detectandframe** çağrısını not edin. Ardından, uygulamanızı test etmek için menüdeki **Çalıştır** ' a basın. Uygulama açıldığında, öykünücü veya bağlı bir cihazda, en alta **gözatıp** ' ye tıklayın. Cihazın dosya seçimi iletişim kutusu görünmelidir. Bir görüntü seçin ve pencerede görüntülendiğini doğrulayın. Ardından, uygulamayı kapatın ve sonraki adıma ilerleyin.

![Yüzler içeren bir fotoğrafın Android ekran görüntüsü](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Yüz SDK 'sını ekleme

### <a name="add-the-gradle-dependency"></a>Gradle bağımlılığını ekleme

**Proje** bölmesinde, aşağı açılan seçiciyi kullanarak **Android**'i seçin. **Gradle Scripts**'i genişletin, sonra *build.gradle (Modül: uygulama)* öğesini açın. Aşağıdaki ekran görüntüsünde gösterildiği gibi Yüz Tanıma istemci kitaplığı `com.microsoft.projectoxford:face:1.4.3` için bir bağımlılık ekleyin, sonra da **Şimdi Eşitle**'ye tıklayın.

![Uygulama build.gradle dosyasının Android Studio ekran görüntüsü](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Yüz ile ilgili proje kodunu ekleme

**MainActivity. Java** ' ya dönün ve aşağıdaki `import` deyimleri ekleyin:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Ardından, aşağıdaki kodu **MainActivity** sınıfına, **OnCreate** yönteminin üstüne ekleyin:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

**Proje** bölmesinde **uygulamayı**, sonra da **bildirimleri** genişletin ve *AndroidManifest.xml* dosyasını açın. Aşağıdaki öğeyi `manifest` öğesinin doğrudan alt öğesi olarak ekleyin:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Görüntüyü karşıya yükleme ve yüzeyleri algılama

Uygulamanız, [algıla](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API sarmalayan ve bir **yüz** örnekleri listesi döndüren **Faceclient. Face. detectwithstreamasync** yöntemini çağırarak yüzeyleri algılar.

Döndürülen her **yüz** , konumunu belirten, isteğe bağlı bir yüz öznitelikleri serisiyle birleştirilmiş bir dikdörtgen içerir. Bu örnekte, yalnızca yüz dikdörtgenler istenir.

Aşağıdaki iki yöntemi **MainActivity** sınıfına ekleyin. Yüz Algılama tamamlandığında, uygulamanın **ImageView**öğesini değiştirmek Için **Drawfacerectanglesonbitmap** metodunu çağırdığına bakın. Bu yöntemi bir sonraki adımda tanımlayacaksınız.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Yüz tanıma dikdörtgenler çizme

Aşağıdaki yardımcı yöntemi **MainActivity** sınıfına ekleyin. Bu yöntem her bir **yüz** örneğinin dikdörtgen koordinatlarını kullanarak algılanan her bir yüzey etrafında bir dikdörtgen çizer.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Son olarak, **Onactivityresult**Içindeki **detectandframe** yöntemine yapılan çağrının açıklamasını kaldırın.

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Uygulamayı çalıştırın ve içinde yüzlerin yer aldığı bir resim bulun. Yüz Tanıma hizmetinin yanıt vermesi için birkaç saniye bekleyin. Görüntüdeki her yüzey üzerinde kırmızı bir dikdörtgen görmeniz gerekir.

![Kırmızı dikdörtgenlerin çevresinde çizmiş yüzlerinin Android ekran görüntüsü](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Yüz Tanıma API'si Java SDK 'sını kullanmaya yönelik temel süreci öğrenmiş ve bir görüntüdeki yüzeyleri algılayıp çerçevele almak için bir uygulama oluşturdunuz. Sonra, yüz algılama 'nın ayrıntıları hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Resimdeki Yüzleri Tanıma](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
