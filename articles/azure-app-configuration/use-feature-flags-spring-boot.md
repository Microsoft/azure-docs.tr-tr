---
title: Spring Boot App 'te Özellik bayraklarını kullanma öğreticisi-Azure Uygulama yapılandırması | Microsoft Docs
description: Bu öğreticide, Spring Boot uygulamalarında Özellik bayraklarını nasıl uygulayacağınızı öğreneceksiniz.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: 8c66e2995462701f7ddaefc3a2623c02fee883ef
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687174"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Öğretici: bir Spring Boot uygulamasında Özellik bayraklarını kullanma

Spring Boot Core özelliği yönetim kitaplıkları, bir Spring Boot uygulamasında Özellik bayraklarını uygulamaya yönelik destek sağlar. Bu kitaplıklar, kodunuza bildirimli olarak özellik bayrakları eklemenize olanak tanır.

Özellik Yönetimi kitaplıkları Ayrıca arka planda özellik bayrakları yaşam döngülerini yönetir. Örneğin, kitaplıklar yenileme ve önbellek bayrak durumlarını veya bir istek çağrısı sırasında bayrak durumunun sabit olmasını garanti eder. Ayrıca, Spring Boot kitaplığı, MVC denetleyici eylemleri, rotalar ve ara yazılım gibi tümleştirmeler sunar.

[Yay önyükleme uygulaması hızlı başlangıç 'a özellik ekleme bayrakları](./quickstart-feature-flag-spring-boot.md) , bir Spring Boot uygulamasına özellik bayrakları eklemenin birkaç yolunu gösterir. Bu öğreticide bu yöntemler daha ayrıntılı olarak açıklanmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Özellik kullanılabilirliğini denetlemek için uygulamanızın önemli bölümlerine özellik bayrakları ekleyin.
> * Özellik bayraklarını yönetmek için kullandığınızda uygulama yapılandırmasıyla tümleştirin.

## <a name="set-up-feature-management"></a>Özellik yönetimini ayarlama

Spring Boot Feature Manager `FeatureManager`, Framework 'ün yerel yapılandırma sisteminden Özellik bayraklarını alır. Sonuç olarak, yerel *önyükleme. yıml* dosyası veya ortam değişkenleri dahil olmak üzere Spring Boot tarafından desteklenen herhangi bir yapılandırma kaynağını kullanarak uygulamanızın Özellik bayraklarını tanımlayabilirsiniz. `FeatureManager` bağımlılığı ekleme işlemini kullanır. Özellik yönetimi hizmetlerini standart kuralları kullanarak kaydedebilirsiniz:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Özellik bayraklarını uygulamanın dışında tutmanızı ve ayrı olarak yönetmenizi öneririz. Bunun yapılması, bayrak durumlarını dilediğiniz zaman değiştirmenize ve bu değişikliklerin uygulamada hemen etkili olmasını sağlar. Uygulama yapılandırması, özel bir Portal Kullanıcı arabirimi aracılığıyla tüm özellik Bayraklarınızı düzenlemek ve denetlemek için merkezi bir yer sağlar. Uygulama yapılandırması, bayrakları uygulamanıza doğrudan Spring Boot istemci kitaplıkları aracılığıyla da sunar.

Spring Boot uygulamanızı uygulama yapılandırmasına bağlanmanın en kolay yolu yapılandırma sağlayıcısı üzerinden yapılır:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
    <version>1.1.0.M4</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Özellik bayrağı bildirimi

Her özellik bayrağının iki bölümü vardır: bir özelliğin durumunun *Açık* olup olmadığını değerlendirmek için kullanılan bir veya daha fazla filtrenin adı ve listesi (yani, değeri `True` olduğunda). Bir filtre, bir özelliğin açılması gerektiğinde kullanım durumunu tanımlar.

Bir özellik bayrağının birden çok filtresi olduğunda, filtrenin etkin olması gerektiğini belirlerken filtre listesine bir düzen eklenir. Bu noktada, özellik bayrağı *Açık*ve kalan filtre sonuçları atlanır. Filtre yoksa, özelliğin etkinleştirilmesi gerektiğini gösteriyorsa, özellik bayrağı *kapalıdır*.

Özellik Yöneticisi, *uygulama. yıml* 'yi özellik bayrakları için yapılandırma kaynağı olarak destekler. Aşağıdaki örnekte, bir YAML dosyasında özellik bayraklarının nasıl ayarlanacağı gösterilmektedir:

```yml
feature-management:
  featureSet:
    features:
      FeatureA: true
      FeatureB: false
      FeatureC:
        EnabledFor:
          -
            name: Percentage
            parameters:
              value: 50
```

Kurala göre, bu IML belgesinin `feature-management` bölümü Özellik bayrağı ayarları için kullanılır. Önceki örnekte, `EnabledFor` özelliğinde tanımlanan filtreleriyle birlikte üç özellik bayrağı gösterilmektedir:

* `FeatureA` *'* dır.
* `FeatureB` *kapalı*.
* `FeatureC`, `Parameters` özelliği ile `Percentage` adlı bir filtre belirtir. `Percentage` yapılandırılabilir bir filtredir. Bu örnekte, `Percentage` `FeatureC` bayrağının *Açık*olması için% 50 oranında bir olasılık belirtir.

## <a name="feature-flag-checks"></a>Özellik bayrağı denetimleri

Özellik yönetiminin temel stili, ilk olarak bir özellik bayrağının *Açık*olarak ayarlanmış olup olmadığını denetme. Bu durumda, Özellik Yöneticisi özelliğin içerdiği eylemleri çalıştırır. Örnek:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabled("FeatureA"))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Bağımlılık ekleme

Spring Boot 'da, `FeatureManager` ' a bağımlılık ekleme aracılığıyla erişebilirsiniz:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Denetleyici eylemleri

MVC denetleyicileri ' nde, belirli bir eylemin etkin olup olmadığını denetlemek için `@FeatureGate` özniteliğini kullanırsınız. Aşağıdaki `Index` eylemi, çalıştırılabilmesi *için önce @no__t* -1 gerektirir:

```java
@GetMapping("/")
@FeatureGate(feature = "FeatureA")
public String index(Model model) {
    ...
}
```

Denetim özelliği bayrağı *kapalı*olduğu IÇIN bir MVC denetleyicisi veya eylemi engellendiğinde, kayıtlı bir `IDisabledFeaturesHandler` arabirimi çağırılır. Varsayılan `IDisabledFeaturesHandler` arabirimi, yanıt gövdesi olmayan istemciye 404 durum kodu döndürür.

## <a name="mvc-filters"></a>MVC filtreleri

MVC filtrelerini bir özellik bayrağının durumuna göre etkinleştirilecek şekilde ayarlayabilirsiniz. Aşağıdaki kod `FeatureFlagFilter` adlı bir MVC filtresi ekler. Bu filtre, yalnızca `FeatureA` etkinse MVC işlem hattı içinde tetiklenir.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabled("FeatureA")) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Yollar

Yolları yeniden yönlendirmek için özellik bayraklarını kullanabilirsiniz. Aşağıdaki kod, `FeatureA` ' dan bir kullanıcıyı yeniden yönlendirecektir:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "FeatureA", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, `spring-cloud-azure-feature-management-web` kitaplıklarını kullanarak Spring Boot uygulamanızda Özellik bayraklarını nasıl uygulayacağınızı öğrendiniz. Spring Boot ve App Configuration içindeki özellik yönetimi desteği hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Yay önyükleme özelliği bayrağı örnek kodu](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Özellik bayraklarını yönetme](./manage-feature-flags.md)
