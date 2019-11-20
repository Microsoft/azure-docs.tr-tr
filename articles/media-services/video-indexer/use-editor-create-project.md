---
title: Video Indexer düzenleyicisini kullanarak projeler oluşturma
titleSuffix: Azure Media Services
description: Bu konu başlığı altında, projeleri oluşturmak için Video Indexer Düzenleyicisi 'nin nasıl kullanılacağı gösterilmektedir.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839158"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Video Indexer düzenleyicisini kullanarak projeler oluşturma

Video Indexer Web sitesi, videolarınızın derin içgörülerini kullanarak, doğru medya içeriğini bulmanıza, ilgilendiğiniz bölümleri bulmanıza ve sonuçları kullanarak tamamen yeni bir proje oluşturmanıza olanak sağlar. Oluşturulduktan sonra proje, Video Indexer oluşturulabilir ve indirilebilir ve kendi Düzenle uygulamalarınızda veya aşağı akış akışlarınızda kullanılabilir.

Bu özelliği yararlı bulabileceğiniz bazı senaryolar şunlardır: 

* Tanıtım için film vurguları oluşturma.
* Haber yayınlarına daha eski video klipleri kullanma.
* Sosyal medya için daha kısa içerik oluşturma.

Bu makalede, sıfırdan bir proje oluşturma ve hesabınızdaki bir videodan bir proje oluşturma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="create-new-project-and-manage-videos"></a>Yeni proje oluşturma ve videoları yönetme

1. [Video Indexer](https://www.videoindexer.ai/) web sitesine gidip oturum açın.
1. **Projeler** sekmesini seçin. Daha önce projeler oluşturduysanız, burada diğer projelerinizi görürsünüz.
1. **Yeni proje oluştur**' a tıklayın.  

    ![Yeni proje](./media/video-indexer-view-edit/new-project.png)
1. Kalem simgesine tıklayarak projenize bir ad verin. "Başlıksız proje" yazan metni proje adınızla değiştirin ve onay düğmesine tıklayın.

    ![Yeni proje](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Projeye video ekleyin

> [!NOTE]
> Şu anda projeler yalnızca aynı dilde dizinli videolar içerebilir. Bir dilde video seçtikten sonra, farklı bir dildeki hesabınızdaki videoları ekleyemezsiniz.

1. **Video ekle**seçeneğini belirleyerek bu projede birlikte çalışmak istediğiniz videoları ekleyin.

    Hesabınızdaki tüm videoları ve "metin, anahtar sözcükleri veya görsel içerik arama" yazılı bir arama kutusunu görürsünüz. Belirli bir kişiye, etikete, markaya, anahtar sözcüğe veya betiğe ve OCR 'ye bir örneğe sahip videoları aramak için.
    
    Örneğin, aşağıdaki görüntüde "GitHub" ile ilgili videoları arıyoruz.
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Sonuçları **Filtrele ' i**seçerek sonuçlarınızı daha fazla filtreleyebilirsiniz. Belirli bir kişiye sahip olan videoları göstermek veya yalnızca belirli bir dilde olan veya belirli bir sahibe sahip olan video sonuçlarını görmek istediğinizi belirtmek için filtre uygulayabilirsiniz. <br/> Sorgunuzun kapsamını da belirtebilirsiniz. Örneğin, OCR 'de "GitHub" öğesini aramak istiyorsanız **görsel metin**' i seçin.

    ![Filtre](./media/video-indexer-view-edit/visual-text.png)

    Sorgunuza birden çok filtre uygulayabilirsiniz. Filtreleri eklemek/kaldırmak için **+** / **-** düğmelerini kullanın. Tüm filtreleri kaldırmak için **clear filtrelerini** kullanın.
1. Video eklemek için, bunları seçin ve ardından **Ekle**' yi seçin.
1. Şimdi, seçtiğiniz tüm videoları görürsünüz. Bunlar, projeniz için klip seçeceğim olan videolardır.

    Video sırasını sürükleyip bırakarak veya liste menü düğmesini seçip **aşağı taşı** veya **Yukarı taşı**' yı seçerek yeniden düzenleyebilirsiniz. Liste menüsünde, bu projeden videoyu da kaldırabileceksiniz. 

    ![Düzenlemenize](./media/video-indexer-view-edit/rearrange.png)
    
    Bu projeye **video ekle**seçeneğini belirleyerek istediğiniz zaman daha fazla video ekleyebilirsiniz. Ayrıca, projenize aynı videonun birden fazla örneğini de ekleyebilirsiniz. Bir videodan klip ve sonra başka bir klibin bir kırpından sonra ilk videodan başka bir klip göstermek istiyorsanız bunu yapmak isteyebilirsiniz. 

### <a name="select-clips-to-use-in-your-project"></a>Projenizde kullanılacak klipleri seçin

Her videonun sağ tarafındaki aşağı oka tıklarsanız, videoda zaman damgalarına göre (videonun klipleri) öngörüleri açarsınız. 

1. Hangi öngörülerin görmek istediğinizi ve görmek istediğinizi özelleştirmek için **öngörüleri görüntüle** ' yi seçin. 

    ![Öngörüleri görüntüle](./media/video-indexer-view-edit/insights.png)
1. Belirli klipler için sorgular oluşturmak üzere "yazılı betik, görsel metin, kişiler ve etiketlerde arama" ifadesini içeren arama kutusunu kullanın.
1. **Filtre seçeneklerini**belirleyerek Aradığınız sahnelerin ayrıntılarını daha fazla belirtmek için filtre ekleyin.

    ![Filtre seçenekleri](./media/video-indexer-view-edit/filter-options.png)

    Örneğin, Donovan kahverengi ekranda olduğu sürece GitHub 'ın belirtildiği klipleri görmek isteyebilirsiniz. Bunun için, öngörü türü olarak "kişiler" içeren bir "içerme" filtresi eklemeniz gerekir. Ardından, filtrenin arama kutusuna "Donovan kahverengi" yazmanız gerekir.
    
    ![içeriyor](./media/video-indexer-view-edit/include.png)
    
    Donovan kahverengi, ekranda _olmadığı_ sürece GitHub 'ın belirtildiği küçük resimleri istiyorsanız, açılan menüyü kullanarak "içerme" filtresini bir "hariç tut" filtresine değiştirmelisiniz. 

1. Eklemek istediğiniz segmenti seçerek projenize bir klip ekleyin. Segmente yeniden tıklayarak bu klibin seçimini kaldırabilirsiniz.
    
    Videonun yanındaki liste menüsü seçeneğine tıklayıp **tüm segmentleri Seç**' i seçerek videonun tüm parçalarını ekleyin. 

    ![Tümünü Ekle](./media/video-indexer-view-edit/add-all.png)

    Seçimi Temizle ' i seçerek tüm seçiminizi temizleyebilirsiniz.

> [!TIP]
> Kliplerinizi seçip sıralamayı yaparken, sayfanın sağ tarafındaki Player 'da videoyu önizleyebilirsiniz. 

![Önizleme](./media/video-indexer-view-edit/preview.png)

**Projeyi kaydet**' i seçerek projenizi kaydetmeyi unutmayın. 

### <a name="render-and-download-the-project"></a>Projeyi işle ve indir

> [!NOTE]
> Video Indexer ücretli hesaplar için, projenizin işlenmesi kodlama maliyetlerine sahiptir. Video Indexer deneme hesapları, 5 saatlik işleme sınırlıdır.

1. İşiniz bittiğinde projenizin kaydedildiğinden emin olun. Artık bu projeyi işleyebilirsiniz. **İşle ve İndir '** i seçin. 

    ![Kaydet](./media/video-indexer-view-edit/save.png)

    Video dizin oluşturucunun bir dosyayı işlemesini ve ardından indirme bağlantısının e-postanıza gönderileceğini söyleyen bir açılan pencere görüntülenir. Devam ' ı seçin. 
    
    Ayrıca, projenin sayfanın üstünde oluşturulmakta olduğunu belirten bir bildirim görürsünüz. İşlem tamamlandıktan sonra, projenin başarıyla işlenmiş olduğunu belirten yeni bir bildirim görürsünüz. Projeyi indirmek için bildirime tıklayın. Projeyi MP4 biçiminde indirir.

    ![İşleme bitti](./media/video-indexer-view-edit/rendering-done.png)

1. Kaydedilmiş projelere **Projeler** sekmesinden erişebilirsiniz. 

    Bu projeyi seçerseniz, bu projenin tüm öngörülerini ve zaman çizelgesini görürsünüz. **Video Düzenleyicisi**' ni seçerseniz bu projede düzenleme yapmaya devam edebilirsiniz. Düzenlemeler arasında video ve klip ekleme veya kaldırma ya da projeyi yeniden adlandırma dahildir.

    ![Video Düzenleyicisi](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Videoınızdan bir proje oluşturun

Hesabınızdaki bir videodan doğrudan yeni bir proje oluşturabilirsiniz. 

1. Video Indexer Web sitesinin **kitaplık** sekmesine gidin.
1. Projenizi oluşturmak için kullanmak istediğiniz videoyu açın. Öngörüler ve zaman çizelgesi sayfasında, **video Düzenleyicisi** düğmesini seçin.

    Bu, sizi yeni bir proje oluşturmak için kullandığınız sayfaya götürür. Yeni projeden farklı olarak, daha önce düzenlenmeye başladıysanız videonun zaman damgalandırılmış Öngörüler segmentlerini görürsünüz.

## <a name="see-also"></a>Ayrıca bkz.

[Video Indexer’a genel bakış](video-indexer-overview.md)

