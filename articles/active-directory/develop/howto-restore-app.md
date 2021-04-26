---
title: 'Nasıl yapılır: Microsoft Identity platform ile son silinen bir uygulamayı geri yükleme veya kaldırma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu nasıl yapılır, Microsoft Identity platformu ile kaydedilen son silinen uygulamayı geri yüklemeyi veya kalıcı olarak silmeyi öğrenirsiniz.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: dbe3e16bf56c5480fbe4aff8dad78bbbb5591f67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080595"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Son silinen bir uygulamayı Microsoft Identity platformu ile geri yükleme veya kaldırma
Uygulama kaydını sildikten sonra, uygulama 30 gün boyunca askıya alınmış durumda kalır. Bu 30 günlük bir pencere sırasında, uygulama kaydı tüm özellikleriyle birlikte geri yüklenebilir. 30 günlük pencere geçtikten sonra, uygulama kayıtları geri yüklenemez ve kalıcı silme işlemi otomatik olarak başlatılabilir.  Bu işlev yalnızca bir dizinle ilişkili uygulamalar için geçerlidir.  Bir kişisel Microsoft hesabı uygulamalar için kullanılamaz, bu geri yüklenemez.

Silinen uygulamalarınızı görüntüleyebilir, silinen bir uygulamayı geri yükleyebilir veya Azure portal Azure Active Directory (Azure AD) altındaki Uygulama kayıtları deneyimini kullanarak bir uygulamayı kalıcı olarak silebilirsiniz.

Ne ne de Microsoft müşteri destek 'in kalıcı olarak silinen bir uygulamayı veya 30 günden daha önce silinen bir uygulamayı geri yüklemesini unutmayın.

> [!IMPORTANT]
> Silinen uygulamalar portalı Kullanıcı Arabirimi özelliği [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="required-permissions"></a>Gerekli izinler
Uygulamaları kalıcı olarak silmek için aşağıdaki rollerden birine sahip olmanız gerekir.

- Genel yönetici
- Uygulama Yöneticisi
- Bulut uygulaması Yöneticisi
- Karma kimlik yöneticisi
- Uygulama sahibi

Uygulamaları geri yüklemek için aşağıdaki rollerden birine sahip olmanız gerekir.

- Genel yönetici
- Uygulama sahibi

### <a name="view-your-deleted-applications"></a>Silinen uygulamalarınızı görüntüleme
Tüm uygulamaları geçici olarak silinmiş bir durumda görebilirsiniz.  Yalnızca 30 günden az bir süre önce silinen uygulamalar geri yüklenebilir.

#### <a name="to-view-your-restorable-applications"></a>Geri yüklenebilen uygulamalarınızı görüntülemek için
1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Arama yapın ve **Azure Active Directory** seçin, **uygulama kayıtları**' i seçin ve ardından **silinen uygulamalar (Önizleme)** sekmesini seçin.

Uygulama listesini gözden geçirin. Yalnızca son 30 gün içinde silinen uygulamalar geri yüklenebilir. Uygulama kayıtları arama önizlemesi kullanılıyorsa, yalnızca bu uygulamaları görmek için ' silinen Tarih ' sütununa göre filtreleme yapabilirsiniz.

## <a name="restore-a-recently-deleted-application"></a>Son silinen uygulamayı geri yükleme

Kuruluştan bir uygulama kaydı silindiğinde, uygulama askıya alınma durumundadır ve yapılandırması korunur. Bir uygulama kaydını geri yüklediğinizde, kendi yapılandırması da geri yüklenir.  Ancak, **Kurumsal uygulamalarda** uygulamanın giriş kiracısı için kuruluşa özgü herhangi bir ayar varsa, bunlar geri yüklenmez.  

Bunun nedeni, kuruluşa özgü ayarların hizmet sorumlusu olarak adlandırılan ayrı bir nesne üzerinde depolanmasıdır.  Hizmet sorumlusu 'nda tutulan ayarlar, belirli bir kuruluş için izin yarışmaları ve Kullanıcı ve grup atamalarını içerir; uygulama geri yüklendiğinde bu yapılandırma geri yüklenmez. Daha fazla bilgi için bkz. [uygulama ve hizmet sorumlusu nesneleri](app-objects-and-service-principals.md). 


### <a name="to-restore-an-application"></a>Bir uygulamayı geri yüklemek için
1. **Silinen uygulamalar (Önizleme)** sekmesinde, 30 günden daha önce silinen uygulamalardan birini arayın ve seçin.
2. **Uygulama kaydını geri yükle**' yi seçin.

## <a name="permanently-delete-an-application"></a>Bir uygulamayı kalıcı olarak silme
Bir uygulamayı kuruluşunuzdan el ile kalıcı olarak silebilirsiniz. Kalıcı olarak silinen bir uygulama siz, başka bir yönetici veya Microsoft müşteri desteği tarafından geri yüklenemez.

### <a name="to-permanently-delete-an-application"></a>Bir uygulamayı kalıcı olarak silmek için

1. **Silinen uygulamalar (Önizleme)** sekmesinde, kullanılabilir uygulamalardan birini arayıp seçin.
2. **Kalıcı olarak sil**' i seçin.
3. Uyarı metnini okuyun ve **Evet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar
Uygulamanızı geri yükledikten veya kalıcı olarak sildikten sonra şunları yapabilirsiniz:

- [Bir uygulama ekleyin](quickstart-register-app.md).
- Microsoft Identity platformunda [uygulama ve hizmet sorumlusu nesneleri](app-objects-and-service-principals.md) hakkında daha fazla bilgi edinin.
