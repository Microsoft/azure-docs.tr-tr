---
title: Şirket içi kodlayıcı iş ortağı olun
description: Bu makalede, şirket içi canlı akış kodlayıcılarınızın nasıl doğrulandığı açıklanır.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/31/2020
ms.topic: how-to
ms.service: media-services
ms.openlocfilehash: a8b40206a73672b1d6bbb0ecded26b9f974be6b9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277598"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Şirket içi canlı akış kodlayıcılarınızı doğrulama

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services şirket içi kodlayıcı iş ortağı olarak, Media Services kurumsal müşterilere kodlayıcınızı önererek ürününüzü yükseltir. Şirket içi kodlayıcı iş ortağı olmak için, Media Services ile şirket içi kodlayıcılarınızın uyumluluğunu doğrulamanız gerekir. Bunu yapmak için aşağıdaki doğrulamaları doldurun.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Doğrudan geçiş canlı olay doğrulaması

1. Media Services hesabınızda, **akış uç noktasının** çalıştığından emin olun. 
2. **Geçiş** canlı olayını oluşturun ve başlatın. <br/> Daha fazla bilgi için bkz. [canlı olay durumları ve faturalandırma](live-event-states-billing-concept.md).
3. Alma URL 'sini alın ve şirket içi kodlayıcıyı, Media Services çoklu bit hızlı canlı akış göndermek için URL 'YI kullanacak şekilde yapılandırın.
4. Kodlayıcının girişinin gerçekten alındığını doğrulamak için önizleme URL 'sini alın ve kullanın.
5. Yeni bir **varlık** nesnesi oluşturun.
6. Canlı bir **çıktı** oluşturun ve oluşturduğunuz varlık adını kullanın.
7. Yerleşik **akış ilkesi** türleriyle bir **akış Bulucu** oluşturun.
8. Kullanılacak URL 'Leri geri almak için **akış bulucunun** yollarını listeleyin.
9. Akış yapmak istediğiniz **akış uç noktası** için ana bilgisayar adını alın.
10. Tam URL 'yi almak için adım 8 ' deki URL 'YI adım 9 ' da ana bilgisayar adıyla birleştirin.
11. Gerçek zamanlı kodlarınızı yaklaşık 10 dakika boyunca çalıştırın.
12. Canlı etkinliği durdurun. 
13. Kayıttan yürütmenin tüm kalite düzeylerinde görünür bir görünmüyor olmamasını sağlamak için [Azure Media Player](https://aka.ms/azuremediaplayer) gibi bir oyuncu kullanın. Ya da canlı oturum sırasında önizleme URL 'SI aracılığıyla izleyin ve doğrulayın.
14. Varlık KIMLIĞINI, Canlı Arşiv için yayımlanan akış URL 'sini ve canlı kodlayıcıınızdan kullanılan ayarları ve sürümü kaydedin.
15. Her bir örneği oluşturduktan sonra canlı olay durumunu sıfırlayın.
16. Kodlarınızın desteklediği tüm yapılandırmalarda 5 ile 15 arasındaki adımları yineleyin (reklam sinyali, açıklamalı alt yazılar veya farklı kodlama hızları olmadan).

## <a name="live-encoding-live-event-verification"></a>Canlı kodlama canlı olay doğrulaması

1. Media Services hesabınızda, **akış uç noktasının** çalıştığından emin olun. 
2. **Canlı kodlama** canlı olayını oluşturun ve başlatın. <br/> Daha fazla bilgi için bkz. [canlı olay durumları ve faturalandırma](live-event-states-billing-concept.md).
3. İçeri ve Media Services için tek bit hızlı canlı bir akış göndermek üzere kodlayıcıyı yapılandırın ve kodlayıcınızı yapılandırın.
4. Kodlayıcının girişinin gerçekten alındığını doğrulamak için önizleme URL 'sini alın ve kullanın.
5. Yeni bir **varlık** nesnesi oluşturun.
6. Canlı bir **çıktı** oluşturun ve oluşturduğunuz varlık adını kullanın.
7. Yerleşik **akış ilkesi** türleriyle bir **akış Bulucu** oluşturun.
8. Kullanılacak URL 'Leri geri almak için **akış bulucunun** yollarını listeleyin.
9. Akış yapmak istediğiniz **akış uç noktası** için ana bilgisayar adını alın.
10. Tam URL 'yi almak için adım 8 ' deki URL 'YI adım 9 ' da ana bilgisayar adıyla birleştirin.
11. Gerçek zamanlı kodlarınızı yaklaşık 10 dakika boyunca çalıştırın.
12. Canlı etkinliği durdurun.
13. Kayıttan yürütmenin tüm kalite seviyeleri için görünür olmadığından emin olmak üzere arşivlenmiş varlığı izlemek için [Azure Media Player](https://aka.ms/azuremediaplayer) gibi bir oyuncu kullanın. Ya da canlı oturum sırasında önizleme URL 'SI aracılığıyla izleyin ve doğrulayın.
14. Varlık KIMLIĞINI, Canlı Arşiv için yayımlanan akış URL 'sini ve canlı kodlayıcıınızdan kullanılan ayarları ve sürümü kaydedin.
15. Her bir örneği oluşturduktan sonra canlı olay durumunu sıfırlayın.
16. Kodlarınızın desteklediği tüm yapılandırmalarda 5 ile 15 arasındaki adımları yineleyin (reklam sinyali, açıklamalı alt yazılar veya farklı kodlama hızları olmadan).

## <a name="longevity-verification"></a>Longeçekimi doğrulaması

Adım 11 hariç [geçiş canlı olay doğrulaması ile](#pass-through-live-event-verification) aynı adımları izleyin. <br/>10 dakika yerine, canlı kodlayıcıyı bir hafta veya daha uzun bir süre içinde çalıştırın. Kayıttan yürütmenin görünür olmadığından emin olmak için zaman zaman (veya arşivlenmiş bir varlık) ile ilgili canlı akışı izlemek için [Azure Media Player](https://aka.ms/azuremediaplayer) gibi bir oyuncu kullanın.

## <a name="email-your-recorded-settings"></a>Kayıtlı ayarlarınıza e-posta gönderin

Son olarak, kayıtlı ayarlarınızı ve Canlı Arşiv parametrelerinizi, amshelp@microsoft.com tüm otomatik doğrulama denetimlerinin başarılı olduğunu belirten bir bildirim olarak Azure Media Services için e-posta ile gönderin. Ayrıca, herhangi bir izleme için iletişim bilgilerinizi ekleyin. Azure Media Services ekibine bu işlemle ilgili tüm soruları ile başvurabilirsiniz.

## <a name="see-also"></a>Ayrıca bkz.

[Sınanan şirket içi kodlayıcılar](encode-recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Sonraki adımlar

[Media Services v3 ile canlı akış](stream-live-streaming-concept.md)
