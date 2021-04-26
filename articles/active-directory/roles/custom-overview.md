---
title: Rol tabanlı erişim denetimine (RBAC) Azure Active Directory genel bakış
description: Azure Active Directory bir rol atamasının ve kısıtlanmış kapsamın parçalarını nasıl anlayacağınızı öğrenin.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bddb03094b73dbd6d3b8d44c76ab242caa515dc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727867"
---
# <a name="overview-of-role-based-access-control-in-azure-active-directory"></a>Azure Active Directory içindeki rol tabanlı erişim denetimine genel bakış

Bu makalede Azure Active Directory (Azure AD) rol tabanlı erişim denetiminin nasıl anlaşılması açıklanmaktadır. Azure AD rolleri, yöneticilerinize en az ayrıcalık ilkesine göre ayrıntılı izinler vermenize olanak tanır. Azure AD yerleşik ve özel rolleri [, Azure kaynakları için rol tabanlı erişim denetimi sisteminde](../../role-based-access-control/overview.md) bulacağınız olanlarla benzer kavramlar üzerinde çalışır (Azure rolleri). [Bu iki rol tabanlı erişim denetimi sistemi arasındaki fark](../../role-based-access-control/rbac-and-directory-admin-roles.md) şudur:

- Azure AD rolleri, Graph API kullanarak kullanıcılar, gruplar ve uygulamalar gibi Azure AD kaynaklarına erişimi denetler
- Azure rolleri, Azure Kaynak yönetimini kullanarak sanal makineler veya depolama gibi Azure kaynaklarına erişimi denetler

Her iki sistem de benzer şekilde kullanılan rol tanımlarını ve rol atamalarını içerir. Ancak Azure AD rol izinleri Azure özel rollerinde kullanılamaz ve tam tersi de geçerlidir.

## <a name="understand-azure-ad-role-based-access-control"></a>Azure AD rol tabanlı erişim denetimini anlama
Azure AD, rol tanımlarının 2 türünü destekler:
* [Yerleşik roller](./permissions-reference.md)
* [Özel roller](./custom-create.md)

Yerleşik roller, sabit bir izin kümesine sahip olan kutu dışı rollerdir. Bu rol tanımları değiştirilemez. Azure AD 'nin desteklediği birçok [yerleşik rol](./permissions-reference.md) vardır ve liste büyüyordur. Azure AD, kenarları yuvarlamak ve gelişmiş gereksinimlerinizi karşılamak için [özel rolleri](./custom-create.md)de destekler. Özel Azure AD rolleri kullanarak izin verilmesi, özel bir rol tanımı oluşturmayı ve ardından rol ataması kullanarak atamayı kapsayan iki adımlı bir işlemdir. Özel bir rol tanımı, önceden ayarlanmış bir listeden eklediğiniz izinlerin koleksiyonudur. Bu izinler, yerleşik rollerde kullanılan izinlerdir.  

Özel rol tanımınızı oluşturduktan sonra (veya yerleşik bir rol kullanarak) bir rol ataması oluşturarak bunu bir kullanıcıya atayabilirsiniz. Rol ataması, kullanıcıya belirtilen kapsamdaki bir rol tanımında izinleri verir. Bu iki adımlı işlem, tek bir rol tanımı oluşturmanızı ve farklı kapsamlarda birçok kez atamanızı sağlar. Kapsam, rol üyesinin erişimi olan Azure AD kaynakları kümesini tanımlar. En yaygın kapsam, kuruluş genelinde (kuruluş genelinde) kapsamındadır. Özel bir rol kuruluş genelinde bir kapsamda atanabilir, Yani rol üyesi kuruluştaki tüm kaynaklar üzerinde rol izinlerine sahiptir. Özel bir rol, bir nesne kapsamına da atanabilir. Bir nesne kapsamına bir örnek, tek bir uygulama olabilir. Aynı rol, kuruluştaki tüm uygulamalarda bir kullanıcıya ve sonra yalnızca contoso gider raporları uygulamasının kapsamına sahip başka bir kullanıcıya atanabilir.  

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Azure AD 'nin bir kullanıcının kaynağa erişip erişemeyeceğini belirleme

Aşağıda, Azure AD 'nin bir yönetim kaynağına erişiminizin olup olmadığını belirlemede kullandığı üst düzey adımlar verilmiştir. Erişim sorunlarını gidermek için bu bilgileri kullanın.

1. Bir Kullanıcı (veya hizmet sorumlusu) Microsoft Graph veya Azure AD Graph uç noktası için bir belirteç alır.
1. Kullanıcı, verilen belirteci kullanarak Microsoft Graph veya Azure AD Graph aracılığıyla Azure Active Directory (Azure AD) için bir API çağrısı yapar.
1. Durumunuza bağlı olarak, Azure AD aşağıdaki eylemlerden birini alır:
   - Kullanıcının erişim belirtecindeki [wids talebine](../../active-directory-b2c/access-tokens.md) göre Kullanıcı rolü üyeliklerini değerlendirir.
   - Kullanıcı için, doğrudan veya grup üyeliği aracılığıyla, eylemin alındığı kaynağa uygulanan tüm rol atamalarını alır.
1. Azure AD, API çağrısındaki eylemin kullanıcının bu kaynak için sahip olduğu rollere dahil edilip edilmediğini belirler.
1. Kullanıcının istenen kapsamda eyleme sahip bir rolü yoksa erişime izin verilmez. Aksi takdirde erişim izni verilir.

## <a name="role-assignment"></a>Rol ataması

Rol ataması, Azure AD kaynaklarına erişim izni vermek için belirli bir *kapsamdaki* *kullanıcıya* *rol tanımı* ekleyen bir Azure AD kaynağıdır. Erişim, rol ataması oluşturularak sağlanır ve rol ataması kaldırıldığında iptal edilir. Bir rol ataması, temel tarafında üç öğeden oluşur:

- Azure AD kullanıcısı
- Rol tanımı
- Kaynak kapsamı

Azure portal, Azure AD PowerShell veya Graph API kullanarak [rol atamaları oluşturabilirsiniz](custom-create.md) . [Rol atamalarını da listeleyebilirsiniz](view-assignments.md).

Aşağıdaki diyagramda rol ataması örneği gösterilmektedir. Bu örnekte, Chris Green, contoso pencere öğesi Oluşturucu uygulama kaydı kapsamında uygulama kayıt yöneticisi özel rolü atandı. Atama, kemal 'e uygulama kayıt yöneticisi rolünün yalnızca bu belirli uygulama kaydı için izin verir.

![Rol ataması, izinlerin nasıl zorlanacağını ve üç bölümden oluşur](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Güvenlik sorumlusu

Güvenlik sorumlusu, Azure AD kaynaklarına erişim atanacak kullanıcıyı temsil eder. Kullanıcı, Azure Active Directory bir kullanıcı profiline sahip kişidir.

### <a name="role"></a>Rol

Rol tanımı veya rol, izin koleksiyonudur. Rol tanımı, oluşturma, okuma, güncelleştirme ve silme gibi Azure AD kaynaklarında gerçekleştirilebilecek işlemleri listeler. Azure AD 'de iki tür rol vardır:

- Microsoft tarafından oluşturulan ve değiştirilemeyen yerleşik roller.
- Kuruluşunuz tarafından oluşturulan ve yönetilen özel roller.

### <a name="scope"></a>Kapsam

Kapsam, rol atamasının bir parçası olarak belirli bir Azure AD kaynağına izin verilen eylemlerin kısıtlamasıdır. Bir rol atadığınızda, yöneticinin belirli bir kaynağa erişimini sınırlayan bir kapsam belirtebilirsiniz. Örneğin, bir geliştiriciye özel bir rol vermek istiyorsanız, ancak yalnızca belirli bir uygulama kaydını yönetmek için, belirli uygulama kaydını rol atamasında kapsam olarak dahil edebilirsiniz.

## <a name="required-license-plan"></a>Gerekli lisans planı

Azure AD 'de yerleşik roller kullanmak ücretsizdir, özel roller Azure AD Premium P1 lisansı gerektirir. Gereksinimlerinize uygun lisansı bulmak için bkz. [Ücretsiz, Temel ve Premium sürümlerinin genel olarak sağlanan özelliklerini karşılaştırma](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD rollerini anlama](concept-understand-roles.md)
- [Azure Portal, Azure AD PowerShell ve Graph API](custom-create.md) kullanarak özel rol atamaları oluşturun
- [Rol atamalarını listeleme](view-assignments.md)