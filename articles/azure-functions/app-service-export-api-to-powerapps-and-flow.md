---
title: Azure 'da barındırılan bir API 'yi PowerApps 'e verme ve Microsoft Flow | Microsoft Docs
description: App Service ' de barındırılan bir API 'yi PowerApps ve Microsoft Flow üzerinde kullanıma sunma konusuna genel bakış
services: app-service
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: app-service
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: b12d1fec9b7852835d3d5b5346d64868d2ee8c46
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082859"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Azure 'da barındırılan bir API 'yi PowerApps ve Microsoft Flow dışa aktarma

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) , verilerinize bağlanan ve platformlar arasında çalışan özel iş uygulamaları oluşturmaya ve kullanmaya yönelik bir hizmettir. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) , sık kullandığınız uygulamalar ve hizmetler arasında iş akışlarını ve iş süreçlerini otomatikleştirmenizi kolaylaştırır. Hem PowerApps hem de Microsoft Flow, Office 365, Dynamics 365, Salesforce gibi veri kaynaklarına yönelik çeşitli yerleşik bağlayıcılarla birlikte gelir. Bazı durumlarda, uygulama ve akış oluşturucuları, kuruluşları tarafından oluşturulan veri kaynaklarına ve API 'lere bağlanmak de ister.

Benzer şekilde, API 'Lerini bir kuruluşta daha geniş hale getirmek isteyen geliştiriciler, API 'Lerinin uygulama ve akış oluşturucuları tarafından kullanılabilmesini sağlayabilir. Bu konu başlığı altında, [Azure işlevleri](../azure-functions/functions-overview.md) veya [Azure App Service](../app-service/overview.md)ile oluşturulmuş bir API 'nin nasıl dışarı aktarılacağı gösterilmektedir. Oluşturulan API, PowerApps 'te kullanılan *özel bir bağlayıcı*olur ve tıpkı yerleşik bağlayıcı gibi Microsoft Flow.

> [!IMPORTANT]
> Bu makalede gösterilen API tanımı işlevselliği yalnızca [Azure işlevleri çalışma zamanı ve App Services uygulamalarının sürüm 1. x](functions-versions.md#creating-1x-apps) 'i için desteklenir. 2\. x Işlevleri, Openapı tanımlarını oluşturmak ve sürdürmek için API Management ile tümleşir. Daha fazla bilgi edinmek için bkz. [Azure API Management bir işlev Için Openapı tanımı oluşturma](functions-openapi-definition.md). 

## <a name="create-and-export-an-api-definition"></a>API tanımı oluşturma ve dışarı aktarma
Bir API 'yi dışarı aktarmadan önce, bir Openapı tanımı (eski adıyla [Swagger](https://swagger.io/) dosyası) kullanarak API 'yi tanımlamalısınız. Bu tanım, bir API’de hangi işlemlerin kullanılabildiğinin yanı sıra API için istek ve yanıt verilerinin nasıl yapılandırılması gerektiğiyle ilgili bilgileri içerir. PowerApps ve Microsoft Flow, tüm Openapı 2,0 tanımları için özel bağlayıcılar oluşturabilir. Azure Işlevleri ve Azure App Service, Openapı tanımlarını oluşturmak, barındırmak ve yönetmek için yerleşik desteğe sahiptir. Daha fazla bilgi için bkz. [Azure App Service IÇINDE CORS Ile Restuz API barındırma](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Ayrıca, bir Openapı tanımı kullanmadan PowerApps ve Microsoft Flow Kullanıcı arabiriminde özel bağlayıcılar da oluşturabilirsiniz. Daha fazla bilgi için bkz. [özel bağlayıcı (PowerApps) kaydetme ve kullanma](https://powerapps.microsoft.com/tutorials/register-custom-api/) ve [özel bağlayıcı kaydetme ve kullanma (Microsoft Flow)](/power-automate/developer/register-custom-api).

API tanımını dışarı aktarmak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com)Azure işlevleriniz veya başka bir App Service uygulamasına gidin.

    Azure Işlevleri 'ni kullanıyorsanız, işlev uygulamanızı seçin, **platform özellikleri**' ni ve ardından **API tanımı**' nı seçin.

    ![Azure Işlevleri API tanımı](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Azure App Service kullanıyorsanız, ayarlar listesinden **API tanımı** ' nı seçin.

    ![App Service API tanımı](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. **PowerApps 'e ver + Microsoft Flow** düğmesi kullanılabilir olmalıdır (yoksa, önce bir openapı tanımı oluşturmanız gerekir). Dışarı aktarma işlemine başlamak için bu düğmeye tıklayın.

    ![PowerApps + Microsoft Flow düğmesine aktar](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. **Dışarı aktarma modunu**seçin:

    **Express** , Azure Portal içinden özel bağlayıcı oluşturmanıza olanak sağlar. PowerApps veya Microsoft Flow oturum açmanızı ve hedef ortamda bağlayıcı oluşturma iznine sahip olmanızı gerektirir. Bu iki gereksinim karşılanabileceği durumlarda önerilen yaklaşım budur. Bu modu kullanıyorsanız aşağıdaki [hızlı dışarı aktarma yönergelerini kullanın](#express) ' ı izleyin.

    **El ile** , PowerApps veya Microsoft Flow portallarını kullanarak IÇERI aktardığınız API tanımını dışarı aktarmanızı sağlar. Bu, Azure kullanıcısı ve bağlayıcı oluşturma izni olan kullanıcı farklı insanlardır ya da bağlayıcının başka bir Azure kiracısında oluşturulması gerekiyorsa önerilen yaklaşımdır. Bu modu kullanıyorsanız aşağıdaki [el ile dışarı aktarma yönergelerini kullanın](#manual) .

    ![Dışarı aktarma modu](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Özel bağlayıcı, API tanımının bir *kopyasını* kullanır, bu nedenle powerapps ve Microsoft Flow uygulamada ve API tanımında değişiklik yapıp yapmayacağına hemen haberdar olmaz. Değişiklik yaparsanız, yeni sürüm için dışarı aktarma adımlarını yineleyin.

<a name="express"></a>
## <a name="use-express-export"></a>Express dışarı aktarmayı kullanma

Dışarı aktarmayı **hızlı** modda gerçekleştirmek için şu adımları izleyin:

1. Dışarı aktarmak istediğiniz PowerApps veya Microsoft Flow kiracısında oturum açtığınızdan emin olun. 

2. Tabloda belirtilen ayarları kullanın.

    |Ayar|Açıklama|
    |--------|------------|
    |**Ortam**|Özel bağlayıcının kaydedileceği ortamı seçin. Daha fazla bilgi için bkz. [ortamlara genel bakış](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Özel API adı**|PowerApps ve Microsoft Flow oluşturucuların bağlayıcı listesinde göreceği bir ad girin.|
    |**Güvenlik yapılandırmasını hazırla**|Gerekirse, API 'nize Kullanıcı erişimi sağlamak için gereken güvenlik yapılandırma ayrıntılarını sağlayın. Bu örnekte bir API anahtarı gösterilmektedir. Daha fazla bilgi için bkz. [kimlik doğrulama türünü belirtin](#auth) .|
 
    ![PowerApps ve Microsoft Flow Express dışa aktarma](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. **OK (Tamam)** düğmesine tıklayın. Özel bağlayıcı artık kurulmuştur ve belirlediğiniz ortama eklenir.

<a name="manual"></a>
## <a name="use-manual-export"></a>El ile dışarı aktarma kullan

Dışarı aktarmayı **el ile** gerçekleştirmek için şu adımları izleyin:

1. **İndir** ' e tıklayın ve dosyayı kaydedin ya da Kopyala düğmesine tıklayıp URL 'yi kaydedin. İndirme dosyasını veya içeri aktarma sırasında URL 'YI kullanacaksınız.
 
    ![PowerApps ve Microsoft Flow el ile dışarı aktarma](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. API tanımınızda herhangi bir güvenlik tanımı varsa, bunlar adım #2 ' de çağrılır. İçeri aktarma sırasında PowerApps ve Microsoft Flow bunları algılar ve güvenlik bilgilerini ister. Bir sonraki bölümde kullanılmak üzere her tanım ile ilgili kimlik bilgilerini toplayın. Daha fazla bilgi için bkz. [kimlik doğrulama türünü belirtin](#auth) .

    ![El ile dışarı aktarma için güvenlik](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Bu örnek, Openapı tanımına dahil edilen API anahtarı güvenlik tanımını gösterir.

API tanımını dışarı aktardığınıza göre, PowerApps 'te özel bir bağlayıcı oluşturmak ve Microsoft Flow için içeri aktarırsınız. Özel Bağlayıcılar iki hizmet arasında paylaşılır, bu nedenle yalnızca tanımı bir kez içeri aktarmanız gerekir.

API tanımını PowerApps ve Microsoft Flow aktarmak için şu adımları izleyin:

1. [powerapps.com](https://web.powerapps.com) veya [flow.microsoft.com](https://flow.microsoft.com) adresine gidin.

2. Sağ üst köşede dişli simgesine ve ardından **özel bağlayıcılar**' a tıklayın.

   ![Hizmetteki dişli simgesi](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. **Özel bağlayıcı oluştur**' a tıklayın ve ardından **Openapı tanımını içeri aktar**' a tıklayın.

   ![Özel bağlayıcı oluşturma](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Özel bağlayıcı için bir ad girin, ardından verdiğiniz Openapı tanımına gidin ve **devam**' a tıklayın.

   ![Openapı tanımını karşıya yükle](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. **Genel** sekmesinde, openapı tanımından gelen bilgileri gözden geçirin.

5. **Güvenlik** sekmesinde, kimlik doğrulama ayrıntılarını sağlamanız istenirse, kimlik doğrulama türü için uygun değerleri girin. **Devam**’a tıklayın.

    ![Güvenlik sekmesi](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Bu örnek, API anahtarı kimlik doğrulaması için gerekli alanları gösterir. Alanlar, kimlik doğrulama türüne göre farklılık gösterir.

6. **Tanımlar** sekmesinde, openapı dosyanızda tanımlanan tüm işlemler otomatik olarak doldurulur. Tüm gerekli işlemlerinizin tanımlanması durumunda, bir sonraki adıma gidebilirsiniz. Aksi takdirde, işlemleri buradan ekleyebilir ve değiştirebilirsiniz.

    ![Tanımlar sekmesi](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Bu örnekte `CalculateCosts`adlı bir işlem vardır. **Description**gibi metaveri, openapı dosyasından gelir.

7. Sayfanın üst kısmındaki **bağlayıcı oluştur** ' a tıklayın.

Artık PowerApps ve Microsoft Flow içindeki özel bağlayıcıya bağlanabilirsiniz. PowerApps ve Microsoft Flow portallarında bağlayıcı oluşturma hakkında daha fazla bilgi için bkz. [özel bağlayıcınızı kaydetme (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) ve [özel bağlayıcınızı kaydetme (Microsoft Flow)](/power-automate/get-started-flow-dev#create-a-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Kimlik doğrulaması türünü belirtme

PowerApps ve Microsoft Flow, özel bağlayıcılar için kimlik doğrulaması sağlayan bir kimlik sağlayıcıları koleksiyonunu destekler. API 'niz kimlik doğrulaması gerektiriyorsa, aşağıdaki örnekte olduğu gibi Openapı belgenizde bir _Güvenlik tanımı_ olarak yakalandığından emin olun:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Dışa aktarma sırasında, PowerApps ve Microsoft Flow kullanıcıların kimliğini doğrulamak için izin veren yapılandırma değerleri sağlarsınız.

Bu bölüm **Express** modunda desteklenen kimlik doğrulama türlerini, Azure Active Directory ve genel OAuth 2,0 ' i içerir. PowerApps ve Microsoft Flow Dropbox, Facebook ve SalesForce gibi belirli hizmetler için de temel kimlik doğrulamasını ve OAuth 2,0 'yi destekler.

### <a name="api-key"></a>API anahtarı
Bir API anahtarı kullanırken, bağlayıcınızın kullanıcılarına bir bağlantı oluşturduklarında anahtarı sağlaması istenir. Hangi anahtarın gerekli olduğunu anlamalarına yardımcı olmak için bir API anahtarı adı belirtirsiniz. Önceki örnekte, ad `API Key (contact meganb@contoso.com)` kullandığımızda API anahtarı hakkında bilgilerin nereden alınacağını öğreniyoruz. Azure Işlevleri için anahtar, genellikle işlev uygulaması içindeki çeşitli işlevleri kapsayan ana bilgisayar anahtarlarından biridir.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Azure AD kullanırken iki Azure AD uygulama kaydı gerekir: biri API kendisi için ve diğeri özel bağlayıcı için:

- API 'nin kaydını yapılandırmak için [App Service kimlik doğrulaması/yetkilendirme](../app-service/configure-authentication-provider-aad.md) özelliğini kullanın.

- Bağlayıcının kaydını yapılandırmak için, [Azure AD uygulaması ekleme](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)bölümündeki adımları izleyin. Kayıt, API 'nize temsilci erişimi ve `https://msmanaged-na.consent.azure-apim.net/redirect`yanıt URL 'sine sahip olmalıdır. 

Daha fazla bilgi için bkz. [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) IÇIN Azure AD kayıt örnekleri ve [Microsoft Flow](https://docs.microsoft.com/connectors/custom-connectors/azure-active-directory-authentication). Bu örnekler, API olarak Azure Resource Manager kullanır; adımları izlerseniz API 'nizi değiştirin.

Aşağıdaki yapılandırma değerleri gereklidir:
- **ISTEMCI kimliği** -bağlayıcınızın Istemci KIMLIĞI Azure ad kaydı
- **İstemci gizli anahtarı** -bağlayıcınızın Istemci sırrı Azure ad kaydı
- **Oturum açma URL 'si** -Azure AD 'nin temel URL 'si. Azure 'da bu genellikle `https://login.windows.net`.
- **KIRACı kimliği** -oturum açma için kullanılacak KIRACıNıN kimliği. Bu, "ortak" ya da bağlayıcının oluşturulduğu kiracının KIMLIĞI olmalıdır.
- **Kaynak URL 'si** -API 'Niz IÇIN Azure ad kaydı 'nın kaynak URL 'si

> [!IMPORTANT]
> Başka biri de API tanımını PowerApps 'e aktarabilir ve el ile akışının bir parçası olarak Microsoft Flow, bu istemcilere *bağlayıcı kaydının*istemci kimliği ve istemci GIZLILIĞINI ve API 'NIZIN kaynak URL 'sini sağlamanız gerekir. Bu parolaların güvenli şekilde yönetildiğinden emin olun. **API 'nin güvenlik kimlik bilgilerini paylaşmayın.**

### <a name="generic-oauth-20"></a>Genel OAuth 2,0
Genel OAuth 2,0 kullanılırken, herhangi bir OAuth 2,0 sağlayıcısıyla tümleştirilebilir. Bu, yerel olarak desteklenmeyen özel sağlayıcılarla çalışmanıza olanak sağlar.

Aşağıdaki yapılandırma değerleri gereklidir:
- **ISTEMCI kimliği** -OAuth 2,0 istemci kimliği
- **İstemci parolası** -OAuth 2,0 istemci parolası
- **Yetkilendirme URL 'si** -OAuth 2,0 yetkilendirme URL 'si
- **Belirteç URL 'si** -OAuth 2,0 belirteç URL 'si
- **URL 'Yi Yenile** -OAuth 2,0 yenileme URL 'si


