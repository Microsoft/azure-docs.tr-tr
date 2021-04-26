---
title: Azure geçişi ile Azure 'a geçiş için GCP sanal makine örneklerini değerlendirin
description: Azure geçişi kullanılarak Azure 'a geçiş için GCP sanal makine örneklerinin nasıl değerlendirileneceğini açıklar.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 6a59400ca0d8f2e4ced899166fe6e67b5ac1d2d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780693"
---
# <a name="tutorial-assess-google-cloud-platform-gcp-vm-instances-for-migration-to-azure"></a>Öğretici: Azure 'a geçiş için Google Cloud Platform (GCP) sanal makine örneklerini değerlendir

Azure 'a geçiş sürecinizin bir parçası olarak, bulut hazırlığını belirlemek, riskleri belirlemek ve maliyetleri ve karmaşıklığı tahmin etmek için şirket içi iş yüklerinizi değerlendirmenizi sağlar.

Bu makalede, Azure geçişi: bulma ve değerlendirme aracı kullanılarak Azure 'a geçiş için Google Cloud Platform (GCP) sanal makine örneklerinin nasıl değerlendirireceğiniz gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
- Sunucu meta verileri ve yapılandırma bilgilerine göre bir değerlendirme çalıştırın.
- Performans verilerine göre bir değerlendirme çalıştırın.

> [!NOTE]
> Öğreticiler senaryo denemek için en hızlı yolu gösterir ve mümkün olan yerlerde varsayılan seçenekleri kullanır. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


## <a name="prerequisites"></a>Önkoşullar

- Bu öğreticideki adımları izlemeden önce, Şirket [içi envanterinizi saptamak](tutorial-discover-gcp.md)için bu serideki ilk öğreticiyi tamamlayın. 


## <a name="decide-which-assessment-to-run"></a>Hangi değerlendirmenin çalıştırılacağını belirleyin

Şirket içinde olduğu gibi toplanan veya performans verilerine bağlı olarak, sunucu yapılandırma verilerine/meta verilere dayalı olarak boyutlandırma ölçütlerini kullanarak bir değerlendirme çalıştırmak istediğinize karar verin.

**Değerlendirme** | **Ayrıntılar** | **Öneri**
--- | --- | ---
**Şirket içinde olduğu gibi** | Sunucu yapılandırma verilerine/meta verilere göre değerlendirin.  | Önerilen Azure VM boyutu, şirket içi VM boyutunu temel alır.<br/><br> Önerilen Azure disk türü, değerlendirmede depolama türü ayarında neleri seçdiklerinizi temel alır.
**Performans tabanlı** | Toplanan performans verilerine göre değerlendirin. | Önerilen Azure VM boyutu, CPU ve bellek kullanımı verilerine göre belirlenir.<br/><br/> Önerilen disk türü, şirket içi disklerin ıOPS ve aktarım hızını temel alır.

## <a name="run-an-assessment"></a>Değerlendirme çalıştırma

Bir değerlendirmeyi aşağıdaki gibi çalıştırın:

1. **Windows, Linux ve SQL Server**> **genel bakış** sayfasında **sunucuları değerlendir ve geçir**' e tıklayın.

   ![Sunucuları değerlendir ve geçir düğmesinin konumu](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. **Azure geçişi: bulma ve değerlendirme** bölümünde **değerlendir**' e tıklayın.

    ![Değerlendirme düğmesinin konumu](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. **Sunucu**  >  **değerlendirmesi türünü** değerlendir bölümünde **Azure VM**' yi seçin.
4. **Bulma kaynağında**:

    - Gereci kullanarak sunucular keşfetiyorsanız, **Azure geçişi gereci ' ndan bulunan sunucuları** seçin.
    - İçeri aktarılan bir CSV dosyası kullanarak sunucular keşfettiği takdirde, **Içeri aktarılan sunucular**' ı seçin. 
    
1. Değerlendirme özelliklerini gözden geçirmek için **Düzenle** ' ye tıklayın.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="Değerlendirme özelliklerini gözden geçirmek için Düzenle düğmesinin konumu":::

1. **Değerlendirme özellikleri**  >  **hedef özellikleri**:
    - **Hedef konum**' da, geçirmek istediğiniz Azure bölgesini belirtin.
        - Boyut ve maliyet önerileri belirttiğiniz konuma göre hesaplanır. Hedef konumu varsayılan olarak değiştirdiğinizde, **ayrılmış örnekler** ve **VM Serisi** belirtmeniz istenir.
        - Azure Kamu 'da, [Bu bölgelerdeki](migrate-support-matrix.md#supported-geographies-azure-government) değerlendirmeleri hedefleyebilirsiniz
    - **Depolama türü**' nde,
        - Değerlendirmede performans tabanlı verileri kullanmak istiyorsanız, disk ıOPS ve aktarım hızı temelinde depolama türü önermek için Azure geçişi için **Otomatik** ' i seçin.
        - Alternatif olarak, geçiş yaparken VM için kullanmak istediğiniz depolama türünü seçin.
    - **Ayrılmış örnekler**' de, GEÇIŞ yaparken VM için ayrılmış örnekler kullanmak isteyip istemediğinizi belirtin.
        - Ayrılmış bir örnek kullanmayı seçerseniz, '**indirim (%)** veya **VM çalışma süresi** belirtemezsiniz. 
        - [Daha fazla bilgi edinin](https://aka.ms/azurereservedinstances).
 1. **VM boyutu**:
     - **Boyutlandırma ölçütündeki** değerlendirmeyi sunucu yapılandırma verileri/meta verileri veya performans tabanlı veriler üzerinde temel almak istiyorsanız seçin. Performans verileri kullanıyorsanız:
        - **Performans geçmişi**' nde, değerlendirmeye dayandırmak istediğiniz veri süresini belirtin
        - **Yüzdelik kullanım**' de, performans örneği için kullanmak istediğiniz yüzdebirlik değerini belirtin. 
    - **VM Serisi**' nde, göz önünde bulundurmanız ISTEDIĞINIZ Azure VM serisini belirtin.
        - Performans tabanlı değerlendirme kullanıyorsanız, Azure geçişi sizin için bir değer önerir.
        - Gerektiğinde ince ayar ayarları. Örneğin, Azure 'da bir serisi VM gerektiren bir üretim ortamınız yoksa, seri listesinden bir serisi dışarıda bırakabilirsiniz.
    - **Rahatlık faktörüyle**, değerlendirme sırasında kullanmak istediğiniz arabelleği belirtin. Bu hesaplar, dönemsel kullanım, kısa performans geçmişi ve gelecekteki kullanımlarda olası artışlar gibi sorunlara yöneliktir. Örneğin, iki bir rahatlık faktörü kullanıyorsanız:
    
        **Bileşen** | **Etkin kullanım** | **Rakip çarpanı ekleme (2,0)**
        --- | --- | ---
        Çekirdekler | 2  | 4
        Bellek | 8 GB | 16 GB
   
1. **Fiyatlandırma**:
    - **Teklif** bölümünde, kaydolduysanız [Azure teklifini](https://azure.microsoft.com/support/legal/offer-details/) belirtin. Değerlendirme, bu teklifin maliyetini tahmin eder.
    - **Para birimi**' nde, hesabınız için faturalandırma para birimini seçin.
    - **İndirim (%)** bölümünde, Azure teklifinin üzerine aldığınız aboneliğe özgü indirimleri ekleyin. Varsayılan ayar, %0’dır.
    - **VM çalışma süresi**' nde, VM 'lerin çalışacağı süreyi (gün başına aylık gün/saat) belirtin.
        - Bu, sürekli olarak çalışmayan Azure VM 'Leri için yararlıdır.
        - Maliyet tahminleri belirtilen süreye göre belirlenir.
        - Varsayılan değer ayda 31 gün/günde 24 saattir.
    - **EA aboneliğinde**, maliyet tahmini için bir kurumsal anlaşma (EA) abonelik indirimi hesaba alıp almayacağını belirtin. 
    - **Azure hibrit avantajı**' de, zaten bir Windows Server lisansınız olup olmadığını belirtin. Bunu yaptıysanız ve Windows Server aboneliklerinin etkin yazılım güvencesi kapsamında yer alındıklarında, lisansları Azure 'a aktardığınızda [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-use-benefit/) uygulayabilirsiniz.

1. Değişiklik yaparsanız **Kaydet** ' e tıklayın.

    ![Değerlendirme özellikleri](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. **Sunucuları değerlendir** > **İleri**' ye tıklayın.

1. Değerlendirme **adını değerlendirmek için sunucuları seçin**  >   > değerlendirme için bir ad belirtin. 

1. > **Grup Seç veya oluştur** bölümünde **Yeni oluştur** ' u seçin ve bir grup adı belirtin. 
    

1. Gereç ' ı seçin ve gruba eklemek istediğiniz VM 'Leri seçin. Ardından **İleri**'ye tıklayın.


1. **İnceleme ve değerlendirme oluştur**' da, değerlendirme ayrıntılarını gözden geçirin ve grubu oluşturmak ve değerlendirmeyi çalıştırmak Için değerlendirme **Oluştur** ' a tıklayın.

1. Değerlendirme oluşturulduktan sonra **sunucuları**  >  **Azure geçişi: bulma ve değerlendirme**  >  **değerlendirmelerinde** görüntüleyin.

1. Excel dosyası olarak indirmek için **Değerlendirmeyi dışarı aktar**’a tıklayın.
    > [!NOTE]
    > Performans tabanlı değerlendirmeler için, değerlendirme oluşturmadan önce bulmayı başlattıktan sonra en az bir gün beklemeniz önerilir. Bu, daha yüksek güvenilirliğe sahip performans verilerinin toplanması için zaman sağlar. İdeal olarak, bulmayı başlattıktan sonra, belirttiğiniz performans süresini (gün/hafta/ay) yüksek güvenilirlikli bir derecelendirme için bekleyin.

## <a name="review-an-assessment"></a>Değerlendirmeyi gözden geçirme

Bir değerlendirme şunları açıklar:

- **Azure hazırlığı**: VM 'lerin Azure 'a geçiş için uygun olup olmadığı.
- **Aylık maliyet tahmini**: VM 'leri Azure 'da çalıştırmaya yönelik tahmini aylık işlem ve depolama maliyetleri.
- **Aylık depolama maliyeti tahmini**: Geçiş sonrasında disk depolaması için tahmini maliyetler.

Bir değerlendirmeyi görüntülemek için:

1. **Windows, Linux ve SQL Server**  >  **Azure geçişi: bulma ve değerlendirme**' da, **değerlendirmeler**' ın yanındaki sayıya tıklayın.
2. **Değerlendirmeler** sayfasında açmak istediğiniz değerlendirmeye tıklayın. Örnek olarak (yalnızca Örneğin, tahminler ve maliyetler): 

    ![Değerlendirme özeti](./media/tutorial-assess-gcp/assessment-summary.png)

3. Değerlendirme özetini gözden geçirin. Ayrıca değerlendirme özelliklerini düzenleyebilir veya değerlendirmeyi yeniden hesaplayabilirsiniz.
 
 
### <a name="review-readiness"></a>İnceleme hazırlığı

1. **Azure hazırlığı**' ne tıklayın.
2. **Azure hazırlık** bölümünde VM durumunu gözden geçirin:
    - **Azure Için hazırlanma**: Azure geçişi, değerlendirmede VM 'ler IÇIN bir VM boyutu ve maliyet tahminleri önermesinde kullanılır.
    - **Koşullara hazırlanma**: sorunları ve önerilen düzeltmeyi gösterir.
    - **Azure için hazırlanma**: sorunları ve önerilen düzeltmeyi gösterir.
    - **Hazır olma durumu bilinmiyor**: veri kullanılabilirliği sorunları nedeniyle Azure geçişi hazırlığı değerlendiremediği zaman kullanılır.

3. **Azure için hazır olma** durumlarından birini seçin. VM hazırlığı ayrıntıları ' nı görüntüleyebilirsiniz. Ayrıca, işlem, depolama ve ağ ayarları dahil olmak üzere VM ayrıntılarını görmek için ayrıntıya gidebilirsiniz.

### <a name="review-cost-estimates"></a>Tahmini maliyetleri gözden geçirme

Değerlendirme özeti, Azure 'da çalışan VM 'lerin tahmini işlem ve depolama maliyetini gösterir. 

1. Aylık toplam maliyetleri gözden geçirin. Ücretler, değerlendirilen gruptaki tüm VM 'Ler için toplanır.

    - Maliyet tahminleri, bir sunucu, diskleri ve özellikleri için boyut önerilerini temel alır.
    - İşlem ve depolama için aylık tahmini maliyetler gösterilir.
    - Maliyet tahmini, Azure VM 'lerinde şirket içi VM 'Leri çalıştırmaya yöneliktir. Tahmin PaaS veya SaaS maliyetlerini göz önünde bulundurmaz.

2. Aylık depolama maliyetlerini gözden geçirin. Görünüm, değerlendirilen grup için toplanan depolama maliyetlerini gösterir ve farklı türlerdeki depolama disklerinin üzerine bölünür. 
3. Belirli VM 'Ler için maliyet ayrıntılarını görmek için ayrıntıya gidebilirsiniz.

### <a name="review-confidence-rating"></a>Güvenilirlik derecelendirmesini gözden geçirme

Azure geçişi, performans tabanlı değerlendirmelere güvenle bir derecelendirme atar. Derecelendirme bir yıldız (en düşük) ile beş yıldız (en yüksek) arasında.

![Güvenilirlik derecelendirmesi](./media/tutorial-assess-gcp/confidence-rating.png)

Güvenilirlik derecelendirmesi, değerlendirmede boyut önerilerinin güvenilirliğini tahmin etmenize yardımcı olur. Derecelendirme, değerlendirmeyi hesaplamak için gereken veri noktalarının kullanılabilirliğine bağlıdır.

> [!NOTE]
> Bir CSV dosyasını temel alan bir değerlendirme oluşturursanız güven derecelendirmeleri atanmaz.


Güven derecelendirmeleri aşağıdaki gibidir.

**Veri noktası kullanılabilirliği** | **Güvenilirlik derecelendirmesi**
--- | ---
%0-%20 | 1 yıldız
%21-%40 | 2 yıldız
%41-%60 | 3 yıldız
%61-%80 | 4 yıldız
%81-%100 | 5 yıldız

Güvenilirlikli derecelendirmeler hakkında [daha fazla bilgi edinin](concepts-assessment-calculation.md#confidence-ratings-performance-based) .

## <a name="next-steps"></a>Sonraki adımlar

- [Bağımlılık eşlemesini](concepts-dependency-visualization.md)kullanarak sunucu bağımlılıklarını bulun.
- [Aracı tabanlı](how-to-create-group-machine-dependencies.md) bağımlılık eşlemesini ayarlayın.
