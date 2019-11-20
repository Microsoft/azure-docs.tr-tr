---
title: JavaScript ve sayfa düzeni sürümleri-Azure Active Directory B2C | Microsoft Docs
description: JavaScript 'ı etkinleştirme ve Azure Active Directory B2C sayfa düzeni sürümlerini kullanma hakkında bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0eb5c89387d8bdcf0e0b72c669c42f716ff5fbb3
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227094"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C içindeki JavaScript ve sayfa düzeni sürümleri

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C, Kullanıcı akışlarınızda ve özel ilkelerindeki Kullanıcı arabirimi öğeleri için HTML, CSS ve JavaScript içeren bir paketlenmiş içerik kümesi sağlar. Uygulamalarınız için JavaScript 'i etkinleştirmek üzere [özel ilkenize](active-directory-b2c-overview-custom.md) bir öğe eklemeniz veya Portal 'da Kullanıcı akışları için etkinleştirmeniz, bir sayfa düzeni seçmeniz ve isteklerinizin [b2clogin.com](b2clogin.md) kullanmanız gerekir.

[JavaScript](javascript-samples.md) istemci tarafı kodunu etkinleştirmek Istiyorsanız, JavaScript 'i temel alan öğelerin sabit olduğundan emin olmanız gerekir. Aksi takdirde, tüm değişiklikler Kullanıcı sayfalarınızda beklenmedik davranışa neden olabilir. Bu sorunları engellemek için, bir sayfa düzeninin kullanımını zorunlu kılabilir ve bir sayfa düzeni sürümü belirtebilirsiniz. Bunu yapmak, JavaScript 'i temel alan tüm içerik tanımlarının sabit olmasını sağlar. JavaScript 'i etkinleştirmeyi amaçlamadığınız halde, sayfalarınız için bir sayfa düzeni sürümü belirtebilirsiniz.

## <a name="user-flows"></a>Kullanıcı akışları

Kullanıcı akışı **özelliklerinde**JavaScript 'i etkinleştirebilirsiniz ve bu da sayfa düzeninin kullanımını zorunlu kılar. Daha sonra, sonraki bölümde açıklandığı gibi Kullanıcı akışının sayfa düzeni sürümünü ayarlayabilirsiniz.

![JavaScript ayarlarını etkinleştir seçeneği vurgulanmış şekilde Kullanıcı akışı özellikleri sayfası](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>Sayfa düzeni sürümü seçin

Kullanıcı akışınızın özelliklerinde JavaScript 'ı etkinleştirip etkinleştirmeyeceğinizi, Kullanıcı akış sayfalarınız için bir sayfa düzeni sürümü belirtebilirsiniz. Kullanıcı akışını açın ve **sayfa düzenleri**' ni seçin. **Düzen adı**altında, bir Kullanıcı akış sayfası seçin ve **sayfa düzeni sürümünü**seçin.

Farklı sayfa düzeni sürümleri hakkında daha fazla bilgi için bkz. [sürüm değişiklik günlüğü](page-layout.md#version-change-log).

![Portalda sayfa düzeni sürüm açılan listesini gösteren sayfa düzeni ayarları](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>Özel ilkeler

Özel ilkelerde JavaScript 'ı etkinleştirmek için **Scriptexecution** öğesini özel Ilke dosyanızdaki **RelyingParty** öğesine eklersiniz. Daha fazla bilgi için bkz. [Azure Active Directory B2C Için JavaScript örnekleri](javascript-samples.md).

Özel ilkeleriniz için JavaScript 'ı etkinleştirip etkinleştiremeyeceğinizi, sayfalarınız için bir sayfa düzeni sürümü belirtebilirsiniz. Sayfa düzeni belirtme hakkında daha fazla bilgi için bkz. [özel ilkeleri kullanarak Azure Active Directory B2C sayfa düzeni seçme](page-layout.md).

## <a name="next-steps"></a>Sonraki adımlar

Farklı sayfa düzeni sürümleri hakkında daha fazla bilgi için, [özel ilkeler kullanarak Azure Active Directory B2C sayfa düzeni seçin](page-layout.md#version-change-log)konusunun **sürüm değişiklik günlüğü** bölümüne bakın.

JavaScript örneklerindeki JavaScript kullanım örneklerini [Azure Active Directory B2C ' de kullanmak üzere](javascript-samples.md)bulabilirsiniz.
