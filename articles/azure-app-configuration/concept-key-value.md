---
title: Azure uygulama yapılandırması anahtar-değer deposu | Microsoft Docs
description: Yapılandırma verilerini Azure uygulama yapılandırmasında depolanma genel bakış
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: c7a7e7994ef5e16640f59efdc672f6793bc4f18d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706466"
---
# <a name="keys-and-values"></a>Anahtarları ve değerleri

Azure uygulama yapılandırması, yapılandırma verilerini anahtar-değer çiftleri olarak depolar. Geliştiriciler, alışık olduğunuz uygulama ayarları çeşitli türlerdeki temsil etmek için basit ancak esnek bir şekilde anahtar-değer çiftleridir.

## <a name="keys"></a>Anahtarlar

Anahtarlar, anahtar-değer çiftleri için ad olarak görev yapar ve depolamak ve ilgili değerleri almak için kullanılır. Bir karakter sınırlayıcı kullanarak bir hiyerarşik ad alanına anahtarları düzenlemek için yaygın bir uygulamadır `/` veya `:`. Uygulamanız için en uygun bir kuralı kullanın. Uygulama yapılandırma anahtarlarını bir bütün olarak değerlendirir. Tuşlarını kullanarak nasıl adlarıyla yapılandırılmış veya bunlar üzerinde herhangi bir kural zorlama ekleyeceğimi ayrıştırmak değil.

Yapılandırma verilerinin uygulama çerçeveleri içinde kullanım anahtar değerleri için belirli bir adlandırma düzeni belirleyebilir. Örneğin, Java'nın Spring Cloud çerçevesi tanımlar `Environment` içeren değişkenleri tarafından parametre haline getirilip için Spring uygulaması ayarları sağlamak kaynakları *uygulama adı* ve *profili*. Spring Bulutla ilgili yapılandırma verileri için anahtarlar genellikle bu iki öğenin bir sınırlayıcıyla ayrılmış başlayın.

Uygulama Yapılandırması içinde depolanan anahtarlar büyük/küçük harfe, unicode tabanlı dizelerdir. Anahtarları *app1* ve *App1* bir uygulama yapılandırma deposunda farklıdır. Bazı çerçeveleri yapılandırma anahtarları case-insensitively işlediğinden, bir uygulama yapılandırma ayarlarında kullandığınızda bunu aklınızda bulundurun. Örneğin, ASP.NET Core yapılandırma sistemi anahtarlarını büyük küçük harf duyarsız dize olarak değerlendirir. Bir ASP.NET Core uygulaması içinde uygulama yapılandırması sorguladığınızda öngörülemeyen davranışları önlemek için yalnızca büyük/küçük harfleri tarafından farklı tuşlarını kullanmayın.

Uygulama yapılandırması dışında için girilen anahtar adları, herhangi bir unicode karakter kullanabilirsiniz `*`, `,`, ve `\`. Bu karakterler ayrılmıştır. Ayrılmış bir karakter içerecek şekilde gerekiyorsa kullanarak atlatmak gerekir `\{Reserved Character}`. Bir anahtar-değer çifti 10.000 karakterlere toplam boyut sınırı yoktur. Bu sınır, anahtarı değeri, tüm karakterleri içerir ve ilişkili tüm isteğe bağlı öznitelikleri. Bu sınırı içinde anahtarları için birçok hiyerarşik düzeylerine sahip olabilir.

### <a name="design-key-namespaces"></a>Anahtar ad alanları tasarlama

Yapılandırma verileri için kullanılan anahtarları adlandırma için iki genel yaklaşım vardır: düz veya hiyerarşik. Bu yöntemler, bir uygulama kullanım açısından benzerdir, ancak hiyerarşik adlandırma çok sayıda avantaj sunar:

* Kolay okunur. Yerine bir uzun sıralı karakterler hiyerarşik bir anahtar adı sınırlayıcı bir tümcedeki alanları olarak çalışırlar. Sözcükler arasındaki doğal sonları de sağlanır.
* Yönetilmesi daha kolay. Anahtar adı hiyerarşi yapılandırma verilerinin mantıksal gruplar temsil eder.
* Kullanmayı daha kolay. Desen-eşleşmeleri hiyerarşik bir yapıda anahtarları ve yapılandırma verilerini yalnızca bir kısmını alır bir sorgu yazmak daha kolaydır. Ayrıca, birçok yeni programlama çerçevelerini hiyerarşik yapılandırma verileri, uygulamanızın yapabileceğiniz gibi belirli yapılandırma kümesi kullanmak için yerel destek vardır.

Uygulama yapılandırma anahtarlarını birçok yönden hiyerarşik olarak düzenleyebilirsiniz. Bu tür anahtarlar olarak düşünün [URI'ler](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Her bir kaynak hiyerarşik anahtardır *yolu* sınırlayıcılarla birleştirilen bir veya daha fazla bileşenden oluşur. Hangi, uygulama, programlama dili veya framework ihtiyaçlarına göre ayırıcı olarak kullanılacak hangi karakter seçin. Anahtarları farklı uygulama yapılandırması için birden çok sınırlayıcıya kullanın.

Bir hiyerarşiye, anahtar adlarını nasıl yapısı çeşitli örnekler şunlardır:

* Bileşen Hizmetleri tabanlı

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Dağıtım bölgelerine bağlı

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Etiket anahtarları

Uygulama yapılandırma anahtarı değerleri isteğe bağlı olarak bir etiket özniteliği olabilir. Etiketler, aynı anahtarla anahtar değerlerini ayırt etmek için kullanılır. Bir anahtar *app1* etiketlerle *A* ve *B* bir uygulama yapılandırma deposu içinde iki ayrı anahtar oluşturur. Varsayılan olarak, bir anahtar değer etiketi boş olduğundan veya `null`.

Etiket bir anahtar varyantlarını oluşturmak için kullanışlı bir yol sağlar. Bir ortak etiketleri aynı anahtar için birden çok ortama belirtmek için kullanılır:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Sürüm anahtar değerleri

Değişen uygulama yapılandırması sürümü anahtar değerlerini otomatik olarak değil. Etiket bir anahtar değeri birden çok sürümünü oluşturmak için bir yol kullanın. Örneğin, bir uygulama sürüm numarası girebilirsiniz veya anahtar değerlerini tanımlamak için bir Git işleme kimliği etiketlerde belirli yazılım derleme ile ilişkili.

Etiketler dışında herhangi bir unicode karakter kullanabilirsiniz `*`, `,`, ve `\`. Bu karakterler ayrılmıştır. Ayrılmış bir karakter içerecek şekilde kullanarak atlatmak gerekir `\{Reserved Character}`.

### <a name="query-key-values"></a>Sorgu anahtar değerleri

Her bir anahtar değeri anahtarıyla ayrıca olabilir bir etiket tarafından benzersiz şekilde tanımlanır `null`. Bir uygulama yapılandırma deposu anahtar değerleri için bir desen belirterek sorgulayın. Uygulama yapılandırma deposu düzeni ve ilgili değerleri ve öznitelikleri eşleşen tüm anahtar değerlerini döndürür. Aşağıdaki anahtar desenleri uygulama yapılandırması için REST API çağrılarını kullanır:

| Anahtar | |
|---|---|
| `key` Atlanırsa veya `key=*` | Tüm anahtarları eşleşir |
| `key=abc` | Anahtar adı eşleşmeleri **abc** tam olarak |
| `key=abc*` | Eşleşme ile başlayan adlar anahtar **abc** |
| `key=*abc` | Eşleşme ile biten anahtar **abc** |
| `key=*abc*` | Eşleşme anahtar içeren adları **abc** |
| `key=abc,xyz` | Eşleşme anahtar adları **abc** veya **xyz**, beş Csv'leri sınırlıdır |

Aşağıdaki etiketi desenleri de ekleyebilirsiniz:

| Etiket | |
|---|---|
| `label` Atlanırsa veya `label=*` | İçeren bir etiketi ile eşleşen `null` |
| `label=%00` | Eşleşme `null` etiketi |
| `label=1.0.0` | Eşleşen etiket **1.0.0** tam olarak |
| `label=1.0.*` | İle başlayan etiket ile eşleşen **1.0.** |
| `label=*.0.0` | Eşleşen ile bitiş etiketleri **.0.0** |
| `label=*.0.*` | Eşleşen etiketler içeren **.0.** |
| `label=%00,1.0.0` | Eşleşen etiketler `null` veya **1.0.0**, beş Csv'leri sınırlıdır |

## <a name="values"></a>Değerler

Anahtarlar için atanan değerler de unicode dizelerdir. Tüm unicode karakterleri değerlerini kullanabilirsiniz. Her bir değeri ile ilişkili bir isteğe bağlı kullanıcı tanımlı içerik türü yoktur. Bu öznitelik, örneğin bir kodlama düzeni, uygulamanızın düzgün bir şekilde işlemeye yardımcı olan bir değeri hakkında bilgi depolamak için kullanın.

Tüm anahtarları ve değerleri içeren bir uygulama yapılandırma deposu içinde depolanan yapılandırma verilerini beklerken ve aktarım sırasında şifrelenir. Uygulama yapılandırması, Azure Key Vault için bir yedek çözüm değildir. Uygulama gizli dizilerini içinde depolamayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Zaman içinde nokta anlık görüntü](./concept-point-time-snapshot.md)  
* [Özellik Yönetimi](./concept-feature-management.md)  
