---
title: Özel teklifler | Azure Marketi
description: Uygulama ve hizmet yayımcıları için Azure Market 'te özel teklifler.
services: Azure, Marketplace, Compute
author: qianw211
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/1/2018
ms.author: pabutler
ms.openlocfilehash: 16903d4f9b1bad8a588b1a234ac84bc9c0150c4a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827838"
---
# <a name="private-offers"></a>Özel teklifler

Özel teklifler [Microsoft Azure Market](https://azuremarketplace.microsoft.com/) , yayımcıları yalnızca hedeflenen müşterilere görünen SKU 'lar oluşturmak üzere etkinleştirir.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Özel tekliflerle kurumsal anlaşmalar üzerinde yararlanın

Kurumsal müşteriler, bulut çözümlerini bulmak, denemek ve satın almak için çevrimiçi pazar yerleri giderek daha fazla kullanır. Artık özel tekliflerle, yayımcılar, kuruluşların ihtiyaç duyduğu özellikleri karşılayan hedeflenen müşterilerle özelleştirilmiş çözümleri özel olarak paylaşmak için Market kullanabilir:

- *Anlaşmalı fiyatlandırma* , yayımcıların genel kullanıma sunulan tekliflerden indirimleri ve liste fiyatlarını genişletmesini sağlar.
- *Özel hüküm ve koşullar,* yayımcıların hüküm ve koşulları belirli bir müşteriye uyarlayabilmenizi sağlar.
- *Özelleştirilmiş konfigürasyonlar* , yayımcıların sanal makinelerini, Azure uygulamalarını ve SaaS uygulamalarını tek bir müşterinin ihtiyaçlarına uyarlamasına olanak sağlar. Bu seçenek, yayımcıların tüm müşterilere daha geniş bir şekilde başlatılmadan önce yeni ürün özelliklerine önizleme erişimi sağlamasına de olanak sağlar.

Özel teklifler, yayımcıların genel bir market 'in ölçek ve küresel kullanılabilirliğinden yararlanmasını sağlar ve özel anlaşmalar ve Konfigürasyonlar sağlamak için gereken esnekliği ve denetimi sağlar. Bu özellikler birlikte, bulut Pazar yerlerinin sağlam kurumsal benimseme kapağına yönelik kapıyı açar.  Kuruluşlar artık bekledikleri ve talep ettikleri yollarla satın alabilir ve satılarlar.

Özel teklifler artık sanal makine, Azure uygulaması (çözüm şablonları veya yönetilen uygulamalar olarak uygulanır) ve SaaS uygulamaları tarafından sunulmaktadır. Genel tekliflere benzer şekilde, özel teklifler [bulut iş ortağı portalı](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)aracılığıyla oluşturulup yönetilebilir.  Müşteriler, dakikalar içinde özel tekliflere erişim izni verebilir veya iptal edilebilir.

## <a name="creating-private-offers-using-skus-and-plans"></a>SKU 'Ları ve planları kullanarak özel teklifler oluşturma

*Kamu SKU 'ları veya planlarıyla yeni veya mevcut teklifler*için, yayımcılar yeni SKU 'lar veya planlar oluşturarak ve bunları özel olarak işaretleyerek kolayca yeni, özel Çeşitlemeler oluşturabilir.  [Özel SKU 'lar](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ve planlar bir teklifin bileşenleridir ve hedeflenen müşteriler tarafından yalnızca görünür ve satın alınabilir alınırken. Özel SKU 'Lar ve planlar, temel görüntüleri ve/veya zaten ortak bir SKU veya plan için yayımlanmış meta verileri kullanabilir. Bu seçenek, yayımcıların aynı temel görüntünün birden çok sürümünü yayınlamak ve meta verileri sunmak zorunda kalmadan ortak bir teklifin birden çok özel çeşitlemelerini oluşturmalarına olanak tanır. Yalnızca sanal makine ve Azure uygulaması için özel bir SKU ortak SKU 'SU olan bir temel görüntüyü paylaşıyorsa, teklifin temel görüntüsündeki tüm değişiklikler, bu temel görüntüyü kullanarak tüm ortak ve özel SKU 'Lara yayılır.

*Yalnızca özel SKU 'ları veya planları içeren yeni teklifler*için yayımcılar, tekliflerini başka bir teklif olarak oluşturabilir ve ardından SKU 'ları veya planları özel olarak işaretleyebilir. Yalnızca özel SKU 'Ların veya planların bulunduğu teklifler, [Azure Marketi](https://azuremarketplace.microsoft.com) veya teklifle ilişkilendirilmemiş müşteriler tarafından [Azure Portal](https://azure.microsoft.com/features/azure-portal/) bulunabilir veya erişilebilir olmayacaktır.

## <a name="targeting-customers-with-private-offers"></a>Özel tekliflerle müşterileri hedefleme
Yayımcılar, hem yeni hem de mevcut özel teklifleriniz için abonelik tanımlayıcılarını kullanarak müşterileri hedefleyebilir. Bir sanal makine veya Azure Uygulama teklifi kullanan yayımcılar, özel SKU 'nun kullanılabilirliğini tek bir Azure abonelik kimliğiyle kısıtlayabilir veya en fazla 20.000 Azure abonelik kimliği olan bir CSV 'yi karşıya yükleyebilir. SaaS uygulaması özel teklifi kullanılırken, yayımcılar, el ile veya CSV yükleme yaklaşımını kullanarak bir özel planın kullanılabilirliğini kısıtlamak için bir Azure abonelik KIMLIĞI veya kiracı KIMLIĞI ilişkilendirebilir.

Bir teklif sertifikalandırıldıktan ve yayımlandıktan sonra, müşteriler özel abonelikleri Eşitle özelliği kullanılarak SKU 'dan veya dakikalar içinde planlayabilir. Bu özellik, yayımcıların özel SKU veya planın sunulduğu müşterilerin listesini, teklifi yeniden belirlemeden veya yeniden yayınlamadan, kolayca ve kolayca güncelleştirmesine olanak sağlar.

## <a name="deploying-private-offers"></a>Özel teklifleri dağıtma

Özel Teklifler yalnızca [Azure Portal](https://azure.microsoft.com/features/azure-portal/) aracılığıyla bulunabilir ve [Azure Marketi](https://azuremarketplace.microsoft.com)aracılığıyla sunulmaz. Müşteriler, Azure portal oturum açtıktan sonra özel tekliflerinden erişmek için Market gezinti öğesini seçebilir. Özel teklifler de arama sonuçlarında görünür ve diğer teklifler gibi komut satırı ve Azure Resource Manager şablonları aracılığıyla dağıtılabilir.

![[Özel teklifler]](./media/marketplace-publishers-guide/private-offer.png)

Özel teklifler, arama sonuçlarında da görünür. Yalnızca "özel" rozet için göz atın.

>[!Note]
>Özel teklifler, bulut çözümü sağlayıcısı programı (CSP) satıcısı aracılığıyla oluşturulan aboneliklerle desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

Özel teklifleri kullanmaya başlamak için [özel SKU 'lar ve planlar](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) kılavuzundaki adımları izleyin.
