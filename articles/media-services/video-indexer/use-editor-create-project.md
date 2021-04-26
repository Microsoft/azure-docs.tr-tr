---
title: Video Indexer düzenleyicisini kullanarak projeler oluşturun ve video klipleri ekleyin
titleSuffix: Azure Media Services
description: Bu konu başlığı altında, proje oluşturmak ve video klipleri eklemek için Video Indexer Düzenleyicisi 'nin nasıl kullanılacağı gösterilmektedir.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: 07e4b05e12a5994c707a171c5736aea04a9c9723
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632935"
---
# <a name="add-video-clips-to-your-projects"></a>Projelerinize video klipleri ekleyin

[Video Indexer](https://www.videoindexer.ai/) Web sitesi, videonuzun derin öngörülerinizi kullanarak şunları yapmanızı sağlar: doğru medya içeriğini bulur, ilgilendiğiniz bölümleri bulabilir ve sonuçları kullanarak tamamen yeni bir proje oluşturabilirsiniz. 

Oluşturulduktan sonra proje, Video Indexer oluşturulabilir ve indirilebilir ve kendi Düzenle uygulamalarınızda veya aşağı akış akışlarınızda kullanılabilir.

Bu özelliği yararlı bulabileceğiniz bazı senaryolar şunlardır: 

* Tanıtım için film vurguları oluşturma.
* Haber yayınlarına daha eski video klipleri kullanma.
* Sosyal medya için daha kısa içerik oluşturma.

Bu makalede, projenin nasıl oluşturulacağı ve seçili kliplerin videolardan projeye nasıl ekleneceği gösterilmektedir. 

## <a name="create-new-project-and-manage-videos"></a>Yeni proje oluşturma ve videoları yönetme

1. [Video Indexer](https://www.videoindexer.ai/) web sitesine gidip oturum açın.
1. **Projeler** sekmesini seçin. Daha önce projeler oluşturduysanız, burada diğer projelerinizi görürsünüz.
1. **Yeni proje oluştur**' a tıklayın.  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="Yeni proje oluşturma":::
1. Kalem simgesine tıklayarak projenize bir ad verin. "Başlıksız proje" yazan metni proje adınızla değiştirin ve onay düğmesine tıklayın.

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="Yeni bir proje":::
    
### <a name="add-videos-to-the-project"></a>Projeye video ekleyin

> [!NOTE]
> Şu anda projeler yalnızca aynı dilde dizinli videolar içerebilir. </br>Bir dilde video seçtikten sonra, farklı bir dilde hesabınızdaki videoları ekleyemezsiniz, diğer dillere sahip videolar gri kalır/devre dışı bırakılır.

1. **Video ekle** seçeneğini belirleyerek bu projede birlikte çalışmak istediğiniz videoları ekleyin.

    Hesabınızdaki tüm videoları ve "metin, anahtar sözcükleri veya görsel içerik arama" yazılı bir arama kutusunu görürsünüz. Belirli bir kişiye, etikete, markaya, anahtar sözcüğe veya betiğe ve OCR 'ye sahip olan videolar için arama yapabilirsiniz.
    
    Örneğin, aşağıdaki görüntüde yalnızca "özel Vision" ifadesini içeren videoları aradık (arama sonuçlarınızı filtrelemek istiyorsanız **filtre** kullanın).
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="Ekran görüntüsü, özel vizyona ilişkin videoları aramayı gösterir":::
1. Projeye video eklemek için **Ekle** ' ye tıklayın.
1. Şimdi, seçtiğiniz tüm videoları görürsünüz. Bunlar, projeniz için klip seçeceğim olan videolardır.

    Video sırasını sürükleyip bırakarak veya liste menü düğmesini seçip **aşağı taşı** veya **Yukarı taşı**' yı seçerek yeniden düzenleyebilirsiniz. Liste menüsünde, bu projeden videoyu da kaldırabileceksiniz. 
    
    **Video ekle**' ye tıklayarak istediğiniz zaman bu projeye daha fazla video ekleyebilirsiniz. Ayrıca, projenize aynı videonun birden fazla örneğini de ekleyebilirsiniz. Bir videodan klip ve sonra başka bir klibin bir kırpından sonra ilk videodan başka bir klip göstermek istiyorsanız bunu yapmak isteyebilirsiniz. 

### <a name="select-clips-to-use-in-your-project"></a>Projenizde kullanılacak klipleri seçin

Her videonun sağ tarafındaki aşağı oka tıklarsanız, videoda zaman damgalarına göre (videonun klipleri) öngörüleri açarsınız. 

1. Belirli klipler için sorgular oluşturmak üzere "yazılı betik, görsel metin, kişiler ve etiketlerde arama" ifadesini içeren arama kutusunu kullanın.
1. Hangi öngörülerin görmek istediğinizi ve görmek istediğinizi özelleştirmek için **öngörüleri görüntüle** ' yi seçin. 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="Ekran görüntüsü, TRY bilişsel hizmetler ile ilgili videoları aramayı gösterir":::
1. **Filtre seçeneklerini** belirleyerek Aradığınız sahnelerin ayrıntılarını daha fazla belirtmek için filtre ekleyin.

    Birden çok filtre ekleyebilirsiniz. 
1. Sonuçlardan memnun olduktan sonra eklemek istediğiniz segmenti seçerek bu projeye eklemek istediğiniz klipleri seçin. Segmente yeniden tıklayarak bu klibin seçimini kaldırabilirsiniz.
    
    Videonun yanındaki liste menüsü seçeneğine tıklayıp **Tümünü Seç**' i seçerek bir videonun tüm segmentlerini (veya aramanızla sonra döndürülen tümü) ekleyin. 

Kliplerinizi seçip sıralamayı yaparken, sayfanın sağ tarafındaki Player 'da videoyu önizleyebilirsiniz. 

> [!IMPORTANT]
> Projeyi sayfanın en üstünde **Kaydet** ' i seçerek projenizi kaydetmeyi unutmayın. 

### <a name="render-and-download-the-project"></a>Projeyi işle ve indir

> [!NOTE]
> Video Indexer ücretli hesaplar için, projenizin işlenmesi kodlama maliyetlerine sahiptir. Video Indexer deneme hesapları, 5 saatlik işleme sınırlıdır.

1. İşiniz bittiğinde projenizin kaydedildiğinden emin olun. Artık bu projeyi işleyebilirsiniz. **İşle**' ye tıklayın, video dizin oluşturucunun bir dosyayı işlemesini ve ardından indirme bağlantısının e-postanıza gönderileceğini bildiren bir açılan pencere iletişim kutusu görüntülenir. Devam ' ı seçin. 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="Ekran görüntüsü, projenizi oluşturma ve indirme seçeneği ile Video Indexer gösterir":::
    
    Ayrıca, projenin sayfanın üstünde oluşturulmakta olduğunu belirten bir bildirim görürsünüz. İşlem tamamlandıktan sonra, projenin başarıyla işlenmiş olduğunu belirten yeni bir bildirim görürsünüz. Projeyi indirmek için bildirime tıklayın. Projeyi MP4 biçiminde indirir.
1. Kaydedilmiş projelere **Projeler** sekmesinden erişebilirsiniz. 

    Bu projeyi seçerseniz, bu projenin tüm öngörülerini ve zaman çizelgesini görürsünüz. **Video Düzenleyicisi**' ni seçerseniz bu projede düzenleme yapmaya devam edebilirsiniz. Düzenlemeler arasında video ve klip ekleme veya kaldırma ya da projeyi yeniden adlandırma dahildir.
    
## <a name="create-a-project-from-your-video"></a>Videoınızdan bir proje oluşturun

Hesabınızdaki bir videodan doğrudan yeni bir proje oluşturabilirsiniz. 

1. Video Indexer Web sitesinin **kitaplık** sekmesine gidin.
1. Projenizi oluşturmak için kullanmak istediğiniz videoyu açın. Öngörüler ve zaman çizelgesi sayfasında, **video Düzenleyicisi** düğmesini seçin.

    Bu, sizi yeni bir proje oluşturmak için kullandığınız sayfaya götürür. Yeni projeden farklı olarak, daha önce düzenlenmeye başladıysanız videonun zaman damgalandırılmış Öngörüler segmentlerini görürsünüz.

## <a name="see-also"></a>Ayrıca bkz.

[Video Indexer’a genel bakış](video-indexer-overview.md)

