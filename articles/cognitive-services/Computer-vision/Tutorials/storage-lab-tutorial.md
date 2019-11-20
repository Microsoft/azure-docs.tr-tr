---
title: 'Öğretici: Azure görüntüleri için meta veriler oluşturma'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, görüntülerin meta verilerini oluşturmak için Azure Görüntü İşleme hizmetini bir Web uygulamasıyla tümleştirmeyi öğreneceksiniz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: pafarley
ms.openlocfilehash: ac292f020bb64c7c70ce3ea5c7f66fe9e9ed1bb7
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73604649"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Öğretici: Azure Storage 'da görüntü meta verileri oluşturmak için Görüntü İşleme kullanma

Bu öğreticide, karşıya yüklenen görüntülerin meta verilerini oluşturmak için Azure Görüntü İşleme hizmetini bir Web uygulamasıyla tümleştirmeyi öğreneceksiniz. Bu, bir şirketin tüm görüntüleri için hızlı bir şekilde açıklayıcı açıklamalı alt yazılar veya aranabilir anahtar sözcükler oluşturmak istemesi gibi [dijital varlık yönetimi (Dam)](../Home.md#computer-vision-for-digital-asset-management) senaryolarında kullanışlıdır.

GitHub 'daki [Azure depolama ve bilişsel hizmetler laboratuvarında](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) tam bir uygulama kılavuzu bulunabilir ve bu öğretici aslında laboratuvarın 5. alıştırisini ele alır. Her adımı izleyerek uçtan uca uygulamayı oluşturmak isteyebilirsiniz, ancak Görüntü İşleme var olan bir Web uygulamasıyla nasıl tümleştirilediğinin görmek isterseniz, buradan okuyun.

Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> * Azure 'da bir Görüntü İşleme kaynağı oluşturma
> * Azure depolama görüntülerinde görüntü analizini gerçekleştirme
> * Azure depolama görüntülerine meta veri iliştirme
> * Azure Depolama Gezgini kullanarak görüntü meta verilerini denetleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. 

## <a name="prerequisites"></a>Ön koşullar

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) veya üzeri, "ASP.net ve Web geliştirme" ve "Azure geliştirme" iş yükleri yüklendi.
- Görüntüler için ayrılmış bir blob kapsayıcısına sahip bir Azure depolama hesabı (bu adımla ilgili yardıma ihtiyacınız varsa [Azure Storage laboratuvarının 1. Alıştırmalarınızı](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) izleyin).
- Azure Depolama Gezgini Aracı (bu adımla ilgili yardıma ihtiyacınız varsa [Azure Storage laboratuvarının 2. Alıştırmayı](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) izleyin).
- Azure depolama 'ya erişimi olan bir ASP.NET Web uygulaması (böyle bir uygulamayı hızlı bir şekilde oluşturmak için [Azure Storage laboratuvarının 3. alıştırmaya](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) uyun).

## <a name="create-a-computer-vision-resource"></a>Görüntü İşleme kaynağı oluşturma

Azure hesabınız için bir Görüntü İşleme kaynağı oluşturmanız gerekir; Bu kaynak, Azure Görüntü İşleme hizmetine erişiminizi yönetir. 

1. Bir Görüntü İşleme kaynağı oluşturmak için Azure bilişsel [Hizmetler oluşturma](../../cognitive-services-apis-create-account.md) bölümündeki yönergeleri izleyin.

1. Sonra kaynak grubunuzun menüsüne gidin ve yeni oluşturduğunuz Görüntü İşleme API'si aboneliğine tıklayın. **Uç nokta** altındaki URL 'yi bir yere kopyalamak için bir süre içinde kolayca alabilirsiniz. Ardından **erişim anahtarlarını göster**' e tıklayın.

    ![Uç nokta URL 'SI ve erişim anahtarları bağlantısı olan Azure portal sayfası](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. Sonraki pencerede, **anahtar 1** değerini panoya kopyalayın.

    ![Anahtarları Yönet iletişim kutusu, Kopyala düğmesi ana hatlarıyla](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Görüntü İşleme kimlik bilgileri ekle

Daha sonra, Görüntü İşleme kaynaklarına erişebilmeleri için uygulamanıza gerekli kimlik bilgilerini ekleyeceksiniz

Visual Studio 'da ASP.NET Web uygulamanızı açın ve projenin kökündeki **Web. config** dosyasına gidin. Aşağıdaki deyimlerini dosyanın `<appSettings>` bölümüne ekleyin, `VISION_KEY` önceki adımda kopyaladığınız anahtarla değiştirin ve yukarıdaki adımda kaydettiğiniz URL ile `VISION_ENDPOINT`.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Ardından Çözüm Gezgini, projeye sağ tıklayın ve **Microsoft. Azure. Biliveservices. Vision. ComputerVision**paketini yüklemek Için **NuGet Paketlerini Yönet** komutunu kullanın. Bu paket, Görüntü İşleme API'si çağırmak için gereken türleri içerir.

## <a name="add-metadata-generation-code"></a>Meta veri oluşturma kodu ekle

Daha sonra, görüntüler için meta veriler oluşturmak üzere Görüntü İşleme hizmetinden yararlanan kodu eklersiniz. Bu adımlar laboratuvardaki ASP.NET uygulaması için geçerlidir, ancak bunları kendi uygulamanıza uyarlayabilirsiniz. Bu noktada, Azure depolama kapsayıcısına görüntü yükleyebilecekleri ve bunları görünümde görüntüleyebilen bir ASP.NET Web uygulamasına sahip olduğunuz önemli şeyler vardır. Bunun hakkında emin değilseniz, [Azure depolama laboratuvarının 3. Alıştırmayı](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3)izlemeniz en iyisidir. 

1. *HomeController.cs* dosyasını projenin **denetleyiciler** klasöründe açın ve dosyanın en üstüne aşağıdaki `using` deyimlerini ekleyin:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Sonra, **karşıya yükleme** yöntemine gidin; Bu yöntem görüntüleri blob depolamaya dönüştürür ve yükler. `// Generate a thumbnail` (veya görüntü blobu oluşturma işleminizin sonunda) ile başlayan bloğundan hemen sonra aşağıdaki kodu ekleyin. Bu kod, görüntüyü içeren blobu alır (`photo`) ve bu görüntü için bir açıklama oluşturmak üzere Görüntü İşleme kullanır. Görüntü İşleme API'si Ayrıca görüntüye uygulanan anahtar sözcüklerin bir listesini oluşturur. Oluşturulan açıklama ve anahtar sözcükler, daha sonra alınabilmesi için Blobun meta verilerinde depolanır.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Ardından, aynı dosyadaki **Dizin** yöntemine gidin; Bu yöntem, hedeflenen blob kapsayıcısında depolanan görüntü bloblarını numaralandırır ( **ıblobitem** örnekleri olarak) ve bunları uygulama görünümüne geçirir. Bu yöntemdeki `foreach` bloğunu aşağıdaki kodla değiştirin. Bu kod, her Blobun ekli meta verilerini almak için **Cloudblockblob. FetchAttributes** öğesini çağırır. Bilgisayar tarafından oluşturulan açıklamayı (`caption`) meta verilerden ayıklar ve görünüme geçirilen **Bloınfo** nesnesine ekler.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>Uygulamayı test edin

Değişikliklerinizi Visual Studio 'da kaydedin ve uygulamayı tarayıcınızda başlatmak için **CTRL + F5** tuşlarına basın. Laboratuvar kaynaklarındaki veya kendi klasörünüzdeki "Fotoğraflar" klasöründen birkaç görüntüyü karşıya yüklemek için uygulamayı kullanın. İmleci görünümdeki görüntülerden birinin üzerine getirdiğinizde, bir araç ipucu penceresi görünür ve görüntü için bilgisayar tarafından oluşturulan resim yazısını görüntülemelidir.

![Bilgisayar tarafından oluşturulan başlık](../Images/thumbnail-with-tooltip.png)

Tüm ekli meta verileri görüntülemek için, görüntüler için kullanmakta olduğunuz depolama kapsayıcısını görüntülemek için Azure Depolama Gezgini kullanın. Kapsayıcıdaki bloblara sağ tıklayın ve **Özellikler**' i seçin. İletişim kutusunda, anahtar-değer çiftlerinin bir listesini görürsünüz. Bilgisayar tarafından oluşturulan görüntü açıklaması öğede "Caption" içinde depolanır ve arama anahtar sözcükleri "Tag0," "Etiket1" içinde depolanır. İşiniz bittiğinde, iletişim kutusunu kapatmak için **iptal** ' e tıklayın.

![Resim özellikleri iletişim penceresi, meta veri etiketleri listelendi](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Web uygulamanızda çalışmaya devam etmek istiyorsanız, [sonraki adımlar](#next-steps) bölümüne bakın. Bu uygulamayı kullanmaya devam etmeyi planlamıyorsanız, uygulamaya özgü tüm kaynakları silmeniz gerekir. Bunu yapmak için, Azure depolama aboneliğinizi ve Görüntü İşleme kaynağını içeren kaynak grubunu silmeniz yeterlidir. Bu işlem, depolama hesabını, ona yüklenen Blobları ve ASP.NET Web uygulamasına bağlanmak için gereken App Service kaynağını kaldırır. 

Kaynak grubunu silmek için portalda **kaynak grupları** dikey penceresini açın, bu proje için kullandığınız kaynak grubuna gidin ve görünümün en üstündeki **kaynak grubunu sil** ' e tıklayın. Silinen bir kaynak grubu kurtarılamayacağından, silmek istediğinizi onaylamak için kaynak grubunun adını yazmanız istenir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure 'un Görüntü İşleme hizmetini mevcut bir Web uygulamasıyla tümleştirerek, karşıya yüklenen blob görüntüleri için otomatik olarak açıklamalı alt yazılar ve anahtar sözcükler oluşturabilirsiniz. Daha sonra, Web uygulamanıza nasıl arama işlevselliği ekleneceğini öğrenmek için Azure depolama Laboratuvarı, 6. alıştırma bölümüne bakın. Bu, Görüntü İşleme hizmetinin oluşturduğu arama anahtar sözcüklerinden yararlanır.

> [!div class="nextstepaction"]
> [Uygulamanıza arama ekleyin](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
