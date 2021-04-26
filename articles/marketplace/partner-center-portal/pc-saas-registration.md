---
title: SaaS uygulaması kaydetme-Azure Marketi
description: SaaS uygulamasını kaydetmek ve Azure Active Directory bir güvenlik belirteci almak için Azure portal nasıl kullanacağınızı öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: e7bcfe1afc063d89bc6a5339bf62521cd644b8ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048349"
---
# <a name="register-a-saas-application"></a>SaaS uygulaması kaydetme

Bu makalede, Microsoft [Azure Portal](https://portal.azure.com/) kullanarak bir SaaS uygulamasının nasıl kaydedileceği ve yayımcının erişim belirtecinin (Azure Active Directory erişim belirtecinin) nasıl alınacağı açıklanmaktadır. Yayımcı, SaaS uygulamasının kimliğini bir şekilde karşılama API 'Lerini çağırarak kimlik doğrulamak için bu belirteci kullanır.  Yerine getirme API 'Leri, hizmet-hizmet erişim belirteci isteği oluşturmak için Azure Active Directory (v 1.0) uç noktalarına akış vermek üzere OAuth 2,0 istemci kimlik bilgilerini kullanır.

Azure Marketi, SaaS hizmetinizin son kullanıcılar için kullandığı kimlik doğrulama yönteminde herhangi bir kısıtlama uygulamaz. Aşağıdaki akış yalnızca Azure Marketi 'nde SaaS hizmetinin kimlik doğrulaması için gereklidir.

Azure AD (Active Directory) hakkında daha fazla bilgi için bkz. [kimlik doğrulaması](../../active-directory/develop/authentication-vs-authorization.md)nedir?

## <a name="register-an-azure-ad-secured-app"></a>Azure AD ile güvenli bir uygulamayı kaydetme

Azure AD'nin özelliklerini kullanmak isteyen her uygulama önce bir Azure AD kiracısı olarak kaydedilmelidir. Bu kayıt işlemi, uygulamanız hakkında Azure AD bazı ayrıntıları vermeyi içerir. Azure portal kullanarak yeni bir uygulama kaydetmek için aşağıdaki adımları uygulayın:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Hesabınız birden fazla erişim veriyorsa, sağ üst köşedeki hesabınıza tıklayın ve Portal oturumunuzu istenen Azure AD kiracısı olarak ayarlayın.
3. Sol taraftaki Gezinti bölmesinde **Azure Active Directory** hizmetine tıklayın, **uygulama kayıtları**' a tıklayın ve **Yeni uygulama kaydı**' na tıklayın.

    ![SaaS AD uygulama kayıtları](./media/saas-offer-app-registration-v1.png)

4. Oluştur sayfasında, uygulamanızın \' kayıt bilgilerini girin:
    -   **Ad**: anlamlı bir uygulama adı girin
    -   **Uygulama türü**:  
        
        Güvenli bir sunucuda yüklü olan [istemci uygulamaları](../../active-directory/develop/developer-glossary.md#client-application)için **Web uygulaması/API** 'LERI ve [kaynak/API uygulamaları](../../active-directory/develop/developer-glossary.md#resource-server)' nı seçin. Bu ayar, OAuth gizli [Web istemcileri](../../active-directory/develop/developer-glossary.md#web-client)için ve genel [Kullanıcı Aracısı tabanlı istemciler](../../active-directory/develop/developer-glossary.md#user-agent-based-client)için kullanılır).
        Aynı uygulama gerek bir istemciyi, gerekse kaynağı/API'yi sunabilir.

        Belirli Web uygulamalarına örnek olarak, [Azure AD geliştiricileri Kılavuzu](../../active-directory/develop/index.yml)' nu [kullanmaya başlama](../../active-directory/develop/quickstart-create-new-tenant.md) bölümünde bulunan hızlı başlangıç destekli kurulumları inceleyin.

5. İşiniz bittiğinde **Kaydet**' e tıklayın.  Azure AD, yeni uygulamanıza benzersiz bir *uygulama kimliği* atar. Yalnızca API 'ye ve tek bir kiracıya erişen bir uygulamanın kaydolmalarını öneririz.

6. İstemci parolası oluşturmak için **sertifikalar & gizlilikler sayfasına** gidin ve **+ yeni istemci parolası**' na tıklayın.  Kodunuzda kullanmak için gizli değeri kopyalamadığınızdan emin olun.

**Azure AD uygulama kimliği** , yayımcı Kimliğinizle ilişkilidir, bu nedenle tüm tekliflerinizi aynı *uygulama kimliğinin* kullanıldığından emin olun.

>[!Note]
>Yayımcının Iş Ortağı Merkezi 'nde iki veya daha fazla farklı hesabı varsa, Azure AD uygulama kayıt ayrıntıları yalnızca bir hesapta kullanılabilir. Aynı kiracı KIMLIĞINI kullanan bir teklif için uygulama KIMLIĞI çifti, farklı bir yayımcı hesabı altında desteklenmez.

## <a name="how-to-get-the-publishers-authorization-token"></a>Yayımcının yetkilendirme belirtecini alma

Uygulamanızı kaydettikten sonra, program aracılığıyla yayımcının yetkilendirme belirtecini (Azure AD v1 uç noktasını kullanarak Azure AD erişim belirteci) isteyebilirsiniz. Yayımcının çeşitli SaaS karşılama API 'Lerini çağırırken bu belirteci kullanması gerekir. Bu belirteç yalnızca bir saat için geçerlidir. 

Bu belirteçler hakkında daha fazla bilgi için bkz. [Azure Active Directory erişim belirteçleri](../../active-directory/develop/access-tokens.md).  Aşağıdaki akışta v1 uç noktası belirtecinin kullanıldığını unutmayın.

### <a name="get-the-token-with-an-http-post"></a>Belirteci bir HTTP GÖNDERIMIYLE al

#### <a name="http-method"></a>HTTP yöntemi

Gönderi<br>

##### <a name="request-url"></a>*İstek URL’si* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI parametresi*

|  Parametre adı    |  Gerekli         |  Açıklama |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  Doğru      |  Kayıtlı AAD uygulamasının kiracı KIMLIĞI. |

##### <a name="request-header"></a>*İstek üst bilgisi*

|  Üst bilgi adı       |  Gerekli         |  Açıklama |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  Doğru      |  İstekle ilişkilendirilmiş içerik türü. `application/x-www-form-urlencoded` varsayılan değerdir. |

##### <a name="request-body"></a>*İstek gövdesi*

|  Özellik adı     |  Gerekli         |  Açıklama |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  Doğru      |  Verme türü. `"client_credentials"` komutunu kullanın. |
|  `client_id`       |  Doğru      |  Azure AD uygulamasıyla ilişkili istemci/uygulama tanımlayıcısı. |
|  `client_secret`   |  Doğru      |  Azure AD uygulamasıyla ilişkili gizli dizi. |
|  `resource`        |  Doğru      |  Belirtecin istendiği hedef kaynak. `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`Bu durumda Market SaaS API 'si her zaman hedef kaynak olduğundan kullanın. |

##### <a name="response"></a>*Response*

|  Ad     |  Tür         |  Description |
|  ------   |  ---------------  | ------------ |
|  200 TAMAM   |  TokenResponse    |  İstek başarılı oldu. |

##### <a name="tokenresponse"></a>*TokenResponse*

Örnek yanıt:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

`"access_token"`Yanıttaki alan değeri, `<access_token>` Tüm Saas karşılama ve Market ölçüm API 'lerini çağırırken yetkilendirme parametresi olarak geçilecektir.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD özellikli uygulamanız artık [SaaS karşılama API 'Si sürüm 2](./pc-saas-fulfillment-api-v2.md)' i kullanabilir.