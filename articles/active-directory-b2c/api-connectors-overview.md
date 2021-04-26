---
title: Azure AD B2C 'deki API bağlayıcıları hakkında
description: Web API 'Lerini kullanarak kaydolma Kullanıcı akışlarınızı özelleştirmek ve genişletmek için Azure Active Directory (Azure AD) API bağlayıcılarını kullanın.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: d7a3301cb6ec10e75979d0e1fdfad52c7103a2aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611530"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>Kaydolma Kullanıcı akışlarını özelleştirmek ve genişletmek için API bağlayıcılarını kullanma

> [!IMPORTANT]
> Kaydolma için API bağlayıcıları, Azure AD B2C genel önizleme özelliğidir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Genel Bakış 
Geliştirici veya BT Yöneticisi olarak, oturum açma deneyimini özelleştirmek ve dış sistemlerle bütünleştirmek için, Web API 'Leriyle kaydolma Kullanıcı akışlarınızı bütünleştirmek üzere API bağlayıcıları kullanabilirsiniz. Örneğin, API bağlayıcılarıyla şunları yapabilirsiniz:

- **Kullanıcı giriş verilerini doğrulayın**. Hatalı biçimlendirilmiş veya geçersiz kullanıcı verilerine karşı doğrulayın. Örneğin, bir dış veri deposundaki veya izin verilen değerler listesindeki mevcut verilere karşı Kullanıcı tarafından belirtilen verileri doğrulayabilirsiniz. Geçersiz ise, kullanıcıdan geçerli veri sağlamasını isteyebilir veya kullanıcının kaydolma akışına devam etmesini engelleyebilirsiniz.
- **Özel bir onay iş akışıyla tümleştirin**. Hesap oluşturmayı yönetmek ve kısıtlamak için özel bir onay sistemine bağlanın.
- **Kullanıcı özniteliklerinin üzerine yaz**. Kullanıcıdan toplanan bir özniteliğe yeniden biçimlendirin veya bir değer atayın. Örneğin, bir Kullanıcı ilk adı küçük harfle veya tüm büyük harflerde girerse, adı yalnızca ilk harfi büyük harfle biçimlendirebilirsiniz. 
- **Kimlik doğrulama gerçekleştirin**. Hesap oluşturma kararlarında ek bir güvenlik düzeyi eklemek için bir kimlik doğrulama hizmeti kullanın.
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
- [Bir Kullanıcı AKıŞıNA API Bağlayıcısı ekleme](add-api-connector.md) hakkında bilgi edinin
- [Örneklerimizi](code-samples.md#api-connectors)kullanmaya başlayın.
<!-- - Learn how to [add a custom approval system to self-service sign-up](add-approvals.md) -->