---
title: Geliştirici Portalında topluluk pencere öğelerini kullanma
titleSuffix: Azure API Management
description: API Management geliştirici portalının topluluk pencere öğeleri ve bunları kodunuzda ekleme ve kullanma hakkında bilgi edinin.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: de6160aa2e556297287ae9e9ecd58a93e54d863f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741827"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>Geliştirici Portalında topluluk pencere öğelerini kullanma

Tüm geliştiriciler topluluk tarafından katkıda bulunulan pencere öğelerini `/community/widgets/` API Management geliştirici portalı [GitHub deposunun](https://github.com/Azure/api-management-developer-portal)klasörüne yerleştirir. Her biri geliştirici portalı ekibi tarafından kabul edilmiştir. Pencere öğelerini, portalınızın [Şirket içinde barındırılan sürümüne](developer-portal-self-host.md) ekleme göre kullanabilirsiniz. Geliştirici portalının yönetilen sürümü şu anda topluluk pencere öğelerini desteklememektedir.

> [!NOTE]
> Geliştirici portalı ekibi, katkıda bulunulan pencere öğelerini ve bunların bağımlılıklarını ayrıntılı bir şekilde inceler. Ancak, takım pencere öğelerinin yüklenmesi için güvenli olduğunu garanti edemez. Topluluk tarafından katkıda bulunulan bir pencere öğesi kullanmaya karar verirken kendi kararlarınızı kullanın. Önleyici ölçülerimiz hakkında bilgi edinmek için [pencere öğesi katkısı kılavuzumuzu](developer-portal-widget-contribution-guidelines.md#contribution-guidelines) inceleyin.

## <a name="inject-and-use-external-widgets"></a>Dış pencere öğeleri ekleme ve kullanma

1. Geliştirici portalının en son sürümü için [yerel bir ortam](developer-portal-self-host.md#step-1-set-up-local-environment) ayarlayın.

1. Dizindeki pencere öğesinin klasörüne gidin `/community/widgets` . Dosyadaki pencere öğesinin açıklamasını okuyun `readme.md` .

1. Pencere öğesini portalın modüllerine Kaydet:

    1. `src/apim.design.module.ts` -Tasarım zamanı bağımlılıklarını kaydeden bir modül.
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` -Yayımlama zamanı bağımlılıklarını kaydeden bir modül.
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` -çalışma zamanı bağımlılıklarını kaydeden bir modül.
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. Pencere öğesinin bir dosyaya sahip olup olmadığını denetleyin `npm_dependencies` .

1. Bu durumda, komutları dosyadan kopyalayın ve deponun en üst dizininde çalıştırın.

    Bunun yapılması, pencere öğesinin bağımlılıklarını yükler.

1. `npm start` öğesini çalıştırın.

Pencere öğesini pencere öğesi seçicisindeki **topluluk** kategorisinde görebilirsiniz.

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="Pencere öğesi seçicinin ekran görüntüsü":::


## <a name="next-steps"></a>Sonraki adımlar


Geliştirici portalı hakkında daha fazla bilgi edinin:

- [Azure API Management geliştirici portalına genel bakış](api-management-howto-developer-portal.md)

- [Pencere öğelerinin katkıda bulunma](developer-portal-widget-contribution-guidelines.md)
