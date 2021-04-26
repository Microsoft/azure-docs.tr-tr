---
title: Storage FSLogix profil kapsayıcısı Windows sanal masaüstü-Azure
description: Azure depolama 'da Windows sanal masaüstü FSLogix profilinizi depolama seçenekleri.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 1ff8c645b1ad670f3824920d39aa0c6bf9783408
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445559"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Windows sanal masaüstündeki FSLogix profil kapsayıcıları için depolama seçenekleri

Azure, FSLogix profil kapsayıcınızı depolamak için kullanabileceğiniz birden çok depolama çözümleri sunar. Bu makalede, Azure 'un Windows sanal masaüstü FSLogix Kullanıcı profili kapsayıcıları için sunduğu depolama çözümleri karşılaştırılır. FSLogix profil kapsayıcılarını, müşterilerimizin çoğu için Azure dosyalarında depolamanızı öneririz.

Windows sanal masaüstü, önerilen Kullanıcı profili çözümü olarak FSLogix profil kapsayıcıları sunar. FSLogix, Windows Sanal Masaüstü gibi uzak bilgi işlem ortamlarında dolaşım profillerini etkinleştirme amacıyla tasarlanmıştır. Bu kapsayıcı, oturum açma sırasında, yerel olarak desteklenen bir sanal sabit disk (VHD) ve Hyper-V sanal sabit diski (VHDX) kullanılarak bilgi işlem ortamına dinamik olarak eklenir. Kullanıcı profili hemen kullanılabilir ve sistemde yerel kullanıcı profili gibi görünür.

Aşağıdaki tablolar, Windows sanal masaüstü FSLogix profil kapsayıcısı Kullanıcı profilleri için Azure depolama tarafından sunulan depolama çözümlerini karşılaştırın.

## <a name="azure-platform-details"></a>Azure platformu ayrıntıları

|Özellikler|Azure Dosyaları|Azure NetApp Files|Doğrudan Depolama Alanları|
|--------|-----------|------------------|---------------------|
|Kullanım örneği|Genel amaçlı|Şirket içi NetApp 'ten ultra performans veya geçiş|Platformlar arası|
|Platform hizmeti|Evet, Azure-Native çözümü|Evet, Azure-Native çözümü|Hayır, otomatik olarak yönetilen|
|Bölgesel kullanılabilirlik|Tüm bölgeler|[Bölge seçin](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Tüm bölgeler|
|Yedeklilik|Yerel olarak yedekli/bölgesel-yedekli/coğrafi olarak yedekli/coğrafi bölge-yedekli|Yerel olarak yedekli|Yerel olarak yedekli/bölge-yedekli/coğrafi olarak yedekli|
|Katmanlar ve performans| Standart (Işlem için iyileştirilmiş)<br>Premium<br>Paylaşımda en fazla 100K ıOPS, paylaşma başına 10 GBps, yaklaşık 3 ms gecikme|Standart<br>Premium<br>Ultra<br>Yaklaşık 1 ms gecikme süresi içinde birim başına 4,5 GB/sn 'ye kadar 320 KB (16K) ıOPS|Standart HDD: disk başına en çok 500 ıOPS<br>Standart SSD: disk başına en fazla 4k ıOPS<br>Premium SSD: disk başına en fazla 20.000 IOPS sınırı<br>Depolama Alanları Doğrudan için Premium diskler öneriyoruz|
|Kapasite|Her paylaşımdan 100 TiB, genel amaçlı hesap başına 5 PiB |Her birim için 100 TiB, abonelik başına en fazla 12,5 PiB|Disk başına en fazla 32 TiB|
|Gerekli altyapı|En az paylaşma boyutu 1 GiB|Minimum kapasite havuzu 4 TiB, minimum birim boyutu 100 GiB|Azure IaaS (+ Cloud tanık) üzerinde iki VM veya disk 'siz ve maliyeti olan en az üç VM|
|Protokoller|SMB 3.0/2.1, NFSv 4.1 (Önizleme), REST|NFSv3, NFSv 4.1 (Önizleme), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Azure yönetim ayrıntıları

|Özellikler|Azure Dosyaları|Azure NetApp Files|Doğrudan Depolama Alanları|
|--------|-----------|------------------|---------------------|
|Access|Bulut, şirket içi ve karma (Azure dosya eşitleme)|Bulut, şirket içi (ExpressRoute aracılığıyla)|Bulut, şirket içi|
|Backup|Azure Backup anlık görüntü tümleştirmesi|Azure NetApp Files anlık görüntüler|Azure Backup anlık görüntü tümleştirmesi|
|Güvenlik ve uyumluluk|[Tüm Azure desteklenen sertifikaları](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO tamamlandı|[Tüm Azure desteklenen sertifikaları](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory tümleştirmesi|[Yerel Active Directory ve Azure Active Directory Domain Services](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory Domain Services ve yerel Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Yalnızca yerel Active Directory veya Azure Active Directory Domain Services desteği|

Depolama yönteminizi seçtikten sonra, fiyatlandırma planlarımız hakkında bilgi edinmek için [Windows sanal masaüstü fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-desktop/) ' na göz atın.

## <a name="azure-files-tiers"></a>Azure dosyaları katmanları

Azure dosyaları, iki farklı depolama katmanı sunar: Premium ve standart. Bu katmanlar, senaryonun gereksinimlerini karşılamak için dosya paylaşımlarınızın performansını ve maliyetini uyarlamanızı sağlar.

- Premium dosya paylaşımları katı hal sürücüleri (SSD 'Ler) tarafından desteklenir ve FileStorage depolama hesabı türüne dağıtılır. Premium dosya paylaşımları, giriş ve çıkış (GÇ) yoğun iş yükleri için tutarlı yüksek performans ve düşük gecikme süresi sağlar. 

- Standart dosya paylaşımları sabit disk sürücüleri (HDD 'Ler) tarafından desteklenir ve genel amaçlı sürüm 2 (GPv2) depolama hesabı türünde dağıtılır. Standart dosya paylaşımları, genel amaçlı dosya paylaşımları ve geliştirme/test ortamları gibi performans çeşitliliğine daha az duyarlı olan GÇ iş yükleri için güvenilir performans sağlar. Standart dosya paylaşımları yalnızca Kullandıkça Öde faturalandırma modelinde kullanılabilir.

Aşağıdaki tabloda, iş yükünüze göre hangi performans katmanının kullanılacağı önerilerimizi listelenmektedir. Bu öneriler, performans hedeflerinizi, bütçenize ve bölgesel konuları karşılayan performans katmanını seçmenize yardımcı olur. Bu önerileri, [Uzak Masaüstü iş yükü türlerindeki](/windows-server/remote/remote-desktop-services/remote-desktop-workloads)örnek senaryolara dayandırdık. 

| İş yükü türü | Önerilen dosya katmanı |
|--------|-----------|
| Hafif (200 kullanıcıdan az) | Standart dosya paylaşımları |
| Hafif (200 'den fazla Kullanıcı) | Birden çok dosya paylaşımı ile Premium dosya paylaşımları veya standart |
|Orta|Premium dosya paylaşımları|
|Ağır|Premium dosya paylaşımları|
|Güç|Premium dosya paylaşımları|

Azure dosya performansı hakkında daha fazla bilgi için bkz. [dosya paylaşma ve dosya ölçeği hedefleri](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets). Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure dosyaları fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Sonraki adımlar

FSLogix profil kapsayıcıları, Kullanıcı profili diskleri ve diğer Kullanıcı profili teknolojileri hakkında daha fazla bilgi edinmek için [Fslogix profil kapsayıcıları ve Azure dosyaları](fslogix-containers-azure-files.md)içindeki tabloya bakın.

Kendi FSLogix profil Kapsayıcılarınızı oluşturmaya hazırsanız, şu öğreticilerden birini kullanmaya başlayın:

- [Windows sanal masaüstündeki Azure dosyalarında FSLogix profil kapsayıcılarıyla çalışmaya başlama](create-file-share.md)
- [Azure NetApp dosyalarını kullanarak bir konak havuzu için FSLogix profil kapsayıcısı oluşturma](create-fslogix-profile-container.md)
- [Azure 'DA UPD depolama için iki düğümlü depolama alanları doğrudan genişleme dosya sunucusu dağıtma](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) ' daki yönergeler, bir kullanıcı profili diski yerine FSLogix profil kapsayıcısını kullandığınızda da geçerlidir.

Ayrıca, [Windows sanal masaüstü 'nde kiracı oluşturma bölümünde](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md), tek başına başlayıp kendi Windows sanal masaüstü çözümünüzü ayarlayabilirsiniz.
