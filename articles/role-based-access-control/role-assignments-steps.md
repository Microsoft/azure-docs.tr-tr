---
title: Azure rolü atama adımları-Azure RBAC
description: Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak kullanıcılara, gruplara, hizmet sorumlularına veya yönetilen kimliklere Azure rolleri atama adımlarını öğrenin.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.openlocfilehash: 40a17da6383fb1f368c74a82fefa71991cdc1b19
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517683"
---
# <a name="steps-to-assign-an-azure-role"></a>Azure rolü atama adımları

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Bu makalede, Azure rollerini [Azure Portal](role-assignments-portal.md), [Azure POWERSHELL](role-assignments-powershell.md), [Azure CLI](role-assignments-cli.md)veya [REST API](role-assignments-rest.md)kullanarak atamaya yönelik üst düzey adımlar açıklanmaktadır.

## <a name="step-1-determine-who-needs-access"></a>1. Adım: kimlerin erişime ihtiyacı olduğunu belirleme

Önce kimin erişmesi gerektiğini belirlemeniz gerekir. Bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimliğe bir rol atayabilirsiniz. Buna *güvenlik sorumlusu* da denir.

![Rol ataması için güvenlik sorumlusu](./media/shared/rbac-security-principal.png)

- Kullanıcı: Azure Active Directory'de bir profile sahip olan kişidir. Diğer kiracılardaki kullanıcılara da rol atayabilirsiniz. Diğer kuruluşlardaki kullanıcılar hakkında bilgi almak için bkz. [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md).
- Grup: Azure Active Directory'de oluşturulan kullanıcı kümesidir. Bir gruba rol atadığınızda ilgili gruptaki tüm kullanıcılar o role sahip olur. 
- Hizmet sorumlusu: Uygulamalar veya hizmetler tarafından belirli Azure kaynaklarına erişmek için kullanılan güvenlik kimliğidir. Bunu bir uygulamanın *kullanıcı kimliği* (kullanıcı adı ve parola veya sertifika) olarak düşünebilirsiniz.
- Yönetilen kimlik-Azure tarafından otomatik olarak yönetilen Azure Active Directory bir kimlik. Azure hizmetlerinde kimlik doğrulaması için kimlik bilgilerini yönetmek üzere bulut uygulamaları geliştirirken genellikle [Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) kullanırsınız.

## <a name="step-2-select-the-appropriate-role"></a>2. Adım: uygun rolü seçin

İzinler bir *rol tanımında* birlikte gruplandırılır. Genellikle bir *rol* olarak adlandırılır. Birkaç yerleşik rol listesinden seçim yapabilirsiniz. Yerleşik roller kuruluşunuzun belirli ihtiyaçlarını karşılamıyorsa kendi özel rollerinizi oluşturabilirsiniz.

![Rol ataması için rol tanımı](./media/shared/rbac-role-definition.png)

Dört temel yerleşik rol aşağıda listelenmiştir. İlk üçü tüm kaynak türleri için geçerlidir.

- [Sahip](built-in-roles.md#owner): Erişimi başkalarına verme hakkı dahil olmak üzere tüm kaynaklar üzerinde tam erişime sahiptir.
- [Katkıda Bulunan](built-in-roles.md#contributor): Her türlü Azure kaynağını oluşturup yönetebilir, ancak başkalarına erişim izni veremez.
- [Okuyucu](built-in-roles.md#reader) -mevcut Azure kaynaklarını görüntüleyebilir.
- [Kullanıcı Erişimi Yöneticisi](built-in-roles.md#user-access-administrator): Azure kaynaklarına kullanıcı erişimini yönetmenizi sağlar.

Yerleşik rollerin diğerleri belirli Azure kaynakları için yönetim özellikleri sunar. Örneğin [Sanal Makine Katılımcısı](built-in-roles.md#virtual-machine-contributor) rolü, kullanıcının sanal makine oluşturmasını ve yönetmesini sağlar.

1. [Azure yerleşik rolleri](built-in-roles.md)kapsamlı makale ile başlayın. Makalenin üst kısmındaki tablo, makalenin ilerleyen kısımlarında yer alarak ayrıntılara bir dizindir.

1. Bu makalede, izin vermek istediğiniz kaynak için hizmet kategorisine (işlem, depolama ve veritabanları gibi) gidin. Aradığınız şeyi bulmanın en kolay yolu, genellikle sayfada "blob", "sanal makine" gibi ilgili bir anahtar sözcük arar.

1. Hizmet kategorisi için listelenen rolleri gözden geçirin ve ihtiyacınız olan belirli işlemleri yapın. Yine, her zaman en kısıtlayıcı rolle başlayın.

    Örneğin, bir güvenlik sorumlusunun bir Azure depolama hesabındaki Blobları okuması, ancak yazma erişimi gerekmiyorsa, [depolama](built-in-roles.md#storage-blob-data-contributor) blobu veri katılımcısı (ve kesin yönetici düzeyindeki [Depolama Blobu veri sahibi](built-in-roles.md#storage-blob-data-owner) rolünü değil) yerine [Depolama Blobu veri okuyucu](built-in-roles.md#storage-blob-data-reader) ' yı seçin. Rol atamalarını dilediğiniz zaman daha sonra güncelleştirebilirsiniz.

1. Uygun bir rol bulamazsanız [özel bir rol](custom-roles.md)oluşturabilirsiniz.

## <a name="step-3-identify-the-needed-scope"></a>3. Adım: gerekli kapsamı tanımla

*Kapsam* , erişimin uygulandığı kaynak kümesidir. Azure 'da, dört düzeyde bir kapsam belirtebilirsiniz: [Yönetim grubu](../governance/management-groups/overview.md), abonelik, [kaynak grubu](../azure-resource-manager/management/overview.md#resource-groups)ve kaynak. Kapsamlar üst-alt ilişkisiyle yapılandırılmıştır. Her hiyerarşi düzeyi kapsamı daha belirgin hale getirir. Bu kapsam düzeylerinden herhangi birine roller atayabilirsiniz. Seçtiğiniz düzey rolün ne kadar büyük ölçüde uygulanacağını belirler. Alt düzeyler, daha yüksek düzeylerdeki rol izinlerini miras alır. 

![Rol ataması kapsamı](./media/shared/rbac-scope.png)

Bir üst kapsamda bir rol atadığınızda, bu izinler alt kapsamlarına devralınır. Örnek:

- [Okuyucu](built-in-roles.md#reader) rolünü yönetim grubu kapsamındaki bir kullanıcıya atarsanız, bu kullanıcı yönetim grubundaki tüm aboneliklerdeki her şeyi okuyabilir.
- [Faturalandırma okuyucusu](built-in-roles.md#billing-reader) rolünü abonelik kapsamındaki bir gruba atarsanız, o grubun üyeleri abonelikteki her kaynak grubu ve kaynak için faturalandırma verilerini okuyabilir.
- [Katkıda bulunan](built-in-roles.md#contributor) rolünü bir uygulamaya kaynak grubu kapsamında atadığınızda ilgili grup bu kaynak grubundaki her türlü kaynağı yönetebilir ancak abonelikteki diğer kaynak gruplarını yönetemez.

 Daha fazla bilgi için bkz. [kapsamı anlama](scope-overview.md).

## <a name="step-4-check-your-prerequisites"></a>4. Adım: Önkoşulları denetleyin

Rolleri atamak için, rol atamaya çalıştığınız kapsamda [sahip](built-in-roles.md#owner) veya [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator) gibi, rol atamaları yazma iznine sahip bir rol atanmış bir kullanıcıyla oturum açmalısınız. Benzer şekilde, rol atamasını kaldırmak için, rol atamalarını silme iznine sahip olmanız gerekir.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Kullanıcı hesabınızın aboneliğiniz dahilinde rol atama izni yoksa, hesabınızın "Microsoft. Authorization/Roleatamaları/Write ' işlemini gerçekleştirmek için yetkilendirmeye sahip olmadığını belirten bir hata iletisi görürsünüz. Bu durumda, sizin adınıza izinleri atayabilecekleri için aboneliğinizin yöneticilerine başvurun.

Rol atamak için bir hizmet sorumlusu kullanıyorsanız, "işlemi tamamlamaya yönelik ayrıcalıklar yetersiz" hatasını alabilirsiniz. Bu hata, Azure 'un Azure Active Directory (Azure AD) ' de atanan kimliğe bakmaya çalışıyor olması ve hizmet sorumlusu varsayılan olarak Azure AD 'yi okuyamadığından kaynaklanıyor olabilir. Bu durumda, dizindeki verileri okumak için hizmet sorumlusu izinleri vermeniz gerekir. Alternatif olarak, Azure CLı kullanıyorsanız, Azure AD aramasını atlamak için atane nesne KIMLIĞINI kullanarak rol atamasını oluşturabilirsiniz. Daha fazla bilgi için bkz. [Azure RBAC sorunlarını giderme](troubleshooting.md).

## <a name="step-5-assign-role"></a>5. Adım. Rol atama

Güvenlik sorumlusunu, rolünü ve kapsamını öğrendikten sonra rolü atayabilirsiniz. Azure portal, Azure PowerShell, Azure CLı, Azure SDK 'Ları veya REST API 'Lerini kullanarak roller atayabilirsiniz. Her abonelikte en fazla **2000** rol ataması yapabilirsiniz. Bu sınır, abonelik, kaynak grubu ve kaynak kapsamlarındaki rol atamalarını içerir. Her bir yönetim grubunda en fazla **500** rol ataması yapabilirsiniz.

Rol atama hakkında ayrıntılı adımlar için aşağıdaki makalelere göz atın.

- [Azure portal kullanarak Azure rolleri atama](role-assignments-portal.md)
- [Azure PowerShell kullanarak Azure rolleri atama](role-assignments-powershell.md)
- [Azure CLı kullanarak Azure rolleri atama](role-assignments-cli.md)
- [REST API kullanarak Azure rolleri atama](role-assignments-rest.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure portal kullanarak Azure kaynaklarına Kullanıcı erişimi verme](quickstart-assign-role-user-portal.md)