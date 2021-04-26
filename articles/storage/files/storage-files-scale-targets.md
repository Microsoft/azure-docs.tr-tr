---
title: Azure Dosyaları ölçeklenebilirlik ve performans hedefleri
description: Kapasite, istek hızı ve gelen ve giden bant genişliği sınırları dahil olmak üzere Azure dosyaları için ölçeklenebilirlik ve performans hedefleri hakkında bilgi edinin.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bdcee6b8ced45dba34309724e5a634cbb60a6d37
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818827"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure Dosyaları ölçeklenebilirlik ve performans hedefleri
[Azure dosyaları](storage-files-introduction.md) , BULUTTA, SMB ve NFS dosya sistemi protokolleri aracılığıyla erişilebilen tam olarak yönetilen dosya paylaşımları sunar. Bu makalede, Azure dosyaları ve Azure Dosya Eşitleme için ölçeklenebilirlik ve performans hedefleri ele alınmaktadır.

Burada listelenen ölçeklenebilirlik ve performans hedefleri, yüksek kaliteli hedeflerdir, ancak dağıtımınızdaki diğer değişkenlerden etkilenebilir. Örneğin, bir dosya için üretilen iş, yalnızca Azure dosya paylaşımlarınızı barındıran sunucular değil, kullanılabilir ağ bant genişliğiniz tarafından da sınırlanabilir. Azure dosyalarının ölçeklenebilirlik ve performansının gereksinimlerinizi karşılayıp karşılamadığını öğrenmek için kullanım modelinizi test etmenizi kesinlikle öneririz. Bu limitleri zaman içinde artırmayı de taahhüt ediyoruz. 

## <a name="azure-files-scale-targets"></a>Azure Dosyalar ölçek hedefleri
Azure dosya paylaşımları, paylaşılan bir depolama havuzunu temsil eden üst düzey nesneler olan depolama hesaplarına dağıtılır. Bu depolama havuzu, birden çok dosya paylaşımını dağıtmak için kullanılabilir. Bu nedenle dikkate alınması gereken üç kategori vardır: depolama hesapları, Azure dosya paylaşımları ve dosyalar.

### <a name="storage-account-scale-targets"></a>Depolama hesabı ölçek hedefleri
Azure, müşterilerin sahip olabileceği farklı depolama senaryoları için birden çok depolama hesabı türünü destekler, ancak Azure dosyaları için iki ana depolama hesabı türü vardır. Oluşturmanız gereken depolama hesabı türü, standart bir dosya paylaşma veya Premium dosya paylaşımının oluşturulmasını isteyip istemediğinize bağlıdır: 

- **Genel amaçlı sürüm 2 (GPv2) depolama hesapları**: GPv2 depolama hesapları, Azure dosya paylaşımlarını standart/sabit disk tabanlı (HDD tabanlı) donanımda dağıtmanıza olanak tanır. GPv2 depolama hesapları, Azure dosya paylaşımlarını depolamanın yanı sıra blob kapsayıcıları, kuyrukları veya tabloları gibi diğer depolama kaynaklarını da depolayabilirler. Dosya paylaşımları, işlem için iyileştirilmiş (varsayılan), sık veya seyrek katmanlara dağıtılabilir.

- **FileStorage depolama hesapları**: FileStorage depolama hesapları, Azure dosya paylaşımlarını Premium/katı hal disk tabanlı (SSD tabanlı) donanımda dağıtmanıza olanak tanır. FileStorage hesapları yalnızca Azure dosya paylaşımlarını depolamak için kullanılabilir; başka depolama kaynakları (blob kapsayıcıları, kuyruklar, tablolar vb.) bir FileStorage hesabında dağıtılabilir.

| Öznitelik | GPv2 depolama hesapları (Standart) | FileStorage depolama hesapları (Premium) |
|-|-|-|
| Abonelik başına bölge başına depolama hesabı sayısı | 250 | 250 |
| Maksimum depolama hesabı kapasitesi | 5 PiB<sup>1</sup> | 100 TiB (sağlandı) |
| En fazla dosya paylaşımı sayısı | Sınırsız | Sınırsız, tüm paylaşımların toplam sağlanan boyutu en fazla depolama hesabı kapasitesinden daha az olmalıdır |
| Maksimum eşzamanlı istek hızı | 20.000 ıOPS<sup>1</sup> | 100.000 ıOPS |
| Maksimum giriş | <ul><li>ABD/Avrupa: 9.536 MIB/sn<sup>1</sup></li><li>Diğer bölgeler (LRS/ZRS): 9.536 MiB/sec<sup>1</sup></li><li>Diğer bölgeler (GRS): 4.768 GiB/sec<sup>1</sup></li></ul> | 4.136 MIB/sn |
| Maksimum çıkış | 47.683 MIB/sn<sup>1</sup> | 6.204 MIB/sn |
| En fazla sanal ağ kuralı sayısı | 200 | 200 |
| En fazla IP adresi kuralı sayısı | 200 | 200 |
| Yönetim okuma işlemleri | 5 dakikada 800 | 5 dakikada 800 |
| Yönetim yazma işlemleri | saniyede 10/1200 saat | saniyede 10/1200 saat |
| Yönetim listesi işlemleri | 5 dakikada 100 | 5 dakikada 100 |

<sup>1</sup> genel amaçlı sürüm 2 depolama hesapları, isteğe göre giriş için daha yüksek kapasite sınırlarını ve daha yüksek limitleri destekler. Hesap sınırlarında artış istemek için [Azure desteği](https://azure.microsoft.com/support/faq/)'ne başvurun.

### <a name="azure-file-share-scale-targets"></a>Azure dosya paylaşma ölçek hedefleri
| Öznitelik | Standart dosya paylaşımları<sup>1</sup> | Premium dosya paylaşımları |
|-|-|-|
| Dosya paylaşımının en küçük boyutu | En az | 100 GiB (sağlandı) |
| Sağlanan boyut artış/azaltma birimi | Yok | 1 GiB |
| Dosya paylaşımının en büyük boyutu | <ul><li>büyük dosya paylaşma<sup>özelliği etkin olan</sup> 100 Tib</li><li>5 TiB, varsayılan</li></ul> | 100 TiB |
| Dosya paylaşımındaki en fazla dosya sayısı | Sınır yok | Sınır yok |
| En yüksek istek oranı (maksimum ıOPS) | <ul><li>10.000, büyük dosya paylaşma özelliği etkinleştirilmiş<sup>2</sup></li><li>1.000 veya 100/100 ms, varsayılan</li></ul> | <ul><li>Temel ıOPS: GiB başına 400 + 1 ıOPS, en fazla 100.000</li><li>IOPS patlama: en fazla 100.000, GiB başına maksimum (4000, 3x ıOPS), 'e kadar</li></ul> |
| Tek bir dosya paylaşımının en büyük girişi | <ul><li>Büyük dosya paylaşma özelliği etkin olan en fazla 300 MIB/sn<sup>2</sup></li><li>En fazla 60 MIB/sn, varsayılan</li></ul> | 40 MIB/s + 0,04 * sağlanan GiB |
| Tek bir dosya paylaşımında maksimum çıkış | <ul><li>Büyük dosya paylaşma özelliği etkin olan en fazla 300 MIB/sn<sup>2</sup></li><li>En fazla 60 MIB/sn, varsayılan</li></ul> | 60 MIB/s + 0,06 * sağlanan GiB |
| Maksimum paylaşılan anlık görüntü sayısı | 200 anlık görüntü | 200 anlık görüntü |
| En fazla nesne (Dizin ve dosya) adı uzunluğu | 2.048 karakter | 2.048 karakter |
| En fazla yol adı bileşeni (\A\B\C\D yolunda her bir bir bileşendir) | 255 karakter | 255 karakter |
| Sabit bağlantı sınırı (yalnızca NFS) | Yok | 178 |
| En fazla SMB çok kanallı kanal sayısı | YOK | 4 |
| Dosya paylaşma başına en fazla depolanan erişim ilkesi sayısı | 5 | 5 |

<sup>1</sup> standart dosya paylaşımları için sınırlar Standart dosya paylaşımları için kullanılabilen her üç katmanda geçerlidir: işlem için iyileştirilmiş, sık erişimli ve seyrek.

<sup>2</sup> standart dosya paylaşımlarında varsayılan değer 5 TİB 'dir ve standart dosya paylaşımlarının ölçeğini 100 TİB 'ye kadar artırma hakkında daha fazla bilgi için bkz. [büyük dosya paylaşımlarını etkinleştirme ve oluşturma](./storage-files-how-to-create-large-file-share.md) .

### <a name="file-scale-targets"></a>Dosya ölçeği hedefleri
| Öznitelik | Standart dosya paylaşımlarındaki dosyalar  | Premium dosya paylaşımlarındaki dosyalar  |
|-|-|-|
| En büyük dosya boyutu | 4 TiB | 4 TiB |
| Maksimum eşzamanlı istek hızı | 1.000 ıOPS | 8.000 adede kadar<sup>1</sup> |
| Bir dosya için maksimum giriş | 60 MIB/sn | 200 MIB/sn (çok kanallı SMB önizleme ile 1 GiB/sn 'ye kadar)<sup>2</sup>|
| Bir dosya için maksimum çıkış | 60 MIB/sn | 300 MIB/sn (çok kanallı SMB önizleme ile 1 GiB/sn 'ye kadar)<sup>2</sup> |
| Maksimum eşzamanlı tanıtıcı | 2.000 tanıtıcı | 2.000 tanıtıcı  |

<sup>1, okuma ve yazma Için geçerlidir (genellikle 64 KiB 'den küçük veya buna EŞIT GÇ boyutları). Okuma ve yazma dışındaki meta veri işlemleri daha düşük olabilir.</sup> 
 <sup>2 makine ağı sınırlarına, kullanılabilir bant genişliğine, GÇ boyutlarına, sıra derinliğine ve diğer faktörlere tabidir. Ayrıntılar için bkz. çok [kanallı SMB performansı](./storage-files-smb-multichannel-performance.md).</sup>

## <a name="azure-file-sync-scale-targets"></a>Azure Dosya Eşitleme ölçek hedefleri
Aşağıdaki tabloda Microsoft 'un test olan sınırları ve hangi hedeflerin sabit sınırları olduğunu gösterilmektedir:

| Kaynak | Hedef | Sabit sınır |
|----------|--------------|------------|
| Bölge başına depolama Eşitleme Hizmetleri | 100 depolama Eşitleme Hizmetleri | Yes |
| Depolama eşitleme hizmeti başına eşitleme grupları | 200 eşitleme grupları | Yes |
| Depolama eşitleme hizmeti başına kayıtlı sunucular | 99 sunucu | Yes |
| Eşitleme grubu başına bulut uç noktaları | 1 bulut uç noktası | Yes |
| Eşitleme grubu başına sunucu uç noktaları | 100 sunucu uç noktaları | Yes |
| Sunucu başına sunucu uç noktaları | 30 sunucu uç noktası | Yes |
| Eşitleme grubu başına dosya sistemi nesneleri (dizinler ve dosyalar) | 100.000.000 nesneleri | No |
| Bir dizindeki en fazla dosya sistemi nesnesi (Dizin ve dosya) sayısı | 5.000.000 nesneleri | Yes |
| En fazla nesne (dizinler ve dosyalar) güvenlik tanımlayıcısı boyutu | 64 KiB | Yes |
| Dosya boyutu | 100 GiB | No |
| Katman oluşturulacak bir dosya için en küçük dosya boyutu | V9 ve daha yeni: dosya sistemi kümesi boyutuna (çift dosya sistemi kümesi boyutu) göre. Örneğin, dosya sistemi kümesi boyutu 4 KiB ise, en küçük dosya boyutu 8 kıb olacaktır.<br> V8 ve üzeri: 64 KiB  | Yes |

> [!Note]  
> Azure Dosya Eşitleme uç noktası, bir Azure dosya paylaşımının boyutuna kadar ölçeklendirilebilir. Azure dosya paylaşımının boyut sınırına ulaşıldığında, eşitleme çalışamayacak.

### <a name="azure-file-sync-performance-metrics"></a>Azure Dosya Eşitleme performans ölçümleri
Azure Dosya Eşitleme Aracısı Azure dosya paylaşımlarına bağlanan bir Windows Server makinesinde çalıştığından, etkin eşitleme performansı altyapınızdaki bir dizi etkene bağlıdır: Windows Server ve temel disk yapılandırması, sunucu ile Azure depolama arasındaki ağ bant genişliği, dosya boyutu, toplam veri kümesi boyutu ve veri kümesindeki etkinlik. Azure Dosya Eşitleme dosya düzeyinde çalıştığından Azure Dosya Eşitleme tabanlı bir çözümün performans özellikleri, saniye başına işlenen nesne (dosya ve dizin) sayısında daha fazla ölçülür.

Azure Dosya Eşitleme için performans iki aşamada kritik öneme sahiptir:

1. **İlk bir kerelik sağlama**: ilk sağlama performansını iyileştirmek için en iyi dağıtım ayrıntıları için [Azure dosya eşitleme ile ekleme](../file-sync/file-sync-deployment-guide.md#onboarding-with-azure-file-sync) bölümüne bakın.
2. **Devam eden eşitleme**: veriler başlangıçta Azure dosya paylaşımlarında oluşturulduktan sonra Azure dosya eşitleme birden çok uç noktayı eşitlenmiş halde tutar.

Bir aşamanın her biri için dağıtımınızı planlamaya yardımcı olmak üzere, bir sistem üzerinde yapılandırmaya sahip iç test sırasında gözlemlenen sonuçlar aşağıda verilmiştir

| Sistem yapılandırması | Ayrıntılar |
|-|-|
| CPU | 64 MiB L3 önbellek ile 64 sanal çekirdek |
| Bellek | 128 GiB |
| Disk | Pil gücüyle RAID 10 ile SAS diskleri |
| Ağ | 1 Gbps ağ |
| İş Yükü | Genel Amaçlı dosya sunucusu|

| İlk bir kerelik sağlama  | Ayrıntılar |
|-|-|
| Nesne sayısı | 25.000.000 nesneleri |
| Veri kümesi boyutu| ~ 4,7 TiB |
| Ortalama dosya boyutu | ~ 200 KiB (en büyük dosya: 100 GiB) |
| İlk bulut değişikliği numaralandırması | saniyede 20 nesne  |
| Aktarım hızını karşıya yükle | Her eşitleme grubu için saniyede 20 nesne |
| Ad alanı Indirme üretilen Işi | saniyede 400 nesne |

### <a name="initial-one-time-provisioning"></a>İlk bir kerelik sağlama

**İlk bulut değişikliği numaralandırması**: yeni bir eşitleme grubu oluşturulduğunda, ilk bulut değişikliği numaralandırması çalıştırılacak ilk adımdır. Bu süreçte, sistem Azure dosya paylaşımındaki tüm öğeleri listelemeye çalışır. Bu işlem sırasında, hiçbir eşitleme etkinliği olmaz. bir öğe, bulut uç noktasından sunucu uç noktasına indirilmeyecek ve sunucu uç noktasından bulut uç noktasına hiçbir öğe yüklenemeyecektir. İlk bulut değişikliği numaralandırması tamamlandıktan sonra eşitleme etkinliği sürdürülecek.
Performans hızı saniyede 20 nesne olur. Müşteriler, bulut paylaşımındaki öğelerin sayısını belirleyerek ve saati gün olarak almak için aşağıdaki formül ' i kullanarak ilk bulut değişikliği numaralandırması işleminin tamamlanması için gereken süreyi tahmin edebilir. 

   **İlk bulut numaralandırması için (gün olarak) süre = (bulut uç noktasındaki nesne sayısı)/(20 * 60 * 60 * 24)**

**Windows Server 'Dan Azure dosya paylaşımında ilk veri eşitlemesi**: pek çok Azure dosya eşitleme dağıtımı, tüm veriler Windows Server üzerinde olduğundan boş bir Azure dosya paylaşımıyla başlar. Bu durumlarda, ilk bulut değişikliği numaralandırması hızlıdır ve çoğu zaman, değişiklikleri Windows Server 'dan Azure dosya paylaşımıyla eşitlemeye harcanacaktır. 

Eşitleme, verileri Azure dosya paylaşımında karşıya yüklerken yerel dosya sunucusunda kapalı kalma süresi yoktur ve Yöneticiler, arka planda karşıya yükleme için kullanılan bant genişliği miktarını kısıtlamak için [ağ sınırları](../file-sync/file-sync-server-registration.md#set-azure-file-sync-network-limits) ayarlayabilir.

İlk eşitleme genellikle eşitleme grubu başına saniyede 20 dosyanın ilk karşıya yükleme oranıyla sınırlandırılır. Müşteriler, gün içinde zaman almak için aşağıdaki formül ile tüm verilerini Azure 'a yükleme süresini tahmin edebilir:  

   **Eşitleme grubuna dosya yüklemek için geçen süre (gün cinsinden)/(sunucu uç noktasındaki nesne sayısı)/(20 * 60 * 60 * 24)**

Verilerinizi birden çok sunucu uç noktasına bölmek ve eşitleme grupları bu ilk verileri karşıya yüklemeyi hızlandırabilir. çünkü karşıya yükleme, birden fazla eşitleme grubu için saniyede 20 öğe hızında bir hızda paralel olarak yapılabilir. Bu nedenle, iki eşitleme grubu saniyede 40 öğe oranında Birleşik bir hızda çalışır. Toplam tamamlanma süresi, eşitleme grubu için en fazla dosya eşitlenecek süre tahmini olur.

**Ad alanı indirme üretilen işi** Var olan bir eşitleme grubuna yeni bir sunucu uç noktası eklendiğinde Azure Dosya Eşitleme Aracısı, bulut uç noktasındaki dosya içeriğini indirmez. Önce tam ad alanını eşitler ve sonra, dosyaları tamamen veya bulut katmanlaması etkinse, sunucu uç noktasında ayarlanan bulut katmanlaması ilkesi için arka plan geri yüklemeyi tetikler.

| Devam eden eşitleme  | Ayrıntılar  |
|-|--|
| Eşitlenen nesne sayısı| 125.000 nesneleri (%1 karmaşıklık) |
| Veri kümesi boyutu| 50 GiB |
| Ortalama dosya boyutu | ~ 500 KiB |
| Aktarım hızını karşıya yükle | Her eşitleme grubu için saniyede 20 nesne |
| Tam Indirme performansı * | saniyede 60 nesne |

* Bulut katmanlaması etkinse, bazı dosya verilerinin yalnızca bir kısmı indirildiğinden daha iyi performans gözlemleyebilirsiniz. Azure Dosya Eşitleme yalnızca, uç noktaların hiçbirinde değiştirildiklerinde önbelleğe alınmış dosyaların verilerini indirir. Katmanlı veya yeni oluşturulan dosyalar için, aracı dosya verilerini indirmez ve bunun yerine yalnızca ad alanını tüm sunucu uç noktalarına eşitler. Aracı Ayrıca katmanlı dosyaların kullanıcı tarafından erişilen kısmi karşıdan yüklenmesini de destekler. 

> [!Note]  
> Yukarıdaki numaralar, deneyiminizle ilgili olarak bir performans göstergesi değildir. Gerçek performans, bu bölümün başlangıcında özetlenen şekilde, birden çok etkene bağlıdır.

Dağıtımınız için genel bir kılavuz olarak birkaç şeyi göz önünde bulundurmanız gerekir:

- Nesne aktarım hızı, sunucudaki eşitleme gruplarının sayısıyla orantılı olarak ölçeklendirilir. Sunucuda birden çok eşitleme grubuna veri bölmek, sunucu ve ağ tarafından da sınırlı olan daha iyi aktarım hızı verir.
- Nesne aktarım hızı, saniye başına işlem için MIB ile orantılı şekilde orantılıdır. Daha küçük dosyalar için, saniye başına işlenen nesne sayısı açısından daha yüksek aktarım hızı, saniye başına ise alt MIB ile karşılaşırsınız. Buna karşılık, daha büyük dosyalar için, saniye başına işlenen daha az sayıda nesne ve saniye başına daha fazla MIB alacaksınız. Saniyedeki MIB, Azure dosyaları ölçek hedefleri ile sınırlıdır.

## <a name="see-also"></a>Ayrıca bkz.
- [Azure Dosyalar dağıtımını planlama](storage-files-planning.md)
- [Azure Dosya Eşitleme dağıtımı planlama](../file-sync/file-sync-planning.md)