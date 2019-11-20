---
title: Azure maliyet yönetimi 'nde ortak maliyet analizi kullanımları
description: Bu makalede, Azure maliyet yönetimi 'nde ortak maliyet analizi görevlerinin sonuçlarını nasıl alabileceğiniz açıklanmaktadır.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/16/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: ''
ms.openlocfilehash: cc133a75506dcbe552d380e2a6de219a47aca3d8
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600195"
---
# <a name="common-cost-analysis-uses"></a>Ortak maliyet analizi kullanımları

Azure maliyet yönetimi kullanıcıları genellikle pek çok sorduğu soruların yanıtlarını ister. Bu makalede, maliyet yönetiminde ortak maliyet analizi görevlerinin sonuçları elde edersiniz.

## <a name="view-cost-breakdown-by-azure-service"></a>Azure hizmetine göre maliyet dökümünü görüntüleme

Azure hizmetine göre maliyetleri görüntülemek, altyapınızın en fazla maliyetli parçalarını daha iyi anlamanıza yardımcı olabilir. Örneğin, VM işlem maliyetleri küçük olabilir. Ancak, VM 'lerden alınan bilgi miktarı nedeniyle önemli ağ maliyetleri de tahakkuk edebilir. Azure hizmetlerinizin birincil maliyet sürücülerini anlamak, gerektiğinde hizmet kullanımını ayarlayabilmeniz için gereklidir.

1. Azure portal kapsamınız için maliyet analizi ' ne gidin. Örneğin: maliyet **yönetimi + faturalandırma**  >  maliyet**yönetimi**  > **Maliyet Analizi**.
1. **Hizmete göre maliyet** ' i ve ardından **hizmet katmanına**göre Gruplandır ' ı seçin.
1. Görünümü **tablo**olarak değiştirin.

![Azure hizmetine göre maliyet dökümü](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Azure kaynağına göre maliyet dökümünü görüntüleme

Hizmetleriniz Azure kaynaklarıyla oluşturulmuştur. Kaynaklara göre maliyetleri gözden geçirmek, birincil maliyetlerinizi hızlı bir şekilde tanımanıza yardımcı olabilir. Bir hizmette çok pahalı kaynaklar varsa, maliyetlerinizi azaltmak için değişiklikler yapmayı deneyin.

1. Azure portal kapsamınız için maliyet analizi ' ne gidin. Örneğin: maliyet **yönetimi + faturalandırma**  >  maliyet**yönetimi**  > **Maliyet Analizi**.
1. **Kaynağa göre maliyet**' i seçin.
1. Görünümü **tablo**olarak değiştirin.

![Azure kaynağına göre maliyet dökümünü görüntüleme](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Seçilen boyutlara göre maliyet dökümünü görüntüle

Boyutlar, maliyetlerinizi ücretlendirmekte gösterilen çeşitli meta veri değerlerine göre düzenlemenizi sağlar. Örneğin, maliyetlerinizi konuma göre gruplandırabilirsiniz.

1. Azure portal kapsamınız için maliyet analizi ' ne gidin. Örneğin: maliyet **yönetimi + faturalandırma**  >  maliyet**yönetimi**  > **Maliyet Analizi**.
1. **Gruplandırma ölçütü** filtresi ' ni seçin.  
    öğe grubuna göre bir grup ![Select ](./media/cost-analysis-common-uses/group-by.png)
1. İsterseniz, daha sonra kullanmak üzere görünümü kaydedersiniz.
1. Daha ayrıntılı verileri görüntülemek için grafiğin altındaki bir pasta grafiğine tıklayın.  
    ![Seçilen boyutlara göre maliyet dökümünü görüntüle](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Günde veya aya göre maliyetleri görüntüleme

Günlük ve aylık maliyetlere bakarak, maliyetlerinizin daha yüksek olduğu hafta veya yılda bir saat olup olmadığını daha iyi anlamanıza yardımcı olabilir. Bir tatil döneminde daha fazla müşteri trafiğine sahipseniz, bu, Azure maliyetlerinizin karşılık gelen bir artışla sonuçlansın mı? Pazartesi 'tan daha pahalı bir gün mı?

1. Azure portal kapsamınız için maliyet analizi ' ne gidin. Örneğin: maliyet **yönetimi + faturalandırma**  >  maliyet**yönetimi**  > **Maliyet Analizi**.
1. **Ayrıntı düzeyini** **aylık** veya **günlük**olarak ayarlayın.

![Günlük maliyetlerini görüntüleme](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Belirli bir etiketin maliyetlerini görüntüleme

Birçok Azure kullanıcısı, ücretleri daha iyi kategorilere ayırmak için maliyet merkezi veya geliştirme ortamı (üretim ve test) gibi kaynaklarına ait Etiketler uygular. Etiketler, maliyet analizinde bir boyut olarak görünür. Özel etiketleme kategorisimleriniz hakkında öngörüler elde etmek için boyutu kullanabilirsiniz.

1. Azure portal kapsamınız için maliyet analizi ' ne gidin. Örneğin: maliyet **yönetimi + faturalandırma**  >  maliyet**yönetimi**  > **Maliyet Analizi**.
1. Etiketinize **göre Gruplandır** ' ı seçin.

![Belirli bir etiketin maliyetlerini görüntüleme](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Kullanım ayrıntılarınızı indirin

Kullanım ayrıntıları rapor dosyanız, CSV biçiminde, bir faturaya yönelik olarak tahakkuk eden tüm ücretler için bir döküm sağlar. Raporunuzu, faturanızı karşılaştırmak ve daha iyi anlayabilmek için kullanabilirsiniz. Faturanızda faturalandırılan her bir ücret, kullanım raporundaki, kesilen ücretlere karşılık gelir.

1. Azure portal, bir faturalandırma hesabı veya aboneliği için **kullanım ve ücretler** sekmesine gidin. Örneğin: **maliyet yönetimi + faturalandırma**  > **faturalandırma**  > **kullanım + ücretlidir**.
1. İndirilecek satır öğesini seçin ve ardından indir simgesine tıklayın.  
    ![Kullanım ve ücretleri indirin](./media/cost-analysis-common-uses/download1.png)
1.  İndirilecek kullanım dosyasını seçin.  
    ![İndirilecek bir kullanım dosyası seçin](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Aylık EA maliyet dökümünü görüntüle

EA kaydınız, kuruluşunuzun tamamına yönelik maliyetlerinizi ücreteder. Maliyetlerin zaman içinde nasıl tahakkuk ve faturalandırılır olduğunu anlamak, maliyetlerin yönetilip yönetilmemesini sağlamak için uygun paydaşlara sahip olmanıza yardımcı olur.

1. Azure portal **maliyet yönetimi + faturalandırma**  > **Genel Bakış ' a**gidin.
1. Geçerli ay için **döküm** ' ye tıklayın ve parasal taahhüdünüzü aşağı yazmayı görüntüleyin.  
    ![EA maliyetlere genel bakış-döküm Özeti ](./media/cost-analysis-common-uses/breakdown1.png)
1.  **Kullanım ve ücretler** sekmesine tıklayın ve seçilen TimeSpan 'de önceki ayın dökümünü görüntüleyin.  
    ![Usage ve ücretler sekmesi ](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Kayda göre kayıt aylık maliyetini görüntüleyin

Belirli bir döneme ait maliyet eğilimlerini ve Faturalanan tutarları anlamak için kaydınızın aylık maliyetlerinin grafik görünümünü kullanın.

1. Azure portal kapsamınız için maliyet analizi ' ne gidin. Örneğin: maliyet **yönetimi + faturalandırma**  >  maliyet**yönetimi**  > **Maliyet Analizi**.
1. Kaydınız ' ni seçin ve kayıt terimini ayarlayın.
1. Ayrıntı düzeyini aylık olarak ayarlayın ve görünümü **sütun (yığılmış)** olarak ayarlayın.

Daha ayrıntılı bir analize göre gruplandırabilirsiniz ve verilerinize filtre uygulayabilirsiniz.

![Terime göre aylık kayıt maliyeti](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>EA kaydı birikmiş maliyetlerini görüntüle

Belirli bir dönem için kuruluşunuzun genel harcamalarını anlamak için zaman içinde net birikmiş ücretleri görüntüleyin.

1. Azure portal kapsamınız için maliyet analizi ' ne gidin. Örneğin: maliyet **yönetimi + faturalandırma**  >  maliyet**yönetimi**  > **Maliyet Analizi**.
1. Kaydınız ' ni seçin ve ardından geçerli birikmiş maliyetlerinizi görüntüleyin.

![Kayıt birikmiş maliyetleri](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Sonraki adımlar
- Maliyet yönetimi için ilk hızlı başlangıcı Henüz tamamlamadıysanız, [maliyetleri çözümlemeye başlamak](quick-acm-cost-analysis.md)için bunu okuyun.
- [Maliyet yönetimi belgelerini](index.yml)okuyun.
