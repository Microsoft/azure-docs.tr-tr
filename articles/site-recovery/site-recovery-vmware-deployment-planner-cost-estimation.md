---
title: Azure Site Recovery Dağıtım Planlayıcısı maliyet tahminleri gözden geçirin
description: Bu makalelerde, VMware olağanüstü durum kurtarma için Azure Site Recovery Dağıtım Planlayıcısı maliyet tahminleri gözden geçirilmesi açıklanmaktadır.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/29/2019
ms.author: mayg
ms.openlocfilehash: 27678fff1c0322f9755e7726026c73934810d5d6
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953337"
---
# <a name="review-cost-estimations-in-the-vmware-deployment-planner"></a>VMware Dağıtım Planlayıcısı maliyet tahminleri gözden geçirin 

Dağıtım planlayıcısı raporu, [Öneriler](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) sayfalarında maliyet tahmini özeti ve Maliyet Tahmini sayfasında da ayrıntılı maliyet analizi sağlar. Sanal makine başına ayrıntılı maliyet analizi içerir. 

>[!Note]
>Dağıtım planlayıcısı aracı v 2.5 ' nin geçerli sürümü, yönetilen disklere çoğaltılan VM 'Ler için maliyet tahmini sağlar.

### <a name="cost-estimation-summary"></a>Maliyet tahmini özeti 
Grafta, seçtiğiniz hedef bölgede ve rapor oluşturma için belirttiğiniz para biriminde Azure'a tahmini toplam olağanüstü durum kurtarma (DR) maliyetinin özet görünümü gösterilir.
Maliyet tahmini özeti

![Maliyet tahmini özeti](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Bu özet Azure Site Recovery kullanarak tüm uyumlu sanal makinelerinizi Azure'da koruduğunuzda ödemeniz gereken depolama, bilgi işlem, ağ ve lisansın maliyetini anlamanıza yardımcı olur. Maliyet, tüm profili oluşturulan sanal makineler için değil uyumlu sanal makineler için hesaplanır.  
 
Aylık veya yıllık maliyeti görüntüleyebilirsiniz. [Desteklenen hedef bölgeler](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) ve [desteklenen para birimleri](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies) hakkında daha fazla bilgi edinin.

**Bileşenlere göre maliyet** Toplam DR dört bileşene bölünür: İşlem, Depolama, Ağ ve Azure Site Recovery lisansı maliyeti. Maliyet, şirket içi siteyle Azure arasında ve yapılandırılan işlem, depolama (premium ve standart) ExpressRoute/VPN ve Azure Site Recovery lisansı için çoğaltma sırasında ve DR tatbikatında tahakkuk ettirilecek tüketim temelinde hesaplanır.

**Durumlara göre maliyet** Toplam olağanüstü durum kurtarma (DR) maliyeti, iki farklı duruma göre kategorilere ayrılır: Çoğaltma ve DR tatbikatı. 

**Çoğaltma maliyeti**: Çoğaltma sırasında tahakkuk ettirilen maliyet. Depolama, ağ ve Azure Site Recovery lisansı maliyetini kapsar. 

**DR Tatbikatı maliyeti**: Yük devretme testi sırasında tahakkuk ettirilen maliyet. Azure Site Recovery, yük devretme testi sırasında sanal makineleri çalıştırır. DR tatbikatı maliyeti, çalıştırılan sanal makinelerin işlem ve depolama maliyetini kapsar. 

**Ay/Yıl başına Azure depolama maliyeti** Çoğaltma ve DR tatbikatının premium ve standart depolaması için tahakkuk ettirilecek toplam depolama maliyetini gösterir.

## <a name="detailed-cost-analysis"></a>Ayrıntılı maliyet analizi
Azure'un işlem, depolama, ağ, vb. fiyatları Azure bölgeleri arasında değişiklik gösterir. Aboneliğinize göre Azure'un son fiyatlarına, aboneliğinizle ilişkili teklife ve belirtilen hedef Azure bölgenizle belirtilen para birimine göre maliyet tahmini raporunu oluşturabilirsiniz. Varsayılan olarak araç, Batı ABD 2 Azure bölgesini ve ABD Doları (USD) para birimini kullanır. Başka bir bölge ve para birimi kullandıysanız, raporu abonelik kimliği, teklif kimliği, hedef bölge ve para birimi belirtmeden bir sonraki oluşturmanızda, maliyet tahmini için son kullanılan hedef bölgeyi ve son kullanılan para birimini kullanır.
Bu bölümde, rapor oluştururken kullandığınız abonelik kimliği ve teklif kimliği gösterilir.  Kullanılmadıysa, boş kalır.

Raporun tamamında, gri renkle işaretlenmiş hücreler salt okunurdur. Beyaz hücreler, gereksinimlerinize göre değiştirilebilir.

![Maliyet tahmini ayrıntıları1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>Bileşenlere göre genel DR maliyeti
İlk bölümde bileşenlere göre genel DR maliyeti ve durumlara göre DR maliyeti gösterilir. 

**İşlem**: DR gereksinimleri için Azure'da çalıştırılan IaaS sanal makinelerinin maliyeti. DR tatbikatları (yük devretme testleri) sırasında Azure Site Recovery tarafından oluşturulan sanal makineleri ve Always On Kullanılabilirlik Grupları içeren SQL Server ve etki alanı denetleyicileri / Etki Alanı Adı Sunucuları gibi Azure üzerinde çalıştırılan sanal makineleri içerir.

**Depolama**: DR gereksinimleri için Azure depolama alanı tüketiminin maliyeti. Çoğaltma ve DR tatbikatları sırasındaki depolama alanı tüketimini içerir.
Ağ: DR gereksinimleri için ExpressRoute ve Siteden Siteye VPN maliyeti. 

**ASR lisansı**: Tüm uyumlu sanal makineler için Azure Site Recovery lisans maliyeti. Ayrıntılı maliyet analizi tablosuna bir sanal makineyi el ile girdiyseniz, o sanal makine için de Azure Site Recovery lisans maliyeti eklenir.

### <a name="overall-dr-cost-by-states"></a>Durumlara göre genel DR maliyeti
Toplam DR maliyeti, iki farklı duruma göre kategorilere ayrılır: çoğaltma ve DR Tatbikatı.

**Çoğaltma maliyeti**: Çoğaltma sırasında tahakkuk ettirilen maliyet. Depolama, ağ ve Azure Site Recovery lisansı maliyetini kapsar. 

**DR Tatbikatı maliyeti**: DR tatbikatları sırasında tahakkuk ettirilen maliyet. Azure Site Recovery, DR tatbikatları sırasında sanal makineleri çalıştırır. DR tatbikatı maliyeti çalıştırılan sanal makinelerin işlem ve depolama maliyetini kapsar.
Bir yıl süresince toplam DR tatbikatı = DR tatbikatlarının sayısı x Her DR tatbikatının süresi (gün) Ortalama DR tatbikatı maliyeti (aylık) = Toplam DR tatbikatı maliyeti / 12

### <a name="storage-cost-table"></a>Depolama maliyeti tablosu:
Bu tabloda, çoğaltma ve DR tatbikatları için indirimli ve indirimsiz premium ve standart depolama maliyeti gösterilir.

### <a name="site-to-azure-network"></a>Azure'da site ağı
Gereksinimlerinize göre uygun ayarı seçin. 

**ExpressRoute**: Varsayılan olarak araç, değişiklik çoğaltması için gereken ağ bant genişliğiyle eşleyen en yakın ExpressRoute planını seçer. Gereksinimlerinize göre planı değiştirebilirsiniz.

**VPN Ağ Geçidi**: Ortamınızda varsa, VPN Ağ Geçidi'ni seçin. Varsayılan olarak, Yok değeri gösterilir.

**Hedef Bölge**: DR için belirtilen Azure bölgesi. Raporda işlem, depolama, ağ ve lisans için kullanılan fiyat, söz konusu bölgeye ilişkin Azure fiyatına bağlıdır. 

### <a name="vm-running-on-azure"></a>Azure üzerinde çalıştırılan sanal makine
DR için Azure üzerinde çalıştırılan etki alanı denetleyiciniz, DNS sanal makineniz veya Always On Kullanılabilirlik Grupları içeren SQL Server sanal makineniz varsa, toplam DR maliyetinde işlem maliyetlerinin dikkate alınması için sanal makinelerin sayısını sağlayabilirsiniz. 

### <a name="apply-overall-discount-if-applicable"></a>Varsa genel indirimi uygula
Azure iş ortağı veya müşterisiyseniz ve genel Azure fiyatlarında herhangi bir indirime hak kazandıysanız, bu alanı kullanabilirsiniz. Araç, tüm bileşenlere indirimi (% cinsinden) uygular.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Sanal makine türü sayısı ve işlem maliyeti (yıllık)
Bu tabloda Windows ve Windows dışı sanal makinelerin sayısı ile bunların DR tatbikatı işlem maliyeti gösterilir.

### <a name="settings"></a>Ayarlar 

**Para birimi**: Rapor oluşturulurken kullanılan para birimi. Maliyet süresi: Aya veya yılın tamamına denk gelen tüm maliyetleri görüntüleyebilirsiniz. 

## <a name="detailed-cost-analysis-table"></a>Ayrıntılı maliyet analizi tablosu
![Ayrıntılı maliyet analizi](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png) Tabloda, uyumlu her sanal makinenin maliyet dağılımı listelenir. Ayrıca bu tabloyu kullanıp sanal makineleri el ile ekleyerek profili oluşturulmamış sanal makineler için tahmini Azure DR maliyetini de alabilirsiniz. Ayrıntılı profil oluşturma işlemi yapılmamış yeni olağanüstü durum kurtarma dağıtımları için Azure maliyetlerini hesaplamanız gerektiğinde yararlı olur.
Sanal makineleri el ile eklemek için: 
1.  Başlangıç ve Bitiş satırları arasına yeni satır eklemek için 'Satır ekle' düğmesine tıklayın.

2.  Bu yapılandırmayla eşleşen yaklaşık sanal makine boyutu ve sanal makinelerin sayısı temelinde aşağıdaki sütunları doldurun: 

* VM sayısı, IaaS boyutu (Sizin seçiminiz)
* Depolama Türü (Standart/Premium)
* Kaynak makinenin VM toplam depolama boyutu (GB)
* Yıllık DR tatbikatları sayısı 
* Her DR tatbikatının süresi (gün) 
* İşletim Sistemi Türü
* Veri yedekliği 
* Azure Hibrit Avantajı

1. Tablodaki tüm sanal makinelere Yıllık DR Tatbikatları Sayısı, Her DR Tatbikatının süresi (Gün), Veri yedekliği ve Azure Hibrit Kullanım Teklifi olarak aynı değeri uygulamak için 'Tümüne uygula' düğmesine tıklayabilirsiniz.

1. Maliyeti güncelleştirmek için 'Maliyeti yeniden hesapla' düğmesine tıklayın.

**VM Adı**: Sanal makinenin adı.

**VM Sayısı**: Yapılandırmayla eşleşen sanal makine sayısı. Benzer yapılandırmadaki sanal makinelerin profili oluşturulmadıysa ancak bunlar korunacaksa, mevcut sanal makinelerin sayısını güncelleştirebilirsiniz.

**IaaS boyutu (Öneri)** : Uyumlu sanal makinenin araç tarafından önerilen sanal makine rolü boyutudur. 

**IaaS boyutu (Sizin seçiminiz)** : Varsayılan olarak, önerilen VM rolü boyutuyla aynıdır. İhtiyacınıza göre rolü değiştirebilirsiniz. İşlem maliyetinde seçtiğiniz sanal makine rolü boyutu temel alınır.

**Depolama türü**: Sanal makine tarafından kullanılan depolamanın türü. Bu, standart veya premium depolamadır.

**VM toplam depolama alanı boyutu (GB)** : kaynak VM 'nin toplam depolaması.

**Yıllık DR Tatbikatları sayısı**: Bir yılda gerçekleştirdiğiniz DR Tatbikatlarının sayısı. Varsayılan olarak, yılda 4 kez gerçekleştirilir. Belirli sanal makineler için süreyi değiştirebilir veya en üst satıra yeni bir değer girip 'Tümüne uygula' düğmesine tıklayarak yeni değerin tüm sanal makinelere uygulanmasını sağlayabilirsiniz. Yıllık DR Tatbikatları sayısı ve her DR Tatbikatının süresi temelinde, toplam DR Tatbikatı maliyeti hesaplanır.  

**Her DR Tatbikatının süresi (Gün)** : Her DR Tatbikatının süresi. Varsayılan olarak, [Disaster Recovery Yazılım Güvencesi avantajına](https://azure.microsoft.com/pricing/details/site-recovery) göre her 90 günde bir 7 gündür. Belirli sanal makineler için süreyi değiştirebilir veya en üst satıra yeni bir değer girip 'Tümüne uygula' düğmesine tıklayarak, yeni değerin tüm sanal makinelere uygulanmasını sağlayabilirsiniz. Toplam DR Tatbikatı maliyeti, yıllık DR Tatbikatlarının sayısıyla her DR Tatbikatının süresi temel alınarak hesaplanır.
  
**İşletim sistemi türü**: Sanal makinenin işletim sistemi türü. Windows veya Linux'tır. İşletim sistemi türü Windows olduğunda, o sanal makineye Azure Hibrit Kullanım Teklifi uygulanabilir. 

**Veri yedekliği**: Şunlardan biri olabilir: Yerel olarak yedekli depolama (LRS), Coğrafi olarak yedekli depolama (GRS) veya Okuma erişimli coğrafi olarak yedekli depolama (RA-GRS). Varsayılan değer LRS'dir. Belirli sanal makineler için depolama hesabınız temelinde türü değiştirebilir veya en üst satırdaki türü değiştirip 'Tümüne uygula' düğmesine tıklayarak yeni türün tüm sanal makinelere uygulanmasını sağlayabilirsiniz.  Çoğaltmanın depolama maliyeti, seçtiğiniz veri yedekliğinin fiyatı temel alınarak hesaplanır. 

**Azure Hibrit Avantajı**: Uygunsa, Windows sanal makinelerine Azure Hibrit Avantajı’nı uygulayabilirsiniz.  Varsayılan değer Evet’tir. Belirli sanal makineler için ayarı değiştirebilir veya 'Tümüne uygula' düğmesine tıklayarak tüm sanal makineleri güncelleştirebilirsiniz.

**Toplam Azure tüketimi**: Bu, DR'niz için işlem, depolama ve Azure Site Recovery lisans maliyetini içerir. Yaptığınız seçime göre, aylık veya yıllık maliyeti gösterir.

**Durağan depolama maliyeti**: Çoğaltmanın depolama maliyetini içerir.

**Toplam DR Tatbikatı maliyeti (ortalama)** : DR Tatbikatının işlem ve depolama maliyetini içerir.

**ASR lisans maliyeti**: Azure Site Recovery lisansının maliyeti.

## <a name="supported-target-regions"></a>Desteklenen hedef bölgeler
Azure Site Recovery dağıtım planlayıcısı aşağıdaki Azure bölgeleri için maliyet tahmini sağlar. Bölgeniz aşağıda listelenmiyorsa, fiyatlandırması sizin bölgenize yakın olan aşağıdaki bölgelerden birini kullanabilirsiniz.

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Desteklenen para birimleri
Azure Site Recovery Dağıtım Planlayıcısı aşağıdaki para birimlerinin tümünde maliyet raporu oluşturabilir.

|Currency|Ad||Currency|Ad||Currency|Ad|
|---|---|---|---|---|---|---|---|
|ARS|Arjantin Pesosu ($)||AUD|Avustralya Doları ($)||BRL|Brezilya Reali (R$)|
|CAD|Kanada Doları ($)||CHF|İsviçre Frangı (chf)||DKK|Danimarka Kronu (kr)|
|EUR|Euro (€)||GBP|İngiliz Sterlini (£)||HKD|Hong Kong Doları (HK$)|
|IDR|Endonezya Rupisi (Rp)||INR|Hindistan Rupisi (₹)||JPY|Japon Yeni (¥)|
|KRW|Kore Wonu (₩)||MXN|Meksika Pesosu (MX$)||MYR|Malezya Ringgiti (RM$)|
|NOK|Norveç Kronu (kr)||NZD|Yeni Zelanda Doları ($)||RUB|Rus Rublesi (руб)|
|SAR|Suudi Riyali (SR)||SEK|İsveç Kronu (kr)||TWD|Tayvan Doları (NT$)|
|TRY|Türk Lirası (TL)||USD| ABD Doları ($)||ZAR|Güney Afrika Randı (R)|

## <a name="next-steps"></a>Sonraki adımlar
[Azure Site Recovery kullanarak VMware VM'lerden Azure'a dağıtımı](https://docs.microsoft.com/azure/site-recovery/tutorial-vmware-to-azure) koruma hakkında daha fazla bilgi edinin.
