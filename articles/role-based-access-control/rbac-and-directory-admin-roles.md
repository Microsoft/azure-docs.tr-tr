---
title: Klasik abonelik yönetici rolleri, Azure RBAC rolleri ve Azure AD yönetici rolleri | Microsoft Docs
description: Azure - Klasik abonelik yönetici rolleri, Azure role tabanlı erişim denetimi (RBAC) rolleri ve Azure Active Directory (Azure AD) yönetici rollerindeki farklı roller açıklanmaktadır
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/01/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: 1fe1d50f24c70c89ea0fbaa952673cdeacc4f1bf
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803155"
---
# <a name="classic-subscription-administrator-roles-azure-rbac-roles-and-azure-ad-administrator-roles"></a>Klasik abonelik yönetici rolleri, Azure RBAC rolleri ve Azure AD yönetici rolleri

Azure'da yeniyseniz farklı rolleri kavrama konusunda zorluk yaşıyor olabilirsiniz. Bu makalede aşağıdaki roller ve bunları kullanacağınız zamanlar açıklanmaktadır:
- Klasik abonelik yönetici rolleri
- Azure rol tabanlı erişim denetimi (RBAC) rolleri
- Azure Active Directory (Azure AD) yönetici rolleri

## <a name="how-the-roles-are-related"></a>Roller arasındaki ilişki

Azure'un geçmişine göz atmanız rolleri daha iyi anlamanıza yardımcı olabilir. Azure ilk kez kullanıma sunulduğunda kaynaklara erişim yalnızca üç yönetici rolüyle yönetiliyordu: Hesap Yöneticisi, Hizmet Yöneticisi ve Ortak Yönetici. Sonradan Azure kaynakları için rol tabanlı erişim denetimi (RBAC) özelliği eklendi. Azure RBAC, Azure kaynakları için daha ayrıntılı bir erişim yönetimi sunan daha yeni bir yetkilendirme sistemidir. RBAC farklı kapsamlarda atanabilen birçok yerleşik role sahiptir ve kendi özel rollerinizi oluşturmanıza imkan tanır. Azure AD'deki kullanıcılar, gruplar veya etki alanları gibi kaynakları yönetmek için birden fazla Azure AD yönetici rolü bulunur.

Aşağıdaki diyagramda klasik abonelik yönetici rolleri, Azure RBAC rolleri ve Azure AD yönetici rolleri arasındaki ilişki yüksek düzeyde gösterilmiştir.

![Azure'daki farklı roller](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>Klasik abonelik yönetici rolleri

Hesap Yöneticisi, Hizmet Yöneticisi ve Ortak Yönetici, Azure'daki üç klasik abonelik yönetici rolüdür. Klasik abonelik yöneticileri, Azure aboneliğinde tam erişime sahiptir. Bu yöneticiler Azure portal, Azure Resource Manager API'leri ve klasik dağıtım modeli API'leri aracılığıyla kaynakları yönetebilir. Azure'a kaydolmak için kullanılan hesap otomatik olarak hem Hesap Yöneticisi hem de Hizmet Yöneticisi olarak ayarlanır. Kayıt işleminin ardından ek Ortak Yöneticiler eklenebilir. Hizmet Yöneticisi ve Ortak Yöneticiler, abonelik kapsamında Sahip rolü (bir Azure RBAC rolüdür) atanmış olan kullanıcılarla eşit düzeyde erişime sahiptir. Aşağıdaki tabloda bu üç klasik abonelik yönetici rolü arasındaki farklar gösterilmiştir.

| Klasik abonelik yöneticisi | Sınır | İzinler | Notlar |
| --- | --- | --- | --- |
| Hesap Yöneticisi | Azure hesabı başına 1 | <ul><li>[Azure Hesap Merkezi](https://account.azure.com/Subscriptions)'ne erişme</li><li>Hesaptaki tüm abonelikleri yönetme</li><li>Yeni abonelik oluşturma</li><li>Abonelikleri iptal etme</li><li>Aboneliğin faturalama bilgilerini değiştirme</li><li>Hizmet Yöneticisini değiştirme</li></ul> | Kavramsal açıdan aboneliğin faturalama sahibidir.<br>Hesap Yöneticisi’nin Azure portala erişim yoktur. |
| Hizmet Yöneticisi | Azure aboneliği başına 1 | <ul><li>[Azure portal](https://portal.azure.com)'da hizmetleri yönetme</li><li>Aboneliği iptal et</li><li>Ortak Yönetici rolüne kullanıcı atama</li></ul> | Yeni bir abonelikte Hesap Yöneticisi varsayılan olarak Hizmet Yöneticisi olur.<br>Hizmet Yöneticisi, abonelik kapsamında Sahip rolü atanmış olan kullanıcıyla eşit düzeyde erişime sahiptir.<br>Hizmet Yöneticisi’nin Azure portala tam erişimi vardır. |
| Ortak Yönetici | Abonelik başına 200 | <ul><li>Hizmet Yöneticisi ile aynı erişim ayrıcalıklarına sahiptir, ancak aboneliklerin Azure dizinleriyle ilişkisini değiştiremez</li><li>Ortak Yönetici rolüne kullanıcı atayabilir ancak Hizmet Yöneticisini değiştiremez</li></ul> | Ortak Yönetici, abonelik kapsamında Sahip rolü atanmış olan kullanıcıyla eşit düzeyde erişime sahiptir. |

Azure portal, ortak yöneticileri yönetebilir veya **Klasik Yöneticiler** sekmesini kullanarak hizmet yöneticisini görüntüleyebilirsiniz.

![Azure portal Azure klasik abonelik yöneticileri](./media/rbac-and-directory-admin-roles/classic-administrators.png)

Azure portal, hizmet yöneticisini görüntüleyebilir veya değiştirebilir veya hesap yöneticisini aboneliğinizin Özellikler dikey penceresinde görüntüleyebilirsiniz.

![Azure portalda Hesap Yöneticisi ve Hizmet Yöneticisi](./media/rbac-and-directory-admin-roles/account-admin.png)

Daha fazla bilgi için bkz. [Azure klasik abonelik yöneticileri](classic-administrators.md).

### <a name="azure-account-and-azure-subscriptions"></a>Azure hesabı ve Azure abonelikleri

Azure hesabı bir faturalama ilişkisini temsil eder. Azure hesabı bir kullanıcı kimliğinden, bir veya daha fazla Azure aboneliğinden ve ilgili Azure kaynakları kümesinden oluşur. Hesabı oluşturan kişi, bu hesapta oluşturulan tüm aboneliklerin Hesap Yöneticisi olur. Bu kişi ayrıca aboneliğin varsayılan Hizmet Yöneticisi de olur.

Azure abonelikleri, Azure kaynaklarına erişimi düzenlemenize de yardımcı olur. Ayrıca kaynak kullanımının nasıl raporlandığını, faturalandırıldığını ve ödendiği denetlemenize yardımcı olur. Her abonelik farklı bir faturalandırma ve ödeme ayarına sahip olabilir, bu nedenle ofise, departmana, projeye vs. göre farklı abonelikleriniz ve farklı planlarınız olabilir. Her hizmet bir aboneliğe aittir ve programlama işlemleri için abonelik kimliği gerekebilir.

Her abonelik bir Azure AD diziniyle ilişkilendirilir. Aboneliğin ilişkilendirildiği dizini bulmak için Azure portal **abonelikler** ' i açın ve ardından dizini görmek için bir abonelik seçin.

Hesaplar ve abonelikler [Azure Hesap Merkezi](https://account.azure.com/Subscriptions)'nden yönetilir.

## <a name="azure-rbac-roles"></a>Azure RBAC rolleri

Azure RBAC, [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)'ı temel alan, işlem ve depolama gibi Azure kaynakları için ayrıntılı erişim yönetimi sağlayan bir yetkilendirme sistemidir. Azure RBAC'de 70'in üzerinde yerleşik rol bulunur. Dört temel RBAC rolü vardır. İlk üçü tüm kaynak türleri için geçerlidir:

| Azure RBAC rolü | İzinler | Notlar |
| --- | --- | --- |
| [Sahip](built-in-roles.md#owner) | <ul><li>Tüm kaynaklara tam erişim</li><li>Diğer kullanıcılara erişim yetkisi verme</li></ul> | Hizmet Yöneticisine ve Ortak Yöneticilere abonelik kapsamında Sahip rolü atanır<br>Tüm kaynak türleri için geçerlidir. |
| [Katılımcı](built-in-roles.md#contributor) | <ul><li>Her türlü Azure kaynağını oluşturma ve yönetme</li><li>Başkalarına erişim izni veremez</li></ul> | Tüm kaynak türleri için geçerlidir. |
| [Okuyucu](built-in-roles.md#reader) | <ul><li>Azure kaynaklarını görüntüleme</li></ul> | Tüm kaynak türleri için geçerlidir. |
| [Kullanıcı Erişimi Yöneticisi](built-in-roles.md#user-access-administrator) | <ul><li>Azure kaynaklarına kullanıcı erişimini yönetme</li></ul> |  |

Yerleşik rollerin diğerleri belirli Azure kaynakları için yönetim özellikleri sunar. Örneğin [Sanal Makine Katılımcısı](built-in-roles.md#virtual-machine-contributor) rolü, kullanıcının sanal makine oluşturmasını ve yönetmesini sağlar. Tüm yerleşik rollerin listesi için bkz. [Azure kaynakları Için yerleşik roller](built-in-roles.md).

RBAC yalnızca Azure portal ve Azure Resource Manager API'leri tarafından desteklenir. RBAC rolü atanmış olan kullanıcılar, gruplar ve uygulamalar [Azure klasik dağıtım modeli API'lerini](../azure-resource-manager/resource-manager-deployment-model.md) kullanamaz.

Azure portalda RBAC kullanan rol atamaları **Erişim denetimi (IAM)** dikey penceresinde görünür. Bu dikey pencere, Portal genelinde yönetim grupları, abonelikler, kaynak grupları ve çeşitli kaynaklar gibi bulunabilir.

![Azure portalda Erişim denetimi (IAM) dikey penceresi](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

**Roller** sekmesine tıkladığınızda, yerleşik ve özel rollerin listesini görürsünüz.

![Azure portalda yerleşik roller](./media/rbac-and-directory-admin-roles/roles-list.png)

Daha fazla bilgi için bkz. [RBAC kullanarak Azure kaynaklarına erişimi yönetme ve Azure Portal](role-assignments-portal.md).

## <a name="azure-ad-administrator-roles"></a>Azure AD yönetici rolleri

Azure AD yönetici rolleri kullanıcı oluşturma veya düzenleme, başkalarına yönetici rolleri atama, kullanıcı parolalarını sıfırlama, kullanıcı lisanslarını yönetme ve etki alanlarını yönetme gibi bir dizin içindeki Azure AD kaynaklarını yönetmek için kullanılır. Aşağıdaki tabloda önemli Azure AD yönetici rollerinin birkaç tanesi gösterilmiştir.

| Azure AD yönetici rolü | İzinler | Notlar |
| --- | --- | --- |
| [Genel Yönetici](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) | <ul><li>Azure Active Directory'deki tüm yönetim özelliklerine ve Azure Active Directory'yi federasyona ekleyen hizmetlere erişimi yönetme</li><li>Diğer kullanıcılara yönetici rolü atama</li><li>Tüm kullanıcıların ve diğer yöneticilerin parolasını sıfırlama</li></ul> | Azure Active Directory'ye kaydolan kullanıcı, Genel Yönetici olur. |
| [Kullanıcı Yöneticisi](../active-directory/users-groups-roles/directory-assign-admin-roles.md#user-administrator) | <ul><li>Kullanıcı ve grup oluşturma ve bunların tüm özelliklerini yönetme</li><li>Destek biletlerini yönetme</li><li>Hizmet durumunu izleme</li><li>Kullanıcıların, Yardım Masası yöneticilerinin ve Kullanıcı Yöneticilerinin parolalarını değiştirme</li></ul> |  |
| [Faturalama Yöneticisi](../active-directory/users-groups-roles/directory-assign-admin-roles.md#billing-administrator) | <ul><li>Satın alma gerçekleştirme</li><li>Abonelikleri yönetme</li><li>Destek biletlerini yönetme</li><li>Hizmet durumunu izleme</li></ul> |  |

Azure portalda Azure AD yönetici rollerinin listesini **Roller ve yöneticiler** dikey penceresinde görebilirsiniz. Tüm Azure AD yöneticisi rollerinin bir listesi için [Azure Active Directory Içindeki yönetici rolü izinleri](../active-directory/users-groups-roles/directory-assign-admin-roles.md)bölümüne bakın.

![Azure portalda Azure AD yönetici rolleri](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-rbac-roles-and-azure-ad-administrator-roles"></a>Azure RBAC rolleri ile Azure AD yönetici rolleri arasındaki farklar

Genel anlamda Azure RBAC rolleri, Azure kaynaklarını yönetme izinlerini yönetirken Azure AD yönetici rolleri, Azure Active Directory kaynaklarını yönetme izinlerini denetler. Farkların bazıları aşağıdaki tabloda karşılaştırılmıştır.

| Azure RBAC rolleri | Azure AD yönetici rolleri |
| --- | --- |
| Azure kaynaklarına erişimi yönetme | Azure Active Directory kaynaklarına erişimi yönetme |
| Özel rolleri destekler | Özel rolleri destekler |
| Birden fazla düzeyde (yönetim grubu, abonelik, kaynak grubu, kaynak) kapsam belirtilebilir | Kapsam kiracı düzeyindedir |
| Rol bilgilerine Azure portal, Azure CLI, Azure PowerShell, Azure Resource Manager şablonları, REST API'si aracılığıyla erişilebilir | Rol bilgilerine Azure yönetim portalı, Microsoft 365 Yönetim Merkezi, Microsoft Graph, AzureAD PowerShell ile erişilebilir |

### <a name="do-azure-rbac-roles-and--azure-ad-administrator-roles-overlap"></a>Azure RBAC rolleri ile Azure AD yönetici rolleri arasında çakışma var mı?

Azure RBAC rolleri ve Azure AD yönetici rolleri varsayılan olarak Azure ve Azure AD'yi kapsamaz. Ancak bir Genel Yöneticinin, Azure portalda **Genel yönetici Azure Aboneliklerini ve Yönetim Gruplarını yönetebilir**'i seçerek erişim kapsamını genişletmesi halinde Genel Yöneticiye ilgili kiracının tüm aboneliklerinde [Kullanıcı Erişimi Yöneticisi](built-in-roles.md#user-access-administrator) rolü (bir RBAC rolüdür) verilir. Kullanıcı Erişimi Yöneticisi, kullanıcının diğer kullanıcılara Azure kaynaklarına erişim izni vermesini sağlar. Bu seçenek bir aboneliğe yeniden erişim elde etmek konusunda faydalı olabilir. Daha fazla bilgi için bkz. [Azure AD yöneticisi olarak erişimi yükseltme](elevate-access-global-admin.md).

Genel Yönetici ve Kullanıcı Yöneticisi rolleri gibi Azure AD ve Microsoft Office 365'i kapsayan birden fazla Azure AD yönetici rolü vardır. Örneğin Genel Yönetici rolüne üye olduğunuzda Azure AD ve Office 365'te Microsoft Exchange ve Microsoft SharePoint'te değişiklik yapma gibi genel yönetici özelliklerine sahip olursunuz. Ancak Genel Yönetici varsayılan olarak Azure kaynaklarına erişim sahibi değildir.

![Azure RBAC ile Azure AD yönetici rollerinin karşılaştırılması](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynakları için rol tabanlı erişim denetimi (RBAC) nedir?](overview.md)
- [Azure Active Directory'deki yönetici rolü izinleri](../active-directory/users-groups-roles/directory-assign-admin-roles.md)
- [Azure klasik abonelik yöneticileri](classic-administrators.md)
