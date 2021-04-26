---
title: Azure Uygulama teklifiniz için teknik ayrıntılar ekleme
description: Iş Ortağı Merkezi 'nde Azure Uygulama teklifiniz için teknik ayrıntıları nasıl ekleyeceğinizi öğrenin.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370454"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Azure Uygulama teklifiniz için teknik ayrıntılar ekleme

Bu makalede, Microsoft Commercial Market 'in çözümünüze bağlanmasına yardımcı olacak teknik ayrıntıların nasıl girilmesi açıklanmaktadır. Bu bağlantı, teklifi edinmeyi ve yönetmeyi tercih ettiklerinde müşteri için teklifinizi sağlamamızı sağlar.

Bu bölümü yalnızca teklifiniz, [Market tarifeli faturalandırma API 'lerini](partner-center-portal/marketplace-metering-service-apis.md) kullanarak ölçüm olaylarını yayan bir yönetilen uygulama içeriyorsa ve bır Azure AD güvenlik belirteciyle kimlik doğrulaması yapılacak bir hizmete sahip olduğunda doldurun. Daha fazla bilgi için bkz. farklı kimlik doğrulama seçeneklerinde [Market ölçüm hizmeti kimlik doğrulama stratejileri](partner-center-portal/marketplace-metering-service-authentication.md) .

## <a name="technical-configuration-offer-level"></a>Teknik yapılandırma (teklif düzeyi)

**Teknik yapılandırma** sekmesi size yalnızca [Market tarifeli faturalandırma API 'lerini](partner-center-portal/marketplace-metering-service-apis.md)kullanarak ölçüm olaylarını gösteren bir yönetilen uygulama oluşturacağınız durumlarda geçerlidir. Bu durumda, aşağıdaki adımları uygulayın. Aksi takdirde, [sonraki adımlara](#next-steps)gidin. 

Bu alanlar hakkında daha fazla bilgi için bkz. [ticari Market için bir Azure Uygulama teklifi planlayın](plan-azure-application-offer.md#technical-configuration).

1. **Teknik yapılandırma** sekmesinde, iki hizmetimiz arasındaki bağlantıyı doğrulamak için kullanılan **Azure Active Directory kiracı kimliği** ve **Azure Active Directory Uygulama kimliği** , kimliği doğrulanmış bir iletişimin arkasında yer alır.

1. Sonraki sekmeye geçmeden önce **Taslağı kaydet** ' i seçin: plana genel bakış.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Uygulama teklifiniz için planlar oluşturma](create-new-azure-apps-offer-plans.md)
