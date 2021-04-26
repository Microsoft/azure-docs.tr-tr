---
title: Azure Uygulama yapılandırması 'nı kullanarak özellik yönetimini anlama
description: Azure Uygulama yapılandırması 'nı kullanarak özellikleri açma ve kapatma
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: cfd676092bcaede58909a3ec1eefeabb4c80f86b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930237"
---
# <a name="feature-management-overview"></a>Özellik yönetimine genel bakış

Geleneksel olarak, yeni bir uygulama özelliği teslim etmek, uygulamanın kendisi için tamamen yeniden dağıtımı gerektirir. Bir özelliğin test edilmesi genellikle uygulamanın birden çok dağıtımını gerektirir.  Her dağıtım, özelliği değiştirebilir veya test için özelliği farklı müşterilere sunabilir.  

Özellik Yönetimi, özellik yayınını kod dağıtımından ayrışar ve isteğe bağlı olarak özellik kullanılabilirliğine hızlı değişiklikler sağlayan modern bir yazılım geliştirme uygulamasıdır. Özelliğin yaşam döngüsünü dinamik olarak yönetmek için *özellik bayrakları* ( *özellik* *geçişleri, özellik anahtarları* vb. olarak da bilinir) adlı bir teknik kullanır.

Özellik Yönetimi, geliştiricilerin aşağıdaki sorunları ele almanıza yardımcı olur:

* **Kod dalı yönetimi**: Geliştirme kapsamında olan yeni uygulama işlevselliğini kaydırmak için özellik bayraklarını kullanın. Bu işlevsellik varsayılan olarak "gizlidir". Tamamlanmamış olsa bile özelliği güvenle gönderebilirsiniz ve üretimde devre dışı kalır. *Koyu dağıtım* olarak adlandırılan bu yaklaşımı kullanarak her bir geliştirme döngüsünün sonunda tüm kodunuzu serbest bırakabilirsiniz. Belirli bir özellik için birden fazla döngü gerektiğinden, artık birden çok geliştirme döngüsünde kod dallarını korumanız gerekmez.
* **Üretimde test**: üretimde yeni işlevlere erken erişim sağlamak için özellik bayraklarını kullanın. Örneğin, erişimi takım üyelerine veya iç beta sınayıcılarına sınırlayabilirsiniz. Bu kullanıcılar bir test ortamında benzetimli veya kısmi bir deneyim yerine tam uygunlukta üretim deneyimine sahip olur.
* **Fışıklandırma**: son kullanıcılara artımlı olarak yeni işlevsellik aktarmak için özellik bayraklarını kullanın. Önce Kullanıcı popülasyonunun küçük bir yüzdesini hedefleyebilir ve zaman içinde kademeli olarak bu yüzdeyi artırabilirsiniz.
* **Anında sonlandırma anahtarı**: özellik bayrakları, yeni işlevselliği serbest bırakmak için devralınmış bir güvenlik ağı sağlar. Herhangi bir kodu yeniden dağıtmaya gerek kalmadan uygulama özelliklerini etkinleştirebilir ve devre dışı bırakabilirsiniz. Gerekirse, uygulamanızı yeniden derlemek ve yeniden dağıtmak zorunda kalmadan bir özelliği hızlıca devre dışı bırakabilirsiniz.
* **Seçmeli etkinleştirme**: kullanıcılarınızı segmentlere ayırmak ve her gruba belirli bir özellikler kümesi sunmak için özellik bayraklarını kullanın. Yalnızca belirli bir Web tarayıcısında çalışacak bir özelliğe sahip olabilirsiniz. Özellik bayrağını, yalnızca bu tarayıcı kullanıcılarının özelliği görebilmesi ve kullanabilmesi için tanımlayabilirsiniz. Bu yaklaşımda, daha sonra herhangi bir kod değişikliği yapmak zorunda kalmadan desteklenen tarayıcı listesini kolayca genişletebilirsiniz.

## <a name="basic-concepts"></a>Temel kavramlar

Özellik yönetimiyle ilgili birkaç yeni terim aşağıda verilmiştir:

* **Özellik bayrağı**: Özellik bayrağı, ikili durumu *Açık* veya *kapalı* olan bir değişkendir. Özellik bayrağının ayrıca ilişkili bir kod bloğu vardır. Özellik bayrağının durumu kod bloğunun çalıştırılıp çalıştırılmadığını tetikler.
* **Özellik Yöneticisi**: bir özellik Yöneticisi, bir uygulamadaki tüm özellik bayraklarının yaşam döngüsünü işleyen bir uygulama paketidir. Özellik Yöneticisi ayrıca Özellik bayraklarını önbelleğe alma ve durumlarını güncelleştirme gibi ek işlevler de sağlar.
* **Filtre**: filtre, bir özellik bayrağının durumunu değerlendirmek için bir kuraldır. Olası filtreler Kullanıcı gruplarını, cihaz veya tarayıcı türlerini, coğrafi konumları ve zaman pencerelerini içerir.

Özellik yönetiminin etkili bir şekilde uygulanması, Concert üzerinde çalışan en az iki bileşenden oluşur:

* Özellik bayraklarını kullanan bir uygulama.
* Özellik bayraklarını ve bunların geçerli durumlarını depolayan ayrı bir depo.

## <a name="using-feature-flags-in-your-code"></a>Kodunuzda Özellik bayraklarını kullanma

Bir uygulamada Özellik bayraklarını uygulamaya yönelik temel model basittir. Özellik bayrağı, kodunuzda koşullu bir ifadeyi denetleyen bir Boole durum değişkenidir:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Statik olarak değerini belirleyebilirsiniz `featureFlag` .

```csharp
bool featureFlag = true;
```

Belirli kurallara göre bayrağın durumunu değerlendirebilirsiniz:

```csharp
bool featureFlag = isBetaUser();
```

Her iki durum için de uygulama davranışını ayarlamak üzere koşullu öğesini genişletebilirsiniz:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Özellik bayrağı deposu

Özellik bayraklarını etkin bir şekilde kullanmak için, bir uygulamada kullanılan tüm özellik bayraklarını externalize gerekir. Bu, uygulamanın kendisini değiştirmeden ve yeniden dağıtmaya gerek kalmadan Özellik bayrağı durumlarını değiştirmenize olanak sağlar.

Azure Uygulama yapılandırması, özellik bayrakları için merkezi bir depo sağlar. Farklı özellik bayraklarını tanımlamak ve durumlarını hızla ve güvenle işlemek için kullanabilirsiniz. Bundan sonra, bu özellik bayraklarını uygulamanızdan kolayca erişmek için çeşitli programlama dilleri çerçeveleri için uygulama yapılandırma kitaplıklarını kullanabilirsiniz.

[ASP.NET Core uygulamasında Özellik bayraklarını kullanma](./use-feature-flags-dotnet-core.md) , .NET Core uygulama yapılandırma sağlayıcısı ve özellik yönetimi kitaplıklarının, ASP.NET Web uygulamanıza yönelik özellik bayraklarını uygulamak için birlikte nasıl kullanıldığını gösterir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [ASP.NET Core Web uygulamasına özellik bayrakları ekleme](./quickstart-feature-flag-aspnet-core.md)  
