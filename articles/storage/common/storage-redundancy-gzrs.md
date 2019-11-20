---
title: Coğrafi bölge yedekli depolama (GZRS) (Önizleme) ile yüksek oranda kullanılabilir Azure depolama uygulamaları oluşturun | Microsoft Docs
description: Coğrafi olarak yedekli depolama (GZRS) marrıes, coğrafi olarak yedekli depolama (GRS) tarafından sağlandığı şekilde bölgesel kesintilerden koruma ile bölgesel olarak yedekli depolamanın (ZRS) yüksek oranda kullanılabilir olduğunu. Bir GZRS Storage hesabındaki veriler, birincil bölgedeki Azure kullanılabilirlik alanları arasında çoğaltılır ve ayrıca bölgesel felaketlerden koruma için ikincil bir coğrafi bölgeye çoğaltılır.
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: e040533acdd8979b7b43358c74d1b729dafa2c66
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111845"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>Coğrafi bölge yedekli depolama (GZRS) ile yüksek oranda kullanılabilir Azure depolama uygulamaları oluşturun (Önizleme)

Coğrafi bölge yedekli depolama (GZRS) (Önizleme) marrıes, [coğrafi olarak yedekli depolama (GRS)](storage-redundancy-grs.md)tarafından sağlandığı şekilde bölgesel kesintilerden koruma ile bölgesel olarak yedekli [depolamanın (ZRS)](storage-redundancy-zrs.md) yüksek oranda kullanılabilir olması. Bir GZRS Storage hesabındaki veriler, birincil bölgedeki üç [Azure kullanılabilirlik](../../availability-zones/az-overview.md) alanı arasında çoğaltılır ve ayrıca bölgesel felaketlerden koruma için ikincil bir coğrafi bölgeye çoğaltılır. Her Azure bölgesi aynı coğrafya içindeki başka bir bölge ile eşleştirilir ve bir bölgesel çift oluşturur. Daha ayrıntılı bilgi ve özel durumlar için [belgelere](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)bakın.

Bir GZRS depolama hesabı ile bir kullanılabilirlik alanı kullanılamaz hale gelirse veya kurtarılamaz durumdaysa verileri okumaya ve yazmaya devam edebilirsiniz. Ayrıca, tüm bölgesel bir kesinti veya birincil bölgenin kurtarılamaz bir olağanüstü durum durumunda verileriniz de dayanıklı olur. GZRS belirli bir yıl boyunca nesnelerin en az% 99.99999999999999 (16 9) oranında dayanıklılığını sağlamak üzere tasarlanmıştır. GZRS, LRS, ZRS, GRS veya RA-GRS ile aynı [ölçeklenebilirlik hedeflerini](storage-scalability-targets.md) de sunmaktadır. Uygulamalarınızın birincil bölgedeki bir olağanüstü durum durumunda verileri okuyabilmeleri gerekiyorsa, isteğe bağlı olarak, Okuma Erişimli Coğrafi bölge-yedekli depolama (RA-GZRS) ile ikincil bölgedeki verilere okuma erişimini etkinleştirebilirsiniz.

Microsoft, olağanüstü durum kurtarma için tutarlılık, dayanıklılık, yüksek kullanılabilirlik, mükemmel performans ve esnekliği gerektiren uygulamalar için GZRS kullanılmasını önerir. Bölgesel bir olağanüstü durum durumunda ikincil bölgeye yönelik okuma erişiminin ek güvenliği için, depolama hesabınız için RA-GZRS ' y i etkinleştirin.

## <a name="about-the-preview"></a>Önizleme hakkında

GZRS ve RA-GZRS desteği yalnızca genel amaçlı v2 depolama hesaplarıdır. Depolama hesabı türleri hakkında daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](storage-account-overview.md). GZRS ve RA-GZRS desteği blok Blobları, sayfa Blobları (VHD diskleri olmayan), dosyalar, tablolar ve kuyruklar.

GZRS ve RA-GZRS Şu anda şu bölgelerde önizleme için kullanılabilir:

- Kuzey Avrupa
- Batı Avrupa
- Birleşik Krallık Güney
- ABD Doğu
- ABD Doğu 2
- ABD Orta

Microsoft, ek Azure bölgelerinde GZRS ve RA-GZRS etkinleştirmesine devam etmektedir. Desteklenen bölgeler hakkında bilgi için [Azure hizmet güncelleştirmeleri](https://azure.microsoft.com/updates/) sayfasını düzenli olarak denetleyin.

Önizleme fiyatlandırması hakkında bilgi için, [Bloblar](https://azure.microsoft.com/pricing/details/storage/blobs), [dosyalar](https://azure.microsoft.com/pricing/details/storage/files/), [Kuyruklar](https://azure.microsoft.com/pricing/details/storage/queues/)ve [Tablolar](https://azure.microsoft.com/pricing/details/storage/tables/)için GZRS Preview fiyatlandırması bölümüne bakın.

> [!IMPORTANT]
> Microsoft, üretim iş yükleri için Önizleme özelliklerinin kullanılmasını önerir.

## <a name="how-gzrs-and-ra-gzrs-work"></a>GZRS ve RA-GZRS nasıl çalışır?

Veriler GZRS veya RA-GZRS etkin bir depolama hesabına yazıldığında, bu veriler ilk olarak üç kullanılabilirlik alanı genelinde birincil bölgede zaman uyumlu olarak çoğaltılır. Veriler daha sonra zaman uyumsuz olarak yüzlerce mil uzakta olan ikinci bir bölgeye çoğaltılır. Veriler ikincil bölgeye yazıldığında, [yerel olarak yedekli depolama (LRS)](storage-redundancy-lrs.md)kullanılarak bu bölge içinde zaman uyumlu olarak üç kez çoğaltılır.

> [!IMPORTANT]
> Zaman uyumsuz çoğaltma, verilerin birincil bölgeye yazıldığı ve ikincil bölgeye çoğaltıldığı zaman arasında bir gecikme içerir. Bölgesel bir olağanüstü durum durumunda, bu veriler birincil bölgeden kurtarılamazsa, ikincil bölgeye henüz çoğaltılmamış değişiklikler kaybolabilir.

Bir depolama hesabı oluşturduğunuzda, bu hesaptaki verilerin nasıl çoğaltılacağı ve ayrıca söz konusu hesabın birincil bölgesini belirtmeniz gerekir. Coğrafi olarak çoğaltılan bir hesabın eşleştirilmiş ikincil bölgesi, birincil bölgeye göre belirlenir ve değiştirilemez. Azure tarafından desteklenen bölgeler hakkında güncel bilgiler için bkz. [iş sürekliliği ve olağanüstü durum kurtarma (BCDR): Azure eşleştirilmiş bölgeleri](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). GZRS veya RA-GZRS kullanarak bir depolama hesabı oluşturma hakkında bilgi için bkz. [depolama hesabı oluşturma](storage-quickstart-create-account.md).

### <a name="use-ra-gzrs-for-high-availability"></a>Yüksek kullanılabilirlik için RA-GZRS kullanma

Depolama hesabınız için RA-GZRS ' ı etkinleştirdiğinizde, verileriniz ikincil uç noktadan ve depolama hesabınızın birincil uç noktasından okunabilir. İkincil uç nokta son eki *–* hesap adına ekler. Örneğin, blob hizmeti için birincil uç noktanız `myaccount.blob.core.windows.net`, ikincil uç noktanız `myaccount-secondary.blob.core.windows.net`. Depolama hesabınızın erişim anahtarları hem birincil hem de ikincil uç noktalar için aynıdır.

Bölgesel bir kesinti durumunda RA-GZRS avantajlarından yararlanmak için, bu senaryoyu işlemek üzere uygulamanızı önceden tasarlamanız gerekir. Uygulamanız birincil uç noktadan okuyup yazmalı, ancak birincil bölgenin kullanılamaz hale geldiği olayda ikincil uç noktayı kullanmaya geçiş yapar. RA-GZRS ile yüksek kullanılabilirlik için tasarlamaya yönelik yönergeler için bkz. [ra-GZRS veya RA-GRS kullanarak yüksek oranda kullanılabilir uygulamalar tasarlama](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs).

Veriler ikincil bölgeye zaman uyumsuz olarak çoğaltıldığından, ikincil bölge genellikle birincil bölgenin arkasında olur. İkincil bölgeye çoğaltılan yazma işlemlerini öğrenmek için, uygulamanız depolama hesabınızın son eşitleme zamanını kontrol edin. Birincil bölgeye son eşitleme zamanından önce yazılan tüm yazma işlemleri, ikincil bölgeye başarıyla çoğaltılmıştı ve bu, ikincilden okunmak üzere kullanılabilir. Son eşitleme zamanından sonra birincil bölgeye yazılan yazma işlemleri, ikincil bölgeye çoğaltılmayabilir veya bu, okuma işlemleri için uygun olmayabilir.

**Son eşitleme zamanı** özelliğinin değerini Azure PowerShell, Azure CLI veya Azure Storage istemci kitaplıklarından birini kullanarak sorgulayabilirsiniz. **Son eşitleme zamanı** ÖZELLIĞI bir GMT Tarih/saat değeri.

RA-GZRS ile performans ve ölçeklenebilirlik hakkında ek yönergeler için [Microsoft Azure depolama performans ve ölçeklenebilirlik denetim listesine](storage-performance-checklist.md)bakın.

### <a name="availability-zone-outages"></a>Kullanılabilirlik alanı kesintileri

Birincil bölgedeki bir kullanılabilirlik bölgesini etkileyen bir hata durumunda uygulamalarınız, bu bölgeye yönelik diğer kullanılabilirlik bölgelerini kullanarak depolama hesabınıza okuma ve yazma ile sorunsuz bir şekilde devam edebilir. Microsoft, GZRS veya ZRS kullanırken geçici hata işlemeye yönelik uygulamaları izlemeye devam etmeniz önerilir. Bu uygulamalar, yeniden deneme ilkelerini üstel geri ile uygulamayı içerir.

Bir kullanılabilirlik alanı kullanılamaz hale geldiğinde Azure, DNS yeniden işaretleme gibi ağ güncelleştirmelerini alır. Güncelleştirmeler tamamlanmadan önce verilere erişiyorsanız, bu güncelleştirmeler uygulamanızı etkileyebilir.

### <a name="regional-outages"></a>Bölgesel kesintiler

Bir hata tüm birincil bölgeyi etkiliyorsa, Microsoft öncelikle birincil bölgeyi geri yüklemeyi dener. Geri yükleme mümkün değilse, ikincil bölgenin yeni birincil bölge haline gelmesi için Microsoft, ikincil bölgeye yük devreder. Depolama hesabında RA-GZRS etkinse, bu senaryo için tasarlanan uygulamalar, yük devretmeyi beklerken ikincil bölgeden okuyabilir. Depolama hesabında RA-GZRS etkin değilse, yük devretme tamamlanana kadar uygulamalar ikincili okuyamayacak.

> [!NOTE]
> GZRS ve RA-GZRS Şu anda yalnızca ABD Doğu bölgesinde önizlemededir. Müşteri tarafından yönetilen hesap yük devretmesi (Önizleme) ABD Doğu 2 içinde henüz kullanılamadığından müşteriler şu anda GZRS ve RA-GZRS hesaplarıyla hesap yük devretme olaylarını yönetemez. Önizleme sırasında, Microsoft GZRS ve RA-GZRS hesaplarını etkileyen tüm yük devretme olaylarını yönetir.

Veriler ikincil bölgeye zaman uyumsuz olarak çoğaltıldığından, birincil bölgeyi etkileyen bir hata, birincil bölge kurtarılamazsa veri kaybına neden olabilir. Birincil bölgeye en son yazma işlemleri ile ikincil bölgeye son yazma arasındaki Aralık, kurtarma noktası hedefi (RPO) olarak bilinir. RPO, verilerin kurtarılabileceği noktayı gösterir. Azure depolama, genellikle 15 dakikadan daha kısa bir RPO 'ya sahiptir, ancak şu anda verilerin ikincil bölgeye çoğaltılmasının ne kadar sürdüğü konusunda bir SLA yoktur.

Kurtarma süresi hedefi (RTO), yük devretmeyi gerçekleştirmek ve depolama hesabını yeniden çevrimiçi hale getirmek için ne kadar süreceğine ilişkin bir ölçüdür. Bu ölçü, birincil DNS girdilerini ikincil konuma işaret etmek üzere değiştirerek yük devretmeyi gerçekleştirmek için Azure tarafından gereken süreyi gösterir.

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>Depolama hesabını GZRS veya RA-GZRS 'e geçirme

Var olan bir depolama hesabını GZRS veya RA-GZRS ' e geçirebilirsiniz. Bir LRS, GRS veya RA-GRS hesabından geçiş yaparken, mevcut bir ZRS hesabından GZRS veya RA-GZRS ' e geçiş basittir. Aşağıdaki bölümlerde her iki durumda da nasıl geçiş yapılacağı açıklanır.

### <a name="migrating-from-a-zrs-account"></a>ZRS hesabından geçiş yapma

Mevcut bir ZRS hesabını RA-GZRS ' a dönüştürmek için, [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) cmdlet 'ini kullanarak hesabın SKU 'sunu değiştirin. Yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>LRS, GRS veya RA-GRS hesabından geçiş yapma

LRS, GRS veya RA-GRS hesabından GZRS veya RA-GZRS ' a geçiş yapmak için iki seçenek vardır:

- Mevcut bir hesaptan, verileri el ile yeni bir GZRS veya RA-GZRS hesabına kopyalayabilir veya taşıyabilirsiniz.
- Dinamik geçiş isteğinde bulunabilir.

#### <a name="perform-a-manual-migration"></a>El ile geçiş gerçekleştirme

Geçişin belirli bir tarihten sonra tamamlanmasını istiyorsanız el ile geçiş yapmayı deneyin. El ile geçiş, dinamik geçişten daha fazla esneklik sağlar. El ile geçiş işleminde zamanlama sizin denetiminizde olur.

Mevcut bir hesaptan GZRS veya RA-GZRS hesabına el ile veri geçirebilmek için, verileri verimli bir şekilde kopyalayabileceğiniz bir araç kullanın. Bazı örnekler:

- AzCopy veya güvenilir bir üçüncü taraf aracı gibi bir yardımcı program kullanın. AzCopy hakkında daha fazla bilgi için bkz. [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md).
- Hadoop veya HDInsight hakkında bilgi sahibiyseniz, kümenize hem kaynak hem de hedef depolama hesapları ekleyin. Ardından, paralel hale getirmek gibi bir araçla veri kopyalama işlemini yapın.
- Azure depolama istemci kitaplıklarından birini kullanarak kendi araç yapınızı oluşturun.

#### <a name="perform-a-live-migration"></a>Dinamik geçiş gerçekleştirme

El ile geçiş, uygulama kapalı kalma süresine yol açabilir. Uygulamanız yüksek kullanılabilirlik gerektiriyorsa, Microsoft dinamik geçiş seçeneği de sağlar. Dinamik geçiş, kapalı kalma süresi olmayan bir yerinde geçiş işlemidir.

Dinamik geçiş sırasında, verileriniz kaynak ve hedef depolama hesapları arasında geçirildiğinde depolama hesabınızı kullanabilirsiniz. Dinamik geçiş işlemi sırasında hesabınız, dayanıklılık ve kullanılabilirlik için SLA 'Sı buluşmaya devam eder. Dinamik geçişin neden olduğu kesinti veya veri kaybı yoktur.

Yalnızca genel amaçlı v2 hesapları GZRS/RA-GZRS destekler, bu nedenle GZRS/RA-GZRS ' e dinamik geçiş için bir istek göndermeden önce, hesabınızı genel amaçlı v2 'ye yükseltmeniz gerekir. Daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](https://docs.microsoft.com/azure/storage/common/storage-account-overview) ve [genel amaçlı v2 depolama hesabına yükseltme](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).

Geçiş işlemi tamamlandıktan sonra, depolama hesabının çoğaltma ayarı **coğrafi bölge yedekli depolama (GZRS)** veya **Okuma Erişimli Coğrafi bölge-YEDEKLI depolama (ra-GZRS)** olarak güncelleştirilir. Hizmet uç noktaları, erişim anahtarları, paylaşılan erişim imzaları (SAS) ve diğer hesap yapılandırma seçenekleri değişmeden kalır ve bozulmaz.

Dinamik geçiş ile ilgili aşağıdaki kısıtlamaları göz önünde bulundurun:

- Microsoft canlı geçiş isteğinizi anında işler ama canlı bir geçişin ne zaman tamamlanacağı garanti edilemez. Verilerinizin belirli bir tarihe göre GZRS veya RA-GZRS 'e geçirilmesi gerekiyorsa, Microsoft bunun yerine el ile geçiş gerçekleştirmenizi önerir. Genel olarak hesabınızda ne kadar çok veri varsa bu verilerin geçişi de o kadar uzun sürer.
- Hesabınızın veri içermesi gerekir.
- Yalnızca aynı bölgedeki verileri geçirebilirsiniz.
- Yalnızca standart depolama hesabı türleri dinamik geçişi destekler. Premium Depolama hesaplarının el ile geçirilmesi gerekir.
- GZRS veya RA-GZRS hesabından LRS, GRS veya RA-GRS hesabına dinamik geçiş desteklenmez. Verileri yeni veya mevcut depolama hesabına el ile taşımanız gerekir.
- RA-GRS 'den RA-GZRS ' d e dinamik geçiş isteğinde bulabilirsiniz. Ancak, RA-GRS ' den GZRS ' a geçiş desteklenmez. Bu durumda, RA-GZRS için dinamik geçiş istemeniz ve depolama hesabını el ile GZRS kullanacak şekilde dönüştürmeniz gerekir.
- Yönetilen diskler yalnızca LRS 'yi destekler ve GZRS veya RA-GZRS ' a geçirilemez. Kullanılabilirlik kümeleriyle tümleştirme için bkz. [Azure yönetilen disklere giriş](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Standart SSD yönetilen disklerin anlık görüntülerini ve görüntülerini Standart HDD depolamada saklayabilir ve [LRS, ZRS, GZRS ve Ra-GZRS seçenekleri arasından seçim](https://azure.microsoft.com/pricing/details/managed-disks/)yapabilirsiniz.
- Büyük dosya paylaşımları içeren hesaplar GZRS için desteklenmez.

Dinamik geçiş istemek için [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)kullanın. Portaldan GZRS veya RA-GZRS ' e geçirilecek depolama hesabını seçin ve şu yönergeleri izleyin:

1. **Yeni destek isteği ' ni**seçin.
2. Hesap bilgilerinizi temel alan **temel bilgileri** doldurun. **Hizmet** bölümünde, **depolama hesabı yönetimi** ' ni seçin ve geçirilecek hesabı belirtin.
3. **İleri**’yi seçin.
4. **Sorun** bölümünün aşağıdaki değerlerini belirtin:
    - **Önem derecesi**: varsayılan değeri olduğu gibi bırakın.
    - **Sorun türü**: **veri geçişini**seçin.
    - **Kategori**: **bir bölge içinde (ra-) GZRS geçir**' i seçin.
    - **Başlık**: Örneğin, **(ra-) GZRS hesabı geçişi**gibi açıklayıcı bir başlık yazın.
    - **Ayrıntılar**: **Ayrıntılar** kutusuna ek ayrıntılar yazın, örneğin, "\_\_ bölgesindeki [LRS, GRS] öğesinden GZRS 'e geçiş yapmak istiyorum." ya da "\_\_ bölgesinde [LRS, RA-GRS] konumundan RA-GZRS ' a geçiş yapmak istiyorum."
5. **İleri**’yi seçin.
6. İletişim bilgilerinin **iletişim bilgileri** dikey penceresinde doğru olduğunu doğrulayın.
7. **Oluştur**'u seçin.

Destek temsilcisi, yardım sağlamak için sizinle iletişim kuracaktır.

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Depolama çoğaltması](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [Yerel olarak yedekli depolama (LRS): Azure depolama için düşük maliyetli veri artıklığı](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [Bölgesel olarak yedekli depolama (ZRS): yüksek oranda kullanılabilir Azure depolama uygulamaları](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
