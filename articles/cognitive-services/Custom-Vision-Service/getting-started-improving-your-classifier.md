---
title: Modelinizi geliştirme-Özel Görüntü İşleme Hizmeti
titleSuffix: Azure Cognitive Services
description: Bu makalede, miktardaki, kalitede ve çeşitli verilerin Özel Görüntü İşleme hizmetinde modelinizin kalitesini nasıl iyileştirebileceğinizi öğreneceksiniz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: ae0112292994fdcf88e80abff8ab52e5971cb0ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101731094"
---
# <a name="how-to-improve-your-custom-vision-model"></a>Özel Görüntü İşleme modelinizi geliştirme

Bu kılavuzda, Özel Görüntü İşleme Hizmeti modelinizin kalitesini geliştirmeyi öğreneceksiniz. [Sınıflandırıcınız](./getting-started-build-a-classifier.md) veya [nesne algılayıcısının](./get-started-build-detector.md) kalitesi, sağladığınız etiketli verilere ve genel veri kümesinin ne kadar dengeli olduğuna bağlıdır. İyi bir modelde, kendisine nelerin gönderilebileceklerini temsil eden bir dengeli eğitim veri kümesi vardır. Böyle bir modeli oluşturma işlemi yinelemeli bir işlemdir; eğitimin beklenen sonuçlara ulaşabilmesi için birkaç kez bir kez daha yaygın hale gelir.

Aşağıda, daha doğru bir modeli eğmenize yardımcı olacak genel bir örüntü verilmiştir:

1. Birinci yuvarlama eğitimi
1. Daha fazla görüntü ekleyin ve verileri dengeleyin; yeniden eğitme
1. Farklı arka plan, aydınlatma, nesne boyutu, kamera açısı ve stil içeren görüntüler ekleyin; yeniden eğitme
1. Tahmin testi için yeni görüntü (ler) kullanın
1. Mevcut eğitim verilerini tahmin sonuçlarına göre değiştirin

## <a name="prevent-overfitting"></a>Fazla sığdırmayı engelle

Bazen, bir model, görüntülerinizin ortak olduğunu rastgele özelliklere göre tahminleri yapmayı öğrenmeyecektir. Örneğin, eller vs. Citrus için bir sınıflandırıcı oluşturuyorsanız ve beyaz levhalar üzerinde ve Citrus 'de elmalar görüntülerini kullandıysanız, sınıflandırıcı vs. Citrus yerine ellerde ve bu levhalar için önemli olmayan önem verebilir.

![Beklenmeyen sınıflandırmanın görüntüsü](./media/getting-started-improving-your-classifier/unexpected.png)

Bu sorunu düzeltmek için, farklı açılara, arka plana, nesne boyutuna, gruplara ve diğer çeşitlere sahip görüntüler sağlayın. Aşağıdaki bölümler, bu kavramların üzerine genişletilir.

## <a name="data-quantity"></a>Veri miktarı

Eğitim görüntülerinin sayısı, veri kümeniz için en önemli faktördür. Başlangıç noktası olarak etiket başına en az 50 görüntü kullanmanızı öneririz. Daha az görüntü sayesinde, çok fazla ekleme riski daha yüksektir ve performans sayılarınız iyi kalite önerebilir, ancak modeliniz gerçek dünyada verilerle uğraşmaya başlayabilir. 

## <a name="data-balance"></a>Veri dengesi

Eğitim verilerinizin göreli miktarları göz önünde bulundurmanız de önemlidir. Örneğin, bir etiket için 500 görüntü, başka bir etiket için 50 görüntüleri ise imdendengelenmiş bir eğitim veri kümesi için yapılır. Bu, modelin bir etiketi diğerinden tahmin etmek için daha doğru olmasına neden olur. En az görüntüler ve en çok görüntü içeren etiket arasında en az bir 1:2 oranını tuttuğunuz zaman daha iyi sonuçlar elde edersiniz. Örneğin, en çok görüntünün bulunduğu etikette 500 görüntü varsa, en az görüntüde bulunan etikette eğitim için en az 250 görüntü olmalıdır.

## <a name="data-variety"></a>Veri çeşitleri

Normal kullanım sırasında sınıflandırıcıya gönderilecek öğeleri temsil eden görüntüleri kullandığınızdan emin olun. Aksi halde modelinize, görüntülerinizin ortak olarak sahip olduğu rastgele özelliklere göre tahminler yapmayı öğrenirsiniz. Örneğin, eller vs. Citrus için bir sınıflandırıcı oluşturuyorsanız ve beyaz levhalar üzerinde ve Citrus 'de elmalar görüntülerini kullandıysanız, sınıflandırıcı vs. Citrus yerine ellerde ve bu levhalar için önemli olmayan önem verebilir.

![Beklenmeyen sınıflandırmanın görüntüsü](./media/getting-started-improving-your-classifier/unexpected.png)

Bu sorunu düzeltmek için, modelinizin iyi genelleştirildiğinden emin olmak için çeşitli görüntüler ekleyin. Aşağıda, eğitim ayarlarınızı daha farklı hale getirmek için kullanabileceğiniz bazı yollar verilmiştir:

* __Arka plan:__ Nesnenizin görüntülerini farklı arka planların önüne girin. Doğal bağlamlardaki fotoğraflar, sınıflandırıcının daha fazla bilgi sağlaması için nötr arka planların önünde bulunan fotoğraflardan daha iyidir.

    ![Arka plan örneklerinin görüntüsü](./media/getting-started-improving-your-classifier/background.png)

* __Aydınlatma:__ Özellikle tahmin için kullanılan görüntülerin farklı ışıklandırmaya sahip olması halinde değişen aydınlatma (yani, Flash, yüksek pozlama vb. ile alınan) görüntüleri sağlayın. Ayrıca, değişen doygunluk, ton ve parlaklık ile görüntüleri kullanmak da yararlıdır.

    ![Aydınlatma örneklerinin görüntüsü](./media/getting-started-improving-your-classifier/lighting.png)

* __Nesne boyutu:__ Nesnelerin boyut ve sayı olarak değişen görüntülerini sağlayın (örneğin, muzlar ve tek bir muz için bir adet). Farklı boyutlandırma, sınıflandırıcının daha iyi genelleştirilmesine yardımcı olur.

    ![Boyut örnekleri görüntüsü](./media/getting-started-improving-your-classifier/size.png)

* __Kamera açısı:__ Farklı kamera açılarla çekilen görüntüleri sağlayın. Alternatif olarak, tüm fotoğraflarınızın sabit kameralar (örneğin, Gözetleme Kameralar) ile alınması gerekiyorsa, &mdash; ilgisiz nesneleri (örneğin, lampgönderimleri gibi) anahtar özelliği olarak yorumlamalarına engel olmak için her düzenli olarak oluşan her nesneye farklı bir etiket atadığınızdan emin olun.

    ![Açı örneklerinin görüntüsü](./media/getting-started-improving-your-classifier/angle.png)

* __Stil:__ Aynı sınıfa ait farklı stillerin görüntülerini sağlayın (örneğin, aynı meyve 'in farklı değişen özellikleri). Ancak, büyük ölçüde farklı stilleriniz varsa (örneğin, Mickey Mouse ve gerçek hayatta bir fare), farklı özelliklerini daha iyi göstermek için bunları ayrı sınıflar olarak etiketlemenize önerilir.

    ![Stil örnekleri görüntüsü](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images-classifiers-only"></a>Negatif görüntüler (yalnızca sınıflandırıcılar)

Resim sınıflandırıcısını kullanıyorsanız, sınıflandırıcınızı daha doğru hale getirmenize yardımcı olması için _negatif örnekler_ eklemeniz gerekebilir. Negatif örnekler, diğer etiketlerden hiçbiriyle eşleşmeyen görüntülerdir. Bu görüntüleri karşıya yüklediğinizde, özel **negatif** etiketi bunlara uygulayın.

Çizilmiş sınırlayıcı kutular dışında herhangi bir görüntü alanı negatif olarak kabul edildiğinden nesne algılayıcıları negatif örnekleri otomatik olarak işler.

> [!NOTE]
> Özel Görüntü İşleme Hizmeti, bazı otomatik negatif görüntü işlemeyi destekler. Örneğin, bir Grape vs. muz Sınıflandırıcısı oluşturuyorsanız ve tahmin için bir showe görüntüsü gönderirseniz, sınıflandırıcının hem Grape hem de muz için %0 ' a yakın olması gerekir.
> 
> Öte yandan, negatif görüntülerin yalnızca eğitiminde kullanılan görüntülerin bir çeşitlemesi olduğu durumlarda, büyük benzerlikler nedeniyle modelin negatif görüntüleri etiketli bir sınıf olarak sınıflandırmasına neden olur. Örneğin, bir turuncu vs. grapemeyve sınıflandırıcınız varsa ve bir klementin 'nin görüntüsüne akış yaparsanız, klementin 'nın birçok özelliği Portages 'lere benzediğinden, klementin bir turuncu olarak puan verebilir. Negatif görüntüleriniz bu doğası içeriyorsa, modelin bu sınıflar arasında daha iyi ayırt edilmesini sağlamak için eğitim sırasında bir veya daha fazla ek etiket oluşturmanızı ve bu etiketle negatif görüntüleri etiketlemesini öneririz.

## <a name="consider-occlusion-and-truncation-object-detectors-only"></a>Kabul etmeyi ve kesme yapmayı düşünün (yalnızca nesne algılayıcıları)

Nesne algılayıcısı 'nın kesilen nesneleri (nesne kısmen kesilmiş) veya occluğlu nesneleri algılamasını istiyorsanız (nesne görüntüdeki başka bir nesne tarafından kısmen engelleniyorsa), bu servis taleplerini kapsayan eğitim görüntülerini dahil etmeniz gerekir.

> [!NOTE]
> Diğer nesneler tarafından occluşmuş nesnelerin sorunu, bir derecelendirme modeli performansı için bir parametre olan **Çakışma eşiğine** göre karıştırılmamalıdır. [Özel görüntü işleme Web sitesindeki](https://customvision.ai) **Çakışma eşiği** kaydırıcısı, tahmin edilen bir sınırlayıcı kutusunun doğru olarak kabul edilmesi için doğru sınırlayıcı kutusuyla ne kadar çakışmalıdır.

## <a name="use-prediction-images-for-further-training"></a>Daha fazla eğitim için tahmin görüntülerini kullanma

Tahmin uç noktasına görüntü göndererek modeli kullandığınızda veya test ettiğinizde, Özel Görüntü İşleme Hizmeti bu görüntüleri depolar. Ardından, modeli geliştirmek için bunları kullanabilirsiniz.

1. Modele gönderilen görüntüleri görüntülemek için, [özel görüntü işleme Web sayfasını](https://customvision.ai)açın, projenize gidin ve __tahmin__ sekmesini seçin. Varsayılan görünüm geçerli yinelemeden resimleri gösterir. Önceki yinelemeler sırasında gönderilen görüntüleri görüntülemek için __yineleme__ açılan menüsünü kullanabilirsiniz.

    ![tahmine dayalı sekmesinin ekran görüntüsü, görünümdeki görüntülerle birlikte](./media/getting-started-improving-your-classifier/predictions.png)

2. Model tarafından tahmin edilen etiketleri görmek için bir görüntünün üzerine gelin. Görüntüler, modele en iyi iyileştirmeleri getirebilen bir şekilde sıralanır. Farklı bir sıralama yöntemi kullanmak için __sıralama__ bölümünde bir seçim yapın. 

    Mevcut eğitim verilerinize bir görüntü eklemek için görüntüyü seçin, doğru etiketleri ayarlayın ve __Kaydet ve Kapat__' a tıklayın. Görüntü __tahminlerden__ kaldırılır ve eğitim görüntüleri kümesine eklenir. Bunu, __eğitim görüntüleri__ sekmesini seçerek görüntüleyebilirsiniz.

    ![Etiketleme sayfasının görüntüsü](./media/getting-started-improving-your-classifier/tag.png)

3. Ardından, modeli yeniden eğitebilmeniz için __eğitme__ düğmesini kullanın.

## <a name="visually-inspect-predictions"></a>Tahmin öngörülerini görsel olarak inceleyin

Görüntü tahminlerini denetlemek için __eğitim görüntüleri__ sekmesine gidin, **yineleme** açılan menüsünde önceki eğitim yinelemenize tıklayın ve **Etiketler** bölümünün altındaki bir veya daha fazla etiketi kontrol edin. Görünüm artık, modelin verilen etiketi doğru tahmin edebile ilişkin her bir görüntünün etrafında kırmızı bir kutu görüntülemesi gerekir.

![Yineleme geçmişinin görüntüsü](./media/getting-started-improving-your-classifier/iteration.png)

Bazen görsel inceleme, daha fazla eğitim verisi ekleyerek veya mevcut eğitim verilerini değiştirerek düzeltebileceğiniz desenleri tanımlayabilir. Örneğin, elmalar ile ilgili bir sınıflandırıcı, tüm yeşil elleri yanlış şekilde etiketleyebilir. Daha sonra bu sorunu, Yeşil elmalar etiketli görüntülerini içeren eğitim verileri ekleyerek ve sağlayarak düzeltebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, özel görüntü sınıflandırma modelinizi veya nesne algılayıcı modelinizi daha doğru hale getirmek için çeşitli teknikler öğrendiniz. Daha sonra, görüntüleri bir tahmin API 'sine göndererek programlı bir şekilde test etme hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [Tahmin API’sini kullanma](use-prediction-api.md)