---
title: Dil modelini özelleştirmek için Video Indexer Web sitesini kullanma-Azure
titleSuffix: Azure Media Services
description: Bu makalede, Video Indexer Web sitesiyle bir dil modelinin nasıl özelleştirileceği gösterilmektedir.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 5d92cb02a0ac52b317cf9d4b6c8e0278f9291910
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838453"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Video Indexer Web sitesiyle bir dil modeli özelleştirme

Video Indexer, altyapı özelliğini benzetirecek olan etki alanındaki metin olan uyarlama metnini karşıya yükleyerek konuşma tanımayı özelleştirmek için özel dil modelleri oluşturmanızı sağlar. Modelinize eğtikten sonra, uyarlama metninde görüntülenen yeni kelimeler tanınacaktır. 

Özel dil modelleriyle ilgili ayrıntılı genel bakış ve en iyi uygulamalar için bkz. [video Indexer bir dil modelini özelleştirme](customize-language-model-overview.md).

Bu konuda açıklandığı gibi, hesabınızda özel dil modelleri oluşturmak ve düzenlemek için Video Indexer Web sitesini kullanabilirsiniz. API 'yi [API 'leri kullanarak dil modelini özelleştirme](customize-language-model-with-api.md)bölümünde açıklandığı gibi de kullanabilirsiniz.

## <a name="create-a-language-model"></a>Dil modeli oluşturma

1. [Video Indexer](https://www.videoindexer.ai/) web sitesine gidip oturum açın.
2. Hesabınızdaki bir modeli özelleştirmek için sayfanın sağ üst köşesinde bulunan **içerik modeli özelleştirmesi** düğmesine tıklayın.

   ![İçerik modelini Özelleştir](./media/content-model-customization/content-model-customization.png)

3. **Dil** sekmesini seçin.

    Desteklenen dillerin listesini görürsünüz. 

    ![Dil modelini özelleştirme](./media/customize-language-model/customize-language-model.png)

4. İstediğiniz dilin altında **Model Ekle**' ye tıklayın.
5. Dil modelinin adını yazın ve ENTER tuşuna basın.

    Bu, modeli oluşturur ve modele metin dosyaları yükleme seçeneği sunar.
6. Bir metin dosyası eklemek için **Dosya Ekle**' ye tıklayın. Bu, dosya Gezgini 'ni açar.

7. Adresine gidin ve metin dosyasını seçin. Bir dil modeline birden çok metin dosyası ekleyebilirsiniz.

    Ayrıca, dil modelinin sağ tarafındaki **...** düğmesine tıklayıp **Dosya Ekle**' yi seçerek bir metin dosyası ekleyebilirsiniz.
8. Metin dosyalarını karşıya yüklemeyi tamamladıktan sonra yeşil **tren** seçeneğine tıklayın.

    ![Dil modelini eğitme](./media/customize-language-model/train-model.png)

Eğitim işlemi birkaç dakika sürebilir. Eğitim yapıldıktan sonra, modelin yanında **eğitilen** ' u görürsünüz. Dosyayı modelden önizleyebilir, indirebilir ve silebilirsiniz.

![Eğitilen dil modeli](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Yeni bir videoda dil modeli kullanma

Dil modelinizi yeni bir videoda kullanmak için aşağıdakilerden birini yapın:

* Sayfanın üst kısmındaki **karşıya yükle** düğmesine tıklayın 

    ![Karşıya Yükle](./media/customize-language-model/upload.png)
* Ses veya video dosyanızı daire içine bırakın veya dosyanıza gözatamazsınız

    ![Karşıya Yükle](./media/customize-language-model/upload2.png)

Bu, size **video kaynak dilini**seçme seçeneği sunar. Açılan listeye tıklayın ve listeden oluşturduğunuz bir dil modelini seçin. Dil modelinizin dilini ve parantez içinde verdiğiniz adı söylemelidir.

Sayfanın alt kısmındaki **karşıya yükle** seçeneğine tıklayın ve Yeni videonuzun dil modeliniz kullanılarak dizini oluşturulur.

### <a name="using-a-language-model-to-reindex"></a>Yeniden dizin eklemek için dil modeli kullanma

Bir videoyu koleksiyonunuzda yeniden eklemek için dil modelinizi kullanmak üzere [video Indexer](https://www.videoindexer.ai/) giriş sayfasında **Hesap videolarınıza** gidin ve yeniden adlandırmak istediğiniz videonun adının üzerine gelin.

Videonuzu düzenleme, videoyu silme ve videonuzu yeniden dizin seçeneklerini görürsünüz. Videonuzu yeniden dizinle ilgili seçeneğe tıklayın.

![Yeniden dizin oluşturma](./media/customize-language-model/reindex1.png)

Bu, videonuzu yeniden eklemek için **video kaynak dilini** seçme seçeneği sunar. Açılan listeye tıklayın ve listeden oluşturduğunuz bir dil modelini seçin. Dil modelinizin dilini ve parantez içinde verdiğiniz adı söylemelidir.

![Yeniden dizin oluşturma](./media/customize-language-model/reindex.png)

**Yeniden dizin** düğmesine tıklayın ve videonuzun dili modeli kullanılarak yeniden dizin oluşturulur.

## <a name="edit-a-language-model"></a>Dil modeli düzenleme

Bir dil modelini, adını değiştirerek, dosyaya dosya ekleyerek ve dosyaları silerek düzenleyebilirsiniz.

Dil modelinden dosya ekler veya silerseniz, yeşil **eğitme** seçeneğine tıklayarak modeli yeniden eğmeniz gerekecektir.

### <a name="rename-the-language-model"></a>Dil modelini yeniden adlandırma

Dil modelinin sağ tarafındaki **...** öğesine tıklayıp **Yeniden Adlandır**' ı seçerek dil modelinin adını değiştirebilirsiniz. 

Yeni adı yazın ve ENTER tuşuna basın.

### <a name="add-files"></a>Dosya Ekle

Bir metin dosyası eklemek için **Dosya Ekle**' ye tıklayın. Bu, dosya Gezgini 'ni açar.

Adresine gidin ve metin dosyasını seçin. Bir dil modeline birden çok metin dosyası ekleyebilirsiniz.

Ayrıca, dil modelinin sağ tarafındaki **...** düğmesine tıklayıp **Dosya Ekle**' yi seçerek bir metin dosyası ekleyebilirsiniz.

### <a name="delete-files"></a>Dosyaları silme

Dil modelinden bir dosyayı silmek için, metin dosyasının sağ tarafındaki **...** düğmesine tıklayın ve **Sil**' i seçin. Bu, silmenin geri alınamayacağı yeni bir pencere getirir. Yeni penceredeki **Sil** seçeneğine tıklayın.

Bu eylem, dosyayı dil modelinden tamamen kaldırır.

## <a name="delete-a-language-model"></a>Dil modelini silme

Hesabınızdan bir dil modelini silmek için, dil modelinin sağ tarafındaki **...** düğmesine tıklayın ve **Sil**' i seçin.

Bu, silmenin geri alınamayacağı yeni bir pencere getirir. Yeni penceredeki **Sil** seçeneğine tıklayın.

Bu eylem dil modelini hesabınızdan tamamen kaldırır. Silinen dil modelini kullanan tüm videolar, videoyu yeniden dizinlene kadar aynı dizini tutacaktır. Videoyu yeniden dizinleyebilirsiniz, videoya yeni bir dil modeli atayabilirsiniz. Aksi takdirde, Video Indexer videoyu yeniden dizin oluşturacak varsayılan modelini kullanır. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Yazılı betikleri düzelterek dil modellerini özelleştirme

Video Indexer, kullanıcıların videolarının dökümlerinde yaptıkları gerçek düzeltmeleri temel alarak dil modellerinin otomatik özelleştirmesini destekler.

1. Bir döküm dosyasında düzeltme yapmak için, hesap videolarınızdan düzenlemek istediğiniz videoyu açın. **Zaman çizelgesi** sekmesini seçin.

    ![Dil modelini özelleştirme](./media/customize-language-model/timeline.png)
1. Döküm dosyasının dökümünü düzenlemek için kalem simgesine tıklayın. 

    ![Dil modelini özelleştirme](./media/customize-language-model/edits.png)

    Video Indexer, videonuzın dökümindeki sizin tarafınızdan düzeltilen tüm satırları yakalar ve bunları otomatik olarak "dökümden döküm düzenlemelerine" adlı bir metin dosyasına ekler. Bu düzenlemeler, bu videoyu dizinlemek için kullanılan belirli dil modelini yeniden eğitelemek için kullanılır. 
    
    Bu videoyu dizin oluştururken bir dil modeli belirtmediyseniz, bu videonun tüm düzenlemeleri, videonun algılanan dilinde hesap uyarlamalar adlı varsayılan bir dil modelinde saklanır. 
    
    Aynı satırda birden çok düzenleme yapıldığından, dil modelinin güncelleştirilmesi için yalnızca düzeltilen satırın son sürümü kullanılacaktır.  
    
    > [!NOTE]
    > Özelleştirme için yalnızca metin düzeltmeleri kullanılır. Bu, gerçek sözcükleri içermeyen düzeltmelerin (örneğin, noktalama işaretleri veya boşluklar) dahil edilmeyeceği anlamına gelir. 
    
1. Yazılı betik düzeltmelerinin, Içerik modeli özelleştirmesi sayfasının dil sekmesinde görünür.

    ![Dil modelini özelleştirme](./media/customize-language-model/customize.png)

   Dil modellerinizin her biri için "dökümden betik düzenlemeleri" dosyasına bakmak için üzerine tıklayın. 

    ![Döküm düzenlemelerinin](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Sonraki adımlar

[API 'Leri kullanarak dil modelini özelleştirme](customize-language-model-with-api.md)
