---
title: Dil modelini Video Indexer Web sitesiyle özelleştirme
titleSuffix: Azure Media Services
description: Bir dil modelini Video Indexer Web sitesiyle özelleştirmeyi öğrenin.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: dd8b36340deb6c785989107461dd420e7fc0d985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97722581"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Video Indexer Web sitesiyle bir dil modeli özelleştirme

Video Indexer, altyapı özelliğini benzetirecek olan etki alanındaki metin olan uyarlama metnini karşıya yükleyerek konuşma tanımayı özelleştirmek için özel dil modelleri oluşturmanızı sağlar. Modelinize eğtikten sonra, uyarlama metninde görüntülenen yeni kelimeler tanınacaktır.

Özel dil modelleriyle ilgili ayrıntılı genel bakış ve en iyi uygulamalar için bkz. [video Indexer bir dil modelini özelleştirme](customize-language-model-overview.md).

Bu konuda açıklandığı gibi, hesabınızda özel dil modelleri oluşturmak ve düzenlemek için Video Indexer Web sitesini kullanabilirsiniz. API 'yi [API 'leri kullanarak dil modelini özelleştirme](customize-language-model-with-api.md)bölümünde açıklandığı gibi de kullanabilirsiniz.

## <a name="create-a-language-model"></a>Dil modeli oluşturma

1. [Video Indexer](https://www.videoindexer.ai/) Web sitesine gidin ve oturum açın.
1. Hesabınızdaki bir modeli özelleştirmek için sayfanın solundaki **içerik modeli özelleştirmesi** düğmesini seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-language-model/model-customization.png" alt-text="Video Indexer içerik modelini özelleştirme":::
1. **Dil** sekmesini seçin.

    Desteklenen dillerin listesini görürsünüz.
1. İstediğiniz dilin altında **Model Ekle**' yi seçin.
1. Dil modelinin adını yazın ve ENTER tuşuna basın.

    Bu adım modeli oluşturur ve modele metin dosyaları yükleme seçeneği sunar.
1. Bir metin dosyası eklemek için **Dosya Ekle**' yi seçin. Dosya Gezgini açılır.
1. Adresine gidin ve metin dosyasını seçin. Bir dil modeline birden çok metin dosyası ekleyebilirsiniz.

    Ayrıca, dil modelinin sağ tarafındaki **...** düğmesini seçip **Dosya Ekle**' ye tıklayarak da bir metin dosyası ekleyebilirsiniz.
1. Metin dosyalarını karşıya yüklemeyi tamamladıktan sonra yeşil **eğitme** seçeneğini belirleyin.

Eğitim işlemi birkaç dakika sürebilir. Eğitim yapıldıktan sonra, modelin yanında **eğitilen** ' u görürsünüz. Dosyayı modelden önizleyebilir, indirebilir ve silebilirsiniz.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/customize-language-model/customize-language-model.png" alt-text="Modeli eğitme":::

### <a name="using-a-language-model-on-a-new-video"></a>Yeni bir videoda dil modeli kullanma

Dil modelinizi yeni bir videoda kullanmak için aşağıdaki eylemlerden birini yapın:

* Sayfanın üst kısmındaki **karşıya yükle** düğmesini seçin.

    ![Karşıya yükle düğme Video Indexer](./media/customize-language-model/upload.png)
* Ses veya video dosyanızı bırakın veya dosyanıza gözatamazsınız.

Size **video kaynak dilini** seçme seçeneği verilir. Açılan listeyi seçin ve listeden oluşturduğunuz bir dil modelini seçin. Dil modelinizin dilini ve parantez içinde verdiğiniz adı söylemelidir. Örnek:

![Video kaynak dilini seçin — Video Indexer bir videoyu yeniden dizinle](./media/customize-language-model/reindex.png)

Sayfanın alt kısmındaki **karşıya yükle** seçeneğini belirleyin ve Yeni videonuzun dil modeliniz kullanılarak dizini oluşturulur.

### <a name="using-a-language-model-to-reindex"></a>Yeniden dizin eklemek için dil modeli kullanma

Modelinizde bir videoyu yeniden eklemek üzere dil modelinizi kullanmak için şu adımları izleyin:

1. [Video Indexer](https://www.videoindexer.ai/) giriş sayfasında oturum açın.
1. Videoda **...** düğmesine tıklayın ve **yeniden dizin**' i seçin.
1. Videonuzu ile yeniden eklemek için **video kaynak dilini** seçme seçeneği verilirler. Açılan listeyi seçin ve listeden oluşturduğunuz bir dil modelini seçin. Dil modelinizin dilini ve parantez içinde verdiğiniz adı söylemelidir.
1. **Yeniden dizin** düğmesini seçin ve videonuzun dil modeliniz kullanılarak yeniden dizin oluşturulur.

## <a name="edit-a-language-model"></a>Dil modeli düzenleme

Bir dil modelini, adını değiştirerek, dosyaya dosya ekleyerek ve dosyaları silerek düzenleyebilirsiniz.

Dil modelinden dosya ekler veya silerseniz, yeşil **eğitme** seçeneğini belirleyerek modeli yeniden eğitmeniz gerekir.

### <a name="rename-the-language-model"></a>Dil modelini yeniden adlandırma

Dil modelinin sağ tarafındaki üç nokta (**...**) düğmesini seçip **Yeniden Adlandır**' a seçerek dil modelinin adını değiştirebilirsiniz.

Yeni adı yazın ve ENTER tuşuna basın.

### <a name="add-files"></a>Dosya Ekle

Bir metin dosyası eklemek için **Dosya Ekle**' yi seçin. Dosya Gezgini açılır.

Adresine gidin ve metin dosyasını seçin. Bir dil modeline birden çok metin dosyası ekleyebilirsiniz.

Ayrıca, dil modelinin sağ tarafındaki üç nokta (**...**) düğmesini seçip **Dosya Ekle**' ye tıklayarak da bir metin dosyası ekleyebilirsiniz.

### <a name="delete-files"></a>Dosyaları silme

Dil modelinden bir dosyayı silmek için, metin dosyasının sağ tarafındaki üç nokta (**...**) düğmesini seçin ve **Sil**' i seçin. Silmenin geri alınamayacağı hakkında yeni bir pencere açılır. Yeni pencerede **Sil** seçeneğini belirleyin.

Bu eylem, dosyayı dil modelinden tamamen kaldırır.

## <a name="delete-a-language-model"></a>Dil modelini silme

Hesabınızdan bir dil modelini silmek için, dil modelinin sağ tarafındaki üç nokta (**...**) düğmesini seçin ve **Sil**' i seçin.

Silmenin geri alınamayacağı hakkında yeni bir pencere açılır. Yeni pencerede **Sil** seçeneğini belirleyin.

Bu eylem dil modelini hesabınızdan tamamen kaldırır. Silinen dil modelini kullanan tüm videolar, videoyu yeniden dizinle aynı dizini tutar. Videoyu yeniden dizinle, videoya yeni bir dil modeli atayabilirsiniz. Aksi takdirde, Video Indexer videoyu yeniden eklemek için varsayılan modelini kullanır.

## <a name="customize-language-models-by-correcting-transcripts"></a>Yazılı betikleri düzelterek dil modellerini özelleştirme

Video Indexer, kullanıcıların videolarının dökümlerinde yaptıkları gerçek düzeltmeleri temel alarak dil modellerinin otomatik özelleştirmesini destekler.

1. Bir döküm dosyasında düzeltme yapmak için, hesap videolarınızdan düzenlemek istediğiniz videoyu açın. **Zaman çizelgesi** sekmesini seçin.

    ![Dil modeli zaman çizelgesi sekmesini özelleştirme — Video Indexer](./media/customize-language-model/timeline.png)

1. Dökümizin dökümünü düzenlemek için kalem simgesini seçin.

    ![Dil modeli düzenleme dökümünü özelleştirme — Video Indexer](./media/customize-language-model/edits.png)

    Video Indexer, videonuzın dökümindeki sizin tarafınızdan düzeltilen tüm satırları yakalar ve bunları otomatik olarak "dökümden döküm düzenlemelerine" adlı bir metin dosyasına ekler. Bu düzenlemeler, bu videoyu dizinlemek için kullanılan belirli dil modelini yeniden eğitmek için kullanılır. 
    
    [Pencere öğesinin](video-indexer-embed-widgets.md) zaman çizelgesinde gerçekleştirilen düzenlemeler da dahil edilmiştir.
    
    Bu videoyu dizin oluştururken bir dil modeli belirtmediyseniz, bu videonun tüm düzenlemeleri, videonun algılanan dili içinde "hesap uyarlamalar" adlı varsayılan bir dil modelinde depolanır.
    
    Aynı satırda birden çok düzenleme yapıldığından, dil modelinin güncelleştirilmesi için yalnızca düzeltilen satırın son sürümü kullanılacaktır.  
    
    > [!NOTE]
    > Özelleştirme için yalnızca metin düzeltmeleri kullanılır. Gerçek kelimeleri içermeyen düzeltmeler (örneğin, noktalama işaretleri veya boşluklar) dahil değildir.
    
1. Yazılı betik düzeltmelerinin, Içerik modeli özelleştirmesi sayfasının dil sekmesinde görünür olduğunu görürsünüz.

   Dil modellerinizin her biri için "döküm dökümü" dosyasına bakmak için, bu dosyayı açmak üzere seçin.

    ![Döküm düzenlemelerine göre — video Indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Sonraki adımlar

[API 'Leri kullanarak dil modelini özelleştirme](customize-language-model-with-api.md)
