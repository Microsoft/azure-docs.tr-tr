---
title: Sınıflandırıcınızı geliştirme-Özel Görüntü İşleme Hizmeti
titleSuffix: Azure Cognitive Services
description: Bu makalede, miktardaki, kalitede ve çeşitli verilerin Özel Görüntü İşleme hizmetinde sınıflandırıcının kalitesini nasıl iyileştirebileceğinizi öğreneceksiniz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: c2858d5f9bca662cbbcd48b2345a7dc2c7ae48b2
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718532"
---
# <a name="how-to-improve-your-classifier"></a>Sınıflandırıcınızı geliştirme

Bu kılavuzda, Özel Görüntü İşleme Hizmeti sınıflandırıcının kalitesini nasıl iyileştireceğinizi öğreneceksiniz. Sınıflandırıcınız kalitesi, sağladığınız etiketli verilere ve genel veri kümesinin ne kadar dengeli olduğuna göre değişir. İyi bir sınıflandırıcının, sınıflandırıcının ne gönderdiklerine temsilci atanmış bir dengeli eğitim veri kümesi vardır. Böyle bir sınıflandırıcı oluşturma işlemi yinelemeli bir işlemdir; eğitimin beklenen sonuçlara ulaşabilmesi için birkaç kez bir kez daha yaygın hale gelir.

Aşağıda, daha doğru bir sınıflandırıcı oluşturmanıza yardımcı olacak genel bir model verilmiştir:

1. Birinci yuvarlama eğitimi
1. Daha fazla görüntü ekleyin ve verileri dengeleyin; yeniden eğitme
1. Farklı arka plan, aydınlatma, nesne boyutu, kamera açısı ve stil içeren görüntüler ekleyin; yeniden eğitme
1. Tahmin testi için yeni görüntü (ler) kullanın
1. Mevcut eğitim verilerini tahmin sonuçlarına göre değiştirin

## <a name="prevent-overfitting"></a>Fazla sığdırmayı engelle

Bazen bir sınıflandırıcı, görüntülerinizin ortak olduğunu rastgele özelliklere göre tahminleri yapmayı öğrenmeyecektir. Örneğin, eller vs. Citrus için bir sınıflandırıcı oluşturuyorsanız ve beyaz levhalar üzerinde ve Citrus 'de elmalar görüntülerini kullandıysanız, sınıflandırıcı vs. Citrus yerine ellerde ve bu levhalar için önemli olmayan önem verebilir.

![Beklenmeyen sınıflandırmanın görüntüsü](./media/getting-started-improving-your-classifier/unexpected.png)

Bu sorunu düzeltmek için, daha değişen görüntülerle eğitim için aşağıdaki kılavuzu kullanın: farklı açılara, arka plana, nesne boyutuna, gruplara ve diğer çeşitlere sahip görüntüler sağlayın.

## <a name="data-quantity"></a>Veri miktarı

Eğitim görüntülerinin sayısı en önemli faktördür. Başlangıç noktası olarak etiket başına en az 50 görüntü kullanmanızı öneririz. Daha az görüntü sayesinde, çok fazla ekleme riski daha yüksektir ve performans sayılarınız iyi kalite önerebilir, ancak modeliniz gerçek dünyada verilerle uğraşmaya başlayabilir. 

## <a name="data-balance"></a>Veri dengesi

Eğitim verilerinizin göreli miktarları göz önünde bulundurmanız de önemlidir. Örneğin, bir etiket için 500 görüntü, başka bir etiket için 50 görüntüleri ise imdendengelenmiş bir eğitim veri kümesi için yapılır. Bu, modelin bir etiketi diğerinden tahmin etmek için daha doğru olmasına neden olur. En az görüntüler ve en çok görüntü içeren etiket arasında en az bir 1:2 oranını tuttuğunuz zaman daha iyi sonuçlar elde edersiniz. Örneğin, en çok görüntünün bulunduğu etikette 500 görüntü varsa, en az görüntüde bulunan etikette eğitim için en az 250 görüntü olmalıdır.

## <a name="data-variety"></a>Veri çeşitleri

Normal kullanım sırasında sınıflandırıcıya gönderilecek öğeleri temsil eden görüntüleri kullandığınızdan emin olun. Aksi takdirde, sınıflandırmalarınız, görüntülerinizin ortak olduğunu rastgele özelliklere göre tahmine dayalı hale getirme hakkında bilgi verebilir. Örneğin, eller vs. Citrus için bir sınıflandırıcı oluşturuyorsanız ve beyaz levhalar üzerinde ve Citrus 'de elmalar görüntülerini kullandıysanız, sınıflandırıcı vs. Citrus yerine ellerde ve bu levhalar için önemli olmayan önem verebilir.

![Beklenmeyen sınıflandırmanın görüntüsü](./media/getting-started-improving-your-classifier/unexpected.png)

Bu sorunu düzeltmek için, sınıflandırıcınızı iyi genelleştirdiğinden emin olmak üzere çeşitli görüntüler ekleyin. Aşağıda, eğitim ayarlarınızı daha farklı hale getirmek için kullanabileceğiniz bazı yollar verilmiştir:

* __Arka plan:__ Nesnenizin görüntülerini farklı arka planların önüne girin. Doğal bağlamlardaki fotoğraflar, sınıflandırıcının daha fazla bilgi sağlaması için nötr arka planların önünde bulunan fotoğraflardan daha iyidir.

    ![Arka plan örneklerinin görüntüsü](./media/getting-started-improving-your-classifier/background.png)

* __Aydınlatma:__ Özellikle tahmin için kullanılan görüntülerin farklı ışıklandırmaya sahip olması halinde değişen aydınlatma (yani, Flash, yüksek pozlama vb. ile alınan) görüntüleri sağlayın. Ayrıca, değişen doygunluk, ton ve parlaklık ile görüntüleri kullanmak da yararlıdır.

    ![Aydınlatma örneklerinin görüntüsü](./media/getting-started-improving-your-classifier/lighting.png)

* __Nesne boyutu:__ Nesnelerin boyut ve sayı olarak değişen görüntülerini sağlayın (örneğin, muzlar ve tek bir muz için bir adet). Farklı boyutlandırma, sınıflandırıcının daha iyi genelleştirilmesine yardımcı olur.

    ![Boyut örnekleri görüntüsü](./media/getting-started-improving-your-classifier/size.png)

* __Kamera açısı:__ Farklı kamera açılarla çekilen görüntüleri sağlayın. Alternatif olarak, tüm fotoğraflarınızın sabit kameralar (örneğin, Gözetleme Kameralar) ile alınması gerekiyorsa, ilgisiz nesneleri (örneğin, lampgönderimleri gibi) yorumlama&mdash;anahtar özelliği.

    ![Açı örneklerinin görüntüsü](./media/getting-started-improving-your-classifier/angle.png)

* __Stil:__ Aynı sınıfa ait farklı stillerin görüntülerini sağlayın (örneğin, aynı meyve 'in farklı değişen özellikleri). Ancak, büyük ölçüde farklı stilleriniz varsa (örneğin, Mickey Mouse ve gerçek hayatta bir fare), farklı özelliklerini daha iyi göstermek için bunları ayrı sınıflar olarak etiketlemenize önerilir.

    ![Stil örnekleri görüntüsü](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Negatif görüntüler

Projenizde bir noktada, sınıflandırıcınızı daha doğru hale getirmenize yardımcı olmak için _negatif örnekler_ eklemeniz gerekebilir. Negatif örnekler, diğer etiketlerden hiçbiriyle eşleşmeyen olanlardır. Bu görüntüleri karşıya yüklediğinizde, özel **negatif** etiketi bunlara uygulayın.

> [!NOTE]
> Özel Görüntü İşleme Hizmeti, bazı otomatik negatif görüntü işlemeyi destekler. Örneğin, bir Grape vs. muz Sınıflandırıcısı oluşturuyorsanız ve tahmin için bir showe görüntüsü gönderirseniz, sınıflandırıcının hem Grape hem de muz için %0 ' a yakın olması gerekir.
> 
> Öte yandan, negatif görüntülerin yalnızca eğitiminde kullanılan görüntülerin bir çeşitlemesi olduğu durumlarda, büyük benzerlikler nedeniyle modelin negatif görüntüleri etiketli bir sınıf olarak sınıflandırmasına neden olur. Örneğin, bir turuncu vs. grapemeyve sınıflandırıcınız varsa ve bir klementin 'nin görüntüsüne akış yaparsanız, klementin 'nın birçok özelliği Portages 'lere benzediğinden, klementin bir turuncu olarak puan verebilir. Negatif görüntüleriniz bu doğası içeriyorsa, modelin bu sınıflar arasında daha iyi ayırt edilmesini sağlamak için eğitim sırasında bir veyadaha fazla ek etiket oluşturmanızı ve bu etiketle negatif görüntüleri etiketlemesini öneririz.

## <a name="use-prediction-images-for-further-training"></a>Daha fazla eğitim için tahmin görüntülerini kullanma

Tahmin uç noktasına görüntü göndererek görüntü sınıflandırıcısını kullandığınızda veya test ettiğinizde, Özel Görüntü İşleme Hizmeti bu görüntüleri depolar. Ardından, modeli geliştirmek için bunları kullanabilirsiniz.

1. Sınıflandırıcıya gönderilen görüntüleri görüntülemek için, [özel görüntü işleme Web sayfasını](https://customvision.ai)açın, projenize gidin ve __tahmin__ sekmesini seçin. Varsayılan görünüm geçerli yinelemeden resimleri gösterir. Önceki yinelemeler sırasında gönderilen görüntüleri görüntülemek için __yineleme__ açılan menüsünü kullanabilirsiniz.

    ![tahmine dayalı sekmesinin ekran görüntüsü, görünümdeki görüntülerle birlikte](./media/getting-started-improving-your-classifier/predictions.png)

2. Sınıflandırıcı tarafından tahmin edilen etiketleri görmek için bir görüntünün üzerine gelin. Görüntüler, Sınıflandırıcıların en iyi geliştirmelerini getirebileceği şekilde sıralanır. Farklı bir sıralama yöntemi kullanmak için __sıralama__ bölümünde bir seçim yapın. 

    Mevcut eğitim verilerinize bir görüntü eklemek için görüntüyü seçin, doğru etiketleri ayarlayın ve __Kaydet ve Kapat__' a tıklayın. Görüntü __tahminlerden__ kaldırılır ve eğitim görüntüleri kümesine eklenir. Bunu, __eğitim görüntüleri__ sekmesini seçerek görüntüleyebilirsiniz.

    ![Etiketleme sayfasının görüntüsü](./media/getting-started-improving-your-classifier/tag.png)

3. Ardından, sınıflandırıcının yeniden eğmesi için __eğitme__ düğmesini kullanın.

## <a name="visually-inspect-predictions"></a>Tahmin öngörülerini görsel olarak inceleyin

Görüntü tahminlerini denetlemek için __eğitim görüntüleri__ sekmesine gidin, **yineleme** açılan menüsünde önceki eğitim yinelemenize tıklayın ve **Etiketler** bölümünün altındaki bir veya daha fazla etiketi kontrol edin. Görünüm artık, modelin verilen etiketi doğru tahmin edebile ilişkin her bir görüntünün etrafında kırmızı bir kutu görüntülemesi gerekir.

![Yineleme geçmişinin görüntüsü](./media/getting-started-improving-your-classifier/iteration.png)

Bazen görsel inceleme, daha fazla eğitim verisi ekleyerek veya mevcut eğitim verilerini değiştirerek düzeltebileceğiniz desenleri tanımlayabilir. Örneğin, elmalar ile ilgili bir sınıflandırıcı, tüm yeşil elleri yanlış şekilde etiketleyebilir. Daha sonra bu sorunu, Yeşil elmalar etiketli görüntülerini içeren eğitim verileri ekleyerek ve sağlayarak düzeltebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, özel görüntü sınıflandırma modelinizi daha doğru hale getirmek için çeşitli teknikler öğrendiniz. Daha sonra, görüntüleri bir tahmin API 'sine göndererek programlı bir şekilde test etme hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [Tahmin API 'sini kullanma](use-prediction-api.md)
