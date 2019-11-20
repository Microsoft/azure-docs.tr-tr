---
title: Varsayılan kullanıcı izinleri - Azure Active Directory | Microsoft Docs
description: Azure Active Directory'de bulunan farklı kullanıcı izinleri hakkında bilgi edinin.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99ceb72930d756926bbc73508b20fbde8ea31324
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561601"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Varsayılan kullanıcı izinleri Azure Active Directory nelerdir?
Azure Active Directory'de (Azure AD) tüm kullanıcılara varsayılan olarak belirli izinler verilir. Kullanıcının erişimi, Kullanıcı türünden, [rol atamalarından](active-directory-users-assign-role-azure-portal.md)ve ayrı nesnelerin sahiplerinden oluşur. Bu makalede bu varsayılan izinler ve açıklanmakta ve üye ile konuk varsayılan değerleri karşılaştırılmaktadır. Varsayılan Kullanıcı izinleri yalnızca Azure AD 'deki Kullanıcı ayarları ' nda değiştirilebilir.

## <a name="member-and-guest-users"></a>Üyeler ve konuk kullanıcılar
Alınan varsayılan izinler kümesi, kullanıcının kiracının (Üye Kullanıcı) yerel üyesi olup olmamasına veya Kullanıcı başka bir dizinden B2B işbirliği konuğu (Konuk Kullanıcı) olarak getirilmesine bağlıdır. Konuk kullanıcıları ekleme hakkında daha fazla bilgi için bkz. [Azure AD B2B işbirliği nedir?](../b2b/what-is-b2b.md) .
* Üye kullanıcılar uygulama kaydedebilir, kendi profil fotoğraflarını ve cep telefonu numaralarını yönetebilir, kendi parolalarını değiştirebilir ve B2B konuklarını davet edebilirler. Ayrıca kullanıcılar dizindeki tüm bilgileri okuyabilir (birkaç özel durum haricinde). 
* Konuk kullanıcıların kısıtlı dizin izinleri vardır. Örneğin konuk kullanıcılar kendi profil bilgileri dışındaki kiracı bilgilerine ulaşamaz. Ancak konuk kullanıcılar Kullanıcı Asıl Adı veya nesne kimliği kullanarak diğer kullanıcılar hakkında bilgi alabilir. Konuk Kullanıcı **izinlerinin sınırlı** ayarı ne olursa olsun, Konuk Kullanıcı, Grup üyeliği de dahil olmak üzere, ait oldukları grupların özelliklerini okuyabilir. Konuk, diğer kiracı nesneleri hakkındaki bilgileri görüntüleyemez.

Konuklar için varsayılan izinler varsayılan olarak kısıtlanmıştır. Konuklar yönetici rollerine eklenerek rol kapsamındaki tüm yazma ve okuma izinlerine erişmeleri sağlanabilir. Konuklara uygulanabilen diğer bir kısıtlama da başka konukları davet etme özelliğidir. **Konuklar davet edebilir** ayarını **Hayır** olarak belirlerseniz konuklar başka konuk davet edemez. Nasıl yapılacağını öğrenmek için bkz. [B2B işbirliği için davetiye temsilcisi seçme](../b2b/delegate-invitations.md). Konuk kullanıcılara varsayılan olarak üye kullanıcılarla aynı izinleri vermek için **Konuk kullanıcıların izinleri sınırlıdır** ayarını **Hayır** olarak belirleyin. Bu ayar varsayılan olarak konuk kullanıcılara tüm üye kullanıcı izinlerini vermenin yanı sıra konuklara yönetici rollerinin verilmesini sağlar.

## <a name="compare-member-and-guest-default-permissions"></a>Üye ve konuk varsayılan izinlerini karşılaştırma

**Alan** | **Üye kullanıcı izinleri** | **Konuk kullanıcı izinleri**
------------ | --------- | ----------
Kullanıcılar ve kişiler | Kullanıcıların ve kişilerin tüm genel özelliklerini okuma<br>Konuk davet etme<br>Kendi parolasını değiştirme<br>Kendi cep telefonu numarasını yönetme<br>Kendi fotoğrafını yönetme<br>Kendi yenileme belirteçlerini geçersiz kılma | Kendi özelliklerini okuma<br>Diğer kullanıcıların ve kişilerin görünen ad, e-posta, oturum açma adı, fotoğraf, Kullanıcı asıl adı ve Kullanıcı türü özelliklerini okuyun<br>Kendi parolasını değiştirme
Gruplar | Güvenlik grubu oluşturma<br>Office 365 grubu oluşturma<br>Grupların tüm özelliklerini okuma<br>Gizli olmayan grup üyeliklerini okuma<br>Birleştirilen grup için gizli Office 365 grup üyeliklerini okuma<br>Kullanıcının sahip olduğu grupların özelliklerini, sahipliğini ve üyeliğini yönetme<br>Sahip olunan gruplara konuk ekleme<br>Dinamik üyelik ayarlarını yönetme<br>Sahip olunan grupları silme<br>Sahip olunan Office 365 gruplarını geri yükleme | Grupların tüm özelliklerini okuma<br>Gizli olmayan grup üyeliklerini okuma<br>Birleştirilen gruplar için gizli Office 365 grup üyeliklerini okuma<br>Sahip olunan grupları yönetme<br>Sahip olunan gruplara konuk ekleme (izin veriliyorsa)<br>Sahip olunan grupları silme<br>Sahip olunan Office 365 gruplarını geri yükleme<br>Üyelik dahil, ait oldukları grupların özelliklerini okuyun.
Uygulamalar | Yeni uygulama kaydetme (oluşturma)<br>Kayıtlı ve kurumsal uygulamaların özelliklerini okuma<br>Sahip olunan uygulamaların uygulama özelliklerini, atamalarını ve kimlik bilgilerini yönetme<br>Kullanıcı için uygulama parolasını oluşturma veya silme<br>Sahip olunan uygulamaları silme<br>Sahip olunan uygulamaları geri yükleme | Kayıtlı ve kurumsal uygulamaların özelliklerini okuma<br>Sahip olunan uygulamaların uygulama özelliklerini, atamalarını ve kimlik bilgilerini yönetme<br>Sahip olunan uygulamaları silme<br>Sahip olunan uygulamaları geri yükleme
Cihazlar | Cihazların tüm özelliklerini okuma<br>Sahip olunan cihazların tüm özelliklerini yönetme<br> | İzin yok<br>Sahip olunan cihazları silme<br>
Dizin | Tüm şirket bilgilerini okuma<br>Tüm etki alanlarını okuma<br>Tüm iş ortağı sözleşmelerini okuma | Görünen ad ve onaylı etki alanlarını okuma
Roller ve Kapsamlar | Tüm yönetim rollerini ve üyelikleri okuma<br>Tüm yönetim birimlerinin özelliklerini ve üyeliklerini okuma | İzin yok 
Abonelikler | Tüm abonelikleri okuma<br>Hizmet Planı Üyesini etkinleştirme | İzin yok
İlkeler | İlkelerin tüm özelliklerini okuma<br>Sahip olunan ilkelerin tüm özelliklerini yönetme | İzin yok

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Üye kullanıcıların varsayılan izinlerini kısıtlama

Üye kullanıcıların varsayılan izinleri aşağıdaki yöntemlerle kısıtlanabilir.

İzin | Ayar açıklaması
---------- | ------------
Kullanıcılar uygulamayı kaydedebilir | Bu seçeneğin Hayır olarak ayarlanması, kullanıcıların uygulama kaydı oluşturmasını engeller. Daha sonra bu özellik, uygulama geliştirici rolüne eklenerek belirli kişilere geri verilebilir.
Kullanıcıların iş veya okul hesabını LinkedIn'e bağlanmasına izin ver | Bu seçeneğin Hayır olarak ayarlanması, kullanıcıların iş veya okul hesabını LinkedIn hesabıyla bağlanmalarını engeller. Daha fazla bilgi için bkz. [LinkedIn hesap bağlantıları veri paylaşımı ve onayı](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Güvenlik grubu oluşturma olanağı | Bu seçenek Hayır olarak ayarlanırsa kullanıcılar güvenlik grubu oluşturamaz. Genel Yöneticiler ve Kullanıcı yöneticileri güvenlik grupları oluşturmaya devam edebilir. Nasıl yapılacağını öğrenmek için bkz. [Grup ayarlarını yapılandırmak için Azure Active Directory cmdlet'leri](../users-groups-roles/groups-settings-cmdlets.md).
Office 365 grubu oluşturma olanağı | Bu seçenek Hayır olarak ayarlanırsa kullanıcılar Office 365 grubu oluşturamaz. Bu seçeneğin Bazı olarak ayarlanması belirli bir kullanıcı kümesinin Office 365 grubu oluşturmasını sağlar. Genel Yöneticiler ve Kullanıcı yöneticileri, hala Office 365 grupları oluşturabiliyor. Nasıl yapılacağını öğrenmek için bkz. [Grup ayarlarını yapılandırmak için Azure Active Directory cmdlet'leri](../users-groups-roles/groups-settings-cmdlets.md).
Azure AD yönetim portalına erişimi sınırlayın | Bu seçeneğin Evet olarak ayarlanması, kullanıcıların yalnızca Azure portal üzerinden Azure Active Directory erişmesini engeller.
Diğer kullanıcıları okuma olanağı | Bu ayar yalnızca PowerShell ile kullanılabilir. Bu bayrağın $false olarak ayarlanması, yöneticilerin tüm yönetici olmayan kullanıcı bilgilerini dizinden okumasını önler. Bu bayrak Exchange Online gibi diğer Microsoft hizmetlerindeki Kullanıcı bilgilerinin okunmasına engel olmaz. Bu ayar özel koşullara yöneliktir ve bu bayrağın $false olarak ayarlanması önerilmez.

## <a name="object-ownership"></a>Nesne sahipliği

### <a name="application-registration-owner-permissions"></a>Uygulama kaydı sahibi izinleri
Uygulamayı kaydeden kullanıcılar otomatik olarak uygulamanın sahibi olarak eklenir. Bu uygulama sahipleri ad ve uygulama isteği izinleri gibi uygulama meta verilerini yönetebilir. Bu kullanıcılar ayrıca SSO yapılandırması ve kullanıcı atamaları gibi kiracıya özgü uygulama yapılandırmalarını da yönetebilir. Sahipler başka kullanıcıları sahip olarak ekleyebilir veya kaldırabilir. Genel Yöneticilerden farklı olarak sahipler yalnızca kendilerine ait uygulamaları yönetebilir.

### <a name="enterprise-application-owner-permissions"></a>Kurumsal uygulama sahibi izinleri
Bir Kullanıcı yeni bir kurumsal uygulama eklediğinde, bunlar otomatik olarak sahip olarak eklenir. Bir sahip olarak, uygulamanın SSO yapılandırması, sağlama ve Kullanıcı atamaları gibi kiracıya özgü yapılandırmasını yönetebilirler. Sahipler başka kullanıcıları sahip olarak ekleyebilir veya kaldırabilir. Küresel yöneticilerin aksine, sahipler yalnızca kendi sahip oldukları uygulamaları yönetebilir.

### <a name="group-owner-permissions"></a>Grup sahibi izinleri
Grup oluşturan kullanıcılar otomatik olarak o grubun sahibi olur. Sahip olarak, ad gibi grubun özelliklerini yönetebilir ve grup üyeliğini yönetebilir. Sahipler başka kullanıcıları sahip olarak ekleyebilir veya kaldırabilir. Küresel yöneticilerin ve Kullanıcı yöneticilerinin aksine, sahipler yalnızca sahip oldukları grupları yönetebilir. Grup sahibi atamak için bkz. [Grup sahiplerini yönetme](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Sahiplik Izinleri
Aşağıdaki tablolarda, Azure Active Directory üye kullanıcılarına ait nesneler üzerindeki belirli izinler açıklanır. Kullanıcı yalnızca kendi sahip oldukları nesnelerde bu izinlere sahiptir.

#### <a name="owned-application-registrations"></a>Sahip olan uygulama kayıtları
Kullanıcılar, sahip olan uygulama kayıtlarında aşağıdaki eylemleri gerçekleştirebilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme | Azure Active Directory'de applications.audience özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme | Azure Active Directory'de applications.authentication özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/temel/güncelleştirme | Azure Active Directory'de uygulamalar üzerindeki temel özellikleri güncelleştirin. |
| Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme | Azure Active Directory'de applications.credentials özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/Sil | Azure Active Directory'de uygulamaları silin. |
| Microsoft. Directory/uygulamalar/sahipler/güncelleştirme | Azure Active Directory'de applications.owners özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/izinler/güncelleştirme | Azure Active Directory'de applications.permissions özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Azure Active Directory'de applications.policies özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/geri yükleme | Azure Active Directory'de uygulamaları geri yükleyin. |

#### <a name="owned-enterprise-applications"></a>Sahip olunan kurumsal uygulamalar
Kullanıcılar, sahip olunan kurumsal uygulamalarda aşağıdaki eylemleri gerçekleştirebilir. Kurumsal uygulama hizmet sorumlusu, bir veya daha fazla uygulama ilkesi ve bazen hizmet sorumlusu ile aynı kiracıda bir uygulama nesnesi oluşur.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Azure Active Directory'de auditLogs üzerinde tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Directory/policies/Basic/Update | Azure Active Directory'de ilkeler üzerindeki temel özellikleri güncelleştirin. |
| Microsoft. Directory/policies/Delete | Azure Active Directory'de ilkeleri silin. |
| Microsoft. Directory/policies/Owners/Update | Azure Active Directory'de policies.owners özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Azure Active Directory'de servicePrincipals.appRoleAssignedTo özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/Approtaatamalar/güncelleştirme | Azure Active Directory'de users.appRoleAssignments özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/hedef kitle/güncelleştirme | Azure Active Directory'de servicePrincipals.audience özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme | Azure Active Directory'de servicePrincipals.authentication özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/temel/güncelleştirme | Azure Active Directory'de servicePrincipals üzerindeki temel özellikleri güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/kimlik bilgileri/güncelleştirme | Azure Active Directory'de servicePrincipals.credentials özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/silme | Azure Active Directory'de servicePrincipals özelliğini silin. |
| Microsoft. Directory/Servicesorumlularını/Owners/Update | Azure Active Directory'de servicePrincipals.owners özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/izinleri/güncelleştirme | Azure Active Directory'de servicePrincipals.permissions özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Azure Active Directory'de servicePrincipals.policies özelliğini güncelleştirin. |
| Microsoft. Directory/Signınreports/allProperties/Read | Azure Active Directory'de signInReports üzerinde tüm özellikleri (ayrıcalıklı özellikler dahil) okuyabilir. |

#### <a name="owned-devices"></a>Sahip olunan cihazlar
Kullanıcılar, sahip olduğu cihazlarda aşağıdaki eylemleri gerçekleştirebilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Azure Active Directory içindeki Devices. bitLockerRecoveryKeys özelliğini okuyun. |
| Microsoft. Directory/cihazlar/devre dışı bırak | Azure Active Directory'de cihazları devre dışı bırakın. |

#### <a name="owned-groups"></a>Sahip olunan gruplar
Kullanıcılar, sahip olduğu gruplar üzerinde aşağıdaki eylemleri gerçekleştirebilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Groups/Approtaatamalar/güncelleştirme | Azure Active Directory'de groups.appRoleAssignments özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/Basic/Update | Azure Active Directory'de gruplar üzerindeki temel özellikleri güncelleştirin. |
| Microsoft. Directory/gruplar/Sil | Azure Active Directory'de grupları silin. |
| Microsoft. Directory/Groups/dynamicMembershipRule/Update | Azure Active Directory'de groups.dynamicMembershipRule özelliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Azure Active Directory'de groups.members özelliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Owners/Update | Azure Active Directory'de groups.owners özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/restore | Azure Active Directory'de grupları geri yükleyin. |
| Microsoft. Directory/Groups/Settings/Update | Azure Active Directory'de groups.owners özelliğini güncelleştirin. |

## <a name="next-steps"></a>Sonraki adımlar

* Azure AD yönetici rollerini atama hakkında daha fazla bilgi için bkz: [bir kullanıcı Azure Active Directory'de yönetici rolleri atama](active-directory-users-assign-role-azure-portal.md)
* Microsoft Azure'da kaynak erişiminin nasıl denetlendiği konusunda daha fazla bilgi için bkz. [Azure'da kaynak erişimini anlama](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure Active Directory ile Azure aboneliğinizin arasındaki ilişki hakkında bilgi için bkz. [Azure aboneliklerinin Azure Active Directory ile ilişkisi](active-directory-how-subscriptions-associated-directory.md)
* [Kullanıcıları yönetme](add-users-azure-active-directory.md)
