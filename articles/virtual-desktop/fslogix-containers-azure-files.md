---
title: Windows sanal masaüstü FSLogix profil kapsayıcıları dosyaları-Azure
description: Bu makalede, Windows sanal masaüstü ve Azure dosyaları içindeki FSLogix profil kapsayıcıları açıklanmaktadır.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
ms.openlocfilehash: 7003e5b8574d2caa05bfe66e500b93db0c1cdcfa
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891632"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix profil kapsayıcıları ve Azure dosyaları

Windows sanal masaüstü hizmeti, FSLogix profil kapsayıcılarını bir kullanıcı profili çözümü olarak önerir. FSLogix, Windows sanal masaüstü gibi uzak bilgi işlem ortamlarında profil dolaşımını yapmak için tasarlanmıştır. Tüm kullanıcı profilini tek bir kapsayıcıda depolar. Bu kapsayıcı, oturum açma sırasında, yerel olarak desteklenen sanal sabit disk (VHD) ve Hyper-V sanal sabit diski (VHDX) kullanılarak bilgi işlem ortamına dinamik olarak eklenir. Kullanıcı profili hemen kullanılabilir olur ve sistemde tam olarak yerel bir kullanıcı profili gibi görünür. Bu makalede, FSLogix profil kapsayıcılarının Windows sanal masaüstündeki Azure dosyaları işleviyle nasıl kullanıldığı açıklanmaktadır.

>[!NOTE]
>Azure 'daki farklı FSLogix profili kapsayıcı depolama seçenekleri hakkında daha fazla bilgi arıyorsanız, bkz. [FSLogix profil kapsayıcıları Için depolama seçenekleri](store-fslogix-profile.md).

## <a name="user-profiles"></a>Kullanıcı profilleri

Kullanıcı profili, masaüstü ayarları, kalıcı ağ bağlantıları ve uygulama ayarları gibi yapılandırma bilgileri de dahil olmak üzere bir bireyin hakkındaki veri öğelerini içerir. Varsayılan olarak, Windows işletim sistemiyle sıkı bir şekilde tümleştirilmiş bir yerel kullanıcı profili oluşturur.

Uzak Kullanıcı profili, Kullanıcı verileri ile işletim sistemi arasında bir bölüm sağlar. İşletim sisteminin, Kullanıcı verilerini etkilemeden değiştirilmesini veya değiştirilmesini sağlar. Uzak Masaüstü Oturumu Ana Bilgisayarı (RDSH) ve sanal masaüstü altyapılarında (VDı), işletim sistemi aşağıdaki nedenlerle değiştirilebilir:

- İşletim sisteminin yükseltilmesi
- Var olan bir sanal makinenin (VM) yerini değiştirme
- Bir Kullanıcı havuza alınmış (kalıcı olmayan) bir RDSH veya VDı ortamının parçası.

Microsoft ürünleri, bu teknolojiler dahil olmak üzere uzak kullanıcı profilleri için birkaç teknolojiyle çalışır:
- Gezici Kullanıcı profilleri (RUP)
- Kullanıcı profili diskleri (UPD)
- Kurumsal durum dolaşımı (ESR)

UPD ve RUP, Uzak Masaüstü Oturumu Ana Bilgisayarı (RDSH) ve sanal sabit disk (VHD) ortamlarındaki kullanıcı profilleri için en yaygın olarak kullanılan teknolojilerdir.

### <a name="challenges-with-previous-user-profile-technologies"></a>Önceki Kullanıcı profili teknolojileriyle ilgili sorunlar

Kullanıcı profilleri için mevcut ve eski Microsoft çözümleri çeşitli güçlüklerle birlikte geldi. Bir RDSH veya VDı ortamıyla gelen tüm Kullanıcı profili gereksinimlerini işlenmemiş bir önceki çözüm yoktur. Örneğin, UPD büyük OST dosyalarını işleyemez ve RUP modern ayarları kalıcı yapmaz.

#### <a name="functionality"></a>İşlev

Aşağıdaki tabloda önceki Kullanıcı profili teknolojilerinin avantajları ve sınırlamaları gösterilmektedir.

| Teknoloji | Modern ayarlar | Win32 ayarları | İşletim sistemi ayarları | Kullanıcı verileri | Sunucu SKU 'sunda destekleniyor | Azure 'da arka uç depolama | Şirket içinde arka uç depolama | Sürüm desteği | Sonraki oturum açma zamanı |Notlar|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Kullanıcı profili diskleri (UPD)** | Evet | Evet | Evet | Evet | Evet | Hayır | Evet | Win 7 + | Evet | |
| **Gezici Kullanıcı profili (RUP), bakım modu** | Hayır | Evet | Evet | Evet | Evet| Hayır | Evet | Win 7 + | Hayır | |
| **Enterprise State Roaming (ESR)** | Evet | Hayır | Evet | Hayır | Notlara bakın | Evet | Hayır | Win 10 | Hayır | Sunucu SKU 'sunda işlevler ancak destekleyici Kullanıcı arabirimi yok |
| **Kullanıcı deneyimi sanallaştırma (UE-V)** | Evet | Evet | Evet | Hayır | Evet | Hayır | Evet | Win 7 + | Hayır |  |
| **OneDrive bulut dosyaları** | Hayır | Hayır | Hayır | Evet | Notlara bakın | Notlara bakın  | Notlara bakın | Win 10 RS3 | Hayır | Sunucu SKU 'sunda sınanmamıştır. Azure üzerinde arka uç depolama, eşitleme istemcisine bağlıdır. Arka uç depolamada şirket içi depolama için bir eşitleme istemcisi gerekir. |

#### <a name="performance"></a>Performans

UPD, performans gereksinimlerini karşılamak için [depolama alanları doğrudan (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) gerektirir. UPD, sunucu Ileti bloğu (SMB) protokolünü kullanır. Profili, kullanıcının günlüğe kaydedildiği VM 'ye kopyalar. S2D ile UPD, Windows sanal masaüstü için önerdiğimiz çözümdür.  

#### <a name="cost"></a>Maliyet

S2D kümeleri gerekli performansa ulaşsa da, kurumsal müşterilere yönelik maliyet pahalıdır, ancak küçük ve orta ölçekli iş (SMB) müşterileri için özellikle pahalıdır. Bu çözüm için, işletmeler depolama diskleri için, bir paylaşımın disklerini kullanan VM 'Lerin maliyetiyle birlikte ödeme yapar.

#### <a name="administrative-overhead"></a>Yönetim yükü

S2D kümeleri, güvenli bir durumda düzeltme eki uygulanmış, güncelleştirilmiş ve korunan bir işletim sistemi gerektirir. Bu süreçler ve S2D olağanüstü durum kurtarmayı ayarlamanın karmaşıklığı, S2D yalnızca adanmış BT personeli olan kuruluşlar için uygulanabilir hale gelir.

## <a name="fslogix-profile-containers"></a>FSLogix profil kapsayıcıları

19 Kasım 2018 ' de, [Microsoft FSLogix aldı](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix, birçok profil kapsayıcısı sorunlarını giderir. Aralarında anahtar şunlardır:

- **Performans:** [Fslogix profil kapsayıcıları](https://fslogix.com/products/profile-containers) yüksek performansdadır ve geçmişte engellenmiş önbelleğe alınmış Exchange moduna sahip performans sorunlarını çözer.
- **OneDrive:** FSLogix profil kapsayıcıları olmadan, OneDrive Iş kalıcı olmayan RDSH veya VDı ortamlarında desteklenmez. [OneDrive iş ve FSLogix en iyi uygulamaları,](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) nasıl etkileşime gireceğini açıklar. Daha fazla bilgi için bkz. [sanal masaüstlerinde eşitleme Istemcisini kullanma](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Ek klasörler:** FSLogix, Kullanıcı profillerini ek klasörler içerecek şekilde genişletebilme olanağı sağlar.

Alma işlemi, Microsoft, FSLogix profil kapsayıcılarıyla UPD gibi mevcut kullanıcı profili çözümlerini değiştirmeye başladı.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure Active Directory etki alanı hizmeti ile Azure dosyaları tümleştirmesi

FSLogix profil kapsayıcılarının performansı ve özellikleri buluttan faydalanır. 7 Ağustos 2019 ' de Microsoft Azure dosyalar [Azure Active Directory etki alanı hizmeti (AD DS) Ile Azure dosyaları kimlik doğrulamasının](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)genel kullanıma sunulduğunu duyurmuştur. Hem maliyet hem de yönetim yükünü ele alarak Azure AD DS kimlik doğrulaması ile Azure dosyaları, Windows sanal masaüstü hizmetindeki Kullanıcı profillerine yönelik bir Premium çözümdür.

## <a name="best-practices-for-windows-virtual-desktop"></a>Windows sanal masaüstü için en iyi yöntemler

Windows sanal masaüstü, müşteriler tarafından kullanılmakta olan VM 'lerin boyutu, türü ve sayısı üzerinde tam denetim sağlar. Daha fazla bilgi için bkz. [Windows sanal masaüstü nedir?](overview.md).

Windows sanal masaüstü ortamınızın en iyi yöntemleri takip edin:

- Azure dosyaları depolama hesabı, oturum ana bilgisayar VM 'Leri ile aynı bölgede olmalıdır.
- Azure dosyaları izinleri, [gereksinimler profil kapsayıcılarında](https://docs.microsoft.com/fslogix/overview#requirements)açıklanan izinlerle eşleşmelidir.
- Her konak havuzunun aynı ana görüntüye göre aynı tür ve boyut VM 'si oluşturulması gerekir.
- Yönetim, ölçeklendirme ve güncelleştirme yardımcı olması için her bir konak havuzu sanal makinesi aynı kaynak grubunda olmalıdır.
- En iyi performans için, depolama çözümü ve FSLogix profili kapsayıcısı aynı veri merkezi konumunda olmalıdır.
- Ana görüntünün bulunduğu depolama hesabı, VM 'Lerin sağlandığı aynı bölgede ve abonelikte olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Windows sanal masaüstü ortamı ayarlamak için aşağıdaki kılavuzlardan yararlanabilirsiniz.

- Masaüstü Sanallaştırma çözümünüzü oluşturmaya başlamak için bkz. [Windows sanal masaüstü 'nde kiracı oluşturma](tenant-setup-azure-active-directory.md).
- Windows sanal masaüstü kiracınızda bir konak havuzu oluşturmak için bkz. [Azure Marketi ile konak havuzu oluşturma](create-host-pools-azure-marketplace.md).
- Bulutta tam olarak yönetilen dosya paylaşımları ayarlamak için bkz. [Azure dosyaları paylaşımını ayarlama](/azure/storage/files/storage-files-active-directory-enable).
- FSLogix profil kapsayıcılarını yapılandırmak için, bkz. bir [dosya paylaşımının kullanıldığı konak havuzu için profil kapsayıcısı oluşturma](create-host-pools-user-profile.md).
- Kullanıcıları bir konak havuzuna atamak için bkz. [Windows sanal masaüstü için uygulama gruplarını yönetme](manage-app-groups.md).
- Windows sanal masaüstü kaynaklarınıza bir Web tarayıcısından erişmek için bkz. [Windows sanal masaüstüne bağlanma](connect-web.md).
