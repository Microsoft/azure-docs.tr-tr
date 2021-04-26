---
title: Self Servis kaydolma akışlarında API bağlayıcıları hakkında-Azure AD
description: Web API 'Lerini kullanarak Self Servis kaydolma Kullanıcı akışlarınızı özelleştirmek ve genişletmek için Azure Active Directory (Azure AD) API bağlayıcılarını kullanın.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a5563ff1f57f6b3684834a2488fc0665ac5eddd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102610051"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>Self Servis kaydolma 'yı özelleştirmek ve genişletmek için API bağlayıcılarını kullanma 

## <a name="overview"></a>Genel Bakış 
Geliştirici veya BT Yöneticisi olarak, oturum açma deneyimini özelleştirmek ve dış sistemlerle bütünleştirmek için [self servis kaydolma Kullanıcı](self-service-sign-up-overview.md) akışlarınızı Web API 'leriyle BÜTÜNLEŞTIRMEK üzere API bağlayıcılarını kullanabilirsiniz. Örneğin, API bağlayıcılarıyla şunları yapabilirsiniz:

- [**Özel bir onay iş akışıyla tümleştirin**](self-service-sign-up-add-approvals.md). Hesap oluşturmayı yönetmek ve kısıtlamak için özel bir onay sistemine bağlanın.
- [**Kimlik doğrulama gerçekleştirin**](code-samples-self-service-sign-up.md#identity-verification). Hesap oluşturma kararlarında ek bir güvenlik düzeyi eklemek için bir kimlik doğrulama hizmeti kullanın.
- **Kullanıcı giriş verilerini doğrulayın**. Hatalı biçimlendirilmiş veya geçersiz kullanıcı verilerine karşı doğrulayın. Örneğin, bir dış veri deposundaki veya izin verilen değerler listesindeki mevcut verilere karşı Kullanıcı tarafından belirtilen verileri doğrulayabilirsiniz. Geçersiz ise, kullanıcıdan geçerli veri sağlamasını isteyebilir veya kullanıcının kaydolma akışına devam etmesini engelleyebilirsiniz.
- **Kullanıcı özniteliklerinin üzerine yaz**. Kullanıcıdan toplanan bir özniteliğe yeniden biçimlendirin veya bir değer atayın. Örneğin, bir Kullanıcı ilk adı küçük harfle veya tüm büyük harflerde girerse, adı yalnızca ilk harfi büyük harfle biçimlendirebilirsiniz. 
- **Özel iş mantığını çalıştırın**. Anında iletme bildirimleri göndermek, kurumsal veritabanlarını güncelleştirmek, izinleri yönetmek, veritabanlarını denetlemek ve başka özel eylemler gerçekleştirmek için bulut sistemlerinizdeki aşağı akış olaylarını tetikleyebilirsiniz.

API Bağlayıcısı, API çağrısı için HTTP uç noktası URL 'sini ve kimlik doğrulamasını tanımlayarak API uç noktasını çağırmak için gereken bilgileri Azure Active Directory sağlar. Bir API bağlayıcısını yapılandırdıktan sonra, Kullanıcı akışındaki belirli bir adım için etkinleştirebilirsiniz. Kullanıcı kaydolma akışındaki bu adıma ulaştığında, API Bağlayıcısı çağrılır ve API 'nize bir HTTP POST isteği olarak çalışır ve bir JSON gövdesinde anahtar-değer çiftleri olarak Kullanıcı bilgilerini ("talepler") gönderir. API yanıtı, Kullanıcı akışının yürütülmesini etkileyebilir. Örneğin, API yanıtı bir kullanıcının kaydolmasını engelleyebilir, kullanıcıdan bilgi yeniden girmelerini veya kullanıcı özniteliklerinin üzerine yazmasına ya da bu özniteliklerin üzerine yazılmasına neden olabilir.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Bir Kullanıcı akışında bir API bağlayıcısını etkinleştirebileceğiniz durumlar

Bir API bağlayıcısını etkinleştirebileceğiniz bir Kullanıcı akışında iki konum vardır:

- Bir kimlik sağlayıcısıyla oturum açtıktan sonra
- Kullanıcı oluşturmadan önce

> [!IMPORTANT]
> Her iki durumda da, API bağlayıcıları Kullanıcı **kaydı** sırasında çağrılır, oturum açma desteklenmez.

### <a name="after-signing-in-with-an-identity-provider"></a>Bir kimlik sağlayıcısıyla oturum açtıktan sonra

Kaydolma işleminde bu adımdaki bir API Bağlayıcısı, Kullanıcı kimlik sağlayıcısıyla kimlik doğrulamasından sonra (Google, Facebook, & Azure AD) hemen çağrılır. Bu adım, kullanıcı özniteliklerinin toplanması için kullanıcıya sunulan form olan ***öznitelik koleksiyonu sayfasından*** önce gelir. Bir kullanıcı yerel hesapla kayıt alıyorsa bu adım çağrılmaz. Bu adımda etkinleştirebileceğiniz API Bağlayıcısı senaryolarının örnekleri aşağıda verilmiştir:

- Kullanıcının mevcut bir sistemde talepleri aramak için verdiği e-postayı veya federal kimliği kullanın. Mevcut sistemden bu talepleri döndürün, öznitelik koleksiyonu sayfasını önceden doldurabilir ve belirtece döndürmek için kullanılabilir hale getirin.
- Sosyal kimlik temelinde bir izin verilenler veya engellenenler listesi uygulayın.

### <a name="before-creating-the-user"></a>Kullanıcı oluşturmadan önce

Kaydolma işleminde bu adımda bulunan bir API Bağlayıcısı, varsa öznitelik toplama sayfasından sonra çağrılır. Bu adım, Kullanıcı hesabı oluşturulmadan önce her zaman çağrılır. Kayıt sırasında bu noktada etkinleştirebileceğiniz senaryoların örnekleri aşağıda verilmiştir:

- Kullanıcı giriş verilerini doğrulayın ve kullanıcıdan veri göndermesini isteyin.
- Kullanıcı tarafından girilen verileri temel alan bir kullanıcının kaydolup engellenme.
- Kimlik doğrulama gerçekleştirin.
- Kullanıcı hakkındaki mevcut veriler için dış sistemleri, uygulama belirtecine döndürmek veya Azure AD 'de depolamak üzere sorgulayın.

## <a name="next-steps"></a>Sonraki adımlar
- [Bir Kullanıcı AKıŞıNA API Bağlayıcısı ekleme](self-service-sign-up-add-api-connector.md) hakkında bilgi edinin
- [Self servis kaydolma 'ya özel bir onay sistemi eklemeyi](self-service-sign-up-add-approvals.md) öğrenin