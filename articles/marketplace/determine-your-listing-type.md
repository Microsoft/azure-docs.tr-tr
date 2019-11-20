---
title: Azure Marketi 'nde yayımlama seçeneğinizi belirleme | Mavisi
description: Bu makalede, uygulamaları Azure Marketi 'Nde nasıl yayımlayacağınızı anlamaya çalışan uygunluk ölçütleri ve yayımlama gereksinimleri iş ortakları açıklanır.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 967d33cb7a9eb3ec922f9d85cfc2581b85bce537
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825897"
---
# <a name="determine-your-publishing-option"></a>Yayımlama seçeneğinizi belirleme
Teklifiniz için seçtiğiniz yayımlama seçeneği, doğrudan uygunluk gereksinimlerine ve Market GTM avantajlarına yöneliktir. Daha önemlisi, yayımlama seçeneği ve teklif türü seçimi, kullanıcıların Market teklifinizle nasıl etkileşime gireceğini tanımlar.

Teklifinizi yapılandırmak için aşağıdaki ana Market kavramlarını anlamanız gerekir: Yayımlama seçenekleri, teklif türleri ve yapılandırma ve teklifinizin Market stokörlerde nasıl ve nerede sunulduğunu belirleyen, eylem çağrıları.

![](./media/marketplace-publishers-guide/storefronts_options_table.png)


Bu makalede, öğrenirsiniz...
<ul><li>    Çözümünüz için uygun storefront belirleme </ul></li>
<ul><li>    Her storefront için hangi yayımlama seçenekleri ve eylem çağrıları kullanılabilir </ul></li>
<ul><li>    Her yayımlama seçeneği için kullanılabilen teklif türleri </ul></li>


## <a name="selecting-a-storefront-publishing-option-and-offer-type-for-your-solution"></a>Çözümünüz için bir storefront, yayımlama seçeneği ve teklif türü seçme

Bir yayımlama seçeneği seçmeden önce Market çözümleri, uygulamalar ve hizmetler için storefront uygunluk gereksinimlerinin anlaşılması önemlidir:

**Azure Market** uygulamaları, Azure için yerleşik veya yerleşik olarak bulunan ve BT ya da geliştirici kitlesi için tasarlanan teknik "derleme bloğu" çözümleridir. Azure Market Danışmanlık Hizmetleri, müşterilerin Azure kullanımını kullanmaya veya hızlandırmasına yardımcı olan profesyonel hizmet teklifleridir.

**Appsource** uygulamaları, Azure veya yerleşik olarak kullanılabilecek iş kolu çözümleridir: Dynamics 365, Office 365, Power BI veya Power Apps. AppSource Danışmanlık Hizmetleri, müşterilerin Dynamics 365 ve Power BI kullanımını kullanmaya veya hızlandırmasına yardımcı olan profesyonel hizmet tekliflerdir.


## <a name="understand-storefront-selection"></a>Storefront seçimini anlayın

Teklifinizin sunulacak olan storefront, Azure Marketi ve/veya AppSource, teklif ayrıntılarınız ve hedef kitlelerinizin yanı sıra teklifinizi oluştururken seçtiğiniz Kategoriler ve endüstriler tarafından otomatik olarak belirlenir. 

>[!Note]
>"Çapraz listeleme" (yalnızca SaaS uygulamaları için): bir liste veya deneme tabanlı teklif, hem teknik hem de iş Kullanıcı kitleleriyle ilgili ölçütleri karşıladığında, teklifiniz her iki stoporonun içinde de listelenir. Aşağıdaki yayımlama seçenekleri hakkında daha fazla bilgi edinin.

## <a name="choose-a-publishing-option"></a>Yayımlama seçeneği seçin

Kullanılabilir yayımlama seçenekleri, müşteri adayı paylaşma ve [ticari Market avantajlarına](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits)erişmenizi sağlarken farklılaştırılan müşteri katılımı sunar.  Yayımlama seçeneğiyle karşılık gelen eylem çağrılarını unutmayın:

| **Yayımlama seçeneği**    | **Açıklama**  |
| :------------------- | :-------------------|
| **Listele** | Uygulama veya hizmetinizin, bir market kullanıcısına **Ilgili kişi** eylemi çağrısı aracılığıyla müşteriye bağlanmanızı istemesini sağlayan basit listesi. |
| **Deneme** | Market 'i kullanarak, çözümünüzün deneme deneyiminizin sağlamasını geliştirin ve bu sayede, olası kullanıcıların satın almadan önce sınırlı bir süre boyunca SaaS, IaaS veya Microsoft uygulama içi deneyiminizi kullanmasına olanak sağlar. Deneme yayımlama seçeneği için kullanılan eylem çağrıları: **ücretsiz deneme** veya **test sürücüsü**. |
|**KLG**  |Keşfedilebilirliğini geliştirmek ve çözümünüzün sağlamasını otomatik hale getirmek için Market 'i kullanın ve mali işlemi ayrı olarak doldurun. KLG teklif türleri şirket içinde bulut geçişleri için idealdir. Eyleme çağrı **Şimdi**alınır.
| **Transact** | Uygulamanız Azure 'da çalışır ve müşteri **Şimdi bu uygulamayı şimdi al** eylemini seçtiğinde müşterinin Azure aboneliğine doğrudan bir kaynak olarak sağlanabilir. Yazılım lisans ücretleri, isteğe bağlı olarak müşterinin ödeme aracı ve koşulları aracılığıyla satın alınabilir ve faturalandırılır ve **ücretsiz yazılım denemenize** (yalnızca Azure Marketi 'nde kullanılabilir) zamana sınırlı erişim sunmayı tercih edebilirsiniz. |

>[!Note]
>Transact Publishing seçeneğini kullanırken, bir teklif türü seçmeden ve teklifinizi oluşturmadan önce fiyatlandırma, faturalandırma, faturalama ve ödeme konularını anlamak önemlidir. [Daha fazla bilgi edinmek Için Market faturalandırma ve ticari hususlar makalesini](./marketplace-commercial-transaction-capabilities-and-considerations.md)gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

*   Bir yayımlama seçeneğine karar verdikten sonra, teklifinizi sunmak için kullanılacak [teklif türünü seçmek](./publisher-guide-by-offer-type.md) için hazır olursunuz.
*   Teklifinizin seçimini ve yapılandırmasını tamamlamak için teklif türüne göre yayımlama seçeneklerinde uygunluk gereksinimlerini gözden geçirin.
*   Çözümünüzün bir teklif türü ve yapılandırmaya nasıl eşlendiğini gösteren örnekler için storefront göre yayımlama düzenlerini gözden geçirin.



