---
title: Çok kiracılı uygulamaları etkinleştirme-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS için çok kiracılı Azure Active Directory uygulamalarını yapılandırma.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 6394d519b93b55358ef9d528f89978d5a3cf3007
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005912"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Azure dijital TWINS ile çok kiracılı uygulamaları etkinleştirme

Azure dijital TWINS üzerinde çalışan çözümler geliştiricileri, tek bir hizmet veya çözümle birden çok müşteriyi desteklemek istediğini fark edebilir. Aslında, *çok kiracılı* uygulamalar en sık kullanılan Azure dijital TWINS yapılandırmalarının arasındadır.

Bu belgede, Azure Digital TWINS uygulamasının birkaç Azure Active Directory kiracıyı ve müşteriyi destekleyecek şekilde nasıl yapılandırılacağı açıklanmaktadır.

## <a name="multitenancy"></a>Çoklu Müşteri mimarisi

Çoklu *kiracılı* kaynak, birden çok müşteriyi destekleyen tek bir sağlanmış örneğidir. Her müşterinin kendi bağımsız verileri ve ayrıcalıkları vardır. Her bir müşterinin deneyimi, uygulamanın "görünümü" farklı olacak şekilde birbirinden ayrı tutulur.

Çoklu Kiralama hakkında daha fazla bilgi edinmek için [Azure 'Da çok kiracılı uygulamalar](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)makalesini okuyun.

## <a name="problem-scenario"></a>Sorun senaryosu

Bu senaryoda, bir Azure dijital TWINS çözümü (**Geliştirici**) ve bu çözümü kullanan bir müşteriyi (**Müşteri**) oluşturan bir geliştirici düşünün:

- **Geliştirici** Azure Active Directory kiracısıyla bir Azure aboneliğine sahiptir.
- **Geliştirici** Azure aboneliğine bir Azure dijital TWINS örneği dağıtır. Azure Active Directory **geliştirici**Azure Active Directory kiracısında otomatik olarak bir hizmet sorumlusu oluşturdu.
- **Geliştirici**Azure Active Directory kiracı kapsamındaki kullanıcılar daha sonra Azure dijital TWINS hizmetinden [OAuth 2,0 belirteçleri](./security-authenticating-apis.md) alabilir.
- **Geliştirici** artık Azure Digital TWINS yönetim API 'leriyle doğrudan tümleştirilen bir mobil uygulama oluşturur.
- **Geliştirici** , **müşterilerin** mobil uygulamayı kullanmasına izin verir.
- **Müşterinin** **Geliştirici**uygulaması Içinde Azure DIGITAL TWINS yönetim API 'sini kullanma yetkisi olmalıdır.

Sorun:

- **Müşteri** **Geliştirici**uygulamasına oturum açtığında, uygulama **müşterinin**kullanıcılarına Azure Digital TWINS yönetim API 'lerinde kimlik doğrulaması yapmak için belirteçleri alamaz.
- Azure dijital TWINS 'in, **müşterinin**dizininde tanınmadığını belirten Azure Active Directory bir özel durum verilir.

## <a name="problem-solution"></a>Sorun çözümü

Önceki sorun senaryosunu çözmek için, **müşterinin**Azure Active Directory kiracısında bir Azure dijital TWINS hizmet sorumlusu oluşturmak için aşağıdaki eylemler gereklidir:

- **Müşterinin** Azure Active Directory kiracısı olan bir Azure aboneliği yoksa:

  - **Müşterinin**Azure Active Directory kiracı yöneticisi bir [Kullandıkça Öde Azure aboneliği](https://azure.microsoft.com/offers/ms-azr-0003p/)almalıdır.
  - Daha sonra, **müşterinin**Azure Active Directory kiracı yöneticisi [kiracının yeni abonelikle bağlantı](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)altına alınmalıdır.

- [Azure Portal](https://portal.azure.com), **müşterinin**Azure Active Directory kiracı yöneticisi aşağıdaki adımları gerçekleştirir:

  1. **Abonelikleri**açın.
  1. **Geliştirici**uygulamasında kullanılacak Azure Active Directory kiracıya sahip aboneliği seçin.

     [![Azure Active Directory abonelikleri](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. **Kaynak sağlayıcıları**' nı seçin.
  1. **Microsoft. ıotspaces**için arama yapın.
  1. **Kaydol**’u seçin.

     [![Azure Active Directory kaynak sağlayıcıları](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Sonraki adımlar

- Azure dijital TWINS ile Kullanıcı tanımlı işlevlerin nasıl kullanılacağı hakkında daha fazla bilgi edinmek için [Azure dijital TWINS Kullanıcı tanımlı işlevleri oluşturma](./how-to-user-defined-functions.md)makalesini okuyun.

- Rol atamaları ile uygulamayı daha güvenli hale getirmek için rol tabanlı erişim denetimini nasıl kullanacağınızı öğrenmek için [Azure dijital TWINS rol tabanlı erişim denetimi oluşturma ve yönetme](./security-create-manage-role-assignments.md)makalesini okuyun.
