---
title: Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD rolleri
description: Azure klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure Active Directory (Azure AD) rollerinde farklı rolleri açıklar
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
ms.date: 03/31/2021
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: bd5533f4bc8420ee6d8900e8a17dfe7099d7945f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107206"
---
# <a name="classic-subscription-administrator-roles-azure-roles-and-azure-ad-roles"></a>Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD rolleri

Azure'da yeniyseniz farklı rolleri kavrama konusunda zorluk yaşıyor olabilirsiniz. Bu makalede aşağıdaki roller ve bunları kullanacağınız zamanlar açıklanmaktadır:
- Klasik abonelik yönetici rolleri
- Azure rolleri
- Azure Active Directory (Azure AD) rolleri

## <a name="how-the-roles-are-related"></a>Roller arasındaki ilişki

Azure'un geçmişine göz atmanız rolleri daha iyi anlamanıza yardımcı olabilir. Azure ilk kez kullanıma sunulduğunda kaynaklara erişim yalnızca üç yönetici rolüyle yönetiliyordu: Hesap Yöneticisi, Hizmet Yöneticisi ve Ortak Yönetici. Daha sonra, Azure rol tabanlı erişim denetimi (Azure RBAC) eklenmiştir. Azure RBAC, Azure kaynakları için daha ayrıntılı bir erişim yönetimi sunan daha yeni bir yetkilendirme sistemidir. Azure RBAC birçok yerleşik rol içerir, farklı kapsamlara atanabilir ve kendi özel rollerinizi oluşturmanıza olanak sağlar. Kullanıcılar, gruplar ve etki alanları gibi Azure AD 'de kaynakları yönetmek için birkaç Azure AD rolü vardır.

Aşağıdaki diyagramda, klasik abonelik yöneticisi rollerinin, Azure rollerinin ve Azure AD rollerinin nasıl ilişkili olduğu hakkında üst düzey bir görünüm bulunur.

![Azure'daki farklı roller](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>Klasik abonelik yönetici rolleri

Hesap Yöneticisi, Hizmet Yöneticisi ve Ortak Yönetici, Azure'daki üç klasik abonelik yönetici rolüdür. Klasik abonelik yöneticileri, Azure aboneliğinde tam erişime sahiptir. Bu yöneticiler Azure portal, Azure Resource Manager API'leri ve klasik dağıtım modeli API'leri aracılığıyla kaynakları yönetebilir. Azure'a kaydolmak için kullanılan hesap otomatik olarak hem Hesap Yöneticisi hem de Hizmet Yöneticisi olarak ayarlanır. Kayıt işleminin ardından ek Ortak Yöneticiler eklenebilir. Hizmet Yöneticisi ve Co-Administrators, abonelik kapsamında sahip rolü (bir Azure rolü) atanan kullanıcılara eşdeğer erişime sahiptir. Aşağıdaki tabloda bu üç klasik abonelik yönetici rolü arasındaki farklar gösterilmiştir.

| Klasik abonelik yöneticisi | Sınır | İzinler | Notlar |
| --- | --- | --- | --- |
| Hesap Yöneticisi | Azure hesabı başına 1 | <ul><li>[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) faturalandırma 'yi yönetme</li><li>Hesaptaki tüm abonelikleri yönetme</li><li>Yeni abonelik oluşturma</li><li>Abonelikleri iptal etme</li><li>Aboneliğin faturalama bilgilerini değiştirme</li><li>Hizmet Yöneticisini değiştirme</li></ul> | Kavramsal açıdan aboneliğin faturalama sahibidir. |
| Hizmet Yöneticisi | Azure aboneliği başına 1 | <ul><li>[Azure portal](https://portal.azure.com)'da hizmetleri yönetme</li><li>Aboneliği iptal et</li><li>Ortak Yönetici rolüne kullanıcı atama</li></ul> | Yeni bir abonelikte Hesap Yöneticisi varsayılan olarak Hizmet Yöneticisi olur.<br>Hizmet Yöneticisi, abonelik kapsamında Sahip rolü atanmış olan kullanıcıyla eşit düzeyde erişime sahiptir.<br>Hizmet Yöneticisi’nin Azure portala tam erişimi vardır. |
| Ortak Yönetici | Abonelik başına 200 | <ul><li>Hizmet Yöneticisi ile aynı erişim ayrıcalıklarına sahiptir, ancak aboneliklerin Azure dizinleriyle ilişkisini değiştiremez</li><li>Ortak Yönetici rolüne kullanıcı atayabilir ancak Hizmet Yöneticisini değiştiremez</li></ul> | Ortak Yönetici, abonelik kapsamında Sahip rolü atanmış olan kullanıcıyla eşit düzeyde erişime sahiptir. |

Azure portal, **Klasik Yöneticiler** sekmesini kullanarak Co-Administrators yönetebilir veya hizmet yöneticisini görüntüleyebilirsiniz.

![Azure portal Azure klasik abonelik yöneticileri](./media/rbac-and-directory-admin-roles/subscription-view-classic-administrators.png)

Azure portal, hizmet yöneticisini görüntüleyebilir veya değiştirebilir veya hesap yöneticisini aboneliğinizin Özellikler dikey penceresinde görüntüleyebilirsiniz.

![Azure portalda Hesap Yöneticisi ve Hizmet Yöneticisi](./media/rbac-and-directory-admin-roles/account-admin.png)

Daha fazla bilgi için bkz. [Azure klasik abonelik yöneticileri](classic-administrators.md).

### <a name="azure-account-and-azure-subscriptions"></a>Azure hesabı ve Azure abonelikleri

Azure hesabı bir faturalama ilişkisini temsil eder. Azure hesabı bir kullanıcı kimliğinden, bir veya daha fazla Azure aboneliğinden ve ilgili Azure kaynakları kümesinden oluşur. Hesabı oluşturan kişi, bu hesapta oluşturulan tüm aboneliklerin Hesap Yöneticisi olur. Bu kişi ayrıca aboneliğin varsayılan Hizmet Yöneticisi de olur.

Azure abonelikleri, Azure kaynaklarına erişimi düzenlemenize de yardımcı olur. Ayrıca kaynak kullanımının nasıl raporlandığını, faturalandırıldığını ve ödendiği denetlemenize yardımcı olur. Her abonelik farklı bir faturalandırma ve ödeme ayarına sahip olabilir, bu nedenle ofise, departmana, projeye vs. göre farklı abonelikleriniz ve farklı planlarınız olabilir. Her hizmet bir aboneliğe aittir ve programlama işlemleri için abonelik kimliği gerekebilir.

Her abonelik bir Azure AD diziniyle ilişkilendirilir. Aboneliğin ilişkilendirildiği dizini bulmak için Azure portal **abonelikler** ' i açın ve ardından dizini görmek için bir abonelik seçin.

Hesaplar ve abonelikler [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)yönetilir.

## <a name="azure-roles"></a>Azure rolleri

Azure RBAC, [Azure Resource Manager](../azure-resource-manager/management/overview.md)'ı temel alan, işlem ve depolama gibi Azure kaynakları için ayrıntılı erişim yönetimi sağlayan bir yetkilendirme sistemidir. Azure RBAC'de 70'in üzerinde yerleşik rol bulunur. Dört temel Azure rolü vardır. İlk üçü tüm kaynak türleri için geçerlidir:

| Azure rolü | İzinler | Notlar |
| --- | --- | --- |
| [Sahibi](built-in-roles.md#owner) | <ul><li>Tüm kaynaklara tam erişim</li><li>Diğer kullanıcılara erişim yetkisi verme</li></ul> | Hizmet Yöneticisine ve Ortak Yöneticilere abonelik kapsamında Sahip rolü atanır<br>Tüm kaynak türleri için geçerlidir. |
| [Katkıda Bulunan](built-in-roles.md#contributor) | <ul><li>Her türlü Azure kaynağını oluşturma ve yönetme</li><li>Azure Active Directory yeni bir kiracı oluşturun</li><li>Başkalarına erişim izni veremez</li></ul> | Tüm kaynak türleri için geçerlidir. |
| [Okuyucu](built-in-roles.md#reader) | <ul><li>Azure kaynaklarını görüntüleme</li></ul> | Tüm kaynak türleri için geçerlidir. |
| [Kullanıcı Erişimi Yöneticisi](built-in-roles.md#user-access-administrator) | <ul><li>Azure kaynaklarına kullanıcı erişimini yönetme</li></ul> |  |

Yerleşik rollerin diğerleri belirli Azure kaynakları için yönetim özellikleri sunar. Örneğin [Sanal Makine Katılımcısı](built-in-roles.md#virtual-machine-contributor) rolü, kullanıcının sanal makine oluşturmasını ve yönetmesini sağlar. Tüm yerleşik rollerin listesi için bkz. [Azure yerleşik rolleri](built-in-roles.md).

Azure RBAC 'yi yalnızca Azure portal ve Azure Resource Manager API 'Leri destekler. Azure rolleri atanmış kullanıcılar, gruplar ve uygulamalar, [Klasik Azure dağıtım modeli API 'lerini](../azure-resource-manager/management/deployment-models.md)kullanamaz.

Azure portal, Azure RBAC kullanan rol atamaları **erişim denetimi (IAM)** dikey penceresinde görünür. Bu dikey pencere, Portal genelinde yönetim grupları, abonelikler, kaynak grupları ve çeşitli kaynaklar gibi bulunabilir.

![Azure portalda Erişim denetimi (IAM) dikey penceresi](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

**Roller** sekmesine tıkladığınızda, yerleşik ve özel rollerin listesini görürsünüz.

![Azure portalda yerleşik roller](./media/rbac-and-directory-admin-roles/roles-list.png)

Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rolleri atama](role-assignments-portal.md).

## <a name="azure-ad-roles"></a>Azure AD rolleri

Azure AD rolleri, Kullanıcı oluşturma veya düzenleme, diğer kullanıcılara yönetici rolleri atama, Kullanıcı parolalarını sıfırlama, kullanıcı lisanslarını yönetme ve etki alanlarını yönetme gibi bir dizinde Azure AD kaynaklarını yönetmek için kullanılır. Aşağıdaki tabloda, daha önemli Azure AD rollerinin bazıları açıklanmaktadır.

| Azure AD rolü | İzinler | Notlar |
| --- | --- | --- |
| [Genel Yönetici](../active-directory/roles/permissions-reference.md#global-administrator) | <ul><li>Azure Active Directory'deki tüm yönetim özelliklerine ve Azure Active Directory'yi federasyona ekleyen hizmetlere erişimi yönetme</li><li>Diğer kullanıcılara yönetici rolü atama</li><li>Tüm kullanıcıların ve diğer yöneticilerin parolasını sıfırlama</li></ul> | Azure Active Directory'ye kaydolan kullanıcı, Genel Yönetici olur. |
| [Kullanıcı Yöneticisi](../active-directory/roles/permissions-reference.md#user-administrator) | <ul><li>Kullanıcı ve grup oluşturma ve bunların tüm özelliklerini yönetme</li><li>Destek biletlerini yönetme</li><li>Hizmet durumunu izleme</li><li>Kullanıcıların, Yardım Masası yöneticilerinin ve Kullanıcı Yöneticilerinin parolalarını değiştirme</li></ul> |  |
| [Faturalama yöneticisi](../active-directory/roles/permissions-reference.md#billing-administrator) | <ul><li>Satın alma gerçekleştirme</li><li>Abonelikleri yönetme</li><li>Destek biletlerini yönetme</li><li>Hizmet durumunu izleme</li></ul> |  |

Azure portal, Azure AD rollerinin listesini **Roller ve yöneticiler** dikey penceresinde görebilirsiniz. Tüm Azure AD rollerinin bir listesi için [Azure Active Directory Içindeki yönetici rolü izinleri](../active-directory/roles/permissions-reference.md)bölümüne bakın.

![Azure portal Azure AD rolleri](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-roles-and-azure-ad-roles"></a>Azure rolleri ile Azure AD rolleri arasındaki farklar

Azure rolleri, yüksek düzeyde Azure kaynakları 'nı yönetmek için izinleri kontrol ederken Azure AD rolleri Azure Active Directory kaynaklarını yönetme izinlerini denetler. Farkların bazıları aşağıdaki tabloda karşılaştırılmıştır.

| Azure rolleri | Azure AD rolleri |
| --- | --- |
| Azure kaynaklarına erişimi yönetme | Azure Active Directory kaynaklarına erişimi yönetme |
| Özel rolleri destekler | Özel rolleri destekler |
| Birden fazla düzeyde (yönetim grubu, abonelik, kaynak grubu, kaynak) kapsam belirtilebilir | [Kapsam](../active-directory/roles/custom-overview.md#scope) , kiracı düzeyinde (kuruluş genelinde), yönetim biriminde veya tek bir nesne üzerinde (örneğin, belirli bir uygulama) belirtilebilir |
| Rol bilgilerine Azure portal, Azure CLI, Azure PowerShell, Azure Resource Manager şablonları, REST API'si aracılığıyla erişilebilir | Rol bilgilerine Azure yönetim portalı, Microsoft 365 Yönetim Merkezi, Microsoft Graph, AzureAD PowerShell ile erişilebilir |

### <a name="do-azure-roles-and-azure-ad-roles-overlap"></a>Azure rolleri ve Azure AD rolleri çakışıyor mu?

Azure rolleri ve Azure AD rolleri, varsayılan olarak Azure ve Azure AD 'yi kapsamaz. Ancak, bir genel yönetici Azure portal **Azure kaynakları Için erişim yönetimini** seçerek erişimini yükseltir, genel yöneticiye belirli bir kiracının tüm aboneliklerinde [Kullanıcı erişimi yönetici](built-in-roles.md#user-access-administrator) rolü (bir Azure rolü) verilecektir. Kullanıcı Erişimi Yöneticisi, kullanıcının diğer kullanıcılara Azure kaynaklarına erişim izni vermesini sağlar. Bu seçenek bir aboneliğe yeniden erişim elde etmek konusunda faydalı olabilir. Daha fazla bilgi için bkz. [tüm Azure aboneliklerini ve Yönetim gruplarını yönetmek için erişimi yükseltme](elevate-access-global-admin.md).

Azure AD ve Microsoft 365 yayılmış genel yönetici ve Kullanıcı Yöneticisi rolleri gibi çeşitli Azure AD rolleri. Örneğin, genel yönetici rolünün bir üyesiyseniz, Microsoft Exchange ve Microsoft SharePoint üzerinde değişiklik yapma gibi Azure AD 'de ve Microsoft 365 genel yönetici özelliklerine sahip olursunuz. Ancak Genel Yönetici varsayılan olarak Azure kaynaklarına erişim sahibi değildir.

![Azure RBAC ve Azure AD rolleri](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?](overview.md)
- [Azure Active Directory'de yönetici rolü izinleri](../active-directory/roles/permissions-reference.md)
- [Azure klasik abonelik yöneticileri](classic-administrators.md)
