---
title: Hızlı başlangıç derleme Sınıflandırıcısı-Özel Görüntü İşleme Hizmeti
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Özel Görüntü İşleme Web sitesini kullanarak bir görüntü sınıflandırma modeli oluşturma hakkında bilgi edineceksiniz.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: anroth
ms.openlocfilehash: 12be696c74a32909d79be405144582cd8fc05fb6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "73519148"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Hızlı başlangıç: Özel Görüntü İşleme Sınıflandırıcısı oluşturma

Bu hızlı başlangıçta, Özel Görüntü İşleme Web sitesi aracılığıyla bir sınıflandırıcı oluşturmayı öğreneceksiniz. Bir sınıflandırıcı modeli oluşturduktan sonra, görüntü sınıflandırması için Özel Görüntü İşleme hizmetini kullanabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- Sınıflandırıcınızı eğiten bir görüntü kümesi. Görüntüleri seçme hakkında ipuçları için aşağıya bakın.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Azure portal Özel Görüntü İşleme kaynakları oluşturma

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Yeni bir proje oluşturma

Web tarayıcınızda [özel görüntü işleme Web sayfasına](https://customvision.ai) gidin ve __oturum aç '__ ı seçin. Azure portal oturum açmak için kullandığınız hesapla oturum açın.

![Oturum açma sayfasının görüntüsü](./media/browser-home.png)


1. İlk projenizi oluşturmak için **Yeni proje**' yi seçin. **Yeni proje oluştur** iletişim kutusu görüntülenir.

    ![Yeni proje iletişim kutusunda ad, açıklama ve etki alanları için alanlar bulunur.](./media/getting-started-build-a-classifier/new-project.png)

1. Proje için bir ad ve açıklama girin. Ardından bir kaynak grubu seçin. Oturum açmış hesabınız bir Azure hesabıyla ilişkiliyse, kaynak grubu açılır listesi bir Özel Görüntü İşleme Hizmeti kaynağı içeren tüm Azure Kaynak gruplarınızı görüntüler. 

   > [!NOTE]
   > Kullanılabilir kaynak grubu yoksa, lütfen [Azure Portal](https://portal.azure.com/)oturum açmak için kullandığınız hesapla [customvision.ai](https://customvision.ai) oturum açtığınızdan emin olun. Ayrıca, lütfen Özel Görüntü İşleme kaynaklarınızın bulunduğu Azure portal dizin olarak Özel Görüntü İşleme portalında aynı "Dizin" i seçtiğinizi onaylayın. Her iki sitede, ekranın sağ üst köşesindeki açılan hesap menüsünden dizininizi seçebilirsiniz. 

1. __Proje türleri__altında __Sınıflandırmayı__ seçin. Daha sonra, __sınıflandırma türleri__altında, kullanım durumunuza bağlı olarak **MultiLabel** veya **birden çok sınıf**seçeneklerinden birini belirleyin. Çok etiketli sınıflandırma bir görüntüye (sıfır veya daha fazla) etiket uygular, ancak birden çok Lass sınıflandırması görüntüleri tek kategorilere göre sıralar (gönderdiğiniz her görüntü, en olası etiket ile sıralanır). İsterseniz sınıflandırma türünü daha sonra değiştirebileceksiniz.

1. Sonra, kullanılabilir etki alanlarından birini seçin. Her etki alanı, aşağıdaki tabloda açıklandığı gibi belirli görüntü türleri için sınıflandırıcıyı iyileştirir. İsterseniz, etki alanını daha sonra değiştirebileceksiniz.

    |Domain|Amaç|
    |---|---|
    |__Yorlar__| Çok çeşitli resim Sınıflandırma görevleri için iyileştirilmiştir. Diğer etki alanlarından hiçbiri uygun değilse veya hangi etki alanının seçeceğinizden emin değilseniz, genel etki alanını seçin. |
    |__Yemek__|Bir restoran menüsünde gördüğünüz gibi yemeklerin fotoğrafları için iyileştirildi. Bireysel meyve veya vegetables fotoğraflarını sınıflandırmak istiyorsanız, yiyecek etki alanını kullanın.|
    |__Sakal__|Hem doğal hem de yapay olan tanınabilir yer işaretleri için iyileştirilmiştir. Bu etki alanı, yer işareti fotoğrafta açık bir şekilde görünür olduğunda en iyi şekilde çalışıyor. Bu etki alanı, yer işareti, önündeki kişiler tarafından biraz engelde olsa bile çalışıyor.|
    |__Maliyeti__|Bir alışveriş kataloğunda veya alışveriş web sitesinde bulunan görüntüler için iyileştirilmiştir. Dresler, Pants ve Shirts arasında yüksek duyarlıklı sınıflandırmaları istiyorsanız bu etki alanını kullanın.|
    |__Compact etki alanları__| Mobil cihazlardaki gerçek zamanlı sınıflandırmanın kısıtlamaları için iyileştirildi. Compact etki alanları tarafından oluşturulan modeller yerel olarak çalışacak şekilde aktarılabilir.|

1. Son olarak, __proje oluştur__' u seçin.

## <a name="choose-training-images"></a>Eğitim görüntülerini seçin

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Görüntüleri karşıya yükleme ve etiketleme

Bu bölümde, sınıflandırıcının eğmesini sağlamaya yardımcı olmak için görüntüleri karşıya yükleyip el ile etiketlemelisiniz. 

1. Görüntü eklemek için __Görüntü ekle__ düğmesine tıklayın ve ardından __yerel dosyalara gözatamazsınız__' ı seçin. Etiketlemeyle gezinmek için __Aç__ ' ı seçin. Etiket seçiminiz, karşıya yüklemek üzere seçtiğiniz tüm görüntü grubuna uygulanır, bu nedenle görüntüleri istediğiniz etiketlere göre ayrı gruplara karşıya yüklemek daha kolay olur. Ayrıca, her bir görüntünün etiketlerini karşıya yükledikten sonra değiştirebilirsiniz.

    ![Resim ekle denetimi, sol üst köşede ve alt ortadaki bir düğme olarak gösterilir.](./media/getting-started-build-a-classifier/add-images01.png)


1. Etiket oluşturmak için __etiketlerim__ alanına metin girin ve ENTER tuşuna basın. Etiket zaten varsa, açılan menüde görünür. Çok etiketli bir projede, resimlerinize birden fazla etiket ekleyebilirsiniz, ancak birden çok Lass projesinde yalnızca bir tane ekleyebilirsiniz. Görüntüleri karşıya yüklemeyi tamamlaması için __karşıya yükleme [sayı] dosyaları__ düğmesini kullanın. 

    ![Etiket ve karşıya yükleme sayfasının görüntüsü](./media/getting-started-build-a-classifier/add-images03.png)

1. Görüntüler karşıya yüklendikten sonra __bitti__ ' yi seçin.

    ![İlerleme çubuğu tamamlanan tüm görevleri gösterir.](./media/getting-started-build-a-classifier/add-images04.png)

Başka bir görüntü kümesini karşıya yüklemek için bu bölümün üst kısmına dönün ve adımları tekrarlayın.

## <a name="train-the-classifier"></a>Sınıflandırıcıyı eğitme

Sınıflandırıcının eğmesi için **eğitme** düğmesini seçin. Sınıflandırıcı, her bir etiketin görsel kalitelerini tanımlayan bir model oluşturmak için tüm geçerli görüntüleri kullanır.

![Web sayfasının başlık araç çubuğunun sağ üst köşesindeki eğitme düğmesi](./media/getting-started-build-a-classifier/train01.png)

Eğitim süreci yalnızca birkaç dakika sürer. Bu süre boyunca, eğitim işlemiyle ilgili bilgiler **performans** sekmesinde görüntülenir.

![Ana bölümde eğitim iletişim kutusuyla tarayıcı penceresi](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Sınıflandırıcının değerlendirmesi

Eğitim tamamlandıktan sonra, modelin performansı tahmini ve görüntülenir. Özel Görüntü İşleme Hizmeti, [k katlamalı çapraz doğrulama](https://en.wikipedia.org/wiki/Cross-validation_(statistics))adlı bir işlem kullanarak duyarlık ve geri çekme işlemlerini hesaplamak için gönderdiğiniz görüntüleri kullanır. Duyarlık ve geri çekme, sınıflandırıcının verimliliğinden oluşan iki farklı ölçümlerdir:

- **Duyarlık** , doğru olan belirlenen sınıflandırmaların kesirini gösterir. Örneğin, model, 100 görüntüsünü köpekler olarak tanımlarsa ve bunların 99 ' i gerçekten dotalar ise, duyarlık %99% olur.
- **Geri çağırma** , doğru şekilde tanımlanan gerçek sınıflandırmaların kesirini belirtir. Örneğin, gerçekten de 100 resim ve bir model, her ikisi de 80 olarak tanımlanmış ise, geri çekme %80% olur.

![Eğitim sonuçları, sınıflandırıcının her bir etiketi için genel duyarlık ve geri çekmeyi ve duyarlık ve geri çekmeyi gösterir.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Olasılık eşiği

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Eğitim yinelemelerini yönetme

Sınıflandırıcınızı her eğişinizde, kendi güncelleştirilmiş performans ölçümleriyle yeni bir _yineleme_ oluşturursunuz. Tüm yinelemelerinizi **performans** sekmesinin sol bölmesinde görüntüleyebilirsiniz. Sol bölmede, bir yinelemeyi silmek için kullanabileceğiniz **Sil** düğmesini de bulabilirsiniz. Bir yinelemeyi sildiğinizde, onunla benzersiz olarak ilişkili olan tüm görüntüleri silersiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Özel Görüntü İşleme Web sitesini kullanarak bir görüntü sınıflandırma modeli oluşturmayı ve eğiteyi öğrendiniz. Daha sonra, modelinizi iyileştirmeye yönelik yinelemeli süreç hakkında daha fazla bilgi alın.

> [!div class="nextstepaction"]
> [Modeli test etme ve yeniden eğitme](test-your-model.md)

