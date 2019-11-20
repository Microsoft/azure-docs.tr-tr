---
title: Bölgede yedekli depolama (ZRS) üzerinde yüksek oranda kullanılabilir Azure depolama uygulamaları oluşturun | Microsoft Docs
description: Bölgesel olarak yedekli depolama (ZRS), yüksek oranda kullanılabilir uygulamalar oluşturmanın basit bir yolunu sunar. ZRS, veri merkezindeki donanım arızalarına ve bazı bölgesel felate karşı koruma sağlar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 0196e6fb4b2cd52fd66a26e07e8d4ce71983bc16
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013201"
---
# <a name="zone-redundant-storage-zrs-for-building-highly-available-azure-storage-applications"></a>Yüksek oranda kullanılabilir Azure depolama uygulamaları oluşturmak için bölge yedekli depolama (ZRS)

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Destek kapsamı ve bölgesel kullanılabilirlik

ZRS Şu anda standart genel amaçlı v2 ve FileStorage depolama hesabı türlerini desteklemektedir. Depolama hesabı türleri hakkında daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](storage-account-overview.md).

ZRS, blok Blobları, disk olmayan sayfa Blobları, standart dosya paylaşımları, tablolar ve kuyruklar için kullanılabilir.

Genel amaçlı v2 hesaplarında, ZRS aşağıdaki bölgelerde genel kullanıma sunulmuştur:

- Güneydoğu Asya
- Batı Avrupa
- Kuzey Avrupa
- Fransa Orta
- Japonya Doğu
- Birleşik Krallık Güney
- ABD Orta
- ABD Doğu
- ABD Doğu 2
- ABD Batı 2

FileStorage hesapları için (Premium dosya paylaşımları), ZRS aşağıdaki bölgelerde genel kullanıma sunulmuştur:

- Batı Avrupa
- ABD Doğu

Microsoft, ek Azure bölgelerinde ZRS 'yi etkinleştirmeye devam etmektedir. Yeni bölgeler hakkında bilgi için [Azure hizmet güncelleştirmeleri](https://azure.microsoft.com/updates/) sayfasını düzenli olarak denetleyin.

**Bilinen sınırlamalar**

- Arşiv katmanı Şu anda ZRS hesaplarında desteklenmiyor. Daha fazla ayrıntı için bkz. [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) .
- Yönetilen diskler ZRS 'yi desteklemez. Standart SSD yönetilen disklerin anlık görüntülerini ve görüntülerini Standart HDD depolamada saklayabilir ve [LRS ve ZRS seçenekleri arasında seçim](https://azure.microsoft.com/pricing/details/managed-disks/)yapabilirsiniz.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Bir bölge kullanılamaz duruma geldiğinde ne olur?

Bir bölge kullanılamaz duruma gelirse bile verilerinize hem okuma hem de yazma işlemleri için erişilebilir. Microsoft, geçici hata işleme için uygulamaları izlemeye devam etmeniz önerilir. Bu uygulamalar, yeniden deneme ilkelerini üstel geri ile uygulamayı içerir.

Bir bölge kullanılamadığında Azure, DNS yeniden işaretleme gibi ağ güncelleştirmelerini alır. Güncelleştirmeler tamamlanmadan önce verilerinize erişiyorsanız, bu güncelleştirmeler uygulamanızı etkileyebilir.

ZRS, verileri birden çok bölgenin kalıcı olarak etkilediği bölgesel bir olağanüstü duruma karşı koruyamayabilir. Bunun yerine, ZRS geçici olarak kullanılamaz duruma gelirse verileriniz için dayanıklılık sağlar. Bölgesel felate karşı koruma için Microsoft, coğrafi olarak yedekli depolama (GRS) kullanmanızı önerir. GRS hakkında daha fazla bilgi için bkz. [coğrafi olarak yedekli depolama (GRS): Azure depolama Için çapraz bölgesel çoğaltma](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>ZRS çoğaltmaya dönüştürme

LRS, GRS ve RA-GRS 'ye veya sürümünden geçiş basittir. Hesabınızın artıklık türünü değiştirmek için Azure portal veya depolama kaynak sağlayıcısı API 'sini kullanın. Azure daha sonra verilerinizi buna göre çoğaltacaktır. 

ZRS 'ye veri geçirmek farklı bir strateji gerektirir. ZRS geçişi, tek bir depolama damgasından bir bölgedeki birden çok damgaya verilerin fiziksel hareketini içerir.

ZRS 'ye geçiş için iki birincil seçenek vardır: 

- Mevcut hesaptaki verileri el ile yeni ZRS hesabına kopyalayın veya taşıyın.
- Dinamik geçiş isteğinde bulunun.

> [!IMPORTANT]
> Dinamik geçiş, Premium dosya paylaşımları için şu anda desteklenmiyor. Şu anda yalnızca el ile kopyalama veya taşıma işlemi destekleniyor.

Geçişin belirli bir tarihe kadar tamamlanması gerekiyorsa el ile geçiş yapmayı deneyin. El ile geçiş, dinamik geçişten daha fazla esneklik sağlar. El ile geçiş işleminde zamanlama sizin denetiminizde olur.

El ile geçiş yapmak için seçenekleriniz vardır:
- AzCopy gibi mevcut araçları, Azure Storage istemci kitaplıklarından birini veya güvenilir üçüncü taraf araçlarından birini kullanın.
- Hadoop veya HDInsight hakkında bilginiz varsa, kümenize hem kaynak hem de hedef (ZRS) hesabı ekleyin. Daha sonra, paralel hale getirmek gibi bir araçla veri kopyalama işlemini yapın.
- Azure depolama istemci kitaplıklarından birini kullanarak kendi araç yapınızı oluşturun.

El ile geçiş, uygulama kapalı kalma süresine yol açabilir. Uygulamanız yüksek kullanılabilirlik gerektiriyorsa, Microsoft dinamik geçiş seçeneği de sağlar. Dinamik geçiş, kapalı kalma süresi olmayan bir yerinde geçiş işlemidir. 

Dinamik geçiş sırasında, verileriniz kaynak ve hedef depolama düğümleri arasında geçirilirken depolama hesabınızı kullanabilirsiniz. Geçiş işlemi sırasında her zamanki dayanıklılık ve kullanılabilirlik düzeyi SLA’sına sahip olursunuz.

Dinamik geçiş ile ilgili aşağıdaki kısıtlamaları göz önünde bulundurun:

- Microsoft canlı geçiş isteğinizi anında işler ama canlı bir geçişin ne zaman tamamlanacağı garanti edilemez. Verilerinizin belirli bir tarihe kadar ZRS'ye geçirilmesi gerekiyorsa, Microsoft bunun yerine el ile geçiş yapmanızı önerir. Genel olarak hesabınızda ne kadar çok veri varsa bu verilerin geçişi de o kadar uzun sürer. 
- Dinamik geçiş yalnızca LRS veya GRS çoğaltmasını kullanan depolama hesapları için desteklenir. Hesabınız RA-GRS kullanıyorsa, önce devam etmeden önce hesabınızın çoğaltma türünü LRS veya GRS olarak değiştirmeniz gerekir. Bu ara adım, geçişten önce RA-GRS tarafından sunulan ikincil salt okuma uç noktasını kaldırır.
- Hesabınızın veri içermesi gerekir.
- Yalnızca aynı bölgedeki verileri geçirebilirsiniz. Verilerinizi kaynak hesaptan farklı bir bölgede bulunan bir ZRS hesabına geçirmek istiyorsanız, el ile geçiş gerçekleştirmeniz gerekir.
- Yalnızca standart depolama hesabı türleri dinamik geçişi destekler. Premium Depolama hesaplarının el ile geçirilmesi gerekir.
- ZRS 'den LRS 'ye dinamik geçiş, GRS veya RA-GRS desteklenmez. Verileri yeni veya mevcut depolama hesabına el ile taşımanız gerekir.
- Yönetilen diskler yalnızca LRS için kullanılabilir ve ZRS 'ye geçirilemez. Standart SSD yönetilen disklerin anlık görüntülerini ve görüntülerini Standart HDD depolamada saklayabilir ve [LRS ve ZRS seçenekleri arasında seçim](https://azure.microsoft.com/pricing/details/managed-disks/)yapabilirsiniz. Kullanılabilirlik kümeleriyle tümleştirme için bkz. [Azure yönetilen disklere giriş](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Arşiv verileri olan LRS veya GRS hesapları ZRS 'ye geçirilemez.

[Azure Destek Portalı](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)aracılığıyla dinamik geçiş isteğinde bulunabilir. Portaldan ZRS 'ye dönüştürmek istediğiniz depolama hesabını seçin.
1. **Yeni destek isteği** seçin
2. Hesap bilgilerinizi temel alan **temel bilgileri** doldurun. **Hizmet** bölümünde **depolama hesabı yönetimi** ' ni ve ZRS 'ye dönüştürmek istediğiniz kaynağı seçin. 
3. **İleri**’yi seçin. 
4. **Sorun** bölümünün aşağıdaki değerlerini belirtin: 
    - **Önem derecesi**: varsayılan değeri olduğu gibi bırakın.
    - **Sorun türü**: **veri geçişini**seçin.
    - **Kategori**: **ZRS 'ye geçir**' i seçin.
    - **Başlık**: Örneğin, **ZRS hesabı geçişi**gibi açıklayıcı bir başlık yazın.
    - **Ayrıntılar**: **Ayrıntılar** kutusuna ek ayrıntılar yazın, örneğin, \_\_ bölgesindeki [LRS, GRS] öğesinden ZRS 'ye geçmek istiyorum. 
5. **İleri**’yi seçin.
6. İletişim bilgilerinin **iletişim bilgileri** dikey penceresinde doğru olduğunu doğrulayın.
7. **Oluştur**'u seçin.

Bir destek kişisi sizinle iletişim kuracaktır ve ihtiyacınız olan herhangi bir yardımı sağlar.

## <a name="live-migration-to-zrs-faq"></a>ZRS 'ye dinamik geçiş hakkında SSS

**Geçiş sırasında kapalı kalma süresini planlıyorum mıyım?**

Geçişin neden olduğu kesinti yok. Dinamik geçiş sırasında, verileriniz kaynak ve hedef depolama damgaları arasında geçirildiğinde depolama hesabınızı kullanmaya devam edebilirsiniz. Geçiş işlemi sırasında her zamanki dayanıklılık ve kullanılabilirlik düzeyi SLA’sına sahip olursunuz.

**Geçişle ilişkili bir veri kaybı var mı?**

Geçişle ilişkili bir veri kaybı yok. Geçiş işlemi sırasında her zamanki dayanıklılık ve kullanılabilirlik düzeyi SLA’sına sahip olursunuz.

**Geçiş tamamlandıktan sonra uygulama (lar) için gerekli güncelleştirmeler mi?**

Geçiş işlemi tamamlandıktan sonra, hesap (ler) in çoğaltma türü "bölgesel olarak yedekli depolama (ZRS)" olarak değişir. Hizmet uç noktaları, erişim anahtarları, SAS ve diğer hesap yapılandırma seçenekleri değişmeden kalır ve bozulmaz.

**Genel amaçlı v1 hesablarım için ZRS 'ye dinamik geçiş isteğinde bulunabilir miyim?**

ZRS, ZRS 'ye dinamik geçiş için bir istek göndermeden önce genel amaçlı v2 hesaplarını destekler, böylece, hesaplarınızı genel amaçlı v2 'ye yükseltdiğinizden emin olun. Daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](https://docs.microsoft.com/azure/storage/common/storage-account-overview) ve [genel amaçlı v2 depolama hesabına yükseltme](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) .

**Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) hesaplarının ZRS 'ye dinamik geçişini isteyebilir miyim?**

ZRS 'ye dinamik geçiş isteği göndermeden önce, uygulamanızın veya iş yüklerinizin artık ikincil salt okunurdur uç noktaya erişim gerektirmediğinden emin olun ve depolama hesabınızın çoğaltma türünü coğrafi olarak yedekli depolama (GRS) olarak değiştirin. Daha fazla ayrıntı için bkz. [çoğaltma stratejisini değiştirme](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) .

**Depolama hesabımın, başka bir bölgeye ZRS 'ye dinamik geçişini isteyebilir miyim?**

Verilerinizi kaynak hesabın bölgesinden farklı bir bölgede bulunan bir ZRS hesabına geçirmek istiyorsanız, el ile geçiş gerçekleştirmeniz gerekir.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS klasik: blok bloblarının artıklığı için eski bir seçenek
> [!NOTE]
> Microsoft, ZRS klasik hesaplarını 31 Mart 2021 ' de kullanımdan kaldırır ve geçirebilir. Kullanım dışı bırakmadan önce ZRS klasik müşterilerine daha fazla ayrıntı sunulacaktır. 
>
> ZRS, bir bölgede [genel kullanıma](#support-coverage-and-regional-availability) sunulduğunda, müşteriler bu bölgedeki portaldan ZRS klasik hesapları oluşturamayacak. ZRS klasik hesapları oluşturmak için Microsoft PowerShell ve Azure CLı kullanmak, ZRS klasik kullanım dışı olana kadar bir seçenektir.

ZRS klasik, verileri bir veya iki bölge içindeki veri merkezlerinde zaman uyumsuz olarak çoğaltır. Microsoft ikinciye yük devretmeyi başlatmadığı müddetçe çoğaltılan veriler kullanılamayabilir. ZRS klasik hesabı LRS, GRS veya RA-GRS ' y e veya bu bilgisayarlardan dönüştürülemez. ZRS klasik hesapları, ölçümleri veya günlüğe kaydetmeyi de desteklemez.

ZRS klasik, yalnızca genel amaçlı v1 (GPv1) depolama hesaplarında **blok Bloblar** için kullanılabilir. Depolama hesapları hakkında daha fazla bilgi için bkz. [Azure Depolama hesabına genel bakış](storage-account-overview.md).

ZRS hesabı verilerini LRS, ZRS klasik, GRS veya RA-GRS hesabına el ile geçirmek için şu araçlardan birini kullanın: AzCopy, Azure Depolama Gezgini, Azure PowerShell veya Azure CLı. Ayrıca, Azure depolama istemci kitaplıklarından biriyle kendi geçiş çözümünüzü oluşturabilirsiniz.

Ayrıca, ZRS Classic hesabınızı portalda ZRS 'ye veya ZRS 'nin kullanılabildiği bölgelerde Azure PowerShell veya Azure CLı 'yi kullanarak yükseltebilirsiniz. Azure portal ZRS 'ye yükseltmek için, hesabın **yapılandırma** bölümüne gidin ve **Yükselt**' i seçin:

![Portalda ZRS Classic 'i ZRS 'ye yükseltme](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.png)

PowerShell kullanarak ZRS 'ye yükseltmek için aşağıdaki komutu çağırın:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

CLı kullanarak ZRS 'ye yükseltmek için aşağıdaki komutu çağırın:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Ayrıca bkz.
- [Azure Depolama çoğaltması](storage-redundancy.md)
- [Yerel olarak yedekli depolama (LRS): Azure depolama için düşük maliyetli veri artıklığı](storage-redundancy-lrs.md)
- [Coğrafi olarak yedekli depolama (GRS): Azure depolama için çapraz bölgesel çoğaltma](storage-redundancy-grs.md)
