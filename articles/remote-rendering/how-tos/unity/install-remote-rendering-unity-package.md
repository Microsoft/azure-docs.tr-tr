---
title: Unity için Remote Rendering paketini yükleme
description: Unity için uzaktan Işleme istemci dll 'Lerinin nasıl yükleneceğini açıklar.
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: bfb383a7079e98db1db1f9b5077558c187bcea96
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047737"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Unity için Remote Rendering paketini yükleme

Azure uzaktan Işleme, tümleştirmeyi Unity 'ye kapsüllemek için bir Unity paketi kullanır.
Bu paket tüm C# API 'SININ yanı sıra Unity ile Azure uzaktan Işlemeyi kullanmak için gereken tüm eklenti ikililerini içerir.
Aşağıdaki Unity 'nin paketlere yönelik adlandırma şeması, pakete **com. Microsoft. Azure. Remote-Rendering** olarak adlandırılır.

Unity paketini yüklemek için aşağıdaki seçeneklerden birini belirleyebilirsiniz.

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Karma Gerçeklik Özellik aracını kullanarak uzaktan Işleme paketini yükler

[Karma gerçeklik özelliği aracı](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) ([Indirme](https://aka.ms/mrfeaturetool)), karma gerçeklik özellik paketlerini Unity projelerine bütünleştirmek için kullanılan bir araçtır. Paket, [ARR örnekleri deposunun](https://github.com/Azure/azure-remote-rendering)bir parçası değildir ve Unity 'nin iç paket kayıt defterinde kullanılamaz.

Paketi bir projeye eklemek için şunları yapmanız gerekir:
1. [Karma Gerçeklik Özellik aracını indirin](https://aka.ms/mrfeaturetool)
1. Aracının nasıl kullanılacağına ilişkin [tüm yönergeleri](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) izleyin.
1. **Özellikleri bul** sayfasında, **uzaktan işleme paketinin Microsoft Azure** onay kutusunu işaretleyin ve projenize eklemek istediğiniz paketin sürümünü seçin

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

Yerel paketinizi güncelleştirmek için karma gerçeklik Özellik aracından daha yeni bir sürümü seçip yüklemesi yeterlidir. Paketin güncelleştirilmesi, bazen konsol hatalarına neden olabilir. Bu durumda projeyi kapatıp yeniden açmayı deneyin.

## <a name="install-remote-rendering-package-manually"></a>Uzaktan Işleme paketini el ile yükler

Uzaktan Işleme paketini el ile yüklemek için şunları yapmanız gerekir:

1. Paketi konumundaki karma gerçeklik paketleri NPM akışından indirin `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` .
    * Paketi geçerli klasöre indirmek için [NPM](https://www.npmjs.com/get-npm) 'yi kullanabilir ve aşağıdaki komutu çalıştırabilirsiniz.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * Ya da `Scripts/DownloadUnityPackages.ps1` [Azure-Remote-Rendering GitHub deposundan](https://github.com/Azure/azure-remote-rendering)konumundaki PowerShell betiğini kullanabilirsiniz.
        * `Scripts/unity_sample_dependencies.json`İçeriğini düzenleyin
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * Paketi belirtilen hedef dizine indirmek için PowerShell 'de aşağıdaki komutu çalıştırın.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. [İndirilen paketi](https://docs.unity3d.com/Manual/upm-ui-tarball.html) Unity 'Nin Paket Yöneticisi Ile birlikte yükler.

Yerel paketinizi güncelleştirmek için yalnızca kullandığınız ilgili komutu yeniden çalıştırın ve paketi yeniden içeri aktarın. Paketin güncelleştirilmesi, bazen konsol hatalarına neden olabilir. Bu durumda projeyi kapatıp yeniden açmayı deneyin.

## <a name="unity-render-pipelines"></a>Unity işleme işlem hatları

Uzaktan Işleme hem hem de ile birlikte çalışarak **:::no-loc text="Universal render pipeline":::** **:::no-loc text="Standard render pipeline":::** . Performans nedenleriyle, evrensel işleme işlem hattı önerilir.

Öğesini kullanmak için **:::no-loc text="Universal render pipeline":::** , paketinin Unity 'ye yüklenmesi gerekir. Bu, Unity 'nin **Paket Yöneticisi** Kullanıcı arabiriminde (paket adı **Universal RP**, sürüm 7.3.1 veya üzeri) ya da `Packages/manifest.json` [Unity proje kurulumu öğreticisinde](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)açıklandığı gibi dosya aracılığıyla yapılabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Unity oyun nesneleri ve bileşenleri](objects-components.md)
* [Öğretici: uzak modelleri görüntüleme](../../tutorials/unity/view-remote-models/view-remote-models.md)