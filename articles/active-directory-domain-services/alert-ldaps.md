---
title: Azure AD Domain Services 'da Güvenli LDAP uyarılarını çözün | Microsoft Docs
description: Azure Active Directory Domain Services için Güvenli LDAP ile sık karşılaşılan uyarıları nasıl giderebileceğinizi ve çözeceğinizi öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15c1f3a1731edf7b45061646d43688b4aacc6104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620316"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Bilinen sorunlar: Azure Active Directory Domain Services Güvenli LDAP uyarılar

Azure Active Directory Domain Services (Azure AD DS) ile iletişim kurmak için Basit Dizin Erişim Protokolü (LDAP) kullanan uygulamalar ve hizmetler, [GÜVENLI LDAP kullanacak şekilde yapılandırılabilir](tutorial-configure-ldaps.md). Güvenli LDAP 'nin düzgün çalışması için uygun bir sertifika ve gerekli ağ bağlantı noktalarının açık olması gerekir.

Bu makale, Azure AD DS 'da Güvenli LDAP erişimiyle genel uyarıları anlamanıza ve çözmenize yardımcı olur.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Güvenli LDAP ağ yapılandırması

### <a name="alert-message"></a>Uyarı iletisi

*Internet üzerinden Güvenli LDAP, yönetilen etki alanı için etkinleştirilmiştir. Ancak, 636 numaralı bağlantı noktasına erişim bir ağ güvenlik grubu kullanılarak kilitlenmemiştir. Bu, yönetilen etki alanındaki Kullanıcı hesaplarını parola yanılma saldırısı saldırılarına maruz bırakabilir.*

### <a name="resolution"></a>Çözüm

Güvenli LDAP etkinleştirdiğinizde, gelen LDAPS erişimini belirli IP adreslerine kısıtlayan ek kurallar oluşturmanız önerilir. Bu kurallar, yönetilen etki alanını deneme yanılma saldırılarına karşı korur. Güvenli LDAP için TCP bağlantı noktası 636 erişimini kısıtlamak üzere ağ güvenlik grubunu güncelleştirmek için aşağıdaki adımları izleyin:

1. Azure portal **ağ güvenlik grupları**' nı arayıp seçin.
1. Yönetilen etki alanınız ile ilişkili ağ güvenlik grubunu seçin (örneğin, *Aeklemesine-contoso.com-NSG*) ve ardından **gelen güvenlik kuralları** ' nı seçin.
1. 636 numaralı TCP bağlantı noktası için bir kural oluşturmak için **+ Ekle** ' yi seçin. Gerekirse, bir kural oluşturmak için pencerede **Gelişmiş** ' i seçin.
1. **Kaynak** için açılan menüden *IP adresleri* ' ni seçin. Güvenli LDAP trafiği için erişim vermek istediğiniz kaynak IP adreslerini girin.
1. **Hedef** olarak *herhangi birini* seçin ve ardından **hedef bağlantı noktası aralıkları** için *636* girin.
1. **Protokol** olarak *TCP* ve *izin ver* **eylemini** ayarlayın.
1. Kural için öncelik belirtin ve *RestrictLDAPS* gibi bir ad girin.
1. Hazırsanız, kuralı oluşturmak için **Ekle** ' yi seçin.

Yönetilen etki alanının sistem durumu otomatik olarak iki saat içinde güncelleştirilir ve uyarıyı kaldırır.

> [!TIP]
> TCP bağlantı noktası 636, Azure AD DS sorunsuz bir şekilde çalışması için gerekli tek kural değildir. Daha fazla bilgi edinmek için bkz. [Azure AD DS ağ güvenlik grupları ve gerekli bağlantı noktaları](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Güvenli LDAP sertifikanın süresi doluyor

### <a name="alert-message"></a>Uyarı iletisi

*Yönetilen etki alanı için Güvenli LDAP sertifikasının kullanım süreleri [Date]].*

### <a name="resolution"></a>Çözüm

[GÜVENLI LDAP için sertifika oluşturma](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)adımlarını izleyerek yeni BIR Güvenli LDAP sertifikası oluşturun. Değiştirme sertifikasını Azure AD DS 'a uygulayın ve sertifikayı Güvenli LDAP kullanarak bağlanan tüm istemcilere dağıtın.

## <a name="next-steps"></a>Sonraki adımlar

Hala sorun yaşıyorsanız, ek sorun giderme yardımı için [bir Azure destek isteği açın][azure-support] .

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
