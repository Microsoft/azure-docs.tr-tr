---
title: Azure Active Directory Kimlik Koruması riskleri araştırma
description: Azure Active Directory Kimlik Koruması ' de riskli kullanıcıları, algılamaları ve oturum açma işlemlerini nasıl araştırılacağını öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e04cadef1f6a4aee7d8f807fc7aaa999ba6da8d7
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887097"
---
# <a name="how-to-investigate-risk"></a>Nasıl yapılır: riski araştırma

Kimlik koruması, kuruluşların ortamlarında kimlik risklerini araştırmak için kullanabilecekleri üç rapor sağlar. Bu raporlar **riskli kullanıcılar**, **riskli oturum**açmalar ve **risk algılamaları**. Olayların araştırılması, güvenlik stratejinizde zayıf noktaları daha iyi anlamak ve tanımlamak için önemli bir anahtardır.

Tüm üç rapor, içindeki olayların indirilemelerine izin verir. Azure portal dışında daha fazla analiz için CSV biçimi. Riskli kullanıcılar ve riskli oturum açma işlemleri raporları, en son 2500 girişin indirilemelerine izin verir, ancak risk algılamaları raporu en son 5000 kayıtları indirmeye izin verir.

Kuruluşlar, verileri bir kuruluş olarak erişimi olabilecek diğer kaynaklarla toplamak için Microsoft Graph API tümleştirmelerinden yararlanabilir.

**Azure portal** > **Azure Active Directory** > **güvenlik**bölümünde üç rapor bulunur.

## <a name="navigating-the-reports"></a>Raporlarda gezinme

Her rapor, raporun en üstünde gösterilen süre için tüm algılamalardan oluşan bir liste ile başlatılır. Her rapor, sütunları yönetici tercihine göre ekleme veya kaldırma için izin verir. Yöneticiler içindeki verileri indirmeyi seçebilir. CSV biçimi. Raporlar, raporun üst kısmında filtreler kullanılarak filtrelenebilir.

Tek tek girişlerin seçilmesi, raporun üst kısmında, tehlikede veya güvende olan bir oturum açmayı onaylama, bir kullanıcıyı tehlikede onaylama veya Kullanıcı riskini kapatma gibi ek girişler sağlayabilir.

Tek tek girişlerin seçilmesi, algılamaların altına bir ayrıntılar penceresi genişletir. Ayrıntılar görünümü, yöneticilerin her bir algılama üzerinde eylemleri araştırmasını ve gerçekleştirmesini sağlar. 

![Riskli oturum açma işlemlerini ve ayrıntılarını gösteren örnek kimlik koruması raporu](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Riskli kullanıcılar

Riskli kullanıcılar raporu tarafından sağlanacak bilgiler ile yöneticiler şunları bulabilir:

- Risk altında olan kullanıcılar risk düzeltildi mi, ne de risk kapatıldı mi?
- Algılamalar hakkındaki ayrıntılar
- Riskli oturum açma işlemlerinin geçmişi
- Risk geçmişi
 
Yöneticiler daha sonra bu olaylar üzerinde işlem yapmak için seçim yapabilir. Yöneticiler şunları yapabilir:

- Kullanıcı parolasını sıfırlayın
- Kullanıcının güvenliğini onaylama
- Kullanıcı riskini kapat
- Kullanıcının oturum açmasını engelle
- Azure ATP 'yi kullanarak daha fazla araştırma yapın

## <a name="risky-sign-ins"></a>Riskli oturum açma işlemleri

Riskli oturum açma işlemleri raporu, son 30 güne kadar (1 ay) filtrelenebilir verileri içerir.

Riskli oturum açma işlemleri raporu tarafından belirtilen bilgiler ile yöneticiler şunları bulabilir:

- Hangi oturum açma riskleri risk altında sınıflandırıldı, tehlikeye atıldığı, güvenli, kapatıldı veya düzeltildi.
- Oturum açma girişimleri ile ilişkili gerçek zamanlı ve toplu risk düzeyleri.
- Tetiklenmiş algılama türleri
- Koşullu erişim ilkeleri uygulandı
- MFA ayrıntıları
- Cihaz bilgileri
- Uygulama bilgileri
- Konum bilgileri

Yöneticiler daha sonra bu olaylar üzerinde işlem yapmak için seçim yapabilir. Yöneticiler şunları yapabilir:

- Oturum açma güvenliğini Onayla
- Oturum açmayı güvenli Onayla

## <a name="risk-detections"></a>Risk algılamaları

Risk algılama raporu, son 90 güne kadar (3 ay) filtrelenebilir verileri içerir.

Risk algılamaları raporu tarafından sağlanabilecek bilgiler ile yöneticiler şunları bulabilir:

- Türü de dahil olmak üzere her risk algılaması hakkında bilgi.
- Aynı anda tetiklenen diğer riskler
- Oturum açma denemesi konumu
- Microsoft Cloud App Security (MCAS) ' den daha fazla ayrıntı için bağlantı.

Yöneticiler daha sonra toplanan bilgilere göre eylemler gerçekleştirmek için kullanıcının risk veya oturum açma raporuna geri dönerek seçim yapabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Riskleri azaltmak için kullanılabilir ilkeler](concept-identity-protection-policies.md)

- [Oturum açma ve Kullanıcı risk ilkelerini etkinleştir](howto-identity-protection-configure-risk-policies.md)
