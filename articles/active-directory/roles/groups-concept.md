---
title: Azure Active Directory | rol atamalarını yönetmek için bulut gruplarını kullanın | Microsoft Docs
description: Kimlik yönetimi temsilcisi seçme için özel Azure AD rollerini önizleyin. Azure portal, PowerShell veya Graph API Azure rol atamalarını yönetin.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1fea64305d4735c5bf1bf59b86ae5283600e622
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502207"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Azure Active Directory 'de rol atamalarını yönetmek için bulut gruplarını kullanma (Önizleme)

Azure Active Directory (Azure AD), Azure AD yerleşik rollerine bir bulut grubu atayabilmenizi sağlayan bir genel önizlemeye giriş aşamasındadır. Bu özellikle, Azure AD 'de genel ve ayrıcalıklı rol yöneticilerinizin en düşük çabayla yönetici erişimi sağlamak için grupları kullanabilirsiniz.

Bu örneği göz önünde bulundurun: contoso, kişilerin Azure AD kuruluşunda çalışanların parolalarını yönetmesi ve sıfırlaması için coğrafi olarak kişileri işe yayımlamıştır. Ayrıcalıklı bir rol yöneticisinin veya küresel yöneticinin yardım masası yönetici rolünü her kişiye ayrı ayrı atamasını sormak yerine, bir Contoso_Helpdesk_Administrators grubu oluşturabilir ve bu rolü role atayabilir. Kullanıcılar gruba katıladıklarında, rol dolaylı olarak atanır. Mevcut idare iş akışınız daha sonra yalnızca meşru kullanıcıların grubun üyesi olduğundan ve bu nedenle yardım masası yönetici rolüne atandığından emin olmak için grubun üyeliğinin onay işlemini ve denetimini ele alabilir.

## <a name="how-this-feature-works"></a>Bu özelliğin çalışması

' Isastifbletorole ' özelliği ' true ' olarak ayarlanmış yeni bir Microsoft 365 veya güvenlik grubu oluşturun. Ayrıca, bu özelliği, Azure portal bir grup oluştururken, **Azure AD rolleri gruba atanabilmesini** sağlayabilirsiniz. Her iki durumda da, kullanıcılara rolleri atarken aynı şekilde grubu bir veya daha fazla Azure AD rolüne atayabilirsiniz. Tek bir Azure AD kuruluşunda (kiracı), en fazla 250 rol atanabilir grup oluşturulabilir.

Grubun üyelerinin role erişiminin olmasını istemiyorsanız Azure AD Privileged Identity Management kullanabilirsiniz... Bir Azure AD rolünün uygun bir üyesi olarak bir grup atayın. Grubun her üyesi, grubun atandığı rol için atamasının etkinleştirilmesini sağlar. Daha sonra, kendi rol atamasını sabit bir süre için etkinleştirebilir.

> [!Note]
> Azure AD rolüne PıM aracılığıyla bir grup atayabilmek için Privileged Identity Management 'ın güncelleştirilmiş sürümünde olmanız gerekir. Azure AD kuruluşunuz Privileged Identity Management API 'den yararlandığından, PıM 'nin eski sürümünde olabilirsiniz. Lütfen pim_preview@microsoft.com kuruluşunuzu taşımak ve API 'nizi güncelleştirmek için diğer ada ulaşın. [PIM 'de Azure AD rolleri ve özellikleri](../privileged-identity-management/azure-ad-roles-features.md)hakkında daha fazla bilgi edinin.

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Bir role atamak için özel bir grup oluşturmayı zorlıyoruz

Gruba bir rol atanırsa, Grup üyeliğini yönetebilen hiçbir BT Yöneticisi de dolaylı olarak bu rolün üyeliğini yönetebilir. Örneğin, bir grup Contoso_User_Administrators Kullanıcı hesabı yöneticisi rolüne atandığını varsayın. Grup üyeliğini değiştirebilen bir Exchange Yöneticisi kendilerini Contoso_User_Administrators grubuna ekleyebilir ve bu şekilde Kullanıcı hesabı Yöneticisi olur. Gördüğünüz gibi, bir yönetici, ayrıcalıklarınızı amaçlamadığınız bir şekilde yükseltebilir.

Azure AD, gruplar için Isastifbletorole adlı yeni bir özellik kullanarak bir role atanan bir grubu korumanıza olanak sağlar. Oluşturma zamanında yalnızca ısastifbletorole özelliği ' true ' olarak ayarlanmış bulut grupları bir role atanabilir. Bu özellik sabittir; Bu özellik ' true ' olarak ayarlanmış bir Grup oluşturulduktan sonra değiştirilemez. Mevcut bir grupta özelliği ayarlayamazsınız.
Olası ihlalin oluşmasını engellemek için grupların rollere nasıl atandığını tasarlıyoruz:

- Yalnızca genel Yöneticiler ve ayrıcalıklı rol yöneticileri, rol atanabilir bir grup oluşturabilir ("Isastifbletorole" özelliği etkin).
- Azure AD dinamik grubu olamaz; diğer bir deyişle, "atandı" üyelik türüne sahip olmalıdır. Dinamik grupların otomatik popülasyonu, gruba eklenen istenmeyen bir hesaba neden olabilir ve bu nedenle role atanır.
- Varsayılan olarak, yalnızca genel Yöneticiler ve ayrıcalıklı rol yöneticileri, rol atanabilir bir grubun üyeliğini yönetebilir, ancak Grup sahipleri ekleyerek rol atanabilir grupların yönetimine temsilci atayabilirsiniz.
- Ayrıcalık yükseltmesini engellemek için, rol atanabilir bir grubun üyelerinin ve sahiplerinin kimlik bilgileri yalnızca ayrıcalıklı bir kimlik doğrulama Yöneticisi veya genel yönetici tarafından değiştirilebilir.
- İç içe geçme yok. Bir grup, rol atanabilir bir grubun üyesi olarak eklenemez.

## <a name="limitations"></a>Sınırlamalar

Şu senaryolar Şu anda desteklenmiyor:  

- Azure AD rollerine şirket içi gruplar atama (yerleşik veya özel)

## <a name="known-issues"></a>Bilinen sorunlar

- *Yalnızca Azure AD P2 lisanslı müşteriler*: bir gruba hem Azure AD hem de PRIVILEGED IDENTITY Management (PIM) aracılığıyla bir rol için etkin olarak atamayın. Özellikle, oluşturulduktan sonra rol atanabilir bir gruba rol atamayın *ve* PIM 'yi kullanarak gruba bir rol atarsınız. Bu, kullanıcıların, PıM 'de etkin rol atamalarını görmemesi ve bu PıM atamasını kaldıramamasının gerektiği sorunlara yol açacaktır. Uygun atamalar Bu senaryoda etkilenmez. Bu atamayı yapmayı denerseniz, şöyle beklenmeyen davranışlar görebilirsiniz:
  - Rol atamasının bitiş saati yanlış görüntülenebilir.
  - PıM portalında, (bir veya daha fazla grup ve doğrudan) bir atamaya göre kaç yöntemden bağımsız olarak **rollerim** yalnızca bir rol ataması gösterebilir.
- *Yalnızca Azure AD P2 lisanslı müşterileri* Grubu sildikten sonra bile, PıM Kullanıcı arabirimindeki rolün uygun bir üyesi gösterilmeye devam eder. İşlevsel bir sorun yoktur; yalnızca Azure portal bir önbellek sorunudur.  
- Grup üyeliği aracılığıyla rol atamaları için yeni [Exchange Yönetim merkezini](https://admin.exchange.microsoft.com/) kullanın. Eski Exchange Yönetim Merkezi bu özelliği henüz desteklemiyor. Exchange PowerShell cmdlet 'leri beklenen şekilde çalışır.
- Azure Information Protection Portal (klasik portal) henüz grup aracılığıyla rol üyeliğini tanımıyor. [Birleşik duyarlılık etiketleme platformuna geçiş](/azure/information-protection/configure-policy-migrate-labels) yapabilir ve sonra rolleri yönetmek için Grup atamalarını kullanmak üzere Office 365 güvenlik & Uyumluluk Merkezi ' ni kullanabilirsiniz.
- [Apps yönetim merkezi](https://config.office.com/) bu özelliği henüz desteklemiyor. Kullanıcıları doğrudan Office uygulamaları yönetici rolüne atayın.
- [M365 Uyumluluk Merkezi](https://compliance.microsoft.com/) bu özelliği henüz desteklemiyor. Bu portalı kullanmak için kullanıcıları doğrudan uygun Azure AD rollerine atayın.

Bu sorunları düzeltiyoruz.

## <a name="required-license-plan"></a>Gerekli lisans planı

Bu özelliği kullanmak için Azure AD kuruluşunuzda Azure AD Premium P1 lisansına sahip olmanız gerekir. Tek seferlik rol etkinleştirme için de Privileged Identity Management kullanmak için, kullanılabilir bir Azure AD Premium P2 lisansınızın olması gerekir. Gereksinimleriniz için doğru lisans bulmak için bkz. [ücretsiz ve Premium planların genel kullanıma sunulan özelliklerini karşılaştırma](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses).

## <a name="next-steps"></a>Sonraki adımlar

- [Rol atanabilir grup oluşturma](groups-create-eligible.md)
- [Rol atanabilir gruba rol atama](groups-assign-role.md)
