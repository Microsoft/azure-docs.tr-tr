---
title: Unity için Azure uzamsal bağlayıcıları 'nı yükler
description: Azure uzamsal bağlayıcıları kullanmak için Unity projesi yapılandırma
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/30/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: fe9160f22754c62888b2a61ce9751f596842604e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076725"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Unity projesinde Azure uzamsal bağlayıcılarını yapılandırma

Bu kılavuzda, Unity projenizde Azure uzamsal bağlayıcı SDK 'sını kullanmaya nasıl başlacağınız gösterilmektedir.

## <a name="project-requirements"></a>Proje gereksinimleri

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Proje yapılandırma

Unity projenize Azure uzamsal bağlayıcı SDK 'sını eklemeden önce, Unity paket Yöneticisi aracılığıyla [gerekli](#project-requirements) paketleri yüklediğinizden emin olun.

### <a name="download-asa-packages"></a>ASA paketlerini indirme
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>ASA paketlerini içeri aktarma
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Yalnızca Android: mainTemplate. Gradle dosyasını yapılandırma

1.   >  **Proje ayarları**  >  **oynatıcıyı** Düzenle ' ye gidin.
2. **Oynatıcı ayarları** Için **Inspector panelinde** **Android** simgesini seçin.
3. **Derleme** bölümünün altında özel **ana Gradle şablonu** onay kutusunu işaretleyerek, üzerinde özel bir Gradle şablonu oluşturun `Assets\Plugins\Android\mainTemplate.gradle` .
4. `mainTemplate.gradle`Dosyanızı bir metin düzenleyicisinde açın.
5. `dependencies`Bölümünde aşağıdaki bağımlılıkları yapıştırın:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Her şey yapıldığında, `dependencies` bölüm şuna benzer şekilde görünmelidir:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Nasıl yapılır: Unity 'de bağlayıcı oluşturma ve bulma](./create-locate-anchors-unity.md)
