---
title: Azure portal müşterileri ve Temsilcili kaynakları görüntüleyin ve yönetin
description: Azure Use ile bir hizmet sağlayıcı veya kuruluş olarak, Azure portal tüm temsilcilerimizi ve aboneliklerinizi, müşterilerime giderek görüntüleyebilirsiniz.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 78344015ee027b9844b6339fa7cd95d348488a54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419345"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Azure portal müşterileri ve Temsilcili kaynakları görüntüleyin ve yönetin

[Azure açık Thouse](../overview.md) kullanan hizmet sağlayıcıları, temsilcili müşteri kaynaklarını ve abonelikleri görüntülemek için [Azure Portal](https://portal.azure.com) **müşteriler** sayfasını kullanabilir.

> [!TIP]
> Burada hizmet sağlayıcılarına ve müşterilere başvuracağız. [birden çok kiracıyı yöneten kuruluşlar](../concepts/enterprise.md) , yönetim deneyimlerini birleştirmek için aynı süreci kullanabilir.

Azure portal **müşterilerimiz** sayfasına erişmek Için, **tüm hizmetler**' i seçin, ardından **müşterilerimi** arayın ve seçin. Ayrıca, Azure portal üst kısmındaki arama kutusuna "My Customers" girerek de bulabilirsiniz.

**Müşteriler** sayfasının en popüler **müşterileri** bölümünün yalnızca Azure açık thouse aracılığıyla Azure Active Directory (Azure AD) kiracınızda abonelik veya kaynak grupları Temsilcili müşteriler hakkındaki bilgileri gösterdiğini aklınızda bulundurun. Diğer müşterilerle (örneğin, [bulut çözümü sağlayıcısı (CSP) program](/partner-center/csp-overview)aracılığıyla) çalışıyorsanız, [Azure açık thouse kaynaklarına eklendi](onboard-customer.md) sahip olmadığınız sürece **müşteriler** bölümünde bu müşterilerle ilgili BILGILERI görmezsiniz (ancak, belirli CSP müşterileri hakkındaki ayrıntıları sayfada daha düşük olan [bulut çözümü sağlayıcısı (Önizleme) bölümünde](#cloud-solution-provider-preview) görebilirsiniz).

> [!NOTE]
> Müşterileriniz, Azure portal hizmet **sağlayıcılarına** giderek hizmet sağlayıcılarıyla ilgili bilgileri görüntüleyebilir. Daha fazla bilgi için bkz. [hizmet sağlayıcılarını görüntüleme ve yönetme](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Müşteri ayrıntılarını görüntüleme ve yönetme

Müşteri ayrıntılarını görüntülemek için **müşterilerimin** sol tarafında bulunan **müşteriler** ' i seçin.

> [!IMPORTANT]
> Bu bilgileri görmek için kullanıcılara, ekleme sürecinde [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolü (veya okuyucu erişimi içeren başka bir yerleşik rol) verilmiş olması gerekir.

Her müşteri için müşterinin adını, müşteri KIMLIĞINI (kiracı KIMLIĞI) ve görevlendirmede ilişkili **TEKLIF kimliğini** ve **teklif sürümünü** görürsünüz. **Temsilciler** sütununda, temsilci olan aboneliklerin sayısını ve/veya atanan kaynak grubu sayısını görürsünüz.

Sayfanın üst kısmındaki seçenekler, müşteri bilgilerinizi belirli müşteriler, teklifler veya anahtar sözcüklere göre sıralamanıza, filtrelemenize ve gruplandırmasına olanak tanır.

Bu sayfadan aşağıdaki bilgileri görebilirsiniz:

- Bir müşteriyle ilişkili tüm abonelikleri, teklifleri ve temsilcileri görmek için müşterinin adını seçin.
- Bir teklif ve temsilcileri hakkında daha fazla ayrıntı görmek için teklif adını seçin.
- Temsilci atanan abonelikler veya kaynak grupları için rol atamaları hakkında daha fazla ayrıntı görüntülemek için **Temsilciler** sütunundaki girişi seçin.

## <a name="view-and-manage-delegations"></a>Temsilcileri görüntüleyin ve yönetin

Temsilciler, kendisine erişimi olan Kullanıcı ve izinlerle birlikte, temsilci seçilmiş olan abonelik veya kaynak grubunu gösterir. Bu bilgileri görüntülemek için, **müşterilerimiz** sayfasının sol tarafındaki **temsilci** seçme ' yi seçin.

Sayfanın üst kısmındaki seçenekler, bu bilgileri belirli müşteriler, teklifler veya anahtar sözcüklere göre sıralamanıza, filtrelemenize ve gruplandırmasına olanak tanır.

### <a name="view-role-assignments"></a>Rol atamalarını görüntüle

Her bir temsilciyle ilişkili kullanıcılar ve izinler, **rol atamaları** sütununda görünür. Her bir girişi, abonelik veya kaynak grubuna erişim izni verilen kullanıcıların, grupların ve hizmet sorumlularının tam listesini görüntülemek için seçebilirsiniz. Buradan, daha fazla ayrıntı edinmek için belirli bir Kullanıcı, Grup veya hizmet asıl adı seçebilirsiniz.

### <a name="remove-delegations"></a>Temsilcileri kaldır

Bir müşteriyi Azure Aydınlam 'ye eklerken [yönetilen hizmetler kayıt ataması silme rolü](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ' ne sahipseniz, bu kullanıcılar bu temsilcinin satırında görünen çöp kutusu simgesini seçerek bir temsilciyi kaldırabilir. Bunu yaparken, hizmet sağlayıcısının kiracısındaki hiçbir Kullanıcı daha önce atanmış kaynaklara erişemeyecektir.

Daha fazla bilgi için bkz. [bir temsilciye erişimi kaldırma](remove-delegation.md).

## <a name="view-delegation-change-activity"></a>Temsili değiştirme etkinliğini görüntüle

**Müşterilerimin** **etkinlik günlüğü** bölümü, müşteri aboneliklerinin veya kaynak gruplarının kiracınız için her seferinde ve önceden yetkilendirilmiş kaynakların kaldırıldığı her seferinde izler. Bu bilgiler yalnızca [kök kapsamda Izleme okuyucusu rolüne atanmış](monitor-delegation-changes.md)kullanıcılar tarafından görüntülenebilir.

Daha fazla bilgi için bkz. [Azure Portal temsili değişikliklerini görüntüleme](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal).

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Temsilci atanmış bir abonelik bağlamında çalışın

Azure portal içinde, oturum açtığınız dizini değiştirmeden, temsilci bir abonelik bağlamında doğrudan çalışabilirsiniz. Bunun için:

1. Azure portal üst tarafında bulunan **Dizin + abonelik** simgesini seçin.
2. **Varsayılan abonelik filtresinde**, yalnızca bu temsilci abonelik için kutunun seçildiğinden emin olun. Yalnızca belirli bir dizin içindeki abonelikleri göstermek için **geçerli + temsilci Dizin** açılan kutusunu kullanabilirsiniz. (Bu, oturum açtığınız dizini değiştirdiği için **Dizin Değiştir** seçeneğini kullanmayın.)

Daha sonra, [çapraz kiracı yönetim deneyimlerini](../concepts/cross-tenant-management-experience.md)destekleyen bir hizmete eriştiğinizde, hizmet varsayılan olarak seçtiğiniz Temsilcili aboneliğin bağlamıdır. Yukarıdaki adımları izleyerek ve **Tümünü Seç** kutusunu işaretleyerek (veya bunun yerine içinde çalışacak bir veya daha fazla abonelik seçerek) bunu değiştirebilirsiniz.

> [!NOTE]
> Bir aboneliğin tamamına erişmek yerine bir veya daha fazla kaynak grubuna erişim verdiyseniz, bu kaynak grubunun ait olduğu aboneliği seçin. Daha sonra bu aboneliğin bağlamında çalışacaksınız, ancak yalnızca belirlenen kaynak gruplarına erişebileceksiniz.

Ayrıca, bu hizmetin içinden abonelik veya kaynak grubunu seçerek, şirketler arası yönetim deneyimlerini destekleyen hizmetler içinden Temsilcili abonelikler veya kaynak gruplarıyla ilgili işlevlere da erişebilirsiniz.

## <a name="cloud-solution-provider-preview"></a>Bulut çözümü sağlayıcısı (Önizleme)

**Müşteriler** sayfasının ayrı bir **bulut çözümü sağlayıcısı (Önizleme)** bölümü, [MICROSOFT Müşteri Sözleşmesi 'ni (MCA) imzalayan](/partner-center/confirm-customer-agreement) ve [Azure planına](/partner-center/azure-plan-get-started)ait olan CSP müşterileriniz için fatura bilgilerini ve kaynakları gösterir. Daha fazla bilgi için bkz. [Microsoft Iş ortağı sözleşmesi Faturalandırma hesabınızı kullanmaya başlama](../../cost-management-billing/understand/mpa-overview.md).

Bu tür CSP müşterileri, Azure 'da da eklendi etmeksizin bu bölümde görünür. Benzer şekilde, bir CSP müşterisi, Azure açık Thouse 'a eklemek için **müşterilerimin** **bulut çözümü sağlayıcısı (Önizleme)** bölümünde gözükmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Çapraz kiracı yönetim deneyimleri](../concepts/cross-tenant-management-experience.md)hakkında bilgi edinin.
- Müşterilerinizin Azure portal **hizmet sağlayıcılarına** giderek [hizmet sağlayıcılarını nasıl görüntüleyip yönetebileceğini](view-manage-service-providers.md) öğrenin.
