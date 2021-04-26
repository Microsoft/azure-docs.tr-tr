---
title: Kullanılabilirlik ve artıklık Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Azure Key Vault kullanılabilirliği ve artıklığı hakkında bilgi edinin.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 3c5afc92044fcb109bedd38298b0b027ebeb437d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749699"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault kullanılabilirliği ve yedekliliği

, Tek tek hizmet bileşenleri başarısız olsa bile, anahtarlarınızın ve sırlarınızın uygulamanız için kullanılabilir kalmasını sağlamak için birden çok artıklık katmanı Azure Key Vault.

> [!NOTE]
> Bu kılavuz, kasaların için geçerlidir. Yönetilen HSM havuzları, farklı bir yüksek kullanılabilirlik ve olağanüstü durum kurtarma modeli kullanır. Daha fazla bilgi için bkz. [YÖNETILEN HSM olağanüstü durum kurtarma Kılavuzu](../managed-hsm/disaster-recovery-guide.md) .

Anahtar kasanızın içeriği bölge içinde ve bir ikincil bölgeye en az 150 mil uzakta, ancak anahtarlarınızın ve gizli dizilerlerinizin yüksek dayanıklılığını sürdürmek için aynı coğrafya dahilinde çoğaltılır. Belirli bölge çiftleri hakkında daha fazla bilgi için bkz. [Azure eşleştirilmiş bölgeler](../../best-practices-availability-paired-regions.md). Eşleştirilmiş bölge modelinin özel durumu Brezilya Güney, yalnızca Brezilya Güney içinde verileri güncel tutma seçeneğinin kullanılmasına izin verir. Brezilya Güney, tek konum/bölge içinde verilerinizi üç kez çoğaltmak için bölge yedekli depolama (ZRS) kullanır. AKV Premium için, HSM 'lerden veri çoğaltmak için 3 bölgenin yalnızca 2 ' i kullanılır.  

Anahtar Kasası hizmetindeki tek tek bileşenler başarısız olursa, işlevselliğe bir azalma olmadığından emin olmak için, içindeki bölge adımındaki alternatif bileşenler. Bu işlemi başlatmak için herhangi bir işlem yapmanız gerekmez, otomatik olarak gerçekleşir ve sizin için saydam hale gelir.

Tüm Azure bölgesinin kullanılamadığı nadir bir olayda, bu bölgedeki Azure Key Vault yaptığınız istekler, Brezilya Güney bölgesinin olması dışında ikincil bir bölgeye otomatik olarak yönlendirilir (*Yük devredildi*). Birincil bölge yeniden kullanılabilir olduğunda, istekler birincil bölgeye geri yönlendirilir (*başarısız* olur). Yine de, bu otomatik olarak gerçekleştiğinden herhangi bir işlem yapmanız gerekmez.

Brezilya Güney bölgesinde, Azure anahtar kasalarınızın bir bölge hatası senaryosunda kurtarılmasını planlamalısınız. Azure anahtar kasanızı yedeklemek ve seçtiğiniz bir bölgeye geri yüklemek için [Azure Key Vault yedekleme](backup.md)'de ayrıntılı adımları uygulayın. 

Bu yüksek kullanılabilirlik tasarımı aracılığıyla Azure Key Vault bakım etkinlikleri için kapalı kalma süresi gerektirmez.

Bilmeniz için bazı uyarılar vardır:

* Bölge yük devretmesi durumunda, hizmetin yük devretmesinin tamamlanması birkaç dakika sürebilir. Yük devretmeden önce bu süre içinde yapılan istekler başarısız olabilir.
* Anahtar kasanıza bağlanmak için özel bağlantı kullanıyorsanız, bağlantı yük devri durumunda bağlantının yeniden kurulması 20 dakikaya kadar sürebilir. 
* Yük devretme sırasında, anahtar kasanızın salt okuma modunda olması gerekir. Bu modda desteklenen istekler şunlardır:
  * Sertifikaları listeleme
  * Sertifika Al
  * Gizli dizileri listeleme
  * Gizli dizileri al
  * Anahtarları Listele
  * Get (özellikleri) anahtarları
  * Şifreleme
  * Şifre Çözme
  * Ilacağını
  * Unwrap
  * Doğrulama
  * İşaret
  * Backup

* Yük devretme sırasında, Anahtar Kasası özelliklerinde değişiklik yapamazsınız. Erişim ilkesi veya güvenlik duvarı yapılandırma ve ayarlarını değiştiremeyeceksiniz.

* Yük devretme işlemi geri alındıktan sonra, tüm istek türleri (okuma *ve* yazma istekleri dahil) kullanılabilir.
