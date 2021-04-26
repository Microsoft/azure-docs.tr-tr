---
title: Azure geçişi ile bir AVS değerlendirmesi oluşturun | Microsoft Docs
description: Azure geçişi ile bir AVS değerlendirmesi oluşturmayı açıklar
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 72372e6365a2535e449681549a515c3f8594f2f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786609"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Azure VMware çözümü (AVS) değerlendirmesi oluşturma

Bu makalede, Azure geçişi: bulma ve değerlendirme ile VMware ortamında şirket içi sunucular için bir Azure VMware çözümü (AVS) değerlendirmesi oluşturma işlemi açıklanmaktadır.

[Azure geçişi](migrate-services-overview.md) , Azure 'a geçiş yapmanıza yardımcı olur. Azure geçişi, şirket içi altyapıyı, uygulamaları ve verileri Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için merkezi bir merkez sağlar. Hub, değerlendirme ve geçiş için Azure araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini sağlar.

## <a name="before-you-start"></a>Başlamadan önce

- Bir Azure geçişi projesi [oluşturduğunuzdan](./create-manage-projects.md) emin olun.
- Zaten bir proje oluşturduysanız Azure geçişi: bulma ve değerlendirme [aracını eklediğinizden emin](how-to-assess.md) olun.
- Bir değerlendirme oluşturmak için, [VMware](how-to-set-up-appliance-vmware.md)Için bir Azure geçiş gereci ayarlamanız gerekir. Bu, şirket içi sunucuları tespit eder ve Azure geçişi: bulma ve değerlendirme 'a meta veri ve performans verileri gönderir. [Daha fazla bilgi edinin](migrate-appliance.md).
- Ayrıca [, sunucu meta verilerini](./tutorial-discover-import.md) virgülle ayrılmış değerler (CSV) biçiminde de içeri aktarabilirsiniz.


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Azure VMware çözümü (AVS) değerlendirmesi genel bakış

Azure geçişi: bulma ve değerlendirme kullanarak oluşturabileceğiniz üç tür değerlendirme vardır.

***Değerlendirme türü** | **Ayrıntılar**
--- | --- 
**Azure VM** | Şirket içi sunucularınızı Azure sanal makinelerine geçirmeye yönelik değerlendirmeler. Bu değerlendirme türünü kullanarak, [VMware](how-to-set-up-appliance-vmware.md) ve [Hyper-V](how-to-set-up-appliance-hyper-v.md) ortamındaki şirket Içi sunucularınızı ve Azure VM 'lerine geçiş için [fiziksel sunucuları](how-to-set-up-appliance-physical.md) değerlendirebilirsiniz.
**Azure SQL** | Şirket içi SQL Server 'larınızı VMware ortamınızdan Azure SQL veritabanı 'na veya Azure SQL yönetilen örneğine geçirmeye yönelik değerlendirmeler.
**Azure VMware Çözümü (AVS)** | Şirket içi sunucularınızı [Azure VMware Çözümü'ne (AVS)](../azure-vmware/introduction.md) geçirmeye yönelik değerlendirmeler. Bu değerlendirme türünü kullanarak Azure VMware çözümüne (AVS) geçiş için, [VMware ortamındaki](how-to-set-up-appliance-vmware.md) şirket içi sunucularınızı değerlendirebilirsiniz. [Daha fazla bilgi edinin](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Azure VMware çözümü (AVS) değerlendirmesi yalnızca VMware ortamındaki sunucular için oluşturulabilir.


Azure VMware çözümü (AVS) değerlendirmesi oluşturmak için kullanabileceğiniz iki tür boyutlandırma ölçütü vardır:

**Değerlendirme** | **Ayrıntılar** | **Veriler**
--- | --- | ---
**Performans tabanlı** | Şirket içi sunucuların toplanan performans verilerini temel alan değerlendirmeler. | **Önerilen düğüm boyutu**: değerlendirme için seçtiğiniz düğüm türü, depolama türü ve FTT ayarı Ile birlikte CPU ve bellek kullanım verilerine göre.
**Şirket içi olarak** | Şirket içi boyutlandırmayı temel alan değerlendirmeler. | **Önerilen düğüm boyutu**: değerlendirme için seçtiğiniz düğüm türü, depolama türü ve FTT ayarı ile birlikte Şirket içi sunucu boyutuna bağlı olarak.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Azure VMware çözümü (AVS) değerlendirmesi çalıştırma

1.  **Windows, Linux ve SQL Server**> **genel bakış** sayfasında **sunucuları değerlendir ve geçir**' e tıklayın.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Azure geçişi için genel bakış sayfası":::

1. **Azure geçişi: bulma ve değerlendirme** bölümünde **değerlendir**' e tıklayın.

   ![Değerlendirme düğmesinin konumu](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. **Sunucu**  >  **değerlendirmesi türünü** değerlendir bölümünde **Azure VMware çözümü (AVS)** seçeneğini belirleyin.

1. **Bulma kaynağında**:

    - Gereci kullanarak sunucular keşfetiyorsanız, **Azure geçişi gereci ' ndan bulunan sunucuları** seçin.
    - İçeri aktarılan bir CSV dosyası kullanarak sunucular keşfettiği takdirde, **Içeri aktarılan sunucular**' ı seçin. 
    
1. Değerlendirme özelliklerini gözden geçirmek için **Düzenle** ' ye tıklayın.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Değerlendirme ayarlarını seçme sayfası":::
 

1. **Değerlendirme özellikleri**  >  **hedef özellikleri**:

    - **Hedef konum**' da, geçirmek istediğiniz Azure bölgesini belirtin.
       - Boyut ve maliyet önerileri belirttiğiniz konuma göre hesaplanır.
   - **Depolama türü** varsayılan olarak **vSAN** olarak ayarlanır. Bu, bir AVS özel bulutu için varsayılan depolama türüdür.
   - **Ayrılmış örnekler** Şu anda AVS düğümleri için desteklenmiyor.
1. **VM boyutu**:
    - **Düğüm türü** varsayılan olarak **AV36**' dir. Azure geçişi, sunucuları AVS 'ye geçirmek için gereken düğüm düğümünü önerir.
    - **FTT ayarında, RAID düzeyinde**, tolerans ve RAID birleşimine yönelik hata seçin.  On-premises Server disk gereksinimiyle birlikte bulunan seçili FTT seçeneği, AVS 'de gereken toplam vSAN depolama alanını belirler.
    - **CPU fazla aboneliği**' nde, AVS düğümündeki bir fiziksel çekirdekle ilişkili sanal çekirdekleri oranını belirtin. 4:1 'den büyük abonelik, performans düşüşüne neden olabilir, ancak Web sunucusu türü iş yükleri için de kullanılabilir.
    - **Bellek fazla tamamlama faktöründe**, küme üzerinde işlemeye göre bellek oranını belirtin. 1 değeri %100 bellek kullanımını temsil eder, 0,5 Örneğin %50 ve 2 kullanılabilir belleğin %200 ' i kullanıyor. Yalnızca 0,5 ' den 10 ' a kadar ondalık bir yere değer ekleyebilirsiniz.
    - **Yinelenenleri kaldırma ve Compression faktöründe**, iş yükleriniz için beklenen yinelenenleri kaldırma ve sıkıştırma faktörünü belirtin. Gerçek değer Şirket içi vSAN veya depolama yapılandırmadan alınabilir ve bu, iş yüküne göre farklılık gösterebilir. 3 değeri, 300 GB disk için yalnızca 100 GB depolama alanı için 3x anlamına gelir. 1 değeri, hiç dupe veya Compression anlamına gelir. Yalnızca 1 ' den 10 ' a kadar ondalık bir yere değerler ekleyebilirsiniz.
1. **Düğüm boyutu**: 
    - **Boyutlandırma ölçütünde**, değerlendirmeyi statik meta verilerde veya performans tabanlı verilerde temel almak istiyorsanız seçin. Performans verileri kullanıyorsanız:
        - **Performans geçmişi**' nde, değerlendirmeye dayandırmak istediğiniz veri süresini belirtin
        - **Yüzdelik kullanım**' de, performans örneği için kullanmak istediğiniz yüzdebirlik değerini belirtin. 
    - **Rahatlık faktörüyle**, değerlendirme sırasında kullanmak istediğiniz arabelleği belirtin. Bu hesaplar, dönemsel kullanım, kısa performans geçmişi ve gelecekteki kullanımlarda olası artışlar gibi sorunlara yöneliktir. Örneğin, iki bir rahatlık faktörü kullanıyorsanız:
    
        **Bileşen** | **Etkin kullanım** | **Rakip çarpanı ekleme (2,0)**
        --- | --- | ---
        Çekirdekler | 2  | 4
        Bellek | 8 GB | 16 GB  

1. **Fiyatlandırma**:
    - **Teklifte**, kaydettiğiniz [Azure teklifi](https://azure.microsoft.com/support/legal/offer-details/) görüntülenir. Değerlendirme, bu teklifin maliyetini tahmin eder.
    - **Para birimi**' nde, hesabınız için faturalandırma para birimini seçin.
    - **İndirim (%)** bölümünde, Azure teklifinin üzerine aldığınız aboneliğe özgü indirimleri ekleyin. Varsayılan ayar, %0’dır.

1. Değişiklik yaparsanız **Kaydet** ' e tıklayın.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Değerlendirme özellikleri":::

1. **Sunucuları değerlendir** bölümünde **İleri**' ye tıklayın.

1. Değerlendirme **adını değerlendirmek için sunucuları seçin**  >   > değerlendirme için bir ad belirtin. 
 
1. > **Grup Seç veya oluştur** bölümünde **Yeni oluştur** ' u seçin ve bir grup adı belirtin. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Bir gruba sunucular ekleme":::
 
1. Gereç ' ı seçin ve gruba eklemek istediğiniz sunucuları seçin. Ardından **İleri**'ye tıklayın.

1. **İnceleme ve değerlendirme oluştur**' da, değerlendirme ayrıntılarını gözden geçirin ve grubu oluşturmak ve değerlendirmeyi çalıştırmak Için değerlendirme **Oluştur** ' a tıklayın.

    > [!NOTE]
    > Performans tabanlı değerlendirmeler için, değerlendirme oluşturmadan önce bulmayı başlattıktan sonra en az bir gün beklemeniz önerilir. Bu, daha yüksek güvenilirliğe sahip performans verilerinin toplanması için zaman sağlar. İdeal olarak, bulmayı başlattıktan sonra, belirttiğiniz performans süresini (gün/hafta/ay) yüksek güvenilirlikli bir derecelendirme için bekleyin.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Azure VMware çözümü (AVS) değerlendirmesini gözden geçirme

Bir Azure VMware çözümü (AVS) değerlendirmesi şunları açıklar:

- **Azure VMware çözümü (AVS) hazırlığı**: Şirket Içi sunucuların Azure VMware çözümüne (AVS) geçiş için uygun olup olmadığı.
- **AVS düğüm sayısı**: sunucuları çalıştırmak için gereken, tahmini AVS düğüm sayısı.
- **AVS düğümleri genelinde kullanım**: tüm DÜĞÜMLERDE öngörülen CPU, bellek ve depolama kullanımı.
    - Kullanım, vCenter Server, NSX Manager (büyük), NSX Edge gibi aşağıdaki küme yönetimi üst kısmında bulunan ön düzenleme içerir. HCX aynı zamanda HCX Yöneticisi ve x gereci (11 CPU), 75 GB RAM ve sıkıştırma ve yinelenenleri kaldırma öncesinde 7 22GB depolama alanı.
- **Aylık maliyet tahmini**: Şirket Içi VM 'leri çalıştıran tüm Azure VMware çözümü (AVS) düğümlerine yönelik tahmini aylık maliyetler.


### <a name="view-an-assessment"></a>Değerlendirmeyi görüntüleme

1. **Windows, Linux ve SQL Server**  >  **Azure geçişi: bulma ve değerlendirme**, * * Azure VMware çözümü * * seçeneğinin yanındaki sayıya tıklayın.

1. **Değerlendirmeler** sayfasında açmak istediğiniz değerlendirmeye tıklayın. Örnek olarak (yalnızca Örneğin, tahminler ve maliyetler): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="AVS değerlendirmesi Özeti":::

1. Değerlendirme özetini gözden geçirin. Ayrıca değerlendirme özelliklerini düzenleyebilir veya değerlendirmeyi yeniden hesaplayabilirsiniz.

### <a name="review-azure-vmware-solution-avs-readiness"></a>Azure VMware Solution (AVS) hazırlığını gözden geçirme

1. **Azure hazır** olduğunda, sunucuların AVS 'ye geçiş için hazır olup olmadığını doğrulayın.

2. Sunucu durumunu gözden geçirin:
    - **AVS Için hazırlanma**: sunucu, herhangi bir değişiklik yapılmadan Azure 'A (AVS) olarak geçirilebilir. Tam AVS desteğiyle AVS 'de başlatılır.
    - **Koşullara göre**: sunucu, geçerli vSphere sürümü ile uyumluluk sorunlarına ve sunucudaki tam işlevselliği AVS 'de hazırlanmadan önce büyük olasılıkla VMware araçlarının ve diğer ayarlarının gerekli olabileceği bir durum olabilir.
    - **AVS için hazırlanma**: sunucu AVS 'de başlamacaktır. Örneğin, şirket içi sunucuda bir CD-ROM gibi bağlı bir dış cihaz varsa, VMotion işlemi başarısız olur (VMware VMotion kullanılıyorsa).
    - **Hazır olma bilinmiyor**: Azure geçişi, şirket içi ortamdan toplanan meta veriler nedeniyle sunucunun hazır olduğunu saptayamadık.

3. Önerilen aracı gözden geçirin:
    - VMware **HCX veya Enterprise**: VMware sunucuları Için VMware karma bulut uzantısı (HCX) çözümü, şirket içi iş yükünüzü Azure VMware çözümünüz (AVS) özel bulutuna geçirmek için önerilen geçiş aracıdır. [Daha Fazla Bilgi Edinin](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Bilinmiyor**: CSV dosyası aracılığıyla içeri aktarılan sunucular için varsayılan geçiş aracı bilinmez. Ancak VMware sunucuları için, VMware karma bulut uzantısı (HCX) çözümünün kullanılması önerilir. 

4. **AVS hazırlığı** durumuna tıklayın. VM hazırlığı ayrıntılarını görüntüleyebilir ve işlem, depolama ve ağ ayarları dahil olmak üzere VM ayrıntılarını görmek için ayrıntıya gidebilirsiniz.

### <a name="review-cost-details"></a>Maliyet ayrıntılarını gözden geçirme

Bu görünüm, Azure VMware çözümünde (AVS) çalışan sunucuların tahmini maliyetini gösterir.

1. Aylık toplam maliyetleri gözden geçirin. Değerlendirilen gruptaki tüm sunucular için toplu maliyetler gösterilir. 

    - Maliyet tahminleri, toplam tüm sunucuların kaynak gereksinimlerini dikkate alarak gereken AVS düğümlerinin sayısına bağlıdır.
    - Azure VMware çözümü (AVS) fiyatlandırması düğüm başına olduğunda, toplam maliyet işlem maliyeti ve depolama maliyeti dağıtımına sahip değildir.
    - Maliyet tahmini, AVS 'de şirket içi sunucuları çalıştırmak içindir. AVS değerlendirmesi PaaS veya SaaS maliyetlerini göz önünde bulundurmaz.
    
2. Aylık depolama maliyeti tahminlerini gözden geçirebilirsiniz. Bu görünüm, farklı türlerdeki depolama disklerinin üzerine bölünen, değerlendirilen grup için toplanan depolama maliyetlerini gösterir.

3. Belirli sunucuların ayrıntılarını görmek için ayrıntıya gidebilirsiniz.


### <a name="review-confidence-rating"></a>Güvenilirlik derecelendirmesini gözden geçirme

Performans tabanlı değerlendirmeler çalıştırdığınızda, değerlendirmeye bir güvenilirlik derecelendirmesi atanır.

![Güvenilirlik derecelendirmesi](./media/how-to-create-assessment/confidence-rating.png)

- 1-yıldız (en düşük) ile 5 yıldız (en yüksek) arasında bir derecelendirme verilir.
- Güvenilirlik derecelendirmesi, değerlendirme tarafından belirtilen boyut önerilerinin güvenilirliğini tahmin etmenize yardımcı olur.
- Güvenilirlik derecelendirmesi, değerlendirmeyi hesaplamak için gereken veri noktalarının kullanılabilirliğine bağlıdır.
- Performans tabanlı boyutlandırma için, AVS değerlendirmelerinin CPU ve sunucu belleği için kullanım verilerine ihtiyacı vardır. Aşağıdaki performans verileri toplanır ancak AVS değerlendirmelerine yönelik boyutlandırma önerilerinde kullanılmaz:
  - Sunucuya bağlı her diske yönelik disk ıOPS ve aktarım hızı verileri.
  - Bir sunucuya bağlı her ağ bağdaştırıcısı için performans tabanlı boyutlandırmayı işlemek üzere ağ g/ç.

Bir değerlendirme için güven derecelendirmeleri aşağıdaki gibidir.

**Veri noktası kullanılabilirliği** | **Güvenilirlik derecelendirmesi**
--- | ---
%0-%20 | 1 Yıldız
%21-%40 | 2 Yıldız
%41-%60 | 3 Yıldız
%61-%80 | 4 Yıldız
%81-%100 | 5 Yıldız

Performans verileri hakkında [daha fazla bilgi edinin](concepts-azure-vmware-solution-assessment-calculation.md) 


## <a name="next-steps"></a>Sonraki adımlar

- Yüksek güvenilirlikli gruplar oluşturmak için [bağımlılık eşlemeyi](how-to-create-group-machine-dependencies.md) nasıl kullanacağınızı öğrenin.
- AVS değerlendirmelerinin nasıl hesaplandığı hakkında [daha fazla bilgi edinin](concepts-azure-vmware-solution-assessment-calculation.md) .