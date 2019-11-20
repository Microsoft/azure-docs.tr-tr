---
title: 'Öğretici: Azure hizmetlerini tanımak için özel logo algılayıcısı kullanın-Özel Görüntü İşleme'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, logo algılama senaryosunun parçası olarak Özel Görüntü İşleme kullanan bir örnek uygulamada adım adım ilerleyebilirsiniz. Özel Görüntü İşleme, uçtan uca bir uygulama sunmak için diğer bileşenlerle nasıl kullanıldığını öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: b48d82354a8e733db5ddd0c86e34bab1fa9caa8d
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177784"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Öğretici: kamera resimlerde Azure hizmet logolarını tanıma

Bu öğreticide, daha büyük bir senaryonun parçası olarak Özel Görüntü İşleme kullanan bir örnek uygulama keşfedeceğiz. Mobil platformlar için bir Xamarin. Forms uygulaması olan AI görsel sağlama uygulaması, Azure hizmet logolarının kamera resimlerini analiz eder ve ardından gerçek Hizmetleri kullanıcının Azure hesabına dağıtır. Burada, yararlı bir uçtan uca uygulama sunmak için diğer bileşenlerle birlikte Özel Görüntü İşleme nasıl kullandığını öğreneceksiniz. Tüm uygulama senaryosunu kendiniz çalıştırabilirsiniz veya kurulumun yalnızca Özel Görüntü İşleme parçasını tamamlayabilir ve uygulamanın bunu nasıl kullandığını keşfedebilirsiniz.

Bu öğreticide şunları nasıl yapacağınızı gösterilecek:

> [!div class="checklist"]
> - Azure hizmet logolarını tanımak için özel nesne algılayıcısı oluşturun.
> - Uygulamanızı Azure Görüntü İşleme ve Özel Görüntü İşleme bağlayın.
> - Uygulamadan Azure hizmetlerini dağıtmak için bir Azure hizmet sorumlusu hesabı oluşturun.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. 

## <a name="prerequisites"></a>Önkoşullar

- [Visual Studio 2017 veya üzeri](https://www.visualstudio.com/downloads/)
- Visual Studio için Xamarin iş yükü (bkz. [Xamarin 'ı yükleme](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Visual Studio için iOS veya Android öykünücüsü
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (isteğe bağlı)

## <a name="get-the-source-code"></a>Kaynak kodunu al

Belirtilen Web uygulamasını kullanmak istiyorsanız, GitHub 'daki [AI görsel sağlama](https://github.com/Microsoft/AIVisualProvision) deposundan uygulamanın kaynak kodunu kopyalayın veya indirin. Visual Studio 'da *kaynak/VisualProvision. sln* dosyasını açın. Daha sonra, uygulamayı çalıştırmak için bazı proje dosyalarını düzenleyeceksiniz.

## <a name="create-an-object-detector"></a>Nesne algılayıcısı oluşturma

[Özel görüntü işleme web sitesinde](https://customvision.ai/) oturum açın ve yeni bir proje oluşturun. Bir nesne algılama projesi belirtin ve logo etki alanını kullanın; Bu, hizmetin logo algılama için iyileştirilmiş bir algoritma kullanmasına izin verir. 

![Chrome tarayıcısında Özel Görüntü İşleme Web sitesinde yeni-proje penceresi](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Görüntüleri karşıya yükleme ve etiketleme

Ardından, Azure hizmet logolarının görüntülerini karşıya yükleyerek ve bunları el ile etiketleyerek logo algılama algoritmasını eğitme. Aıvisualprovision deposu kullanabileceğiniz bir eğitim görüntüleri kümesi içerir. Web sitesinde, **eğitim görüntüleri** sekmesinde **Görüntü ekle** düğmesini seçin. Sonra deponun **Belgeler/görüntüler/Training_DataSet** klasörüne gidin. Her görüntüde logoları el ile etiketlemelisiniz, bu nedenle yalnızca bu projeyi test ediyorsanız, görüntülerin yalnızca bir alt kümesini karşıya yüklemek isteyebilirsiniz. Kullanmayı planladığınız her bir etiketin en az 15 örneğini karşıya yükleyin.

Eğitim görüntülerini karşıya yükledikten sonra, görüntüde ilk olanı seçin. Etiketleme penceresi görüntülenir. Her bir resimdeki her bir logo için kutular çizin ve Etiketler atayın. 

![Özel Görüntü İşleme Web sitesinde Logo etiketleme](media/azure-logo-tutorial/tag-logos.png)

Uygulama, belirli etiket dizeleriyle çalışacak şekilde yapılandırıldı. Tanımları *Source\visualprovision\services\recognıtion\recognıtionservice.cs* dosyasında bulacaksınız:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

Bir görüntüyü etiketledikten sonra, bir sonraki etiketi etiketlemek için sağa gidin. Bitirdiğinizde etiketleme penceresini kapatın.

## <a name="train-the-object-detector"></a>Nesne algılayıcısı 'nı eğitme

Görüntülerinizi göstermek için sol bölmede **Etiketler** ' i **etiketlendirilmiş** olarak ayarlayın. Ardından, modelin eğitilmesi için sayfanın en üstündeki yeşil düğmeyi seçin. Algoritma, yeni görüntülerde aynı etiketleri tanımak için eğitecektir. Ayrıca, doğruluk puanları oluşturmak için modeli mevcut görüntülerinizden bazıları üzerinde test eder.

![Eğitim görüntüleri sekmesinde Özel Görüntü İşleme Web sitesi. Bu ekran görüntüsünde eğitme düğmesi özetlenmiştir](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Tahmin URL 'sini al

Modelinize eğitim verdikten sonra, bu uygulamayı uygulamanızla tümleştirmeye hazırsınız demektir. Uç nokta URL 'sini (uygulamanın Sorgulayabileceğiniz) ve tahmin anahtarını (uygulamanın tahmin isteklerine erişmesini sağlamak için) almanız gerekir. **Performans** sekmesinde, sayfanın üst kısmındaki **tahmin URL 'si** düğmesini seçin.

![URL adresini ve API anahtarını görüntüleyen bir tahmin API penceresini gösteren Özel Görüntü İşleme Web sitesi](media/azure-logo-tutorial/cusvis-endpoint.png)

Endpoint URL 'sini ve **tahmin-anahtar** değerini *Source\visualprovision\appsettings.cs* dosyasındaki uygun alanlara kopyalayın:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>Özel Görüntü İşleme kullanımı inceleyin

Uygulamanın Özel Görüntü İşleme anahtarınızı ve uç nokta URL 'sini nasıl kullandığını görmek için *kaynak/VisualProvision/Services/tanıması/CustomVisionService. cs* dosyasını açın. **Predictımagecontentsasync** yöntemi bir görüntü dosyasının bayt akışını (zaman uyumsuz görev yönetimi için), özel görüntü işleme tahmin API 'sini çağırır ve tahmin sonucunu döndürür. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

Bu sonuç, kendisi bir **tahmin** örnekleri listesi Içeren bir **PredictionResult** örneği biçimini alır. **Tahmin** , görüntüde algılanan bir etiketi ve bunun sınırlama kutusu konumunu içerir.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

Uygulamanın bu verileri nasıl işleyeceği hakkında daha fazla bilgi edinmek için **Getresourcesasync** yöntemiyle başlayın. Bu yöntem, *kaynak/VisualProvision/Services/tanıması/Recognıtionservice. cs* dosyasında tanımlanmıştır.  

## <a name="add-computer-vision"></a>Görüntü İşleme Ekle

Öğreticinin Özel Görüntü İşleme kısmı tamamlanmıştır. Uygulamayı çalıştırmak istiyorsanız, Görüntü İşleme hizmetini de tümleştirmeniz gerekir. Uygulama, logo algılama işlemini tamamlamak için Görüntü İşleme metin tanıma özelliğini kullanır. Azure logosu, görünümü *veya* yakınında yazılı metin tarafından tanınır. Özel Görüntü İşleme modellerinden farklı olarak, Görüntü İşleme görüntülerde veya videolarda belirli işlemleri gerçekleştirmek için önceden eğitilmiş olur.

Anahtar ve uç nokta URL 'SI almak için Görüntü İşleme hizmetine abone olun. Bu adımla ilgili yardım için bkz. [abonelik anahtarları nasıl elde edilir](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![Azure portal, Hızlı Başlangıç menüsü seçili olan Görüntü İşleme hizmetidir. API uç noktası URL 'SI olduğu gibi anahtarlar için bir bağlantı özetlenmiştir](media/azure-logo-tutorial/comvis-keys.png)

Sonra *Source\visualprovision\appsettings.cs* dosyasını açın ve `ComputerVisionEndpoint` ve `ComputerVisionKey` değişkenlerini doğru değerlerle doldurun.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

Uygulamanın Azure aboneliğinize hizmet dağıtması için bir Azure hizmet sorumlusu hesabının olması gerekir. Hizmet sorumlusu, rol tabanlı erişim denetimi kullanarak bir uygulamaya belirli izinler atamanıza olanak tanır. Daha fazla bilgi edinmek için bkz. [hizmet sorumluları Kılavuzu](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

Burada gösterildiği gibi Azure Cloud Shell veya Azure CLı kullanarak bir hizmet sorumlusu oluşturabilirsiniz. Başlamak için oturum açın ve kullanmak istediğiniz aboneliği seçin.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Ardından hizmet sorumlunuzu oluşturun. (Bu işlemin tamamlanması biraz zaman alabilir.)

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Başarıyla tamamlandıktan sonra, gerekli kimlik bilgileri de dahil olmak üzere aşağıdaki JSON çıkışını görmeniz gerekir.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

@No__t-0 ve `tenantId` değerlerine göz atın. Bunları *Source\visualprovision\appsettings.cs* dosyasındaki uygun alanlara ekleyin.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Bu noktada, uygulama erişimini şu şekilde vermiş olursunuz:

- Eğitilen bir Özel Görüntü İşleme modeli
- Görüntü İşleme Hizmeti
- Hizmet sorumlusu hesabı

Uygulamayı çalıştırmak için aşağıdaki adımları izleyin:

1. Visual Studio Çözüm Gezgini içinde, **visualprovision. Android** projesini veya **Visualprovision. iOS** projesini seçin. Ana araç çubuğundaki açılan menüden ilgili öykünücü veya bağlı bir mobil cihaz seçin. Ardından uygulamayı çalıştırın.

    > [!NOTE]
    > Bir iOS öykünücüsü çalıştırmak için bir MacOS cihazı gerekir.

1. İlk ekranda, hizmet sorumlusu istemci KIMLIĞI, kiracı KIMLIĞI ve parolanızı girin. **Oturum aç** düğmesini seçin.

    > [!NOTE]
    > Bazı Öykünücülerde, **oturum açma** düğmesi bu adımda etkinleştirilmemiş olabilir. Bu durumda, uygulamayı durdurun, *kaynak/VisualProvision/Pages/LoginPage. xaml* dosyasını açın, **oturum açma düğmesi**etiketli `Button` öğesini bulun, aşağıdaki satırı kaldırın ve uygulamayı yeniden çalıştırın.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Hizmet sorumlusu kimlik bilgileri alanlarını gösteren uygulama ekranı](media/azure-logo-tutorial/app-credentials.png)

1. Sonraki ekranda, açılan menüden Azure aboneliğinizi seçin. (Bu menü, hizmet sorumlusunun erişimi olan tüm abonelikleri içermelidir.) **Devam** düğmesini seçin. Bu noktada, uygulama cihazın kameraya ve fotoğraf depolamaya erişim vermenizi isteyebilir. Erişim izinlerini verin.

    ![Hedef Azure aboneliğine yönelik bir açılan alan gösteren uygulama ekranı](media/azure-logo-tutorial/app-az-subscription.png)


1. Cihazınızdaki kamera etkinleştirilecek. Eğitilen Azure hizmet logolarının bir fotoğrafını alın. Dağıtım penceresi, yeni hizmetler için bir bölge ve kaynak grubu seçmenizi ister (Azure portal dağıtıyorsanız yaptığınız gibi). 

    ![Azure logolarının iki kağıda odaklanan bir smartphone kamera ekranı](media/azure-logo-tutorial/app-camera-capture.png)

    ![Dağıtım bölgesinin ve kaynak grubunun alanlarını gösteren bir uygulama ekranı](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu senaryonun tüm adımlarını izlediyseniz ve Azure hizmetlerini hesabınıza dağıtmak için uygulamayı kullandıysanız, [Azure Portal](https://ms.portal.azure.com/)gidin. Burada, kullanmak istemediğiniz Hizmetleri iptal edin.

Özel Görüntü İşleme ile kendi nesne algılama projenizi oluşturmayı planlıyorsanız, bu öğreticide oluşturduğunuz logo algılama projesini silmek isteyebilirsiniz. Özel Görüntü İşleme ücretsiz deneme sürümü yalnızca iki proje için izin verir. Logo algılama projesini silmek için, [özel görüntü işleme web sitesinde](https://customvision.ai) **Projeler** ' i açın ve **yeni projem**altında çöp kutusu simgesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Mobil kamera görüntülerinde logoları algılamak için Özel Görüntü İşleme hizmetini kullanan tam özellikli bir Xamarin. Forms uygulaması ayarlayabilir ve araştırın. Daha sonra, bir Özel Görüntü İşleme modeli oluşturmak için en iyi yöntemleri öğrenin, böylece kendi uygulamanız için bir tane oluşturduğunuzda, güçlü ve doğru hale getirebilirsiniz.

> [!div class="nextstepaction"]
> [Sınıflandırıcınızı geliştirme](getting-started-improving-your-classifier.md)
