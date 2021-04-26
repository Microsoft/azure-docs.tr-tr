---
title: SaaS karşılama API 'SI SSS-Microsoft ticari Market
description: Azure müşterilerinin SaaS tekliflerine abone olmalarını sağlamak için Microsoft Commercial Market 'in çeşitli Tümleştirme gereksinimlerinden birkaçı hakkında bilgi edinin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/19/2021
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 760e7210d054e44dfec6d6a6e480baecd04d6807
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044133"
---
# <a name="common-questions-about-saas-fulfillment-apis"></a>SaaS 'in API 'Leri karşılama hakkında genel sorular

Bu makalede, Azure müşterilerinin SaaS tekliflerine abone olmalarını sağlamak için Microsoft Commercial Market 'in çeşitli tümleştirme gereksinimleri açıklanmaktadır.

## <a name="discovery-experience"></a>Bulma deneyimi

SaaS teklifi yayımlandıktan sonra Azure kullanıcıları bunu Azure Marketi 'nde bulabilir. Müşterileriniz, ürün türü (SaaS) temelinde teklifleri filtreleyebilir ve ilgilendiğiniz SaaS hizmetlerini bulabilir.

## <a name="purchase-experience"></a>Satın alma deneyimi

Bir Kullanıcı belirli bir SaaS hizmeti ile ilgilendikten sonra, Kullanıcı bu hizmete Azure Marketi 'nden abone olabilir.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Azure kullanıcısının Azure Marketi 'nde SaaS teklifine abone olması ne anlama geliyor?

Bu, bir kullanıcının SaaS hizmeti ile ilişkili kullanım koşullarını ve gizlilik bildirimini görüntüleyebileceği ve BT için Microsoft 'un faturasında, SaaS teklifinin yayıncısı olan faturalandırma koşullarına göre ödeme yapmayı kabul etmiş olduğu anlamına gelir. Kullanıcılar, SaaS hizmeti tüketimi için ödeme yapmak üzere Azure 'da mevcut ödeme profilini kullanabilir.

Bu özellik birkaç şekilde faydalıdır. Müşteriler artık Microsoft Bulut platformunu güvenilir bir kaynak olarak kullanarak tek bir yerde bulabilir ve abone olabilir. Ayrıca, müşteriler her ISV yazılımını bağımsız olarak açıkça ödemek zorunda kalmadan mevcut ödeme profillerini kullanabilir.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Teklif abone olduğunda kullanıcı otomatik olarak ücretlendirilir mi?

SaaS teklifine abone olurken, Kullanıcı Microsoft Platformu aracılığıyla SaaS hizmetinin tüketimi için ödeme yapmayı kabul etmiş demektir. Ancak, ücretler yalnızca teklif tüketilmişse başlatılır. Kullanıcının SaaS teklifine gitmesi ve teklifi tüketmeye başlamak için hesap oluşturmayı onaylamasını gerekir. Daha sonra Microsoft 'un bu müşteri SaaS aboneliği için faturalandırmaya başlamasını bildirecaksınız.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Kullanıcı SaaS teklifinizi abone olduğunda nasıl bilgilendirirsiniz?

Bir teklifine abone olduktan sonra Azure kullanıcısı tüm tekliflerini Azure 'da bulabilir ve yönetebilir. Varsayılan olarak, yeni abone olunan SaaS teklifinin durumu **sağlama, bekleyen karşılama** olarak gösterilir. Bu durumda, Azure kullanıcısına, Azure portal SaaS Abonelik yönetimi deneyimine gitmek için **Hesap Yapılandırma** eylemiyle karşılaşacaktır.

Kullanıcı **hesabı Yapılandır**' ı seçtiğinde, SaaS hizmeti Web sitesine yönlendirilir. Yayımcı, teklifi yayınlama sırasında URL 'YI yapılandırdı. Bu sayfa, yayımcının giriş sayfası olarak adlandırılır. Azure kullanıcıları, Azure 'daki mevcut Azure Active Directory (Azure AD) kimlik bilgilerini temel alarak SaaS giriş sayfasında oturum açabilirler.

> [!IMPORTANT]
> [İlke](/legal/marketplace/certification-policies?context=/azure/marketplace/context/context)tarafından yönlendirilen Azure Active Directory, çoklu oturum açma (Azure AD SSO) kullanarak satın alma kullanıcısına oturum açmalısınız. `mail`MICROSOFT Graph API 'sinden alınan Kullanıcı kaynağındaki özellik, Azure AD ve MSA için iletişim bilgilerini sağlar `userPrincipalName` . "Mail" alanı Azure AD için boş ve kullanıcının kayıtlı bir e-postası olmayabilir. Bu durumda, bunu saptamanız ve bir iletişim e-postası sormanız önerilir. Bu, müşteri tarafından taslak işleme sırasında veya sonrasında müşteriye ulaşmak üzere bir iletişim e-postası alma şansınız olur.

Azure kullanıcısı giriş sayfasına yeniden yönlendirildiğinde sorgu URL 'sine bir belirteç eklenir. Bu belirteç kısa süreli ve 24 saatlik bir süre için geçerlidir. Daha sonra bu belirtecin varlığını algılayabilir ve belirteçle ilişkili daha fazla bağlam almak için Microsoft 'un API 'sini çağırabilirsiniz.

![Müşteri aboneliği akışı](media/saas-metering-service-integration-flow-a.png)

SaaS teklifinin yaşam döngüsünde Transact senaryolarını işlemeye yönelik API sözleşmesi hakkında daha fazla bilgi için bkz. [SaaS karşılama API 'si](pc-saas-fulfillment-api-v2.md).

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Kullanıcının Azure 'da abone olduğu SaaS teklifini nasıl anlarsınız?

API 'ye yapılan yanıt, `Resolve` SaaS aboneliğiyle ilişkili teklif ve plan bilgilerini içerir.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Azure kullanıcısı bu Azure aboneliğiyle ilişkili planı nasıl değiştirebilir?

* Azure kullanıcısı SaaS aboneliğiyle ilişkili planı doğrudan SaaS deneyiminde veya Microsoft Platformu aracılığıyla değiştirebilir.

* Dönüştürme işlemleri, fatura döngüsünün herhangi bir saatinde yapılabilir. Her dönüştürmeyi onaylamanız istenir ve bu, kabul edildikten sonra etkili olur.

* Ön ödemeli plan (**aylık** veya **yıllık**) ücretleri, eşit olarak derecelendirilmiştir. Dönüştürme zamanına göre yayılan tüm fazla kullanım, bir sonraki faturada ücretlendirilecektir. Yeni yerleştirme, yeni plana göre yayınlanacaktır.

>[!Note]
>Belirli dönüştürme yollarını desteklemek istemiyorsanız, eski sürüme engel olabilirsiniz.

Aşağıdaki sıra, bir Azure müşterisi SaaS deneyiminde bir planı değiştirdiğinde akışı yakalar:

![Müşteri planı değişiklik akışı](media/saas-metering-service-integration-flow-b.png)

Aşağıdaki sıra, bir Azure müşterisi Microsoft 'un çevrimiçi mağazasındaki bir planı değiştirdiğinde akışı yakalar:

![Müşteri çevrimiçi mağaza planı değişiklik akışı](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Azure kullanıcısı, Azure aboneliği ile ilişkili planın aboneliğini nasıl iptal edebilir?

Bir Azure kullanıcısı satın alınan SaaS teklifini doğrudan SaaS deneyiminde veya Microsoft Platformu aracılığıyla abonelikten kaldırabilir. Kullanıcı abone olduktan sonra, bundan sonra bir sonraki fatura döngüsünden ücretlendirilmeyecektir.

Aşağıdaki sıra, bir Azure müşterisi SaaS deneyiminde SaaS teklifine abone olarak aboneliği kaldırdığınızda akışı yakalar:

![SaaS deneyiminde müşteri aboneliği kaldırma](media/saas-metering-service-integration-flow-d.png)

Aşağıdaki sıra, Azure kullanıcısı Microsoft 'un çevrimiçi mağazasındaki aboneliği kaldırdığınızda akışı yakalar:

![Microsoft 'un çevrimiçi mağazasındaki müşteri aboneliği kaldırılıyor](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Sonraki adımlar

[Market ölçüm hizmeti API’leri](./marketplace-metering-service-apis.md)
