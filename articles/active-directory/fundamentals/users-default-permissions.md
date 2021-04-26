---
title: Varsayılan Kullanıcı izinleri-Azure Active Directory | Microsoft Docs
description: Azure Active Directory ' de kullanılabilen farklı Kullanıcı izinleri hakkında bilgi edinin.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 348f1b4e6182739b3afbc96597853a5b887877c1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748781"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Azure Active Directory varsayılan kullanıcı izinleri nelerdir?
Azure Active Directory'de (Azure AD) tüm kullanıcılara varsayılan olarak belirli izinler verilir. Kullanıcının erişimi, Kullanıcı türünden, [rol atamalarından](active-directory-users-assign-role-azure-portal.md)ve ayrı nesnelerin sahiplerinden oluşur. Bu makalede bu varsayılan izinler ve açıklanmakta ve üye ile konuk varsayılan değerleri karşılaştırılmaktadır. Varsayılan Kullanıcı izinleri yalnızca Azure AD 'deki Kullanıcı ayarları ' nda değiştirilebilir.

## <a name="member-and-guest-users"></a>Üyeler ve konuk kullanıcılar
Alınan varsayılan izinler kümesi, kullanıcının kiracının (Üye Kullanıcı) yerel üyesi olup olmamasına veya Kullanıcı başka bir dizinden B2B işbirliği konuğu (Konuk Kullanıcı) olarak getirilmesine bağlıdır. Konuk kullanıcıları ekleme hakkında daha fazla bilgi için bkz. [Azure AD B2B işbirliği nedir?](../external-identities/what-is-b2b.md) .
* Üye kullanıcılar uygulama kaydedebilir, kendi profil fotoğraflarını ve cep telefonu numaralarını yönetebilir, kendi parolalarını değiştirebilir ve B2B konuklarını davet edebilirler. Ayrıca kullanıcılar dizindeki tüm bilgileri okuyabilir (birkaç özel durum haricinde). 
* Konuk kullanıcıların kısıtlı dizin izinleri vardır. Kendi profillerini yönetebilir, kendi parolalarını değiştirebilir ve diğer kullanıcılar, gruplar ve uygulamalar hakkında bazı bilgileri alabilirler, ancak tüm dizin bilgilerini okuyamazlar. Örneğin, Konuk kullanıcılar tüm kullanıcıların, grupların ve diğer dizin nesnelerinin listesini numaralandıramaz. Konuklar yönetici rollerine eklenerek rol kapsamındaki tüm yazma ve okuma izinlerine erişmeleri sağlanabilir. Konuklar diğer konukları da davet edebilir.

## <a name="compare-member-and-guest-default-permissions"></a>Üye ve konuk varsayılan izinlerini karşılaştırma

**Alan** | **Üye kullanıcı izinleri** | **Varsayılan Konuk Kullanıcı izinleri** | **Kısıtlı Konuk Kullanıcı izinleri (Önizleme)**
------------ | --------- | ---------- | ----------
Kullanıcılar ve ilgili kişiler | <ul><li>Tüm kullanıcıların ve kişilerin listesini listeleme<li>Kullanıcıların ve kişilerin tüm genel özelliklerini okuma</li><li>Konuk davet etme<li>Kendi parolasını değiştirme<li>Kendi cep telefonu numarasını yönetme<li>Kendi fotoğrafını yönetme<li>Kendi yenileme belirteçlerini geçersiz kılma</li></ul> | <ul><li>Kendi özelliklerini okuma<li>Diğer kullanıcıların ve kişilerin görünen ad, e-posta, oturum açma adı, fotoğraf, Kullanıcı asıl adı ve Kullanıcı türü özelliklerini okuyun<li>Kendi parolasını değiştirme<li>ObjectID tarafından başka bir kullanıcı ara (izin veriliyorsa)<li>Diğer kullanıcıların yönetim ve doğrudan rapor bilgilerini okuyun</li></ul> | <ul><li>Kendi özelliklerini okuma<li>Kendi parolasını değiştirme</li><li>Kendi cep telefonu numarasını yönetme</li></ul>
Gruplar | <ul><li>Güvenlik grubu oluşturma<li>Microsoft 365 grupları oluşturma<li>Tüm grupların listesini listeleme<li>Grupların tüm özelliklerini okuma<li>Gizli olmayan grup üyeliklerini okuma<li>Birleştirilmiş grup için gizli Microsoft 365 grup üyeliklerini oku<li>Kullanıcının sahip olduğu grupların özelliklerini, sahipliğini ve üyeliğini yönetme<li>Sahip olunan gruplara konuk ekleme<li>Dinamik üyelik ayarlarını yönetme<li>Sahip olunan grupları silme<li>Sahip olunan Microsoft 365 gruplarını geri yükleme</li></ul> | <ul><li>Üyelik ve sahiplik (hatta katılmamış gruplar) dahil olmak üzere, gizli olmayan grupların özelliklerini okuyun<li>Birleştirilmiş gruplar için gizli Microsoft 365 grup üyeliklerini oku<li>Grupları görünen ada veya ObjectID 'ye göre ara (izin veriliyorsa)</li></ul> | <ul><li>Birleştirilmiş gruplar için nesne kimliğini oku<li>Bazı Microsoft 365 uygulamalarında birleştirilmiş grupların üyeliğini ve sahipliğini okuma (izin veriliyorsa)</li></ul>
Uygulamalar | <ul><li>Yeni uygulama kaydetme (oluşturma)<li>Tüm uygulamaların listesini listeleme<li>Kayıtlı ve kurumsal uygulamaların özelliklerini okuma<li>Sahip olunan uygulamaların uygulama özelliklerini, atamalarını ve kimlik bilgilerini yönetme<li>Kullanıcı için uygulama parolasını oluşturma veya silme<li>Sahip olunan uygulamaları silme<li>Sahip olunan uygulamaları geri yükleme</li></ul> | <ul><li>Kayıtlı ve kurumsal uygulamaların özelliklerini okuma</li></ul> | <ul><li>Kayıtlı ve kurumsal uygulamaların özelliklerini okuma
Cihazlar</li></ul> | <ul><li>Tüm cihazların listesini listeleme<li>Cihazların tüm özelliklerini okuma<li>Sahip olunan cihazların tüm özelliklerini yönetme</li></ul> | İzin yok | İzin yok
Dizin | <ul><li>Tüm şirket bilgilerini okuma<li>Tüm etki alanlarını okuma<li>Tüm iş ortağı sözleşmelerini okuma</li></ul> | <ul><li>Şirket görünen adını oku<li>Tüm etki alanlarını okuma</li></ul> | <ul><li>Şirket görünen adını oku<li>Tüm etki alanlarını okuma</li></ul>
Roller ve Kapsamlar | <ul><li>Tüm yönetim rollerini ve üyelikleri okuma<li>Tüm yönetim birimlerinin özelliklerini ve üyeliklerini okuma</li></ul> | İzin yok | İzin yok
Abonelikler | <ul><li>Tüm abonelikleri okuma<li>Hizmet Planı Üyesini etkinleştirme</li></ul> | İzin yok | İzin yok
İlkeler | <ul><li>İlkelerin tüm özelliklerini okuma<li>Sahip olunan ilkelerin tüm özelliklerini yönetme</li></ul> | İzin yok | İzin yok

## <a name="restrict-member-users-default-permissions"></a>Üye kullanıcıları varsayılan izinlerini kısıtla 

Üye kullanıcıları için varsayılan izinler aşağıdaki yollarla kısıtlanabilir:

İzin | Ayar açıklaması
---------- | ------------
Kullanıcılar uygulamayı kaydedebilir | Bu seçeneğin Hayır olarak ayarlanması, kullanıcıların uygulama kaydı oluşturmasını engeller. Daha sonra bu özellik, uygulama geliştirici rolüne eklenerek belirli kişilere geri verilebilir.
Kullanıcıların iş veya okul hesabını LinkedIn ile bağlanmasına izin ver | Bu seçeneğin Hayır olarak ayarlanması, kullanıcıların iş veya okul hesabını LinkedIn hesabıyla bağlanmalarını engeller. Daha fazla bilgi için bkz. [LinkedIn hesap bağlantıları veri paylaşımı ve onayı](../enterprise-users/linkedin-user-consent.md).
Güvenlik grubu oluşturma olanağı | Bu seçenek Hayır olarak ayarlanırsa kullanıcılar güvenlik grubu oluşturamaz. Genel Yöneticiler ve Kullanıcı yöneticileri güvenlik grupları oluşturmaya devam edebilir. Nasıl yapılacağını öğrenmek için bkz. [Grup ayarlarını yapılandırmak için Azure Active Directory cmdlet'leri](../enterprise-users/groups-settings-cmdlets.md).
Microsoft 365 grupları oluşturma olanağı | Bu seçeneğin Hayır olarak ayarlanması, kullanıcıların Microsoft 365 grupları oluşturmasını engeller. Bu seçeneğin bazılarına ayarlanması, bazı Kullanıcı kümesinin Microsoft 365 grupları oluşturmasına izin verir. Genel Yöneticiler ve Kullanıcı yöneticileri yine de Microsoft 365 grupları oluşturabiliyor. Nasıl yapılacağını öğrenmek için bkz. [Grup ayarlarını yapılandırmak için Azure Active Directory cmdlet'leri](../enterprise-users/groups-settings-cmdlets.md).
Azure AD yönetim portalına erişimi sınırlayın | Bu seçeneğin Hayır olarak ayarlanması, yönetici olmayanların Azure AD kaynaklarını okuyup yönetmek için Azure AD yönetim portalını kullanmasını sağlar. Evet seçeneği, yönetici olmayanların yönetim portalındaki tüm Azure AD verilerine erişmesini kısıtlar.<p>**Not**: Bu ayar, PowerShell veya Visual Studio gibi diğer Istemcileri kullanarak Azure AD verilerine erişimi kısıtlamaz. Evet olarak ayarlandığında, belirli bir yönetici olmayan kullanıcıya izin vermek Için, Azure AD yönetim portalını kullanma özelliği, Dizin Okuyucular rolü gibi herhangi bir yönetim rolü atar.<p>Bu rol, üye kullanıcıların varsayılan olarak sahip olduğu temel dizin bilgilerinin okunmasına izin verir (konuklar ve hizmet sorumluları değildir).
Diğer kullanıcıları okuma olanağı | Bu ayar yalnızca PowerShell ile kullanılabilir. Bu bayrağın $false olarak ayarlanması, yöneticilerin tüm yönetici olmayan kullanıcı bilgilerini dizinden okumasını önler. Bu bayrak Exchange Online gibi diğer Microsoft hizmetlerindeki Kullanıcı bilgilerinin okunmasına engel olmaz. Bu ayar özel koşullara yöneliktir ve bu bayrağın $false olarak ayarlanması önerilmez.

## <a name="restrict-guest-users-default-permissions"></a>Konuk kullanıcıların varsayılan izinlerini kısıtla

Konuk kullanıcılar için varsayılan izinler aşağıdaki yollarla kısıtlanabilir:

>[!NOTE]
>Ziyaretçi Kullanıcı erişimi kısıtlamaları ayarı, **Konuk kullanıcılar izinlerinin sınırlı** ayardır. Bu özelliği kullanma hakkında yönergeler için, bkz. [Azure Active Directory Konuk erişim Izinlerini kısıtlama (Önizleme)](../enterprise-users/users-restrict-guest-permissions.md).

İzin | Ayar açıklaması
---------- | ------------
Konuklar Kullanıcı erişimi kısıtlamaları (Önizleme) | Bu seçeneğin Konuk kullanıcılara ayarlanması, üye kullanıcılara varsayılan olarak tüm üye Kullanıcı izinlerini veren **aynı erişime sahip olmalıdır** .<p>Bu seçeneğin **Konuk Kullanıcı erişimi olarak ayarlanması, kendi dizin nesnelerinin özellikleri ve üyelikleri ile kısıtlıdır,** konuk erişimini varsayılan olarak yalnızca kendi Kullanıcı profiline kısıtlar. Kullanıcı asıl adına, ObjectID 'ye veya görünen ada göre arama yapıldığında bile diğer kullanıcılara erişime izin verilmez. Gruplar üyelikleri de dahil olmak üzere grupların bilgilerine erişime artık izin verilmez.<p>**Not**: Bu ayar, Microsoft ekipleri gibi bazı Microsoft 365 hizmetlerde birleştirilmiş gruplara erişimi engellemez. Daha fazla bilgi için bkz. [Microsoft ekipleri konuk erişimi](/MicrosoftTeams/guest-access) .<p>Konuk kullanıcılar, bu izin ayarlarından bağımsız olarak yönetici rollerine de eklenebilir.
Konuklar davet edebilir | Bu seçeneğin Evet olarak ayarlanması, konukların diğer konukları davet etmesine olanak tanır. Daha fazla bilgi için bkz. [B2B işbirliği Için temsilci davetleri](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Üyeler davet edebilir | Bu seçeneğin Evet olarak ayarlanması, konuklarınızın yönetici olmayan üyelerine konukları davet etmesine izin verir. Daha fazla bilgi için bkz. [B2B işbirliği Için temsilci davetleri](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Konuk davet eden rolündeki yöneticiler ve kullanıcılar davet edebilir | Bu seçeneğin Evet olarak ayarlanması, "konuk davet eden" rolündeki yöneticilerin ve kullanıcıların konukları davet etmesine izin verir. Evet olarak ayarlandığında, konuk davet eden rolündeki kullanıcılar, Üyeler ayar davet edebilmeksizin konukları davet edebilir. Daha fazla bilgi için bkz. [B2B işbirliği Için temsilci davetleri](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) .

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
| Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme | Azure Active Directory 'da Applications. Audience özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme | Azure Active Directory güncelleştirme uygulamaları. kimlik doğrulama özelliği. |
| Microsoft. Directory/uygulamalar/temel/güncelleştirme | Azure Active Directory içindeki uygulamalarda temel özellikleri güncelleştirin. |
| Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme | Azure Active Directory içindeki Applications. Credentials özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/Sil | Azure Active Directory uygulamaları silin. |
| Microsoft. Directory/uygulamalar/sahipler/güncelleştirme | Azure Active Directory içindeki Applications. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/izinler/güncelleştirme | Azure Active Directory 'da Applications. Permissions özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Azure Active Directory içindeki Applications. Policies özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/geri yükleme | Azure Active Directory içindeki uygulamaları geri yükleyin. |

#### <a name="owned-enterprise-applications"></a>Sahip olunan kurumsal uygulamalar
Kullanıcılar, sahip olunan kurumsal uygulamalarda aşağıdaki eylemleri gerçekleştirebilir. Kurumsal uygulama hizmet sorumlusu, bir veya daha fazla uygulama ilkesi ve bazen hizmet sorumlusu ile aynı kiracıda bir uygulama nesnesi oluşur.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Azure Active Directory içindeki auditLogs 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Directory/policies/Basic/Update | Azure Active Directory ilkelerde temel özellikleri güncelleştirin. |
| Microsoft. Directory/policies/Delete | Azure Active Directory ilkeleri silin. |
| Microsoft. Directory/policies/Owners/Update | Azure Active Directory içindeki policies. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Azure Active Directory Servicesorumlularını. appRoleAssignedTo özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/Approtaatamalar/güncelleştirme | Azure Active Directory Users. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/hedef kitle/güncelleştirme | Azure Active Directory Servicesorumlularını. Audience özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme | Azure Active Directory Servicesorumlularını. Authentication özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/temel/güncelleştirme | Azure Active Directory içindeki Servicesorumlularını temel özelliklerini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/kimlik bilgileri/güncelleştirme | Azure Active Directory Servicesorumlularını. Credentials özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/silme | Azure Active Directory Servicesorumlularını silin. |
| Microsoft. Directory/Servicesorumlularını/Owners/Update | Azure Active Directory Servicesorumlularını. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/izinleri/güncelleştirme | Azure Active Directory Servicesorumlularını. Permissions özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Azure Active Directory Servicesorumlularını. Policies özelliğini güncelleştirin. |
| Microsoft. Directory/Signınreports/allProperties/Read | Azure Active Directory içindeki Signınreports 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |

#### <a name="owned-devices"></a>Sahip olunan cihazlar
Kullanıcılar, sahip olduğu cihazlarda aşağıdaki eylemleri gerçekleştirebilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Azure Active Directory içindeki Devices. bitLockerRecoveryKeys özelliğini okuyun. |
| Microsoft. Directory/cihazlar/devre dışı bırak | Azure Active Directory cihazları devre dışı bırakın. |

#### <a name="owned-groups"></a>Sahip olunan gruplar
Kullanıcılar, sahip olduğu gruplar üzerinde aşağıdaki eylemleri gerçekleştirebilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Groups/Approtaatamalar/güncelleştirme | Azure Active Directory groups. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/Basic/Update | Azure Active Directory gruplardaki temel özellikleri güncelleştirin. |
| Microsoft. Directory/gruplar/Sil | Azure Active Directory grupları silin. |
| Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Azure Active Directory groups. Members özelliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Owners/Update | Azure Active Directory groups. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/restore | Azure Active Directory grupları geri yükleyin. |
| Microsoft. Directory/Groups/Settings/Update | Azure Active Directory groups. Settings özelliğini güncelleştirin. |

## <a name="next-steps"></a>Sonraki adımlar

* Guests Kullanıcı erişimi kısıtlamaları ayarı hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory Konuk erişim Izinlerini kısıtlama (Önizleme)](../enterprise-users/users-restrict-guest-permissions.md).
* Azure AD yönetici rolleri atama hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory yönetici rollerine Kullanıcı atama](active-directory-users-assign-role-azure-portal.md)
* Microsoft Azure'da kaynak erişiminin nasıl denetlendiği konusunda daha fazla bilgi için bkz. [Azure'da kaynak erişimini anlama](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure Active Directory ile Azure aboneliğinizin arasındaki ilişki hakkında bilgi için bkz. [Azure aboneliklerinin Azure Active Directory ile ilişkisi](active-directory-how-subscriptions-associated-directory.md)
* [Kullanıcıları yönetme](add-users-azure-active-directory.md)
