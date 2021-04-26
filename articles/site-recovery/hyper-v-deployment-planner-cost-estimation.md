---
title: Hyper-V VM 'lerinin Azure 'a olağanüstü durum kurtarması için Azure Site Recovery Dağıtım Planlayıcısı maliyet tahmini raporunu gözden geçirin | Microsoft Docs
description: Bu makalede, Azure 'da Hyper-V olağanüstü durum kurtarma için Azure Site Recovery Dağıtım Planlayıcısı üretilen maliyet tahmini raporunun nasıl gözden geçirkullanılacağı açıklanmaktadır.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8e5a1f727f32fda7740b958cb3f03a3a4a3ae03f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727561"
---
# <a name="cost-estimation-report-by-azure-site-recovery-deployment-planner"></a>Azure Site Recovery Dağıtım Planlayıcısı maliyet tahmini raporu 

Azure Site Recovery Dağıtım Planlayıcısı raporu, [Öneriler](hyper-v-deployment-planner-analyze-report.md#recommendations) sayfalarında maliyet tahmini özeti ve Maliyet Tahmini sayfasında da ayrıntılı maliyet analizi sağlar. Sanal makine başına ayrıntılı maliyet analizi içerir. 

### <a name="cost-estimation-summary"></a>Maliyet tahmini özeti 
Grafta, seçtiğiniz hedef bölgede ve rapor oluşturma için belirttiğiniz para biriminde Azure’a tahmini toplam olağanüstü durum kurtarma (DR) maliyetinin özet görünümü gösterilir.

![Maliyet tahmini özeti](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation-summary-h2a.png)

Bu özet Azure Site Recovery kullanarak tüm uyumlu sanal makinelerinizi koruduğunuzda ödemeniz gereken depolama, bilgi işlem, ağ ve lisansın maliyetini anlamanıza yardımcı olur. Maliyet, tüm profili oluşturulan sanal makineler için değil uyumlu sanal makineler için hesaplanır. 
 
Aylık veya yıllık maliyeti görüntüleyebilirsiniz. [Desteklenen hedef bölgeler](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) ve [desteklenen para birimleri](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies) hakkında daha fazla bilgi edinin.

**Bileşenlere göre maliyet**: Toplam DR maliyeti dört bileşene bölünür: bilgi işlem, depolama, ağ ve Site Recovery lisansı maliyeti. Maliyet, çoğaltma sırasında ve DR tatbikatı anında oluşan tüketime dayalı olarak hesaplanır. Hesaplamalar için bilgi işlem, depolama (premium ve standart), şirket içi site ve Azure arasında yapılandırılan ExpressRoute/VPN ve Site Recovery lisansı kullanılır.

**Durumlara göre maliyet** Toplam olağanüstü durum kurtarma (DR) maliyeti, iki farklı duruma göre kategorilere ayrılır: Çoğaltma ve DR tatbikatı. 

**Çoğaltma maliyeti**: Çoğaltma sırasında tahakkuk ettirilen maliyet. Depolama, ağ ve Site Recovery lisansı maliyetini kapsar. 

**DR Tatbikatı maliyeti**: Yük devretme testi sırasında tahakkuk ettirilen maliyet. Site Recovery, yük devretme testi sırasında sanal makineleri çalıştırır. DR tatbikatı maliyeti, çalıştırılan sanal makinelerin işlem ve depolama maliyetlerini kapsar. 

**Ay/Yıl başına Azure Depolama Maliyeti**: Çoğaltma ve DR tatbikatının premium ve standart depolaması için tahakkuk ettirilen toplam depolama maliyeti.

## <a name="detailed-cost-analysis"></a>Ayrıntılı maliyet analizi
Azure'un işlem, depolama ve ağ fiyatları Azure bölgeleri arasında değişiklik gösterir. Aboneliğinize, aboneliğinizle ilişkili teklife ve belirtilen hedef Azure bölgenizle belirtilen para birimine göre Azure'un son fiyatları ile maliyet tahmini raporunu oluşturabilirsiniz. Varsayılan olarak araç, Batı ABD 2 Azure bölgesini ve ABD Doları (USD) para birimini kullanır. Başka bir bölge ve para birimi kullanırsanız, raporu abonelik kimliği, teklif kimliği, hedef bölge ve para birimi belirtmeden bir sonraki oluşturmanızda, araç maliyet tahmini için son kullanılan hedef bölgeyi ve para birimini kullanır.

Bu bölümde, rapor oluştururken kullandığınız abonelik kimliği ve teklif kimliği gösterilir. Kullanılmadıysa, boş bırakılır.

Raporun tamamında, gri renkle işaretlenmiş hücreler salt okunurdur. Beyaz hücreler, gereksinimlerinize göre değiştirilebilir.

![Maliyet tahmini ayrıntıları](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-costs-by-components"></a>Bileşenlere göre genel DR maliyetleri
İlk bölümde bileşenlere göre genel DR maliyeti ve durumlara göre DR maliyeti gösterilir. 

**İşlem**: DR gereksinimleri için Azure'da çalıştırılan IaaS sanal makinelerinin maliyeti. DR tatbikatları (yük devretme testleri) sırasında Site Recovery tarafından oluşturulan VM’leri içerir. Ayrıca AlwaysOn kullanılabilirlik grupları ile SQL Server ve etki alanı denetleyicileri veya etki alanı ad sunucuları gibi Azure üzerinde çalışan VM’leri içerir.

**Depolama**: DR gereksinimleri için Azure depolama alanı tüketiminin maliyeti. Çoğaltma ve DR tatbikatları sırasındaki depolama alanı tüketimini içerir.

**Ağ**: DR gereksinimleri için ExpressRoute ve siteden siteye VPN maliyeti. 

**Azure Site Recovery lisansı**: tüm uyumlu sanal makineler için Site Recovery lisans maliyeti. Ayrıntılı maliyet analizi tablosuna bir sanal makineyi el ile girdiyseniz, o sanal makine için de Site Recovery lisans maliyeti eklenir.

### <a name="overall-dr-costs-by-states"></a>Durumlara göre genel DR maliyetleri
Toplam DR maliyetleri, iki farklı duruma göre kategorilere ayrılır: Çoğaltma ve DR tatbikatı.

**Çoğaltma**: Çoğaltma sırasında tahakkuk ettirilen maliyet. Depolama, ağ ve Site Recovery lisansı maliyetini kapsar. 

**DR Tatbikatı**: DR tatbikatları sırasında tahakkuk ettirilen maliyet. Site Recovery, DR tatbikatları sırasında sanal makineleri çalıştırır. DR tatbikatı maliyeti çalıştırılan sanal makinelerin işlem ve depolama maliyetini kapsar.

* Bir yıl süresince toplam DR tatbikatı = DR tatbikatlarının sayısı x Her DR tatbikatının süresi (gün)
* Ortalama DR tatbikatı maliyeti (aylık) = Toplam DR tatbikatı maliyeti / 12

### <a name="storage-cost-table"></a>Depolama maliyeti tablosu
Bu tabloda, çoğaltma ve DR tatbikatları için indirimli ve indirimsiz premium ve standart depolama maliyetleri gösterilir.

### <a name="site-to-azure-network"></a>Azure'da site ağı
Gereksinimlerinize göre uygun ayarı seçin. 

**ExpressRoute**: Varsayılan olarak araç, değişiklik çoğaltması için gereken ağ bant genişliğiyle eşleyen en yakın ExpressRoute planını seçer. Gereksinimlerinize göre planı değiştirebilirsiniz.

**VPN Ağ Geçidi türü**: Ortamınızda varsa, Azure VPN Gateway'i seçin. Varsayılan olarak, Yok değeri gösterilir.

**Hedef bölge**: DR için belirtilen Azure bölgesi. Raporda işlem, depolama, ağ ve lisans için kullanılan fiyat, söz konusu bölgeye ilişkin Azure fiyatına bağlıdır. 

### <a name="vm-running-on-azure"></a>Azure üzerinde çalıştırılan sanal makine
DR için Azure üzerinde çalışan bir etki alanı denetleyiciniz veya DNS VM’niz ya da AlwaysOn kullanılabilirlik grupları ile SQL Server VM’niz olabilir. Toplam DR maliyetinde bunların işlem maliyetini değerlendirmek için VM sayısı ve boyutunu belirtebilirsiniz. 

### <a name="apply-overall-discount-if-applicable"></a>Varsa genel indirimi uygula
Azure iş ortağı veya müşterisiyseniz ve genel Azure fiyatlarında herhangi bir indirime hak kazandıysanız, bu alanı kullanabilirsiniz. Araç, tüm bileşenlere indirimi (yüzde cinsinden) uygular.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Sanal makine türü sayısı ve işlem maliyeti (yıllık)
Bu tabloda Windows ve Windows dışı sanal makinelerin sayısı ile bunların DR tatbikatı işlem maliyeti gösterilir.

### <a name="settings"></a>Ayarlar 
**Yönetilen disk kullanarak**: Bu ayar DR tatbikatları sırasında bir yönetilen diskin kullanılıp kullanılmadığını belirtir. Varsayılan değer **Evet**’tir. **-UseManagedDisks** değerini **Hayır** olarak ayarlarsanız, maliyet hesaplamasında yönetilmeyen disk fiyatı kullanılır.

**Para birimi**: Rapor oluşturulurken kullanılan para birimi.

**Maliyet süresi**: Aya veya yılın tamamına denk gelen tüm maliyetleri görüntüleyebilirsiniz. 

## <a name="detailed-cost-analysis-table"></a>Ayrıntılı maliyet analizi tablosu
![Ayrıntılı maliyet analizi](media/hyper-v-azure-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png)

Tabloda, uyumlu her sanal makinenin maliyet dağılımı listelenir. Ayrıca bu tabloyu kullanıp sanal makineleri el ile ekleyerek profili oluşturulmamış sanal makineler için tahmini Azure DR maliyetini de alabilirsiniz. Bu bilgiler ayrıntılı profil oluşturma işlemi yapılmamış yeni DR dağıtımları için Azure maliyetlerini hesaplamanız gerektiğinde yararlı olur.

Sanal makineleri el ile eklemek için:

1. **Başlangıç** ve **Bitiş** satırları arasına yeni satır eklemek için **Satır ekle** öğesini seçin.

1. Bu yapılandırmayla eşleşen yaklaşık sanal makine boyutu ve sanal makinelerin sayısı temelinde aşağıdaki sütunları doldurun: 

    a. **VM sayısı**

    b. **IaaS boyutu (Sizin seçiminiz)**

    c. **Depolama türü Standart/Premium**

    d. **VM toplam depolama alanı boyutu (GB)**

    e. **Yıllık DR tatbikatları sayısı**

    f. **Her DR tatbikatının süresi (gün)**

    örneğin: **İşletim Sistemi Türü**

    h. **Veri yedekliği**

    i. **Azure Hibrit Kullanım Teklifi**

1. Tablodaki tüm sanal makinelere **Yıllık DR Tatbikatları sayısı**, **Her DR Tatbikatının süresi (Gün)**, **Veri yedekliği** ve **Azure Hibrit Kullanım Teklifi** olarak aynı değeri uygulamak için **Tümüne uygula** öğesini seçebilirsiniz.

1. Maliyeti güncelleştirmek için **Maliyeti yeniden hesapla** öğesini seçin.

**VM Adı**: Sanal makinenin adı.

**VM Sayısı**: Yapılandırmayla eşleşen sanal makine sayısı. Benzer yapılandırmadaki sanal makinelerin profili oluşturulmadıysa ancak bunlar korunuyorsa, mevcut sanal makinelerin sayısını güncelleştirebilirsiniz.

**IaaS boyutu (Öneri)**: Uyumlu sanal makinenin araç tarafından önerilen sanal makine rolü boyutu. 

**IaaS boyutu (Sizin seçiminiz)**: Varsayılan olarak, önerilen VM rolü boyutuyla aynıdır. İhtiyacınıza göre rolü değiştirebilirsiniz. İşlem maliyetinde seçtiğiniz sanal makine rolü boyutu temel alınır.

**Depolama türü**: Sanal makine tarafından kullanılan depolamanın türü. Bu, standart veya premium depolamadır.

**VM toplam depolama alanı boyutu (GB)**: Sanal makinenin toplam depolama alanı.

**Yıllık DR Tatbikatları sayısı**: Bir yılda gerçekleştirdiğiniz DR tatbikatlarının sayısı. Varsayılan olarak, yılda dört kez gerçekleştirilir. Süreyi belirli VM’ler için değiştirebilir veya yeni değeri tüm VM’lere uygulayabilirsiniz. Üst satıra yeni değeri girip **Tümüne uygula** öğesini seçin. Yıllık DR tatbikatları sayısı ve her DR tatbikatının süresi temelinde, toplam DR tatbikatı maliyeti hesaplanır. 

**Her DR Tatbikatının süresi (Gün)**: Her DR tatbikatının süresi. Varsayılan olarak, [Disaster Recovery Yazılım Güvencesi avantajına](https://azure.microsoft.com/pricing/details/site-recovery) göre her 90 günde bir 7 gündür. Süreyi belirli VM’ler için değiştirebilir veya yeni değeri tüm VM’lere uygulayabilirsiniz. Üst satıra yeni bir değer girip **Tümüne uygula** öğesini seçin. Toplam DR tatbikatı maliyeti, yıllık DR tatbikatlarının sayısıyla her DR tatbikatının süresi temel alınarak hesaplanır.
 
**İşletim Sistemi Türü**: VM’nin işletim sistemi (OS) türü. Windows veya Linux'tır. İşletim sistemi türü Windows olduğunda, o sanal makineye Azure Hibrit Kullanım Teklifi uygulanabilir. 

**Veri yedekliği**: Yerel olarak yedekli depolama, coğrafi olarak yedekli depolama veya okuma erişimli coğrafi olarak yedekli depolama olabilir. Varsayılan değer yerel olarak yedekli depolamadır. Belirli sanal makineler için depolama hesabınız temelinde türü değiştirebilir veya yeni türü tüm sanal makinelere uygulayabilirsiniz. Üst satırın türünü değiştirip **Tümüne uygula** öğesini seçin. Çoğaltmanın depolama maliyeti, seçtiğiniz veri yedekliğinin fiyatı temel alınarak hesaplanır. 

**Azure Hibrit Kullanım Teklifi**: Uygunsa, Windows sanal makinelerine Azure Hibrit Kullanım Teklifi'ni uygulayabilirsiniz. Varsayılan değer **Evet**’tir. Belirli VM’ler için ayarı değiştirebilir veya tüm VM’leri güncelleştirebilirsiniz. **Tümüne uygula**’yı seçin.

**Toplam Azure tüketimi**: DR'niz için işlem, depolama ve Site Recovery lisans maliyetini içerir. Yaptığınız seçime göre, aylık veya yıllık maliyeti gösterir.

**Durağan depolama maliyeti**: Çoğaltmanın depolama maliyeti.

**Toplam DR Tatbikatı maliyeti (ortalama)**: DR tatbikatlarının işlem ve depolama maliyeti.

**Azure Site Recovery lisans maliyeti**: Site Recovery lisans maliyeti.

## <a name="supported-target-regions"></a>Desteklenen hedef bölgeler
Site Recovery Dağıtım Planlayıcısı aşağıdaki Azure bölgeleri için maliyet tahmini sağlar. Bölgeniz burada listelenmiyorsa, fiyatlandırması sizin bölgenize yakın olan aşağıdaki bölgelerden birini kullanabilirsiniz:

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Desteklenen para birimleri
Site Recovery Dağıtım Planlayıcısı aşağıdaki para birimlerinin tümünde maliyet raporu oluşturabilir.

|Para Birimi|Name|Para Birimi|Name|Para Birimi|Name|
|---|---|---|---|---|---|
|ARS|Arjantin pesosu ($)|AUD|Avustralya doları ($)|BRL|Brezilya reali (R$)|
|CAD|Kanada doları ($)|CHF|İsviçre frangı (chf)|DKK|Danimarka kronu (kr)|
|EUR|Euro ( &euro; )|GBP|İngiliz Sterlini (£)|HKD|Hong Kong doları (HK$)|
|IDR|Endonezya Rupisi (Rp)|INR|Hint Rupisi (₹)|JPY|Japon yen (¥)|
|KRW|Kore Wonu (₩)|MXN|Meksika pesosu (MX$)|MYR|Malezya ringgiti (RM$)|
|NOK|Norveç kronu (kr)|NZD|Yeni Zelanda doları ($)|RUB|Rusça Rublesi (руб)|
|SAR|Suudi riyali (SR)|SEK|İsveç kronu (kr)|TWD|Tayvan doları (NT$)|
|TRY|Türk lirası (TL)|USD| ABD doları ($)|ZAR|Güney Afrika randı (R)|

## <a name="next-steps"></a>Sonraki adımlar
[Site Recovery kullanarak Hyper-V VM'lerden Azure'a dağıtımı](hyper-v-azure-tutorial.md) koruma hakkında daha fazla bilgi edinin.
