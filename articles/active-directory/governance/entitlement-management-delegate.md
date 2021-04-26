---
title: Yetkilendirme yönetiminde yetkilendirme ve roller-Azure AD
description: Erişim yönetimini, BT yöneticilerinden departman yöneticilerine ve proje yöneticilerine, erişimi yönetebilmeleri için nasıl atayacağınızı öğrenin.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 204d83b96e3cbe26759d678126d8826d0b2e492e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577845"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetiminde yetkilendirme ve roller

Varsayılan olarak, genel Yöneticiler ve Kullanıcı yöneticileri Azure AD yetkilendirme yönetiminin tüm yönlerini oluşturabilir ve yönetebilir. Ancak, bu rollerdeki kullanıcılar erişim paketlerinin gerekli olduğu tüm durumları bilmiyor olabilir. Genellikle, ilgili departmanlar, takımlar ya da kimin ile birlikte çalıştığının, hangi kaynakların ve ne kadar süreyle işbirliği yaptığını bilen kullanıcılar içindeki kullanıcılardır. Yönetici olmayanlar için kısıtlanmamış izinler vermek yerine kullanıcılara, işlerini yapmak için ihtiyaç duydukları en düşük izinleri verebilir ve çakışan veya uygunsuz erişim hakları oluşturmaktan kaçınabilirsiniz.

Bu videoda, BT yöneticisinden yönetici olmayan kullanıcılara erişim yönetimi atama hakkında genel bakış sunulmaktadır.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Temsilci örneği

Yetkilendirme yönetiminde erişim yönetimini nasıl temsil edebileceğinizi anlamak için, bir örneği göz önünde bulundurmasına yardımcı olur. Kuruluşunuzun aşağıdaki yönetici ve yöneticilere sahip olduğunu varsayalım.

![BT yöneticisinden yöneticilere temsilci seçme](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

BT Yöneticisi olarak, Hana her bir departmanla ilgili kişiler, örneğin, pazarlama, finans ve ali olarak, departmanın ve iş açısından kritik içeriklerinden sorumlu olan her bir departmanda kişiye aittir.

Yetkilendirme Yönetimi sayesinde, bu yönetici olmayan kullanıcılar için erişim gerektiren kullanıcıları, ne kadar süreyle, ne kadar süreyle erişmesi gerektiğini bilen kişiler olduklarından, bu yönetici olmayanlar için temsilci atayabilirsiniz. Yönetici olmayanlar için temsilci seçme, doğru kişilerin departmanlarının erişimini yönetmesini sağlar.

Bu, Hana 'nın pazarlama, finans ve hukuk departmanlarına erişim yönetimini temsilciliğini sağlamak için kullanabileceğiniz bir yoldur.

1. Hana yeni bir Azure AD güvenlik grubu oluşturur ve grubun üyesi olarak Mamta, Mark ve ali ekler.

1. Hana, bu grubu Katalog oluşturucuları rolüne ekler.

    Mamta, Mark ve ali artık departmanları için kataloglar oluşturabilir, departmanlarının ihtiyaç duyduğu kaynakları ekleyebilirler ve Katalog içinde daha fazla temsilciyi gerçekleştirebilir. Birbirlerinin kataloglarından bazıları göremez.

1. Mamta, kaynakların kapsayıcısı olan bir **Pazarlama** kataloğu oluşturur.

1. Mamta, pazarlama bölümünün sahip olduğu kaynakları bu kataloğa ekler.

1. Mamta, departmanından diğer kişileri bu katalog için Katalog sahipleri olarak ekleyebilir ve bu da katalog yönetimi sorumluluklarını paylaşmaya yardımcı olur.

1. Mamta, pazarlama kataloğundaki erişim paketlerinin oluşturulması ve yönetimi için pazarlama departmanındaki proje yöneticilerine daha fazla yetki verebilir. Bunu, erişim paketi Yöneticisi rolüne atayarak yapabilir. Erişim paketi Yöneticisi, erişim paketleri oluşturabilir ve yönetebilir. 

Aşağıdaki diyagramda, pazarlama, finans ve hukuk departmanları için kaynakları içeren kataloglar gösterilmektedir. Proje yöneticileri, bu katalogları kullanarak ekiplerine veya projelerine yönelik erişim paketleri oluşturabilir.

![Yetkilendirme Yönetimi temsilcisi örneği](./media/entitlement-management-delegate/elm-delegate.png)

Temsilcinin ardından, pazarlama departmanı aşağıdaki tabloya benzer rollere sahip olabilir.

| Kullanıcı | İş rolü | Azure AD rolü | Yetkilendirme Yönetimi rolü |
| --- | --- | --- | --- |
| Hana | BT Yöneticisi | Genel yönetici veya Kullanıcı Yöneticisi |  |
| Mamta | Pazarlama Yöneticisi | Kullanıcı | Katalog Oluşturucu ve Katalog sahibi |
| Bob | Pazarlama lideri | Kullanıcı | Katalog sahibi |
| Jessica | Pazarlama Projesi Yöneticisi | Kullanıcı | Paket Yöneticisi 'ne erişim |

## <a name="entitlement-management-roles"></a>Yetkilendirme Yönetimi rolleri

Yetkilendirme yönetiminin, yetkilendirme yönetimine özgü aşağıdaki rolleri vardır.

| Yetkilendirme Yönetimi rolü | Description |
| --- | --- |
| Katalog Oluşturucu | Kataloglar oluşturun ve yönetin. Genellikle genel yönetici olmayan bir BT Yöneticisi veya bir kaynak koleksiyonu için kaynak sahibi. Katalog oluşturan kişi otomatik olarak kataloğun ilk katalog sahibi olur ve daha fazla katalog sahibi ekleyebilir. Bir katalog Oluşturucu yönetmez veya sahip olmadıkları katalogları göremez ve bir kataloğa ait olmadıkları kaynakları ekleyemez. Katalog oluşturucunun başka bir kataloğu yönetmesi veya sahip olmadıkları kaynakları eklemesi gerekiyorsa, o kataloğun veya kaynağın ortak sahibi olmasını isteyebilir. |
| Katalog sahibi | Mevcut katalogları düzenleyin ve yönetin. Genellikle bir BT Yöneticisi veya kaynak sahipleri ya da Katalog sahibinin seçtiği bir kullanıcı. |
| Paket Yöneticisi 'ne erişim | Bir kataloğun içindeki tüm mevcut erişim paketlerini düzenleyin ve yönetin. |
| Paket atama Yöneticisi 'ne erişim | Tüm mevcut erişim paketleri atamalarını düzenleyin ve yönetin. |

Ayrıca, seçilmiş onaylayan ve bir erişim paketinin istek sahibi, rol olmasa da haklara sahip olmalıdır.

| Sağ | Description |
| --- | --- |
| Onaylayan | Erişim paketi tanımlarını değiştiremese de, paketlere erişim isteklerini onaylamak veya reddetmek için bir ilke tarafından yetkilendirilmiştir. |
| Requestor | Erişim paketi istemek için bir erişim paketi ilkesi tarafından yetkilendirilir. |

Aşağıdaki tabloda, Yetkilendirme Yönetimi rollerinin yapa, görevleri listelenmiştir.

| Görev | Yönetici | Katalog Oluşturucu | Katalog sahibi | Paket Yöneticisi 'ne erişim | Paket atama Yöneticisi 'ne erişim |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Bir katalog oluşturucuya temsilci seçme](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |  |
| [Bağlı kuruluş ekleme](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |  |
| [Yeni Katalog oluşturma](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Kataloğa kaynak ekleme](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Katalog sahibi ekleme](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Katalog düzenleme](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Katalog silme](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Erişim paketi yöneticisine temsilci seçme](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Erişim paketi yöneticisini kaldırma](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Bir katalogda yeni bir erişim paketi oluşturma](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Erişim paketindeki kaynak rollerini değiştirme](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [İlke oluşturma ve düzenleme](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Bir kullanıcıyı doğrudan bir erişim paketine atama](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Bir kullanıcıyı erişim paketinden doğrudan kaldırma](entitlement-management-access-package-assignments.md#remove-an-assignment) | :heavy_check_mark:  |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Erişim paketine kimin atanmasına sahip olduğunu görüntüleme](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Bir erişim paketinin isteklerini görüntüleme](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Bir isteğin teslim hatalarını görüntüleme](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [İsteği yeniden işleme](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Bekleyen bir isteği iptal etme](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Erişim paketini gizle](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Erişim paketini silme](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Kataloğa kaynak eklemek için gerekli roller

Genel yönetici, bir katalogda grup (bulut tarafından oluşturulan güvenlik grupları veya bulut tarafından oluşturulan Microsoft 365 grupları), uygulama veya SharePoint Online sitesi ekleyebilir veya kaldırabilir. Bir Kullanıcı Yöneticisi, Dizin rolüne atanabilir olarak yapılandırılmış bir grup dışında, bir katalogda grup veya uygulama ekleyebilir veya kaldırabilir. Bir Kullanıcı yöneticisinin, Dizin rolüne atanabilir olarak yapılandırılmış grupları içeren bir katalogdaki erişim paketlerini yönetebileceğini unutmayın.  Rol atanabilir gruplar hakkında daha fazla bilgi için başvuru [Azure Active Directory bir rol atanabilir grup oluşturun](../roles/groups-create-eligible.md).

Genel yönetici veya Kullanıcı Yöneticisi olmayan bir kullanıcı için bir kataloğa gruplar, uygulamalar veya SharePoint Online siteleri eklemek üzere, bu kullanıcının hem gerekli Azure AD dizin rolüne hem *de* Katalog sahibi yetkilendirme yönetimi rolüne sahip olması gerekir. Aşağıdaki tabloda, bir kataloğa kaynak eklemek için gereken rol birleşimleri listelenmektedir. Bir katalogdan kaynakları kaldırmak için aynı rollere sahip olmanız gerekir.

| Azure AD dizin rolü | Yetkilendirme Yönetimi rolü | Güvenlik grubu eklenebilir | Microsoft 365 grubu eklenebilir | Uygulama eklenebilir | SharePoint Online sitesi eklenebilir |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Genel yönetici](../roles/permissions-reference.md) | yok |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Kullanıcı yöneticisi](../roles/permissions-reference.md) | yok |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune yöneticisi](../roles/permissions-reference.md) | Katalog sahibi | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange yöneticisi](../roles/permissions-reference.md) | Katalog sahibi |  | :heavy_check_mark: |  |  |
| [Takımlar Hizmet Yöneticisi](../roles/permissions-reference.md) | Katalog sahibi |  | :heavy_check_mark: |  |  |
| [SharePoint yöneticisi](../roles/permissions-reference.md) | Katalog sahibi |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Uygulama Yöneticisi](../roles/permissions-reference.md) | Katalog sahibi |  |  | :heavy_check_mark: |  |
| [Bulut uygulaması Yöneticisi](../roles/permissions-reference.md) | Katalog sahibi |  |  | :heavy_check_mark: |  |
| Kullanıcı | Katalog sahibi | Yalnızca Grup sahibi ise | Yalnızca Grup sahibi ise | Yalnızca uygulama sahibi ise |  |

Bir görev için en düşük ayrıcalıklı rolü öğrenmek için, [Azure Active Directory ' de yönetici görevine göre yönetici rollerine](../roles/delegate-by-task.md#entitlement-management)de başvurabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Katalog oluşturucuları için erişim yönetimini devretmek](entitlement-management-delegate-catalog.md)
- [Kaynak kataloğu oluşturma ve yönetme](entitlement-management-catalog-create.md)
