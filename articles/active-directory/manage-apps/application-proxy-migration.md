---
title: Azure AD Uygulama Ara Sunucusu yükseltin | Microsoft Docs
description: Microsoft Forefront veya Unified Access Gateway 'ten yükseltiyorsanız hangi proxy çözümünün en iyi olduğunu seçin.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52cb4480e8b7820d656b8ab66f12486c2062dca5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443500"
---
# <a name="compare-remote-access-solutions"></a>Uzaktan erişim çözümlerini karşılaştırın

Azure Active Directory Uygulama Ara Sunucusu, Microsoft 'un sunduğu iki uzaktan erişim çözümünden biridir. Diğer bir deyişle, şirket içi sürümü olan Web uygulaması ara sunucusu. Bu iki çözüm, Microsoft tarafından sunulan önceki ürünlerin yerini alır: Microsoft Forefront Threat Management Gateway (TMG) ve Birleşik erişim ağ geçidi (UAG). Bu dört çözümün birbirleriyle nasıl karşılaştırılacağını anlamak için bu makaleyi kullanın. Hala kullanım dışı olan TMG veya UAG çözümlerini kullanmaya devam ettiğiniz için, uygulama proxy 'sinin birine geçişinizi planlamaya yardımcı olması için bu makaleyi kullanın. 


## <a name="feature-comparison"></a>Özellik karşılaştırması

Tehdit yönetimi ağ geçidi (TMG), Birleşik erişim ağ geçidi (UAG), Web uygulaması ara sunucusu (WAP) ve Azure AD Uygulama Ara Sunucusu (AP) birbirleriyle nasıl karşılaştırılacağını anlamak için bu tabloyu kullanın.

| Özellik | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Sertifika kimlik doğrulaması | Yes | Yes | - | - |
| Tarayıcı uygulamalarını seçmeli olarak yayınla | Yes | Yes | Yes | Yes |
| Ön kimlik doğrulaması ve çoklu oturum açma | Yes | Yes | Yes | Yes | 
| Katman 2/3 güvenlik duvarı | Yes | Yes | - | - |
| İletme Proxy özellikleri | Yes | - | - | - |
| VPN özellikleri | Yes | Yes | - | - |
| Zengin protokol desteği | - | Yes | Evet, HTTP üzerinden çalışıyorsa | Evet, HTTP üzerinden veya Uzak Masaüstü Ağ Geçidi aracılığıyla çalışıyorsa |
| ADFS proxy sunucusu işlevi görür | - | Yes | Yes | - |
| Uygulama erişimi için bir portal | - | Yes | - | Yes |
| Yanıt gövdesi bağlantı çevirisi | Yes | Yes | - | Yes | 
| Üst bilgiyle kimlik doğrulama | - | Yes | - | Evet, PingAccess ile | 
| Bulut ölçeğinde güvenlik | - | - | - | Yes | 
| Koşullu Erişim | - | Yes | - | Yes |
| Sivilleştirilmiş bölgede bileşen yok (DMZ) | - | - | - | Yes |
| Gelen bağlantı yok | - | - | - | Yes |

Çoğu senaryoda, modern çözüm olarak Azure AD Uygulama Ara Sunucusu önerilir. Web uygulaması ara sunucusu yalnızca AD FS için bir proxy sunucu gerektiren senaryolarda tercih edilir ve Azure Active Directory özel etki alanlarını kullanamazsınız. 

Azure AD Uygulama Ara Sunucusu, benzer ürünlerle karşılaştırıldığında aşağıdakiler de dahil olmak üzere benzersiz avantajlar sunar:

- Azure AD 'yi şirket içi kaynaklara genişletme
   - Bulut ölçeğinde güvenlik ve koruma
   - Koşullu erişim ve Multi-Factor Authentication gibi özelliklerin etkinleştirilmesi kolaydır
- Sivilleştirilmiş bölgede bileşen yok
- Gelen bağlantı gerekli değil
- Kullanıcılarınızın Microsoft 365, Azure AD Tümleşik SaaS uygulamaları ve şirket içi Web uygulamalarınız dahil tüm uygulamalarına gidebilmeleri için bir uygulamalarım sayfası. 


## <a name="next-steps"></a>Sonraki adımlar

- [Şirket içi uygulamalara güvenli uzaktan erişim sağlamak için Azure AD Uygulama Ara Sunucusu kullanma](application-proxy.md)
