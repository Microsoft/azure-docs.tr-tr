---
title: Azure geçişi 'nde değerlendirme hesaplamaları | Microsoft Docs
description: Azure geçişi hizmetindeki değerlendirme hesaplamalarına genel bir bakış sağlar.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: hamusa
ms.openlocfilehash: d72e5a6dea8b411b6214e7749b8993f9f5a6e7a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466926"
---
# <a name="assessment-calculations-in-azure-migrate"></a>Azure geçişi 'nde değerlendirme hesaplamaları

[Azure geçişi](migrate-services-overview.md) , şirket içi uygulamalarınızın ve iş yüklerinizin bulmayı, değerlendirmesini ve geçişini izlemek için bir merkezi Merkez sağlar. Ayrıca, özel ve genel bulut örneklerinizi Azure 'da izler. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri sunar.

Sunucu değerlendirmesi, Azure 'da değerlendirir şirket içi sunucuları Azure 'a geçiş için geçirme konusunda bir araçtır. Bu makalede, değerlendirmelerin nasıl hesaplandığı hakkında bilgi sağlanır.

## <a name="whats-in-an-assessment"></a>Bir değerlendirme neleri içerir?

**Özellik** | **Ayrıntılar**
--- | ---
**Hedef konum** | Geçirmek istediğiniz Azure konumunu belirtir.<br/><br/>Sunucu değerlendirmesi Şu anda şu hedef bölgeleri destekliyor: Avustralya Doğu, Avustralya Güneydoğu, Brezilya Güney, Kanada Orta, Kanada Doğu, Orta Hindistan, Orta ABD, Çin Doğu, Çin Kuzey, Doğu Asya, Doğu ABD, Doğu ABD2, Almanya Orta, Almanya Kuzeydoğu, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney, Orta Kuzey ABD, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, Güney Hindistan, UK Güney, UK Batı, US Gov Arizona, US Gov Teksas, US Gov Virginia, Orta Batı ABD, Batı Avrupa, Batı Hindistan, Batı ABD ve Batı ABD2.
**Depolama türü** | Azure 'da depolama için kullanmak istediğiniz disk türünü belirtir. <br/><br/> Şirket içi boyutlandırma için, hedef depolama diskinin türünü Premium tarafından yönetilen, Standart SSD yönetilen veya Standart HDD yönetilen olarak belirtebilirsiniz. Performans tabanlı boyutlandırma için, hedef depolama diskinin türünü otomatik, Premium tarafından yönetilen, Standart HDD yönetilen veya Standart SSD yönetilen olarak belirtebilirsiniz. Depolama türünü otomatik olarak belirttiğinizde, disk önerisi, disklerin performans verilerine göre belirlenir: saniye başına giriş/çıkış işlemi (ıOPS) ve aktarım hızı. <br/><br/>Depolama türünü Premium veya standart olarak belirtirseniz, değerlendirme, seçilen depolama türü içinde bir disk SKU 'SU önerir. % 99,9 tek örnekli bir VM SLA 'Sı elde etmek istiyorsanız, depolama türünü Premium ile yönetilen diskler olarak belirtmek isteyebilirsiniz. Bu, değerlendirmede tüm disklerin Premium tarafından yönetilen diskler olarak önerilmesini sağlar. Azure Geçişi’nin yönetilen diskleri yalnızca geçiş değerlendirmesi için desteklediğini unutmayın.
**Ayrılmış örnekler (RIS)** | Bu özellik, Azure 'da [ayrılmış örnekler](https://azure.microsoft.com/pricing/reserved-vm-instances/) belirtmenize yardımcı olur. Değerlendirmede maliyet tahminleri daha sonra hesap halinde RI indirimleri alır. Şu anda yalnızca Azure geçişi 'ndeki Kullandıkça Öde teklifleri için yapılandırılmış olan RIS desteği sunulmaktadır.
**Boyutlandırma ölçütü** | Azure için VM 'leri *doğru boyuta* göre kullanılacak ölçütleri ayarlar. Performans geçmişini dikkate almadan, *performans tabanlı* boyutlandırmayı tercih edebilir veya VM 'leri *Şirket içinde olacak şekilde* değiştirebilirsiniz.
**Performans geçmişi** | Makinelerin performans verilerini değerlendirmek için göz önünde bulundurulması gereken süreyi ayarlar. Bu özellik yalnızca boyutlandırma ölçütü *performans tabanlı*olduğunda geçerlidir.
**Yüzdebirlik kullanımı** | Sağ boyutlandırmanın kabul edileceği performans örneği kümesinin yüzdebirlik değerini belirtir. Bu özellik yalnızca boyutlandırma performans tabanlı olduğunda geçerlidir.
**VM serisi** | Doğru boyutlandırma için göz önünde bulundurmanız istediğiniz VM serisini belirtmenize olanak tanır. Örneğin, Azure 'da bir serisi VM 'lere geçirmeyi planlamadığınız bir üretim ortamınız varsa, bir serisi listeden veya seriden hariç tutabilir ve sağ boyutlandırma yalnızca seçili seriler içinde yapılır.
**Konfor katsayısı** | Azure geçişi sunucu değerlendirmesi, değerlendirme sırasında bir arabellek (rahatetken) kabul eder. Bu tampon, VM’lerin makine kullanım verilerinin (CPU, bellek, disk ve ağ) üzerine uygulanır. Konfor katsayısı; sezona özgü kullanım, kısa performans geçmişi ve gelecek kullanımlarda oluşabilecek artışlar gibi konuları hesaba katar.<br/><br/> Örneğin, %20 kullanıma sahip 10 çekirdekli bir VM normalde 2 çekirdekli VM ile sonuçlanır. Ancak, 2.0x konfor katsayısı ile sonuç 4 çekirdekli VM olur.
**Teklif** | Kayıtlı olduğunuz [Azure teklifini](https://azure.microsoft.com/support/legal/offer-details/) görüntüler. Azure Geçişi, buna göre bir maliyet tahmini oluşturur.
**Para Birimi** | Hesabınız için faturalandırma para birimini gösterir.
**İndirim (%)** | Azure teklifinin üzerine aldığınız aboneliğe özgü tüm indirimi listeler. Varsayılan ayar, %0’dır.
**VM çalışma süresi** | VM 'leriniz günde 24 saat, Azure 'da haftanın 7 günü çalışır durumda değilse, çalıştırılacağı süreyi (aylık gün sayısı ve gün başına saat sayısı) ve maliyet tahminleri buna göre işlenir. Varsayılan değer ayda 31 gün ve günde 24 saat olur.
**Azure Hibrit Avantajı** | Yazılım Güvencesi olup olmadığını ve [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-use-benefit/)uygun olduğunu belirtir. Evet olarak ayarlanırsa, Windows sanal makineleri için Windows olmayan Azure fiyatları dikkate alınır. Varsayılan ayar Evet ' tir.

## <a name="how-are-assessments-calculated"></a>Değerlendirmeler nasıl hesaplanır?

Azure geçişi sunucu değerlendirmesi ' nde bir değerlendirme, şirket içi sunucular hakkında toplanan meta veriler kullanılarak hesaplanır. Bulma kaynağı bir kullanarak içeri aktarmayla. CSV dosyası, değerlendirme, Kullanıcı tarafından sunucular hakkında belirtilen meta veriler kullanılarak hesaplanır. Değerlendirme hesaplaması üç aşamada işlenir. Her sunucu için, değerlendirme hesaplaması bir Azure uygunluk analizi ile başlar, ardından boyutlandırma ve son olarak aylık maliyet tahmini. Sunucu daha sonraki bir aşamaya geçer ve yalnızca öncekini geçerse. Örneğin, bir sunucu Azure uygunluk denetiminde başarısız olursa, Azure için uygun değil olarak işaretlenir ve bu sunucu için boyutlandırma ve maliyetlendirme yapılmaz.

## <a name="azure-suitability-analysis"></a>Azure uygunluk Analizi

Tüm makineler Azure 'da çalışmak üzere uygun değildir. Sunucu değerlendirmesi, her şirket içi makinenin Azure geçişine uygunluk için değerlendirir. Ayrıca, her bir değerlendirilen makineyi aşağıdaki uygunluk kategorilerinden birine atar:
- **Azure Için hazırlanma**: makine, hiçbir değişiklik yapılmadan Azure 'a olarak geçirilebilir. Azure 'da tam Azure desteğiyle başlatılır.
- **Azure Için koşullu olarak hazırlanıyor**: makine Azure 'da başlayabilir, ancak tam Azure desteği olmayabilir. Örneğin, eski bir Windows Server sürümünü çalıştıran bir makine Azure 'da desteklenmez. Bu makineleri Azure 'a geçirmeden önce dikkatli olmanız ve hazırlık sorunlarını gidermek için değerlendirmede önerilen düzeltme kılavuzunu izlemeniz gerekir.
- **Azure için hazırlanma**: makine Azure 'da başlamacaktır. Örneğin, şirket içi bir makinede 64 terabayta (TB) bağlı bir disk varsa, Azure üzerinde barındırılamaz. Makineyi Azure 'a geçirmeden önce hazırlık sorununu düzeltebilmeniz için değerlendirmede önerilen düzeltme kılavuzunu izlemeniz gerekir. Doğru boyut ve maliyet tahmini, Azure için uygun değil olarak işaretlenen makineler için yapılmaz.
- **Hazır olma durumu bilinmiyor**: Azure geçişi, şirket içi ortamdan toplanan meta verilerin yetersiz olması nedeniyle makinenin hazır olduğunu saptayamadık.

Sunucu değerlendirmesi, şirket içi makinenin Azure 'un hazır olduğunu öğrenmek için makine özelliklerini ve konuk işletim sistemini gözden geçirir.

### <a name="machine-properties"></a>Makine özellikleri

Sunucu değerlendirmesi, Azure 'da çalışıp çalışmadığını öğrenmek için şirket içi VM 'nin aşağıdaki özelliklerini gözden geçirir.

**Özellik** | **Ayrıntılar** | **Azure hazırlık durumu**
--- | --- | ---
**Önyükleme türü** | Azure, UEFı değil, BIOS 'un önyükleme türü olan VM 'Leri destekler. | Önyükleme türü UEFı ise koşullu olarak hazırlanın.
**Sayısı** | Makinelerdeki çekirdek sayısı, bir Azure VM için desteklenen en yüksek çekirdek sayısına (128) eşit veya ondan daha az olmalıdır.<br/><br/> Performans geçmişi varsa, Azure geçişi karşılaştırma için kullanılan çekirdekleri dikkate alır. Değerlendirme ayarlarında bir rahatlık faktörü belirtilmişse, kullanılan çekirdek sayısı, rahatlık faktörüyle çarpılarak çarpılır.<br/><br/> Hiçbir performans geçmişi yoksa, Azure geçişi, rakip etmenini uygulamadan ayrılmış çekirdekleri kullanır. | Sınırlara eşit veya daha küçükse hazırlanın.
**Bellek** | Makinenin bellek boyutu, bir Azure sanal makinesi için izin verilen en yüksek bellekten (3892 gigabayt [GB], Azure 8 serisi Standard_M128m&nbsp;<sup>2</sup>) daha düşük veya bu değere eşit olmalıdır. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Performans geçmişi varsa, Azure geçişi karşılaştırma için kullanılan belleği kabul eder. Bir rakip faktörü belirtilmişse, kullanılan bellek, rahatlık faktörüyle çarpılarak çarpılır.<br/><br/> Geçmiş yoksa, ayrılan bellek, rahatlık faktörü uygulanmadan kullanılır.<br/><br/> | Sınırlar içindeyse hazırlanın.
**Depolama diski** | Bir diskin ayrılan boyutu 32 TB veya daha az olmalıdır. Azure, Ultra SSD diskleriyle 64 TB diski destekler, ancak Azure geçişi: Sunucu değerlendirmesi Şu anda Ultra SSD desteklemediği için, disk boyutu sınırları olarak 32 TB 'yi kontrol eder. <br/><br/> Makineye bağlı disk sayısı, işletim sistemi diski dahil 65 veya daha az olmalıdır. | Sınırlar içindeyse hazırlanın.
**Ağ** | Bir makinenin kendisine bağlı 32 veya daha az ağ arabirimi (NIC) olmalıdır. | Sınırlar içindeyse hazırlanın.

### <a name="guest-operating-system"></a>Konuk işletim sistemi
Sunucu değerlendirmesi, VM özellikleriyle birlikte makinelerin Konuk işletim sistemine bakar ve bunların Azure üzerinde çalışıp çalışmadığını tespit edebilir.

> [!NOTE]
> VMware VM 'Leri için sunucu değerlendirmesi, Konuk IŞLETIM sistemi analizini işlemek üzere vCenter Server ' de VM için belirtilen işletim sistemini kullanır. VMware üzerinde çalışan Linux VM 'Leri için şu anda Konuk işletim sisteminin tam çekirdek sürümünü tanımlamaz.

Aşağıdaki mantık, sunucu değerlendirmesi tarafından, işletim sistemine bağlı olarak Azure hazırlığını belirlemek için kullanılır.

**İşletim Sistemi** | **Ayrıntılar** | **Azure hazırlık durumu**
--- | --- | ---
Tüm SPs & Windows Server 2016 | Azure tam destek sağlar. | Azure için hazır
Tüm SPs & Windows Server 2012 R2 | Azure tam destek sağlar. | Azure için hazır
Tüm SPs & Windows Server 2012 | Azure tam destek sağlar. | Azure için hazır
Tüm SPs 'ler ile Windows Server 2008 R2 | Azure tam destek sağlar.| Azure için hazır
Windows Server 2008 (32-bit ve 64-bit) | Azure tam destek sağlar. | Azure için hazır
Windows Server 2003, 2003 R2 | Bu işletim sistemleri destek son tarihlerini geçti ve Azure 'da destek için [özel bir destek sözleşmesine (CSA)](https://aka.ms/WSosstatement) ihtiyaç duyuyor. | Azure için koşullu olarak hazırlanın. Azure 'a geçiş yapmadan önce işletim sistemini yükseltmeniz göz önünde bulundurun.
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Bu işletim sistemleri destek son tarihleri geçti. Makine Azure 'da başlayabilir, ancak Azure işletim sistemi desteği sağlamaz. | Azure için koşullu olarak hazırlanın. Azure 'a geçiş yapmadan önce işletim sistemini yükseltmenizi öneririz.
Windows Istemcisi 7, 8 ve 10 | Azure [yalnızca Visual Studio aboneliği](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) için destek sağlar. | Azure için koşullu olarak hazır
Windows 10 Pro masaüstü | Azure, [çok kiracılı barındırma haklarıyla](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) destek sağlar. | Azure için koşullu olarak hazır
Windows Vista, XP Professional | Bu işletim sistemleri destek son tarihleri geçti. Makine Azure 'da başlayabilir, ancak Azure işletim sistemi desteği sağlamaz. | Azure için koşullu olarak hazırlanın. Azure 'a geçiş yapmadan önce işletim sistemini yükseltmenizi öneririz.
Linux | Azure bu [Linux işletim sistemlerini](../virtual-machines/linux/endorsed-distros.md)doğrubir şekilde algılar. Diğer Linux işletim sistemleri Azure 'da başlayabilir, ancak Azure 'a geçiş yapmadan önce işletim sistemini onaylı bir sürüme yükseltmenizi öneririz. | Sürüm onaylanmış ise Azure için hazırlanın.<br/><br/>Sürüm henüz hazırlanmadığından koşullu olarak kabul edilebilir.
Diğer işletim sistemleri<br/><br/> Örneğin, Oracle Solaris, Apple Mac OS vb., FreeBSD vb. | Azure, bu işletim sistemlerini onaylamaz. Makine Azure 'da başlayabilir, ancak Azure işletim sistemi desteği sağlamaz. | Azure için koşullu olarak hazırlanın. Azure 'a geçiş yapmadan önce desteklenen bir işletim sistemi yüklemenizi öneririz.  
VCenter Server içinde **diğeri** olarak belirtilen işletim sistemi | Azure geçişi bu durumda işletim sistemini tanımlayamıyor. | Bilinmeyen hazırlık. VM içinde çalışan işletim sisteminin Azure 'da desteklendiğinden emin olun.
32 bit işletim sistemleri | Makine Azure 'da başlayabilir, ancak Azure tam destek sunmayabilir. | Azure için koşullu olarak hazırlanın. Azure 'a geçiş yapmadan önce makinenin işletim sistemini 32 bitlik IŞLETIM sisteminden 64 bit işletim sistemine yükseltmeniz göz önünde bulundurun.

## <a name="sizing"></a>Boyutlandırma

Bir makine Azure için hazırlanın olarak işaretlendikten sonra sunucu değerlendirmesi, şirket içi VM için uygun Azure VM ve disk SKU 'sunu tanımlamayı içeren boyutlandırma önerilerini sağlar. Bu öneriler, belirtilen değerlendirme özelliklerine bağlı olarak farklılık gösterir.

- Değerlendirme *performans tabanlı boyutlandırma*kullanıyorsa, Azure geçişi, Azure 'da VM boyutunu ve disk türünü belirlemek için makinenin performans geçmişini dikkate alır. Bulma kaynağı içeri aktarma olan sunucular söz konusu olduğunda, Kullanıcı tarafından belirtilen performans kullanım değerleri göz önünde bulundurulmakta sayılır. Bu yöntem, şirket içi VM 'yi aşırı ayırdıysanız ancak kullanım düşükse ve maliyetleri kaydetmek için Azure 'da VM 'yi sağ boyuta eklemek istiyorsanız özellikle yararlıdır. Bu yöntem, geçiş sırasında boyutları iyileştirmenize yardımcı olur.
- VM boyutlandırma için performans verilerini göz önünde bulundurmayın ve şirket içi makineleri Azure 'a olarak almak istiyorsanız, boyutlandırma ölçütünü *Şirket içi olarak*olarak ayarlayabilirsiniz. Daha sonra, sunucu değerlendirmesi, kullanım verilerini dikkate almadan VM 'Leri şirket içi yapılandırmaya göre boyut olarak kullanacaktır. Bu durumda, disk boyutlandırma etkinlikleri değerlendirme özelliklerinde (Standart HDD, Standart SSD veya Premium disklerde) belirttiğiniz depolama türünü temel alır.

### <a name="performance-based-sizing"></a>Performans tabanlı boyutlandırma

Performans tabanlı boyutlandırma için sunucu değerlendirmesi, VM 'ye bağlı disklerle başlar ve ardından ağ bağdaştırıcıları gelir. Daha sonra, şirket içi VM 'nin işlem gereksinimlerine bağlı olarak bir Azure VM SKU 'sunu eşler. Azure geçişi gereci, CPU, bellek, disk ve ağ bağdaştırıcısı için performans verilerini toplamak üzere şirket içi ortamı profiller.

**Performans verileri toplama adımları:**

1. VMware VM 'Leri için Azure geçişi gereci, her 20 saniyelik aralığa göre gerçek zamanlı bir örnek noktası toplar. Hyper-V VM 'Leri için gerçek zamanlı örnek noktası her 30 saniyelik aralığa göre toplanır. Fiziksel sunucular için gerçek zamanlı örnek noktası her 5 dakikalık aralıkta toplanır. 
2. Gereç, 10 dakikada bir toplanan örnek noktaların dökümünü yapar ve en son 10 dakikalık değeri sunucu değerlendirmesine gönderir. 
3. Sunucu değerlendirmesi son bir ay için 10 dakikalık tüm örnek noktalarını depolar. Daha sonra, *performans geçmişi* ve *yüzdebirlik kullanımı*için belirtilen değerlendirme özelliklerine bağlı olarak, doğru boyutlandırma için kullanılacak uygun veri noktasını tanımlar. Örneğin, performans geçmişi 1 güne ayarlanmışsa ve yüzdebirlik kullanımı 95. yüzdebirlik ise, sunucu değerlendirmesi son bir gün için 10 dakikalık örnek noktalarını kullanır, bunları artan düzende sıralar ve sağ boyutlandırma için 95. yüzdebirlik değerini seçer. 
4. Bu değer, her ölçüm için etkili performans kullanım verilerini (CPU kullanımı, bellek kullanımı, disk ıOPS (okuma ve yazma), disk aktarım hızı (okuma ve yazma) ve ağ aktarım hızını (ın ve out) almak için rahatlık faktörüyle çarpılır. Gereç toplar.

Etkin kullanım değeri saptandıktan sonra, depolama, ağ ve bilgi işlem boyutu aşağıdaki şekilde işlenir.

> [!NOTE]
> İçeri aktarma yoluyla eklenen sunucular için, Kullanıcı tarafından sunulan performans verileri doğrudan doğru boyutlandırma için kullanılır.

**Depolama boyutlandırma**: Azure geçişi, makineye bağlı her diski Azure 'daki bir diske eşlemeye çalışır.

> [!NOTE]
> Azure geçişi sunucu değerlendirmesi yalnızca değerlendirme için yönetilen diskleri destekler.

  - Sunucu değerlendirmesi, gereken toplam ıOPS 'yi almak için bir diskin okuma ve yazma ıOPS 'sini ekler. Benzer şekilde, her diskin toplam aktarım hızını almak için okuma ve yazma aktarım hızı değerlerini ekler.
  - Etkin ıOPS ve aktarım hızı değerlerine göre depolama türünü otomatik olarak belirttiyseniz, sunucu değerlendirmesi, diskin bir standart HDD, standart SSD veya Azure 'da bir Premium diskle eşlenmesi gerekip gerekmediğini belirler. Depolama türü Standart HDD/SSD/Premium olarak ayarlandıysa, sunucu değerlendirmesi seçilen depolama türü (Standart HDD/SSD/Premium diskler) içinde bir disk SKU 'SU bulmayı dener.
  - Sunucu değerlendirmesi gereken ıOPS ve aktarım hızına sahip bir disk bulamazsa, makineyi Azure için uygun değil olarak işaretler.
  - Sunucu değerlendirmesi bir dizi uygun disk bulursa, değerlendirme ayarlarında belirtilen konumu destekleyen diskleri seçer.
  - Birden fazla uygun disk varsa, sunucu değerlendirmesi en düşük maliyetli diski seçer.
  - Herhangi bir disk için performans verileri kullanılamaz durumdaysa, Azure 'da standart SSD disk bulmak için diskin yapılandırma verileri (disk boyutu) kullanılır.

**Ağ boyutlandırma**: Sunucu değerlendirmesi, şirket içi makineye bağlı ağ bağdaştırıcılarının sayısını ve bu ağ bağdaştırıcılarının gerektirdiği performansı destekleyebilen BIR Azure VM bulmaya çalışır.
- Şirket içi VM 'nin etkili ağ performansını almak için sunucu değerlendirmesi, saniyede aktarılan verileri (MB/sn), tüm ağ bağdaştırıcılarında (ağ üzerinden) toplar ve rahatlık faktörünü uygular. Bu numarayı, gerekli ağ performansını destekleyebilen bir Azure VM bulmak için kullanır.
- Sunucu değerlendirmesi, ağ performansının yanı sıra Azure VM 'nin gerekli ağ bağdaştırıcısı sayısını destekleyip desteklemediğini de dikkate alır.
- Ağ performans verisi yoksa, sunucu değerlendirmesi yalnızca VM boyutlandırma için ağ bağdaştırıcısı sayısını dikkate alır.

> [!NOTE]
> Ağ bağdaştırıcılarının sayısı belirtildiğinde içeri aktarılan sunucular için şu anda desteklenmiyor

**İşlem boyutlandırma**: depolama ve ağ gereksinimlerini hesapladıktan sonra sunucu değerlendirmesi, Azure 'da uygun bir VM boyutu bulmak için CPU ve bellek gereksinimlerini dikkate alır.
- Azure geçişi, Azure 'da uygun bir VM boyutu bulmak için kullanılan etkin çekirdekler ve belleğe bakar.
- Uygun boyut bulunamazsa, makine Azure için uygun olmayan olarak işaretlenir.
- Uygun bir boyut bulunursa Azure geçişi depolama ve ağ hesaplamalarını uygular. Ardından, son VM boyutu önerisi için konum ve fiyatlandırma katmanı ayarlarını uygular.
- Birden fazla uygun Azure sanal makinesi boyutu varsa, en düşük maliyetli olan önerilir.

### <a name="as-on-premises-sizing"></a>Şirket içi boyutlandırma olarak

*Şirket içi boyutlandırma olarak*kullanıyorsanız, sunucu değerlendirmesi VM 'lerin ve disklerin performans geçmişini dikkate almaz. Bunun yerine, şirket içinde ayrılan boyuta göre Azure 'da bir VM SKU 'SU ayırır. Benzer şekilde disk boyutlandırması için sunucu değerlendirmesi, değerlendirme özelliklerinde belirtilen depolama türüne bakar (Standart HDD/SSD/Premium) ve disk türünü uygun şekilde önerir. Varsayılan depolama türü Premium disklerdir.

## <a name="confidence-ratings"></a>Güven derecelendirmeleri
Azure geçişi 'ndeki her performans tabanlı değerlendirme, bir (en düşük) ile beş yıldızlı (en yüksek) arasında değişen bir güvenirlik derecelendirmesi ile ilişkilendirilir.
- Güvenilirlik derecelendirmesi, değerlendirmeyi hesaplamak için gereken veri noktalarının kullanılabilirliği temelinde bir değerlendirmeye atanır.
- Bir değerlendirmenin güvenilirlik derecesi, Azure Geçişi tarafından sağlanan boyut önerilerinin güvenilirliğini tahmin etmenize yardımcı olur.
- Güven derecelendirmeleri, *Şirket içi* değerlendirmelere uygun değildir.
- Performans tabanlı boyutlandırma için sunucu değerlendirmesi şunları gerektirir:
    - CPU ve VM belleği için kullanım verileri.
    - SANAL makineye bağlı her diske yönelik disk ıOPS ve aktarım hızı verileri.
    - Bir VM 'ye bağlı her bir ağ bağdaştırıcısı için performans tabanlı boyutlandırmayı işlemek üzere ağ g/ç.

   Bu kullanım numaralarının herhangi biri vCenter Server kullanılamazsa, boyut önerisi güvenilir olmayabilir.

Kullanılabilir veri noktalarının yüzdesine bağlı olarak, değerlendirmenin güvenilirlik derecelendirmesi aşağıdaki gibi gider.

   **Veri noktalarının kullanılabilirliği** | **Güvenilirlik derecelendirmesi**
   --- | ---
   % 0-20 | 1 yıldız
   % 21-40 | 2 yıldız
   % 41-60 | 3 yıldız
   % 61-80 | 4 yıldız
   % 81-100 | 5 yıldız

> [!NOTE]
> Güven derecelendirmeleri, kullanılarak içeri aktarılan sunucuların değerlendirmelerine atanmaz. CSV dosyasını Azure geçişi 'ne ekleyin. 

### <a name="low-confidence-ratings"></a>Düşük güvenilirlikli derecelendirmeler

Bir değerlendirmenin en düşük güvenilirlik derecelendirmesinin neden olmasının birkaç nedeni aşağıda verilmiştir:

- Değerlendirmeyi oluşturduğunuz süre için ortamınızı profildiniz. Örneğin, performans süresi ile bir gün ayarlanmış bir değerlendirme oluşturursanız, toplanan tüm veri noktaları için bulmayı başlattıktan sonra en az bir gün beklemeniz gerekir.
- Bazı sanal makineler, değerlendirmenin hesaplanmakta olduğu süre boyunca kapatıldı. Bir süre için herhangi bir VM kapatılmışsa, sunucu değerlendirmesi söz konusu döneme ait performans verilerini toplayamazlar.
- Değerlendirmenin hesaplandığı süre boyunca bazı sanal makineler oluşturulmuştur. Örneğin, geçen ayın performans geçmişi için bir değerlendirme oluşturduysanız ancak bazı sanal makineler ortamda yalnızca bir hafta önce oluşturulduysa, yeni VM 'lerin performans geçmişi, tamamlanma süresi boyunca mevcut olmayacaktır.

> [!NOTE]
> Herhangi bir değerlendirmenin güvenilirlik derecelendirmesi beş yıldızlı günden azsa, gerecin ortamın profilini oluşturup daha sonra değerlendirmeyi yeniden hesaplayabilmeniz için en az bir gün beklemeniz önerilir. Aksi takdirde, performans tabanlı boyutlandırma güvenilir olmayabilir. Bu durumda, değerlendirmeyi şirket içi boyutlandırılmasına geçmeniz önerilir.

## <a name="monthly-cost-estimation"></a>Aylık maliyet tahmini

Boyutlandırma önerileri tamamlandıktan sonra, Azure geçişi geçişten sonra işlem ve depolama maliyetlerini hesaplar.

- **İşlem maliyeti**: ÖNERILEN Azure VM boyutunu kullanarak Azure GEÇIŞI, VM 'nin aylık maliyetini hesaplamak IÇIN faturalandırma API 'sini kullanır.
    - Hesaplama, işletim sistemi, yazılım güvencesi, ayrılmış örnekler, VM çalışma süresi, konum ve para birimi ayarlarını hesaba göre alır.
    - Toplam aylık işlem maliyetini hesaplamak için tüm makinelerdeki maliyeti toplar.
- **Depolama maliyeti**: bir makine için aylık depolama maliyeti, makineye bağlı tüm disklerin aylık maliyeti aşağıda gösterildiği gibi hesaplanır:
    - Sunucu değerlendirmesi, tüm makinelerin depolama maliyetlerini toplayarak aylık toplam depolama maliyetlerini hesaplar.
    - Şu anda hesaplama, değerlendirme ayarlarında belirtilen teklifleri göz önünde bulundurmaz.

Ücretler, değerlendirme ayarlarında belirtilen para biriminde görüntülenir.


## <a name="next-steps"></a>Sonraki adımlar

[VMware VM](tutorial-assess-vmware.md) 'Leri veya [Hyper-V VM 'leri](tutorial-assess-hyper-v.md)için bir değerlendirme oluşturun.
