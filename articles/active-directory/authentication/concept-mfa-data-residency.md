---
title: Azure AD çok faktörlü kimlik doğrulaması verileri yerleşimi
description: Kişisel ve kurumsal verileri Azure AD çok faktörlü kimlik doğrulamasının ne olduğunu, sizin ve kullanıcılarınız hakkındaki verileri ve kaynak ülke/bölge dahilinde hangi verilerin kaldığını öğrenin.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c8df67a8cfed92e478caacca1171b7a48fa9ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932905"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Azure AD çok faktörlü kimlik doğrulaması için veri yerleşimi ve müşteri verileri

Azure Active Directory (Azure AD), Microsoft 365 veya Azure gibi bir Microsoft Online hizmetine abone olurken, müşteri verilerini bir kuruluşun sağladığı adrese göre coğrafi bir konumda depolar. Müşteri verilerinizin nerede depolandığını öğrenmek için, Microsoft Güven Merkezi ' nde [verilerinizin nerede](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) bulunduğu hakkında bilgi edinmek için bkz..

Bulut tabanlı Azure AD çok faktörlü kimlik doğrulaması ve Azure çok faktörlü kimlik doğrulama sunucusu işleme ve kişisel verileri ve kurumsal verileri depolama. Bu makalede, verilerin depolandığı ve nerede saklandığı özetlenmektedir.

Azure AD çok faktörlü kimlik doğrulama hizmeti Birleşik Devletler, Avrupa ve Asya Pasifik veri merkezlerine sahiptir. Aşağıdaki etkinlikler, belirtilen durumlar dışında bölgesel veri merkezlerinde kaynaklarından geliyor:

* Çok faktörlü kimlik doğrulama telefon çağrıları Birleşik Devletler veri merkezinden kaynaklanarak genel sağlayıcılar tarafından yönlendirilir.
* Diğer bölgelerden gelen genel amaçlı kullanıcı kimlik doğrulama istekleri şu anda kullanıcının konumuna göre işlenir.
* Microsoft Authenticator uygulamayı kullanan anında iletme bildirimleri, kullanıcının konumuna bağlı olarak bölgesel veri merkezlerinde işlenir. Apple Anında İletilen Bildirim Servisi gibi satıcıya özgü cihaz Hizmetleri, kullanıcının konumunun dışında olabilir.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD çok faktörlü kimlik doğrulaması tarafından depolanan kişisel veriler

Kişisel veriler, belirli bir kişiyle ilişkili olan kullanıcı düzeyindeki bir bilgi. Aşağıdaki veri depoları kişisel bilgiler içerir:

* Engellenen kullanıcılar
* Atlanan kullanıcılar
* Microsoft Authenticator cihaz belirteci değişiklik istekleri
* Çok faktörlü kimlik doğrulaması etkinlik raporları — çok faktörlü kimlik doğrulama etkinliğini, çok faktörlü kimlik doğrulaması şirket içi bileşenlerinde depola: NPS uzantısı, AD FS bağdaştırıcısı ve MFA sunucusu.
* Microsoft Authenticator etkinleştirmeleri

Bu bilgiler 90 gün boyunca tutulur.

Azure AD çok faktörlü kimlik doğrulaması, Kullanıcı adları, telefon numaraları veya IP adresleri gibi kişisel verileri günlüğe kaydetmez. Ancak, *Userobjectıd* kullanıcılara yönelik kimlik doğrulama girişimlerini tanımlar. Günlük verileri 30 gün boyunca depolanır.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD çok faktörlü kimlik doğrulaması tarafından depolanan veriler

Azure AD B2C kimlik doğrulaması, NPS uzantısı ve Windows Server 2016 ya da 2019 Active Directory Federasyon Hizmetleri (AD FS) (AD FS) bağdaştırıcı dışında Azure genel bulutları için aşağıdaki kişisel veriler depolanır:

| Olay türü                           | Veri deposu türü |
|--------------------------------------|-----------------|
| OATH belirteci                           | Çok faktörlü kimlik doğrulama günlükleri     |
| Tek yönlü SMS                          | Çok faktörlü kimlik doğrulama günlükleri     |
| Sesli arama                           | Çok faktörlü kimlik doğrulama günlükleri<br/>Çok faktörlü kimlik doğrulaması etkinlik raporu veri deposu<br/>Engellenen kullanıcılar (sahtekarlık raporlandıysa) |
| Microsoft Authenticator bildirimi | Çok faktörlü kimlik doğrulama günlükleri<br/>Çok faktörlü kimlik doğrulaması etkinlik raporu veri deposu<br/>Engellenen kullanıcılar (sahtekarlık raporlandıysa)<br/>Microsoft Authenticator cihaz belirteci değiştiğinde istekleri Değiştir |

Microsoft Azure Kamu, Microsoft Azure Almanya, 21Vianet tarafından çalıştırılan Microsoft Azure, Azure AD B2C kimlik doğrulaması, NPS uzantısı ve Windows Server 2016 ya da 2019 AD FS bağdaştırıcısı için aşağıdaki kişisel veriler depolanır:

| Olay türü                           | Veri deposu türü |
|--------------------------------------|-----------------|
| OATH belirteci                           | Çok faktörlü kimlik doğrulama günlükleri<br/>Çok faktörlü kimlik doğrulaması etkinlik raporu veri deposu |
| Tek yönlü SMS                          | Çok faktörlü kimlik doğrulama günlükleri<br/>Çok faktörlü kimlik doğrulaması etkinlik raporu veri deposu |
| Sesli arama                           | Çok faktörlü kimlik doğrulama günlükleri<br/>Çok faktörlü kimlik doğrulaması etkinlik raporu veri deposu<br/>Engellenen kullanıcılar (sahtekarlık raporlandıysa) |
| Microsoft Authenticator bildirimi | Çok faktörlü kimlik doğrulama günlükleri<br/>Çok faktörlü kimlik doğrulaması etkinlik raporu veri deposu<br/>Engellenen kullanıcılar (sahtekarlık raporlandıysa)<br/>Microsoft Authenticator cihaz belirteci değiştiğinde istekleri Değiştir |

### <a name="data-stored-by-azure-multifactor-authentication-server"></a>Azure çok faktörlü kimlik doğrulama sunucusu tarafından depolanan veriler

Azure çok faktörlü kimlik doğrulama sunucusu kullanıyorsanız, aşağıdaki kişisel veriler depolanır.

> [!IMPORTANT]
> 1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için çok faktörlü kimlik doğrulama sunucusu sunmayacaktır. Kullanıcılardan çok faktörlü kimlik doğrulaması gerektirmek isteyen yeni müşteriler, bulut tabanlı Azure AD çok faktörlü kimlik doğrulaması kullanmalıdır. 1 Temmuz 2019 tarihinden önce çok faktörlü Authentication Server 'ı etkinleştiren mevcut müşteriler en son sürümü ve güncelleştirmeleri indirebilir ve her zamanki gibi etkinleştirme kimlik bilgilerini oluşturabilir.

| Olay türü                           | Veri deposu türü |
|--------------------------------------|-----------------|
| OATH belirteci                           | Çok faktörlü kimlik doğrulama günlükleri<br />Çok faktörlü kimlik doğrulaması etkinlik raporu veri deposu |
| Tek yönlü SMS                          | Çok faktörlü kimlik doğrulama günlükleri<br />Çok faktörlü kimlik doğrulaması etkinlik raporu veri deposu |
| Sesli arama                           | Çok faktörlü kimlik doğrulama günlükleri<br />Çok faktörlü kimlik doğrulaması etkinlik raporu veri deposu<br />Engellenen kullanıcılar (sahtekarlık raporlandıysa) |
| Microsoft Authenticator bildirimi | Çok faktörlü kimlik doğrulama günlükleri<br />Çok faktörlü kimlik doğrulaması etkinlik raporu veri deposu<br />Engellenen kullanıcılar (sahtekarlık raporlandıysa)<br />Microsoft Authenticator cihaz belirteci değiştiğinde istekleri değiştirme |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD çok faktörlü kimlik doğrulaması tarafından depolanan Kurumsal veriler

Kuruluş verileri, yapılandırma veya ortam kurulumunu açığa çıkaran kiracı düzeyinde bir bilgi olabilir. Aşağıdaki Azure portal çok faktörlü kimlik doğrulama sayfalarındaki kiracı ayarları, gelen telefon kimlik doğrulama istekleri için kilitleme eşikleri veya arayan KIMLIĞI bilgileri gibi kuruluş verilerini saklayabilir:

* Hesap kilitleme
* Sahtekarlık uyarısı
* Bildirimler
* Telefon araması ayarları

Azure çok faktörlü kimlik doğrulama sunucusu için aşağıdaki Azure portal sayfalarında Kurumsal veriler bulunabilir:

* Sunucu ayarlar
* Bir kerelik geçiş
* Önbelleğe alma kuralları
* Çok faktörlü kimlik doğrulama sunucusu durumu

## <a name="multifactor-authentication-logs-location"></a>Çok faktörlü kimlik doğrulama günlükleri konumu

Aşağıdaki tabloda, Genel bulutların hizmet günlüklerinin konumu gösterilmektedir.

| Genel bulut| Oturum açma günlükleri | Çok faktörlü kimlik doğrulaması etkinlik raporu        | Çok faktörlü kimlik doğrulama hizmeti günlükleri       |
|-------------|--------------|----------------------------------------|------------------------|
| Birleşik Devletler          | Birleşik Devletler           | Birleşik Devletler                                     | Birleşik Devletler                     |
| Avrupa      | Avrupa       | Birleşik Devletler                                     | Avrupa <sup>2</sup>    |
| Avustralya   | Avustralya    | Birleşik Devletler<sup>1</sup>                         | Avustralya <sup>2</sup> |

<sup>1</sup> OATH kodu günlükleri Avustralya 'da depolanır.

<sup>2</sup> Sesli çağrılar çok faktörlü kimlik doğrulama hizmeti günlükleri Birleşik Devletler depolanır.

Aşağıdaki tabloda, bağımsız bulutları için hizmet günlüklerinin konumu gösterilmektedir.

| Bağımsız bulut                      | Oturum açma günlükleri                         | Çok faktörlü kimlik doğrulaması etkinlik raporu (kişisel verileri içerir)| Çok faktörlü kimlik doğrulama hizmeti günlükleri |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Almanya              | Almanya                              | Birleşik Devletler                            | Birleşik Devletler               |
| Azure China 21Vianet                 | Çin                                | Birleşik Devletler                            | Birleşik Devletler               |
| Microsoft kamu bulutu           | Birleşik Devletler                                   | Birleşik Devletler                            | Birleşik Devletler               |

Çok faktörlü kimlik doğrulama etkinliği raporları, Kullanıcı asıl adı (UPN) ve tüm telefon numarası gibi kişisel verileri içerir.

Çok faktörlü kimlik doğrulama hizmeti günlükleri hizmeti çalıştırmak için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

Bulut tabanlı Azure AD çok faktörlü kimlik doğrulaması ve Azure çok faktörlü kimlik doğrulama sunucusu tarafından hangi kullanıcı bilgilerinin toplandığı hakkında daha fazla bilgi için bkz. [Azure AD çok faktörlü kimlik doğrulama kullanıcı verileri koleksiyonu](howto-mfa-reporting-datacollection.md).
