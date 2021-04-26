---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076726"
---
Sonraki adım, Unity için Azure uzamsal bağlayıcı paketlerinin indirileceği bir adımdır. 

> [!WARNING]
> ASA SDK 2.7.0 desteklenen en düşük sürümdür. Unity 2020 kullanıyorsanız, ASA SDK 2.9.0 desteklenen en düşük sürümdür.

Unity 'de Azure uzamsal bağlayıcılarını kullanmak için, desteklemeyi planladığınız her platform (Android/iOS/HoloLens) için çekirdek paketin yanı sıra platforma özgü bir paket de indirmeniz gerekir.

| Platform | Paket adı                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | com. Microsoft. Azure. uzamsal-Tutturucuların sdk.core@ <version_number> |
| Android  | com. Microsoft. Azure. uzamsal-Tutturucuların sdk.android@ <version_number> |
| iOS      | com. Microsoft. Azure. uzamsal-Tutturucuların sdk.ios@ <version_number>     |
| HoloLens | com. Microsoft. Azure. uzamsal-Tutturucuların sdk.windows@ <version_number> |

# <a name="download-with-web-browser"></a>[Web tarayıcısı ile indir](#tab/unity-package-web-ui)

Unity için Azure uzamsal bağlayıcı çekirdek paketini [buradan](https://aka.ms/aoa/unity-sdk/package)bulun. İstediğiniz sürümü seçin ve **İndir** düğmesini kullanarak paketi indirin. Desteklemeyi planladığınız her platformun paketini indirmek için bu adımı tekrarlayın.

# <a name="download-with-npm"></a>[NPM ile indir](#tab/unity-package-npm)

Bu adım <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> 'nin yüklü ve kullanılabilir olmasını gerektirir.

Şu komutu çalıştırarak, `<version_number>` geçerli klasöre indirmek Istediğiniz Azure uzamsal çıpası sürümü ile değiştirin:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Azure uzamsal bağlayıcı paketinin kullanılabilir sürümlerini listelemek için aşağıdakileri çalıştırın:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Azure uzamsal bağlayıcı çekirdek paketi, komutu çalıştırdığınız klasöre indirilir. Desteklemeyi planladığınız her platformun paketini indirmek için bu adımı tekrarlayın.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Karma Gerçeklik Özellik Aracı (Beta) ile Install](#tab/unity-package-mixed-reality-feature-tool)

Sonraki adımla devam edin. Daha sonraki bir adımda <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">karma gerçeklik özelliği aracını</a> kullanacaksınız.

---