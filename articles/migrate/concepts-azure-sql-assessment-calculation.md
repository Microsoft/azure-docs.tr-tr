---
title: Azure SQL değerlendirmesi bulma ve değerlendirme aracı
description: Azure 'da bulma ve değerlendirme aracında Azure SQL değerlendirmeleri hakkında bilgi edinin
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: a22fa184f91cb409f7a4d7795a4bc34bdd83e598
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077814"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>Değerlendirmeye genel bakış (Azure SQL 'e geçiş)

Bu makalede, [Azure geçişi: bulma ve değerlendirme aracı](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)kullanılarak bir VMware ORTAMıNDAN Azure SQL veritabanlarına veya yönetilen örneklere şirket içi SQL Server örnekleri geçirmeye yönelik değerlendirmelere genel bakış sunulmaktadır.

## <a name="whats-an-assessment"></a>Değerlendirme nedir?
Bulma ve değerlendirme aracı ile bir değerlendirme, verilerin zaman içindeki bir noktaya görüntüsüdür ve kullanıma hazır olma durumunu ölçer ve şirket içi sunucuları Azure 'a geçirme etkisini tahmin eder.

## <a name="types-of-assessments"></a>Değerlendirme türleri

Azure geçişi: bulma ve değerlendirme aracını kullanarak oluşturabileceğiniz üç tür değerlendirme vardır.

**Değerlendirme Türü** | **Ayrıntılar**
--- | --- 
**Azure VM** | Şirket içi sunucularınızı Azure sanal makinelerine geçirmeye yönelik değerlendirmeler. <br/><br/> Bu değerlendirme türünü kullanarak, [VMware](how-to-set-up-appliance-vmware.md) ve [Hyper-V](how-to-set-up-appliance-hyper-v.md) ortamındaki şirket Içi sunucularınızı ve Azure VM 'lerine geçiş için [fiziksel sunucuları](how-to-set-up-appliance-physical.md) değerlendirebilirsiniz.
**Azure SQL** | Şirket içi SQL Server 'larınızı VMware ortamınızdan Azure SQL veritabanı 'na veya Azure SQL yönetilen örneğine geçirmeye yönelik değerlendirmeler.
**Azure VMware Çözümü (AVS)** | Şirket içi sunucularınızı [Azure VMware Çözümü'ne (AVS)](../azure-vmware/introduction.md) geçirmeye yönelik değerlendirmeler. <br/><br/> Bu değerlendirme türünü kullanarak Azure VMware çözümüne (AVS) geçiş için şirket içi [VMware VM](how-to-set-up-appliance-vmware.md) 'lerinizi değerlendirebilirsiniz. [Daha fazla bilgi edinin](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Bulma ve değerlendirme aracında Azure VM veya AVS değerlendirmesi sayısı yanlışsa, Tüm değerlendirmelere gitmek ve Azure VM veya AVS değerlendirmelerini yeniden hesaplamak için toplam değerlendirme sayısına tıklayın. Bulma ve değerlendirme aracı daha sonra bu değerlendirme türü için doğru sayıyı gösterir. 

Azure SQL değerlendirmesi bir boyutlandırma ölçütü sağlar:

**Boyutlandırma ölçütü** | **Ayrıntılar** | **Veriler**
--- | --- | ---
**Performans tabanlı** | Toplanan performans verilerine göre öneriler sunan değerlendirmeler | Azure SQL yapılandırması; CPU kullanımı, bellek kullanımı, ıOPS (veri ve günlük dosyaları), aktarım hızı ve GÇ işlemlerinin gecikme süresini içeren SQL örnekleri ve veritabanlarının performans verilerini temel alır.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>Şirket içi SQL sunucularımı değerlendirmek Nasıl yaparım? mi?

Hafif bir Azure geçişi gereci tarafından toplanan yapılandırma ve kullanım verilerini kullanarak şirket içi SQL Server örneklerinizi değerlendirebilirsiniz. Gereç, şirket içi SQL Server örneklerini ve veritabanlarını bulur ve yapılandırma ve performans verilerini Azure geçişi 'ne gönderir. [Daha Fazla Bilgi](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Gereç ile Nasıl yaparım? değerlendirin?
Şirket içi sunucuları bulmaya yönelik bir Azure geçiş gereci dağıtıyorsanız, aşağıdaki adımları uygulayın:
1.  Azure 'u ve şirket içi ortamınızı Azure geçişi ile çalışacak şekilde ayarlayın.
2.  İlk değerlendirmenizi için bir Azure geçişi projesi oluşturun ve Azure geçişi: bulma ve değerlendirme aracını buna ekleyin.
3.  Hafif bir Azure geçişi gereci dağıtın. Gereç, şirket içi sunucuları sürekli bulur ve Azure geçişi 'ne yapılandırma ve performans verilerini gönderir. Gereci bir VM veya fiziksel sunucu olarak dağıtın. Değerlendirmek istediğiniz sunuculara herhangi bir şey yüklemeniz gerekmez.

Gereç bulma işlemine başladıktan sonra, değerlendirmek istediğiniz sunucuları bir gruba toplayıp değerlendirme türü **Azure SQL** olan grup için bir değerlendirme çalıştırabilirsiniz.

Bu adımları denemek için [SQL Server örneklerini](tutorial-assess-sql.md) değerlendirmek için Öğreticimizi izleyin.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>Gereç SQL örnekleri ve veritabanları için performans verilerini nasıl hesaplar?

Gereç, aşağıdaki adımlarla işlem ayarları için performans verilerini toplar:
1. Gereç gerçek zamanlı bir örnek noktası toplar. SQL sunucuları için, her 30 saniyede bir örnek noktası toplanır.
2. Gereç, 10 dakikada her 30 saniyede bir toplanan örnek veri noktalarını toplar. Veri noktasını oluşturmak için gereç tüm örneklerden tepe değerlerini seçer. Her bir sayaca ait maksimum, ortalama ve varyansı Azure 'a gönderir.
3. Azure geçişi, geçen aya ait 10 dakikalık tüm veri noktalarını depolar.
4. Bir değerlendirme oluşturduğunuzda, Azure geçişi, doğru bir şekilde kullanmak için kullanılacak uygun veri noktasını tanımlar. Kimlik, performans geçmişi ve yüzdebirlik kullanımı için yüzdebirlik değerlerini temel alır.
    - Örneğin, performans geçmişi bir hafta ise ve yüzdebirlik kullanımı 95. yüzdebirlik ise, değerlendirme son hafta için 10 dakikalık örnek noktaları sıralar. Bunları artan düzende sıralar ve doğru bir şekilde yapmak için 95. yüzdebirlik değerini seçer.
    - 95. yüzdebirlik değeri, 99. yüzdebirlik değerini seçtiğiniz takdirde dahil olabilen tüm aykırı değerleri yok saydığınızdan emin olmanızı sağlar.
    - Dönemin en yoğun kullanımını seçmek ve tüm aykırı değerleri kaçırmak istemiyorsanız, yüzdebirlik kullanımı için 99. yüzdebirlik ' ü seçin.
5. Bu değer, gerecin topladığı bu ölçümler için etkili performans kullanım verilerini elde etmek için, rahatlık faktörüyle çarpılır:
    - CPU kullanımı (%)
    - Bellek kullanımı (%)
    - GÇ/s okuma ve yazma GÇ/sn (veri ve günlük dosyaları)
    - Okunan MB/s ve yazma MB/s (aktarım hızı)
    - GÇ işlemlerinin gecikmesi

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Azure SQL değerlendirmesi oluşturmak ve özelleştirmek için hangi Özellikler kullanılır?

Azure SQL değerlendirmesi özelliklerine aşağıda verilmiştir:

**Özellik** | **Ayrıntılar**
--- | ---
**Hedef konum** | Geçirmek istediğiniz Azure bölgesi. Azure SQL yapılandırması ve maliyet önerileri belirttiğiniz konuma göre yapılır.
**Hedef dağıtım türü** | Değerlendirmeyi çalıştırmak istediğiniz hedef dağıtım türü: <br/><br/> Azure geçişi 'nin Azure SQL MI ve Azure SQL DB 'ye geçiş için SQL sunucularınızın hazır olduğunu değerlendirmesini ve en uygun hedef dağıtım seçeneğini, hedef katmanını, Azure SQL yapılandırmasını ve aylık tahminleri önermesini istiyorsanız, **Önerilen**' i seçin.<br/><br/>SQL Server 'larınızı yalnızca Azure SQL veritabanlarına geçirmeye değerlendirmek ve hedef katmanı, Azure SQL DB yapılandırması ve aylık tahminleri gözden geçirmek istiyorsanız **Azure SQL veritabanı**' nı seçin.<br/><br/>SQL Server 'larınızı yalnızca Azure SQL veritabanlarına geçirmek için değerlendirmek istiyorsanız Azure SQL **mi**' yi seçin ve hedef katmanını, Azure SQL mı yapılandırmasını ve aylık tahminleri gözden geçirin.
**Ayrılmış kapasite** | Değerlendirmede maliyet tahminleri bunları hesaba alacak şekilde ayrılmış kapasiteyi belirtir.<br/><br/> Ayrılmış kapasite seçeneğini belirlerseniz, "Indirim (%)" belirtemezsiniz.
**Boyutlandırma ölçütü** | Bu özellik, Azure SQL yapılandırmasını sağ boyuta almak için kullanılır. <br/><br/> Varsayılan olarak, **performans tabanlı** olarak ayarlanır; Bu, değerlendirmenin en iyi şekilde boyutlandırılmış BIR Azure SQL yönetilen örneği ve/veya Azure SQL veritabanı katmanı/yapılandırma önerisi önermek için SQL Server örnekleri ve veritabanlarının performans ölçümlerini toplayacağı anlamına gelir.
**Performans geçmişi** | Performans geçmişi performans verileri değerlendirilirken kullanılan süreyi belirtir.
**Yüzdebirlik kullanımı** | Yüzdebirlik kullanımı, doğru hale getirmek için kullanılan performans örneğinin yüzdebirlik değerini belirtir.
**Konfor katsayısı** | Değerlendirme sırasında kullanılan arabellek. Dönemsel kullanım, kısa performans geçmişi ve gelecekteki kullanımlarda olası artışlar gibi sorunlar için BT hesapları.<br/><br/> Örneğin, %20 kullanımı olan 10 çekirdekli bir örnek, normalde iki çekirdekli örnekle sonuçlanır. 2,0 ile, sonuç olarak dört çekirdekli bir örnek olur.
**Teklif/lisanslama programı** | Kayıtlı olduğunuz [Azure teklifi](https://azure.microsoft.com/support/legal/offer-details/) . Şu anda yalnızca Kullandıkça öde ve kullandıkça öde geliştirme/test seçeneklerinden birini belirleyebilirsiniz. Ayrılmış kapasiteyi uygulayarak ve kullandıkça öde teklifinin üzerine Azure Hibrit Avantajı ek indirim sunabileceğinize dikkat edin.
**Hizmet katmanı** | Azure SQL veritabanı ve/veya Azure SQL yönetilen örneği 'ne geçiş için iş ihtiyaçlarınızı karşılamak üzere en uygun hizmet katmanı seçeneği:<br/><br/>Azure geçişi 'nin sunucularınız için en uygun hizmet katmanını önermesini istiyorsanız **önerilir** . Bu genel amaçlı veya Iş açısından kritik olabilir. <br/><br/> **Genel amaçlı** Bütçe odaklı iş yükleri için tasarlanan bir Azure SQL yapılandırması istiyorsanız. [Daha Fazla Bilgi](../azure-sql/database/service-tier-general-purpose.md) <br/><br/> **İş açısından kritik** Düşük gecikmeli iş yükleri için tasarlanan bir Azure SQL yapılandırmasının, hatalara ve hızlı yük devretmeler ile yüksek dayanıklılık sağlamak istiyorsanız. [Daha Fazla Bilgi](../azure-sql/database/service-tier-business-critical.md)
**Para Birimi** | Hesabınız için faturalandırma para birimi.
**İndirim (%)** | Azure teklifinin üzerine aldığınız, aboneliğe özgü tüm indirimler. Varsayılan ayar, %0’dır.
**Azure Hibrit Avantajı** | Zaten bir SQL Server lisansınızın olup olmadığını belirtir. <br/><br/> Bunu yaptıysanız ve SQL Server aboneliklerinin etkin yazılım güvencesi kapsamında ele alındıklarında, lisansları Azure 'a getirdiğinizde Azure Hibrit Avantajı uygulayabilirsiniz.

Azure geçişi ile bir değerlendirme oluşturmak için [en iyi uygulamaları gözden geçirin](best-practices-assessment.md) .

## <a name="calculate-readiness"></a>Hazırlığı hesapla

> [!NOTE]
Değerlendirme yalnızca çevrimiçi durumdaki veritabanlarını içerir. Veritabanı başka bir durumdaysa, değerlendirme bu tür veritabanları için hazırlık, boyutlandırma ve maliyet hesaplamasını yoksayar. Bu tür veritabanlarını değerlendirmek istiyorsanız lütfen veritabanının durumunu değiştirin ve değerlendirmeyi bir süre sonra yeniden hesaplayın.

### <a name="azure-sql-readiness"></a>Azure SQL hazırlığı

SQL örnekleri ve veritabanları için Azure SQL hazırlığı, Azure SQL veritabanı ve Azure SQL yönetilen örneği ile bir özellik uyumluluğu denetimine dayalıdır:
1. Azure SQL değerlendirmesi, uyumluluk sorunlarını belirlemek için kaynak SQL Server iş yükleri (SQL Aracısı işleri, bağlı sunucular vb.) ve kullanıcı veritabanları şemaları (tablolar, görünümler, Tetikleyiciler, saklı yordamlar vb.) tarafından şu anda kullanılan SQL Server örnek özelliklerini dikkate alır.
1. Hiçbir uyumluluk sorunu bulunmazsa, hazırlık hedef dağıtım türü için **hazır** olarak Işaretlenir (Azure SQL veritabanı veya Azure SQL yönetilen örneği)
1. Belirli bir hedef dağıtım türüne geçiş engellenmeyen düzeyi düşürülmüş veya desteklenmeyen özellikler gibi kritik olmayan uyumluluk sorunları varsa, hazırlık, **Uyarı** ayrıntıları ve önerilen düzeltme kılavuzu ile **hazır** (köprü ve mavi bilgi simgesi) olarak işaretlenir.
1. Belirli bir hedef dağıtım türüne geçişi engelleyebilen herhangi bir uyumluluk sorunu varsa, hazırlık, **sorun** ayrıntıları ve önerilen düzeltme kılavuzu Ile **hazır değil** olarak işaretlenir.
    - Belirli bir hedef dağıtım türü için mevcut olmayan bir SQL örneğinde bile bir veritabanı varsa, örnek bu dağıtım türü için **kullanılamaz** olarak işaretlenir.
1. Bulma devam ediyor veya bir SQL örneği ya da veritabanı için herhangi bir bulma sorunu varsa, değerlendirme bu SQL örneği için hazırlık işlemini hesaplamadığından, hazırlık **bilinmiyor** olarak işaretlenir.

### <a name="recommended-deployment-type"></a>Önerilen dağıtım türü

Azure SQL değerlendirme özelliklerinde **Önerilen** hedef dağıtım türünü seçerseniz Azure GEÇIŞI, SQL örneğiniz ile uyumlu bır Azure SQL dağıtım türü önerir. Microsoft tarafından önerilen hedefe geçiş, genel geçiş çabalarınızı azaltır. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Azure SQL hazırlığı temelinde önerilen dağıtım türü

 **Azure SQL DB hazırlığı** | **Azure SQL MI hazırlığı** | **Önerilen dağıtım türü** | **Azure SQL yapılandırması ve maliyet tahminleri hesaplandı mı?**
 --- | --- | --- | --- |
 Hazır | Hazır | Azure SQL DB veya <br/>Azure SQL MI | Yes
 Hazır | Hazırlanma veya<br/> Bilinmiyor | Azure SQL DB | Yes
 Hazırlanma veya<br/>Bilinmiyor | Hazır | Azure SQL MI | Yes
 Hazırlanma | Hazırlanma | Azure VM için hazırlanma olasılığı | No
 Hazırlanma veya<br/>Bilinmiyor | Hazırlanma veya<br/>Bilinmiyor | Bilinmiyor | No

> [!NOTE]
> Önerilen dağıtım türü değerlendirme özelliklerinde **önerildiği** şekilde seçilirse ve kaynak SQL Server hem Azure SQL veritabanı tek veritabanı hem de Azure SQL yönetilen örneği için uygun ise, değerlendirme, maliyetinizi en iyi duruma getirmek ve boyut ve performans sınırlarının içine sığması için belirli bir seçenek önerir.

#### <a name="potentially-ready-for-azure-vm"></a>Azure VM için hazırlanma olasılığı

SQL örneği Azure SQL veritabanı ve Azure SQL yönetilen örneği için hazırsanız, önerilen dağıtım türü, *Azure VM için, olası* olarak işaretlendi olarak işaretlenir.
- Örneğin, örneğin çalışıyor olduğu sunucunun bunun yerine bir Azure VM 'ye geçişe hazırlanın olup olmadığını anlamak için, kullanıcının değerlendirme türü ile "Azure VM" olarak bir değerlendirme oluşturması önerilir. Şunlara dikkat edin:
    - Azure geçişi 'ndeki Azure VM değerlendirmelerinde, şu anda, Azure sanal makinesinde SQL örnekleri ve veritabanlarını çalıştırmaya yönelik belirli performans ölçümlerini düşünmeyecektir. 
    - Sunucuda Azure VM değerlendirmesi çalıştırdığınızda, önerilen boyut ve maliyet tahminleri sunucuda çalıştırılan ve Sunucu Geçişi aracı kullanılarak bir Azure sanal makinesine geçirilebilecek tüm örnekleri kapsar. Geçiş yapmadan önce Azure sanal makinelerindeki SQL Server için [performans yönergelerini gözden geçirin](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).


## <a name="calculate-sizing"></a>Boyutlandırmayı hesapla

### <a name="azure-sql-configuration"></a>Azure SQL yapılandırması

Değerlendirme, hazırlığı ve önerilen Azure SQL dağıtım türünü belirledikten sonra, şirket içi SQL örneği performansını karşılayabilir veya aşabileceğinden belirli bir hizmet katmanını ve Azure SQL yapılandırmasını (SKU boyutu) hesaplar:
1. Bulma işlemi sırasında, Azure geçişi SQL örnek yapılandırmasını ve aşağıdakileri içeren performansı toplar:
    - Sanal çekirdekler (ayrılmış) ve CPU kullanımı (%)
        - SQL örneği için CPU kullanımı, SQL Server üzerinde örnek tarafından kullanılan ayrılmış CPU yüzdesidir
        - Bir veritabanı için CPU kullanımı, SQL örneğindeki veritabanı tarafından kullanılan ayrılmış CPU yüzdesidir
    - Bellek (ayrılan) ve bellek kullanımı (%)
    - GÇ/s okuma ve yazma GÇ/sn (veri ve günlük dosyaları)
        - GÇ/s okuma ve yazma GÇ/sn 'leri, bu örnekte bulunan tüm veritabanlarının Okuma GÇ/s ve yazma GÇ 'leri eklenerek hesaplanır.
    - Okunan MB/s ve yazma MB/s (aktarım hızı)
    - GÇ işlemlerinin gecikmesi
    - Toplam DB boyutu ve veritabanı dosya kuruluşları
        - Veritabanı boyutu, tüm veri ve günlük dosyaları eklenerek hesaplanır.
1. Değerlendirme, tüm yapılandırma ve performans verilerini toplar ve çeşitli Azure SQL hizmeti katmanlarında ve yapılandırmalarında en iyi eşleşmeyi bulmaya çalışır ve SQL örneği performans gereksinimlerini eşleştirecek veya aşabileceğinden maliyeti en iyi duruma getirmek için bir yapılandırma seçer.

### <a name="confidence-ratings"></a>Güven derecelendirmeleri 
Her Azure SQL değerlendirmesi bir güvenirlik derecelendirmesi ile ilişkilendirilir. Derecelendirme, bir (en düşük) ile beş (en yüksek) yıldıza göre değişir. Güvenilirlik derecelendirmesi, Azure geçişi 'nin sağladığı önerilerin boyutunu tahmin etmenize yardımcı olur.
- Güvenirlik derecelendirmesi bir değerlendirmeye atanır. Derecelendirme, değerlendirmeyi hesaplamak için gereken veri noktalarının kullanılabilirliğine bağlıdır.
- Performans tabanlı boyutlandırma için değerlendirme, tüm SQL örnekleri ve veritabanlarının performans verilerini toplar ve şunları içerir:
    - CPU kullanımı (%)
    - Bellek kullanımı (%)
    - GÇ/s okuma ve yazma GÇ/sn (veri ve günlük dosyaları)
    - Okunan MB/s ve yazma MB/s (aktarım hızı)
    - GÇ işlemlerinin gecikmesi
    
Bu kullanım numaralarının herhangi biri kullanılabilir değilse, boyut önerileri güvenilir olmayabilir.
Bu tabloda, kullanılabilir veri noktalarının yüzdesine bağlı olan değerlendirme güvenilirliği derecelendirmeleri gösterilmektedir:

**Veri noktası kullanılabilirliği** | **Güvenilirlik derecelendirmesi**
--- | ---
%0-%20 | 1 yıldız
%21-%40 | 2 yıldız
%41-%60 | 3 yıldız
%61-%80 | 4 yıldız
%81-%100 | 5 yıldız

#### <a name="low-confidence-ratings"></a>Düşük güvenilirlikli derecelendirmeler
Bir değerlendirmenin en düşük güvenilirlik derecelendirmesinin neden olmasının birkaç nedeni aşağıda verilmiştir:
- Değerlendirmeyi oluşturduğunuz süre boyunca ortamınızın profilini oluşturmadınız. Örneğin, performans süresi ile bir gün ayarlanmış bir değerlendirme oluşturursanız, toplanan tüm veri noktaları için bulmayı başlattıktan sonra en az bir gün beklemeniz gerekir.
- Değerlendirme, değerlendirme süresi içinde sunucuların bir bölümü veya tümü için performans verilerini toplayamıyor. Yüksek güvenilirlikli bir derecelendirme için lütfen şunları doğrulayın:
    - Değerlendirme süresi boyunca sunucular açık
    - 443 bağlantı noktalarında giden bağlantılara izin verilir
    - Azure geçişi 'nde SQL aracısının Azure geçişi bağlantı durumu ' bağlı ' ise, son sinyalin olup olmadığını kontrol edin 
    - Bulunan SQL örneği dikey penceresinde tüm SQL örnekleri için Azure Geçişi bağlantı durumunun “Bağlandı” olup olmadığı

    Güvenilirlik derecelendirmesindeki en son değişiklikleri yansıtacak şekilde değerlendirmeyi 'Yeniden Hesaplayın'.
- Değerlendirmenin hesaplanışında bazı veritabanları veya örnekler oluşturulmuştur. Örneğin, geçen ayın performans geçmişi için bir değerlendirme oluşturduğunuzu varsayalım, ancak bazı veritabanları veya örnekler yalnızca bir hafta önce oluşturulmuştur. Bu durumda, yeni sunucular için performans verileri sürenin tamamına uygun olmayacaktır ve güvenirlik derecelendirmesi düşük olacaktır.

> [!NOTE]
> Azure SQL değerlendirmeleri, performans tabanlı değerlendirmelere sahip olduğu için, herhangi bir değerlendirmenin güvenilirlik derecelendirmesi beş yıldızlı daha azsa, gerecin ortamı profili oluşturup daha sonra değerlendirmeyi yeniden hesaplaması için en az bir gün beklemeniz önerilir. Aksi takdirde, performans tabanlı boyutlandırma güvenilir olmayabilir.

## <a name="calculate-monthly-costs"></a>Aylık maliyetleri hesapla
Boyutlandırma önerileri tamamlandıktan sonra Azure SQL değerlendirmesi, bir iç fiyatlandırma API 'SI kullanarak önerilen Azure SQL yapılandırmalarının işlem ve depolama maliyetlerini hesaplar. Toplam aylık işlem maliyetini hesaplamak için tüm örneklerde işlem ve depolama maliyetini toplar. 
### <a name="compute-cost"></a>İşlem maliyeti
- Azure SQL yapılandırması için işlem maliyetini hesaplamak üzere değerlendirme aşağıdaki özellikleri dikkate alır:
    - SQL lisansları için Azure Hibrit Avantajı
    - Ayrılmış kapasite
    - Azure hedef konumu
    - Para Birimi
    - Teklif/lisanslama programı
    - İndirim (%)

### <a name="storage-cost"></a>Depolama maliyeti
- Depolama maliyeti tahminleri yalnızca veri dosyalarını içerir ve günlük dosyalarını içermez. 
- Azure SQL yapılandırmasına yönelik depolama maliyetini hesaplamak için değerlendirme aşağıdaki özellikleri dikkate alır:
    - Azure hedef konumu
    - Para Birimi
    - Teklif/lisanslama programı
    - İndirim (%)
- Yedekleme depolama maliyeti değerlendirmeye dahil değildir.
- **Azure SQL Veritabanı**
    - Maliyet tahminine minimum 5GB depolama maliyeti eklenir ve 1 GB 'lik artışlarla depolama için ek depolama maliyeti eklenir. [Daha Fazla Bilgi](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Azure SQL Yönetilen Örnek**
    - İlk 32 GB/örnek/ay depolaması için bir depolama maliyeti eklenmez ve daha fazla depolama maliyeti, 32 artışlarla depolama için eklenir. [Daha Fazla Bilgi](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>Sonraki adımlar
- Değerlendirme oluşturmak için en iyi uygulamaları [gözden geçirin](best-practices-assessment.md) . 
- [Azure SQL değerlendirmesi](how-to-create-azure-sql-assessment.md)çalıştırma hakkında bilgi edinin.