---
title: Bir kişi modelini özelleştirmek için Video Indexer Web sitesini kullanma-Azure
titleSuffix: Azure Media Services
description: Bu makalede, bir kişi modelinin Video Indexer Web sitesiyle nasıl özelleştirileceği gösterilmektedir.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 060d94d6181e894c18d268845b48eb802c52730c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838285"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Video Indexer Web sitesiyle bir kişi modelini özelleştirme

Video Indexer video içeriği için ünlüme tanımayı destekler. Ünlüçilerin tanınma özelliği, ıMDB, Viveze ve en popüler LinkedIn etkileyen, sık istenen veri kaynağına göre yaklaşık 1.000.000 yüz içerir. Ayrıntılı bir genel bakış için bkz. [video Indexer kişi modelini özelleştirme](customize-person-model-overview.md).

Bu konuda açıklandığı gibi videoda algılanan yüzeyleri düzenlemek için Video Indexer Web sitesini kullanabilirsiniz. API 'yi, API ['leri kullanarak bir kişi modelini özelleştirme](customize-person-model-with-api.md)bölümünde açıklandığı gibi de kullanabilirsiniz.

## <a name="central-management-of-person-models-in-your-account"></a>Hesabınızdaki kişi modellerinin Merkezi Yönetimi

1. Hesabınızdaki kişi modellerini görüntülemek, düzenlemek ve silmek için Video Indexer Web sitesine gidin ve oturum açın.
2. Sayfanın sağ üst köşesindeki içerik modeli özelleştirmesi düğmesine tıklayın.

    ![İçerik modeli özelleştirmesi](./media/customize-face-model/content-model-customization.png)
3. Kişiler sekmesini seçin.

    Hesabınızda varsayılan kişi modelini görürsünüz. Varsayılan kişi modeli, videolarınızın öngörülerine göre düzenlediğiniz veya değiştirdiğiniz yüzeyleri, dizin oluşturma sırasında özel bir kişi modeli belirtmediniz. 

    Başka kişi modelleri oluşturduysanız, bunlar da bu sayfada listelenir. 

    ![İçerik modeli özelleştirmesi](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Yeni bir kişi modeli oluşturun

1. **+ Model Ekle** düğmesine tıklayın.

    ![Yeni bir kişi ekleyin](./media/customize-face-model/add-new-person.png)
2. Modelin adını girin ve adın yanındaki onay düğmesine tıklayın.

    ![Yeni bir kişi ekleyin](./media/customize-face-model/add-new-person2.png)

    Yeni bir kişi modeli oluşturdunuz. Artık yeni kişi modeline yüz ekleyebilirsiniz.
3. Liste menü düğmesine tıklayın ve **+ kişi ekle**' yi seçin.

    ![Yeni bir kişi ekleyin](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Kişi modeline yeni bir kişi ekleyin

> [!NOTE]
> Video Indexer, kişi modelinde aynı ada sahip birden fazla kişi eklemenize olanak tanır. Ancak, kullanılabilirlik ve açıklık için modelinizdeki her kişiye que ad vermeniz önerilir.

1. Bir kişi modeline yeni bir yüz eklemek için, yüzü eklemek istediğiniz kişi modelinin yanındaki liste menüsü düğmesine tıklayın.
1. Menüden **+ kişi ekle** ' ye tıklayın.

    ![Kişiye yeni bir yüz ekleyin](./media/customize-face-model/add-new-face.png)
 
    Açılır pencere, kişinin ayrıntılarını doldurmanızı ister. Kişinin adını yazın ve onay düğmesine tıklayın.

    ![Kişiye yeni bir yüz ekleyin](./media/customize-face-model/add-new-face2.png)
 
Daha sonra dosya gezgininizden seçim yapabilir veya yüzün yüz görüntülerini sürükleyip bırakabilirsiniz. Video Indexer tüm standart görüntü dosya türlerini alır (örn: JPG, PNG ve daha fazlası).

Video Indexer, bu kişinin, Dizin oluşturduğunuz ve bu yeni yüzeyi eklediğiniz kişi modelini kullanarak, daha önce dizinlenmiş olduğunuz videoların ve mevcut videolarınızın örneklerini algılayabilmelidir. Geçerli videolarınızdaki kişinin tanınabilmesi, bu bir toplu işlem olduğu için biraz zaman alabilir.


## <a name="rename-a-person-model"></a>Kişi modelini yeniden adlandırma

Hesabınızda varsayılan kişi modeli dahil olmak üzere herhangi bir kişi modelini yeniden adlandırabilirsiniz. Varsayılan kişi modelinizi yeniden adlandırsanız bile, hesabınız için varsayılan kişi modeli olarak görev görmeye devam eder.

1. Yeniden adlandırmak istediğiniz kişi modelinin yanındaki liste menüsü düğmesine tıklayın.
2. Menüden **Yeniden Adlandır** ' a tıklayın.

    ![Kişiyi yeniden adlandırma](./media/customize-face-model/rename-person.png)
3. Modelin geçerli adına tıklayın ve yeni adınızı yazın.

    ![Kişiyi yeniden adlandırma](./media/customize-face-model/rename-person2.png)
4. Modelinizin yeniden adlandırılacak onay düğmesine tıklayın.

## <a name="delete-a-person-model"></a>Kişi modelini silme

Hesabınızda oluşturduğunuz herhangi bir kişi modelini silebilirsiniz. Ancak, varsayılan kişi modelinizi silemezsiniz.

1. Menüden **Sil** ' e tıklayın.

    ![Kişiyi silme](./media/customize-face-model/delete-person.png)
    
    Bir açılır pencere görünür ve bu eylemin kişi modelini ve içerdiği tüm kişi ve dosyaları silecektir. Bu eylem geri alınamaz. 

    ![Kişiyi silme](./media/customize-face-model/delete-person2.png)
1. Emin değilseniz, yeniden Sil ' e tıklayın.

> [!NOTE]
> Bu (şimdi silinen) kişi modeli kullanılarak dizin oluşturulan mevcut videolar, videoda görünen yüzlerin adlarını güncelleştirme yeteneğini desteklemezler. Bu videolardaki yüzlerin adlarını, yalnızca başka bir kişi modeli kullanarak yeniden Takduktan sonra düzenleyebileceksiniz. Bir kişi modeli belirtmeden yeniden dizinle, varsayılan model kullanılacaktır. 

## <a name="manage-existing-people-in-a-person-model"></a>Kişi modelindeki mevcut kişileri yönetme

Kişi modellerinizin içeriğini görmek için kişi modelinin adının yanındaki oka tıklayın.
Açılan liste, söz konusu kişi modelindeki tüm kişileri gösterir. Kişilerin yanındaki liste menü düğmesine tıklarsanız, Yönet, yeniden adlandır ve Sil seçeneklerini görürsünüz.  

![Kişiye yeni bir yüz ekleyin](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Kişiyi yeniden adlandırma

1. Kişi modelinizdeki bir kişiyi yeniden adlandırmak için liste menü düğmesine tıklayın ve liste menüsünden **Yeniden Adlandır** ' ı seçin.
1. Kişinin geçerli adına tıklayın ve yeni adınızı yazın.
1. Onay düğmesine tıklayın ve kişi yeniden adlandırılacaktır.

### <a name="delete-a-person"></a>Kişiyi silme

1. Kişi modelinizdeki bir kişiyi silmek için liste menü düğmesine tıklayın ve liste menüsünden **Sil** ' i seçin.
1. Bir açılır pencere, bu eylemin kişiyi silecektir ve bu eylemin geri alınamayacağı konusunda size bildirir.
1. Yeniden Sil ' e tıklayın, bu, kişiyi kişi modelinden kaldırır.

### <a name="manage-a-person"></a>Bir kişiyi yönetme

**Yönet**' e tıklarsanız, bu kişi modelinin eğitilmekte olduğu tüm yüzeyleri görürsünüz. Bu yüzler, bu kişi modelini kullanan videolardan veya el ile karşıya yüklediğiniz görüntülerden gelir. 

Görüntü Ekle ' ye tıklayarak kişiye daha fazla yüzey ekleyebilirsiniz.

Kişiyi yeniden adlandırmak ve kişi modelinden kişiyi silmek için Yönet bölmesini kullanabilirsiniz.

## <a name="use-a-person-model-to-index-a-video"></a>Bir videoyu dizine eklemek için bir kişi modeli kullanma

Videoyu karşıya yükleme sırasında kişi modelini atayarak yeni videonuzu indekslemek için bir kişi modeli kullanabilirsiniz.

Kişi modelinizi yeni bir videoda kullanmak için aşağıdakileri yapın:

1. Sayfanın üst kısmındaki **karşıya yükle** düğmesine tıklayın.

    ![Karşıya Yükle](./media/customize-face-model/upload.png)
1. Video dosyanızı daire içine bırakın veya dosyanıza gözatamazsınız.
1. Gelişmiş Seçenekler okuna tıklayın.

    ![Karşıya Yükle](./media/customize-face-model/upload2.png)
1. Açılan kişiye tıklayın ve oluşturduğunuz kişi modelini seçin.

    ![Karşıya Yükle](./media/customize-face-model/upload3.png)
1. Sayfanın alt kısmındaki karşıya yükle seçeneğine tıklayın ve Yeni videonuzun kişi modeliniz kullanılarak dizini oluşturulur.

Karşıya yükleme sırasında bir kişi modeli belirtmezseniz, Video Indexer hesabınızdaki varsayılan kişi modelini kullanarak videoyu dizinleyecek.

## <a name="use-a-person-model-to-reindex-a-video"></a>Bir videoyu yeniden eklemek için bir kişi modeli kullanma 

Koleksiyonunuzdaki bir videoyu yeniden eklemek üzere bir kişi modeli kullanmak için, Video Indexer giriş sayfasında hesap videolarınıza gidin ve yeniden adlandırmak istediğiniz videonun adının üzerine gelin.

Videonuzu düzenleme, silme ve yeniden dizin seçeneklerini görürsünüz. 

1. Videonuzu yeniden eklemek için seçeneğe tıklayın.

    ![Yeniden dizin oluşturma](./media/customize-face-model/reindex.png)

    Artık videonuzu yeniden eklemek için kişi modelini seçebilirsiniz.
1. Açılan kişiye tıklayın ve kullanmak istediğiniz kişi modelini seçin. 

    ![Yeniden dizin oluşturma](./media/customize-face-model/reindex2.png)

1. Yeniden oluşturma **düğmesine tıklayın** ve videonuzu, kişi modeliniz kullanılarak yeniden dizinlenir.

Az önce oluşturduğunuz yüzlere yaptığınız her türlü yeni düzenleme, videoyu yeniden eklemek için kullandığınız kişi modeline kaydedilir.

## <a name="managing-people-in-your-videos"></a>Videolarınızdaki kişileri yönetme

Algılanan yüzeyleri ve yüzeyleri düzenleyerek ve silerek dizinettiğiniz videolar üzerinde tanınan kişileri yönetebilirsiniz.

Yüzü silme, videonun öngörülerine ait belirli bir yüzü kaldırır.

Bir yüzü düzenlediğinizde, algılanan ve büyük olasılıkla videonuzda tanınan bir yüzü yeniden adlandırır. Videonuzdaki bir yüzü düzenlediğinizde, bu ad, karşıya yükleme ve dizinleme sırasında videoya atanan kişi modelinde bir kişi girişi olarak kaydedilir.

Karşıya yükleme sırasında videoya bir kişi modeli atamadıysanız, düzenlemeniz hesabınızın varsayılan kişi modeline kaydedilir.

### <a name="edit-a-face"></a>Yüz düzenleme


> [!NOTE]
> Bir kişi modelinde aynı ada sahip iki veya daha fazla farklı kişi varsa, bu adı o kişi modelini kullanan videolar dahilinde etiketleyemezsiniz. Yalnızca Video Indexer içerik modeli özelleştirme sayfasının kişiler sekmesinde Bu adı paylaşan kişilere değişiklik yapabilirsiniz. Bu nedenle, kişi modelinizdeki her kişiye benzersiz adlar vermeniz önerilir.

1. Video Indexer Web sitesine gidin ve oturum açın.
1. Hesabınızda görüntülemek ve düzenlemek istediğiniz videoyu arayın.
1. Videonuzdaki bir yüzü düzenlemek için Öngörüler sekmesine gidin ve pencerenin sağ üst köşesindeki kurşun kalem simgesine tıklayın.

    ![Videonuzdaki bir yüzü düzenleme](./media/customize-face-model/edit-face.png)
1. Algılanan yüzlerden birine tıklayın ve adlarını "bilinmeyen #X" (veya daha önce yüze atanmış olan adı) olarak değiştirin. 
1. Yeni adı yazdıktan sonra, yeni adın yanındaki onay simgesine tıklayın. Bu, yeni adı kaydeder ve diğer geçerli videolarınıza ve karşıya yüklediğiniz gelecek videolarda bu yüzün tüm oluşumlarını tanır ve adlandırır. Diğer geçerli videolarınızdaki yüzün tanınabilmesi, bu bir toplu işlem olduğu için biraz zaman alabilir.

Kişinin kullandığı kişi modelinde var olan bir kişinin adı ile bir yüz adı verirseniz, bu, söz konusu kişinin bu videosunda algılanan yüz görüntülerini modelde zaten var olan bir kişiyle birleştirir. Tamamen yeni bir ada sahip bir yüz adı verirseniz, bu işlem, videonun kullandığı kişi modelinde yeni bir kişi girişi oluşturur. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Yüz silme

Videonuzdaki algılanan bir yüzü silmek için Öngörüler bölmesine gidin ve bölmenin sağ üst köşesindeki kurşun kalem simgesine tıklayın. Yüz adının altındaki Sil seçeneğine tıklayın. Bu, algılanan Bu yüzü videodan kaldırır. Kişinin yüzü göründüğü diğer videolarda algılanacaktır, ancak dizin oluşturulduktan sonra da bu videolardan de yüz silebilirsiniz. Adı adlandırılmışsa, kişiyi kişi modelinden özel olarak silmediğiniz takdirde, yüzü sildiğiniz videoyu dizinlemek için kullanılan kişi modelinde da olmaya devam edecektir.

![Videonuzdaki bir yüzü silme](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Sonraki adımlar

[API 'Leri kullanarak kişi modelini özelleştirme](customize-person-model-with-api.md)
