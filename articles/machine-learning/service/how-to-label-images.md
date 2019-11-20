---
title: Azure Machine Learning veri etiketleme aracını kullanma
title.suffix: Azure Machine Learning
description: Bu makalede, bir Azure Machine Learning etiketleme projesinde veri etiketleme araçlarının nasıl kullanılacağı öğretilir.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 9cbebb905ba39dabaf905be2fa741702bd1a0088
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838950"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>Etiketleme projesindeki resimleri etiketleme

Proje yöneticiniz Azure Machine Learning bir etiketleme projesi oluşturduktan sonra, bir makine öğrenimi projesi için hızlı bir şekilde veri hazırlamak üzere etiketleme aracını kullanabilirsiniz. 

> [!div class="checklist"]
> * Etiketleme projelerinize erişme
> * Etiketleme Araçları
> * Belirli etiketleme görevleri için araçları kullanma

## <a name="prerequisites"></a>Önkoşullar

* Çalışan bir veri etiketleme projesi için etiketleme portalı URL 'SI
* Bir [Microsoft hesabı](https://account.microsoft.com/account) veya
* Kuruluş ve proje için Azure Active Directory hesabı

> [!Note]
> Proje Yöneticisi etiketleme portalı URL 'sini, **proje ayrıntıları** sayfasındaki **Ayrıntılar** sekmesinde bulabilir. 

## <a name="logging-in-to-the-projects-labeling-portal"></a>Projenin etiketleme portalında oturum açma

Proje Yöneticisi tarafından size sunulan etiketleme portalı URL 'sine gidin. 

Yönetici tarafından takıma eklemek için kullanılan e-posta hesabını kullanarak oturum açın. Çoğu kullanıcı için Microsoft hesabı oturum açma işlemi, yapılır. Etiketleme projesi Azure Active Directory kullanıyorsa, oturum açma yöntemi bu şekilde yapılır. 

## <a name="understanding-the-labeling-task"></a>Etiketleme görevini anlama

Oturum açtıktan sonra projenin genel bakış sayfasına yönlendirilirsiniz. 

Yapmanız gereken ilk şey **ayrıntılı yönergeleri görüntüler**. Bu yönergeler projenize özeldir ve size, kararlarınızı ve diğer ilgili bilgileri nasıl yapmanız gerektiğini, sizin de ilgilendiğiniz verilerin türünü açıklayacak. İşiniz bittiğinde proje sayfasına dönüp **etiketlemeyi Başlat**' ı seçin.

## <a name="common-features-of-the-labeling-task"></a>Etiketleme görevinin ortak özellikleri

Tüm görüntü etiketleme görevleri, proje yöneticisi tarafından belirtilen bir kümeden uygun bir etiketi veya etiketleri seçmeyi içerir. Klavyenizdeki sayı tuşlarını kullanarak ilk dokuz etiket arasından seçim yapabilirsiniz.  

Görüntü sınıflandırma görevleri birden çok görüntüyü aynı anda sunmanıza olanak tanır. Görüntü alanının üzerindeki simgeler kullanılarak farklı düzenler seçilebilir. **Tümünü Seç** düğmesine basarak, görüntülenmiş tüm görüntüleri eşzamanlı olarak seçebilirsiniz. Görüntü alanının sağ üst köşesindeki dairesel seçim düğmesini kullanarak tek tek fotoğrafları seçin. Etiket uygulamak için en az bir görüntü seçmelisiniz. Birden çok görüntü seçiliyse, bir etiket seçilmesi, bu etiketi Seçilen fotoğrafların her birine uygular.

Burada, bir 2x2 düzeni seçtik ve "Mammal" etiketini, ve Orca 'nın görüntülerine uygulamak üzeresiniz. Parça içeren görüntü zaten "Cartilaginou balığı" olarak etiketlendi ve Iguana henüz etiketlendi.

![Birden çok resim düzeni ve seçimi](media/how-to-label-images/layouts.png)

> [!Important] 
> Yalnızca etiketli verilerin yeni bir sayfasına sahip olduğunuzda mizanpajları değiştirin. Mizanpajları değiştirmek sayfanın devam eden etiketleme işini temizler. 

Sayfadaki tüm görüntüleri etiketlediyseniz Azure, **Gönder** düğmesine izin vermez. Çalışmanızı kaydetmek için **Gönder** ' e basın. 

Her seferinde veriler için Etiketler gönderdikten sonra, Azure sayfayı iş kuyruğundan yeni bir görüntü kümesiyle yenileyecek.  

## <a name="tagging-images-for-multi-class-classification"></a>Birden çok sınıf sınıflandırması için resimleri etiketleme

Projeniz "Image Classification Multi-Class" türünde ise, tüm görüntüye tek bir etiket atarsınız. İstediğiniz zaman **yönergeler** sayfasını seçin ve projeye özgü Kılavuzu görmek için **ayrıntılı yönergeleri görüntüleyin** . 

Daha önce anlatıldığı gibi, görüntüleri sunmak için birkaç düzen arasından seçim yapabilirsiniz. Bir görüntü seçip etiketi atadıktan sonra bir hata yaptığını fark etmiş olursunuz. Resmin altında gösterildiği etikette, `X` hedefine tıkladığınızda, etiketini temizleyemezsiniz. Ya da, görüntüyü seçip başka bir sınıf seçerseniz, etiket yeni seçilen değere geçiş yapar.

## <a name="tagging-images-for-multi-label-classification"></a>Çok etiketli sınıflandırmayla ilgili görüntüleri etiketleme

"Görüntü sınıflandırması çoklu etiketi" türünde bir proje üzerinde çalışıyorsanız, bir görüntüye bir _veya daha fazla_ etiket uygularsınız. İstediğiniz zaman **yönergeler** sayfasını seçin ve projeye özgü Kılavuzu görmek için **ayrıntılı yönergeleri görüntüleyin** . 

Etiketlemek istediğiniz görüntüyü seçin ve ardından etikete tıklayın. Etiketi seçmek, seçilen tüm görüntülere uygular ve bunları kaldırır. Daha fazla etiket uygulamak için görüntüleri yeniden seçmeniz gerekir. Bu animasyon çok etiketli etiketleme sayfasını gösterir:

* "Okyanus" etiketini uygulamak için **Tümünü Seç** seçeneği kullanılır
* Tek bir görüntü seçilir ve "closeup" olarak etiketlendi
* Üç görüntü seçilir ve "geniş açılı" olarak etiketlendi

![MultiLabel akışını gösteren animasyon](media/how-to-label-images/multilabel.gif)

Bir hata düzeltmek için, tek tek etiketleri temizlemek için `X` tıklayabilir ya da görüntüleri seçebilir ve etiketi seçerek seçili tüm görüntülerden etiketi temizler. Bu senaryo burada gösterilmektedir; burada "Land" tıklanması, seçilen iki görüntüden bu etiketi temizler.

![Birden çok seçimi gösteren ekran görüntüsü](media/how-to-label-images/multiple-deselection.png)

Azure yalnızca, her görüntüye en az bir etiket uyguladıktan sonra **Gönder** düğmesini etkinleştirir. Çalışmanız için **Gönder** ' e basın.

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>Nesne algılama için resimleri ve sınırlayıcı kutuları etiketleme

Projeniz "nesne kimliği (sınırlayıcı kutular)" türündedir, görüntüde bir veya daha fazla sınırlayıcı kutu belirtirsiniz ve bu kutuya bir etiket uygularsınız. Her görüntüde, her birinin tek etiketli birden çok sınırlayıcı kutusu olabilir. Birden çok sınırlayıcı kutu eklemenin projenize uygun olup olmadığını anlamak için **ayrıntılı yönergeleri görüntüle** ' i kullanın.

1. Oluşturmak istediğiniz sınırlayıcı kutu için bir etiket seçin
1. Dikdörtgen kutu aracı **![dikdörtgen kutusu** aracını seçin](media/how-to-label-images/rectangular-box-tool.png) veya ' R ' tuşuna basın 
1. Kaba bir sınırlayıcı kutu oluşturmak için, Hedefinizdeki çapraz doğrultuda tıklayın ve sürükleyin
    * Kutunun kenarlarını veya köşelerini tıklatıp sürükleyerek sınırlayıcı kutuyu ayarlayın

![Temel sınırlama kutusu oluşturma ekranının ekran görüntüsü](media/how-to-label-images/bounding-box-sequence.png)

Sınırlayıcı kutuyu silmek isterseniz, oluşturulduktan sonra sınırlayıcı kutunun yanında görünen X şeklindeki hedefe tıklayın.

Varolan bir sınırlayıcı kutunun etiketini yeniden atayamazsınız. Etiket atamasıyla bir hata yaparsanız, sınırlayıcı kutuyu silmeniz ve doğru etiketle yeni bir tane oluşturmanız gerekir.

Varsayılan olarak, varolan sınırlayıcı kutular düzenlenebilir. Kilit **/kilit açma** bölgeleri aracı ![kilit/kilit açma aracı](media/how-to-label-images/lock-bounding-boxes-tool.png) veya "L" Bu davranışa geçiş yapar. Bölgeler kilitliyse, yalnızca yeni bir sınırlayıcı kutunun şeklini veya konumunu değiştirebilirsiniz.

Mevcut bir sınırlayıcı kutuyu ayarlamak **için bölge işleme aracı ![** bölgeleri işleme aracı](media/how-to-label-images/regions-tool.png) veya "d" kullanın. Şekli ayarlamak için kenarları veya köşeleri tıklatıp sürükleyebilirsiniz. İç kısma tıkladığınızda, tüm sınırlayıcı kutusunu sürükleyebilirsiniz. Bir bölgeyi düzenleyemezsiniz, büyük olasılıkla bir bölgeyi **Kilitle/kilidini aç** aracını atlayabilirsiniz. 

Şablon-kutusu aracı](media/how-to-label-images/template-box-tool.png) veya "T" ![**şablon tabanlı Box** aracını kullanarak aynı boyutta birden fazla sınırlayıcı kutu oluşturun. Görüntüde hiçbir sınırlayıcı kutu yoksa ve şablon tabanlı kutuları etkinleştirirseniz araç 5 0x50 piksel kutu üretir. Bir sınırlayıcı kutu oluşturduysanız ve sonra şablon tabanlı kutuları etkinleştirirseniz, yeni sınırlayıcı kutular en son yaptığınız son bir boyut olur. Şablon tabanlı kutular, yerleştirme sonrasında yeniden boyutlandırılabilir. Şablon tabanlı bir kutunun yeniden boyutlandırılması yalnızca belirli bir kutuyu yeniden boyutlandırır. 

Geçerli görüntüdeki _Tüm_ sınırlayıcı kutuları silmek isterseniz, **tüm bölgeleri** Sil aracını ![bölgeleri Sil aracını](media/how-to-label-images/delete-regions-tool.png)seçebilirsiniz. 

Görüntü için sınırlayıcı kutuları oluşturduktan sonra, çalışmanızı kaydetmek için **Gönder** ' e basın. **Gönder** düğmesine basmadığınız takdirde devam eden çalışmanız kaydedilmez. 

## <a name="finishing-up"></a>Tamamlanıyor 

Etiketli verilerin bir sayfasını gönderdiğinizde Azure, bir iş sırasından etiketlendirmede yeni olmayan veriler atar. Etiketi olmayan daha fazla veri yoksa, Portal giriş sayfasına geri bir bağlantı ile bu efekte bir ileti görürsünüz. 

Daha fazla etiketleme yapabildiğinizi biliyorsanız, etiketleme portalının sağ üst köşesinde adınızı seçin ve **SignOut**' ı seçin. Oturumu kapatma, sonunda Azure "sizin zaman aşımına uğrar" ve verilerinizi başka bir etiketleyici 'ye atacaktır. 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'da görüntü sınıflandırma modellerini eğitme](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml) hakkında bilgi edinin
* [Azure kullanarak nesne algılama hakkında bilgi edinin ve daha hızlı R-CNN tekniği](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)