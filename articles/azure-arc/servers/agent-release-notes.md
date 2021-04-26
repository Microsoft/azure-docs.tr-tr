---
title: Azure Arc etkin sunucular aracısındaki yenilikler
description: Bu makalede, Azure Arc etkin sunucular aracısına yönelik sürüm notları bulunur. Özetlenen birçok sorun için daha fazla ayrıntı bağlantısı vardır.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: ecff23225f4d482cc1e9a4f7b7724c8ffe0a1d73
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109076"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Azure Arc etkin sunucular aracısındaki yenilikler

Azure Arc etkin sunucular bağlı makine Aracısı, iyileştirmeleri sürekli olarak alır. En son gelişmelerden haberdar olmak için, bu makalede hakkında bilgi verilmektedir:

- En son yayınlar
- Bilinen sorunlar
- Hata düzeltmeleri

## <a name="march-2021"></a>Mart 2021

Sürüm 1,4

## <a name="new-feature"></a>Yeni Özellik

- Şu anda sınırlı Önizlemedeki özel uç noktalar için destek eklendi.
- Azcmagent çıkış kodlarının genişletilmiş listesi.
- Aracı yapılandırma parametreleri artık parametresi olan bir dosyadan okunabilir `--config` .

## <a name="fixed"></a>Sabit

Ağ uç noktası denetimleri artık daha hızlıdır.

## <a name="december-2020"></a>Aralık 2020

Sürüm: 1,3

### <a name="new-feature"></a>Yeni Özellik

Windows Server 2008 R2 için destek eklendi.

### <a name="fixed"></a>Sabit

Çözülen sorun, Linux üzerinde özel betik uzantısının başarıyla yüklenmesini engellemektedir.

## <a name="november-2020"></a>Kasım 2020

Sürüm: 1,2

### <a name="fixed"></a>Sabit

RPM tabanlı dağıtımlarla yükseltildikten sonra ara sunucu yapılandırmasının kaybedilme sorunu çözüldü.

## <a name="october-2020"></a>Ekim 2020

Sürüm: 1.1

### <a name="fixed"></a>Sabit

- Alternatif GC Daemon birimi dosya konumunu işlemek için sabit proxy betiği.
- GuestConfig Aracısı güvenilirlik değişiklikleri.
- US Gov Virginia bölge için GuestConfig Aracısı desteği.
- Bir hata oluşursa, GuestConfig Aracısı uzantısı iletileri daha ayrıntılı olacak şekilde rapor edin.

## <a name="september-2020"></a>Eylül 2020

Sürüm: 1,0 (genel kullanılabilirlik)

### <a name="plan-for-change"></a>Değişiklik planı

- Önizleme aracıları (1,0 ' den eski tüm sürümler) için destek, gelecekteki bir hizmet güncelleştirmesinde kaldırılacaktır.
- Geri dönüş uç noktası için destek kaldırıldı `.azure-automation.net` . Bir ara sunucu varsa, uç noktaya izin vermeniz gerekir `*.his.arc.azure.com` .
- Bağlı makine Aracısı Azure 'da barındırılan bir sanal makinede yüklüyse, VM uzantıları Arc etkin sunucular kaynağından yüklenemez veya değiştirilemez. Bu, sanal makinenin **Microsoft. COMPUTE** ve **Microsoft. hybridcompute** kaynağından çakışan uzantı işlemlerinin gerçekleştirilmesini önmaktır. Tüm uzantı işlemleri için makineye ait **Microsoft. COMPUTE** kaynağını kullanın.
- Konuk yapılandırma işleminin adı, *GCD* 'de Linux üzerinde *gcad* ve *gcservice* ile *gcarcservice* arasında değiştirilmiştir.

### <a name="new-feature"></a>Yeni Özellik

- `azcmagent logs`Destek için bilgi toplama seçeneği eklendi.
- `azcmagent license`EULA 'yı görüntüleme seçeneği eklendi.
- `azcmagent show --json`, Kolayca ayrılabilir biçimdeki çıkış Aracısı durumuna eklenen seçenek.
- `azcmagent show`Sunucunun Azure 'da barındırılan bir sanal makinede olup olmadığını göstermek için çıkışa bayrak eklendi.
- `azcmagent disconnect --force-local-only`Azure hizmetine ulaşılamadığından yerel aracı durumunun sıfırlanmasına izin verme seçeneği eklendi.
- `azcmagent connect --cloud`Diğer bulutları destekleme seçeneği eklendi. Bu sürümde, aracı sürümü sırasında yalnızca Azure hizmeti desteklenir.
- Aracı, Azure tarafından desteklenen dillerde yerelleştirildi.

### <a name="fixed"></a>Sabit

- Bağlantı denetimi geliştirmeleri.
- Linux üzerinde aracı yükseltilirken, ara sunucu ayarları kaybedilmekte olan sorun düzeltildi.
- Windows Server 2012 R2 çalıştıran sunucuya aracı yüklenmeye çalışıldığında oluşan sorunlar çözüldü.
- Uzantı yükleme güvenilirliği geliştirmeleri

## <a name="august-2020"></a>Ağustos 2020

Sürüm: 0,11

- Bu sürüm daha önce Ubuntu 20,04 için destek duyurmuştur. Bazı Azure VM uzantıları Ubuntu 20,04 'yi desteklemediğinden, Ubuntu 'nın bu sürümü için destek kaldırılıyor.

- Uzantı dağıtımları için güvenilirlik iyileştirmeleri.

### <a name="known-issues"></a>Bilinen sorunlar

Linux aracısının eski bir sürümünü kullanıyorsanız ve bir ara sunucu kullanmak üzere yapılandırıldıysa, yükseltmeden sonra proxy sunucu ayarını yeniden yapılandırmanız gerekir. Bunu yapmak için öğesini çalıştırın `sudo azcmagent_proxy add http://proxyserver.local:83` .

## <a name="next-steps"></a>Sonraki adımlar

Birden çok karma makinede yay etkin sunucuları değerlendirmeden veya etkinleştirmeden önce, gereksinimleri anlamak için [bağlı makine aracısına genel bakış](agent-overview.md) ' ı ve aracı hakkındaki teknik ayrıntıları ve dağıtım yöntemlerini gözden geçirin.