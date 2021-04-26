---
title: Birden çok ortam için uygulamaları yönetme
description: Azure Service Fabric uygulamaları, bir makineden binlerce makineye kadar olan kümeler üzerinde çalıştırılabilir. Bazı durumlarda, bu değişen ortamlar için uygulamanızı farklı şekilde yapılandırmak isteyeceksiniz. Bu makalede, ortam başına farklı uygulama parametrelerinin nasıl tanımlanacağı ele alınmaktadır.
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: c907540c03788ab5f4087a96e301f18ab7ced4ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98787986"
---
# <a name="manage-applications-for-multiple-environments"></a>Birden çok ortam için uygulamaları yönetme

Azure Service Fabric kümeleri, bunlardan çok binlerce makineye kadar her yerde küme oluşturmanıza imkan tanır. Çoğu durumda, uygulamanızı birden çok küme yapılandırmasında dağıtmak zorunda kalmamaktır: yerel geliştirme kümeniz, paylaşılan bir geliştirme kümesi ve üretim kümeniz. Bu kümelerin hepsi, kodunuzun içinde çalışacağı farklı ortamlar olarak değerlendirilir. Uygulama İkilileri bu geniş bir sponun genelinde değişiklik yapılmadan çalıştırılabilir, ancak genellikle uygulamayı farklı şekilde yapılandırmak istersiniz.

İki basit örnek düşünün:
  - hizmetiniz tanımlı bir bağlantı noktasını dinler, ancak bu bağlantı noktasının ortamlar genelinde farklı olması gerekir
  - ortamlar genelinde bir veritabanı için farklı bağlama kimlik bilgileri sağlamanız gerekir

## <a name="specifying-configuration"></a>Yapılandırma belirtme

Sağladığınız yapılandırma iki kategoride ayrılabilir:

- Hizmetlerinizin nasıl çalıştığı için geçerli olan yapılandırma
  - Örneğin, bir uç nokta veya bir hizmetin örnek sayısı için bağlantı noktası numarası
  - Bu yapılandırma, uygulama veya hizmet bildirim dosyasında belirtilir
- Uygulama kodunuz için geçerli olan yapılandırma
  - Örneğin, bir veritabanı için bağlama bilgileri
  - Bu yapılandırma, yapılandırma dosyaları ya da ortam değişkenleri aracılığıyla sağlanıyor

> [!NOTE]
> Uygulama ve hizmet bildirimi dosyasındaki özniteliklerin hepsi, parametreleri desteklemez.
> Bu durumlarda, dizeleri dağıtım iş akışınızın bir parçası olarak değiştirme ' yi bilmeniz gerekir. Azure DevOps 'da, belirteçleri Değiştir gibi bir uzantı kullanabilirsiniz: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens veya Jenkins içinde değerleri değiştirmek için bir betik görevi çalıştırabilirsiniz.
>

## <a name="specifying-parameters-during-application-creation"></a>Uygulama oluşturma sırasında parametreleri belirtme

Service Fabric içinde adlandırılmış bir uygulama örnekleri oluştururken, parametreleri geçirme seçeneğiniz vardır. Bunu yapmanın yolu, uygulama örneğini oluşturma şeklinize bağlıdır.

  - PowerShell 'de cmdlet, [`New-ServiceFabricApplication`](/powershell/module/servicefabric/new-servicefabricapplication) Uygulama parametrelerini bir Hashtable olarak alır.
  - Sfctl kullanarak, [`sfctl application create`](./service-fabric-sfctl-application.md#sfctl-application-create) komut PARAMETRELERI JSON dizesi olarak alır. İnstall.sh betiği sfctl kullanır.
  - Visual Studio, uygulama projesindeki Parameters klasöründe parametre dosyaları kümesi sağlar. Bu parametre dosyaları, Visual Studio 'dan yayımlarken Azure DevOps Services veya Azure DevOps Server kullanılarak kullanılır. Visual Studio 'da parametre dosyaları Deploy-FabricApplication.ps1 betiğe geçirilir.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelerde, burada açıklanan kavramların bazılarını nasıl kullanabileceğiniz gösterilmektedir:

- [Service Fabric içindeki hizmetler için ortam değişkenlerini belirtme](service-fabric-how-to-specify-environment-variables.md)
- [Service Fabric parametreleri kullanarak bir hizmetin bağlantı noktası numarasını belirtme](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Yapılandırma dosyalarını Parametreleştirme](service-fabric-how-to-parameterize-configuration-files.md)

- [Ortam değişkeni başvurusu](service-fabric-environment-variables-reference.md)
