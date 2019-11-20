---
title: Azure Service Fabric yapılandırma dosyalarını parametreleştirin | Microsoft Docs
description: Service Fabric yapılandırma dosyalarını nasıl parametreleyeceğinizi öğrenin.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: dad497978de7187177998524db3b2f2ee448c717
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464777"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Service Fabric 'de yapılandırma dosyalarını Parametreleştirme

Bu makalede, Service Fabric bir yapılandırma dosyasının nasıl parametreleştiriyapılacağı gösterilir.  Birden çok ortam için uygulamaları yönetmeye yönelik temel kavramlardan daha önce alışık değilseniz, [birden çok ortam için uygulamaları yönetme](service-fabric-manage-multiple-environment-app-configuration.md)makalesini okuyun.

## <a name="procedure-for-parameterizing-configuration-files"></a>Yapılandırma dosyalarını parametreize etme yordamı

Bu örnekte, uygulama dağıtımınızdaki parametreleri kullanarak bir yapılandırma değerini geçersiz kılarsınız.

1. Hizmet projenizde hizmetim > \packageroot\config\settings.xml dosyasını açın.  *\<*
1. Aşağıdaki XML 'i ekleyerek bir yapılandırma parametresi adı ve değeri (örneğin, önbellek boyutu 25 ' e eşit) ayarlayın:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Dosyayı kaydedin ve kapatın.
1. MyApplication > \applicationpackageroot\applicationmanifest.xml dosyasını açın.  *\<*
1. ApplicationManifest. xml dosyasında, `Parameters` öğesinde bir parametre ve varsayılan değer bildirin.  Parametre adının hizmetin adını içermesi önerilir (örneğin, "hizmetim").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. ApplicationManifest. xml dosyasının `ConfigOverrides` `ConfigOverride` bölümünde,yapılandırmapaketine,bölümüneveparametresinebaşvurarakbir`ServiceManifestImport` ve öğesi ekleyin.

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> Bir ConfigOverride eklediğinizde Service Fabric her zaman uygulama bildiriminde belirtilen uygulama parametrelerini veya varsayılan değeri seçer.
>
>

## <a name="next-steps"></a>Sonraki adımlar
Visual Studio 'da kullanılabilen diğer uygulama yönetimi özellikleri hakkında daha fazla bilgi için bkz. [Visual Studio 'da Service Fabric uygulamalarınızı yönetme](service-fabric-manage-application-in-visual-studio.md).
