---
title: Etiketleme projesindeki resimleri etiketleme
title.suffix: Azure Machine Learning
description: Azure Machine Learning etiketleme projesindeki bir Machine Learning için verileri hızlıca hazırlamak üzere veri etiketleme araçlarını nasıl kullanacağınızı öğrenin.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: 8f1d648c38760865752c87624dfcb112933650c7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872632"
---
# <a name="tag-images-in-a-labeling-project"></a>Etiketleme projesindeki resimleri etiketleme 

Proje yöneticiniz Azure Machine Learning ' de [bir veri etiketleme projesi](./how-to-create-labeling-projects.md#create-a-data-labeling-project) oluşturduktan sonra etiketleme aracını kullanarak bir Machine Learning projesi için hızlı bir şekilde veri hazırlayabilir. Bu makalede şunları açıklanmaktadır:

> [!div class="checklist"]
> * Etiketleme projelerinize erişme
> * Etiketleme Araçları
> * Belirli etiketleme görevleri için araçları kullanma

## <a name="prerequisites"></a>Önkoşullar

* Kuruluş ve proje için bir [Microsoft hesabı](https://account.microsoft.com/account) veya Azure Active Directory hesabı
* Etiketleme projesini içeren çalışma alanına katkıda bulunan düzeyi erişimi.

## <a name="sign-in-to-the-workspace"></a>Çalışma alanında oturum açın

1. [Azure Machine Learning Studio](https://ml.azure.com)'da oturum açın.

1. Bir abonelik ve etiketleme projesini içeren çalışma alanını seçin.  Bu bilgileri proje yöneticinizden alın.

1. Projeyi bulmak için sol taraftaki **veri etiketleme** ' yi seçin.  

## <a name="understand-the-labeling-task"></a>Etiketleme görevini anlayın

Veri etiketleme projeleri tablosunda, projeniz için **etiket bağlantısı** ' nı seçin.

Projenize özel yönergeler görürsünüz. Bunlar, söz konusu verilerin türünü, kararlarınızı nasıl yapmanız gerektiğini ve diğer ilgili bilgileri açıklar. Bu bilgileri okuduktan sonra, sayfanın üst kısmında **Görevler**' i seçin.  Ya da sayfanın en altında **etiketlemeyi Başlat**' ı seçin.

## <a name="common-features-of-the-labeling-task"></a>Etiketleme görevinin ortak özellikleri

Tüm görüntü etiketleme görevlerinde, proje yöneticisi tarafından belirtilen bir kümeden uygun bir etiketi veya etiketleri seçersiniz. Klavyenizdeki sayı tuşlarını kullanarak ilk dokuz etiketi seçebilirsiniz.  

Görüntü sınıflandırma görevlerinde birden çok görüntüyü aynı anda görüntülemeyi seçebilirsiniz. Düzeni seçmek için görüntü alanının üzerindeki simgeleri kullanın. 

Görüntülenecek tüm görüntüleri eşzamanlı olarak seçmek için **Tümünü Seç**' i kullanın. Tek tek görüntüleri seçmek için görüntünün sağ üst köşesindeki dairesel seçim düğmesini kullanın. Etiket uygulamak için en az bir görüntü seçmelisiniz. Birden çok görüntü seçerseniz, seçtiğiniz herhangi bir etiket seçili tüm görüntülere uygulanır.

Burada iki-by iki düzen seçtik ve "Mammal" etiketini, ve Orca 'nın görüntülerine uygulamak üzeresiniz. Parça görüntüsü zaten "Cartilaginou balığı" olarak etiketlendi ve Iguana henüz etiketlendi.

![Birden çok resim düzeni ve seçimi](./media/how-to-label-images/layouts.png)

> [!Important] 
> Yalnızca etiketli verilerin yeni bir sayfasına sahip olduğunuzda mizanpajları değiştirin. Mizanpajları değiştirmek sayfanın devam eden etiketleme işini temizler.

Sayfadaki tüm görüntüleri etiketlediyseniz Azure, **Gönder** düğmesine izin vermez. Çalışmanızı kaydetmek için **Gönder** ' i seçin.

Bir yandan veriler için Etiketler gönderdikten sonra, Azure sayfayı iş kuyruğundan yeni bir görüntü kümesiyle yeniler.

### <a name="assisted-machine-learning"></a>Yardımlı makine öğrenimi

Makine öğrenimi algoritmaları tetiklenebilir. Bu algoritmalar projenizde etkinleştirilirse, aşağıdakileri görebilirsiniz:

* Bazı görüntü miktarı etiketlendikten sonra, ekranın üst kısmında proje adının yanında **kümelenmiş görevler** görebilirsiniz.  Bu, görüntülerin aynı sayfada benzer görüntüleri sunmak için birlikte gruplandırılacağı anlamına gelir.  Bu durumda, gruplamanın avantajlarından yararlanmak için birden çok görüntü görünümünden birine geçin.  

* Daha sonraki bir noktada, proje adının yanında **önceden etiketlenmiş görevler** görebilirsiniz.  Daha sonra görüntüler, bir makine öğrenimi sınıflandırma modelinden gelen önerilen bir etiketle birlikte görüntülenir. Makine öğrenimi modelinde %100 doğruluk yok. Yalnızca modelin kendine ait olduğu görüntüleri kullandığımızda, bu görüntüler hala hatalı olarak önceden etiketlenmiş olabilir.  Bu etiketleri gördüğünüzde, sayfayı göndermeden önce yanlış etiketleri düzeltin.  

* Nesne tanımlama modelleri için, sınırlayıcı kutular ve Etiketler zaten mevcut olabilir.  Sayfayı göndermeden önce yanlış olan hataları düzeltin.

* Segmentasyon modelleri için, çokgenler ve Etiketler zaten mevcut olabilir.  Sayfayı göndermeden önce yanlış olan hataları düzeltin. 

Özellikle etiketleme projesinde daha önce, makine öğrenimi modeli yalnızca küçük bir resim alt kümesini etiketlemek için yeterince doğru olabilir. Bu görüntülerin etiketlenmesi halinde etiketleme projesi, model eğitiminin bir sonraki turunda daha fazla veri toplamak için el ile etiketlemeye geri döner. Zamanla, model daha yüksek bir görüntü oranında daha emin olur ve daha sonra projede daha sonra daha fazla etiket görevi elde eder.

## <a name="tag-images-for-multi-class-classification"></a>Çok sınıflı sınıflandırma için resimleri etiketleme

Projeniz "Image Classification Multi-Class" türünde ise, tüm görüntüye tek bir etiket atarsınız. Yönergeleri istediğiniz zaman gözden geçirmek için, **yönergeler** sayfasına gidin ve **ayrıntılı yönergeleri görüntüle**' yi seçin.

Bir resme etiket atadıktan sonra bir hata yaptık, bu hatayı çözebilirsiniz. Etiketi temizlemek için görüntünün altında görüntülenen etikette "**X**" işaretini seçin. Veya resmi seçip başka bir sınıf seçin. Yeni seçilen değer, daha önce uygulanan etiketin yerine geçecek.

## <a name="tag-images-for-multi-label-classification"></a>Çok etiketli sınıflandırma için resimleri etiketleme

"Görüntü sınıflandırması çoklu etiketi" türünde bir proje üzerinde çalışıyorsanız, bir görüntüye bir *veya daha fazla* etiket uygularsınız. Projeye özgü yönleri görmek için **yönergeler** ' i seçin ve **ayrıntılı yönergeleri görüntüleyin**.

Etiketlemek istediğiniz görüntüyü seçin ve ardından etiketi seçin. Etiket tüm seçili görüntülere uygulanır ve ardından görüntülerin seçimi kaldırılır. Daha fazla etiket uygulamak için görüntüleri yeniden seçmeniz gerekir. Aşağıdaki animasyon çok etiketli etiketlemesini göstermektedir:

1. "Okyanus" etiketini uygulamak için **Tümünü Seç** kullanılır.
1. Tek bir görüntü seçilir ve "closeup" olarak etiketlenir.
1. Üç görüntü seçilir ve "geniş açılı" olarak etiketlenir.

![Animasyon MultiLabel akışını gösterir](./media/how-to-label-images/multilabel.gif)

Bir hata düzeltmek için "**X**" düğmesine tıklayarak tek bir etiketi temizleyin veya görüntüleri seçin ve ardından etiketi seçerek seçili tüm görüntülerden etiketi temizler. Bu senaryo burada gösterilmiştir. "Land" tıklanması, seçili iki görüntüden bu etiketi temizler.

![Bir ekran görüntüsü birden çok seçimi gösterir](./media/how-to-label-images/multiple-deselection.png)

Azure yalnızca, her görüntüye en az bir etiket uyguladıktan sonra **Gönder** düğmesini etkinleştirir. Çalışmanızı kaydetmek için **Gönder** ' i seçin.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Nesne silme işlemi için resimleri etiketleme ve sınırlayıcı kutuları belirtme

Projeniz "nesne kimliği (sınırlayıcı kutular)" türündedir, görüntüde bir veya daha fazla sınırlayıcı kutu belirtip her kutuya bir etiket uygularsınız. Görüntülerde, her biri tek etiketli birden çok sınırlayıcı kutusu olabilir. Projenizde birden fazla sınırlayıcı kutunun kullanıldığını öğrenmek için **ayrıntılı yönergeleri görüntüle** ' i kullanın.

1. Oluşturmayı planladığınız sınırlama kutusu için bir etiket seçin.
1. **Dikdörtgen kutu** aracı ![ dikdörtgen kutusu aracını seçin ](./media/how-to-label-images/rectangular-box-tool.png) veya "R" seçeneğini belirleyin.
3. Kaba bir sınırlayıcı kutu oluşturmak için, Hedefinizdeki çapraz doğrultuda tıklayın ve sürükleyin. Sınırlayıcı kutuyu ayarlamak için kenarları veya köşeleri sürükleyin.

![Sınırlama kutusu oluşturma](./media/how-to-label-images/bounding-box-sequence.png)

Bir sınırlayıcı kutuyu silmek için, oluşturulduktan sonra sınırlayıcı kutunun yanında görünen X şeklindeki hedefe tıklayın.

Varolan bir sınırlayıcı kutunun etiketini değiştiremezsiniz. Etiket atama hatası yaparsanız, sınırlayıcı kutuyu silmeniz ve doğru etiketle yeni bir tane oluşturmanız gerekir.

Varsayılan olarak, varolan sınırlayıcı kutuları düzenleyebilirsiniz. **Kilitleme/kilit açma bölgesi** aracı ![ kilitleme/kilit açma bölgesi aracı ](./media/how-to-label-images/lock-bounding-boxes-tool.png) veya "L" Bu davranışa geçiş yapar. Bölgeler kilitliyse, yalnızca yeni bir sınırlayıcı kutunun şeklini veya konumunu değiştirebilirsiniz.

**Bölge işleme** aracını kullanın ![ Bu bölge işleme aracı simgelerdir. merkezden, yukarı, sağa, aşağı ve sola doğru işaret eden dört ok.](./media/how-to-label-images/regions-tool.png) ya da "e", varolan bir sınırlayıcı kutuyu ayarlamak için. Şekli ayarlamak için kenarları veya köşeleri sürükleyin. Tüm sınırlayıcı kutusunu sürüklemek için iç öğesine tıklayın. Bir bölgeyi düzenleyemezsiniz, büyük olasılıkla bir bölgeyi **Kilitle/kilidini aç** aracını atlayabilirsiniz.

 ![ ](./media/how-to-label-images/template-box-tool.png) Aynı boyutta birden fazla sınırlayıcı kutu oluşturmak için şablon tabanlı kutu aracı şablon kutusu aracını veya "T" kullanın. Görüntüde hiçbir sınırlayıcı kutu yoksa ve şablon tabanlı kutuları etkinleştirirseniz, araç 50-50 piksel kutu üretir. Bir sınırlayıcı kutusu oluşturup şablon tabanlı kutuları etkinleştirirseniz, yeni sınırlama kutuları oluşturduğunuz son kutunun boyutu olur. Şablon tabanlı kutular, yerleştirme sonrasında yeniden boyutlandırılabilir. Şablon tabanlı bir kutunun yeniden boyutlandırılması yalnızca belirli bir kutuyu yeniden boyutlandırır.

Geçerli görüntüdeki *Tüm* sınırlayıcı kutuları silmek için **tüm bölgeleri Sil** araç ![ bölgelerini Sil aracını seçin ](./media/how-to-label-images/delete-regions-tool.png) .

Bir görüntü için sınırlayıcı kutuları oluşturduktan sonra, çalışmanızı kaydetmek için **Gönder** ' i seçin veya sürmekte olan çalışmanız kaydedilmez.

## <a name="tag-images-and-specify-polygons-for-image-segmentation"></a>Resimleri etiketleme ve görüntü kesimlemesi için çokgenler belirtme 

Projeniz "örnek kesimleme (çokgen)" türündedir, görüntüde bir veya daha fazla poligonu belirtip her bir çokgen için bir etiket uygularsınız. Görüntülerde, her biri tek etiketli birden çok sınırlayıcı Çokgen olabilir. Projenizde birden çok sınırlayıcı poligonun kullanıldığını anlamak için **ayrıntılı yönergeleri görüntüle** ' i kullanın.

1. Oluşturmayı planladığınız Çokgen için bir etiket seçin.
1. Çokgen bölgesi  aracı çiz araç alanı ![ Çokgen ](./media/how-to-label-images/polygon-tool.png) veya "P" seçeneğini belirleyin.
1. Poligon her bir nokta için tıklayın.  Şekli tamamladıktan sonra, son ' a çift tıklayın.

    :::image type="content" source="media/how-to-label-images/polygon.gif" alt-text="Kedi ve köpek için çokgenler oluşturun":::

Bir çokgeni silmek için, oluşturulduktan sonra çokgenin yanında görünen X şeklindeki hedefe tıklayın.

Bir çokgenin etiketini değiştirmek istiyorsanız, **bölgeyi taşı** aracını seçin, Çokgen ' e tıklayın ve doğru etiketi seçin.

Mevcut poligonları düzenleyebilirsiniz. **Kilitleme/kilit açma bölgesi** Aracı, ![ poligonları kilitle/kilidini aç aracı ](./media/how-to-label-images/lock-bounding-boxes-tool.png) veya "L" olan düzenleme, bu davranışa geçiş yapar. Bölgeler kilitliyse, yalnızca yeni bir çokgenin şeklini veya konumunu değiştirebilirsiniz.

**Çokgen noktaları Ekle** veya Kaldır aracını kullanma ![ Bu, çokgen noktaları Ekle veya Kaldır araç simgedir.](./media/how-to-label-images/add-remove-points-tool.png) veya var olan bir çokgeni ayarlamak için "U". Bir nokta eklemek veya kaldırmak için Çokgen ' e tıklayın. Bir bölgeyi düzenleyemezsiniz, büyük olasılıkla bir bölgeyi **Kilitle/kilidini aç** aracını atlayabilirsiniz.

Geçerli görüntüdeki *Tüm* poligonları silmek için **tüm bölgeleri Sil** araç ![ tüm bölgeleri Sil aracını seçin ](./media/how-to-label-images/delete-regions-tool.png) .

Bir görüntü için çokgenler oluşturduktan sonra, çalışmanızı kaydetmek için **Gönder** ' i seçin veya sürmekte olan çalışmanız kaydedilmez.

## <a name="finish-up"></a>Bitirme

Etiketli verilerin bir sayfasını gönderdiğinizde Azure, bir iş kuyruğundan sizin için etiketlendirilmiş yeni veri atar. Etiketli daha fazla veri yoksa, bunu portal giriş sayfasının bağlantısı ile birlikte gösteren bir ileti alırsınız.

Etiketleme tamamlandığında, etiketleme portalının sağ üst köşesindeki adınızı seçin ve ardından **Oturumu Kapat**' ı seçin. Oturumu kapatma, sonunda Azure "sizin zaman aşımına uğrar" ve verilerinizi başka bir etiketleyici 'ye atacaktır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'da görüntü sınıflandırma modellerini eğitme](./tutorial-train-models-with-aml.md) hakkında bilgi edinin


