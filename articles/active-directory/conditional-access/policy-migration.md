---
title: Koşullu erişim ilkelerini geçirme-Azure Active Directory
description: Azure portal klasik ilkeleri geçirmek için bilmeniz gerekenler hakkında bilgi edinin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1811d5b9ae4d3e34b48e1cdc156438f2bad98d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90601665"
---
# <a name="conditional-access-classic-policy-migration"></a>Koşullu erişim klasik ilke geçişi

Koşullu erişim, sinyalleri bir araya getirmek, kararlar almak ve kuruluş ilkelerini zorlamak için Azure Active Directory tarafından kullanılan araçtır. Koşullu erişim, yeni kimlik temelli denetim düzlemi 'nin kalmadır. Amaç hala aynı olsa da, yeni Azure portal sürümü Koşullu erişimin nasıl çalıştığına ilişkin önemli iyileştirmeler getirmiştir.

Azure portal oluşturduğunuz ilkeleri şu nedenle geçirmeyi düşünün:

- Artık, daha önce işleyemeyen senaryolara adres ekleyebilirsiniz.
- Bunları birleştirerek yönetmeniz gereken ilke sayısını azaltabilirsiniz.
- Tüm koşullu erişim ilkelerinizi tek bir merkezi konumda yönetebilirsiniz.
- Klasik Azure portalı kullanımdan kaldırılacak.

Bu makalede, mevcut koşullu erişim ilkelerinizi yeni çerçeveye geçirmek için bilmeniz gerekenler açıklanmaktadır.

## <a name="classic-policies"></a>Klasik ilkeler

[Azure Portal](https://portal.azure.com), koşullu erişim ilkeleri **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi** altında bulunabilir. Kuruluşunuz Ayrıca bu sayfa kullanılarak oluşturulmuş eski koşullu erişim ilkelerine sahip olabilir. Bu ilkeler *Klasik ilkeler* olarak bilinir. Klasik ilkeler, içinde oluşturduğunuz koşullu erişim ilkelerdir:

- Klasik Azure portalı
- Klasik Intune portalı
- Intune Uygulama Koruması portalı

**Koşullu erişim** sayfasında, **Yönet** bölümünde [**Klasik ilkeler**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) ' i tıklatarak klasik ilkelerinize erişebilirsiniz. 

![Klasik ilkeler görünümünü gösteren Azure AD 'de koşullu erişim](./media/policy-migration/71.png)

**Klasik ilkeler** görünümü şunları yapmak için bir seçenek sunar:

- Klasik ilkelerinize filtre uygulayın.
- Klasik ilkeleri devre dışı bırakın.
- Klasik bir ilkenin ayarlarını gözden geçirin ve devre dışı bırakın.

   ![Mevcut ilke yapılandırması da dahil olmak üzere klasik ilke ayrıntıları](./media/policy-migration/74.png)

> [!WARNING]
> Klasik bir ilke devre dışı bırakıldıktan sonra yeniden etkinleştirilemez.

Klasik bir ilkenin Ayrıntılar görünümü ayarları belgeetmenize, dahil edilen veya dışlanan grupları değiştirmenize ve ilkeyi devre dışı bırakmanıza olanak tanır.

![İlke ayrıntıları-dahil edilecek veya hariç tutulacak gruplar](./media/policy-migration/75.png)

Seçilen grupları değiştirerek veya belirli grupları dışlayarak, tüm dahil edilen kullanıcılar ve gruplar için ilkeyi devre dışı bırakmadan önce, birkaç test kullanıcısı için devre dışı bırakılmış bir ilkenin etkisini test edebilirsiniz.
 
## <a name="migration-considerations"></a>Geçiş fikirleri

Bu makalede, Azure AD koşullu erişim ilkelerine *yeni ilkeler* de denir.
Klasik ilkelerinizde, siz devre dışı bırakana kadar yeni ilkeleriniz ile yan yana çalışmaya devam edersiniz. 

Aşağıdaki yönleri bir ilke birleştirme bağlamında önemlidir:

- Klasik ilkeler belirli bir bulut uygulamasına bağlı olsa da, yeni bir ilkede gereken sayıda bulut uygulaması seçebilirsiniz.
- Klasik bir ilkenin denetimleri ve bir bulut uygulaması için yeni bir ilke, tüm denetimlerin (*ve*) yerine getirilmesi gerekir. 
- Yeni bir ilkede şunları yapabilirsiniz:
   - Senaryonuz için gerekliyse birden çok koşulu birleştirin. 
   - Erişim denetimi olarak birkaç verme gereksinimi seçin ve bunları mantıksal *veya* (seçili denetimlerden birini gerektir) ya da mantıksal *ve* (seçili denetimlerin tümünü gerektir) ile birleştirin.

### <a name="exchange-online"></a>Exchange Online

İstemci uygulamaları gibi **Exchange Active Sync** içeren **Exchange Online** için klasik ilkeleri geçirmek istiyorsanız, bunları yeni bir ilke halinde birleştiremeyebilirsiniz. 

Bu, örneğin, tüm istemci uygulama türlerini desteklemek istiyorsanız, bu durumda olur. İstemci uygulamaları olarak **Exchange Active Sync** olan yeni bir ilkede, diğer istemci uygulamalarını seçemezsiniz.

![Koşullu erişim istemci uygulamalarını seçme](./media/policy-migration/64.png)

Klasik ilkeleriniz çeşitli koşullar içeriyorsa, tek bir yeni ilkeye birleştirme de mümkün değildir. İstemci uygulamaları tarafından yapılandırılan **Exchange Active Sync** olan yeni bir ilke diğer koşulları desteklemez:   

![Exchange ActiveSync seçili koşulları desteklemiyor](./media/policy-migration/08.png)

İstemci uygulamaları koşulu yapılandırılmış **Exchange Active Sync** olan yeni bir ilkeniz varsa, diğer tüm koşulların yapılandırılmadığından emin olmanız gerekir. 

![Koşullu erişim koşulları](./media/policy-migration/16.png)
 
Exchange Online için, istemci uygulamaları koşulu olarak **Exchange Active Sync** içeren, **desteklenen** ve **Desteklenmeyen** cihaz platformlarına izin veren uygulama tabanlı klasik ilkeler. İlişkili yeni ilkede ayrı cihaz platformları yapılandıramamanıza karşın, desteği yalnızca [desteklenen cihaz platformlarıyla](concept-conditional-access-conditions.md#device-platforms) sınırlayabilirsiniz. 

![Koşullu erişim Exchange ActiveSync seçin](./media/policy-migration/65.png)

Şunları içeriyorsa, istemci uygulamaları olarak **Exchange Active Sync** içeren birden fazla klasik ilkeyi birleştirebilirsiniz:

- Yalnızca koşul olarak **Exchange Active Sync** 
- Erişim vermek için çeşitli gereksinimler

Yaygın olarak kullanılan bir senaryo şu şekilde birleştirilir:

- Klasik Azure portalından cihaz tabanlı klasik bir ilke 
- Intune uygulama koruma portalında uygulama tabanlı klasik bir ilke 
 
Bu durumda, klasik ilkelerinizi her iki gereksinimin de seçildiği yeni bir ilkede birleştirebilirsiniz.

![Koşullu erişim izni denetimleri](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Cihaz platformları

Uygulama tabanlı denetimlerle klasik ilkeler, cihaz platformu koşulu olarak iOS ve Android ile önceden yapılandırılmıştır. 

Yeni bir ilkede, desteklemek istediğiniz [cihaz platformlarını](concept-conditional-access-conditions.md#device-platforms) tek tek seçmeniz gerekir.

![Koşullu erişim cihaz platformları seçimi](./media/policy-migration/41.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Yeni ilke kararlarının etkisini öğrenmek için koşullu erişim için yalnızca rapor modunu kullanın.](concept-conditional-access-report-only.md)
- Koşullu erişim ilkesini nasıl yapılandıracağınızı öğrenmek istiyorsanız bkz. [koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md).
- Ortamınız için koşullu erişim ilkelerini yapılandırmaya hazırsanız, bkz. [nasıl yapılır: koşullu erişim dağıtımınızı Plan Azure Active Directory](plan-conditional-access.md). 
