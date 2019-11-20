---
title: SaaS karşılama API 'Leri-SSS | Azure Marketi
description: Azure Marketi 'nde SaaS teklifinin müşterilerine yönelik bulma ve satın alma deneyimleri.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 2f8aa41604ac176181252fb7b30cdeb2f039d10f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822883"
---
# <a name="saas-fulfillment-apis---faq"></a>SaaS gerçekleştirme API’leri - SSS

Azure müşterilerinin SaaS teklifine abone olmalarını sağlamak için Azure Marketi ile tümleştirme gereksinimleri listelenmiştir.

## <a name="discovery-experience"></a>Bulma deneyimi

Teklif yayımlandıktan sonra, Azure kullanıcıları Azure Marketi 'nde SaaS teklifini bulabilir. Müşterileriniz ürün türü (SaaS) temelinde teklifleri filtreleyebilir ve ilgilendiğiniz SaaS hizmetlerini keşfedebilecektir.

## <a name="purchase-experience"></a>Satın alma deneyimi

Bir Kullanıcı belirli bir SaaS hizmeti ile ilgilendikten sonra, Kullanıcı bu hizmete Azure Marketi 'nden abone olabilir.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Azure kullanıcısının Azure Marketi 'nde SaaS teklifine abone olması ne anlama geliyor?

Bu, bir kullanıcının SaaS hizmeti ile ilişkili kullanım koşullarını ve gizlilik bildirimini görüntüleyebileceği ve BT için Microsoft 'un faturasında, SaaS teklifinin yayıncısı olan faturalandırma koşullarına göre ödeme yapmayı kabul etmiş olduğu anlamına gelir. Kullanıcılar, SaaS hizmeti tüketimi için ödeme yapmak üzere Azure 'da mevcut ödeme profilini kullanabilir.

Bu çok sayıda nedenden dolayı faydalıdır. Müşteriler artık Microsoft Bulut platformunu güvenilir bir kaynak olarak kullanarak tek bir yerde bulabilir ve abone olabilir. Ayrıca, müşteriler her ISV yazılımını bağımsız olarak açıkça ödemek zorunda kalmadan mevcut ödeme profillerini kullanabilir.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Teklif abone olduğunda kullanıcı otomatik olarak ücretlendirilir mi?

SaaS teklifine abone olurken, Kullanıcı Microsoft Platformu aracılığıyla SaaS hizmetinin tüketimi için ödeme yapmayı kabul etmiş demektir. Ancak, ücretler yalnızca teklif tüketilmişse başlatılır. Kullanıcının SaaS teklifine gitmesi ve teklifi tüketmeye başlamak için hesap oluşturmayı onaylamasını gerekir. Daha sonra Microsoft 'un bu müşteri SaaS aboneliği için faturalandırmaya başlamasını bildirecaksınız.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Kullanıcı SaaS teklifinizi abone olduğunda nasıl bilgilendirirsiniz?

Bir teklifine abone olduktan sonra Azure kullanıcısı tüm tekliflerini Azure 'da bulabilir ve yönetebilir. Varsayılan olarak, yeni abone olunan SaaS teklifinin durumu **' sağlama, bekleyen tamamlama '** olur. Bu durumda, Azure kullanıcısına Azure portal SaaS Abonelik yönetimi deneyimine gitmek için bir **' hesap yapılandırma '** eylemi istenir.

Kullanıcı **' hesabı Yapılandır '** seçeneğine tıkladığında, SaaS hizmeti Web sitesine yönlendirilir. Gezindikleri URL, teklifi yayınlama sırasında yayımcı tarafından sağlanır. Bu sayfa, yayımcının giriş sayfası olarak adlandırılır. Azure kullanıcıları, Azure 'daki mevcut AAD kimlik bilgilerini temel alarak SaaS giriş sayfasında oturum açabilmelidir.

Azure kullanıcısı giriş sayfasına yeniden yönlendirildiğinde sorgu URL 'sine bir belirteç eklenir. Bu belirteç kısa süreli ve 24 saatlik bir süre için geçerlidir. Daha sonra bu belirtecin varlığını algılayabilir ve belirteçle ilişkili daha fazla bağlam almak için Microsoft 'un API 'sini çağırabilirsiniz.

![Müşteri aboneliği akışı](media/saas-metering-service-integration-flow-a.png)

Bir SaaS teklifinin yaşam döngüsünde Transact senaryolarını işlemeye yönelik API sözleşmesi hakkında daha fazla bilgi için [SaaS karşılama API 'si](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) belgesine bakın.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Kullanıcının Azure 'da abone olduğu SaaS teklifini nasıl anlarsınız?

`Resolve` API 'sine yapılan yanıt, SaaS aboneliğiyle ilişkili teklif ve plan bilgilerini içerir.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Azure kullanıcısı bu Azure aboneliğiyle ilişkili planı nasıl değiştirebilir?

* Azure kullanıcısı SaaS aboneliğiyle ilişkili planı doğrudan SaaS deneyiminde veya Microsoft Platformu aracılığıyla değiştirebilir.

* Dönüştürme işlemleri, fatura döngüsünün herhangi bir saatinde yapılabilir. Her dönüştürmeyi kabul etmeniz gerekir, bu, onaylanmış bir kez etkili olur.

* Ön ödemeli plan (**aylık** veya **yıllık**) ücretleri, eşit olarak derecelendirilmiştir. Dönüştürme zamanına göre yayılan tüm fazla kullanım, bir sonraki faturada ücretlendirilecektir. Yeni yerleştirme, yeni plana göre yayınlanacaktır.

>[!Note]
>Belirli dönüştürme yollarını desteklemek istemiyorsanız, eski sürüme engel olabilirsiniz.

Aşağıdaki sıra, SaaS deneyiminde bir Azure müşterisi değiştiğinde akışı yakalar:

![Müşteri planı değişiklik akışı](media/saas-metering-service-integration-flow-b.png)

Aşağıdaki sıra, Microsoft 'un storefront bir Azure müşterisi tarafından planda değiştiği sırada akışı yakalar.

![Müşteri storefront planı değişiklik akışı](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Azure kullanıcısı, Azure aboneliği ile ilişkili planın aboneliğini nasıl iptal edebilir?

Bir Azure kullanıcısı satın alınan SaaS teklifini doğrudan SaaS deneyiminde veya Microsoft Platformu aracılığıyla abonelikten kaldırabilir. Kullanıcı abone olduktan sonra, bundan sonra bir sonraki fatura döngüsünden ücretlendirilmeyecektir.

Aşağıdaki sıra, bir Azure müşterisi SaaS deneyiminde SaaS teklifine abone olarak aboneliği kaldırdığınızda akışı yakalar:

![SaaS deneyiminde müşteri aboneliği kaldırma](media/saas-metering-service-integration-flow-d.png)

Aşağıdaki sıra, Azure kullanıcısı Microsoft 'un storefront abone olarak kaldırılırken akışı yakalar:

![Microsoft 'un storefront abone olduğu müşteri aboneliği](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla bilgi için bkz. [Market ölçüm hizmeti API 'leri](./marketplace-metering-service-apis.md) .
