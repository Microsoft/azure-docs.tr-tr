---
title: Azure kaynakları için rol tabanlı erişim denetimi (RBAC) nedir? | Microsoft Docs
description: Azure kaynakları için rol tabanlı erişim denetimine (RBAC) genel bir bakış alın. Azure kaynaklarına erişimi denetlemek için rol atamalarını kullanın.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 59708be1de53ab231ccb6dc89bd56c795734fccc
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176813"
---
# <a name="what-is-role-based-access-control-rbac-for-azure-resources"></a>Azure kaynakları için rol tabanlı erişim denetimi (RBAC) nedir?

Bulut kaynakları için erişim yönetimi, bulutu kullanan herhangi bir kuruluş için kritik bir işlevdir. Rol tabanlı erişim denetimi (RBAC), Azure kaynaklarına kimlerin erişebileceğini, bu kaynaklarla neler yapabileceğini ve hangi alanların erişebileceğini yönetmenize yardımcı olur.

RBAC, Azure kaynakları üzerinde ayrıntılı erişim yönetimi sağlayan [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) yerleşik bir yetkilendirme sistemidir.

## <a name="what-can-i-do-with-rbac"></a>RBAC ile ne yapabilirim?

RBAC ile neler yapabileceğinize ilişkin bazı örnekler aşağıda verilmiştir:

- Bir kullanıcının bir abonelikteki sanal makineleri yönetmesine ve sanal ağları yönetmek için başka bir kullanıcıya izin ver
- Bir DBA grubunun bir abonelikte SQL veritabanlarını yönetmesine izin ver
- Bir kullanıcının, bir kaynak grubundaki sanal makineler, Web siteleri ve alt ağlar gibi tüm kaynakları yönetmesine izin verin
- Bir uygulamanın bir kaynak grubundaki tüm kaynaklara erişmesine izin ver

## <a name="best-practice-for-using-rbac"></a>RBAC kullanımı için en iyi uygulama

RBAC kullanarak, ekip dahilinde görevleri ayırabilirsiniz ve yalnızca işlerini gerçekleştirmesi için ihtiyaç duydukları kullanıcılara erişim miktarını verebilirsiniz. Azure aboneliğinizde veya kaynaklarında herkes için sınırsız izin vermek yerine, belirli bir kapsamda yalnızca belirli eylemlere izin verebilirsiniz.

Erişim denetimi stratejinizi planlarken, kullanıcılara işlerini yapmak için en az ayrıcalığa izin vermek en iyi uygulamadır. Aşağıdaki diyagramda RBAC kullanımı için önerilen bir model gösterilmektedir.

![RBAC ve en az ayrıcalık](./media/overview/rbac-least-privilege.png)

## <a name="how-rbac-works"></a>RBAC nasıl kullanılır?

RBAC kullanarak kaynaklara erişimi denetleme yönteminiz, rol atamaları oluşturmaktır. Bu, anlaşılması için önemli bir kavramdır. Bu, izinlerin nasıl zorlandığını öğrenin. Rol ataması üç öğeden oluşur: güvenlik sorumlusu, rol tanımı ve kapsam.

### <a name="security-principal"></a>Güvenlik sorumlusu

*Güvenlik sorumlusu* , Azure kaynaklarına erişim isteyen bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimliği temsil eden bir nesnedir.

![Rol ataması için güvenlik sorumlusu](./media/overview/rbac-security-principal.png)

- Kullanıcı-Azure Active Directory bir profili olan kişi. Ayrıca, diğer kiracılardaki kullanıcılara roller de atayabilirsiniz. Diğer kuruluşlardaki kullanıcılar hakkında daha fazla bilgi için bkz. [Azure ACTIVE DIRECTORY B2B](../active-directory/b2b/what-is-b2b.md).
- Grup-Azure Active Directory içinde oluşturulan bir kullanıcı kümesi. Bir gruba bir rol atadığınızda, o gruptaki tüm kullanıcıların bu rolü vardır. 
- Hizmet sorumlusu-belirli Azure kaynaklarına erişmek için uygulamalar veya hizmetler tarafından kullanılan bir güvenlik kimliği. Bunu, bir uygulama için *Kullanıcı kimliği* (Kullanıcı adı ve parola veya sertifika) olarak düşünebilirsiniz.
- Yönetilen kimlik-Azure tarafından otomatik olarak yönetilen Azure Active Directory bir kimlik. Azure hizmetlerinde kimlik doğrulaması için kimlik bilgilerini yönetmek üzere bulut uygulamaları geliştirirken genellikle [Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) kullanırsınız.

### <a name="role-definition"></a>Rol tanımı

*Rol tanımı* , izin koleksiyonudur. Bazen yalnızca bir *rol*olarak adlandırılır. Bir rol tanımı, gerçekleştirilebilecek işlemleri (okuma, yazma ve silme gibi) listeler. Roller, sahip veya belirli bir sanal makine okuyucusu gibi yüksek düzeyde olabilir.

![Rol ataması için rol tanımı](./media/overview/rbac-role-definition.png)

Azure, kullanabileceğiniz çeşitli [yerleşik roller](built-in-roles.md) içerir. Aşağıda dört temel yerleşik rol listelenmektedir. İlk üç tüm kaynak türleri için geçerlidir.

- [Sahip](built-in-roles.md#owner) -başkalarına erişim yetkisi verme hakkı dahil tüm kaynaklara tam erişim sağlar.
- [Katkıda bulunan](built-in-roles.md#contributor) -tüm Azure Kaynak türlerini oluşturabilir ve yönetebilir, ancak diğer kullanıcılara erişim izni veremez.
- [Okuyucu](built-in-roles.md#reader) -mevcut Azure kaynaklarını görüntüleyebilir.
- [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator) -Azure kaynaklarına Kullanıcı erişimini yönetmenizi sağlar.

Yerleşik rollerin geri kalanı belirli Azure kaynaklarının yönetimine izin verir. Örneğin, [sanal makine katılımcısı](built-in-roles.md#virtual-machine-contributor) rolü bir kullanıcının sanal makineler oluşturmasına ve yönetmesine izin verir. Yerleşik roller kuruluşunuzun belirli ihtiyaçlarını karşılamıyorsa, [Azure kaynakları için kendi özel rollerinizi](custom-roles.md)de oluşturabilirsiniz.

Azure 'da bir nesne içindeki verilere erişim izni vermenizi sağlayan veri işlemleri vardır. Örneğin, bir Kullanıcı bir depolama hesabına veri okuma erişimi içeriyorsa, bu depolama hesabındaki blob 'ları veya iletileri okuyabilir. Daha fazla bilgi için bkz. [Azure kaynakları için rol tanımlarını anlama](role-definitions.md).

### <a name="scope"></a>Kapsam

*Kapsam* , erişimin uygulandığı kaynak kümesidir. Bir rol atadığınızda, kapsam tanımlayarak izin verilen eylemleri daha fazla sınırlayabilirsiniz. Bu, birine bir [Web sitesi katılımcısı](built-in-roles.md#website-contributor)yapmak istiyorsanız ancak yalnızca bir kaynak grubu için yararlıdır.

Azure 'da, birden çok düzeyde kapsam belirtebilirsiniz: [Yönetim grubu](../governance/management-groups/overview.md), abonelik, kaynak grubu veya kaynak. Kapsamlar üst-alt ilişkisinde yapılandırılır.

![Rol atamasının kapsamı](./media/overview/rbac-scope.png)

Bir üst kapsamda erişim izni verdiğinizde, bu izinler alt kapsamlarına devralınır. Örneğin:

- [Sahip](built-in-roles.md#owner) rolünü yönetim grubu kapsamındaki bir kullanıcıya atarsanız, bu kullanıcı yönetim grubundaki tüm aboneliklerdeki her şeyi yönetebilir.
- [Okuyucu](built-in-roles.md#reader) rolünü abonelik kapsamındaki bir gruba atarsanız, o grubun üyeleri abonelikteki her kaynak grubunu ve kaynağı görüntüleyebilir.
- [Katkıda](built-in-roles.md#contributor) bulunan rolünü kaynak grubu kapsamındaki bir uygulamaya atarsanız, bu kaynak grubundaki tüm türlerin kaynaklarını yönetebilir, ancak abonelikte diğer kaynak gruplarını yönetebilir.

### <a name="role-assignments"></a>Rol atamaları

*Rol ataması* , erişim vermek amacıyla belirli bir kapsamdaki Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimliğe bir rol tanımı ekleme işlemidir. Rol ataması oluşturularak erişim verilir ve bir rol ataması kaldırılarak erişim iptal edilir.

Aşağıdaki diyagramda bir rol atamasının örneği gösterilmektedir. Bu örnekte, pazarlama grubuna, ilaç-Sales kaynak grubu için [katkıda bulunan](built-in-roles.md#contributor) rolü atanmıştır. Bu, pazarlama grubundaki kullanıcıların ilaç-Sales kaynak grubunda herhangi bir Azure kaynağı oluşturup yönetebileceği anlamına gelir. Pazarlama kullanıcıları, başka bir rol atamasının parçası olmadıkları takdirde ilaç-Sales kaynak grubu dışındaki kaynaklara erişemez.

![Erişimi denetlemek için rol ataması](./media/overview/rbac-overview.png)

Azure portal, Azure CLı, Azure PowerShell, Azure SDK 'Ları veya REST API 'Lerini kullanarak rol atamaları oluşturabilirsiniz. Her bir abonelikte en fazla **2000** rol ataması ve her bir yönetim grubunda **500** rol ataması olabilir. Rol atamaları oluşturmak ve kaldırmak için `Microsoft.Authorization/roleAssignments/*` izninizin olması gerekir. Bu izin, [sahip](built-in-roles.md#owner) veya [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator) rolleri aracılığıyla verilir.

## <a name="multiple-role-assignments"></a>Çoklu rol atamaları

Birden çok çakışan rol atamalarınız varsa ne olur? RBAC bir ek modeldir, bu nedenle, etkin izinleriniz rol atamalarınızın eklenmesiyle yapılır. Bir kullanıcıya, abonelik kapsamında katkıda bulunan rolü ve bir kaynak grubundaki okuyucu rolü verildiğinde aşağıdaki örneği göz önünde bulundurun. Katkıda bulunan izinlerinin ve okuyucu izinlerinin eklenmesi, kaynak grubu için katkıda bulunan roldür. Bu nedenle, bu durumda okuyucu rolü atamasının hiçbir etkisi yoktur.

![Çoklu rol atamaları](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Atamaları Reddet

Daha önce, RBAC reddetme olmayan bir izin verme modelidir, ancak artık RBAC atamaları sınırlı bir şekilde reddetme olanağı desteklemektedir. Bir rol atamasına benzer şekilde, *reddetme atama* , erişimi reddetmek amacıyla belirli bir kapsamdaki bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimliğe reddetme eylemleri kümesi iliştirir. Bir rol ataması *izin*verilen bir dizi eylemi tanımlar, ancak reddetme ataması *izin verilmeyen*bir dizi eylemi tanımlar. Diğer bir deyişle, atamaları Reddet, bir rol ataması erişime izin veriyorsa bile kullanıcıların belirtilen eylemleri gerçekleştirmesini engeller. Atamaları Reddet rol atamalarından önceliklidir. Daha fazla bilgi için bkz. [Azure kaynakları için reddetme atamalarını anlama](deny-assignments.md).

## <a name="how-rbac-determines-if-a-user-has-access-to-a-resource"></a>RBAC, bir kullanıcının kaynağa erişip erişemeyeceğini nasıl belirler

Aşağıda, RBAC 'in yönetim düzleminde bir kaynağa erişiminizin olup olmadığını belirlemede kullandığı üst düzey adımlar verilmiştir. Bu, bir erişim sorunuyla ilgili sorun gidermeye çalışırken anlaşılması yararlı olur.

1. Bir Kullanıcı (veya hizmet sorumlusu) Azure Resource Manager için bir belirteç alır.

    Belirteç, kullanıcının grup üyeliklerini (geçişli grup üyelikleri dahil) içerir.

1. Kullanıcı belirteç eklenmiş bir Azure Resource Manager REST API çağrısı yapar.

1. Azure Resource Manager, tüm rol atamalarını alır ve eylemin alındığı kaynak için uygulanan atamaları reddeder.

1. Azure Resource Manager, bu kullanıcı veya grubu için uygulanan rol atamalarını daraltır ve kullanıcının bu kaynak için sahip olduğu rolleri belirler.

1. Azure Resource Manager, API çağrısındaki eylemin kullanıcının bu kaynak için sahip olduğu rollere dahil edilip edilmediğini belirler.

1. Kullanıcının istenen kapsamda eyleme sahip bir rolü yoksa erişime izin verilmez. Aksi takdirde, bir reddetme atamasının geçerli olup olmadığını denetler Azure Resource Manager.

1. Bir reddetme ataması geçerliyse, erişim engellenir. Aksi takdirde erişim verilir.

## <a name="license-requirements"></a>Lisans gereksinimleri

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: Azure portal kullanarak bir kullanıcının Azure kaynaklarına erişimi görüntüleme](check-access.md)
- [RBAC ve Azure portal kullanarak Azure kaynaklarına erişimi yönetme](role-assignments-portal.md)
- [Azure 'daki farklı rolleri anlayın](rbac-and-directory-admin-roles.md)
- [Kurumsal bulut benimseme: Azure 'da kaynak erişim yönetimi](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
