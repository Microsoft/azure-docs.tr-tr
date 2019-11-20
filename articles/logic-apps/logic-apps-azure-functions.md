---
title: Azure Logic Apps Azure işlevleri ekleme ve çağırma
description: Logic Apps 'ten Azure işlevleri ekleme ve çalıştırma
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 10/01/2019
ms.reviewer: klam, LADocs
ms.openlocfilehash: 5b946e36c5da9f122adce1f8e3b99523a789a66f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901165"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Azure Logic Apps Azure işlevleri 'ni çağırma

Mantıksal uygulamalarınızda belirli bir işi gerçekleştiren kodu çalıştırmak istediğinizde, [Azure işlevleri](../azure-functions/functions-overview.md)'ni kullanarak kendi işlevinizi oluşturabilirsiniz. Bu hizmet, Node. js, C#ve F# işlevleri oluşturmanıza yardımcı olur, böylece kodu çalıştırmak için tamamen bir uygulama veya altyapı oluşturmanız gerekmez. Ayrıca, [Azure işlevlerinin içinden Logic Apps 'i çağırabilirsiniz](#call-logic-app). Azure Işlevleri, bulutta sunucusuz bilgi işlem sağlar ve bu örnekler gibi görevleri gerçekleştirmek için yararlıdır:

* Mantıksal uygulamanızın davranışını Node. js veya C#içindeki işlevlerle genişletin.
* Mantıksal uygulama iş akışınızda hesaplamalar gerçekleştirin.
* Mantıksal uygulamalarınızda gelişmiş biçimlendirme veya işlem alanları uygulayın.

Kod parçacıklarını Azure işlevleri oluşturmadan çalıştırmak için [satır içi kod ekleme ve çalıştırma](../logic-apps/logic-apps-add-run-inline-code.md)hakkında bilgi edinin.

> [!NOTE]
> Logic Apps ve Azure Işlevleri arasındaki tümleştirme Şu anda etkin yuvalarla çalışmıyor.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Azure işlevinizle birlikte Azure işlevleri kapsayıcısı olan bir Azure işlev uygulaması. Bir işlev uygulamanız yoksa, [önce işlev uygulamanızı oluşturun](../azure-functions/functions-create-first-azure-function.md). Daha sonra işlevinizi Azure portal mantıksal uygulamanızın dışında veya mantıksal uygulama Tasarımcısı 'nda [mantıksal](#create-function-designer) uygulamanızın içinden oluşturabilirsiniz.

* Logic Apps ile çalışırken, mevcut veya yeni mi olduğunu bağımsız olarak işlev uygulamalarına ve işlevlere aynı gereksinimleri uygular:

  * İşlev uygulamanız ve mantıksal uygulamanızın aynı Azure aboneliğini kullanması gerekir.

  * Yeni işlev uygulamaları, çalışma zamanı yığını olarak .NET veya JavaScript kullanmalıdır. Varolan işlev uygulamalarına yeni bir işlev eklediğinizde, ya da C# JavaScript ' i seçebilirsiniz.

  * İşleviniz **http tetikleme** şablonunu kullanıyor.

    HTTP tetikleyici şablonu, mantıksal uygulamanızdan `application/json` türüne sahip içeriği kabul edebilir. Mantıksal uygulamanıza bir Azure işlevi eklediğinizde, Logic App Designer, Azure aboneliğinizde Bu şablondan oluşturulan özel işlevleri gösterir.

  * İşleviniz, bir [Openapı tanımı](../azure-functions/functions-openapi-definition.md) (eski adıyla [Swagger dosyası](https://swagger.io/)) tanımlamadığınız sürece özel yollar kullanmaz.

  * İşleviniz için bir Openapı tanımınız varsa Logic Apps tasarımcı, işlev parametreleriyle çalışmanız durumunda daha zengin bir deneyim sunar. Mantıksal uygulamanızın Openapı tanımlarına sahip işlevleri bulabilmesi ve bunlara erişebilmesi için, [Bu adımları izleyerek işlev uygulamanızı ayarlayın](#function-swagger).

* Mantıksal uygulamanızda ilk adım olarak bir [tetikleyici](../logic-apps/logic-apps-overview.md#logic-app-concepts) dahil olmak üzere, işlevini eklemek istediğiniz mantıksal uygulama

  İşlevleri çalıştıran eylemleri ekleyebilmeniz için önce mantıksal uygulamanızın bir tetikleyiciyle başlaması gerekir. Logic Apps 'e yeni başladıysanız, [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [hızlı başlangıç: Ilk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)' yı gözden geçirin.

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Openapı açıklamaları olan işlevleri bulma

Logic Apps tasarımcısında işlev parametreleriyle çalışırken daha zengin bir deneyim için, işleviniz için önceden [Swagger dosyası](https://swagger.io/)olarak bilinen [bir openapı tanımı oluşturun](../azure-functions/functions-openapi-definition.md). Mantıksal uygulamanızın Swagger açıklamalarını içeren işlevleri bulmasını ve kullanmasını sağlamak için işlev uygulamanızı ayarlamak için şu adımları izleyin:

1. İşlev uygulamanızın etkin bir şekilde çalıştığından emin olun.

1. İşlev uygulamanızda, şu adımları izleyerek tüm kaynaklara izin verilecek şekilde, çıkış noktaları [arası kaynak paylaşımı (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) ayarlayın:

   1. **Işlev uygulamaları** listesinden işlev uygulamanızı seçin. Sağ bölmede, **CORS** > **platform özellikleri** ' ni seçin.

      !["Platform özellikleri" > "CORS" > işlev uygulamanızı seçin](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. **CORS**altında, yıldız işareti ( **`*`** ) joker karakterini ekleyin, ancak listedeki diğer tüm kaynakları kaldırın ve **Kaydet**' i seçin.

      !["CORS * joker karakterini" * "olarak ayarlayın](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>HTTP isteklerinin içindeki özellik değerlerine erişin

Web kancası işlevleri HTTP isteklerini giriş olarak kabul edebilir ve bu istekleri diğer işlevlere geçirebilir. Örneğin, Logic Apps [DateTime değerlerini dönüştüren işlevlere](../logic-apps/workflow-definition-language-functions-reference.md)sahip olsa da, bu temel örnek JavaScript işlevi, işleve geçirilen ve bu özellik değerinde işlemler gerçekleştiren bir istek nesnesi içindeki bir özelliğe nasıl erişebileceğiniz gösterir. Nesneler içindeki özelliklere erişmek için bu örnek [nokta (.) işlecini](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)kullanır:

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Bu işlevin içinde ne olur:

1. İşlevi bir `data` değişkeni oluşturur ve bu değişkene `request` nesnesinin içindeki `body` nesnesini atar. İşlevi, `request` nesnesinin içindeki `body` nesnesine başvurmak için nokta (.) işlecini kullanır:

   ```javascript
   var data = request.body;
   ```

1. İşlev artık `data` değişkeni aracılığıyla `date` özelliğine erişebilir ve `ToDateString()` işlevini çağırarak bu özellik değerini DateTime türünden DateString türüne dönüştürebilir. İşlev Ayrıca işlevin yanıtında `body` özelliği aracılığıyla sonucu döndürür:

   ```javascript
   body: data.date.ToDateString();
   ```

Azure işlevinizi oluşturduğunuza göre, [mantıksal uygulamalara işlev ekleme](#add-function-logic-app)adımlarını izleyin.

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Logic Apps içinde işlevler oluşturma

Mantıksal uygulama Tasarımcısı 'nı kullanarak mantıksal uygulamanızın içinden başlayan bir Azure işlevi oluşturabilmeniz için önce işlevleriniz için bir kapsayıcı olan bir Azure işlev uygulamasına sahip olmanız gerekir. Bir işlev uygulamanız yoksa, önce bu işlev uygulamasını oluşturun. Bkz. [Azure Portal ilk işlevinizi oluşturma](../azure-functions/functions-create-first-azure-function.md).

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. İşlevinizi oluşturmak ve eklemek için senaryonuz için geçerli olan adımı izleyin:

   * Mantıksal uygulamanızın iş akışındaki son adım altında **yeni adım**' ı seçin.

   * Mantıksal uygulamanızın iş akışındaki mevcut adımlar arasında farenizi ok üzerine taşıyın, artı (+) işaretini seçin ve ardından **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak "Azure işlevleri" yazın. Eylemler listesinden **bir Azure Işlevi seçin** eylemini seçin, örneğin:

   !["Azure işlevleri" ni bulun](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. İşlev uygulamaları listesinden işlev uygulamanızı seçin. Eylemler listesi açıldıktan sonra şu eylemi seçin: **Yeni Işlev oluştur**

   ![İşlev uygulamanızı seçin](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. İşlev tanımı düzenleyicisinde, işlevinizi tanımlayın:

   1. **İşlev adı** kutusunda, işleviniz için bir ad girin.

   1. **Kod** kutusunda, işleviniz çalıştıktan sonra mantıksal uygulamanıza döndürülmesini istediğiniz yanıt ve yük dahil olmak üzere, kodunuzu işlev şablonuna ekleyin. İşiniz bittiğinde **Oluştur**’u seçin.

   Örneğin:

   ![İşlevinizi tanımlayın](./media/logic-apps-azure-functions/add-code-function-definition.png)

   Şablonun kodunda, *`context` nesnesi* , mantıksal uygulamanızın sonraki bir adımda bulunan **İstek gövdesi** alanı aracılığıyla gönderdiği iletiyi ifade eder. İşlevinizin içinden `context` nesnesinin özelliklerine erişmek için şu sözdizimini kullanın:

   `context.body.<property-name>`

   Örneğin, `context` nesnesinin içindeki `content` özelliğine başvurmak için şu sözdizimini kullanın:

   `context.body.content`

   Şablon kodu Ayrıca, `data` parametresindeki değeri depolayan bir `input` değişkeni de içerir, böylece işleviniz bu değer üzerinde işlem yapabilir. JavaScript işlevlerinin içinde `data` değişkeni de `context.body`için bir kısayoldur.

   > [!NOTE]
   > Burada `body` özelliği `context` nesnesi için geçerlidir ve işlevinize de geçirebilmeniz için bir eylemin çıktısından gelen **gövde** belirteciyle aynı değildir.

1. **Istek gövdesi** kutusunda, bir JAVASCRIPT nesne GÖSTERIMI (JSON) nesnesi olarak biçimlendirilmesi gereken işlevinizin girdisini belirtin.

   Bu giriş, mantıksal uygulamanızın işlevinizin gönderdiği *bağlam nesnesi* veya iletidir. **Istek gövdesi** alanına tıkladığınızda, önceki adımlardan gelen çıkışlara yönelik belirteçleri seçebilmeniz için dinamik içerik listesi görüntülenir. Bu örnek, bağlam yükünün, e-posta tetikleyicisinden **gelen** belirtecin değeri olan `content` adlı bir özellik içerdiğini belirtir.

   !["İstek gövdesi" örneği-bağlam nesnesi yükü](./media/logic-apps-azure-functions/function-request-body-example.png)

   Burada, bağlam nesnesi bir dize olarak atama yapılmıyor, bu nedenle nesnenin içeriği doğrudan JSON yüküne eklenir. Ancak, bağlam nesnesi bir String, JSON nesnesi veya bir JSON dizisi geçen bir JSON belirteci olmadığında bir hata alırsınız. Bu nedenle, bu örnek yerine **alınan süre** belirtecini kullandıysanız, çift tırnak işaretleri ekleyerek bağlam nesnesini bir dize olarak çevirebilirsiniz.

   ![Nesneyi dize olarak yayınla](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Kullanılacak yöntem veya kimlik doğrulama, istek üstbilgileri veya sorgu parametreleri ya da kimlik doğrulaması gibi diğer ayrıntıları belirtmek için **yeni parametre Ekle** listesini açın ve istediğiniz seçenekleri seçin. Kimlik doğrulaması için, seçenekleriniz seçtiğiniz işleve göre farklılık gösterir. Bkz. [Azure işlevleri için kimlik doğrulamasını etkinleştirme](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Logic Apps 'e mevcut işlevleri ekleyin

Mantıksal uygulamalarınızdan mevcut Azure işlevlerini çağırmak için, mantıksal uygulama Tasarımcısı ' nda başka herhangi bir eylem gibi Azure işlevleri ekleyebilirsiniz.

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. İşlevi eklemek istediğiniz adım altında **yeni adım**' ı seçin.

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak "Azure işlevleri" yazın. Eylemler listesinden **bir Azure Işlevi seçin** eylemi seçin.

   !["Azure işlevleri" ni bulun](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. İşlev uygulamaları listesinden işlev uygulamanızı seçin. İşlevler listesi göründükten sonra işlevinizi seçin.

   ![İşlev uygulamanızı ve Azure işlevinizi seçin](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   API tanımlarına (Swagger açıklamaları) sahip olan ve [mantıksal uygulamanızın bu işlevleri bulabileceği ve erişebilecek şekilde ayarlandığı](#function-swagger)işlevlerde **Swagger eylemleri**' ni seçebilirsiniz.

   ![İşlev uygulamanızı, "Swagger eylemleri" ve Azure işlevinizi seçin](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. **Istek gövdesi** kutusunda, bir JAVASCRIPT nesne GÖSTERIMI (JSON) nesnesi olarak biçimlendirilmesi gereken işlevinizin girdisini belirtin.

   Bu giriş, mantıksal uygulamanızın işlevinizin gönderdiği *bağlam nesnesi* veya iletidir. **Istek gövdesi** alanına tıkladığınızda, önceki adımlardan gelen çıkışlara yönelik belirteçleri seçebilmeniz için dinamik içerik listesi görüntülenir. Bu örnek, bağlam yükünün, e-posta tetikleyicisinden **gelen** belirtecin değeri olan `content` adlı bir özellik içerdiğini belirtir.

   !["İstek gövdesi" örneği-bağlam nesnesi yükü](./media/logic-apps-azure-functions/function-request-body-example.png)

   Burada, bağlam nesnesi bir dize olarak atama yapılmıyor, bu nedenle nesnenin içeriği doğrudan JSON yüküne eklenir. Ancak, bağlam nesnesi bir String, JSON nesnesi veya bir JSON dizisi geçen bir JSON belirteci olmadığında bir hata alırsınız. Bu nedenle, bu örnek yerine **alınan süre** belirtecini kullandıysanız, çift tırnak işaretleri ekleyerek bağlam nesnesini bir dize olarak çevirebilirsiniz:

   ![Nesneyi dize olarak yayınla](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Kullanılacak yöntem, üst bilgiler, sorgu parametreleri veya kimlik doğrulaması gibi diğer ayrıntıları belirtmek için **yeni parametre Ekle** listesini açın ve istediğiniz seçenekleri seçin. Kimlik doğrulaması için, seçenekleriniz seçtiğiniz işleve göre farklılık gösterir. Bkz. [Azure işlevlerinde kimlik doğrulamasını etkinleştirme](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Azure işlevlerinden Logic Apps 'i çağırma

Bir Azure işlevinin içinden bir mantıksal uygulama tetiklemek istediğinizde, mantıksal uygulamanın çağrılabilir bir uç nokta sağlayan bir tetikleyiciyle başlaması gerekir. Örneğin, mantıksal uygulamayı **http**, **Istek**, **Azure Kuyrukları**veya **Event Grid** tetikleyicisiyle başlatabilirsiniz. İşlevinizin içinde, tetikleyicinin URL 'sine bir HTTP POST isteği gönderin ve bu mantıksal uygulamanın işlemesini istediğiniz yükü ekleyin. Daha fazla bilgi için bkz. [çağrı, tetikleyici veya iç içe mantıksal uygulamalar](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Azure işlevleri için kimlik doğrulamasını etkinleştirme

Oturum açmak ve kimlik bilgilerini ya da gizli dizileri sağlamak zorunda kalmadan diğer Azure Active Directory (Azure AD) kiracılarındaki kaynaklara erişimin kimliğini doğrulamak için mantıksal uygulamanız [yönetilen bir kimlik](../active-directory/managed-identities-azure-resources/overview.md) (eski adıyla YÖNETILEN HIZMET KIMLIĞI veya MSI) kullanabilir. Azure bu kimliği sizin için yönetir ve gizli dizileri sağlamanız veya döndürmenize gerek olmadığından kimlik bilgilerinizin güvenliğini sağlamaya yardımcı olur. [Azure AD kimlik doğrulaması için yönetilen kimlikleri destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)hakkında daha fazla bilgi edinin.

Mantıksal uygulamanızı sistem tarafından atanan yönetilen kimliği kullanacak şekilde ayarlarsanız, mantıksal uygulamanızdaki Azure işlevleri de kimlik doğrulaması için aynı kimliği kullanabilir. Logic Apps 'te Azure işlevleri için kimlik doğrulama desteği hakkında daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

İşleviniz ile sistem tarafından atanan kimliği ayarlamak ve kullanmak için şu adımları izleyin:

1. Mantıksal uygulamanızda sistem tarafından atanan kimliği etkinleştirin ve bu kimliğin hedef kaynağa erişimini ayarlayın. Bkz. [Azure Logic Apps içindeki yönetilen kimlikleri kullanarak Azure kaynaklarına erişim kimlik doğrulaması](../logic-apps/create-managed-service-identity.md).

1. Aşağıdaki adımları izleyerek Azure işlevinizde ve işlev uygulamanızda kimlik doğrulamasını etkinleştirin:

   * [İşlevinizde anonim kimlik doğrulamasını ayarlama](#set-authentication-function-app)
   * [İşlev uygulamanızda Azure AD kimlik doğrulamasını ayarlama](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>İşlevinizde anonim kimlik doğrulamasını ayarlama

Mantıksal uygulamanızın sistem tarafından atanan kimliğini Azure işlevinizde kullanmak için, işlevinizin kimlik doğrulama düzeyini anonim olarak ayarlamış olursunuz. Aksi takdirde, mantıksal uygulamanız bir "Rozrequest" hatası oluşturur.

1. [Azure Portal](https://portal.azure.com), işlev uygulamanızı bulun ve seçin. Bu adımlar örnek işlev uygulaması olarak "FabrikamFunctionApp" kullanır.

1. İşlev uygulaması bölmesinde **platform özellikleri**' ni seçin. **Geliştirme araçları**altında **Gelişmiş araçlar (kudu)** öğesini seçin.

   ![Kudu için gelişmiş araçları aç](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Kudu Web sitesinin başlık çubuğunda, **hata ayıklama konsolu** menüsünden **cmd**' yi seçin.

   ![Hata ayıklama konsolu menüsünden "CMD" seçeneğini seçin](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Sonraki sayfa görüntülendikten sonra, klasör listesinden **site** > **Wwwroot** > ' *u*seçin. Bu adımlar örnek işlev olarak "FabrikamAzureFunction" kullanır.

   ![İşleviniz > "site" > "Wwwroot" seçin](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. `function.json` dosyasını düzenlenmek üzere açın.

   !["Function. JSON" dosyası için Düzenle 'ye tıklayın](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. `bindings` nesnesinde, `authLevel` özelliğinin mevcut olup olmadığını denetleyin. Özellik varsa, özellik değerini `anonymous`olarak ayarlayın. Aksi takdirde, bu özelliği ekleyin ve değeri ayarlayın.

   !["AuthLevel" özelliği ekleyin ve "anonim" olarak ayarlayın](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. İşiniz bittiğinde ayarlarınızı kaydedin ve sonra bir sonraki bölüme geçin.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>İşlev uygulamanız için Azure AD kimlik doğrulamasını ayarlama

Bu göreve başlamadan önce, daha sonra kullanmak üzere bu değerleri bulup yerleştirin:

* Mantıksal uygulamanızı temsil eden sistem tarafından atanan kimlik için oluşturulan nesne KIMLIĞI

  * Bu nesne KIMLIĞINI oluşturmak için [mantıksal uygulamanızın sistem tarafından atanan kimliğini etkinleştirin](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * Aksi takdirde, bu nesne KIMLIĞINI bulmak için mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın. Mantıksal uygulama menünüzde **Ayarlar**altında, **kimlik** > **sistem atandı**' ı seçin.

* Azure Active Directory (Azure AD) kiracınız için dizin KIMLIĞI

  Kiracının Dizin KIMLIĞINI almak için [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) PowerShell komutunu çalıştırabilirsiniz. Veya Azure portal, aşağıdaki adımları izleyin:

  1. [Azure Portal](https://portal.azure.com), işlev uygulamanızı bulun ve seçin.

  1. Azure AD kiracınızı bulun ve seçin. Bu adımlar örnek kiracı olarak "Fabrikam" kullanır.

  1. Kiracının menüsünde, **Yönet**altında **Özellikler**' i seçin.

  1. Örneğin, kiracınızın Dizin KIMLIĞINI kopyalayın ve daha sonra kullanmak üzere bu KIMLIĞI kaydedin.

     ![Azure AD kiracının Dizin KIMLIĞINI bulun ve kopyalayın](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* Erişmek istediğiniz hedef kaynağın kaynak KIMLIĞI

  * Bu kaynak kimliklerini bulmak için [Azure AD 'yi destekleyen Azure hizmetlerini](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)gözden geçirin.

  > [!IMPORTANT]
  > Bu kaynak KIMLIĞI, tüm gerekli eğik çizgiler de dahil olmak üzere Azure AD 'nin beklediği değerle tam olarak eşleşmelidir.

  Bu kaynak KIMLIĞI Ayrıca, [sistem tarafından atanan kimliği kullanmak üzere işlev eyleminizi ayarladığınızda](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity), daha sonra **hedef kitle** özelliğinde kullanacağınız değerdir.

Artık işlev uygulamanız için Azure AD kimlik doğrulamasını ayarlamaya hazırsınız.

1. [Azure Portal](https://portal.azure.com), işlev uygulamanızı bulun ve seçin.

1. İşlev uygulaması bölmesinde **platform özellikleri**' ni seçin. **Ağ**altında **kimlik doğrulama/yetkilendirme**' yi seçin.

   ![Kimlik doğrulama ve yetkilendirme ayarlarını görüntüleme](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. **App Service kimlik doğrulaması** ayarını **Açık**olarak değiştirin. **İsteğin kimliği doğrulanmamış olduğunda gerçekleştirilecek eylem** listesinden **Azure Active Directory oturum aç**' ı seçin. **Kimlik doğrulama sağlayıcıları**altında **Azure Active Directory**' yi seçin.

   ![Azure AD ile kimlik doğrulamayı etkinleştirme](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. **Azure Active Directory ayarları** bölmesinde, şu adımları izleyin:

   1. **Yönetim modunu** **Gelişmiş**olarak ayarlayın.

   1. **ISTEMCI kimliği** özelliğine, mantıksal uygulamanızın sistem tarafından atanan KIMLIĞININ nesne kimliğini girin.

   1. **Issuer URL** özelliğinde `https://sts.windows.net/` URL 'sini girin ve Azure AD kiracınızın dizin kimliğini ekleyin.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. **Izin verilen belirteç izleyicileri** özelliğinde, erişmek istediğiniz hedef KAYNAĞıN kaynak kimliğini girin.

      Bu kaynak KIMLIĞI, [sistem tarafından atanan kimliği kullanmak üzere işlev eyleminizi ayarladığınızda](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity), daha sonra **hedef kitle** özelliğinde kullanacağınız değerdir.

   Bu noktada sürümünüz Şu örneğe benzer şekilde görünür:

   ![Azure Active Directory kimlik doğrulama ayarları](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. İşiniz bittiğinde **Tamam**’ı seçin.

1. Mantıksal uygulama Tasarımcısına dönün ve [yönetilen kimlikle erişimin kimliğini doğrulamak için adımları](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
