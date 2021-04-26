---
title: 'Öğretici: kullanıcıların kimliğini doğrulama E2E'
description: Uzak API 'lere erişim de dahil olmak üzere App Service uygulamalarınızın uçtan uca güvenliğini sağlamak için App Service kimlik doğrulaması ve yetkilendirmeyi nasıl kullanacağınızı öğrenin.
keywords: app service, azure app service, authN, authZ, güvenli, güvenlik, çok katmanlı, azure active directory, azure ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e35a4abbcaf0cd2298d55c06de0ecfbef0dff354
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765818"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Öğretici: Azure App Service'te kullanıcıların kimliğini doğrulama ve kullanıcıları uçtan uca yetkilendirme

::: zone pivot="platform-windows"  

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Ayrıca, App Service [kullanıcı kimlik doğrulaması ve yetkilendirmesi](overview-authentication-authorization.md) için yerleşik destek sunar. Bu öğreticide, App Service kimlik doğrulaması ve yetkilendirmesi ile uygulamalarınızın nasıl güvenli hale getirileceği gösterilmektedir. Örnek olarak Angular.js ön ucuna sahip bir ASP.NET Core uygulaması kullanır. App Service kimlik doğrulaması ve yetkilendirmesi tüm dil çalışma zamanlarını destekler ve öğreticiyi takip ederek tercih ettiğiniz dilde nasıl uygulanacağını öğrenebilirsiniz.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) , Linux işletim sistemini kullanarak yüksek düzeyde ölçeklenebilir, kendini yayama bir Web barındırma hizmeti sağlar. Ayrıca, App Service [kullanıcı kimlik doğrulaması ve yetkilendirmesi](overview-authentication-authorization.md) için yerleşik destek sunar. Bu öğreticide, App Service kimlik doğrulaması ve yetkilendirmesi ile uygulamalarınızın nasıl güvenli hale getirileceği gösterilmektedir. Örnek olarak Angular.js ön ucuna sahip bir ASP.NET Core uygulaması kullanır. App Service kimlik doğrulaması ve yetkilendirmesi tüm dil çalışma zamanlarını destekler ve öğreticiyi takip ederek tercih ettiğiniz dilde nasıl uygulanacağını öğrenebilirsiniz.

::: zone-end

![Basit kimlik doğrulaması ve yetkilendirme](./media/tutorial-auth-aad/simple-auth.png)

Ayrıca, kimliği doğrulanmış kullanıcı adına hem [sunucu kodundan](#call-api-securely-from-server-code) hem de [tarayıcı kodundan](#call-api-securely-from-browser-code) güvenliği sağlanmış bir arka uç API’sine erişerek çok katmanlı bir uygulamanın güvenliğini sağlamayı gösterir.

![Gelişmiş kimlik doğrulaması ve yetkilendirme](./media/tutorial-auth-aad/advanced-auth.png)

Bunlar, App Service’teki olası kimlik doğrulaması ve yetkilendirme senaryolarından yalnızca bazılarıdır. 

Öğreticide bilgi edineceğiniz konuların daha kapsamlı bir listesi aşağıda verilmiştir:

> [!div class="checklist"]
> * Yerleşik kimlik doğrulaması ve yetkilendirmeyi etkinleştirme
> * Kimliği doğrulanmamış isteklere karşı uygulamaların güvenliğini sağlama
> * Azure Active Directory’yi kimlik sağlayıcısı olarak kullanma
> * Oturum açmış kullanıcının adına uzak bir uygulamaya erişme
> * Belirteç kimlik doğrulaması ile hizmetten hizmete güvenli çağrılar
> * Sunucu kodundan erişim belirteçleri kullanma
> * İstemci (tarayıcı) kodundan erişim belirteçleri kullanma

Bu öğreticideki adımları MacOS, Linux ve Windows üzerinde izleyebilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- <a href="https://git-scm.com/" target="_blank">Git'i yükleyin</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">En son .NET Core 3,1 SDK 'sını yükler</a>
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>Yerel .NET Core uygulaması oluşturma

Bu adımda, yerel .NET Core projesini ayarlarsınız. Bir arka uç API uygulaması ile ön uç web uygulamasını dağıtmak için aynı projeyi kullanırsınız.

### <a name="clone-and-run-the-sample-application"></a>Örnek uygulamayı kopyalama ve çalıştırma

Örnek depoyu kopyalamak ve çalıştırmak için aşağıdaki komutları çalıştırın.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

`http://localhost:5000` sayfasına gidip todo öğeleri ekleme, düzenleme ve kaldırma işlemlerini deneyin. 

![Yerel olarak çalışan ASP.NET Core API'si](./media/tutorial-auth-aad/local-run.png)

ASP.NET Core’u dilediğiniz zaman durdurmak için, terminalde `Ctrl+C` tuşlarına basın.

## <a name="deploy-apps-to-azure"></a>Uygulamaları Azure'a dağıtma

Bu adımda projeyi iki App Service uygulamasına dağıtacaksınız. Bunlardan biri ön uç uygulama, diğeri ise arka uç uygulamadır.

### <a name="configure-a-deployment-user"></a>Dağıtım kullanıcısı yapılandırma

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Azure kaynakları oluşturma

::: zone pivot="platform-windows"  

Cloud Shell iki Windows Web uygulaması oluşturmak için aşağıdaki komutları çalıştırın. _\<front-end-app-name>_ Ve _\<back-end-app-name>_ iki genel benzersiz uygulama adıyla değiştirin (geçerli karakterler `a-z` , `0-9` ve `-` ). Her bir komut hakkında daha fazla bilgi için bkz. [Azure App Service’te CORS ile RESTful API’si](app-service-web-tutorial-rest-api.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

::: zone pivot="platform-linux"

Cloud Shell’de iki web uygulaması oluşturmak için aşağıdaki komutları çalıştırın. _\<front-end-app-name>_ Ve _\<back-end-app-name>_ iki genel benzersiz uygulama adıyla değiştirin (geçerli karakterler `a-z` , `0-9` ve `-` ). Her komut hakkında daha fazla bilgi için, bkz. [Azure App Service bir .NET Core uygulaması oluşturma](quickstart-dotnetcore.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

> [!NOTE]
> Ön uç uygulama ve arka uç uygulamanızın Git uzak öğelerine ait URL’leri, `az webapp create` çıktısında gösterilen şekilde kaydedin.
>

### <a name="push-to-azure-from-git"></a>Git üzerinden Azure'a gönderme

_Yerel terminal penceresine_ geri dönerek, arka uç uygulamasına dağıtmak için aşağıdaki Git komutlarını çalıştırın. _\<deploymentLocalGitUrl-of-back-end-app>_ [Azure kaynakları oluştur](#create-azure-resources)listesinden kaydettiğiniz GIT uzak öğesinin URL 'siyle değiştirin. Git kimlik bilgileri Yöneticisi tarafından kimlik bilgileri istendiğinde, Azure portal oturum açmak için kullandığınız kimlik bilgilerini değil [dağıtım kimlik bilgilerinizi](deploy-configure-credentials.md)girdiğinizden emin olun.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

Yerel terminal penceresinde aynı kodu ön uç uygulamasına dağıtmak için aşağıdaki Git komutlarını çalıştırın. _\<deploymentLocalGitUrl-of-front-end-app>_ [Azure kaynakları oluştur](#create-azure-resources)listesinden kaydettiğiniz GIT uzak öğesinin URL 'siyle değiştirin.

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>Uygulamalara gidin

Bir tarayıcıda aşağıdaki URL'lere gittiğinizde iki uygulamanın çalıştığını görebilirsiniz.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

:::image type="content" source="./media/tutorial-auth-aad/azure-run.png" alt-text="Yapılacaklar listesi uygulaması gösteren bir tarayıcı penceresinde Azure App Service REST API örneğinin ekran görüntüsü.":::

> [!NOTE]
> Uygulamanız yeniden başlatılırsa, yeni verilerin silindiğini fark etmiş olabilirsiniz. Örnek ASP.NET Core uygulaması bir bellek içi veritabanı kullandığından bu davranış tasarım gereğidir.
>
>

## <a name="call-back-end-api-from-front-end"></a>Ön uçtan arka uç API’si çağırma

Bu adımda, ön uç uygulamanın sunucu kodunu arka uç API’sine erişecek şekilde yönlendireceksiniz. Daha sonra, ön uçtan arka uca kimliği doğrulanmış erişimi etkinleştireceksiniz.

### <a name="modify-front-end-code"></a>Ön uç kodunu değiştirme

Yerel depoda _Controllers/TodoController.cs_ dosyasını açın. `TodoController`Sınıfının başlangıcında, aşağıdaki satırları ekleyin ve _\<back-end-app-name>_ arka uç uygulamanızın adıyla değiştirin:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

İle donatılmış yöntemi bulun `[HttpGet]` ve küme ayraçları içindeki kodu ile değiştirin:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

İlk satır, arka uç API uygulamasına bir `GET /api/Todo` çağrısı yapar.

Sonra, ile donatılmış yöntemi bulun `[HttpGet("{id}")]` ve küme ayraçları içindeki kodu ile değiştirin:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
return Content(data, "application/json");
```

İlk satır, arka uç API uygulamasına bir `GET /api/Todo/{id}` çağrısı yapar.

Sonra, ile donatılmış yöntemi bulun `[HttpPost]` ve küme ayraçları içindeki kodu ile değiştirin:

```cs
var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
var data = await response.Content.ReadAsStringAsync();
return Content(data, "application/json");
```

İlk satır, arka uç API uygulamasına bir `POST /api/Todo` çağrısı yapar.

Sonra, ile donatılmış yöntemi bulun `[HttpPut("{id}")]` ve küme ayraçları içindeki kodu ile değiştirin:

```cs
var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
return new NoContentResult();
```

İlk satır, arka uç API uygulamasına bir `PUT /api/Todo/{id}` çağrısı yapar.

Sonra, ile donatılmış yöntemi bulun `[HttpDelete("{id}")]` ve küme ayraçları içindeki kodu ile değiştirin:

```cs
var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
return new NoContentResult();
```

İlk satır, arka uç API uygulamasına bir `DELETE /api/Todo/{id}` çağrısı yapar.

Yaptığınız tüm değişiklikleri kaydedin. Yerel terminal penceresinde, ön uç uygulamasında yaptığınız değişiklikleri aşağıdaki Git komutları ile dağıtın:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Değişikliklerinizi kontrol edin

`http://<front-end-app-name>.azurewebsites.net` sayfasına gidip `from front end 1` ve `from front end 2` gibi birkaç öğe ekleyin.

Ön uç uygulamasından eklenen öğeleri görmek için `http://<back-end-app-name>.azurewebsites.net` sayfasına gidin. Ayrıca, `from back end 1` ve `from back end 2` gibi birkaç öğe ekleyin ve ardından ön uç uygulamasını yenileyerek değişiklikleri yansıtıp yansıtmadığını görün.

:::image type="content" source="./media/tutorial-auth-aad/remote-api-call-run.png" alt-text="Bir tarayıcı penceresinde, ön uç uygulamasından eklenen öğelerle bir yapılacaklar listesi uygulaması gösteren bir Azure App Service REST API örneğinin ekran görüntüsü.":::

## <a name="configure-auth"></a>Kimlik doğrulamasını yapılandırma

Bu adımda, iki uygulama için kimlik doğrulama ve yetkilendirmeyi etkinleştireceksiniz. Ayrıca, arka uç uygulamasına kimliği doğrulanmış çağrılar yapmak için kullanabileceğiniz bir erişim belirteci oluşturacak şekilde ön uç uygulamasını yapılandıracaksınız.

Azure Active Directory’yi kimlik sağlayıcısı olarak kullanacaksınız. Daha fazla bilgi için bkz. [Azure Active Directory kimlik doğrulamasını App Service uygulamanız için yapılandırma](configure-authentication-provider-aad.md).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Arka uç uygulaması için kimlik doğrulama ve yetkilendirmeyi etkinleştirme

[Azure Portal](https://portal.azure.com) menüsünde, **kaynak grupları** ' nı seçin veya herhangi bir sayfadan *kaynak gruplarını* arayıp seçin.

**Kaynak grupları**' nda, kaynak grubunuzu bulun ve seçin. **Genel bakış** bölümünde arka uç uygulamanızın yönetim sayfasını seçin.

:::image type="content" source="./media/tutorial-auth-aad/portal-navigate-back-end.png" alt-text="Kaynak grupları penceresinin, örnek bir kaynak grubuna genel bakış ve bir arka uç uygulamasının yönetim sayfası seçili olarak gösterildiği ekran görüntüsü.":::

Arka uç uygulamanızın sol menüsünde **kimlik doğrulama/yetkilendirme**' yi seçin ve ardından **Açık**' ı seçerek App Service kimlik doğrulamasını etkinleştirin.

**İsteğin kimliği doğrulanmamış olduğunda gerçekleştirilecek eylem** menüsünde **Azure Active Directory ile oturum aç**’ı seçin.

**Kimlik doğrulama sağlayıcıları** altında **Azure Active Directory**' yi seçin.

:::image type="content" source="./media/tutorial-auth-aad/configure-auth-back-end.png" alt-text="Sağ tarafta seçili kimlik doğrulama/yetkilendirme ve ayarları gösteren arka uç uygulamasının sol menünün ekran görüntüsü.":::

**Hızlı**' i seçin, ardından yenı bir ad uygulaması oluşturmak için varsayılan ayarları kabul edin ve **Tamam**' ı seçin.

**Kimlik doğrulama/yetkilendirme** sayfasında **Kaydet**' i seçin.

İletiyle ilgili bildirimi gördüğünüzde `Successfully saved the Auth Settings for <back-end-app-name> App` Portal sayfasını yenileyin.

Yeniden **Azure Active Directory** seçin ve **Azure AD uygulaması** seçin.

Azure AD uygulamasının **ISTEMCI kimliğini** bir not defteri 'ne kopyalayın. Bu değer daha sonra gerekli olacaktır.

:::image type="content" source="./media/tutorial-auth-aad/get-application-id-back-end.png" alt-text="Azure AD Uygulaması ve kopyalanacak Istemci KIMLIĞINI gösteren Azure AD uygulamaları penceresini gösteren Azure Active Directory ayarları penceresinin ekran görüntüsü.":::

Burada durduğunuz, App Service kimlik doğrulaması ve yetkilendirmeyle zaten güvenliği sağlanmış bir bağımsız uygulamanız vardır. Kalan bölümler, kimlik doğrulamalı kullanıcıyı ön uçtan arka uca "akan" olarak bir çoklu uygulama çözümünün nasıl güvenli hale getirilacağını gösterir. 

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Ön uç uygulaması için kimlik doğrulama ve yetkilendirmeyi etkinleştirme

Ön uç uygulaması için de aynı adımları izleyin, ancak son adımı atlayın. Ön uç uygulaması için istemci KIMLIĞI gerekmez.

İsterseniz `http://<front-end-app-name>.azurewebsites.net` sayfasına gidin. Şimdi güvenli oturum açma sayfasına yönlendirilmeniz gerekir. Oturum açtıktan sonra, arka uç uygulamasının artık ön uç uygulamadan Azure Active Directory oturum açmasını gerektirdiğinden, arka uç uygulamasından *verilere erişemezsiniz*. Üç şey yapmanız gerekir:

- Arka uca ön uç erişimi verme
- App Service’i kullanılabilir bir belirteç döndürecek şekilde yapılandırma
- Belirteci kodunuzda kullanma

> [!TIP]
> Hatalarla karşılaşırsanız ve uygulamanızın kimlik doğrulaması/yetkilendirme ayarlarını yeniden yapılandırırsanız, belirteç deposundaki belirteçler yeni ayarlarla yeniden oluşturulamayabilir. Belirteçlerinizin yeniden oluşturulduğundan emin olmak için oturumu kapatıp uygulamanızda yeniden oturum açmanız gerekir. Bunu yapmanın kolay bir yolu, tarayıcınızı özel modda kullanmak ve uygulamanızdaki ayarları değiştirdikten sonra tarayıcıyı kapatıp özel modda yeniden açmaktır.

### <a name="grant-front-end-app-access-to-back-end"></a>Arka uca ön uç uygulaması erişimi verme

Her iki uygulamanızda da kimlik doğrulaması ve yetkilendirmeyi etkinleştirdikten sonra uygulamaların her biri bir AD uygulaması tarafından desteklenir. Bu adımda, ön uç uygulamasına kullanıcı adına arka uca erişme izni vereceksiniz. (Teknik açıdan, ön ucun _AD uygulamasına_ arka ucun _AD uygulaması_ için erişim izinleri vereceksiniz.)

[Azure Portal](https://portal.azure.com) menüsünde **Azure Active Directory** ' i seçin veya herhangi bir sayfadan *Azure Active Directory* ' i arayıp seçin.

**Uygulama kayıtları**  >  **sahip olan uygulamalar**  >  **Bu dizindeki tüm uygulamaları görüntüle '** yi seçin. Ön uç uygulamanızın adını seçin ve ardından **API izinleri**' ni seçin.

:::image type="content" source="./media/tutorial-auth-aad/add-api-access-front-end.png" alt-text="Sahip olunan uygulamalar, ön uç uygulama adı ve API izinleri seçili olan Microsoft-Uygulama kayıtları penceresinin ekran görüntüsü.":::

**Izin Ekle**' yi seçin, sonra **Kuruluşumun kullandığı API 'ler**' i seçin  >  **\<back-end-app-name>** .

Arka uç uygulaması için **API Izinleri iste** sayfasında, **temsilci izinleri** ve **User_impersonation** seçin ve ardından **izin Ekle**' yi seçin.

:::image type="content" source="./media/tutorial-auth-aad/select-permission-front-end.png" alt-text="Temsilci izinleri, user_impersonation ve izin Ekle düğmesinin seçili olduğunu gösteren Istek API 'SI izinleri sayfasının ekran görüntüsü.":::

### <a name="configure-app-service-to-return-a-usable-access-token"></a>App Service’i kullanılabilir bir erişim belirteci döndürecek şekilde yapılandırma

Ön uç uygulama artık, oturum açmış kullanıcı olarak arka uç uygulamasına erişmek için gerekli izinlere sahiptir. Bu adımda, App Service kimlik doğrulaması ve yetkilendirmesini, arka uca erişmeniz için kullanılabilir bir erişim belirteci verecek şekilde yapılandıracaksınız. Bu adımda, arka uç [uygulaması için kimlik doğrulama ve yetkilendirmeyi etkinleştirme](#enable-authentication-and-authorization-for-back-end-app)' den kopyaladığınız arka ucun istemci kimliği gereklidir.

[Azure Kaynak Gezgini](https://resources.azure.com) gidin ve kaynak ağacını kullanarak ön uç Web uygulamanızı bulun.

[Azure Kaynak Gezgini](https://resources.azure.com) artık kaynak ağacında seçili olan ön uç uygulamanız ile açılır. Sayfanın üst kısmındaki **Oku/Yaz**’a tıklayarak Azure kaynaklarınızın düzenlenmesini etkinleştirin.

:::image type="content" source="./media/tutorial-auth-aad/resources-enable-write.png" alt-text="Azure Kaynak Gezgini sayfasının en üstünde bulunan oku/yaz düğmesi seçili olan salt okuma ve okuma/yazma düğmelerinin ekran görüntüsü.":::

Sol tarayıcıda, **config**  >  **authsettings öğesine tıklayın** öğesine gidin.

**authsettings** görünümünde **Düzenle**’ye tıklayın. `additionalLoginParams`Kopyaladığınız ISTEMCI kimliğini kullanarak AŞAĞıDAKI JSON dizesine ayarlayın. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

:::image type="content" source="./media/tutorial-auth-aad/additional-login-params-front-end.png" alt-text="Authsettings öğesine tıklayın görünümündeki bir kod örneği ekran görüntüsü, bir istemci kimliği örneği ile additionalloginparams dizesini gösterir.":::

**PUT** öğesine tıklayarak ayarlarınızı kaydedin.

Uygulamalarınız artık yapılandırılmıştır. Ön uç artık doğru bir erişim belirteci ile arka uca erişmeye hazırdır.

Diğer sağlayıcılar için erişim belirtecinin nasıl yapılandırılacağı hakkında bilgi için bkz. [kimlik sağlayıcısı belirteçlerini yenileme](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="call-api-securely-from-server-code"></a>Sunucu kodundan güvenli bir şekilde API çağırma

Bu adımda, daha önce değiştirdiğiniz sunucu kodunu etkinleştirerek arka uç API’sine kimliği doğrulanmış çağrılar yapacaksınız.

Ön uç uygulamanız artık gerekli izne sahiptir ve ayrıca arka ucun istemci KIMLIĞINI oturum açma parametrelerine ekler. Bu nedenle, arka uç uygulaması ile kimlik doğrulaması için bir erişim belirteci elde edebilirsiniz. App Service, kimliği doğrulanmış her bir isteğe bir `X-MS-TOKEN-AAD-ACCESS-TOKEN` üst bilgisi ekleyerek sunucu kodunuza bu belirteci sağlar (bkz. [Uygulama kodunda belirteçleri alma](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!NOTE]
> Bu üst bilgiler desteklenen tüm diller için eklenir. Üst bilgilere ilgili her dil için standart deseni kullanarak erişebilirsiniz.

Yerel depoda _Controllers/TodoController.cs_ dosyasını yeniden açın. `TodoController(TodoContext context)` oluşturucusu altında aşağıdaki kodu ekleyin:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Bu kod, standart `Authorization: Bearer <access-token>` HTTP üst bilgisini tüm uzak API çağrılarına ekler. ASP.NET Core MVC istek yürütme işlem hattında, `OnActionExecuting` ilgili eylem yapılmadan hemen önce yürütülür, böylece gıden API çağrınızda her biri erişim belirtecini gösterir.

Yaptığınız tüm değişiklikleri kaydedin. Yerel terminal penceresinde, ön uç uygulamasında yaptığınız değişiklikleri aşağıdaki Git komutları ile dağıtın:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

`https://<front-end-app-name>.azurewebsites.net` oturumunu yeniden açın. Kullanıcı veri kullanımı sözleşmesi sayfasında **Kabul Et**’e tıklayın.

Artık daha önce olduğu gibi arka uç uygulamanızdan verileri oluşturabilir, okuyabilir, güncelleştirebilir ve silebilirsiniz. Şimdiki tek fark, her iki uygulamanın da, hizmetten hizmete çağrılar dahil olmak üzere, App Service kimlik doğrulama ve yetkilendirmesi ile güvenli hale getirilmesidir.

Tebrikler! Sunucu kodunuz artık kimliği doğrulanmış kullanıcı adına arka uç verilerine erişir.

## <a name="call-api-securely-from-browser-code"></a>Tarayıcı kodundan güvenli bir şekilde API çağırma

Bu adımda, ön uç Angular.js uygulamasını arka uç API’sine yönlendireceksiniz. Bu şekilde, erişim belirtecini nasıl alacağınızı ve onu kullanarak arka uç uygulamasına API çağrılarını nasıl yapacağınızı öğreneceksiniz.

Sunucu kodu istek üst bilgilerine erişebilse de, istemci kodu aynı erişim belirteçlerini almak için `GET /.auth/me` erişimine sahiptir (bkz. [Uygulama kodunda belirteçleri alma](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!TIP]
> Bu bölümde güvenli HTTP çağrılarını göstermek için standart HTTP yöntemleri kullanılır. Ancak, Angular.js uygulama deseninin basitleştirilmesine yardımcı olması için [JavaScript Için Microsoft kimlik doğrulama kitaplığı](https://github.com/AzureAD/microsoft-authentication-library-for-js) 'nı kullanabilirsiniz.
>

### <a name="configure-cors"></a>CORS Yapılandırma

Cloud Shell, komutunu kullanarak istemci URL 'nizin CORS 'yi etkinleştirin [`az webapp cors add`](/cli/azure/webapp/cors#az_webapp_cors_add) . _\<back-end-app-name>_ Ve _\<front-end-app-name>_ yer tutucularını değiştirin.

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

Bu adım, kimlik doğrulama ve yetkilendirme ile ilgili değildir. Ancak, tarayıcınızın Angular.js uygulamanızdan etki alanları arası API çağrılarına izin vermesi için gereklidir. Daha fazla bilgi için bkz. [CORS işlevselliği ekleme](app-service-web-tutorial-rest-api.md#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Angular.js uygulamasını arka uç API’sine yönlendirme

Yerel depoda _wwwroot/index.html_ dosyasını açın.

Satır 51 ' de, `apiEndpoint` değişkeni arka uç UYGULAMANıZıN https URL 'si () olarak ayarlayın `https://<back-end-app-name>.azurewebsites.net` . _\<back-end-app-name>_ App Service ' deki uygulamanızın adıyla değiştirin.

Yerel depoda _wwwroot/app/scripts/todoListSvc.js_ dosyasını açıp tüm API çağrılarının başına `apiEndpoint` ekinin getirildiğini görün. Angular.js uygulamanız artık arka uç API'lerini çağırır. 

### <a name="add-access-token-to-api-calls"></a>API çağrılarına erişim belirteci ekleme

_wwwroot/app/scripts/todoListSvc.js_ dosyasındaki API çağrıları listesinin üstünde (`getItems : function(){`. satırın üzerinde) listeye aşağıdaki işlevi ekleyin:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Bu işlev, erişim belirteci ile birlikte varsayılan `Authorization` üst bilgisini ayarlamak için çağrılır. Sonraki adımda bu belirteci çağıracaksınız.

Yerel depoda _wwwroot/app/scripts/app.js_ dosyasını açıp aşağıdaki kodu bulun:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Tüm kod bloğunu aşağıdaki kod ile değiştirin:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

Yeni değişiklik, `/.auth/me` çağrısını yapıp erişim belirtecini ayarlayan `resolve` eşlemesini ekler. `todoListCtrl` denetleyicisinin örneğini oluşturmadan önce erişim belirtecine sahip olduğunuzdan emin olur. Bu şekilde, denetleyicinin yaptığı tüm API çağrıları belirteci içerir.

### <a name="deploy-updates-and-test"></a>Güncelleştirmeleri dağıtma ve test etme

Yaptığınız tüm değişiklikleri kaydedin. Yerel terminal penceresinde, ön uç uygulamasında yaptığınız değişiklikleri aşağıdaki Git komutları ile dağıtın:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

`https://<front-end-app-name>.azurewebsites.net` sayfasına yeniden gidin. Artık doğrudan Angular.js uygulamasında veri oluşturabilir, okuyabilir, güncelleştirebilir ve silebilirsiniz.

Tebrikler! İstemci kodunuz artık kimliği doğrulanmış kullanıcı adına arka uç verilerine erişir.

## <a name="when-access-tokens-expire"></a>Erişim belirteçlerinin sona erme zamanı

Erişim belirtecinizin süresi bir süre sonra sona erer. Kullanıcıların uygulamanızla yeniden kimlik doğrulaması yapmaya gerek kalmadan erişim belirteçlerinizi yenileme hakkında daha fazla bilgi için bkz. [kimlik sağlayıcısı belirteçlerini yenileme](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Önceki adımlarda, bir kaynak grubunda Azure kaynakları oluşturdunuz. Bu kaynakların gelecekte gerekli olacağını düşünmüyorsanız, Cloud Shell’de aşağıdaki komutu çalıştırarak kaynak grubunu silin:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Bu komutun çalıştırılması bir dakika sürebilir.

<a name="next"></a>
## <a name="next-steps"></a>Sonraki adımlar

Öğrendikleriniz:

> [!div class="checklist"]
> * Yerleşik kimlik doğrulaması ve yetkilendirmeyi etkinleştirme
> * Kimliği doğrulanmamış isteklere karşı uygulamaların güvenliğini sağlama
> * Azure Active Directory’yi kimlik sağlayıcısı olarak kullanma
> * Oturum açmış kullanıcının adına uzak bir uygulamaya erişme
> * Belirteç kimlik doğrulaması ile hizmetten hizmete güvenli çağrılar
> * Sunucu kodundan erişim belirteçleri kullanma
> * İstemci (tarayıcı) kodundan erişim belirteçleri kullanma

Özel bir DNS adını uygulamanıza nasıl eşleyeceğinizi öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Mevcut bir özel DNS adını Azure App Service eşleme](app-service-web-tutorial-custom-domain.md)
