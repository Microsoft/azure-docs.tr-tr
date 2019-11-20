---
title: Azure AD Connect tarafından eşitlenen öznitelikler | Microsoft Docs
description: Azure Active Directory eşitlenecek öznitelikleri listeler.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e95b230d4f9699f15296ba94946c7063cabd0516
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847203"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Eşitleme Azure AD Connect: Azure Active Directory ile eşitlenen öznitelikler
Bu konu, Azure AD Connect eşitleme tarafından eşitlenen öznitelikleri listeler.  
Öznitelikler, ilgili Azure AD uygulamasına göre gruplandırılır.

## <a name="attributes-to-synchronize"></a>Eşitleneceği öznitelikler
Ortak bir soru, *eşitlenmesi gereken en düşük öznitelik listesidir*. Varsayılan ve önerilen yaklaşım, bulutta tam bir GAL (genel adres listesi) oluşturulabilir ve Office 365 iş yüklerindeki tüm özellikleri almak için varsayılan öznitelikleri tutmalıdır. Bazı durumlarda, bu öznitelikler gizli veya PII (kişisel olarak tanımlanabilir bilgiler) verilerini içerdiğinden, kuruluşunuzun buluta eşitlenmesini istemediğiniz bazı öznitelikler vardır:  
Hatalı öznitelikleri ![](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

Bu durumda, bu konudaki özniteliklerin listesiyle başlayın ve hassas veya PII verisi içerecek olan öznitelikleri tanımlayabilir ve eşitlenemez. Ardından [Azure AD uygulaması ve öznitelik filtreleme](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering)kullanarak yükleme sırasında bu özniteliklerin seçimini kaldırın.

> [!WARNING]
> Özniteliklerin seçimini kaldırdığınızda, dikkatli olun ve yalnızca bu özniteliklerin eşitlenmesi kesinlikle gerekmez. Diğer özniteliklerin seçimini kaldırmak, özelliklerde olumsuz bir etkiye sahip olabilir.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Öznitelik adı | Kullanıcı | Açıklama |
| --- |:---:| --- |
| accountEnabled |X |Bir hesabın etkinleştirilip etkinleştirilmediğini tanımlar. |
| , |X | |
| displayName |X | |
| objectSID |X |mekanik özellik. Azure AD ve AD arasında eşitlemeyi sürdürmek için kullanılan AD Kullanıcı tanımlayıcısı. |
| pwdLastSet |X |mekanik özellik. Zaten verilen belirteçleri geçersiz kılabileceğinizi bildirmek için kullanılır. Hem Parola karması eşitlemesi, hem de doğrudan kimlik doğrulama ve Federasyon tarafından kullanılır. |
|Hesap|X| |
| sourceAnchor |X |mekanik özellik. EKLER ve Azure AD arasındaki ilişkiyi sürdürmek için sabit tanımlayıcı. |
| usageLocation |X |mekanik özellik. Kullanıcının ülkesi/bölgesi. Lisans ataması için kullanılır. |
| userPrincipalName |X |UPN, kullanıcının oturum açma KIMLIĞIDIR. Genellikle [mail] değeri ile aynıdır. |

## <a name="exchange-online"></a>Exchange Online
| Öznitelik adı | Kullanıcı | İletişim | Grup | Açıklama |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Bir hesabın etkinleştirilip etkinleştirilmediğini tanımlar. |
| yardımc |X |X | | |
| altRecipient |X | | |Azure AD Connect Build 1.1.552.0 veya After gerektirir. |
| Authorıg |X |X |X | |
| , |X |X | | |
| , |X | |X | |
| \ |X |X | | |
| Şirketlerin |X |X | | |
| countryCode |X |X | | |
| bölüme |X |X | | |
| açıklama | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimileTelephoneNumber 'dir |X |X | | |
| givenName |X |X | | |
| homePhone 'dur |X |X | | |
| bilgisine |X |X |X |Bu öznitelik şu anda gruplar için tüketilmez. |
| Harf |X |X | | |
| girişindeki |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| Manager |X |X | | |
| üyesidir | | |X | |
| Mo |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| Msexchbypassaudıt |X | | | |
| msExchBypassModerationLink | | |X |Azure AD Connect sürümünde kullanılabilir 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| Msexchenabledenetlemesi |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Bu öznitelik şu anda Exchange Online tarafından tüketilmedi. |
| msExchExtensionCustomAttribute2 |X |X |X |Bu öznitelik şu anda Exchange Online tarafından tüketilmedi. |
| msExchExtensionCustomAttribute3 |X |X |X |Bu öznitelik şu anda Exchange Online tarafından tüketilmedi. |
| msExchExtensionCustomAttribute4 |X |X |X |Bu öznitelik şu anda Exchange Online tarafından tüketilmedi. |
| msExchExtensionCustomAttribute5 |X |X |X |Bu öznitelik şu anda Exchange Online tarafından tüketilmedi. |
| msExchHideFromAddressLists |X |X |X | |
| Msexchimmutableıd |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| Msexchmoderledbylink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| Msexchsenderhintçeviriler |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-Iskuruluş | | |X | |
| objectSID |X | |X |mekanik özellik. Azure AD ve AD arasında eşitlemeyi sürdürmek için kullanılan AD Kullanıcı tanımlayıcısı. |
| Oofreplytooluşturana | | |X | |
| otherFacsimileTelephone |X |X | | |
| Diğer ev telefonu |X |X | | |
| Diğer telefon |X |X | | |
| çağrı cihazı |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |mekanik özellik. Zaten verilen belirteçleri geçersiz kılabileceğinizi bildirmek için kullanılır. Hem parola eşitleme hem de Federasyon tarafından kullanılır. |
| Reporttooluşturana | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |mekanik özellik. EKLER ve Azure AD arasındaki ilişkiyi sürdürmek için sabit tanımlayıcı. |
| Oluşan |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| Telephoneassiste |X |X | | |
| telephoneNumber 'dır |X |X | | |
| thumbnailphoto |X |X | | |
| Başlığın |X |X | | |
| Unauthorıg |X |X |X | |
| usageLocation |X | | |mekanik özellik. Kullanıcının ülkesi/bölgesi. Lisans ataması için kullanılır. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN, kullanıcının oturum açma KIMLIĞIDIR. Genellikle [mail] değeri ile aynıdır. |
| Usersmmecertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Öznitelik adı | Kullanıcı | İletişim | Grup | Açıklama |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Bir hesabın etkinleştirilip etkinleştirilmediğini tanımlar. |
| Authorıg |X |X |X | |
| , |X |X | | |
| , |X | |X | |
| \ |X |X | | |
| Şirketlerin |X |X | | |
| countryCode |X |X | | |
| bölüme |X |X | | |
| açıklama |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimileTelephoneNumber 'dir |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homePhone 'dur |X |X | | |
| bilgisine |X |X |X | |
| Harf |X |X | | |
| IPPhone 'dur |X |X | | |
| girişindeki |X |X | | |
| - |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| Manager |X |X | | |
| üyesidir | | |X | |
| middleName |X |X | | |
| Mo |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |mekanik özellik. Azure AD ve AD arasında eşitlemeyi sürdürmek için kullanılan AD Kullanıcı tanımlayıcısı. |
| Oofreplytooluşturana | | |X | |
| otherFacsimileTelephone |X |X | | |
| Diğer ev telefonu |X |X | | |
| Diğeriptelefonu |X |X | | |
| Diğer mobil |X |X | | |
| Diğersayfalayıcı |X |X | | |
| Diğer telefon |X |X | | |
| çağrı cihazı |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Bu öznitelik şu anda SharePoint Online tarafından tüketilmiyor. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanik özellik. Zaten verilen belirteçleri geçersiz kılabileceğinizi bildirmek için kullanılır. Hem Parola karması eşitlemesi, hem de doğrudan kimlik doğrulama ve Federasyon tarafından kullanılır. |
| Reporttooluşturana | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |mekanik özellik. EKLER ve Azure AD arasındaki ilişkiyi sürdürmek için sabit tanımlayıcı. |
| Oluşan |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| Telephoneassiste |X |X | | |
| telephoneNumber 'dır |X |X | | |
| thumbnailphoto |X |X | | |
| Başlığın |X |X | | |
| Unauthorıg |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |mekanik özellik. Kullanıcının ülkesi/bölgesi
. Lisans ataması için kullanılır. |
| userPrincipalName |X | | |UPN, kullanıcının oturum açma KIMLIĞIDIR. Genellikle [mail] değeri ile aynıdır. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Takımlar ve Skype Kurumsal Çevrimiçi
| Öznitelik adı | Kullanıcı | İletişim | Grup | Açıklama |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Bir hesabın etkinleştirilip etkinleştirilmediğini tanımlar. |
| , |X |X | | |
| , |X | |X | |
| \ |X |X | | |
| Şirketlerin |X |X | | |
| bölüme |X |X | | |
| açıklama |X |X |X | |
| displayName |X |X |X | |
| facsimileTelephoneNumber 'dir |X |X |X | |
| givenName |X |X | | |
| homePhone 'dur |X |X | | |
| IPPhone 'dur |X |X | | |
| girişindeki |X |X | | |
| - |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| Manager |X |X | | |
| üyesidir | | |X | |
| Mo |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |mekanik özellik. Azure AD ve AD arasında eşitlemeyi sürdürmek için kullanılan AD Kullanıcı tanımlayıcısı. |
| Diğer telefon |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanik özellik. Zaten verilen belirteçleri geçersiz kılabileceğinizi bildirmek için kullanılır. Hem Parola karması eşitlemesi, hem de doğrudan kimlik doğrulama ve Federasyon tarafından kullanılır. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mekanik özellik. EKLER ve Azure AD arasındaki ilişkiyi sürdürmek için sabit tanımlayıcı. |
| Oluşan |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber 'dır |X |X | | |
| thumbnailphoto |X |X | | |
| Başlığın |X |X | | |
| usageLocation |X | | |mekanik özellik. Kullanıcının ülkesi/bölgesi. Lisans ataması için kullanılır. |
| userPrincipalName |X | | |UPN, kullanıcının oturum açma KIMLIĞIDIR. Genellikle [mail] değeri ile aynıdır. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Öznitelik adı | Kullanıcı | İletişim | Grup | Açıklama |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Bir hesabın etkinleştirilip etkinleştirilmediğini tanımlar. |
| , |X | |X |Ortak ad veya diğer ad. Genellikle [mail] değerinin ön eki. |
| displayName |X |X |X |Genellikle kolay ad (ad soyadı adı) olarak gösterilen adı temsil eden bir dize. |
| - |X |X |X |tam e-posta adresi. |
| üyesidir | | |X | |
| objectSID |X | |X |mekanik özellik. Azure AD ve AD arasında eşitlemeyi sürdürmek için kullanılan AD Kullanıcı tanımlayıcısı. |
| proxyAddresses |X |X |X |mekanik özellik. Azure AD tarafından kullanılır. Kullanıcının tüm ikincil e-posta adreslerini içerir. |
| pwdLastSet |X | | |mekanik özellik. Zaten verilen belirteçleri geçersiz kılabileceğinizi bildirmek için kullanılır. |
| sourceAnchor |X |X |X |mekanik özellik. EKLER ve Azure AD arasındaki ilişkiyi sürdürmek için sabit tanımlayıcı. |
| usageLocation |X | | |mekanik özellik. Kullanıcının ülkesi/bölgesi. Lisans ataması için kullanılır. |
| userPrincipalName |X | | |Bu UPN, kullanıcının oturum açma KIMLIĞIDIR. Genellikle [mail] değeri ile aynıdır. |

## <a name="intune"></a>Intune
| Öznitelik adı | Kullanıcı | İletişim | Grup | Açıklama |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Bir hesabın etkinleştirilip etkinleştirilmediğini tanımlar. |
| , |X |X | | |
| , |X | |X | |
| açıklama |X |X |X | |
| displayName |X |X |X | |
| - |X |X |X | |
| mailNickname |X |X |X | |
| üyesidir | | |X | |
| objectSID |X | |X |mekanik özellik. Azure AD ve AD arasında eşitlemeyi sürdürmek için kullanılan AD Kullanıcı tanımlayıcısı. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanik özellik. Zaten verilen belirteçleri geçersiz kılabileceğinizi bildirmek için kullanılır. Hem Parola karması eşitlemesi, hem de doğrudan kimlik doğrulama ve Federasyon tarafından kullanılır. |
| sourceAnchor |X |X |X |mekanik özellik. EKLER ve Azure AD arasındaki ilişkiyi sürdürmek için sabit tanımlayıcı. |
| usageLocation |X | | |mekanik özellik. Kullanıcının ülkesi/bölgesi. Lisans ataması için kullanılır. |
| userPrincipalName |X | | |UPN, kullanıcının oturum açma KIMLIĞIDIR. Genellikle [mail] değeri ile aynıdır. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Öznitelik adı | Kullanıcı | İletişim | Grup | Açıklama |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Bir hesabın etkinleştirilip etkinleştirilmediğini tanımlar. |
| , |X |X | | |
| , |X | |X | |
| \ |X |X | | |
| Şirketlerin |X |X | | |
| countryCode |X |X | | |
| açıklama |X |X |X | |
| displayName |X |X |X | |
| facsimileTelephoneNumber 'dir |X |X | | |
| givenName |X |X | | |
| girişindeki |X |X | | |
| managedBy | | |X | |
| Manager |X |X | | |
| üyesidir | | |X | |
| Mo |X |X | | |
| objectSID |X | |X |mekanik özellik. Azure AD ve AD arasında eşitlemeyi sürdürmek için kullanılan AD Kullanıcı tanımlayıcısı. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |mekanik özellik. Zaten verilen belirteçleri geçersiz kılabileceğinizi bildirmek için kullanılır. Hem Parola karması eşitlemesi, hem de doğrudan kimlik doğrulama ve Federasyon tarafından kullanılır. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mekanik özellik. EKLER ve Azure AD arasındaki ilişkiyi sürdürmek için sabit tanımlayıcı. |
| Oluşan |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber 'dır |X |X | | |
| Başlığın |X |X | | |
| usageLocation |X | | |mekanik özellik. Kullanıcının ülkesi/bölgesi. Lisans ataması için kullanılır. |
| userPrincipalName |X | | |UPN, kullanıcının oturum açma KIMLIĞIDIR. Genellikle [mail] değeri ile aynıdır. |

## <a name="3rd-party-applications"></a>3\. taraf uygulamalar
Bu grup, genel bir iş yükü veya uygulama için gereken en az öznitelik olarak kullanılan bir öznitelikler kümesidir. Bu, başka bir bölümde veya Microsoft dışı bir uygulamada listelenmeyen bir iş yükü için kullanılabilir. Bu, açıkça aşağıdakiler için kullanılır:

* Yammer (yalnızca Kullanıcı tüketilecektir)
* [SharePoint gibi kaynaklar tarafından sunulan karma Işletmeler arası (B2B) Şirket içi işbirliği senaryoları](https://go.microsoft.com/fwlink/?LinkId=747036)

Bu grup, Azure AD dizini Office 365, Dynamics veya Intune 'u desteklemek için kullanılmazsa kullanılabilecek bir öznitelikler kümesidir. Küçük bir çekirdek öznitelikleri kümesine sahiptir.

| Öznitelik adı | Kullanıcı | İletişim | Grup | Açıklama |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Bir hesabın etkinleştirilip etkinleştirilmediğini tanımlar. |
| , |X | |X | |
| displayName |X |X |X | |
| Çalışan |X |  |  | |
| givenName |X |X | | |
| - |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| üyesidir | | |X | |
| objectSID |X | | |mekanik özellik. Azure AD ve AD arasında eşitlemeyi sürdürmek için kullanılan AD Kullanıcı tanımlayıcısı. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanik özellik. Zaten verilen belirteçleri geçersiz kılabileceğinizi bildirmek için kullanılır. Hem Parola karması eşitlemesi, hem de doğrudan kimlik doğrulama ve Federasyon tarafından kullanılır. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mekanik özellik. EKLER ve Azure AD arasındaki ilişkiyi sürdürmek için sabit tanımlayıcı. |
| usageLocation |X | | |mekanik özellik. Kullanıcının ülkesi/bölgesi. Lisans ataması için kullanılır. |
| userPrincipalName |X | | |UPN, kullanıcının oturum açma KIMLIĞIDIR. Genellikle [mail] değeri ile aynıdır. |

## <a name="windows-10"></a>Windows 10
Windows 10 etki alanına katılmış bir bilgisayar (cihaz) bazı öznitelikleri Azure AD 'ye eşitler. Senaryolar hakkında daha fazla bilgi için bkz. [Windows 10 deneyimleri için etki alanına katılmış cihazları Azure AD 'ye bağlama](../active-directory-azureadjoin-devices-group-policy.md). Bu öznitelikler her zaman eşitlenir ve Windows 10, bir uygulama olarak görünmez. Windows 10 etki alanına katılmış bir bilgisayar, userCertificate özniteliği doldurulmuş olarak tanımlanır.

| Öznitelik adı | Cihaz | Açıklama |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Etki alanına katılmış bilgisayarlar için sabit kodlanmış değer. |
| displayName |X | |
| ms-DS-CreatorSID |X |Ayrıca, Clonteredownerreference olarak da bilinir. |
| Objectguıd 'dir |X |Ayrıca, DeviceID olarak da bilinir. |
| objectSID |X |OnPremisesSecurityIdentifier olarak da bilinir. |
| operatingSystem |X |DeviceOSType olarak da bilinir. |
| Işletimsistemisürümü |X |DeviceOSVersion olarak da bilinir. |
| userCertificate |X | |

Bu öznitelikler, seçtiğiniz diğer uygulamalara ek olarak **Kullanıcı** için de kullanılır.  

| Öznitelik adı | Kullanıcı | Açıklama |
| --- |:---:| --- |
| domainFQDN |X |Ayrıca dnadı da denir. Örneğin, contoso.com. |
| domainNetBios |X |NetBiosName de denir. Örneğin, CONTOSO. |
| msDS-KeyCredentialLink |X |Iş için Windows Hello 'ya Kullanıcı kaydolduktan sonra. | 

## <a name="exchange-hybrid-writeback"></a>Exchange karma geri yazma
**Exchange karma**'i etkinleştirmeyi seçtiğinizde, bu ÖZNITELIKLER Azure AD 'den şirket içi Active Directory geri yazılır. Exchange sürümünüze bağlı olarak, daha az öznitelik eşitlenebilir.

| Öznitelik adı (Şirket içi AD) | Öznitelik adı (Connect UI) | Kullanıcı | İletişim | Grup | Açıklama |
| --- |:---:|:---:|:---:| --- |---|
| msDS-Externaldirectoryobjectıd| ms-DS-dış-dizin-nesne kimliği |X | | |Azure AD 'de Cloudbağlayıcısını türetiliyor. Bu öznitelik, Exchange 2016 ve Windows Server 2016 AD 'de yenidir. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Çevrimiçi Arşiv: müşterilerin posta arşivlemesini sağlar. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtreleme: istemcilerden şirket içi filtreleme ve çevrimiçi güvenli ve engellenen gönderen verilerini yazar. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtreleme: istemcilerden şirket içi filtreleme ve çevrimiçi güvenli ve engellenen gönderen verilerini yazar. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtreleme: istemcilerden şirket içi filtreleme ve çevrimiçi güvenli ve engellenen gönderen verilerini yazar. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Birleşik mesajlaşmayı etkinleştirme (UM)-çevrimiçi sesli posta: Microsoft Lync Server tümleştirmesi tarafından, kullanıcının çevrimiçi hizmetler ' de sesli posta olduğunu şirket içi Lync Server 'a belirtmek için kullanılır. |
| msExchUserHoldPolicies| MS-EXC-hUserHoldPolicies |X | | |Dava tutma: bulut hizmetlerinin, hangi kullanıcıların bir bekletme tutma altında olduğunu belirlemesini sağlar. |
| proxyAddresses| proxyAddresses |X |X |X |Yalnızca Exchange Online 'dan gelen X500 adresi eklenir. |
| publicDelegates| ms-Exch-Genel-Temsilciler  |X | | |Exchange Online posta kutusuna, şirket içi Exchange posta kutusu olan kullanıcılar için Sendonbeyarı Azure AD Connect Build 1.1.552.0 veya After gerektirir. |

## <a name="exchange-mail-public-folder"></a>Exchange posta genel klasörü
**Exchange mail ortak klasörünü**etkinleştirmeyi seçtiğinizde bu öznitelikler şirket Içi ACTIVE DIRECTORY Azure AD 'ye eşitlenir.

| Öznitelik adı | PublicFolder | Açıklama |
| --- | :---:| --- |
| displayName | X |  |
| - | X |  |
| msExchRecipientTypeDetails | X |  |
| Objectguıd 'dir | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Cihaz geri yazma
Cihaz nesneleri Active Directory oluşturulur. Bu nesneler, Azure AD 'ye veya etki alanına katılmış Windows 10 bilgisayarlara katılmış cihazlar olabilir.

| Öznitelik adı | Cihaz | Açıklama |
| --- |:---:| --- |
| Altsecuritydentities |X | |
| displayName |X | |
| değeri |X | |
| msDS-Cloudbağlayıcısını |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Yalnızca Windows Server 2016 AD şeması ile |
| msDS-ısuyumlu |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-kayıt Teredowner |X | |

## <a name="notes"></a>Notlar
* Alternatif KIMLIK kullanılırken, şirket içi öznitelik userPrincipalName, onPremisesUserPrincipalName Azure AD özniteliğiyle eşitlenir. Alternatif ID özniteliği, örneğin posta, Azure AD özniteliği userPrincipalName ile eşitlenir.
* Yukarıdaki listelerde, **Kullanıcı** nesne türü **InetOrgPerson**nesne türü için de geçerlidir.

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD Connect eşitleme](how-to-connect-sync-whatis.md) yapılandırması hakkında daha fazla bilgi edinin.

[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
