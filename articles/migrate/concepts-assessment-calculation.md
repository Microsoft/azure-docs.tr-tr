---
title: Azure geçişi ile Azure VM değerlendirmeleri
description: Azure geçişi 'nde değerlendirmeler hakkında bilgi edinin
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: c8d625095fc979151ed904fb355b5953e41309b4
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078715"
---
# <a name="assessment-overview-migrate-to-azure-vms"></a>Değerlendirmeye genel bakış (Azure VM’lerine geçiş)

Bu makalede, [Azure geçişi: bulma ve değerlendirme](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) aracında değerlendirmelere genel bakış sunulmaktadır. Araç, VMware sanal ve Hyper-V ortamındaki şirket içi sunucuları ve Azure 'a geçiş için fiziksel sunucuları değerlendirebilirler.

## <a name="whats-an-assessment"></a>Değerlendirme nedir?

Bulma ve değerlendirme aracı ile bir değerlendirme, hazırlığı ölçer ve şirket içi sunucuları Azure 'a geçirme etkisini tahmin eder.

> [!NOTE]
> Azure Kamu 'da [desteklenen hedef](migrate-support-matrix.md#supported-geographies-azure-government) değerlendirme konumlarını gözden geçirin. Değerlendirmelere ait VM boyut önerilerinin, özellikle kamu bulut bölgeleri için VM serisini kullanabileceğini unutmayın. VM türleri hakkında [daha fazla bilgi edinin](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) .

## <a name="types-of-assessments"></a>Değerlendirme türleri

Azure geçişi: bulma ve değerlendirme kullanarak oluşturabileceğiniz üç tür değerlendirme vardır.

***Değerlendirme türü** | **Ayrıntılar**
--- | --- 
**Azure VM** | Şirket içi sunucularınızı Azure sanal makinelerine geçirmeye yönelik değerlendirmeler. Bu değerlendirme türünü kullanarak, [VMware](how-to-set-up-appliance-vmware.md) ve [Hyper-V](how-to-set-up-appliance-hyper-v.md) ortamındaki şirket Içi sunucularınızı ve Azure VM 'lerine geçiş için [fiziksel sunucuları](how-to-set-up-appliance-physical.md) değerlendirebilirsiniz.
**Azure SQL** | Şirket içi SQL Server 'larınızı VMware ortamınızdan Azure SQL veritabanı 'na veya Azure SQL yönetilen örneğine geçirmeye yönelik değerlendirmeler.
**Azure VMware Çözümü (AVS)** | Şirket içi sunucularınızı [Azure VMware Çözümü'ne (AVS)](../azure-vmware/introduction.md) geçirmeye yönelik değerlendirmeler. Bu değerlendirme türünü kullanarak Azure VMware çözümüne (AVS) geçiş için şirket içi [VMware VM](how-to-set-up-appliance-vmware.md) 'lerinizi değerlendirebilirsiniz. [Daha fazla bilgi edinin](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Bulma ve değerlendirme aracında Azure VM veya AVS değerlendirmesi sayısı yanlışsa, Tüm değerlendirmelere gitmek ve Azure VM veya AVS değerlendirmelerini yeniden hesaplamak için toplam değerlendirme sayısına tıklayın. Bulma ve değerlendirme aracı daha sonra bu değerlendirme türü için doğru sayıyı gösterir. 

Azure geçişi ile oluşturduğunuz değerlendirmeler, verilerin bir zaman noktası anlık görüntüsüdür. Azure VM değerlendirmesi iki boyutlandırma ölçütü seçeneği sağlar:

**Değerlendirme türü** | **Ayrıntılar** | **Veriler**
--- | --- | ---
**Performans tabanlı** | Toplanan performans verilerine göre öneriler sunan değerlendirmeler | VM boyutu önerisi, CPU ve RAM kullanımı verilerine göre belirlenir.<br/><br/> Disk türü önerisi, saniye başına giriş/çıkış işlemi (ıOPS) ve şirket içi disklerin aktarım hızını temel alır. Disk türleri Azure Standart HDD, Azure Standart SSD ve Azure Premium disklerdir.
**Şirket içinde olduğu gibi** | Öneriler oluşturmak için performans verilerini kullanmayan değerlendirmeler | VM boyutu önerisi, şirket içi sunucu boyutunu temel alır.<br/><br> Önerilen disk türü, değerlendirme için seçilen depolama türünü temel alır.

## <a name="how-do-i-run-an-assessment"></a>Nasıl yaparım? bir değerlendirme çalıştırmak istiyor musunuz?

Bir değerlendirme çalıştırmak için birkaç yol vardır.

- Hafif bir Azure geçişi gereci tarafından toplanan sunucu meta verilerini kullanarak sunucuları değerlendirin. Gereç, şirket içi sunucuları bulur. Daha sonra Azure geçişi 'ne sunucu meta verilerini ve performans verilerini gönderir.
- Bir virgülle ayrılmış değerler (CSV) biçiminde içeri aktarılan sunucu meta verilerini kullanarak sunucuları değerlendirin.

## <a name="how-do-i-assess-with-the-appliance"></a>Gereç ile Nasıl yaparım? değerlendirin?

Şirket içi sunucuları bulmaya yönelik bir Azure geçiş gereci dağıtıyorsanız, aşağıdaki adımları uygulayın:

1. Azure 'u ve şirket içi ortamınızı Azure geçişi ile çalışacak şekilde ayarlayın.
1. İlk değerlendirmenize, bir Azure projesi oluşturun ve bulma ve değerlendirme aracını buna ekleyin.
1. Hafif bir Azure geçişi gereci dağıtın. Gereç, şirket içi sunucuları sürekli bulur ve Azure geçişi 'ne sunucu meta verilerini ve performans verilerini gönderir. Gereci bir VM veya fiziksel sunucu olarak dağıtın. Değerlendirmek istediğiniz sunuculara herhangi bir şey yüklemeniz gerekmez.

Gereç sunucu bulmayı başlattıktan sonra, değerlendirmek istediğiniz sunucuları bir gruba toplayıp değerlendirme türü **Azure VM** olan grup için bir değerlendirme çalıştırabilirsiniz.

Bu adımları denemek için [VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md)veya [fiziksel sunucular](./tutorial-discover-physical.md) için öğreticilerimizi izleyin.

## <a name="how-do-i-assess-with-imported-data"></a>İçeri aktarılan verilerle Nasıl yaparım? değerlendirin?

Sunucuları bir CSV dosyası kullanarak değerlendiriyorsanız, bir gereç olması gerekmez. Bunun yerine, aşağıdaki adımları uygulayın:

1. Azure 'ı Azure geçişi ile çalışacak şekilde ayarlama
1. İlk değerlendirmenize, bir Azure projesi oluşturun ve bulma ve değerlendirme aracını buna ekleyin.
1. Bir CSV şablonu indirin ve buna sunucu verileri ekleyin.
1. Şablonu Azure geçişi 'ne aktarma
1. İçeri aktarma ile eklenen sunucuları bulun, bir gruba toplayın ve değerlendirme türü **Azure VM** olan grup için bir değerlendirme çalıştırın.

## <a name="what-data-does-the-appliance-collect"></a>Gereç hangi verileri toplar?

Değerlendirme için Azure geçişi gereci kullanıyorsanız, [VMware](migrate-appliance.md#collected-data---vmware) ve [Hyper-V](migrate-appliance.md#collected-data---hyper-v)için toplanan meta veriler ve performans verileri hakkında bilgi edinin.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Gereç performans verilerini nasıl hesaplar?

Bu gereci bulma işlemi için kullanıyorsanız, aşağıdaki adımlarla işlem ayarları için performans verilerini toplar:

1. Gereç gerçek zamanlı bir örnek noktası toplar.

    - **VMware VM 'leri**: her 20 saniyede bir örnek noktası toplanır.
    - **Hyper-V VM 'leri**: bir örnek nokta, her 30 saniyede bir toplanır.
    - **Fiziksel sunucular**: beş dakikada bir örnek nokta toplanır.

1. Gereç, VMware ve Hyper-V sunucuları için her 10 dakikada bir ve fiziksel sunucular için 5 dakikada bir tek bir veri noktası oluşturmak için örnek noktaları birleştirir. Veri noktasını oluşturmak için gereç tüm örneklerden tepe değerlerini seçer. Daha sonra veri noktasını Azure 'a gönderir.
1. Değerlendirme, geçen aya ait 10 dakikalık tüm veri noktalarını depolar.
1. Bir değerlendirme oluşturduğunuzda, değerlendirme için kullanılacak uygun veri noktasını tanımlar. Kimlik, *performans geçmişi* ve *yüzdebirlik kullanımı* için yüzdebirlik değerlerini temel alır.

    - Örneğin, performans geçmişi bir hafta ise ve yüzdebirlik kullanımı 95. yüzdebirlik ise, değerlendirme son hafta için 10 dakikalık örnek noktaları sıralar. Bunları artan düzende sıralar ve doğru bir şekilde yapmak için 95. yüzdebirlik değerini seçer.
    - 95. yüzdebirlik değeri, 99. yüzdebirlik değerini seçtiğiniz takdirde dahil olabilen tüm aykırı değerleri yok saydığınızdan emin olmanızı sağlar.
    - Dönemin en yoğun kullanımını seçmek ve tüm aykırı değerleri kaçırmak istemiyorsanız, yüzdebirlik kullanımı için 99. yüzdebirlik ' ü seçin.

1. Bu değer, gerecin topladığı bu ölçümler için etkili performans kullanım verilerini elde etmek için, rahatlık faktörüyle çarpılır:

    - CPU kullanımı
    - RAM kullanımı
    - Disk ıOPS (okuma ve yazma)
    - Disk aktarım hızı (okuma ve yazma)
    - Ağ aktarım hızı (ın ve out)

## <a name="how-are-azure-vm-assessments-calculated"></a>Azure VM değerlendirmeleri nasıl hesaplanır?

Değerlendirme, değerlendirmeleri hesaplamak için şirket içi sunucuların meta verilerini ve performans verilerini kullanır. Azure geçişi gerecini dağıtırsanız, değerlendirme gereç tarafından toplanan verileri kullanır. Ancak bir CSV dosyası kullanarak içeri aktarılan bir değerlendirmeyi çalıştırırsanız, hesaplama için meta verileri sağlarsınız.

Hesaplamalar şu üç aşamada gerçekleşir:

1. **Azure hazırlığını hesapla**: sunucuların Azure 'a geçiş için uygun olup olmadığını değerlendirin.
1. **Boyutlandırma önerilerini hesapla**: işlem, depolama ve ağ boyutlandırmayı tahmin etme.
1. **Aylık maliyetleri hesapla**: geçişten sonra Azure 'da sunucuları çalıştırmaya yönelik tahmini aylık işlem ve depolama maliyetlerini hesaplayın.

Hesaplamalar önceki sıradadır. Sunucu sunucusu, yalnızca öncekini geçerse daha sonraki bir aşamaya geçer. Örneğin, bir sunucu Azure hazırlık aşamasına geçemezse Azure için uygun değil olarak işaretlenir. Bu sunucu için boyutlandırma ve maliyet hesaplamaları yapılmaz.

## <a name="whats-in-an-azure-vm-assessment"></a>Azure VM değerlendirmesinde neler var?

Azure VM değerlendirmesine aşağıda verilmiştir:

**Özellik** | **Ayrıntılar**
--- | ---
**Hedef konum** | Geçirmek istediğiniz konum. Değerlendirme Şu anda bu hedef Azure bölgelerini destekliyor:<br/><br/> Avustralya Doğu, Avustralya Güneydoğu, Brezilya Güney, Kanada Orta, Kanada Doğu, Orta Hindistan, Orta ABD, Çin Doğu, Çin Kuzey, Doğu Asya, Doğu ABD, Doğu ABD 2, Almanya Orta, Almanya Kuzeydoğu, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney, Orta Kuzey ABD, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, Güney Hindistan, UK Güney, UK Batı, US gov Arizona, US Gov Teksas, US Gov Virginia , Orta Batı ABD, Batı Avrupa, Batı Hindistan, Batı ABD ve Batı ABD 2.
**Hedef depolama diski (örneğin, boyutlandırma)** | Azure 'da depolama için kullanılacak disk türü. <br/><br/> Hedef depolama diskini Premium tarafından yönetilen, Standart SSD yönetilen veya Standart HDD yönetilen olarak belirtin.
**Hedef depolama diski (performans tabanlı boyutlandırma)** | Hedef depolama diskinin türünü otomatik, Premium tarafından yönetilen, Standart HDD yönetilen veya Standart SSD yönetilen olarak belirtir.<br/><br/> **Otomatik**: disk önerisi, disklerin performans verilerine göre BELIRLENIR, IOPS ve aktarım hızı anlamına gelir.<br/><br/>**Premium veya standart**: değerlendirme, seçilen depolama türü içinde bir disk SKU 'su önerir.<br/><br/> % 99,9 için tek örnekli bir VM hizmet düzeyi sözleşmesi (SLA) istiyorsanız Premium ile yönetilen diskler kullanmayı düşünün. Bu kullanım, değerlendirmede tüm disklerin Premium yönetilen diskler olarak önerilmesini sağlar.<br/><br/> Azure geçişi, geçiş değerlendirmesi için yalnızca yönetilen diskleri destekler.
**Azure ayrılmış VM örnekleri** | Değerlendirmede maliyet tahminleri bunları hesaba alacak şekilde [ayrılmış örnekleri](https://azure.microsoft.com/pricing/reserved-vm-instances/) belirtir.<br/><br/> ' Ayrılmış örnekler ' seçtiğinizde, ' Discount (%) ' ve ' VM çalışma süresi ' özellikleri geçerli değil.<br/><br/> Azure geçişi Şu anda Azure ayrılmış VM örneklerini yalnızca Kullandıkça Öde teklifleri için desteklemektedir.
**Boyutlandırma ölçütü** | Azure VM 'yi farenin altına almak için kullanılır.<br/><br/> As, boyutlandırma veya performans tabanlı boyutlandırma kullanın.
**Performans geçmişi** | Performans tabanlı boyutlandırma ile kullanılır. Performans geçmişi performans verileri değerlendirilirken kullanılan süreyi belirtir.
**Yüzdebirlik kullanımı** | Performans tabanlı boyutlandırma ile kullanılır. Yüzdebirlik kullanımı, doğru hale getirmek için kullanılan performans örneğinin yüzdebirlik değerini belirtir.
**VM serisi** | Doğru hale getirmek için göz önünde bulundurulması istediğiniz Azure VM Serisi. Örneğin, Azure 'da bir serisi VM gerektiren bir üretim ortamınız yoksa, seri listesinden bir serisi dışarıda bırakabilirsiniz.
**Konfor katsayısı** | Değerlendirme sırasında kullanılan arabellek. VM 'Ler için CPU, RAM, disk ve ağ verilerine uygulanır. Dönemsel kullanım, kısa performans geçmişi ve gelecekteki kullanımlarda olası artışlar gibi sorunlar için BT hesapları.<br/><br/> Örneğin, %20 kullanımındaki bir 10 çekirdekli VM normalde iki çekirdekli bir VM ile sonuçlanır. 2,0, bir BT faktörü ile bunun yerine dört çekirdekli bir VM olur.
**Teklif** | Kayıtlı olduğunuz [Azure teklifi](https://azure.microsoft.com/support/legal/offer-details/) . Değerlendirme, bu teklifin maliyetini tahmin eder.
**Para Birimi** | Hesabınız için faturalandırma para birimi.
**İndirim (%)** | Azure teklifinin üzerine aldığınız, aboneliğe özgü tüm indirimler. Varsayılan ayar, %0’dır.
**VM çalışma süresi** | Sürekli olarak çalışmayan Azure VM 'Leri için her ay gün ve saat başına gün cinsinden süre. Maliyet tahminleri bu süreye göre hesaplanır.<br/><br/> Varsayılan değerler ayda 31 gün ve günde 24 saat değerlerdir.
**Azure Hibrit Avantajı** | Yazılım Güvencesi olup olmadığını ve [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-use-benefit/)uygun olduğunu belirtir. Ayarın varsayılan değeri "Evet" ise, Windows dışındaki işletim sistemleri için Azure fiyatları Windows VM 'Leri olarak kabul edilir.
**EA aboneliği** | Maliyet tahmini için bir Kurumsal Anlaşma (EA) aboneliğinin kullanıldığını belirtir. Abonelik için geçerli olan indirimi hesaba girer. <br/><br/> Ayrılmış örnekler için ayarları bırakın, indirim (%) ve VM çalışma süresi özellikleri varsayılan ayarlarıyla birlikte.


Azure geçişi ile bir değerlendirme oluşturmak için [en iyi uygulamaları gözden geçirin](best-practices-assessment.md) .

## <a name="calculate-readiness"></a>Hazırlığı hesapla

Tüm sunucular Azure 'da çalışmak üzere uygun değildir. Azure VM değerlendirmesi tüm şirket içi sunucuları değerlendirir ve onlara bir hazır olma kategorisi atar.

- **Azure Için hazırlanma**: sunucu, herhangi bir değişiklik yapılmadan Azure 'a olarak geçirilebilir. Azure 'da tam Azure desteğiyle başlatılır.
- **Azure Için koşullu olarak hazırlanıyor**: sunucu Azure 'da başlayabilir, ancak tam Azure desteği olmayabilir. Örneğin, Azure, Windows Server 'ın eski bir sürümünü çalıştıran bir sunucuyu desteklemez. Bu sunucuları Azure 'a geçirmeden önce dikkatli olmanız gerekir. Tüm hazırlık sorunlarını onarmak için değerlendirmenin önerdiği düzeltme kılavuzunu izleyin.
- **Azure için hazırlanma**: sunucu Azure 'da başlamıyor. Örneğin, bir şirket içi sunucunun diski 64 TB 'tan fazla mağaza içeriyorsa, Azure sunucuyu barındıramıyor. Geçişten önce sorunu gidermek için düzeltme kılavuzunu izleyin.
- **Hazır olma durumu bilinmiyor**: Azure geçişi, yetersiz meta veri nedeniyle sunucunun hazır olup olmadığını belirleyemiyor.

Hazırlığı hesaplamak için değerlendirme, aşağıdaki tablolarda özetlenen sunucu özelliklerini ve işletim sistemi ayarlarını gözden geçirir.

### <a name="server-properties"></a>Sunucu özellikleri

Azure VM değerlendirmesi için değerlendirme, Azure VM 'lerinde çalışıp çalışmadığını tespit etmek için bir şirket içi VM 'nin aşağıdaki özelliklerini gözden geçirir.

Özellik | Ayrıntılar | Azure hazırlık durumu
--- | --- | ---
**Önyükleme türü** | Azure, UEFı değil, BIOS 'un önyükleme türü olan VM 'Leri destekler. | Önyükleme türü UEFı ise koşullu olarak hazırlanıyor
**Çekirdekler** | Her sunucuda en fazla 128 çekirdek olmalıdır; bu, bir Azure VM 'nin desteklediği maksimum sayıdır.<br/><br/> Performans geçmişi varsa, Azure geçişi karşılaştırma için kullanılan çekirdekleri dikkate alır. Değerlendirme ayarları bir rahatetken belirtirseniz, kullanılan çekirdek sayısı, rahatlık faktörüyle çarpılarak çarpılır.<br/><br/> Performans geçmişi yoksa, Azure geçişi, rahatlık faktörünü uygulamak için ayrılmış çekirdekleri kullanır. | Çekirdek sayısı sınırın içindeyse, hazırlanıyor
**KOÇ** | Her sunucuda 3.892 GB 'den fazla RAM olması gerekir. bu boyut, en büyük boyut olan bir Azure ı serisi Standard_M128m &nbsp; <sup>2</sup> VM destekler. [Daha fazla bilgi edinin](../virtual-machines/sizes.md).<br/><br/> Performans geçmişi varsa, Azure geçişi karşılaştırma için kullanılan RAM 'i dikkate alır. Bir rakip faktörü belirtilmişse, kullanılan RAM, rahatlık faktörüyle çarpılarak çarpılır.<br/><br/> Geçmiş yoksa, bir rakip faktörü uygulamak için ayrılan RAM kullanılır.<br/><br/> | RAM miktarı sınırın içindeyse hazırlanıyor
**Depolama diski** | Bir diskin ayrılan boyutu 32 TB 'tan fazla olmamalıdır. Azure, Azure Ultra SSD diskleriyle 64 TB diskleri destekler, ancak henüz Ultra SSD desteklemediği için değerlendirme Şu anda 32 TB 'yi disk boyutu sınırı olarak denetler. <br/><br/> İşletim sistemi diski de dahil olmak üzere sunucuya bağlı disk sayısı 65 veya daha az olmalıdır. | Disk boyutu ve sayı limitlerin içindeyse,
**Ağ** | Sunucuda en fazla 32 ağ arabirimi (NIC) eklenmiş olmalıdır. | NIC sayısı sınırın içindeyse, hazırlanıyor

### <a name="guest-operating-system"></a>Konuk işletim sistemi

Azure VM değerlendirmesi için, VM özelliklerinin incelenmesi ve değerlendirmesi, Azure 'da çalışıp çalışmadığını öğrenmek için bir sunucunun Konuk işletim sistemine bakar.

> [!NOTE]
> VMware VM 'lerinin Konuk analizini işlemek için değerlendirme, vCenter Server içinde VM için belirtilen işletim sistemini kullanır. Ancak, vCenter Server Linux VM işletim sistemleri için çekirdek sürümü sağlamaz. Sürümü bulmak için [uygulama bulmayı](./how-to-discover-applications.md)ayarlamanız gerekir. Daha sonra, Gereç, uygulama bulmayı ayarlarken belirttiğiniz Konuk kimlik bilgilerini kullanarak sürüm bilgilerini bulur.


Değerlendirme, işletim sistemine bağlı olarak Azure hazırlığını belirlemek için aşağıdaki mantığı kullanır:

**İşletim sistemi** | **Ayrıntılar** | **Azure hazırlık durumu**
--- | --- | ---
Windows Server 2016 ve tüm SPs 'ler | Azure tam destek sağlar. | Azure için hazırlayın.
Windows Server 2012 R2 ve tüm SPs 'ler | Azure tam destek sağlar. | Azure için hazırlayın.
Windows Server 2012 ve tüm SPs 'ler | Azure tam destek sağlar. | Azure için hazırlayın.
Tüm SPs 'ler ile Windows Server 2008 R2 | Azure tam destek sağlar.| Azure için hazırlayın.
Windows Server 2008 (32-bit ve 64-bit) | Azure tam destek sağlar. | Azure için hazırlayın.
Windows Server 2003 ve Windows Server 2003 R2 | Bu işletim sistemleri destek son tarihlerini geçti ve Azure 'da destek için [özel bir destek sözleşmesine (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) ihtiyaç duyuyor. | Azure için koşullu olarak hazırlanın. Azure 'a geçiş yapmadan önce işletim sistemini yükseltmeniz göz önünde bulundurun.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3,1 ve MS-DOS | Bu işletim sistemleri destek son tarihlerini geçti. Sunucu Azure 'da başlayabilir, ancak Azure işletim sistemi desteği sağlamaz. | Azure için koşullu olarak hazırlanın. Azure 'a geçiş yapmadan önce işletim sistemini yükseltmenizi öneririz.
Windows 7, Windows 8 ve Windows 10 | Azure [yalnızca bir Visual Studio aboneliği](../virtual-machines/windows/client-images.md) için destek sağlar. | Azure için koşullu olarak hazırlanın.
Windows 10 Pro | Azure, [çok kiracılı barındırma haklarıyla](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) destek sağlar. | Azure için koşullu olarak hazırlanın.
Windows Vista ve Windows XP Professional | Bu işletim sistemleri destek son tarihlerini geçti. Sunucu Azure 'da başlayabilir, ancak Azure işletim sistemi desteği sağlamaz. | Azure için koşullu olarak hazırlanın. Azure 'a geçiş yapmadan önce işletim sistemini yükseltmenizi öneririz.
Linux | Azure 'un onaylayabileceği [Linux işletim sistemlerine](../virtual-machines/linux/endorsed-distros.md) bakın. Diğer Linux işletim sistemleri Azure 'da başlayabilir. Ancak, Azure 'a geçiş yapmadan önce işletim sistemini onaylı bir sürüme yükseltmenizi öneririz. | Sürüm onaylanmış ise Azure için hazırlanın.<br/><br/>Sürüm onaymadıysa koşullu olarak kabul edilebilir.
Oracle Solaris, Apple macOS ve FreeBSD gibi diğer işletim sistemleri | Azure, bu işletim sistemlerini onaylamaz. Sunucu Azure 'da başlayabilir, ancak Azure işletim sistemi desteği sağlamaz. | Azure için koşullu olarak hazırlanın. Azure 'a geçiş yapmadan önce desteklenen bir işletim sistemi yüklemenizi öneririz.  
VCenter Server içinde **diğeri** olarak belirtilen işletim sistemi | Azure geçişi bu durumda işletim sistemini tanımlayamıyor. | Bilinmeyen hazırlık. Azure 'un VM içinde çalışan işletim sistemini desteklediğinden emin olun.
32 bit işletim sistemleri | Sunucu Azure 'da başlayabilir, ancak Azure tam destek sunmayabilir. | Azure için koşullu olarak hazırlanın. Azure 'a geçiş yapmadan önce 64 bitlik bir IŞLETIM sistemine yükseltmeyi göz önünde bulundurun.

## <a name="calculating-sizing"></a>Boyutlandırma hesaplanıyor

Sunucu Azure için hazırlanıyor olarak işaretlendikten sonra değerlendirme, Azure VM değerlendirmesinde boyutlandırma önerileri sağlar. Bu öneriler, Azure VM ve disk SKU 'sunu belirler. Boyutlandırma hesaplamaları, şirket içi boyutlandırma veya performans tabanlı boyutlandırmanın kullanılmasına bağlı olarak değişir.

### <a name="calculate-sizing-as-is-on-premises"></a>Boyutlandırmayı hesapla (Şirket içi olarak)

 Şirket içi olarak boyutlandırma kullanıyorsanız, değerlendirme, Azure VM değerlendirmesinde VM 'Lerin ve disklerin performans geçmişini dikkate almaz.

- **İşlem boyutlandırma**: değerlendirme, şirket içinde ayrılan boyuta göre BIR Azure VM SKU 'su ayırır.
- **Depolama ve disk boyutlandırma**: değerlendirme özellikleri ' nde belirtilen depolama türüne bakar ve uygun disk türünü önerir. Olası depolama türleri Standart HDD, Standart SSD ve Premium ' dur. Varsayılan depolama türü Premium ' dur.
- **Ağ boyutlandırma**: değerlendirme, ağ bağdaştırıcısını şirket içi sunucuda kabul eder.

### <a name="calculate-sizing-performance-based"></a>Boyutlandırmayı hesapla (performans tabanlı)

Azure VM değerlendirmesinde performans tabanlı boyutlandırma kullanırsanız, değerlendirme, boyutlandırma önerilerini aşağıdaki gibi yapar:

- Değerlendirme, Azure 'da VM boyutunu ve disk türünü tanımlamak için sunucunun performans geçmişini dikkate alır.
- Sunucuları bir CSV dosyası kullanarak içeri aktarırsanız, belirttiğiniz değerler kullanılır. Bu yöntem özellikle şirket içi sunucunun fazla yüklenmiş olması, kullanımın düşük olması ve Azure VM 'nin maliyetleri kazanmak için sağ boyutunu kullanmak istediğinizde yararlıdır.
- Performans verilerini kullanmak istemiyorsanız, önceki bölümde açıklandığı gibi boyutlandırma ölçütünü şirket içinde olduğu gibi olarak sıfırlayın.

#### <a name="calculate-storage-sizing"></a>Depolama boyutunu hesapla

Azure geçişi, bir Azure VM değerlendirmesinde depolama boyutu için, sunucuya bağlı her diski bir Azure diskine eşlemeye çalışır. Boyutlandırma aşağıdaki gibi çalışmaktadır:

1. Değerlendirme, gereken toplam ıOPS 'yi almak için bir diskin okuma ve yazma ıOPS 'sini ekler. Benzer şekilde, her diskin toplam aktarım hızını almak için okuma ve yazma aktarım hızı değerlerini ekler. İçeri aktarma tabanlı değerlendirmeler söz konusu olduğunda toplam ıOPS, toplam üretilen iş ve toplam No ' u sağlama seçeneğiniz vardır. ayrı ayrı disk ayarları belirtmeden içeri aktarılan dosyadaki diskler. Bunu yaparsanız, tek bir disk boyutlandırma atlanır ve sağlanan veriler doğrudan boyutlandırmayı hesaplamak için kullanılır ve uygun bir VM SKU 'SU seçer.

1. Depolama türünü otomatik olarak belirttiyseniz, seçilen tür, etkin ıOPS ve aktarım hızı değerlerini temel alır. Değerlendirme, diskin Azure 'da bir Standart HDD, Standart SSD veya Premium diskle eşlenip eşlenmeyeceğini belirler. Depolama türü bu disk türlerinden birine ayarlanırsa, değerlendirme seçili depolama türü içinde bir disk SKU 'SU bulmayı dener.
1. Diskler şu şekilde seçilir:
    - Değerlendirme, gerekli ıOPS ve aktarım hızı ile bir disk bulamazsa, sunucuyu Azure için uygun değil olarak işaretler.
    - Değerlendirme, uygun bir disk kümesi bulursa, değerlendirme ayarlarında belirtilen konumu destekleyen diskleri seçer.
    - Birden fazla uygun disk varsa, değerlendirme en düşük maliyetli diski seçer.
    - Herhangi bir disk için performans verileri kullanılamıyorsa, Azure 'da Standart SSD disk bulmak için yapılandırma disk boyutu kullanılır.

#### <a name="calculate-network-sizing"></a>Ağ boyutlandırmayı hesapla

Azure VM değerlendirmesi için değerlendirme, şirket içi sunucuya bağlı ağ bağdaştırıcılarının sayısını ve gereken performansını destekleyen bir Azure VM bulmaya çalışır.

- Şirket içi sunucunun etkili ağ performansını sağlamak için, değerlendirme tüm ağ bağdaştırıcılarında sunucudan (ağ dışına) veri iletimi hızını toplar. Daha sonra, rahatlık faktörünü uygular. Bu, gereken ağ performansını destekleyebilen bir Azure VM bulmak için elde edilen değeri kullanır.
- Değerlendirme, ağ performansının yanı sıra Azure VM 'nin gerekli sayıda ağ bağdaştırıcısını destekleyip desteklemediğini de dikkate alır.
- Ağ performansı verileri kullanılamıyorsa, değerlendirme yalnızca VM boyutlandırma için ağ bağdaştırıcısı sayısını dikkate alır.

#### <a name="calculate-compute-sizing"></a>İşlem boyutunu hesapla

Depolama ve ağ gereksinimlerini hesapladıktan sonra değerlendirme, Azure 'da uygun bir VM boyutu bulmak için CPU ve RAM gereksinimlerini dikkate alır.

- Azure geçişi, uygun bir Azure VM boyutu bulmak için etkin kullanılan çekirdekleri ve RAM 'i arar.
- Uygun boyut bulunamazsa, sunucu Azure için uygun değil olarak işaretlenir.
- Uygun bir boyut bulunursa Azure geçişi depolama ve ağ hesaplamalarını uygular. Ardından, son VM boyutu önerisi için konum ve fiyatlandırma katmanı ayarlarını uygular.
- Birden fazla uygun Azure sanal makinesi boyutu varsa, en düşük maliyetli olan önerilir.

## <a name="confidence-ratings-performance-based"></a>Güven derecelendirmeleri (performans tabanlı)

Azure geçişi 'ndeki her performans tabanlı Azure VM değerlendirmesi bir güvenirlik derecelendirmesi ile ilişkilendirilir. Derecelendirme, bir (en düşük) ile beş (en yüksek) yıldıza göre değişir. Güvenilirlik derecelendirmesi, Azure geçişi 'nin sağladığı önerilerin boyutunu tahmin etmenize yardımcı olur.

- Güvenirlik derecelendirmesi bir değerlendirmeye atanır. Derecelendirme, değerlendirmeyi hesaplamak için gereken veri noktalarının kullanılabilirliğine bağlıdır.
- Performans tabanlı boyutlandırma için değerlendirme şunları gerektirir:
    - CPU ve RAM için kullanım verileri.
    - Sunucuya bağlı her diske yönelik disk ıOPS ve aktarım hızı verileri.
    - Bir sunucuya bağlı her ağ bağdaştırıcısı için performans tabanlı boyutlandırmayı işlemek üzere ağ g/ç.

Bu kullanım numaralarının herhangi biri kullanılabilir değilse, boyut önerileri güvenilir olmayabilir.

> [!NOTE]
> Güven derecelendirmeleri, içeri aktarılan bir CSV dosyası kullanılarak değerlendirilen sunucular için atanmaz. Derecelendirmeler Ayrıca, şirket içi değerlendirme için de geçerli değildir.

### <a name="ratings"></a>Derecelendirmeler

Bu tabloda, kullanılabilir veri noktalarının yüzdesine bağlı olan değerlendirme güvenilirliği derecelendirmeleri gösterilmektedir:

   **Veri noktalarının kullanılabilirliği** | **Güvenilirlik derecelendirmesi**
   --- | ---
   % 0-20 | 1 yıldız
   % 21-40 | 2 yıldız
   % 41-60 | 3 yıldız
   % 61-80 | 4 yıldız
   % 81-100 | 5 yıldız

### <a name="low-confidence-ratings"></a>Düşük güvenilirlikli derecelendirmeler

Bir değerlendirmenin en düşük güvenilirlik derecelendirmesinin neden olmasının birkaç nedeni aşağıda verilmiştir:

- Değerlendirmeyi oluşturduğunuz süre boyunca ortamınızın profilini oluşturmadınız. Örneğin, performans süresi ile bir gün ayarlanmış bir değerlendirme oluşturursanız, toplanan tüm veri noktaları için bulmayı başlattıktan sonra en az bir gün beklemeniz gerekir.
- Değerlendirme, değerlendirme süresi içinde sunucuların bir bölümü veya tümü için performans verilerini toplayamıyor. Yüksek güvenilirlikli bir derecelendirme için lütfen şunları doğrulayın: 
    - Değerlendirme süresi boyunca sunucular açık
    - 443 bağlantı noktalarında giden bağlantılara izin verilir
    - Hyper-V sunucuları için dinamik bellek etkin 
    
    Güvenilirlik derecelendirmesindeki en son değişiklikleri yansıtacak şekilde değerlendirmeyi 'Yeniden Hesaplayın'.

- Değerlendirmenin hesaplanışında bazı sunucular oluşturulmuştur. Örneğin, geçen ayın performans geçmişi için bir değerlendirme oluşturduğunuzu varsayalım, ancak bazı sunucular yalnızca bir hafta önce oluşturulmuştur. Bu durumda, yeni sunucular için performans verileri sürenin tamamına uygun olmayacaktır ve güvenirlik derecelendirmesi düşük olacaktır.

> [!NOTE]
> Herhangi bir değerlendirmenin güvenilirlik derecelendirmesi beş yıldızlı günden azsa, gerecin ortamın profilini oluşturup daha sonra değerlendirmeyi yeniden hesaplaması için en az bir gün beklemeniz önerilir. Aksi takdirde, performans tabanlı boyutlandırma güvenilir olmayabilir. Bu durumda, değerlendirmeyi şirket içi boyutlandırılmasına geçmeniz önerilir.

## <a name="calculate-monthly-costs"></a>Aylık maliyetleri hesapla

Boyutlandırma önerileri tamamlandıktan sonra, Azure geçişi 'ndeki bir Azure VM değerlendirmesi, geçiş işleminden sonra işlem ve depolama maliyetlerini hesaplar.

- **İşlem maliyeti**: Azure geçişi, sunucunun aylık maliyetini hesaplamak Için ÖNERILEN Azure VM boyutunu ve Azure Faturalandırma API 'sini kullanır.

    Hesaplama şu şekilde hesaba girer:
    - İşletim sistemi
    - Yazılım Güvencesi
    - Ayrılmış örnekler
    - VM çalışma süresi
    - Konum
    - Para birimi ayarları

    Değerlendirme, toplam aylık işlem maliyetini hesaplamak için tüm sunuculardaki maliyeti toplar.

- **Depolama maliyeti**: sunucu için aylık depolama maliyeti, sunucuya bağlı tüm disklerin aylık maliyeti toplayarak hesaplanır.

    Değerlendirme, tüm sunucuların depolama maliyetlerini toplayarak toplam aylık depolama maliyetlerini hesaplar. Şu anda hesaplama, değerlendirme ayarlarında belirtilen teklifleri göz önünde bulundurmaz.

Ücretler, değerlendirme ayarlarında belirtilen para biriminde görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

Değerlendirme oluşturmak için en iyi uygulamaları [gözden geçirin](best-practices-assessment.md) . 

- [VMware](./tutorial-discover-vmware.md) ve [Hyper-V](./tutorial-discover-hyper-v.md) ortamında ve [fiziksel sunucularda](./tutorial-discover-physical.md)çalışan sunucular için değerlendirme çalıştırma hakkında bilgi edinin.
- [BIR CSV dosyası ile içeri aktarılan](./tutorial-discover-import.md)sunucuları değerlendirme hakkında bilgi edinin.
- [Bağımlılık görselleştirmesini](concepts-dependency-visualization.md)ayarlama hakkında bilgi edinin.