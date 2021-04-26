---
title: MFA sunucusunu Yapılandırma-Azure Active Directory
description: Azure portal Azure MFA sunucusu ayarlarını yapılandırma hakkında bilgi edinin
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb28f415b7d08b4c4430ed90a7ccbfe3a333416d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689054"
---
# <a name="configure-mfa-server-settings"></a>MFA sunucu ayarlarını yapılandırma

Bu makale, Azure portal Azure MFA sunucu ayarlarını yönetmenize yardımcı olur.

> [!IMPORTANT]
> 1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sunmaz. Kullanıcılardan Multi-Factor Authentication istemek isteyen yeni müşteriler bulut tabanlı Azure AD Multi-Factor Authentication kullanmalıdır. MFA sunucusunu 1 Temmuz 'dan önce etkinleştiren mevcut müşteriler, en son sürümü ve gelecekteki güncelleştirmeleri indirebilir ve her zamanki gibi etkinleştirme kimlik bilgilerini oluşturabilir.

Aşağıdaki MFA sunucusu ayarları kullanılabilir:

| Özellik | Açıklama |
| ------- | ----------- |
| Sunucu ayarlar | Ortamınızı başlatmak için MFA sunucusunu indirin ve etkinleştirme kimlik bilgilerini oluşturun |
| [Bir kerelik geçiş](#one-time-bypass) | Kullanıcının Multi-Factor Authentication 'ı sınırlı bir süre yapmadan kimlik doğrulamasına izin verin. |
| [Önbelleğe alma kuralları](#caching-rules) |  Önbelleğe alma, birincil olarak ilk istek devam ederken VPN gibi şirket içi sistemler, birden çok doğrulama isteği gönderirken kullanılır. Bu özellik, sonraki isteklerin otomatik olarak başarılı olmasını sağlar ve Kullanıcı devam eden ilk doğrulamayı başarılı olduktan sonra. |
| Sunucu durumu | Sürüm, durum, IP ve son iletişim saat ve tarih dahil olmak üzere şirket içi MFA sunucularınızın durumuna bakın. |

## <a name="one-time-bypass"></a>Bir kerelik geçiş

Tek seferlik atlama özelliği, bir kullanıcının Multi-Factor Authentication gerçekleştirmeden tek bir kez kimlik doğrulaması yapmasına olanak sağlar. Atlama geçicidir ve belirtilen sayıda saniye sonra süresi dolar. Mobil uygulamanın veya telefonun bir bildirim veya telefon araması almaışında, kullanıcının istenen kaynağa erişebilmesi için bir kerelik geçişe izin verebilirsiniz.

Bir kerelik geçiş oluşturmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
1. **Azure Active Directory** arayıp seçin, sonra **güvenlik**  >  **MFA**  >  **bir kerelik geçişe** gidin.
1. **Add (Ekle)** seçeneğini belirleyin.
1. Gerekirse, atlama için çoğaltma grubunu seçin.
1. Kullanıcı adını olarak girin `username@domain.com` . Atlamamasının son olması gereken saniye sayısını ve atlama nedenini girin.
1. **Add (Ekle)** seçeneğini belirleyin. Zaman sınırı hemen yürürlüğe girer. Kullanıcının bir kerelik atlama süresi dolmadan önce oturum açması gerekir.

Aynı pencereden tek seferlik atlama raporunu da görüntüleyebilirsiniz.

## <a name="caching-rules"></a>Önbelleğe alma kuralları

_Önbelleğe alma_ özelliğini kullanarak bir kullanıcının kimliği doğrulandıktan sonra kimlik doğrulama girişimlerine izin vermek için bir zaman aralığı ayarlayabilirsiniz. Belirli bir süre içinde Kullanıcı için sonraki kimlik doğrulama girişimleri otomatik olarak başarılı olur.

Önbelleğe alma, birincil olarak ilk istek devam ederken VPN gibi şirket içi sistemler, birden çok doğrulama isteği gönderirken kullanılır. Bu özellik, sonraki isteklerin otomatik olarak başarılı olmasını sağlar ve Kullanıcı devam eden ilk doğrulamayı başarılı olduktan sonra.

>[!NOTE]
> Önbelleğe alma özelliği, Azure Active Directory (Azure AD) için oturum açma işlemleri için kullanılmak üzere tasarlanmamıştır.

Önbelleğe almayı ayarlamak için aşağıdaki adımları izleyin:

1. **Azure Active Directory**  >  **güvenliği**  >  **MFA**  >  **önbelleğe alma kurallarını** inceleyin.
1. **Add (Ekle)** seçeneğini belirleyin.
1. Açılan listeden **önbellek türünü** seçin. En fazla **önbellek saniyesi** sayısını girin.
1. Gerekirse, bir kimlik doğrulama türü seçin ve bir uygulama belirtin.
1. **Add (Ekle)** seçeneğini belirleyin.

## <a name="next-steps"></a>Sonraki adımlar

MFA sunucusunun Web konsolundan ek MFA sunucu yapılandırma seçenekleri mevcuttur. Ayrıca, [yüksek kullanılabilirlik Için Azure MFA sunucusunu yapılandırabilirsiniz](howto-mfaserver-deploy-ha.md).
