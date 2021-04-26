---
title: "Hızlı başlangıç: bir uygulamayı bir Web API 'sine erişecek şekilde yapılandırma | Mavisi"
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, istemci uygulamalarına kapsamlı kaynak erişimini (izinleri) etkinleştirmek için Microsoft Identity platformunda bir Web API 'sini temsil eden bir uygulama kaydı yapılandırırsınız.
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
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 0b064e8491b5d4fa988e9f476143cc0711e3934a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651383"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Hızlı başlangıç: bir istemci uygulamasını bir Web API 'sine erişmek için yapılandırma

Bu hızlı başlangıçta, kendi Web API 'nize kapsamlı, izin tabanlı erişime sahip Microsoft Identity platformu ile kaydedilmiş bir istemci uygulaması sağlarsınız. Ayrıca, Microsoft Graph için istemci uygulama erişimi de sağlarsınız.

İstemci uygulamanızın kaydında bir Web API 'sinin kapsamlarını belirterek, istemci uygulaması Microsoft Identity platformundan bu kapsamları içeren bir erişim belirteci elde edebilir. Web API 'SI, kodu içinde, erişim belirtecinde bulunan kapsamları temel alarak kaynaklarına izin tabanlı erişim sağlayabilir.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı- [ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Hızlı başlangıç: uygulamayı kaydetme](quickstart-register-app.md)
* [Hızlı başlangıç tamamlama: bir uygulamayı bir Web API 'si göstermek üzere yapılandırma](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>Web API 'nize erişim izinleri ekleme

İlk senaryoda, her ikisi de önkoşulların bir parçası olarak kaydolmanız gereken kendi Web API 'niz için bir istemci uygulamasına erişim hakkı vermiş olursunuz. Hem istemci uygulamanız hem de bir Web API 'SI kayıtlı değilse, iki [Önkoşul](#prerequisites) makalelerindeki adımları izleyin.

Bu diyagramda iki uygulama kaydı birbirleriyle nasıl ilişki gösterilmektedir. Bu bölümde, istemci uygulamanın kaydına izinleri eklersiniz.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="Sağ tarafta açık kapsamlar olan bir Web API 'SI ve izin olarak seçili kapsamlar seçiliyken sol taraftaki bir istemci uygulaması gösteren çizgi diyagramı" border="false":::

Hem istemci uygulamanızı hem de Web API 'nizi kaydettikten sonra, kapsamları oluşturarak API 'yi kullanıma aldıktan sonra, aşağıdaki adımları izleyerek istemcinin izinlerini API 'ye yapılandırabilirsiniz:

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden çok kiracıya erişiminiz varsa,  :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: istemci uygulamanızın kaydını içeren kiracıyı seçmek Için üst menüdeki Dizin + abonelik filtresini kullanın.
1. **Azure Active Directory**  >  **uygulama kayıtları**' i seçin ve ardından istemci uygulamanızı (Web API 'nizi *değil* ) seçin.
1. **API izinleri** Seç  >    >  **API 'lerim** izin Ekle.
1. Önkoşulların bir parçası olarak kaydettiğiniz Web API 'sini seçin.

    **Temsilci izinleri** varsayılan olarak seçilidir. Temsilci izinleri, oturum açmış kullanıcı olarak bir Web API 'sine erişen istemci uygulamalar için uygundur ve erişimi bir sonraki adımda seçtiğiniz izinlerle sınırlandırılmalıdır. Bu örnek için **temsilci izinleri** seçili bırakın.

    **Uygulama izinleri** , oturum açma veya onay için Kullanıcı etkileşimi olmadan, BIR Web API 'sine kendi başına erişmesi gereken hizmet veya Daemon türünde uygulamalar içindir. Web API 'niz için uygulama rolleri tanımlamadığınız takdirde, bu seçenek devre dışıdır.
1. **Izinleri Seç**' in altında, KAPSAMLARıNı Web API 'niz için tanımladığınız kaynağı genişletin ve istemci uygulamanın oturum açmış kullanıcı adına sahip olması gereken izinleri seçin.

    Önceki hızlı başlangıçta belirtilen örnek kapsam adlarını kullandıysanız çalışanları görmeniz gerekir **. Read. All** ve **employees. Write. All**.
    **Çalışanları seç. tümünü** veya önkoşulları tamamlarken oluşturmuş olabileceğiniz başka bir izin seçin.
1. İşlemi gerçekleştirmek için **Izinleri Ekle** ' yi seçin.

API 'nize izinler eklendikten sonra, **yapılandırılan izinler** altında Seçili izinleri görmeniz gerekir. Aşağıdaki görüntüde *çalışanlar. Read* örnek gösterilmektedir. istemci uygulamanın kaydına eklenen tüm temsilci izinleri.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Yeni eklenen izni gösteren Azure portal izinleri bölmesi yapılandırıldı":::

Ayrıca, Microsoft Graph API 'SI için *Kullanıcı. Read* iznine de sahip olabilirsiniz. Bu izin, bir uygulamayı Azure portal kaydettiğinizde otomatik olarak eklenir.

## <a name="add-permissions-to-access-microsoft-graph"></a>Erişim Microsoft Graph izinleri ekleme

Oturum açmış kullanıcı adına kendi Web API 'nize erişmenin yanı sıra, uygulamanızın Microsoft Graph ' de depolanan kullanıcı (veya diğer) verilerine erişmesi veya onları değiştirmesi de gerekebilir. Ya da kendi başına Microsoft Graph erişmesi gereken hizmet veya Daemon uygulamanız olabilir, ancak herhangi bir kullanıcı etkileşimi olmadan işlemler gerçekleştiriliyor.

### <a name="delegated-permission-to-microsoft-graph"></a>Microsoft Graph için temsilci izni

İstemci uygulamanızın oturum açmış kullanıcı adına işlemleri gerçekleştirmesini sağlamak için Microsoft Graph için temsilci izni yapılandırın (örneğin, e-postalarını okumak veya profillerini değiştirmek). Varsayılan olarak, istemci uygulamanızın kullanıcılarına, sizin için yapılandırdığınız Temsilcili izinlere izin vermek üzere oturum açtıklarında sorulur.

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden çok kiracıya erişiminiz varsa,  :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: istemci uygulamanızın kaydını içeren kiracıyı seçmek Için üst menüdeki Dizin + abonelik filtresini kullanın.
1. **Azure Active Directory**  >  **uygulama kayıtları**' i seçin ve ardından istemci uygulamanızı seçin.
1. **API izinlerini** seçin  >  **izin**  >  **Microsoft Graph** Ekle
1. **Temsilci izinleri** seçin. Microsoft Graph, listenin en üstünde gösterilen en yaygın olarak kullanılan birçok izni kullanıma sunar.
1. **Izinleri Seç**' in altında aşağıdaki izinleri seçin:

    | İzin       | Description                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | Kullanıcıların e-posta adresini görüntüleme                           |
    | `offline_access` | Erişimi verdiğiniz verilere erişimi koruyun |
    | `openid`         | Kullanıcıların oturumunu açma                                       |
    | `profile`        | Kullanıcıların temel profilini görüntüleme                           |
1. İşlemi gerçekleştirmek için **Izinleri Ekle** ' yi seçin.

İzinleri yapılandırdığınızda uygulamanızın kullanıcılarına, uygulamanızın kaynak API 'sine kendi adına erişmesine izin vermek için oturum açma istenir.

Yönetici olarak, *Tüm* kullanıcılar adına izin verebilir, dolayısıyla bunu yapması istenmez. Yönetici onayı, bu makalenin daha sonra [API izinleri ve yönetici onayı](#more-on-api-permissions-and-admin-consent) bölümünde ele alınmıştır.

### <a name="application-permission-to-microsoft-graph"></a>Microsoft Graph için uygulama izni

Kullanıcı etkileşimi veya onay olmadan kendisi olarak kimlik doğrulaması yapması gereken bir uygulama için uygulama izinlerini yapılandırın. Uygulama izinleri genellikle arka plan hizmetleri veya bir API 'ye "gözetimsiz" bir şekilde erişen ve başka bir (aşağı akış) API 'ye erişen Web API 'Leri tarafından kullanılır.

Aşağıdaki adımlarda Microsoft Graph dosyalarına izin verirsiniz. bir örnek olarak *. All Iznini okuyun* .

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden çok kiracıya erişiminiz varsa,  :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: istemci uygulamanızın kaydını içeren kiracıyı seçmek Için üst menüdeki Dizin + abonelik filtresini kullanın.
1. **Azure Active Directory**  >  **uygulama kayıtları**' i seçin ve ardından istemci uygulamanızı seçin.
1. **API izinlerini** seçin  >  **bir izin**  >  **Microsoft Graph**  >  **Uygulama izinleri** ekleyin.
1. Microsoft Graph tarafından açığa çıkarılan tüm izinler, **Izinleri Seç** altında gösterilir.
1. Uygulamanıza vermek istediğiniz izin veya izinleri seçin. Örnek olarak, kuruluşunuzdaki dosyaları tarayan, belirli bir dosya türü veya adı üzerinde uyarı veren bir Daemon uygulamanız olabilir.

    **Izinleri Seç**' ın altında **dosyalar**' ı genişletin ve ardından *dosyalar. Read. All* iznini seçin.
1. **Izin Ekle**' yi seçin.

Microsoft Graph *dosyaları. Read. All* izni gibi bazı izinler yönetici onayı gerektirir. Yönetici onayı **verme** düğmesini seçerek yönetici onayı ' nı, daha sonra [Yönetici onay düğmesi](#admin-consent-button) bölümünde anlatıldığı şekilde verirsiniz.

### <a name="configure-client-credentials"></a>İstemci kimlik bilgilerini yapılandırma

Uygulama izinlerini kullanan uygulamalar, Kullanıcı etkileşimi gerektirmeden kendi kimlik bilgilerini kullanarak kimliğini doğrular. Uygulamanız (veya API), uygulama izinlerini kullanarak Microsoft Graph, kendi Web API 'niz veya başka bir API 'ye erişebilerek, o istemci uygulamasının kimlik bilgilerini yapılandırmanız gerekir.

Uygulamanın kimlik bilgilerini yapılandırma hakkında daha fazla bilgi için, [hızlı başlangıç: Microsoft Identity platformu ile bir uygulama kaydetme](quickstart-register-app.md)konusunun [kimlik bilgileri ekleme](quickstart-register-app.md#add-credentials) bölümüne bakın.

## <a name="more-on-api-permissions-and-admin-consent"></a>API izinleri ve yönetici onayı hakkında daha fazla bilgi

Bir uygulama kaydının **API izinleri** bölmesi [yapılandırılmış izinler](#configured-permissions) tablosu içerir ve ayrıca [başka bir izin verilen](#other-permissions-granted) tablo içerebilir. Hem tablolar hem de [Yönetici onay düğmesi](#admin-consent-button) aşağıdaki bölümlerde açıklanmıştır.

### <a name="configured-permissions"></a>Yapılandırılan izinler

**API izinleri** bölmesinde **yapılandırılan izinler** tablosu, uygulamanızın temel işlem için ihtiyaç duyduğu izin listesini gösterir- *gerekli kaynak erişimi* (RRA) listesi. Kullanıcılarınızın veya yöneticilerinin, uygulamanızı kullanmadan önce bu izinlere onay girmeleri gerekir. Diğer bir deyişle, daha sonra çalışma zamanında (dinamik onay kullanılarak) isteğe bağlı izinler istenebilir.

Bu, kullanıcıların uygulamanız için onaylaması gereken en düşük izin listesidir. Daha fazla olabilir, ancak bunlar her zaman gerekecektir. Güvenlik için ve kullanıcıların ve yöneticilerin uygulamanızı daha rahat kullanmasını sağlamaya yardımcı olmak için, gerek duymadığınız hiçbir şeyi hiçbir zaman sorma.

Yukarıda özetlenen adımları veya [verilen diğer izinlerden](#other-permissions-granted) (bir sonraki bölümde açıklanan) belirtilen adımları kullanarak bu tabloda görünen izinleri ekleyebilir veya kaldırabilirsiniz. Yönetici olarak, bir API 'nin tabloda görüntülenen izinlerinin tam kümesi için yönetici onayı verebilir ve bireysel izinler için onayı iptal edebilirsiniz.

### <a name="other-permissions-granted"></a>Diğer izinler verildi

**API izinleri** bölmesinde **{kiracınız} için verilen diğer izinler** adlı bir tablo da görebilirsiniz. **{Kiracınız} tablonuz için verilen diğer izinler** , uygulama nesnesi üzerinde açıkça yapılandırılmamış olan kiracı için verilen izinleri gösterir. Bu izinler dinamik olarak istendi ve olarak verildi. Bu bölüm yalnızca geçerli olan en az bir izin varsa görünür.

Bir API 'nin izinlerinin tam kümesini veya bu tabloyu **yapılandırılmış izinler** tablosuna görünen bireysel izinleri ekleyebilirsiniz. Yönetici olarak, bu bölümdeki API 'Ler veya bireysel izinler için yönetici onayını iptal edebilirsiniz.

### <a name="admin-consent-button"></a>Yönetici onay düğmesi

**{Kiracınız} için yönetici Izni verme** düğmesi bir yöneticinin uygulama için yapılandırılan izinlere yönetici onayı vermesini sağlar. Düğmeyi seçtiğinizde, onay eylemini onaylamanız isteyen bir iletişim kutusu gösterilir.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="Azure portal yapılandırılan izinler bölmesinde Yönetici onay düğmesine vurgulanmış olarak izin ver":::

İzin verdikten sonra, gerekli yönetici onayı izni verilen izinler izin verildi olarak gösterilir:

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="Dosyalar için verilen yönetici onayını gösteren Azure portal izin tablosunu yapılandırın. All iznini okuyun":::

Yönetici değilseniz veya uygulama için hiçbir izin yapılandırılmamışsa **yönetici onayı verme** düğmesi *devre dışı bırakılır* . Verilmiş ancak henüz yapılandırılmamış izinleriniz varsa, yönetici onay düğmesi bu izinleri idare etmek isteyip istemediğinizi sorar. Bunları, yapılandırılmış izinlere ekleyebilir veya kaldırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Hangi hesap türlerinin uygulamanıza erişebileceğini nasıl yapılandıracağınızı öğrenmek için serideki bir sonraki hızlı başlangıca ilerleyin. Örneğin, erişimi yalnızca kuruluşunuzdaki kullanıcılarla (tek kiracı) sınırlamak veya diğer Azure AD kiracılarındaki kullanıcılara (çok kiracılı) ve kişisel Microsoft hesaplarıyla (MSA) izin vermek isteyebilirsiniz.

> [!div class="nextstepaction"]
> [Bir uygulama tarafından desteklenen hesapları değiştirme](quickstart-modify-supported-accounts.md)
