---
title: Microsoft Müşteri Sözleşmesi ödeme hesabınızı kullanmaya başlama - Azure
description: Microsoft Müşteri Sözleşmesi ödeme hesabınızı anlama
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: 835686d639679cca7e9a83b5297b365953835e47
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880747"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Microsoft Müşteri Sözleşmesi ödeme hesabınızı kullanmaya başlama

Azure’ı kullanmak için kaydolduğunuzda bir ödeme hesabı oluşturulur. Faturaları, ödemeleri yönetmek ve maliyetleri izlemek için ödeme hesabınızı kullanırsınız. Birden çok ödeme hesabına erişiminiz olabilir. Örneğin, kişisel projeleriniz için Azure’a kaydolmuş olabilirsiniz. Ayrıca kuruluşunuzun Kurumsal Anlaşması veya Microsoft Müşteri Sözleşmesi aracılığıyla da Azure’a erişiminiz olabilir. Bu senaryoların her biri için ayrı bir ödeme hesabınız olur.

Bu makale, Microsoft Müşteri Sözleşmesi’ne ilişkin ödeme hesabı için geçerlidir. [Microsoft Müşteri Sözleşmesi’ne erişiminiz olup olmadığını denetleyin](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Ödeme hesabınız

Microsoft Müşteri Sözleşmesi için ödeme hesabınız, faturalarınızı ve ödeme yöntemlerinizi yönetmenize olanak sağlayan bir veya daha fazla faturalama profili içerir. Her faturalama profili, faturalama profilinin faturasındaki maliyetleri düzenlemenize olanak sağlayan bir veya daha fazla fatura bölümü içerir.

Aşağıdaki diyagramda faturalama hesabıyla faturalama profilleri ve fatura bölümleri arasındaki ilişki gösterilir.

![Microsoft Müşteri Sözleşmesi faturalama hiyerarşisini gösteren diyagram](./media/billing-mca-overview/mca-billing-hierarchy.png)

Ödeme hesabındaki roller en yüksek izin düzeyine sahiptir. Varsayılan olarak yalnızca Azure’a kaydolan kullanıcı, ödeme hesabına erişim elde eder. Bu roller, faturaları görüntülemesi ve kuruluşunuzun tamamına ait maliyetleri izlemesi gereken finans ve BT yöneticileri gibi kullanıcılara atanmalıdır. Daha fazla bilgi için bkz. [ödeme hesabı rolleri ve görevleri](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Faturalama profilleri

Faturanızı ve ödeme yöntemlerinizi yönetmek için faturalama profili kullanın. Hesabınızdaki her bir faturalama profili için ay başında aylık bir fatura oluşturulur. Fatura, önceki aya ait tüm Azure aboneliklerinin ve diğer satın alımların ilgili ücretlerini içerir.

Ödeme hesabınız için otomatik olarak bir faturalama profili oluşturulur. Varsayılan olarak bir fatura bölümü içerir. İhtiyaçlarınıza bağlı olarak proje, bölüm veya geliştirme ortamı gibi belirli alanlardaki maliyetlerinizi kolayca izleyip düzenlemek için ek bölümler oluşturabilirsiniz. Faturalama profilinizin faturasında her bir abonelikteki kullanımınızı ve buna atadığınız satın almaları yansıtan bölümler görürsünüz.

Faturalama profillerindeki roller, faturaları ve ödeme yöntemlerini görüntüleme ve yönetme izinlerine sahiptir. Bu rolleri, kuruluşunuzdaki muhasebe takımının üyeleri gibi faturaları ödeyen kullanıcılara atayın. Daha fazla bilgi için bkz. [faturalama profili rolleri ve görevleri](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Her faturalama profili aylık bir fatura alır.

Her bir faturalama profili için ay başında aylık bir fatura oluşturulur. Fatura, önceki aya ait tüm ücretleri içerir.

Azure portalında faturayı görüntüleyebilir, belgeleri indirebilir ve gelecekteki faturaları e-postayla alacak şekilde ayarı değiştirebilirsiniz. Daha fazla bilgi için bkz. [Microsoft Müşteri Sözleşmesi için faturaları indirme](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Fatura ödeme yöntemleri

Her faturalama profilinin kendi ödeme yöntemi vardır ve faturaları bu yöntemle ödenir. Aşağıdaki ödeme yöntemleri desteklenir:

| Tür             | Tanım  |
|------------------|-------------|
|Azure kredileri    |  Krediler, faturanızdaki uygun ücretlere otomatik olarak uygulanır ve ödemeniz gereken tutarı azaltır. Daha fazla bilgi için bkz. [faturalama profiliniz için Azure kredi bakiyesini izleme](billing-mca-check-azure-credits-balance.md). |
|Çek/havale | Hesabınız, çek/havale yoluyla ödeme için onaylanırsa. Faturanız için ödenmesi gereken tutarı çek/havale yoluyla ödeyebilirsiniz. Ödeme yönergeleri faturada sunulur |
|Kredi kartı | Azure web sitesi üzerinden Azure’a kaydolan müşteriler, kredi kartıyla ödeme yapabilir. |

### <a name="apply-policies-to-control-purchases"></a>Satın almaları denetlemek için ilkeler uygulama

Faturalama profili kullanarak Azure Market ve Rezervasyon satın almalarını denetlemek için ilkeler uygulayın. Azure Ayrılmış Sanal Makine Örnekleri ve Market ürünlerinin satın alımını devre dışı bırakmak için ilkeler ayarlayabilirsiniz. İlkeler uygulandığında, faturalama profiline faturalandırılan abonelikler bu satın almaları gerçekleştirmek için kullanılamaz.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure planları, abonelikler için fiyatlandırmayı ve hizmet düzeyi sözleşmesini belirler.

Azure planları, Azure abonelikleri için fiyatlandırmayı ve hizmet düzeyi sözleşmelerini belirler. Faturalama profili oluşturduğunuzda bunlar otomatik olarak etkinleştirilir. Faturalama profiliyle ilişkili tüm fatura bölümleri bu planları kullanabilir. Fatura bölümüne erişimi olan kullanıcılar, Azure abonelikleri oluşturmak için planları kullanır. Aşağıdaki Azure planları, Microsoft Müşteri Sözleşmesi için ödeme hesaplarında desteklenir:

| Planlama             | Tanım  |
|------------------|-------------|
|Microsoft Azure Planı   | Kullanıcıların herhangi bir iş yükünü çalıştırabilen abonelikler oluşturmasına olanak sağlar.  |
|Geliştirme ve Test için Microsoft Azure Planı | Visual Studio abonelerinin, geliştirme veya test iş yükleri için kısıtlanmış abonelikler oluşturmasına olanak sağlar. Bu abonelikler, daha düşük fiyatlar ve Azure portalındaki özel sanal makine görüntülerine erişim gibi avantajlar elde eder. |

## <a name="invoice-sections"></a>Fatura bölümleri

Faturanızdaki maliyetleri düzenlemek için fatura bölümleri oluşturun. Örneğin, kuruluşunuz için tek bir faturaya ihtiyacınız olabilir, ancak maliyetleri takıma, ekibe veya projeye göre düzenlemek isteyebilirsiniz. Bu senaryoda, her bir bölüm, ekip veya proje için fatura bölümü oluşturduğunuz tek bir faturalama profiliniz vardır.

Bir fatura bölümü oluşturulduğunda, diğer kullanıcılara, bölüme faturalandırılan Azure abonelikleri oluşturma izni verebilirsiniz. Aboneliklere yönelik tüm kullanım ücretleri ve satın alma işlemleri daha sonra bölüme faturalandırılır.

Fatura bölümündeki roller, kimlerin Azure abonelikleri oluşturduğunu denetleme iznine sahiptir. Bu rolleri, kuruluşumuzdaki takımlar için Azure ortamını ayarlayan mühendislik liderleri ve teknik mimarlar gibi kullanıcılara atayın. Daha fazla bilgi için bkz. [fatura bölümü rolleri ve görevleri](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun.

Yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Sonraki adımlar

Ödeme hesabınız hakkında bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure’daki Microsoft Müşteri Sözleşmesi yönetici rollerini anlama](billing-understand-mca-roles.md)
- [Microsoft Müşteri Sözleşmesi için ek Azure aboneliği oluşturma](billing-mca-create-subscription.md)
- [Maliyetlerinizi düzenlemek için faturanızda bölümler oluşturma](billing-mca-section-invoice.md)
