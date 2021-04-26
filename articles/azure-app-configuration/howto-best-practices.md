---
title: Azure Uygulama yapılandırması en iyi uygulamalar | Microsoft Docs
description: Azure Uygulama yapılandırması kullanırken en iyi yöntemleri öğrenin. Kapsanan konular arasında anahtar gruplandırmaları, anahtar-değer kompozisyonları, uygulama yapılandırması önyüklemesi ve daha fazlası bulunur.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 33661eafee6b180819b18d9a9a980eff1e2aeceb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371558"
---
# <a name="azure-app-configuration-best-practices"></a>Azure Uygulama yapılandırması en iyi uygulamaları

Bu makalede, Azure Uygulama yapılandırması 'nı kullanırken yaygın desenler ve en iyi uygulamalar ele alınmaktadır.

## <a name="key-groupings"></a>Anahtar gruplandırmaları

Uygulama yapılandırması anahtarları düzenlemek için iki seçenek sunar:

* Anahtar ön ekleri
* Etiketler

Anahtarlarınızı gruplandırmak için bir veya iki seçenekten birini kullanabilirsiniz.

*Anahtar ön ekleri* anahtarların başlangıç parçalarından oluşur. Adlarında aynı öneki kullanarak bir anahtar kümesini mantıksal olarak gruplandırabilirsiniz. Ön ekler, bir ad alanı oluşturmak için, gibi bir sınırlayıcı tarafından bağlı bir URL yoluna benzer birden çok bileşen içerebilir `/` . Bu tür hiyerarşiler, tek bir uygulama yapılandırma deposundaki birçok uygulama, bileşen hizmeti ve ortam için anahtar depolarken yararlı olur.

Dikkat edilmesi gereken önemli bir şey, anahtarların ilgili ayarların değerlerini almak için uygulama kodunuzun başvurduğu şeydir. Anahtarların değişmemesi veya herhangi bir zamanda kodunuzun değiştirilmesini değiştirmeniz gerekir.

*Etiketler* anahtarlar üzerindeki bir özniteliktir. Anahtar çeşitleri oluşturmak için kullanılır. Örneğin, bir anahtarın birden çok sürümüne etiketler atayabilirsiniz. Bir sürüm yineleme, ortam ya da başka bir bağlamsal bilgi olabilir. Uygulamanız, başka bir etiket belirterek tamamen farklı bir anahtar değerleri kümesi isteyebilir. Sonuç olarak, tüm anahtar başvuruları kodunuzda değişmeden kalır.

## <a name="key-value-compositions"></a>Anahtar-değer kompozisyonları

Uygulama yapılandırması, onunla depolanan tüm anahtarları bağımsız varlıklar olarak değerlendirir. Uygulama yapılandırması, anahtarlar arasında herhangi bir ilişki çıkarması veya hiyerarşiye göre anahtar değerleri devralması denenmez. Ancak uygulama kodunuzda uygun yapılandırma yığınlayarak bağlanmış Etiketler kullanarak birden çok anahtar kümesini toplayabilirsiniz.

Bir örneğe göz atalım. Değeri geliştirme ortamına göre değişebilen **Asset1** adlı bir ayarınız olduğunu varsayalım. Boş bir etiketi ve "geliştirme" adlı bir etiketi içeren "Asset1" adlı bir anahtar oluşturun. İlk etikette, **Asset1** için varsayılan değeri yerleştirmelisiniz ve "geliştirme" için belirli bir değeri ikinciden yerleştirebilirsiniz.

Kodunuzda, önce herhangi bir etiket olmadan anahtar değerlerini alır ve ardından "geliştirme" etiketiyle ikinci kez aynı anahtar değerleri kümesini alırsınız. İkinci seferinde değerleri aldığınızda, anahtarların önceki değerlerinin üzerine yazılır. .NET Core yapılandırma sistemi, her birinin üzerine birden çok yapılandırma verisi kümesini "yığmanızı" sağlar. Birden çok küme içinde bir anahtar varsa, onu içeren son küme kullanılır. .NET Core gibi modern programlama çerçevesiyle, uygulama yapılandırmasına erişmek için yerel bir yapılandırma sağlayıcısı kullanıyorsanız bu yığınlama özelliğini ücretsiz olarak alırsınız. Aşağıdaki kod parçacığı, .NET Core uygulamasında nasıl yığın uygulayabileceğinizi göstermektedir:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Farklı ortamlara yönelik farklı yapılandırmaların etkinleştirilmesi için etiketleri kullanın](./howto-labels-aspnet-core.md) , tüm bir örnek sağlar.

## <a name="app-configuration-bootstrap"></a>Uygulama yapılandırması önyükleme

Bir uygulama yapılandırma deposuna erişmek için, Azure portal kullanılabilir olan bağlantı dizesini kullanabilirsiniz. Bağlantı dizeleri kimlik bilgisi bilgilerini içerdiğinden, bu değerler gizli olarak değerlendirilir. Bu gizli dizileri Azure Key Vault depolanması gerekir ve kodunuzun bunları almak için Key Vault kimlik doğrulaması gerekir.

Daha iyi bir seçenek, Azure Active Directory Yönetilen kimlikler özelliğini kullanmaktır. Yönetilen kimlikler ile, uygulama yapılandırma deponuza erişimi önyüklemek için yalnızca uygulama yapılandırma uç noktası URL 'sine ihtiyacınız vardır. URL 'YI uygulama kodunuza ekleyebilirsiniz (örneğin, *appsettings.js* dosyadaki). Ayrıntılar için bkz. [Azure tarafından yönetilen kimlikler Ile tümleştirme](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>Uygulama yapılandırmasına uygulama veya işlev erişimi

Aşağıdaki yöntemlerden birini kullanarak Web uygulamaları veya işlevleri için uygulama yapılandırmasına erişim sağlayabilirsiniz:

* Azure portal aracılığıyla, App Service uygulama ayarlarındaki uygulama yapılandırma deponuza bağlantı dizesini girin.
* Bağlantı dizesini Key Vault, uygulama yapılandırma deponuza depolayın ve [App Service başvurun](../app-service/app-service-key-vault-references.md).
* Uygulama yapılandırma deposuna erişmek için Azure yönetilen kimliklerini kullanın. Daha fazla bilgi için bkz. [Azure yönetilen kimliklerle tümleştirme](howto-integrate-azure-managed-service-identity.md).
* Uygulama yapılandırmasından App Service olarak yapılandırma gönderin. Uygulama yapılandırması, doğrudan App Service içine veri gönderen bir dışarı aktarma işlevi (Azure portal ve Azure CLı) sağlar. Bu yöntemle, uygulama kodunu her zaman değiştirmeniz gerekmez.

## <a name="reduce-requests-made-to-app-configuration"></a>Uygulama yapılandırmasına yapılan istekleri azaltma

Uygulama yapılandırmasına yönelik aşırı istek, azaltma veya fazla kullanım ücretlerine neden olabilir. Yapılan isteklerin sayısını azaltmak için:

* Özellikle yapılandırma değerlerinizin sık değişmeme durumunda yenileme zaman aşımını artırın. [ `SetCacheExpiration` Yöntemini](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)kullanarak yeni bir yenileme zaman aşımı belirtin.

* Tek tek tuşları izlemek yerine tek bir *Sentinel anahtarı* izleyin. Yalnızca Sentinel anahtarı değişirse tüm yapılandırmaları yenileyin. Bir örnek için bkz. [ASP.NET Core uygulamasında dinamik yapılandırmayı kullanma](enable-dynamic-configuration-aspnet-core.md) .

* Değişikliklerin sürekli olarak yoklanması yerine, yapılandırma değiştiğinde bildirim almak için Azure Event Grid kullanın. Daha fazla bilgi için bkz. [Azure uygulama yapılandırma olaylarını bir Web uç noktasına yönlendirme](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>Yapılandırma verilerini uygulama yapılandırmasına aktarma

Uygulama yapılandırması, Azure portal veya CLı kullanarak geçerli yapılandırma dosyalarınızda yapılandırma ayarlarınızı toplu olarak [içeri aktarma](./howto-import-export-data.md) seçeneğini sunar. Aynı seçenekleri, örneğin ilgili mağazalar arasında uygulama yapılandırmasından değerleri dışarı aktarmak için de kullanabilirsiniz. GitHub deponuz ile devam eden bir eşitleme ayarlamak isterseniz, [GitHub eylemmizi](./concept-github-action.md) kullanarak, uygulama yapılandırmasının avantajlarını alırken mevcut kaynak denetimi uygulamalarınızı kullanmaya devam edebilirsiniz.

## <a name="multi-region-deployment-in-app-configuration"></a>Uygulama yapılandırmasında çok bölgeli dağıtım

Uygulama yapılandırması bölgesel bir hizmettir. Bölge başına farklı yapılandırmalara sahip uygulamalar için, bu yapılandırmaların tek bir örnekte depolanması tek bir hata noktası oluşturabilir. Birden çok bölgede bölge başına bir uygulama yapılandırma örneği dağıtmak daha iyi bir seçenek olabilir. Bölgesel olağanüstü durum kurtarma, performans ve güvenlik siloing yardımcı olabilir. Bölgeye göre yapılandırma ayrıca gecikmeyi artırır ve daraltma, örnek başına olduğundan, ayrılmış azaltma kotaları kullanır. Olağanüstü durum kurtarma hafifletme uygulamak için [birden çok yapılandırma deposu](./concept-disaster-recovery.md)kullanabilirsiniz. 

## <a name="client-applications-in-app-configuration"></a>Uygulama yapılandırmasındaki istemci uygulamaları 

Uygulama yapılandırmasına yönelik aşırı istek, azaltma veya fazla kullanım ücretlerine neden olabilir. Uygulamalar, gönderdikleri isteklerin sayısını iyileştirmek için şu anda önbelleğe alma ve akıllı yenileme avantajlarından yararlanır. Bu işlem, yapılandırma deposuna doğrudan bağlantıları önleyerek, yüksek hacimli istemci uygulamalarında yansıtılmalıdır. Bunun yerine, istemci uygulamaları özel bir hizmete bağlanır ve bu hizmet yapılandırma deposuyla iletişim kurar. Bu proxy çözümü, istemci uygulamalarının yapılandırma deposundaki azaltma sınırına yaklaşımadığından emin olabilir. Daraltma hakkında daha fazla bilgi için bkz. [SSS](./faq.yml#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration).  

## <a name="next-steps"></a>Sonraki adımlar

* [Anahtarlar ve değerler](./concept-key-value.md)