---
title: Verilerle kullanıcılar ve zenginleştirme uygulamalar sağlamak için SCıM, Microsoft Graph ve Azure AD kullanın
description: Kullanıcı sağlamak ve uygulamanızı gereken verilerle zenginleştirmenin yanı sıra SCıM 'i ve Microsoft Graph kullanma.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: 87df7efcbab89c87a42e611f5fc1219239de6873
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530525"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Kullanıcı sağlamak ve uygulamanızı gereken verilere göre zenginleştirmek için SCıM ve Microsoft Graph birlikte kullanma

**Hedef kitle:** Bu makale, Azure Active Directory (Azure AD) ile tümleştirilecek uygulamalar oluşturmaya yönelik geliştiricilere yöneliktir. Daha önce Azure AD ile tümleştirilmiş olan zoom, ServiceNow ve DropBox gibi uygulamaları kullanmak istiyorsanız, bu makaleyi atlayabilir ve uygulamaya özgü [öğreticileri](../saas-apps/tutorial-list.md) inceleyebilir veya [sağlama hizmeti 'nin nasıl çalıştığını](./how-provisioning-works.md)inceleyebilirsiniz.

**Genel senaryolar**

Azure AD, sağlama için kullanıma hazır bir hizmet ve uygulamalarınızı oluşturmak için genişletilebilir bir platform sağlar. Karar ağacı, bir geliştiricinin sağlamayı otomatik hale getirmek için [SCIM](https://aka.ms/scimoverview) ve [Microsoft Graph](/graph/overview) nasıl kullandığını özetler. 

> [!div class="checklist"]
> * Uygulamamda otomatik olarak Kullanıcı oluşturma
> * Artık erişimleri olmadığında kullanıcıları uygulamamın içinden otomatik olarak kaldır
> * Sağlama için uygulamamı birden çok kimlik sağlayıcısıyla tümleştirin
> * Uygulamalarımı takımlar, Outlook ve Office gibi Microsoft hizmetlerinden alınan verilerle zenginleştirin.
> * Azure AD 'de ve Active Directory Kullanıcıları ve grupları otomatik olarak oluşturun, güncelleştirin ve silin

![SCıM Graf karar ağacı](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Senaryo 1: uygulamamda otomatik olarak Kullanıcı oluşturma
Günümüzde, BT yöneticileri kullanıcı hesaplarını el ile oluşturarak veya CSV dosyalarını uygulamama düzenli olarak yükleyerek kullanıcıları temin etmek. İşlem, müşteriler için zaman alabilir ve uygulamamın benimsenmesini yavaşlatır. Bir kullanıcı oluşturmak için tüm gerekli ad, e-posta ve userPrincipalName gibi temel Kullanıcı bilgileri gereklidir. 

**Öneri:** 
* Müşterileriniz çeşitli IDPs kullanıyorsa ve her biriyle tümleşecek bir eşitleme altyapısı sürdürmek istemiyorsanız, SCıM uyumlu [/Users](https://aka.ms/scimreferencecode) uç noktasını destekler. Müşterileriniz, Azure AD sağlama hizmeti ile tümleştirme için bu uç noktayı kolayca kullanabilir ve erişmesi gerektiğinde otomatik olarak Kullanıcı hesapları oluşturabilir. Uç noktayı bir kez derleyebilir ve tüm IDPs ile uyumlu hale gelir. Bir kullanıcının SCıM kullanarak nasıl oluşturulacağı için aşağıdaki örnek isteği inceleyin.
* Azure AD 'de Kullanıcı nesnesinde ve Microsoft 'un diğer verilerinde bulunan kullanıcı verilerine ihtiyacınız varsa, Kullanıcı sağlaması için bir SCıM uç noktası oluşturmayı ve verilerin geri kalanını almak için Microsoft Graph çağırmayı düşünün. 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Senaryo 2: uygulamamın kullanıcılarını otomatik olarak kaldır
Uygulamamı kullanan müşteriler güvenlik odaklı ve çalışanların artık ihtiyaç duymaları durumunda hesapları kaldırmak için idare gereksinimlerine sahiptir. Uygulamamın sağlamasını kaldırmayı nasıl otomatikleştirebilirim?

**Öneri:** SCıM uyumlu/Users uç noktasını destekler. Azure AD sağlama hizmeti, kullanıcının artık erişimi olmaması durumunda devre dışı bırakılacak ve silinecek istekleri gönderecek. Kullanıcıların hem devre dışı bırakılmasını hem de silmenizi öneririz. Devre dışı bırakma ve silme isteğinin nasıl görüneceğine ilişkin aşağıdaki örneklere bakın. 

Kullanıcıyı devre dışı bırak
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Kullanıcıyı silme
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Senaryo 3: uygulamamda grup üyeliklerini yönetmeyi otomatikleştirme
Uygulamam çeşitli kaynaklara erişmek için grupları kullanır ve müşteriler Azure AD 'de sahip oldukları grupları yeniden kullanmak ister. Azure AD 'den grupları nasıl içeri aktarabilirim ve Üyelikler değiştikçe bunların güncelleştirilmesini nasıl sağlayabilirsiniz?  

**Öneri:** SCıM uyumlu/Groups [uç noktasını](https://aka.ms/scimreferencecode)destekler. Azure AD sağlama hizmeti, uygulamanızda Grup oluşturma ve üyelik güncelleştirmelerini yönetme işlemlerini ele alır. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Senaryo 4: ekipler, Outlook ve OneDrive gibi Microsoft hizmetlerinden alınan verilerle uygulamamı zenginleştirin
Uygulamam Microsoft ekiplerinde yerleşik olarak bulunur ve ileti verilerini kullanır. Ayrıca, OneDrive 'daki kullanıcılar için dosyaları depolarız. Uygulamamı bu hizmetlerdeki ve Microsoft genelindeki verilerle nasıl zenginleştirebilirsiniz?

**Öneri:** [Microsoft Graph](/graph/) , Microsoft verilerine erişmek için giriş noktasıdır. Her iş yükü API 'Leri, gerek duyduğunuz verilerle birlikte kullanıma sunar. Microsoft Graph, yukarıdaki senaryolar için [SCIM sağlama](./use-scim-to-provision-users-and-groups.md) ile birlikte kullanılabilir. Gerekli olan herhangi bir veriyi almak için Graph 'ta çağırma yaparken, uygulamanızda temel kullanıcı özniteliklerini sağlamak için SCıM kullanabilirsiniz. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Senaryo 5: takımlar, Outlook ve Azure AD gibi Microsoft hizmetlerinde yapılan değişiklikleri Izleyin
Ekipler ve Outlook iletilerindeki değişiklikleri izleyebilmem ve bunlara gerçek zamanlı olarak yanıt vermek istiyorum. Uygulamama bu değişiklikleri nasıl gönderebilirim?

**Öneri:** Microsoft Graph çeşitli kaynaklar için [değişiklik bildirimleri](/graph/webhooks) ve [değişiklik izleme](/graph/delta-query-overview) sağlar. Değişiklik bildirimlerinin aşağıdaki sınırlamalarını aklınızda edin:
- Bir olay alıcısı bir olayı ortaya atırsa, ancak herhangi bir nedenden dolayı üzerinde davranmazsa, olay kaybolabilir.
- Değişikliklerin alındığı sıranın kronolojik olması garanti edilmez.
- Değişiklik bildirimleri yukarıdaki nedenlerden dolayı her zaman [kaynak verilerini](/graph/webhooks-with-resource-data) içermez, geliştiriciler genellikle değişiklik bildirimlerini, eşitleme senaryolarında değişiklik izleme ile birlikte kullanır. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Senaryo 6: Azure AD 'de Kullanıcı ve grup sağlama
Uygulamam, müşterilerin Azure AD 'de ihtiyacı olan bir kullanıcı hakkında bilgi oluşturuyor. Bu, işe alma 'yı yönetenden bir ık uygulaması, kullanıcılar için telefon numarası oluşturan bir iletişim uygulaması veya Azure AD 'de değerli veriler üreten bir diğer uygulama olabilir. Azure AD 'deki Kullanıcı kaydı bu verilerle doldurulmi Nasıl yaparım?? 

**Öneri** Microsoft Graph, Azure AD 'ye Kullanıcı sağlamak üzere bugün ile tümleştirebileceğiniz/Users ve/groups uç noktalarını kullanıma sunar. Azure Active Directory, bu kullanıcıların Active Directory yeniden yazmayı desteklemediğini unutmayın. 

> [!NOTE]
> Microsoft 'un, Workday ve başarılı etmenler gibi ık uygulamalardan veri çeken bir sağlama hizmeti vardır. Bu tümleştirmeler Microsoft tarafından oluşturulup yönetilir. Hizmetimizin yeni bir ık uygulaması eklemek için, [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests)üzerinde istek yapabilirsiniz. 

## <a name="related-articles"></a>İlgili makaleler:

- [Eşitleme Microsoft Graph belgelerini gözden geçirin](/graph/api/resources/synchronization-overview)
- [Özel bir SCıM uygulamasını Azure AD ile tümleştirme](use-scim-to-provision-users-and-groups.md)