---
title: Hizmetler için ortam değişkenlerini belirtme
description: Service Fabric 'de uygulamalar için ortam değişkenlerinin nasıl kullanılacağını gösterir
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 610e44ac98b8b8a2ce5a91fdbcfda145ae36a94b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576749"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Service Fabric içindeki hizmetler için ortam değişkenlerini belirtme

Bu makalede, Service Fabric bir hizmet veya kapsayıcı için ortam değişkenlerini nasıl belirtebileceğiniz gösterilmektedir.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Hizmetler için ortam değişkenlerini belirtme yordamı

Bu örnekte, bir kapsayıcı için bir ortam değişkeni ayarlarsınız. Makalede, zaten bir uygulama ve hizmet bildiriminiz olduğunu varsaymaktadır.

1. ServiceManifest.xml dosyasını açın.
2. `CodePackage`Öğesinde, `EnvironmentVariables` `EnvironmentVariable` her ortam değişkeni için yeni bir öğesi ve bir öğesi ekleyin.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Ortam değişkenleri uygulama bildiriminde geçersiz kılınabilir.

3. Uygulama bildiriminde ortam değişkenlerini geçersiz kılmak için `EnvironmentOverrides` öğesini kullanın.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Docker Compose kullanarak ortam değişkenlerini dinamik olarak belirtme

Service Fabric, [dağıtım için Docker Compose kullanma](service-fabric-docker-compose.md#supported-compose-directives)yeteneğini destekler. Oluşturma dosyaları, kabuktan ortam değişkenlerini kaynak olarak içerebilir. Bu davranış, istenen ortam değerlerini dinamik olarak değiştirmek için kullanılabilir:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede ele alınan temel kavramlardan bazıları hakkında daha fazla bilgi edinmek için bkz. [birden çok ortam için uygulamaları yönetme makaleleri](service-fabric-manage-multiple-environment-app-configuration.md).

Visual Studio 'da kullanılabilen diğer uygulama yönetimi özellikleri hakkında daha fazla bilgi için bkz. [Visual Studio 'da Service Fabric uygulamalarınızı yönetme](service-fabric-manage-application-in-visual-studio.md).
