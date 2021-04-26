---
title: Yönetilen güvenlik hizmeti sağlayıcısı (MSSPs) fikri mülkiyet özelliğini Azure Sentinel 'de koruma | Microsoft Docs
description: Yönetilen güvenlik hizmeti sağlayıcılarının (MSSPs 'ler) Azure Sentinel 'de oluşturduğlarındaki fikri mülkiyet özelliğini nasıl koruyabileceği hakkında bilgi edinin.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: bagol
ms.openlocfilehash: 1c15c341d85fae667ee23883043350340ad866b8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315644"
---
# <a name="protecting-mssp-intellectual-property-in-azure-sentinel"></a>Azure Sentinel 'de MSSP fikri mülkiyet özelliğini koruma

Bu makalede, yönetilen güvenlik hizmeti sağlayıcılarının (MSSPs 'ler) Azure Sentinel Analytics kuralları, arama sorguları, PlayBook 'lar ve çalışma kitapları gibi Azure Sentinel 'de geliştirdikleri fikri mülkiyet özelliğini korumak için kullanabilecekleri yöntemler açıklanmaktadır.

Seçtiğiniz yöntem, müşterilerinizin her birinin Azure 'u nasıl satın aldığına bağlı olarak değişir. [bulut çözümü sağlayıcısı (CSP)](#cloud-solutions-providers-csp)olarak davranma veya müşterinin bir [Kurumsal Anlaşma (EA)/pay-as-you-go (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg) hesabına sahip olup olmadığı. Aşağıdaki bölümlerde bu yöntemlerin her biri ayrı olarak açıklanır.

## <a name="cloud-solutions-providers-csp"></a>Bulut çözüm sağlayıcıları (CSP)

Azure 'u bir bulut çözümü sağlayıcısı (CSP) olarak yeniden satıyorsanız müşterinin Azure aboneliğini yönetiyorsunuz. [Şirket adına yönetici (AOBO)](/partner-center/azure-plan-manage)sayesinde, MSSP kiracınızdan yönetici aracıları grubundaki kullanıcılar, müşterinin Azure aboneliğine sahip erişimi ile verilir ve müşterinin varsayılan olarak erişimi yoktur.

Yönetim aracıları grubunun dışında, MSSP kiracısından gelen diğer kullanıcıların müşteri ortamına erişmesi gerekiyorsa, [Azure açık Tathouse](multiple-tenants-service-providers.md)kullanmanızı öneririz. Azure ınthouse, yerleşik rollerden birini kullanarak, bir kaynak grubu veya abonelik gibi belirli bir kapsama erişimi olan kullanıcılara veya gruplara izin vermenizi sağlar.

Müşteri kullanıcılarına Azure ortamına erişim sağlamanız gerekiyorsa, bu kullanıcılara, aboneliğin tamamını değil, *kaynak grubu* düzeyinde erişim vermenizi öneririz. böylece ortamın parçalarını gerektiği gibi gösterebilir/gizleyebilirsiniz.

Örnek:

- Müşteriye, uygulamalarının bulunduğu birkaç kaynak grubuna erişim izni verebilir, ancak yine de Azure Sentinel çalışma alanını müşterinin erişimi olmayan ayrı bir kaynak grubunda tutabilirsiniz.

- Müşterilerin, kendi kaynak grubunda bulunabilecek ayrı kaynaklar olan seçili çalışma kitaplarını ve PlayBook 'ları görüntülemesini sağlamak için bu yöntemi kullanın.

Kaynak grubu düzeyinde erişim izni verilmekle birlikte, müşteriler Azure Sentinel 'e erişim olmadan bile, bir VM 'deki Günlükler gibi erişebilecekleri kaynakların günlük verilerine erişmeye devam eder. Daha fazla bilgi için bkz. [kaynağa göre Azure Sentinel verilerine erişimi yönetme](resource-context-rbac.md).

> [!TIP]
> Müşterilerinize tüm aboneliğe erişimi sağlamanız gerekiyorsa, [Kurumsal Sözleşmeler (EA)/Kullandıkça Öde (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg)içinde bu kılavuzu görmek isteyebilirsiniz.
>

### <a name="sample-azure-sentinel-csp-architecture"></a>Örnek Azure Sentinel CSP mimarisi

Aşağıdaki görüntüde, CSP müşterilerine erişim sağlarken [önceki bölümde](#cloud-solutions-providers-csp) açıklanan izinlerin nasıl çalıştığı açıklanmaktadır:

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="CSP müşterilerinin Azure Sentinel fikri mülkiyet özelliğini koruyun.":::

Bu görüntüde:

- CSP aboneliğine **sahip** erişimi olan kullanıcılar, MSSP Azure AD kiracısındaki yönetici aracıları grubundaki kullanıcılardır.
- MSSP 'deki diğer gruplar, Azure açık Thouse aracılığıyla müşteri ortamına erişim sağlar.
- Azure kaynaklarına müşteri erişimi, Azure RBAC tarafından kaynak grubu düzeyinde yönetilir.

    Bu, MSSPs 'nin, analiz kuralları ve arama sorguları gibi Azure Sentinel bileşenlerini gerektiği gibi gizlemesini sağlar.

Daha fazla bilgi için bkz. [Azure açık Tathouse belgeleri](/azure/lighthouse/concepts/cloud-solution-provider).

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>Kurumsal anlaşmalar (EA)/Kullandıkça Öde (PAYG)

Müşteriniz doğrudan Microsoft 'tan satın alıyorsa, müşterinin Azure ortamına zaten tam erişimi vardır ve müşterinin Azure aboneliğindeki herhangi bir şeyi gizleyemezsiniz.

Bunun yerine, korumanız gereken kaynak türüne bağlı olarak, Azure Sentinel 'de geliştirmiş olduğunuz fikri mülkiyet özelliğini aşağıdaki gibi koruyun:

### <a name="analytics-rules-and-hunting-queries"></a>Analiz kuralları ve arama sorguları

Analiz kuralları ve arama sorguları Azure Sentinel içinde bulunur ve bu nedenle Azure Sentinel çalışma alanından ayrılamamalıdır.

Bir Kullanıcı yalnızca Azure Sentinel okuyucu izinlerine sahip olsa bile, sorguyu yine de görüntüleyebilecektir. Bu durumda, analiz kurallarınızı barındırmaya ve sorguları müşteri kiracısı yerine kendi MSSP kiracınızda arayacağız.

Bunu yapmak için, Azure Sentinel 'i etkin bir şekilde kendi kiracınızda bir çalışma alanı gerekir ve ayrıca [Azure Mathouse](multiple-tenants-service-providers.md)aracılığıyla müşteri çalışma alanını görmeniz gerekir.

MSSP kiracısında müşteri kiracısındaki verilere başvuran bir analitik kural veya arama sorgusu oluşturmak için, `workspace` aşağıdaki gibi bir ifade kullanmanız gerekir:

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

`workspace`Analiz kurallarınız için bir ifade eklerken şunları göz önünde bulundurun:

- **Müşteri çalışma alanında uyarı yok**. Bu şekilde oluşturulan kurallar, müşteri çalışma alanında uyarı veya olay oluşturmaz. Hem uyarı hem de olaylar yalnızca MSSP çalışma alanınızda bulunur.

- **Her müşteri için ayrı uyarılar oluşturun**. Bu yöntemi kullandığınızda, çalışma alanı açıklaması her durumda farklı olacağı için her müşteri ve algılama için ayrı uyarı kuralları kullanmanızı öneririz.

    Uyarının tetiklendiği müşteriyi kolayca belirlemek için, uyarı kuralı adına müşteri adını ekleyebilirsiniz. Ayrı uyarılar, betikleri kullanarak yönetmek isteyebileceğiniz çok sayıda kurala yol açabilir veya [kod olarak Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928)'e sahiptir.

    Örnek:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="Her müşteri için MSSP çalışma alanınızda ayrı kurallar oluşturun.":::

- **Her müşteri için ayrı MSSP çalışma alanları oluşturun**. Her müşteri ve algılama için ayrı kurallar oluşturmak, çalışma alanınız için en fazla analiz kuralı sayısına ulaşmanıza neden olabilir (512). Birçok müşteriniz varsa ve bu sınıra ulaşmayı düşünüyorsanız, her müşteri için ayrı bir MSSP çalışma alanı oluşturmak isteyebilirsiniz.

    Örnek:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="Her müşteri için MSSP kiracınızda bir çalışma alanı ve kurallar oluşturun.":::

> [!IMPORTANT]
> Bu yöntemi başarıyla kullanmanın anahtarı, çalışma alanlarınızdaki büyük bir kural kümesini yönetmek için Otomasyonu kullanıyor.
>
> Daha fazla bilgi için bkz. [çapraz çalışma alanı analiz kuralları](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211)
>

### <a name="workbooks"></a>Çalışma Kitapları

Müşterinizin kopyalamasını istemediğiniz bir Azure Sentinel çalışma kitabı geliştirdiyseniz, çalışma kitabını MSSP kiracınızda barındırın. Azure Use aracılığıyla müşteri çalışma alanlarınızın erişimine sahip olduğunuzdan emin olun ve ardından çalışma kitabını bu müşteri çalışma alanlarını kullanacak şekilde değiştirdiğinizden emin olun.

Örnek:

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="Çalışma alanları arası çalışma kitapları":::

Daha fazla bilgi için bkz. [çalışma alanları arası çalışma kitapları](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks).

Müşterinin çalışma kitabı görselleştirmelerini görüntüleyebilmesini istiyorsanız, hala kod gizliliğini korurken, çalışma kitabını Power BI dışa aktarmanız önerilir.

Çalışma kitabınızı Power BI dışa aktarma:

- **Çalışma kitabı görselleştirmelerini paylaşmayı kolaylaştırır**. Müşteriye, bildirilen verileri Azure erişim izinleri gerekmeden görüntüleyebilecekleri Power BI panosuna bir bağlantı gönderebilirsiniz.
- **Zamanlamayı etkinleştirilir**. Bu süre boyunca panonun anlık görüntüsünü içeren e-postaları düzenli aralıklarla göndermek için Power BI yapılandırın.

Daha fazla bilgi için bkz. [Azure izleyici günlük verilerini Power BI Içine aktarma](/azure/azure-monitor/visualize/powerbi).

### <a name="playbooks"></a>Playbook'lar

PlayBook 'ları tetikleyen analitik kuralların oluşturulduğu yere bağlı olarak, PlayBook 'ları 'ları aşağıdaki şekilde koruyabilirsiniz:

- **MSSP çalışma alanında oluşturulan analiz kuralları**.  MSSP kiracısında PlayBook 'ları oluşturmayı ve MSSP çalışma alanından tüm olay ve uyarı verilerini aldığınızdan emin olun. Çalışma alanınızda her yeni kural oluşturduğunuzda PlayBook 'ları ekleyebilirsiniz.

    Örnek:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="MSSP çalışma alanında oluşturulan kurallar.":::

- **Müşteri çalışma alanında oluşturulan analiz kuralları**. Müşterinin çalışma alanından, MSSP çalışma alanınızda barındırılan bir PlayBook 'a analiz kuralları eklemek için Azure Dıthouse komutunu kullanın. Bu durumda, PlayBook, uyarı ve olay verilerini ve diğer müşteri bilgilerini müşteri çalışma alanından alır.

    Örnek:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="Müşteri çalışma alanında oluşturulan kurallar.":::

Her iki durumda da, PlayBook 'un müşterinin Azure ortamına erişmesi gerekiyorsa, bu erişime sahip olan bir kullanıcı veya hizmet sorumlusunu, açık Thouse aracılığıyla kullanın.

Ancak, PlayBook 'un Azure AD, Office 365 veya Microsoft 365 Defender gibi müşterinin kiracısındaki Azure dışı kaynaklara erişmesi gerekiyorsa, müşteri kiracısında uygun izinlerle bir hizmet sorumlusu oluşturmanız ve ardından bu kimliği PlayBook 'a eklemeniz gerekir.

> [!NOTE]
> PlayBook 'larınızla Otomasyon kurallarını birlikte kullanıyorsanız, PlayBook 'ları 'ın canlı olduğu kaynak grubunda Otomasyon kuralı izinlerini ayarlamanız gerekir.
> Daha fazla bilgi için bkz. [PlayBook 'ları çalıştırmak için Otomasyon kuralları izinleri](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks).
>

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz.

- [MSSPs için Azure Sentinel teknik PlayBook](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Azure Sentinel 'de birden fazla kiracıyı MSSP olarak yönetme](multiple-tenants-service-providers.md)
- [Azure Sentinel’i çalışma alanlarına ve kiracılara genişletme](extend-sentinel-across-workspaces-tenants.md)
- [Öğretici: Verilerinizi görselleştirme ve izleme](tutorial-monitor-your-data.md)
- [Öğretici: Azure Sentinel 'de otomatik tehdit yanıtlarını ayarlama](tutorial-respond-threats-playbook.md)
