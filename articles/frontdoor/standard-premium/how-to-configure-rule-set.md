---
title: 'Azure ön kapı: ön kapı kural kümesini yapılandırma'
description: Bu makale, bir kural kümesinin nasıl yapılandırılacağı hakkında rehberlik sağlar.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 6863c492059ccee152ecf3d03a09e61793576bcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715607"
---
# <a name="configure-a-rule-set-with-azure-front-door-standardpremium-preview"></a>Azure ön kapı standardı/Premium ile bir kural kümesi yapılandırma (Önizleme)

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Bu makalede, Azure portal bir kural kümesinin ve ilk kural kümesinin nasıl oluşturulacağı gösterilmektedir. Daha sonra kural kümesini kural kümesi sayfasından veya Endpoint Manager 'dan bir rota ile ilişkilendirmeyi öğreneceksiniz.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

* Bir kural kümesi yapılandırmadan önce, önce bir Azure ön kapısı Standart/Premium oluşturmanız gerekir. Daha fazla bilgi için bkz. [hızlı başlangıç: Azure ön kapısı Standart/Premium profili oluşturma](create-front-door-portal.md).

## <a name="configure-rule-set-in-azure-portal"></a>Azure portal kural kümesini yapılandırma

1. Ön kapı profilinizde, **Ayarlar**' ın altında bulunan **kural kümesi** ' ni seçin. **Ekle** ' yi seçin ve bir kural kümesi adı verin.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="Kural kümesi giriş sayfasının ekran görüntüsü.":::
    
1. İlk kuralınızı oluşturmak için **Kural Ekle** ' yi seçin. Buna bir kural adı verin. Ardından, kuralı tanımlamak için **Koşul Ekle** veya **Eylem Ekle** ' yi seçin. Tek bir kural için en fazla 10 koşul ve 5 eylem ekleyebilirsiniz. Bu örnekte, URL 'de *contoso* içeren Istekler Için 8coğrafi-ülke * yanıt üst bilgisi eklemek üzere sunucu değişkenini kullanırız.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="Kural kümesi yapılandırma sayfasının ekran görüntüsü.":::
    
    > [!NOTE]
    > * Bir kuraldan koşul veya eylem silmek için, belirli bir koşulun veya eylemin sağ tarafında çöp kutusu ' nu kullanın.
    > * Tüm gelen trafiğe uygulanan bir kural oluşturmak için herhangi bir koşul belirtmeyin.
    > * Belirli bir kural karşılanıyorsa kalan kuralların değerlendirilmesini durdurmak için, **kalan kuralı değerlendirmeyi durdur**' u işaretleyin. Bu seçenek işaretliyse ve kural kümesindeki tüm kalan kurallar, eşleşen koşullar karşılandığı takdirde yürütülmeyecektir.  

1. Kuralları daha yüksek veya daha düşük bir düzeye taşımak için ok düğmelerini kullanarak kural kümesi içindeki kuralların önceliğini belirleyebilirsiniz. Liste artan sırada olduğundan en önemli kural önce listelenir.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="Kural kümesi önceliğinin ekran görüntüsü." lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. Bir veya daha fazla kural oluşturduktan sonra, kural kümesi oluşturmayı tamamladıktan sonra **Kaydet** ' i seçin.

1. Şimdi kural kümesini bir rota ile ilişkilendirin, böylece etkili olur. Kural kümesi sayfası ile ayarlanan kuralları ilişkilendirebilir veya ilişkilendirmeyi oluşturmak için Endpoint Manager ' a gidebilirsiniz.
 
    **Kural kümesi sayfası**: 
    
    1. İlişkilendirilecek kural kümesini seçin.
    
    1. *İlişkilendirilmemiş* bağlantıyı seçin.
     

    1. Ardından, **bir yolu ilişkilendir** sayfasında, kural kümesiyle ilişkilendirmek istediğiniz uç noktayı ve yolu seçin. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="Yol Oluştur sayfasının ekran görüntüsü.":::    
        
    1. Seçili yol altında birden çok kural kümesi varsa, kural kümesi emirlerini değiştirmek için *İleri ' yi* seçin. Kural kümesi yukarıdan aşağı doğru yürütülür. Kural kümesini seçerek siparişleri değiştirebilir ve yukarı veya aşağı taşıyabilirsiniz. Ardından *ilişkilendir*' i seçin.
    
        > [!Note]
        > Bu sayfada yalnızca bir kural kümesini tek bir rota ile ilişkilendirebilirsiniz. Bir kural kümesini birden çok rotasıyla ilişkilendirmek için lütfen Endpoint Manager 'ı kullanın.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="Kural kümesi siparişlerinin ekran görüntüsü.":::
    
    1. Kural kümesi artık bir rota ile ilişkili. Yanıt başlığına bakabilir ve coğrafi ülkenin eklendiğini görebilirsiniz.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="Bir rota ile ilişkili kuralın ekran görüntüsü.":::

   **Uç nokta Yöneticisi**: 
    
    1. Endpoint Manager ' a gidin, kural kümesiyle ilişkilendirmek istediğiniz uç noktayı seçin.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="Endpoint Manager 'da uç nokta seçme ekran görüntüsü." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. *Uç noktayı Düzenle*' yi seçin.  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="Uç nokta yöneticisinde düzenleme uç noktası seçme ekran görüntüsü." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. Rotayı seçin. 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="Rota seçme ekran görüntüsü.":::
    
    1. *Yolu Güncelleştir* sayfasında, *kurallar*' da, açılan listeden rota Ile ilişkilendirmek istediğiniz kural kümelerini seçin. Daha sonra kural kümesini yukarı ve aşağı taşıyarak siparişleri değiştirebilirsiniz. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="Bir yol güncelleştirme sayfasının ekran görüntüsü.":::
    
    1. Ardından, ilişkilendirmeyi son vermek için *Güncelleştir* veya *Ekle* ' yi seçin.

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Azure ön kapı profilinizden bir kural kümesini silme

Yukarıdaki adımlarda, bir kural kümesini sizin ve yönlendirmenize göre yapılandırdınız. Kural kümesini ön kapıla ilişkili olarak artık istemiyorsanız, aşağıdaki adımları tamamlayarak kural kümesini kaldırabilirsiniz:

1. Kural kümesinin tüm ilişkili rotalardan ilişkisini kaldırmak için **Ayarlar** ' ın altındaki **kural kümesi sayfasına** gidin.

1. Rotayı genişletin, üç noktayı seçin. Ardından *rotayı Düzenle*' yi seçin.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="Kural kümesinde genişletilen yolun ekran görüntüsü.":::

1. Yol sayfasında kurallar bölümüne gidin, kural kümesini seçin ve *Sil* düğmesini seçin. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="Bir kural kümesini silmek için yolu Güncelleştir sayfasının ekran görüntüsü." lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. *Güncelleştirme* ' yi seçin ve kural kümesi rotadaki ilişkiyi kaldırılır.

1. Yolların durumu *ilişkilendirilmemiş* olarak gösterilene kadar, bu kural kümesiyle ilişkili diğer yolların ilişkisini kaldırmak için 2-5 arasındaki adımları yineleyin.

1. *İlişkilendirilmemiş* olan kural kümesi için, sağdaki üç noktaya tıklayıp *Sil*' i seçerek kural kümesini silebilirsiniz. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="Bir kural kümesinin nasıl silineceğini gösteren ekran görüntüsü.":::

1. Kural kümesi artık silinir.

## <a name="next-steps"></a>Sonraki adımlar

[Kuralların ayarlandığı güvenlik üstbilgilerini](how-to-add-security-headers.md)nasıl ekleyeceğinizi öğrenin.
