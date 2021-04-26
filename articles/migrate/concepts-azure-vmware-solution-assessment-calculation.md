---
title: Azure geçişi 'nde AVS değerlendirmesi hesaplamaları | Microsoft Docs
description: Azure geçişi hizmetindeki AVS değerlendirmesi hesaplamalarına genel bir bakış sağlar.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: b55f0562ae08a6e5a6b38bd0b6fc9f781b94ab64
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078698"
---
# <a name="assessment-overview-migrate-to-azure-vmware-solution"></a>Değerlendirmeye genel bakış (Azure VMware çözümüne geçiş)

[Azure geçişi](migrate-services-overview.md) , şirket içi uygulamalarınızın ve iş yüklerinizin bulmayı, değerlendirmesini ve geçişini izlemek için bir merkezi Merkez sağlar. Ayrıca, özel ve genel bulut örneklerinizi Azure 'da izler. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri sunar.

Azure sanal makinelere ve Azure VMware çözümüne (AVS) geçiş için Azure 'da değerlendirir on-premises Server 'daki bulma ve değerlendirme aracı. Bu makalede, Azure VMware çözümü (AVS) değerlendirmelerinin nasıl hesaplandığı hakkında bilgi sağlanır.

> [!NOTE]
> Azure VMware çözümü (AVS) değerlendirmesi yalnızca VMware VM 'Leri için oluşturulabilir.

## <a name="types-of-assessments"></a>Değerlendirme türleri

Azure geçişi ile oluşturduğunuz değerlendirmeler, verilerin bir zaman noktası anlık görüntüsüdür. Azure geçişi 'ni kullanarak oluşturabileceğiniz iki tür değerlendirme vardır:

**Değerlendirme Türü** | **Ayrıntılar**
--- | --- 
**Azure VM** | Şirket içi sunucularınızı Azure sanal makinelerine geçirmeye yönelik değerlendirmeler. Bu değerlendirme türünü kullanarak, [VMware](how-to-set-up-appliance-vmware.md) ve [Hyper-V](how-to-set-up-appliance-hyper-v.md) ortamındaki şirket Içi sunucularınızı ve Azure VM 'lerine geçiş için [fiziksel sunucuları](how-to-set-up-appliance-physical.md) değerlendirebilirsiniz.
**Azure SQL** | Şirket içi SQL Server 'larınızı VMware ortamınızdan Azure SQL veritabanı 'na veya Azure SQL yönetilen örneğine geçirmeye yönelik değerlendirmeler.
**Azure VMware Çözümü (AVS)** | Şirket içi sunucularınızı [Azure VMware Çözümü'ne (AVS)](../azure-vmware/introduction.md) geçirmeye yönelik değerlendirmeler. Bu değerlendirme türünü kullanarak Azure VMware çözümüne (AVS) geçiş için şirket içi [VMware VM](how-to-set-up-appliance-vmware.md) 'lerinizi değerlendirebilirsiniz. [Daha fazla bilgi edinin](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Bulma ve değerlendirme aracında Azure VM veya AVS değerlendirmesi sayısı yanlışsa, Tüm değerlendirmelere gitmek ve Azure VM veya AVS değerlendirmelerini yeniden hesaplamak için toplam değerlendirme sayısına tıklayın. Bulma ve değerlendirme aracı daha sonra bu değerlendirme türü için doğru sayıyı gösterir. 

Azure VMware çözümü (AVS) değerlendirmesi iki boyutlandırma ölçütü seçeneği sağlar:

| **Değerlendirme** | **Ayrıntılar** | **Veriler** |
| - | - | - |
| **Performans tabanlı** | Şirket içi VM 'lerin toplanan performans verilerine dayanan değerlendirmeler. | **Önerilen düğüm boyutu**: değerlendirme için seçtiğiniz düğüm türü, depolama türü ve FTT ayarı Ile birlikte CPU ve bellek kullanım verilerine göre. |
| **Şirket içi olarak** | Şirket içi boyutlandırmayı temel alan değerlendirmeler. | **Önerilen düğüm boyutu**: değerlendirme için seçtiğiniz düğüm türü, depolama türü ve FTT ayarı ile birlikte ŞIRKET içi VM boyutuna göre. |

## <a name="how-do-i-run-an-assessment"></a>Nasıl yaparım? bir değerlendirme çalıştırmak istiyor musunuz?

Bir değerlendirme çalıştırmak için birkaç yol vardır.

- Hafif bir Azure geçişi gereci tarafından toplanan sunucu meta verilerini kullanarak sunucuları değerlendirin. Gereç, şirket içi sunucuları bulur. Daha sonra Azure geçişi 'ne sunucu meta verilerini ve performans verilerini gönderir. Bu, daha fazla duyarlık sağlar.
- Bir virgülle ayrılmış değerler (CSV) biçiminde içeri aktarılan sunucu meta verilerini kullanarak sunucuları değerlendirin.

## <a name="how-do-i-assess-with-the-appliance"></a>Gereç ile Nasıl yaparım? değerlendirin?

Şirket içi sunucuları bulmaya yönelik bir Azure geçiş gereci dağıtıyorsanız, aşağıdaki adımları uygulayın:

1. Azure 'u ve şirket içi ortamınızı Azure geçişi ile çalışacak şekilde ayarlayın.
2. İlk değerlendirmenize, bir Azure projesi oluşturun ve bulma ve değerlendirme aracını buna ekleyin.
3. Hafif bir Azure geçişi gereci dağıtın. Gereç, şirket içi sunucuları sürekli bulur ve Azure geçişi 'ne sunucu meta verilerini ve performans verilerini gönderir. Gereci bir VM olarak dağıtın. Değerlendirmek istediğiniz sunuculara herhangi bir şey yüklemeniz gerekmez.

Gereç sunucu bulmayı başlattıktan sonra, değerlendirmek istediğiniz sunucuları bir gruba toplayıp değerlendirme türü **Azure VMware çözümü (AVS)** olan grup için bir değerlendirme çalıştırabilirsiniz.

[Buradaki](how-to-create-azure-vmware-solution-assessment.md)adımları Izleyerek Ilk Azure VMware çözümünüz (AVS) değerlendirmesini oluşturun.

## <a name="how-do-i-assess-with-imported-data"></a>İçeri aktarılan verilerle Nasıl yaparım? değerlendirin?

Sunucuları bir CSV dosyası kullanarak değerlendiriyorsanız, bir gereç olması gerekmez. Bunun yerine, aşağıdaki adımları uygulayın:

1. Azure 'ı Azure geçişi ile çalışacak şekilde ayarlayın.
2. İlk değerlendirmenize, bir Azure projesi oluşturun ve bulma ve değerlendirme aracını buna ekleyin.
3. Bir CSV şablonu indirin ve buna sunucu verileri ekleyin.
4. Şablonu Azure geçişi 'ne aktarın.
5. İçeri aktarma ile eklenen sunucuları bulun, bir gruba toplayın ve değerlendirme türü **Azure VMware çözümü (AVS)** olan grup için bir değerlendirme çalıştırın.

## <a name="what-data-does-the-appliance-collect"></a>Gereç hangi verileri toplar?

Değerlendirme için Azure geçişi gereci kullanıyorsanız, [VMware](migrate-appliance.md#collected-data---vmware)için toplanan meta veriler ve performans verileri hakkında bilgi edinin.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Gereç performans verilerini nasıl hesaplar?

Bu gereci bulma işlemi için kullanıyorsanız, aşağıdaki adımlarla işlem ayarları için performans verilerini toplar:

1. Gereç gerçek zamanlı bir örnek noktası toplar.

   - **VMware VM 'leri**: her 20 saniyede bir örnek noktası toplanır.
2. Gereç, her 10 dakikada bir tek bir veri noktası oluşturmak için örnek noktaları birleştirir. Veri noktasını oluşturmak için gereç tüm örneklerden tepe değerlerini seçer. Daha sonra veri noktasını Azure 'a gönderir.
3. Azure geçişi, geçen aya ait 10 dakikalık tüm veri noktalarını depolar.
4. Bir değerlendirme oluşturduğunuzda, değerlendirme için kullanılacak uygun veri noktasını tanımlar. Kimlik, *performans geçmişi* ve *yüzdebirlik kullanımı* için yüzdebirlik değerlerini temel alır.

   - Örneğin, performans geçmişi bir hafta ise ve yüzdebirlik kullanımı 95. yüzdebirlik ise, değerlendirme son hafta için 10 dakikalık örnek noktaları sıralar. Bunları artan düzende sıralar ve doğru bir şekilde yapmak için 95. yüzdebirlik değerini seçer.
   - 95. yüzdebirlik değeri, 99. yüzdebirlik değerini seçtiğiniz takdirde dahil olabilen tüm aykırı değerleri yok saydığınızdan emin olmanızı sağlar.
   - Dönemin en yoğun kullanımını seçmek ve tüm aykırı değerleri kaçırmak istemiyorsanız, yüzdebirlik kullanımı için 99. yüzdebirlik ' ü seçin.
5. Bu değer, gerecin topladığı bu ölçümler için etkili performans kullanım verilerini elde etmek için, rahatlık faktörüyle çarpılır:

   - CPU kullanımı
   - RAM kullanımı

Aşağıdaki performans verileri toplanır ancak AVS değerlendirmelerine yönelik boyutlandırma önerilerinde kullanılmaz:

- SANAL makineye bağlı her diske yönelik disk ıOPS ve aktarım hızı verileri.
- Bir VM 'ye bağlı her bir ağ bağdaştırıcısı için performans tabanlı boyutlandırmayı işlemek üzere ağ g/ç.

## <a name="how-are-avs-assessments-calculated"></a>AVS değerlendirmesi nasıl hesaplanır?

AVS değerlendirmesi, değerlendirmeleri hesaplamak için şirket içi sunucuların meta verilerini ve performans verilerini kullanır. Azure geçişi gerecini dağıtırsanız, değerlendirme gereç tarafından toplanan verileri kullanır. Ancak bir CSV dosyası kullanarak içeri aktarılan bir değerlendirmeyi çalıştırırsanız, hesaplama için meta verileri sağlarsınız.

Hesaplamalar şu üç aşamada gerçekleşir:

1. **Azure VMware Solution (AVS) hazırlığını hesapla**: Şirket Içi VM 'Lerin Azure VMware çözümüne (AVS) geçiş için uygun olup olmadığı.
2. **Düğüm GENELINDEKI AVS düğümlerinin ve kullanımının sayısını hesapla**: tüm düğümlerde VMware VM 'lerini çalıştırmak için gereken ve tahmini CPU, bellek ve depolama kullanımı IÇIN beklenen AVS düğüm sayısı.
3. **Aylık maliyet tahmini**: Şirket Içi VM 'leri çalıştıran tüm Azure VMware çözümü (AVS) düğümlerine yönelik tahmini aylık maliyetler.

Hesaplamalar önceki sıradadır. Sunucu daha sonraki bir aşamaya geçer ve yalnızca bir öncekini geçerse. Örneğin, bir sunucu AVS hazırlık aşamasına geçemezse, Azure için uygun değil olarak işaretlenir. Bu sunucu için boyutlandırma ve maliyet hesaplamaları yapılmadı

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Çözüm (AVS) değerlendirmesi nelerdir?

Aşağıda bir AVS değerlendirmesi verilmiştir:

| **Özellik** | **Ayrıntılar** |
| - | - |
| **Hedef konum** | Geçirmek istediğiniz AVS özel bulut konumunu belirtir. |
| **Depolama türü** | AVS 'de kullanılacak depolama altyapısını belirtir. AVS Şu anda yalnızca varsayılan depolama türü olarak vSAN 'ı destekliyor, ancak yol haritası başına daha fazla depolama seçeneği sunulacak. |
| **Ayrılmış örnekler (RIS)** | Bu özellik, satın alınan ve ayrılmış örnek teriminin, AVS 'de ayrılmış örnekler belirtmenize yardımcı olur. Maliyetleri hesaplamak için kullanılır. |
| **Düğüm türü** | Azure 'da kullanılmak üzere kullanılan [AVS düğüm türünü](../azure-vmware/concepts-private-clouds-clusters.md) belirtir. Varsayılan düğüm türü AV36 ' dir. Daha fazla düğüm türü gelecekte kullanılabilir olabilir.  Azure geçişi, sanal makinelerin AVS 'ye geçirilmesi için gerekli sayıda düğüm önermenizi önerir. |
| **FTT ayarı, RAID düzeyi** | Tolerans ve RAID birleşimlerine yönelik hataların geçerli birleşimini belirtir. RAID düzeyiyle birleştirilmiş seçili FTT seçeneği ve şirket içi VM diski gereksinimi, AVS 'de gereken toplam vSAN depolama alanını tespit edecektir. Hesaplamalardan sonra toplam kullanılabilir depolama alanı, vCenter ve b gibi yönetim nesneleri için ayrılmış bir alan içerir) vSAN işlemleri için %25 depolama bolluğu gerekir. |
| **Boyutlandırma ölçütü** | AVS düğümlerine yönelik bellek, CPU ve depolama gereksinimlerini belirlemede kullanılacak ölçütleri ayarlar. Performans geçmişini dikkate almadan *performans tabanlı* boyutlandırmayı veya *Şirket içi olarak* tercih edebilirsiniz. Yalnızca kaldırmak ve açmak için şirket içi olarak seçin. Kullanım tabanlı boyutlandırmayı almak için performans tabanlı ' ı seçin. |
| **Performans geçmişi** | Sunucuların performans verilerini değerlendirmek için göz önünde bulundurulması gereken süreyi ayarlar. Bu özellik yalnızca boyutlandırma ölçütü *performans tabanlı* olduğunda geçerlidir. |
| **Yüzdebirlik kullanımı** | Sağ boyutlandırmanın kabul edileceği performans örneği kümesinin yüzdebirlik değerini belirtir. Bu özellik yalnızca boyutlandırma performans tabanlı olduğunda geçerlidir. |
| **Konfor katsayısı** | Azure Geçişi, değerlendirme sırasında bir tamponu (konfor katsayısı) göz önünde bulundurur. Bu arabellek VM 'Ler (CPU, bellek ve disk) için sunucu kullanım verilerinin üzerine uygulanır. Konfor katsayısı; sezona özgü kullanım, kısa performans geçmişi ve gelecek kullanımlarda oluşabilecek artışlar gibi konuları hesaba katar. Örneğin, %20 kullanıma sahip 10 çekirdekli bir VM normalde 2 çekirdekli VM ile sonuçlanır. Ancak, 2.0x konfor katsayısı ile sonuç 4 çekirdekli VM olur. |
| **Teklif** | Kayıtlı olduğunuz [Azure teklifini](https://azure.microsoft.com/support/legal/offer-details/) görüntüler. Azure Geçişi, buna göre bir maliyet tahmini oluşturur. |
| **Para Birimi** | Hesabınız için faturalandırma para birimini gösterir. |
| **İndirim (%)** | Azure teklifinin üzerine aldığınız aboneliğe özgü tüm indirimi listeler. Varsayılan ayar, %0’dır. |
| **Azure Hibrit Avantajı** | Yazılım Güvencesi olup olmadığını ve [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-use-benefit/)uygun olduğunu belirtir. Düğüm tabanlı fiyat nedeniyle Azure VMware Çözüm fiyatlandırması üzerinde hiçbir etkisi olmamasına karşın, müşteriler Azure hibrit avantajlarını kullanarak AVS 'de şirket içi işletim sistemi veya SQL lisanslarını (Microsoft tabanlı) uygulayabilir. Diğer yazılım işletim sistemi satıcılarının, RHEL gibi kendi lisans şartlarını sağlaması gerekecektir. |
| **vCPU fazla abonelik** | AVS düğümündeki bir fiziksel çekirdeğe bağlı sanal çekirdek sayısının oranını belirtir. Hesaplamalardaki varsayılan değer 4 vCPU: AVS 'de 1 fiziksel çekirdek olur. API kullanıcıları bu değeri bir tamsayı olarak ayarlayabilir. VCPU fazla aboneliği > 4:1 ' nin, CPU kullanımına bağlı olarak iş yüklerini etkileyebileceğini unutmayın. Boyutlandırma sırasında her zaman seçili çekirdekleri %100 oranında kullanım varsayıyoruz. |
| **Bellek fazla kayıt faktörü** | Kümedeki işlemeye göre bellek oranını belirtir. 1 değeri %100 bellek kullanımını temsil eder, 0,5 Örneğin %50 ve 2 kullanılabilir belleğin %200 ' i kullanıyor. Yalnızca 0,5 ' den 10 ' a kadar ondalık bir yere değer ekleyebilirsiniz. |
| **Dedupe ve sıkıştırma faktörü** | İş yükleriniz için beklenen yinelenenleri kaldırma ve sıkıştırma etmenini belirtir. Şirket içi vSAN veya depolama yapılandırmadan gerçek değer elde edilebilir. Bunlar iş yüküne göre farklılık gösterir. 3 değeri, 300 GB disk için yalnızca 100 GB depolama alanı için 3x anlamına gelir. 1 değeri, hiç dupe veya Compression anlamına gelir. Yalnızca 1 ' den 10 ' a kadar ondalık bir yere değerler ekleyebilirsiniz. |

## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Azure VMware çözümü (AVS) uygunluk Analizi

AVS değerlendirmeleri, sunucu özelliklerini inceleyerek her şirket içi VM 'yi AVS 'ye uygunluk açısından değerlendirir. Ayrıca, her bir Değerlendirilmiş sunucuyu aşağıdaki uygunluk kategorilerinden birine atar:

- **AVS Için hazırlanma**: sunucu, herhangi bir değişiklik yapılmadan Azure 'A (AVS) olarak geçirilebilir. Tam AVS desteğiyle AVS 'de başlatılır.
- **Koşullara göre**: VM, geçerli vSphere sürümü ile uyumluluk sorunlarına ve VM 'deki tam işlevselliği AVS 'de elde etmeden önce büyük olasılıkla VMware araçları ve veya diğer ayarları gerektirmek olabilir.
- **AVS için hazırlanma**: VM, AVS 'de başlamaz. Örneğin, şirket içi VMware VM 'sinde CD-ROM gibi bir dış cihaz varsa VMware VMotion işlem başarısız olur (VMware VMotion kullanılıyorsa).
- **Hazır olma bilinmiyor**: Azure geçişi, şirket içi ortamdan toplanan meta veriler nedeniyle sunucunun hazır olduğunu saptayamadık.

Değerlendirme, sunucu özelliklerini gözden geçirir ve şirket içi sunucunun Azure 'un hazır olduğunu tespit edin.

### <a name="server-properties"></a>Sunucu özellikleri

Değerlendirme, şirket içi sanal makinenin aşağıdaki özelliğini inceleyip Azure VMware çözümünde (AVS) çalışıp çalışmadığını tespit edebilir.

| **Özellik** | **Ayrıntılar** | **AVS hazırlık durumu** |
| - | - | - |
| **Internet Protokolü** | Azure Şu anda uçtan uca IPv6 internet adreslemesini desteklemez. Sunucunuz IPv6 ile algılanıyorsa düzeltme kılavuzumuzu göstermek için yerel MSFT AVS GBB ekibine başvurun. | Koşullu olarak Ready Internet Protokolü |

### <a name="guest-operating-system"></a>Konuk işletim sistemi

Şu anda, AVS değerlendirmelerinde, uygunluk analizinin bir parçası olarak işletim sistemi incelenmez. Şirket içi VM 'lerde çalışan tüm işletim sistemleri, Azure VMware çözümünde (AVS) çalışıyor olabilir.

VM özellikleriyle birlikte, değerlendirme, Azure 'da çalışıp çalışmadığını öğrenmek için sunucuların Konuk işletim sistemine bakar.

## <a name="sizing"></a>Boyutlandırma

Sunucu AVS için hazırlanın olarak işaretlendikten sonra, AVS değerlendirmesi, uygun şirket içi VM gereksinimlerini tanımlamayı ve gereken AVS düğümlerinin toplam sayısını bulmayı içeren düğüm boyutlandırma önerilerini oluşturur. Bu öneriler, belirtilen değerlendirme özelliklerine bağlı olarak farklılık gösterir.

- Değerlendirme *performans tabanlı boyutlandırma* kullanıyorsa, Azure GEÇIŞI, AVS için uygun boyutlandırma önerisi yapmak üzere sunucunun performans geçmişini dikkate alır. Bu yöntem, şirket içi VM 'yi aşırı ayırdıysanız ancak kullanım düşükse ve maliyetleri kazanmak için AVS 'de sanal makineyi doğru boyuta eklemek istiyorsanız özellikle yararlıdır. Bu yöntem, geçiş sırasında boyutları iyileştirmenize yardımcı olur.
- VM boyutlandırma için performans verilerini göz önünde bulundurmayın ve şirket içi sunucuları, AVS 'ye kadar yapmak istiyorsanız, boyutlandırma ölçütünü "Şirket içi" olarak ayarlayabilirsiniz. Daha sonra değerlendirme, VM 'Leri kullanım verilerini dikkate almadan şirket içi yapılandırmaya göre boyutlandıracaktır.

### <a name="ftt-sizing-parameters"></a>FTT boyutlandırma parametreleri

AVS 'de kullanılan depolama altyapısı vSAN ' dır. vSAN depolama ilkeleri sunucularınız için depolama gereksinimlerini tanımlar. Bu ilkeler sanal makineler için gerekli hizmet düzeyini garanti eder çünkü depolamanın sanal makineye nasıl ayrıldığını belirler. Kullanılabilir FTT-Raid birleşimleri şunlardır:

| **Tolerans Hataları (FTT)** | **RAID Yapılandırması** | **Gereken Minimum Konak Sayısı** | **Boyutlandırmada dikkat edilmesi gerekenler** |
| - | - | - | - |
| 1 | RAID-1 (Yansıtma) | 3 | 100 GB sanal makine 200 GB kullanır. |
| 1 | RAID-5 (Silinme Kodlaması) | 4 | 100 GB sanal makine 133,33 GB kullanır |
| 2 | RAID-1 (Yansıtma) | 5 | 100 GB sanal makine 300 GB kullanır. |
| 2 | RAID-6 (Silinme Kodlaması) | 6 | 100 GB sanal makine 150 GB kullanır. |
| 3 | RAID-1 (Yansıtma) | 7 | 100 GB sanal makine 400 GB kullanır. |

### <a name="performance-based-sizing"></a>Performans tabanlı boyutlandırma

Performans tabanlı boyutlandırma için, Azure geçişi gereç profilleri, CPU, bellek ve disk için performans verilerini toplamak üzere şirket içi ortamı sağlar. Bu nedenle, AVS için performans tabanlı boyutlandırma, ayrılan disk alanını dikkate alacak ve bellek ve CPU 'nun seçili yüzdebirlik kullanımını kullanmaktır. Örneğin, bir VM 'nin ayrılmış 4Vcore varsa ancak yalnızca %25 ' i kullanıyorsa, bu VM için AVS 'nin boyutu 1 sanal çekirdek olur.

**Performans verileri toplama adımları:**

1. VMware VM 'Leri için Azure geçişi gereci, her 20 saniyelik aralığa göre gerçek zamanlı bir örnek noktası toplar.
2. Gereç, her 10 dakikada bir toplanan örnek noktaların dökümünü yapar ve son 10 dakikalık en büyük değeri Azure geçişi 'ne gönderir.
3. Azure geçişi, son bir ay için 10 dakikalık tüm örnek noktalarını depolar. Daha sonra,*performans geçmişi* ve *yüzdebirlik kullanımı* için belirtilen değerlendirme özelliklerine bağlı olarak, doğru boyutlandırma için kullanılacak uygun veri noktasını tanımlar. Örneğin, performans geçmişi 1 güne ayarlanmışsa ve yüzdebirlik kullanımı 95. yüzdebirlik ise, Azure geçişi son bir gün için 10 dakikalık örnek noktalarını kullanır, bunları artan düzende sıralar ve sağ boyutlandırma için 95. yüzdebirlik değerini seçer.
4. Bu değer, gerecin topladığı her ölçüm için etkili performans kullanım verilerini (CPU kullanımı ve bellek kullanımı) elde etmek için, rahatlık faktörüyle çarpılır.

Etkin kullanım değeri saptandıktan sonra, depolama, ağ ve bilgi işlem boyutu aşağıdaki şekilde işlenir.

**Depolama boyutlandırma**: Azure geçişi, müşteriler tarafından seçilen FTT ayarının yanı sıra AVS vSAN depolama gereksinimlerini belirlemede bir hesaplama parametresi olarak toplam ŞIRKET içi VM disk alanını kullanır. FTT-tolerans başına en az sayıda düğüm gerektirme ve FTT seçeneği için gereken toplam vSAN depolama alanı, VM disk gereksinimiyle birlikte gerekli olan toplam vSAN depolamayı tespit eder. Depolama kullanımı Şu anda dikkate alınmaz ve mantık yalnızca VM başına ayrılmış depolamaya bakar.

**Ağ boyutlandırma**: AVS değerlendirmesi Şu anda hiçbir ağ ayarı dikkate almaz.

**İşlem boyutlandırma**: depolama gereksinimlerini hesapladıktan sonra, AVS değerlendirmesi CPU ve bellek gereksinimlerini, düğüm türüne göre AVS için gereken düğüm sayısını belirleyecek şekilde değerlendirir.

- AVS değerlendirmesi, boyutlandırma ölçütlerine göre performans tabanlı VM verilerine veya şirket içi VM yapılandırmasına göre görünür. Rahatlık faktörü ayarı, kümenin büyüme faktörünü belirtmenize olanak tanır. Şu anda varsayılan olarak hyperthreading etkinleştirilmiştir ve bu nedenle 36 çekirdekli düğümlerin 72 sanal çekirdeği olur. Küme kullanılabilirliğini riske atmadan bakım sağlamak veya hataları işlemek üzere WMware'in %80 kullanımı aşmama standardını kullanarak küme başına CPU eşiklerini belirlemek için fiziksel disk başına 4 sanal çekirdek kullanılır. Şu anda, fazla abonelik değerlerini değiştirmek için kullanılabilecek geçersiz kılma yok ve bu, gelecekteki sürümlerde bu durum olabilir.

### <a name="as-on-premises-sizing"></a>Şirket içi boyutlandırma olarak

*Şirket içi boyutlandırma olarak* kullanıyorsanız, AVS değerlendirmesi VM 'lerin ve disklerin performans geçmişini dikkate almaz. Bunun yerine, şirket içinde ayrılan boyuta göre AVS düğümlerini ayırır. Varsayılan depolama türü, AVS 'deki vSAN ' dır.

Azure VMware Çözüm değerlendirmesini İnceleme hakkında [daha fazla bilgi edinin](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment) .

### <a name="cpu-utilization-on-avs-nodes"></a>AVS düğümlerinde CPU kullanımı

CPU kullanımı, kullanılabilir çekirdekler için %100 kullanım olduğunu varsayar. Gerekli düğüm sayısını azaltmak için, abonelik yükünü 4:1 adresinden artırabilir ve bu da iş yükü özelliklerine ve şirket içi deneyimlere göre 6:1 deyin. Disk 'nin aksine, AVS CPU kullanımı için herhangi bir sınırlama yerleştirmez, bu sayede "sık çalıştırılan" çalıştırma "özelliği gerektiği şekilde ayarlanır. Büyümeye daha fazla odaya izin vermek için, fazla aboneliği azaltın veya büyüme faktörü değerini artırın.

CPU kullanımı, vCenter, NSX Manager ve diğer daha küçük kaynaklardan yönetim yükü için de zaten hesaplardır.

### <a name="memory-utilization-on-avs-nodes"></a>AVS düğümlerinde bellek kullanımı

Bellek kullanımı, tüm düğümlerin ve sunucu ya da iş yüklerinden gelen gereksinimlere ait toplam belleği gösterir. Bellek, abone olunmuş olabilir ve bu durumda AVS hiçbir sınır yerleştiriyor ve iş yükleri için en iyi küme performansını çalıştırmak için müşteriye sahiptir.

Bellek kullanımı, vCenter, NSX Manager ve diğer daha küçük kaynaklardan yönetim yükü için de zaten hesaplardır.

### <a name="storage-utilization-on-avs-nodes"></a>AVS düğümlerinde depolama kullanımı

Depolama kullanımı aşağıdaki sıraya göre hesaplanır:

1. VM için gereken boyut (ya ya da performans tabanlı kullanılan alan olarak ayrılır)
2. Varsa büyüme faktörünü Uygula
3. Yönetim ek yükü ekleme ve FTT oranını uygulama
4. Yinelenenleri kaldırma ve sıkıştırma faktörünü Uygula
5. VSAN için gerekli %25 bolluğu Uygula
6. Yönetim yükü dahil olmak üzere toplam depolama alanı dışında VM 'Ler için kullanılabilir sonuç.

3 düğümlü bir küme üzerindeki kullanılabilir depolama alanı, RAID-1 olan ve kalın sağlama kullanan varsayılan depolama ilkesini temel alır. Silinme kodlaması veya RAID-5 için hesaplanırken, en az 4 düğüm gerekir. AVS 'de depolama ilkesinin daha fazla alana izin vermek için yönetici tarafından değiştirilmesi gerektiğini unutmayın.

## <a name="confidence-ratings"></a>Güven derecelendirmeleri

Azure geçişi 'ndeki her performans tabanlı değerlendirme, bir (en düşük) ile beş yıldızlı (en yüksek) arasında değişen bir güvenirlik derecelendirmesi ile ilişkilendirilir.

- Güvenilirlik derecelendirmesi, değerlendirmeyi hesaplamak için gereken veri noktalarının kullanılabilirliği temelinde bir değerlendirmeye atanır.
- Bir değerlendirmenin güvenilirlik derecesi, Azure Geçişi tarafından sağlanan boyut önerilerinin güvenilirliğini tahmin etmenize yardımcı olur.
- Güven derecelendirmeleri, *Şirket içi* değerlendirmelere uygun değildir.
- Performans tabanlı boyutlandırma için, AVS değerlendirmelerinin CPU ve VM belleği için kullanım verilerine ihtiyacı vardır. Aşağıdaki veriler toplanır ancak AVS için boyutlandırma önerilerinde kullanılmaz:

  - SANAL makineye bağlı her diske yönelik disk ıOPS ve aktarım hızı verileri.
  - Bir VM 'ye bağlı her bir ağ bağdaştırıcısı için performans tabanlı boyutlandırmayı işlemek üzere ağ g/ç.

  Bu kullanım numaralarının herhangi biri vCenter Server kullanılamazsa, boyut önerisi güvenilir olmayabilir.

Kullanılabilir veri noktalarının yüzdesine bağlı olarak, değerlendirmenin güvenilirlik derecelendirmesi aşağıdaki gibi gider.

| **Veri noktalarının kullanılabilirliği** | **Güvenilirlik derecelendirmesi** |
| - | - |
| % 0-20 | 1 yıldız |
| % 21-40 | 2 yıldız |
| % 41-60 | 3 yıldız |
| % 61-80 | 4 yıldız |
| % 81-100 | 5 yıldız |

### <a name="low-confidence-ratings"></a>Düşük güvenilirlikli derecelendirmeler

Bir değerlendirmenin en düşük güvenilirlik derecelendirmesinin neden olmasının birkaç nedeni aşağıda verilmiştir:

- Değerlendirmeyi oluşturduğunuz süre boyunca ortamınızın profilini oluşturmadınız. Örneğin, performans süresi ile bir gün ayarlanmış bir değerlendirme oluşturursanız, toplanan tüm veri noktaları için bulmayı başlattıktan sonra en az bir gün beklemeniz gerekir.
- Değerlendirme, değerlendirme süresinde sanal makinelerin bazıları veya tümü için performans verilerini toplayamayabilir. Yüksek güvenilirlikli bir derecelendirme için lütfen şunları doğrulayın:

  - Değerlendirme süresi boyunca VM 'Ler açık
  - 443 bağlantı noktalarında giden bağlantılara izin verilir
  - Hyper-V VM 'Leri için dinamik bellek etkin

  Güvenilirlik derecelendirmesindeki en son değişiklikleri yansıtacak şekilde değerlendirmeyi 'Yeniden Hesaplayın'.
- Değerlendirmenin hesaplanışında bazı sanal makineler oluşturulmuştur. Örneğin, geçen ayın performans geçmişi için bir değerlendirme oluşturdunuz, ancak bazı VM 'Ler yalnızca bir hafta önce oluşturulmuştur. Bu durumda, sürenin tamamında yeni VM'lerin performans verileri sağlanmaz ve güvenilirlik derecesi düşük olabilir.

> [!NOTE]
> Herhangi bir değerlendirmenin güvenilirlik derecelendirmesi beş yıldızlı günden azsa, gerecin ortamın profilini oluşturup daha sonra değerlendirmeyi yeniden hesaplayabilmeniz için en az bir gün beklemeniz önerilir. Aksi takdirde, performans tabanlı boyutlandırma güvenilir olmayabilir. Bu durumda, değerlendirmeyi şirket içi boyutlandırılmasına geçmeniz önerilir.

## <a name="monthly-cost-estimation"></a>Aylık maliyet tahmini

Boyutlandırma önerileri tamamlandıktan sonra, Azure geçişi, düğüm fiyatının gerektirdiği AVS düğümlerinin sayısını çarparak, AVS 'de şirket içi iş yüklerini çalıştırmanın toplam maliyetini hesaplar. VM başına maliyet maliyeti, toplam maliyeti değerlendirmede bulunan VM sayısına bölerek hesaplanır.

- Hesaplama, gerekli düğüm sayısını, düğüm türünü ve konumu hesaba göre alır.
- Toplam aylık maliyeti hesaplamak için tüm düğümlerdeki maliyeti toplar.
- Ücretler, değerlendirme ayarlarında belirtilen para biriminde görüntülenir.

Azure VMware çözümü (AVS) fiyatlandırması düğüm başına olduğunda, toplam maliyet işlem maliyeti ve depolama maliyeti dağıtımına sahip değildir. [Daha Fazla Bilgi](../azure-vmware/introduction.md)

## <a name="migration-tool-guidance"></a>Geçiş Aracı Kılavuzu

Azure VMware Çözümü (AVS) değerlendirmesinin Azure için hazır olma raporunda aşağıdaki önerilen araçları görebilirsiniz:

- VMware **HCX veya Enterprise**: VMware sunucuları Için VMware karma bulut uzantısı (HCX) çözümü, şirket içi iş yükünüzü Azure VMware çözümünüz (AVS) özel bulutuna geçirmek için önerilen geçiş aracıdır. [Daha Fazla Bilgi Edinin](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Bilinmiyor**: CSV dosyası aracılığıyla içeri aktarılan sunucular için varsayılan geçiş aracı bilinmez. Ancak VMware sunucuları için, VMware karma bulut uzantısı (HCX) çözümünün kullanılması önerilir.

## <a name="next-steps"></a>Sonraki adımlar

[AVS VMware VM 'leri](how-to-create-azure-vmware-solution-assessment.md)için bir değerlendirme oluşturun.
