---
title: "Hızlı başlangıç: Web API 'sini kaydetme ve kullanıma sunma | Mavisi"
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, bir Web API 'sini Microsoft Identity platformu ile kaydeder ve kapsamlarını yapılandırarak API 'nin kaynaklarına izin tabanlı erişim için istemcilere kullanıma sunmaktadır.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 820bc7dfe9123db495c151cd5cd0ea5ae337619f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103983"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Hızlı başlangıç: bir uygulamayı bir Web API 'SI göstermek üzere yapılandırma

Bu hızlı başlangıçta, bir Web API 'sini Microsoft Identity platformu ile kaydeder ve örnek bir kapsam ekleyerek bunu istemci uygulamalarına kullanıma sunacaksınız. Web API 'nizi kaydederek ve kapsamlar aracılığıyla kullanıma sunarak, kendi kaynaklarına, API 'nize erişen yetkili kullanıcılara ve istemci uygulamalarına izin tabanlı erişim sağlayabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı- [ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Hızlı başlangıç tamamlama: kiracı ayarlama](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>Web API 'sini kaydetme

Web API 'nizin kaynaklarına kapsamlı erişim sağlamak için, önce API 'yi Microsoft Identity platformu ile kaydetmeniz gerekir.

1. [Hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme](quickstart-register-app.md) **bölümündeki adımları** uygulayın.
1. **Yeniden yönlendirme URI 'Si Ekle** ve **Platform ayarlarını yapılandır** bölümlerini atlayın. Etkileşimli olarak hiçbir Kullanıcı oturum açmadığından, bir Web API 'si için yeniden yönlendirme URI 'SI yapılandırmanız gerekmez.
1. Şimdilik **kimlik bilgileri ekle** bölümünü atlayın. Yalnızca API 'niz bir aşağı akış API 'sine eriştiğinde, bu makalede kapsanmayan bir senaryo olan kendi kimlik bilgilerine ihtiyacı vardır.

Web API 'niz kayıtlı olduğunda, API 'nizin tüketicilerine ayrıntılı izin sağlamak için API 'nizin kullanabileceği kapsamları eklemeye hazırsınız demektir.

## <a name="add-a-scope"></a>Kapsam Ekle

Bir istemci uygulamasındaki kod, bir erişim belirtecini korunan kaynağa (Web API) istekleriyle birlikte geçirerek Web API 'niz tarafından tanımlanan işlemleri gerçekleştirme izni ister. Web API 'niz daha sonra istenen işlemi, yalnızca aldığı erişim belirteci, işlem için gereken kapsamları içeriyorsa gerçekleştirir.

İlk olarak, adlı örnek bir kapsam oluşturmak için aşağıdaki adımları izleyin `Employees.Read.All` :

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden çok kiracıya erişiminiz varsa,  :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false"::: istemci uygulamanızın kaydını içeren kiracıyı seçmek Için üst menüdeki Dizin + abonelik filtresini kullanın.
1. **Azure Active Directory**  >  **uygulama kayıtları** öğesini seçin ve ardından API 'nizin uygulama kaydını seçin.
1. **API 'yi kullanıma** sunma bir  >  **kapsam ekleyin**' i seçin.

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="Uygulama kaydı, Azure portal bir API bölmesi sunar":::

1. Henüz bir tane yapılandırmadıysanız, bir **uygulama kimliği URI 'si** ayarlamanız istenir.

   Uygulama KIMLIĞI URI 'SI, API 'nin kodunda başvuracağız ve genel olarak benzersiz olması gereken kapsamlar için ön ek olarak davranır. Olarak belirtilen varsayılan değeri kullanabilir `api://<application-client-id>` veya gibi daha okunabilir BIR URI belirtebilirsiniz `https://contoso.com/api` .

1. Sonra, kapsam **Ekle** bölmesinde kapsamın özniteliklerini belirtin. Bu izlenecek yol için örnek değerleri kullanabilir veya kendi kendinizinkini belirtebilirsiniz.

    | Alan | Açıklama | Örnek |
    |-------|-------------|---------|
    | **Kapsam adı** | Kapsamınız adı. Ortak bir kapsam adlandırma kuralı `resource.operation.constraint` . | `Employees.Read.All` |
    | **Kimler onaylayabilir** | Bu kapsamın Kullanıcı tarafından kabul edilip edilmeyeceğini veya yönetici onayı gerekli olup olmadığı. Daha yüksek ayrıcalıklı izinler için **Yalnızca yöneticiler**’i seçin. | **Yöneticiler ve kullanıcılar** |
    | **Yönetici onayı görünen adı** | Kapsamın yalnızca yöneticiler tarafından göreceği amaç hakkında kısa bir açıklama. | `Read-only access to Employee records` |
    | **Yönetici onayı açıklaması** | Yalnızca yöneticilerin göreceği kapsam tarafından verilen iznin daha ayrıntılı bir açıklaması. | `Allow the application to have read-only access to all Employee data.` |
    | **Kullanıcı onayı görünen adı** | Kapsamın amacının kısa bir açıklaması. Kullanıcılara yalnızca **Yöneticiler ve kullanıcılara** **izin verebilir** ayarlarsanız gösterilir. | `Read-only access to your Employee records` |
    | **Kullanıcı onayı açıklaması** | Kapsam tarafından verilen iznin daha ayrıntılı bir açıklaması. Kullanıcılara yalnızca **Yöneticiler ve kullanıcılara** **izin verebilir** ayarlarsanız gösterilir. | `Allow the application to have read-only access to your Employee data.` |

1. **Durumu** **etkin** olarak ayarlayın ve ardından **Kapsam Ekle**' yi seçin.

1. Seçim Uygulamanızın kullanıcılarına tanımladığınız kapsamlara izin sorulmasını engellemek için, istemci uygulamasını Web API 'nize erişmek üzere *önceden yetkilendirirsiniz* . Kullanıcılarınızın izin reddetme fırsatı olmadığı için *yalnızca* güvendiğiniz istemci uygulamalarına ön yetki verin.
    1. **Yetkili istemci uygulamaları** altında, **istemci uygulaması Ekle** ' yi seçin.
    1. Önceden yetkilendirmek istediğiniz istemci uygulamanın **uygulama (istemci) kimliğini** girin. Örneğin, daha önce kaydetmiş olduğunuz bir Web uygulamasının.
    1. **Yetkili kapsamlar** altında izin istemini bastırmak istediğiniz kapsamları seçin ve ardından **Uygulama Ekle**' yi seçin.

    İsteğe bağlı bu adımı izlediyseniz, istemci uygulaması artık önceden yetkilendirilmiş bir istemci uygulaması (PCA) ve oturum açarken kullanıcılara onay sorulur.

## <a name="add-a-scope-requiring-admin-consent"></a>Yönetici onayı gerektiren bir kapsam ekleyin

Daha sonra, `Employees.Write.All` yalnızca yöneticilerin izin veremediği adlı başka bir örnek kapsam ekleyin. Yönetici onayı gerektiren kapsamlar genellikle daha yüksek ayrıcalıklı işlemlere erişim sağlamak için kullanılır ve genellikle arka uç hizmetleri olarak çalışan istemci uygulamaları veya kullanıcı etkileşimli olarak oturum açan Daemon 'ları.

`Employees.Write.All`Örnek kapsamı eklemek için, [kapsam ekleme](#add-a-scope) bölümündeki adımları izleyin ve bu değerleri **Kapsam Ekle** bölmesinde belirtin:

| Alan                          | Örnek değer                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **Kapsam adı**                 | `Employees.Write.All`                                              |
| **Kimler onaylayabilir**            | **Yalnızca Yöneticiler**                                                    |
| **Yönetici onayı görünen adı** | `Write access to Employee records`                                 |
| **Yönetici onayı açıklaması**  | `Allow the application to have write access to all Employee data.` |
| **Kullanıcı onayı görünen adı**  | *Hiçbiri (boş bırakın)*                                               |
| **Kullanıcı onayı açıklaması**   | *Hiçbiri (boş bırakın)*                                               |

## <a name="verify-the-exposed-scopes"></a>Sunulan kapsamları doğrulama

Önceki bölümlerde açıklanan örnek kapsamları başarıyla eklediyseniz, bu resme benzer şekilde, Web API 'sinin uygulama kaydınızın **BIR API bölmesini kullanıma sunacaksınız** :

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="İki açık kapsamı gösteren bir API bölmesinin sergileme ekran görüntüsü.":::

Görüntüde gösterildiği gibi, bir kapsamın tam dizesi, Web API 'nizin **uygulama KIMLIĞI URI** 'sinin ve kapsamın **Kapsam adının** bitiştirilmesi olur.

Örneğin, Web API 'nizin uygulama KIMLIĞI URI 'SI ise `https://contoso.com/api` ve kapsam adı ise `Employees.Read.All` , tam kapsam şu şekilde olur:

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>Sunulan kapsamları kullanma

Serinin bir sonraki makalesinde, Web API 'nize erişimi olan bir istemci uygulamasının kaydını ve bu makalenin adımlarını izleyerek tanımladığınız kapsamları yapılandırırsınız.

Bir istemci uygulama kaydına Web API 'nize erişim izni verildiğinde, istemciye Microsoft Identity platformu tarafından bir OAuth 2,0 erişim belirteci verilebilir. İstemci, Web API 'sini çağırdığında, kapsamı ( `scp` ) talebi istemcinin uygulama kaydında belirttiğiniz izinlere ayarlanmış olan bir erişim belirteci sunar.

Gerekirse daha sonra ek kapsamları kullanıma sunabilirsiniz. Web API 'nizin birkaç işlem ile ilişkili birden çok kapsamı kullanıma sunabileceğinden göz önünde bulundurun. Kaynağınız, `scp` aldığı OAuth 2,0 erişim belirtecindeki kapsam () talep (ler) i değerlendirerek çalışma zamanında Web API 'sine erişimi denetleyebilir.

## <a name="next-steps"></a>Sonraki adımlar

Kapsamları yapılandırarak Web API 'nizi kullanıma açıkdığınıza göre, istemci uygulamanızın, kapsamlarına erişim izni olan kaydını yapılandırın.

> [!div class="nextstepaction"]
> [Web API erişimi için uygulama kaydını yapılandırma](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
