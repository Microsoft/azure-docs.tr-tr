---
title: Geliştirici portalı için pencere öğeleri katkısı
titleSuffix: Azure API Management
description: API Management geliştirici portalı deposuna bir pencere öğesi katkısında izlenecek önerilen yönergeler hakkında bilgi edinin.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c4d3ed2aeaac57f721d23d7c7aa1c70ef14e4294
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741876"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>API Management geliştirici portalına pencere öğeleri katkısı

API Management geliştirici portalı [GitHub deposuna](https://github.com/Azure/api-management-developer-portal)bir pencere öğesi katkıda bulunmak istiyorsanız, bu üç adımlı işlemi izleyin:

1. Depoyu çatalla.

1. Pencere öğesini uygulayın.

1. Pencere öğesini resmi depoya dahil etmek için bir çekme isteği açın.

Pencere öğesi deponun lisansını miras alacak. Portalın şirket içinde barındırılan sürümünde [tercih edilen yükleme](developer-portal-use-community-widgets.md) için kullanılabilir olacaktır. Geliştirici portalı ekibi, Portal 'ın yönetilen sürümüne de dahil etme kararı verebilir.

Kendi pencere öğesini geliştirme hakkında bir örnek için [pencere öğesi uygulama](developer-portal-implement-widgets.md) öğreticisine bakın.

## <a name="contribution-guidelines"></a>Katkı yönergeleri

Bu kılavuz, müşterilerimizin ve ziyaretçilerinin portallarının güvenliğini ve gizliliğini sağlamak için tasarlanmıştır. Katkılarınızın kabul edildiğinden emin olmak için aşağıdaki yönergeleri izleyin:

1. Pencere öğesini `community/widgets/<your-widget-name>` klasörüne yerleştirin.

1. Pencere öğesi adının adı, sözcüklerin ayrılarak küçük harf ve alfasayısal olmalıdır. Örneğin, `my-new-widget`.

1. Klasör yayımlanmış bir portalda pencere öğesinin ekran görüntüsünü içermelidir.

1. Klasör, `readme.md` dosyanın şablonunu izleyen bir dosya içermelidir `/scaffolds/widget/readme.md` .

1. Klasör, `npm_dependencies` pencere öğesinin bağımlılıklarını yüklemek veya yönetmek için NPM komutlarıyla bir dosya içerebilir.

    Her bağımlılığın sürümünü açıkça belirtin. Örnek:  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    Pencere öğesi için en az bağımlılık gerekir. Her bağımlılık gözden geçirenler tarafından dikkatle denetlenir. Özellikle, pencere öğesinin Çekirdek mantığı, pencere öğesinin klasöründe açık kaynak olmalıdır. Bir NPM paketine sarmayın.

1. Pencere öğesinin klasörü dışındaki dosyalarda yapılan değişikliklere pencere öğesi katkısı kapsamında izin verilmez. Bu, dosyasını içerir, ancak bunlarla sınırlı değildir `/package.json` .

1. Ekleme İzleme betikleri veya müşteri tarafından yazılan verilerin özel hizmetlere gönderilmesi izin verilmiyor.

    > [!NOTE]
    > Yalnızca arabirim aracılığıyla müşteri tarafından yazılan verileri toplayabilirsiniz `Logger` .

## <a name="next-steps"></a>Sonraki adımlar

- Katılımlar hakkında daha fazla bilgi için bkz. API Management geliştirici portalı [GitHub deposu](https://github.com/Azure/api-management-developer-portal/).

- Kendi pencere öğelerinizi geliştirmeyi ve adım adım adımları öğrenmek için bkz. [pencere öğeleri uygulama](developer-portal-implement-widgets.md) .

- Topluluk tarafından katkıda bulunulan pencere öğelerini nasıl kullanacağınızı öğrenmek için bkz. [topluluk pencere öğelerini kullanma](developer-portal-use-community-widgets.md) .