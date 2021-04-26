---
title: Parametreleri kullanarak bir hizmetin bağlantı noktası numarasını belirtme
description: Service Fabric içindeki bir uygulama için bağlantı noktasını belirtmek üzere parametrelerin nasıl kullanılacağını gösterir
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ba2fb459dc9c981ad168aca4d0edf969650ccf48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576715"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Service Fabric parametreleri kullanarak bir hizmetin bağlantı noktası numarasını belirtme

Bu makalede, Visual Studio kullanarak Service Fabric parametreler kullanarak bir hizmetin bağlantı noktası numarasını nasıl belirtebileceğiniz gösterilmektedir.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Parametreleri kullanarak bir hizmetin bağlantı noktası numarasını belirtme yordamı

Bu örnekte, asp.net Core Web API 'niz için bağlantı noktası numarasını bir parametre kullanarak ayarlarsınız.

1. Visual Studio 'Yu açın ve yeni bir Service Fabric uygulaması oluşturun.
1. Durum bilgisi olmayan ASP.NET Core şablonunu seçin.
1. Web API 'sini seçin.
1. ServiceManifest.xml dosyasını açın.
1. Hizmetiniz için belirtilen uç noktanın adını aklınızda edin. `ServiceEndpoint` varsayılan değerdir.
1. ApplicationManifest.xml dosyasını açın
1. `ServiceManifestImport`Öğesinde, `RessourceOverrides` ServiceManifest.xml dosyanızdaki uç noktaya yönelik başvuruya sahip yeni bir öğe ekleyin.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. `Endpoint`Öğesinde, artık bir parametreyi kullanarak herhangi bir özniteliği geçersiz kılabilirsiniz. Bu örnekte, öğesini belirtip `Port` köşeli parantezleri kullanarak bir parametre adına ayarlarsınız. Örneğin, `[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Hala ApplicationManifest.xml dosyasında, öğesinde parametresini belirtirsiniz `Parameters`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. Ve bir `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. ApplicationParameters klasörünü ve `Cloud.xml` dosyasını açın
1. Uzak bir kümeye yayımlarken kullanılacak farklı bir bağlantı noktası belirtmek için, bu dosyaya bağlantı noktası numarası ile parametresini ekleyin.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Uygulamanızı Visual Studio 'dan Cloud.xml yayımlama profilini kullanarak yayımlarken, hizmetiniz 80 numaralı bağlantı noktasını kullanacak şekilde yapılandırılmıştır. Uygulamayı MyWebAPI_PortNumber parametresi belirtmeden dağıtırsanız, hizmet 8080 numaralı bağlantı noktasını kullanır.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede ele alınan temel kavramlardan bazıları hakkında daha fazla bilgi edinmek için bkz. [birden çok ortam için uygulamaları yönetme makaleleri](service-fabric-manage-multiple-environment-app-configuration.md).

Visual Studio 'da kullanılabilen diğer uygulama yönetimi özellikleri hakkında daha fazla bilgi için bkz. [Visual Studio 'da Service Fabric uygulamalarınızı yönetme](service-fabric-manage-application-in-visual-studio.md).
