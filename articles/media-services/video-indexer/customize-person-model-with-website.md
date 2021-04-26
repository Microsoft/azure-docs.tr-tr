---
title: Bir kişi modelini Video Indexer Web sitesiyle özelleştirme
titleSuffix: Azure Media Services
description: Bir kişi modelini Video Indexer Web sitesiyle özelleştirmeyi öğrenin.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2020
ms.author: juliako
ms.openlocfilehash: 890f8c159c3e8e8d0b4164cf218f320551ea63ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97628986"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Video Indexer Web sitesiyle bir kişi modelini özelleştirme

Video Indexer video içeriği için ünlüme tanımayı destekler. Ünlüçilerin tanınma özelliği, ıMDB, Viveze ve en popüler LinkedIn etkileyen, sık istenen veri kaynağına göre yaklaşık 1.000.000 yüz içerir. Ayrıntılı bir genel bakış için bkz. [video Indexer kişi modelini özelleştirme](customize-person-model-overview.md).

Bu konuda açıklandığı gibi videoda algılanan yüzeyleri düzenlemek için Video Indexer Web sitesini kullanabilirsiniz. API 'yi, API ['leri kullanarak bir kişi modelini özelleştirme](customize-person-model-with-api.md)bölümünde açıklandığı gibi de kullanabilirsiniz.

## <a name="central-management-of-person-models-in-your-account"></a>Hesabınızdaki kişi modellerinin Merkezi Yönetimi

1. Hesabınızdaki kişi modellerini görüntülemek, düzenlemek ve silmek için Video Indexer Web sitesine gidin ve oturum açın.
1. Sayfanın solundaki içerik modeli özelleştirmesi düğmesini seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="İçerik modelini Özelleştir":::
1. Kişiler sekmesini seçin.

    Hesabınızda varsayılan kişi modelini görürsünüz. Varsayılan kişi modeli, videolarınızın öngörülerine göre düzenlediğiniz veya değiştirdiğiniz yüzeyleri, dizin oluşturma sırasında özel bir kişi modeli belirtmediğiniz bir şekilde tutar.

    Başka kişi modelleri oluşturduysanız, bunlar da bu sayfada listelenir.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/content-model-customization-people-tab.png" alt-text="Kişileri özelleştirme":::

## <a name="create-a-new-person-model"></a>Yeni bir kişi modeli oluşturun

1. Sağdaki **+ Model Ekle** düğmesini seçin.
1. Modelin adını girin. Artık yeni kişi modeline yeni kişiler ve yüzler ekleyebilirsiniz.
1. Liste menü düğmesini seçin ve **+ kişi ekle**' yi seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/add-new-person.png" alt-text="Bir peron ekleme":::

## <a name="add-a-new-person-to-a-person-model"></a>Kişi modeline yeni bir kişi ekleyin

> [!NOTE]
> Video Indexer, kişi modelinde aynı ada sahip birden fazla kişi eklemenize olanak tanır. Ancak, kullanılabilirlik ve açıklık için modelinizdeki her kişiye benzersiz adlar vermeniz önerilir.

1. Bir kişi modeline yeni bir yüz eklemek için, yüzü eklemek istediğiniz kişi modelinin yanındaki liste menü düğmesini seçin.
1. Menüden **+ kişi ekle** ' yi seçin.

    Açılır pencere, kişinin ayrıntılarını doldurmanızı ister. Kişinin adını yazın ve onay düğmesini seçin.

    Daha sonra dosya gezgininizden seçim yapabilir veya yüzün yüz görüntülerini sürükleyip bırakabilirsiniz. Video Indexer tüm standart görüntü dosya türlerini alır (örn: JPG, PNG ve daha fazlası).

    Video Indexer, bu kişinin, bu yeni yüzeyi eklediğiniz kişi modelini kullanarak dizininizdeki ve önceden dizinlenmiş olduğunuz geçerli videolarda yer alan oluşumları algılayabilir. Geçerli videolarınızdaki kişinin tanınabilmesi, bu bir toplu işlem olduğu için biraz zaman alabilir.

## <a name="rename-a-person-model"></a>Kişi modelini yeniden adlandırma

Hesabınızda varsayılan kişi modeli dahil olmak üzere herhangi bir kişi modelini yeniden adlandırabilirsiniz. Varsayılan kişi modelinizi yeniden adlandırsanız bile, hesabınız için varsayılan kişi modeli olarak görev görmeye devam eder.

1. Yeniden adlandırmak istediğiniz kişi modelinin yanındaki liste menü düğmesini seçin.
1. Menüden **Yeniden Adlandır** ' ı seçin.
1. Modelin geçerli adını seçin ve yeni adınızı yazın.
1. Modelinizin yeniden adlandırılacak onay düğmesini seçin.

## <a name="delete-a-person-model"></a>Kişi modelini silme

Hesabınızda oluşturduğunuz herhangi bir kişi modelini silebilirsiniz. Ancak, varsayılan kişi modelinizi silemezsiniz.

1. Menüden **Sil** ' i seçin.

    Bir açılır pencere görünür ve bu eylemin kişi modelini ve içerdiği tüm kişi ve dosyaları silecektir. Bu eylem geri alınamaz.
1. Emin değilseniz, yeniden Sil ' i seçin.

> [!NOTE]
> Bu (şimdi silinen) kişi modeli kullanılarak dizin oluşturulan mevcut videolar, videoda görünen yüzlerin adlarını güncelleştirme yeteneğini desteklemez. Bu videolardaki yüzlerin adlarını, yalnızca başka bir kişi modeli kullanarak yeniden Takduktan sonra düzenleyebileceksiniz. Bir kişi modeli belirtmeden yeniden dizinle, varsayılan model kullanılacaktır.

## <a name="manage-existing-people-in-a-person-model"></a>Kişi modelindeki mevcut kişileri yönetme

Kişi modellerinizin içeriğini görmek için kişi modelinin adının yanındaki oku seçin. Açılan liste, söz konusu kişi modelindeki tüm kişileri gösterir. Kişilerin yanındaki liste menü düğmesini seçerseniz, Yönet, yeniden adlandır ve Sil seçeneklerini görürsünüz.  

![Ekran görüntüsü, yönetme, yeniden adlandırma ve silme seçenekleriyle bağlamsal bir menü gösterir.](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Kişiyi yeniden adlandırma

1. Kişi modelinizdeki bir kişiyi yeniden adlandırmak için liste menü düğmesini seçin ve liste menüsünden **Yeniden Adlandır** ' ı seçin.
1. Kişinin geçerli adını seçin ve yeni adınızı yazın.
1. Onay düğmesini seçin ve kişi yeniden adlandırılacaktır.

### <a name="delete-a-person"></a>Kişiyi silme

1. Kişi modelinizdeki bir kişiyi silmek için liste menü düğmesini seçin ve liste menüsünden **Sil** ' i seçin.
1. Bir açılır pencere, bu eylemin kişiyi silecektir ve bu eylemin geri alınamayacağı konusunda size bildirir.
1. Yeniden **Sil** ' i seçin ve bu Işlem kişiyi kişi modelinden kaldırır.

### <a name="manage-a-person"></a>Bir kişiyi yönetme

**Yönet**' i seçerseniz, bu kişi modelinin eğitilmekte olduğu tüm yüzlerle ilgili **kişinin Ayrıntılar** penceresini görürsünüz. Bu yüzler, bu kişi modelini kullanan videolar veya el ile karşıya yüklediğiniz görüntülerden gelir.

> [!TIP]
> Kişinin adına tıklayarak veya yukarıda gösterildiği gibi **Yönet**' e tıklayarak **kişinin Ayrıntılar** penceresine ulaşabilirsiniz.

#### <a name="add-a-face"></a>Yüz ekleme

**Görüntü ekle**' yi seçerek kişiye daha fazla yüzey ekleyebilirsiniz.

#### <a name="delete-a-face"></a>Yüz silme

Silmek istediğiniz görüntüyü seçin ve **Sil**' e tıklayın.

#### <a name="rename-and-delete-the-person"></a>Kişiyi yeniden adlandırma ve silme 

Kişiyi yeniden adlandırmak ve kişi modelinden kişiyi silmek için Yönet bölmesini kullanabilirsiniz.

## <a name="use-a-person-model-to-index-a-video"></a>Bir videoyu dizine eklemek için bir kişi modeli kullanma

Videoyu karşıya yükleme sırasında kişi modelini atayarak yeni videonuzu indekslemek için bir kişi modeli kullanabilirsiniz.

Kişi modelinizi yeni bir videoda kullanmak için aşağıdaki adımları uygulayın:

1. Sayfanın sağ tarafındaki **karşıya yükle** düğmesini seçin.
1. Video dosyanızı bırakın veya dosyanıza gözatamazsınız.
1. **Gelişmiş Seçenekler** okunu seçin.
1. Açılan eklentiyi seçin ve oluşturduğunuz kişi modelini seçin.
1. Sayfanın alt kısmındaki **karşıya yükle** seçeneğini belirleyin ve Yeni videonuzun kişi modeliniz kullanılarak dizini oluşturulur.

Karşıya yükleme sırasında bir kişi modeli belirtmezseniz, Video Indexer hesabınızdaki varsayılan kişi modelini kullanarak videoyu dizinleyecek.

## <a name="use-a-person-model-to-reindex-a-video"></a>Bir videoyu yeniden eklemek için bir kişi modeli kullanma

Koleksiyonunuzdaki bir videoyu yeniden eklemek üzere bir kişi modeli kullanmak için, Video Indexer giriş sayfasında hesap videolarınıza gidin ve yeniden adlandırmak istediğiniz videonun adının üzerine gelin.

Videonuzu düzenleme, silme ve yeniden dizin seçeneklerini görürsünüz.

1. Videonuzu yeniden dizinle seçeneğini belirleyin.

    ![Ekran görüntüsünde, hesap Videoları ve videonuzu yeniden dizinle seçeneği gösterilmektedir.](./media/customize-face-model/reindex.png)

    Artık videonuzu yeniden eklemek için kişi modelini seçebilirsiniz.
1. Açılan eklentiyi seçin ve kullanmak istediğiniz kişi modelini seçin.
1. Yeniden **Dizin** oluşturma düğmesini seçin ve Videonuzun, kişi modeliniz kullanılarak yeniden dizinlendirilecektir.

Az önce oluşturduğunuz yüzlere yaptığınız her türlü yeni düzenleme, videoyu yeniden eklemek için kullandığınız kişi modeline kaydedilir.

## <a name="managing-people-in-your-videos"></a>Videolarınızdaki kişileri yönetme

Algılanan yüzeyleri ve yüzeyleri düzenleyerek ve silerek dizinettiğiniz videolar üzerinde tanınan kişileri yönetebilirsiniz.

Bir yüzü silme, videonun öngörülerine ait belirli bir yüzü kaldırır.

Bir yüzü düzenlediğinizde, algılanan ve büyük olasılıkla videonuzda tanınan bir yüz yeniden adlandırılır. Videonuzdaki bir yüzü düzenlediğinizde, bu ad, karşıya yükleme ve dizinleme sırasında videoya atanan kişi modelinde bir kişi girişi olarak kaydedilir.

Karşıya yükleme sırasında videoya bir kişi modeli atamadıysanız, düzenlemeniz hesabınızın varsayılan kişi modeline kaydedilir.

### <a name="edit-a-face"></a>Yüz düzenleme

> [!NOTE]
> Bir kişi modelinde aynı ada sahip iki veya daha fazla farklı kişi varsa, bu adı o kişi modelini kullanan videolar dahilinde etiketleyemezsiniz. Yalnızca Video Indexer içerik modeli özelleştirme sayfasının kişiler sekmesinde Bu adı paylaşan kişilere değişiklik yapabilirsiniz. Bu nedenle, kişi modelinizdeki her kişiye benzersiz adlar vermeniz önerilir.

1. Video Indexer web sitesine gidip oturum açın.
1. Hesabınızda görüntülemek ve düzenlemek istediğiniz videoyu arayın.
1. Videonuzdaki bir yüzü düzenlemek için Öngörüler sekmesine gidin ve pencerenin sağ üst köşesindeki kurşun kalem simgesini seçin.

    ![Ekran görüntüsünde seçim yapmak için bilinmeyen yüzü olan bir video gösterilir.](./media/customize-face-model/edit-face.png)

1. Algılanan yüzeyleri seçin ve adlarını "bilinmeyen #X" (veya daha önce yüz yüze atanmış olan ad) olarak değiştirin.
1. Yeni adı yazdıktan sonra, yeni adın yanındaki onay simgesini seçin. Bu eylem yeni adı kaydeder ve diğer geçerli videolarınızdaki ve karşıya yüklediğiniz gelecekteki videolarda bu yüzün tüm oluşumlarını tanır ve adlandırır. Diğer geçerli videolarınızdaki yüzün tanınabilmesi, bu bir toplu işlem olduğu için biraz zaman alabilir.

Videonun kullandığı kişi modelinde var olan bir kişinin adı ile bir yüz adı verirseniz, söz konusu kişinin bu videosunda algılanan yüz görüntüleri modelde zaten mevcut olacak şekilde birleşir. Yeni bir ada sahip bir yüz adı verirseniz videonun kullandığı kişi modelinde yeni bir kişi girişi oluşturulur.

### <a name="delete-a-face"></a>Yüz silme

Videonuzdaki algılanan bir yüzü silmek için Öngörüler bölmesine gidin ve bölmenin sağ üst köşesindeki kurşun kalem simgesini seçin. Yüz adının altındaki **Sil** seçeneğini belirleyin. Bu eylem, algılanan yüzü videodan kaldırır. Kişinin yüzü göründüğü diğer videolarda algılanacaktır, ancak dizin oluşturulduktan sonra da bu videolardan de yüz silebilirsiniz.

Adı adlandırılmışsa, kişiyi kişi modelinden özel olarak silmediğiniz takdirde, yüzü sildiğiniz videoyu dizine eklemek için kullanılan kişi modelinde da olmaya devam edecektir.

## <a name="next-steps"></a>Sonraki adımlar

[API 'Leri kullanarak kişi modelini özelleştirme](customize-person-model-with-api.md)
