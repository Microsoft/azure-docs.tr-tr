---
title: Yönetim birimleri sorun giderme ve SSS-Azure Active Directory | Microsoft Docs
description: Azure Active Directory, kısıtlanmış kapsama sahip izinler vermek için yönetim birimlerini araştırın.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1f41dca3b52ce75ba2342506f621cca0618a3bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565895"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD yönetim birimleri: sorun giderme ve SSS

Azure Active Directory (Azure AD) ' da daha ayrıntılı yönetim denetimi için, bir veya daha fazla yönetim birimiyle sınırlı bir kapsama sahip bir Azure AD rolüne kullanıcı atayabilirsiniz. Ortak görevlere yönelik örnek PowerShell betikleri için bkz. [çalışma, yönetim birimleriyle çalışma](/powershell/azure/active-directory/working-with-administrative-units).

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S: neden yönetim birimi oluşturamıyorum?**

Y **:** Yalnızca bir *genel yönetici* veya *ayrıcalıklı rol YÖNETICISI* , Azure AD 'de bir yönetim birimi oluşturabilir. Yönetim birimini oluşturmaya çalışan kullanıcının *genel yönetici* ya da *ayrıcalıklı rol yöneticisi* rolüne atandığından emin olun.

**S: bir yönetim birimine bir grup ekledim. Grup üyeleri neden hala gösterilmiyor?**

Y **:** Bir yönetim birimine bir grup eklediğinizde, bu, gruba eklenen tüm üyelerin oluşmasına neden olmaz. Kullanıcıların doğrudan yönetim birimine atanması gerekir.

**S: yönetici biriminin bir üyesini yeni ekledim (veya kaldırdım). Üye Kullanıcı arabiriminde neden gösterilmiyor (veya hala gösterilmiyor)?**

Y **:** Bazen, yönetim biriminin bir veya daha fazla üyesinin eklenmesi veya kaldırılması, **yönetim birimleri** bölmesinde yansıtılmayı birkaç dakika sürebilir. Alternatif olarak, doğrudan ilişkili kaynağın özelliklerine gidebilir ve eylemin tamamlanıp tamamlanmadığını görebilirsiniz. Yönetim birimlerindeki kullanıcılar ve gruplar hakkında daha fazla bilgi için, bkz. bir [Kullanıcı için yönetim birimlerinin listesini görüntüleme](admin-units-add-manage-users.md) ve [bir grup için yönetim birimlerinin bir listesini görüntüleme](admin-units-add-manage-groups.md).

**S: bir yönetim biriminde Temsilcili bir parola yöneticisidir. Belirli bir kullanıcının parolasını neden sıfırlayamıyorum?**

Y **:** Bir yönetim biriminin Yöneticisi olarak, yalnızca yönetim biriminizdeki kullanıcılara atanan kullanıcılar için parolaları sıfırlayabilirsiniz. Parola sıfırlama başarısız olan kullanıcının, atandığı yönetim birimine ait olduğundan emin olun. Kullanıcı aynı yönetim birimine aitse ancak hala kullanıcının parolasını sıfırlamadıysanız, kullanıcıya atanan rolleri kontrol edin. 

Ayrıcalık yükselmesine engel olmak için, yönetim birimi kapsamlı bir yönetici, kuruluş genelinde bir kapsama sahip bir role atanan kullanıcının parolasını sıfırlayabilir.

**S: yönetim birimleri neden gereklidir? Kapsam tanımlama yolu olarak güvenlik grupları kullanılamadı mu?**

Y **:** Güvenlik grupları, mevcut bir amaç ve yetkilendirme modeline sahiptir. Örneğin, bir *Kullanıcı Yöneticisi*, Azure AD kuruluşundaki tüm güvenlik gruplarının üyeliğini yönetebilir. Rol, Salesforce gibi uygulamalara erişimi yönetmek için grupları kullanabilir. Bir *Kullanıcı Yöneticisi* , yetkilendirme modelinin kendisini yönetebilmelidir, bu da güvenlik grupları "kaynak gruplandırması" senaryolarını destekleyecek şekilde genişletilmişse sonuç olur. 

Windows Server Active Directory 'daki kuruluş birimleri gibi yönetim birimlerinin, çok çeşitli dizin nesnelerinin kapsamını yönetmek için bir yol sağlaması amaçlanmıştır. Güvenlik grupları, kaynak kapsamlarının üyesi olabilir. Yöneticinin yönetebileceği güvenlik grupları kümesini tanımlamak için güvenlik gruplarının kullanılması kafa karıştırıcı hale gelebilir.

**S: yönetim birimine bir grup eklemek ne anlama geliyor?**

Y **:** Bir yönetim birimine Grup eklemek, grubun kendisini o yönetim birimi kapsamındaki herhangi bir *Kullanıcı yöneticisinin* yönetim kapsamına taşır. Yönetim birimi için Kullanıcı yöneticileri, grubun adını ve üyeliğini yönetebilir. Grup kullanıcılarını yönetmek için *Kullanıcı Yöneticisi* izinleri vermez (örneğin, parolalarını sıfırlamak için). *Kullanıcı yöneticisine* kullanıcıları yönetme olanağı vermek için kullanıcıların doğrudan yönetim biriminin üyesi olması gerekir.

**S: bir kaynak (Kullanıcı veya grup) birden fazla yönetim biriminin üyesi olabilir mi?**

Y **:** Evet, kaynak birden fazla yönetim biriminin üyesi olabilir. Kaynak, kaynak üzerinde izinlere sahip olan tüm kuruluş genelindeki ve yönetim birimi kapsamlı yöneticilerle yönetilebilir.

**S: B2C kuruluşları 'nda yönetim birimleri kullanılabilir mi?**

Y **:** Hayır, B2C kuruluşları için yönetim birimleri kullanılamaz.

**S: iç içe geçmiş yönetim birimleri destekleniyor mu?**

Y **:** Hayır, iç içe geçmiş yönetim birimleri desteklenmez.

**S: PowerShell ve Graph API desteklenen yönetim birimleri var mı?**

**Y:** Evet. [PowerShell cmdlet belgeleri](/powershell/module/Azuread/) ve [örnek betiklerdeki](/powershell/azure/active-directory/working-with-administrative-units)yönetim birimleri için destek bulacaksınız.

Microsoft Graph ' de [Yönetimtiveunit kaynak türü](/graph/api/resources/administrativeunit) için destek bulun.

## <a name="next-steps"></a>Sonraki adımlar

- [Yönetim birimlerini kullanarak roller için kapsamı kısıtlama](administrative-units.md)
- [Yönetim birimlerini yönetme](admin-units-manage.md)
