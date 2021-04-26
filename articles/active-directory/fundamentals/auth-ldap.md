---
title: Azure Active Directory ile LDAP kimlik doğrulaması
description: Azure Active Directory ile LDAP kimlik doğrulamasını elde etmeye yönelik mimari rehberlik.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f209d394e1a0c2c4ddde9cbf8df2704647e2822a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168721"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>Azure Active Directory ile LDAP kimlik doğrulaması

Hafif Dizin Erişim Protokolü (LDAP), çeşitli dizin hizmetleriyle çalışmaya yönelik bir uygulama protokolüdür. Active Directory, [Kullanıcı ve hesap bilgilerini depolama](https://www.dnsstuff.com/active-directory-service-accounts)gibi Dizin Hizmetleri ve parolalar gibi güvenlik bilgileri. Daha sonra hizmet, bilgilerin ağdaki diğer cihazlarla paylaşılmasını sağlar. E-posta, müşteri ilişkisi yöneticileri (CRMs) ve Insan kaynakları (HR) yazılımları gibi kurumsal uygulamalar, kimlik doğrulamak, erişmek ve bilgi bulmak için LDAP kullanabilir. 

Azure Active Directory (Azure AD) Azure AD Domain Services (AD DS) üzerinden bu kalıbı destekler. Bu, şirket içi LDAP kaynaklarını buluta taşıyarak, bulut öncelikli stratejisini benimseme olanağı sunan kuruluşların kendi ortamlarına modernleştirin etmesine olanak tanır. En hızlı avantajlar şunlardır: 

* Azure AD ile tümleşiktir. Kullanıcıların ve grupların eklemeleri veya nesnelerine yönelik öznitelik değişiklikleri, Azure AD kiracınızdan AD DS olarak otomatik olarak eşitlenir. Şirket içi Active Directory nesnelerdeki değişiklikler Azure AD ile eşitlenir ve ardından AD DS.

* İşlemleri kolaylaştırın. Şirket içi altyapıları el ile saklama ve düzeltme eki uygulama gereksinimini azaltır. 

* Güvenilir. Yönetilen, yüksek oranda kullanılabilir hizmetler edinirsiniz 

## <a name="use-when"></a>Şu durumlarda kullanın

Bir uygulamanın veya hizmetin LDAP kimlik doğrulamasını kullanması gerekir.

![Mimari diyagramı](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>Sistem bileşenleri

* **Kullanıcı**: bir tarayıcı aracılığıyla LDAP bağımlı uygulamalara erişir.

* **Web tarayıcısı**: uygulamanın dış URL 'sine erişmek için kullanıcının etkileşimde bulunduğu arabirim.

* **Sanal ağ**: Azure 'da, eskı uygulamanın LDAP hizmetlerini kullanabileceği bir özel ağ. 

* **Eski uygulamalar**: Azure 'da bir sanal ağda ya da ağ yönlendirmeleri aracılığıyla örnek ıp 'leri AD DS görünürlüğe sahip LDAP gerektiren uygulamalar veya sunucu iş yükleri. 

* **Azure AD**: kimlik bilgilerini kuruluşun şirket içi dizininden Azure AD Connect aracılığıyla eşitler.

* **Azure AD Domain Services (AD DS)**: merkezi bir grup kullanıcıya, gruba ve kimlik bilgilerine erişim sağlamak IÇIN Azure AD 'den tek yönlü bir eşitleme gerçekleştirir. AD DS örneği bir sanal ağa atanır. Azure 'daki AD DS atanan sanal ağa bağlanan uygulamalar, hizmetler ve VM 'Ler LDAP, etki alanına ekleme, Grup ilkesi, Kerberos ve NTLM kimlik doğrulaması gibi yaygın AD DS özellikleri kullanabilir.
   > [!NOTE]
   >  Kuruluşun parola karmalarını eşitleyebileceği veya akıllı kartlar kullanan kullanıcıların oturum açması gereken ortamlarda, AD DS bir kaynak ormanı kullanmanızı öneririz. 

* **Azure AD Connect**: şirket kimliği bilgilerini Microsoft Azure AD ile eşitlemeye yönelik bir araç. Dağıtım Sihirbazı ve kılavuzlu deneyimler, bağlantı için gerekli önkoşulları ve bileşenleri yapılandırmanıza yardımcı olur. Active Directory Azure AD 'ye eşitleme ve oturum açma de dahil olmak üzere. 

* **Active Directory**: [Kullanıcı ve hesap bilgileri gibi şirket içi kimlik bilgilerini](https://www.dnsstuff.com/active-directory-service-accounts)ve parolalar gibi güvenlik bilgilerini depolayan dizin hizmeti.

## <a name="implement-ldap-authentication-with-azure-ad"></a>Azure AD ile LDAP kimlik doğrulamasını uygulama

* [Azure AD DS örneği oluşturma ve yapılandırma](../../active-directory-domain-services/tutorial-create-instance.md) 

* [Azure AD DS örneği için sanal ağ yapılandırma](../../active-directory-domain-services/tutorial-configure-networking.md) 

* [Azure AD DS yönetilen etki alanı için Güvenli LDAP yapılandırma](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

* [Azure AD DS şirket içi etki alanına giden bir orman güveni oluşturma](../../active-directory-domain-services/tutorial-create-forest-trust.md)

