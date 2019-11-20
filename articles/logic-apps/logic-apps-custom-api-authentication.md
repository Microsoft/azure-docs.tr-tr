---
title: Özel API 'lere kimlik doğrulaması ekleme-Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps özel API 'Leri çağırmak için kimlik doğrulamasını ayarlama
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: fb9f986c2711e0cbc8ac3facd073f1a72f46043d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039122"
---
# <a name="secure-calls-to-custom-apis-from-azure-logic-apps"></a>Azure Logic Apps 'den özel API 'lere güvenli çağrılar

API 'lerinize yönelik çağrıları güvenli hale getirmek için, Azure Active Directory (Azure AD) kimlik doğrulamasını Azure portal aracılığıyla ayarlayabilirsiniz, böylece kodunuzu güncelleştirmeniz gerekmez. İsterseniz, API'nizin kodu aracılığıyla kimlik doğrulamasının gerekli ve zorunlu olmasını da sağlayabilirsiniz.

## <a name="authentication-options-for-your-api"></a>API 'niz için kimlik doğrulama seçenekleri

Özel API 'nize yapılan çağrıları şu yollarla güvenli hale getirebilirsiniz:

* [Kod değişikliği yok](#no-code): apı 'nizi [Azure ACTIVE DIRECTORY (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) ile Azure Portal ile koruyun, böylece kodunuzu güncelleştirmeniz veya API 'nizi yeniden dağıtmanız gerekmez.

  > [!NOTE]
  > Varsayılan olarak, Azure portal açtığınızda Azure AD kimlik doğrulaması ayrıntılı yetkilendirme sağlamaz. Örneğin, bu kimlik doğrulaması, API 'nizi belirli bir kullanıcı veya uygulamaya değil yalnızca belirli bir kiracıya kilitler. 

* [API 'nizin kodunu güncelleştirin](#update-code): [sertifika kimlik doğrulaması](#certificate), [temel kimlik doğrulama](#basic)veya kod aracılığıyla [Azure AD kimlik doğrulamasını](#azure-ad-code) zorunlu tutarak API 'nizi koruyun.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Kodu değiştirmeden API 'nize yapılan çağrıların kimliğini doğrulama

Bu yöntemin genel adımları aşağıda verilmiştir:

1. Mantıksal uygulamanız ve bir Web uygulamanız (veya API uygulaması) için bir tane olmak üzere iki Azure Active Directory (Azure AD) uygulama kimliği oluşturun.

2. API 'nize yapılan çağrıların kimliğini doğrulamak için, mantıksal uygulamanızın Azure AD uygulama kimliğiyle ilişkili hizmet sorumlusu için kimlik bilgilerini (istemci KIMLIĞI ve gizli dizi) kullanın.

3. Uygulama kimliklerini mantıksal uygulama tanımınıza dahil edin.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>1\. kısım: mantıksal uygulamanız için bir Azure AD uygulama kimliği oluşturma

Mantıksal uygulamanız Azure AD kimlik doğrulaması için bu Azure AD uygulama kimliğini kullanır. Bu kimliği yalnızca dizininiz için bir kez ayarlamanız yeterlidir. Örneğin, her mantıksal uygulama için benzersiz kimlikler oluşturabileceğiniz halde tüm mantıksal uygulamalarınız için aynı kimliği kullanmayı seçebilirsiniz. Bu kimlikleri Azure portal ayarlayabilir veya [PowerShell](#powershell)kullanabilirsiniz.

**Azure portal mantıksal uygulamanız için uygulama kimliği oluşturma**

1. [Azure portal](https://portal.azure.com "https://portal.azure.com") **Azure Active Directory**öğesini seçin. 

2. Web uygulamanızın veya API uygulamanızın bulunduğu dizinde olduğunuzdan emin olun.

   > [!TIP]
   > Dizinleri değiştirmek için profilinizi seçin ve başka bir dizin seçin. Ya da, **genel bakış** > **Dizin Değiştir**' i seçin.

3. Dizin menüsünde, **Yönet**altında, **yeni uygulama kaydı** > **uygulama kayıtları** ' yi seçin.

   > [!TIP]
   > Varsayılan olarak, uygulama kayıt listesi, dizininizdeki tüm uygulama kayıtlarını gösterir. Yalnızca uygulama kayıtlarınızı görüntülemek için arama kutusunun yanındaki **uygulamalarım**' ı seçin. 

   ![Yeni uygulama kaydı oluştur](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Uygulama kimliğinize bir ad verin, **uygulama türünü** **Web uygulaması/API**olarak ayarlayın, **oturum açma URL 'si**için etki alanı olarak biçimlendirilen benzersiz bir dize sağlayın ve **Oluştur**' u seçin.

   ![Uygulama kimliği için ad ve oturum açma URL 'SI sağlayın](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Mantıksal uygulamanız için oluşturduğunuz uygulama kimliği artık uygulama kayıtları listesinde görünür.

   ![Mantıksal uygulamanız için uygulama kimliği](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Uygulama kayıtları listesinde, yeni uygulama kimliğinizi seçin. Bölüm 3 ' te mantıksal uygulamanız için "istemci KIMLIĞI" olarak kullanılacak **uygulama kimliğini** kopyalayın ve kaydedin.

   ![Mantıksal uygulama için uygulama KIMLIĞINI Kopyala ve Kaydet](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Uygulama kimliği ayarlarınız görünür değilse, **Ayarlar** veya **Tüm ayarlar**' ı seçin.

7. **API erişimi**altında **anahtarlar**' ı seçin. **Açıklama**altında anahtarınız için bir ad girin. **Süre sonu**altında anahtarınız için bir süre seçin.

   Oluşturmakta olduğunuz anahtar, mantıksal uygulamanız için uygulama kimliğinin "gizli" veya parola gibi davranır.

   ![Mantıksal uygulama kimliği için anahtar oluştur](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Araç çubuğunda **Kaydet**' i seçin. **Değer**altında, anahtarınız artık görüntülenir. 
**Anahtarlar** sayfasından ayrıldığınızda anahtar gizlendiğinden, daha sonra kullanmak üzere **anahtarınızı kopyalayıp kaydettiğinizden emin olun** .

   Mantıksal uygulamanızı 3. bölümde yapılandırdığınızda, bu anahtarı "gizli" veya parola olarak belirtirsiniz.

   ![Anahtarı daha sonra Kopyala ve Kaydet](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**PowerShell 'de mantıksal uygulamanız için uygulama kimliği oluşturma**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu görevi, PowerShell ile Azure Resource Manager aracılığıyla gerçekleştirebilirsiniz. PowerShell 'de şu komutları çalıştırın:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Bir parola girin ve ENTER tuşuna basın.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. **KIRACı kimliğini** (Azure AD kiracınız için GUID), **uygulama kimliğini**ve kullandığınız parolayı kopyalamadığınızdan emin olun.

Daha fazla bilgi için, [PowerShell ile kaynaklara erişmek için hizmet sorumlusu oluşturma](../active-directory/develop/howto-authenticate-service-principal-powershell.md)hakkında bilgi edinin.

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>2\. Bölüm: Web uygulamanız veya API uygulamanız için bir Azure AD uygulama kimliği oluşturma

Web uygulamanız veya API uygulamanız zaten dağıtılmışsa, kimlik doğrulamasını açabilir ve Azure portal uygulama kimliğini oluşturabilirsiniz. Aksi takdirde, [bir Azure Resource Manager şablonuyla dağıtırken kimlik doğrulamasını etkinleştirebilirsiniz](#authen-deploy). 

**Uygulama kimliği oluşturma ve dağıtılan uygulamalar için Azure portal kimlik doğrulamasını etkinleştirme**

1. [Azure Portal](https://portal.azure.com "https://portal.azure.com")Web UYGULAMANıZı veya API uygulamanızı bulun ve seçin. 

2. **Ayarlar**altında **kimlik doğrulama/yetkilendirme**' yi seçin. **App Service kimlik doğrulaması**altında kimlik doğrulaması **' nı açın.** **Kimlik doğrulama sağlayıcıları**altında **Azure Active Directory**' yi seçin.

   ![Kimlik doğrulamasını aç](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Şimdi Web uygulamanız veya API uygulamanız için burada gösterildiği gibi bir uygulama kimliği oluşturun. **Azure Active Directory ayarları** sayfasında **yönetim modunu** **Express**olarak ayarlayın. **Yenı ad uygulaması oluştur**öğesini seçin. Uygulama kimliğinize bir ad verin ve **Tamam**' ı seçin. 

   ![Web uygulamanız veya API uygulamanız için uygulama kimliği oluşturma](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. **Kimlik Doğrulama / Yetkilendirme** sayfasında **Kaydet**’i seçin.

Şimdi Web uygulamanız veya API uygulamanız ile ilişkili uygulama kimliği için istemci KIMLIĞINI ve kiracı KIMLIĞINI bulmanız gerekir. Bu kimlikleri 3. bölümde kullanın. Bu nedenle Azure portal için bu adımlarla devam edin.

**Azure portal web uygulamanız veya API uygulamanız için uygulama kimliğinin istemci KIMLIĞINI ve kiracı KIMLIĞINI bulun**

1. **Kimlik doğrulama sağlayıcıları**altında **Azure Active Directory**' yi seçin. 

   !["Azure Active Directory" seçeneğini belirleyin](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. **Azure Active Directory ayarları** sayfasında **yönetim modu** ' nu **Gelişmiş**' e ayarlayın.

3. **ISTEMCI kimliğini**kopyalayın ve bu GUID 'yi Bölüm 3 ' te kullanmak üzere kaydedin.

   > [!TIP] 
   > **ISTEMCI kimliği** ve **veren URL 'si** görünmezse, Azure Portal yenilemeyi deneyin ve 1. adımı yineleyin.

4. **Veren URL 'si**altında yalnızca 3. kısım için GUID 'yi kopyalayın ve kaydedin. Ayrıca, bu GUID 'yi Web uygulamanızda veya API uygulamanızın dağıtım şablonunda da kullanabilirsiniz.

   Bu GUID, özel kiracınızın GUID 'sidir ("Kiracı KIMLIĞI") ve şu URL 'de görünmelidir: `https://sts.windows.net/{GUID}`

5. Değişikliklerinizi kaydetmeden **Azure Active Directory ayarları** sayfasını kapatın.

<a name="authen-deploy"></a>

**Azure Resource Manager şablonuyla dağıtırken kimlik doğrulamasını etkinleştirin**

Web uygulamanız veya API uygulamanız için mantıksal uygulamanızın uygulama kimliğinden farklı bir Azure AD uygulama kimliği oluşturmanız gerekir. Uygulama kimliğini oluşturmak için, Azure portal 2. Kısım 2 ' de önceki adımları izleyin. 

Ayrıca, 1. bölüm içindeki adımları da izleyebilirsiniz, ancak **oturum açma URL** 'Si ve **uygulama kimliği URI**'si IÇIN Web uygulamanızı veya API uygulamanızın gerçek `https://{URL}` kullandığınızdan emin olun. Bu adımlardan, hem istemci KIMLIĞINI hem de kiracı KIMLIĞINI uygulamanızın dağıtım şablonunda kullanmak için ve ayrıca Bölüm 3 için kaydetmeniz gerekir.

> [!NOTE]
> Web uygulamanız veya API uygulamanız için Azure AD uygulama kimliğini oluşturduğunuzda PowerShell değil Azure portal kullanmanız gerekir. PowerShell komutu, kullanıcıları bir Web sitesinde imzalamak için gerekli izinleri ayarmıyor.

İstemci KIMLIĞI ve kiracı KIMLIĞINI aldıktan sonra, bu kimlikleri dağıtım şablonunuza Web uygulamanızın veya API uygulamanızın alt kaynağı olarak ekleyin:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Boş bir Web uygulamasını ve bir mantıksal uygulamayı Azure Active Directory kimlik doğrulamasıyla birlikte otomatik olarak dağıtmak için, [burada tüm şablonu görüntüleyin](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)veya buradan **Azure 'a dağıt** ' a tıklayın:

[![Azure’a dağıtma](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>3\. kısım: mantıksal uygulamanızdaki yetkilendirme bölümünü doldurma

Önceki şablonda zaten bu yetkilendirme bölümü ayarlanmıştır, ancak mantıksal uygulamayı doğrudan yazıyorsanız, tam yetkilendirme bölümünü dahil etmeniz gerekir.

Mantıksal uygulama tanımınızı kod görünümünde açın, **http** eylem tanımına gidin, **Yetkilendirme** bölümünü bulun ve şu özellikleri ekleyin:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Özellik | Gerekli | Açıklama | 
| -------- | -------- | ----------- | 
| tenant | Yes | Azure AD kiracısı için GUID | 
| audience | Yes | Web uygulamanız veya API uygulamanız için uygulama kimliğinden istemci KIMLIĞI olan, erişmek istediğiniz hedef kaynak için GUID | 
| clientId | Yes | Mantıksal uygulamanızın uygulama kimliğinden istemci KIMLIĞI olan, erişim isteyen istemcinin GUID 'SI | 
| gizli dizi | Yes | Erişim belirtecini isteyen istemcinin uygulama kimliğinden anahtar veya parola | 
| type | Yes | Kimlik doğrulama türü. ActiveDirectoryOAuth kimlik doğrulaması için değer `ActiveDirectoryOAuth`. | 
|||| 

Örneğin:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Kodla API çağrılarını güvenli hale getirme

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Sertifika kimlik doğrulaması

Mantıksal uygulamanızdan gelen istekleri Web uygulamanıza veya API uygulamanıza doğrulamak için istemci sertifikaları kullanabilirsiniz. Kodunuzu ayarlamak için, [TLS karşılıklı kimlik doğrulamasının nasıl yapılandırılacağını](../app-service/app-service-web-configure-tls-mutual-auth.md)öğrenin.

**Yetkilendirme** bölümünde şu özellikleri ekleyin:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Özellik | Gerekli | Açıklama |
| -------- | -------- | ----------- |
| `type` | Yes | Kimlik doğrulama türü. SSL istemci sertifikaları için değerin `ClientCertificate`olması gerekir. |
| `password` | Hayır | İstemci sertifikasına (PFX dosyası) erişim parolası |
| `pfx` | Yes | İstemci sertifikasının Base64 ile kodlanmış içeriği (PFX dosyası) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Temel kimlik doğrulama

Mantıksal uygulamanızdan Web uygulamanıza veya API uygulamanıza gelen istekleri doğrulamak için, Kullanıcı adı ve parola gibi temel kimlik doğrulaması kullanabilirsiniz. Temel kimlik doğrulaması ortak bir modeldir ve bu kimlik doğrulamasını Web uygulamanızı veya API uygulamanızı derlemek için kullanılan herhangi bir dilde kullanabilirsiniz.

**Yetkilendirme** bölümünde şu özellikleri ekleyin:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Özellik | Gerekli | Açıklama | 
| -------- | -------- | ----------- | 
| type | Yes | Kullanmak istediğiniz kimlik doğrulama türü. Temel kimlik doğrulaması için değer `Basic`olmalıdır. | 
| kullanıcı adı | Yes | Kimlik doğrulaması için kullanmak istediğiniz Kullanıcı adı | 
| password | Yes | Kimlik doğrulaması için kullanmak istediğiniz parola | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Kod aracılığıyla Azure Active Directory kimlik doğrulaması

Varsayılan olarak, Azure portal açtığınızda Azure AD kimlik doğrulaması ayrıntılı yetkilendirme sağlamaz. Örneğin, bu kimlik doğrulaması, API 'nizi belirli bir kullanıcı veya uygulamaya değil yalnızca belirli bir kiracıya kilitler. 

Kod aracılığıyla mantıksal uygulamanıza yönelik API erişimini kısıtlamak için JSON Web belirteci (JWT) olan üstbilgiyi ayıklayın. Arayanın kimliğini denetleyip eşleşmeyen istekleri reddedin.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Sonraki adımlar

* [Mantıksal uygulama iş akışlarından özel API 'Leri dağıtma ve çağırma](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
