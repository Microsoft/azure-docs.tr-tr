---
title: SaaS uygulaması kaydetme | Azure Marketi
description: Azure portal kullanarak bir SaaS uygulamasının nasıl kaydedileceği açıklanmaktadır.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: b2e02e42ab63f893574ca5217fd2f36c7481aabd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827950"
---
# <a name="register-a-saas-application"></a>SaaS uygulaması kaydetme

Bu makalede, Microsoft [Azure Portal](https://portal.azure.com/)kullanarak bir SaaS uygulamasının nasıl kaydedileceği açıklanmaktadır.  Başarılı bir kayıt sonrasında SaaS 'e yönelik API 'Lere erişmek için kullanabileceğiniz bir Azure Active Directory (Azure AD) güvenlik belirteci alacaksınız.  Azure AD hakkında daha fazla bilgi için bkz. [kimlik doğrulaması nedir?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Hizmetten hizmete kimlik doğrulama akışı

Aşağıdaki diyagramda, yeni bir müşterinin abonelik akışı ve bu API 'Ler kullanıldığında gösterilmektedir:

![SaaS teklifi API akışı](./media/saas-offer-publish-api-flow-v1.png)

Azure, SaaS hizmetinin son kullanıcılarına sunduğu kimlik doğrulaması üzerinde herhangi bir kısıtlama uygulamaz. Bununla birlikte, SaaS yerine getirme API 'Leri ile kimlik doğrulaması, genellikle SaaS uygulamasını Azure portal aracılığıyla kaydederek elde edilen bir Azure AD güvenlik belirteciyle gerçekleştirilir. 


## <a name="register-an-azure-ad-secured-app"></a>Azure AD ile güvenli bir uygulamayı kaydetme

Azure AD'nin özelliklerini kullanmak isteyen her uygulama önce bir Azure AD kiracısı olarak kaydedilmelidir. Bu kayıt işlemi, uygulamanız hakkında, bulunduğu URL gibi Azure AD ayrıntılarının yanı sıra bir kullanıcının kimlik doğrulamasından sonra yanıtların gönderileceği URL 'yi, uygulamayı tanımlayan URI 'yi ve benzerlerini içerir.  Azure portal kullanarak yeni bir uygulama kaydetmek için aşağıdaki adımları uygulayın:

1.  [Azure portalında](https://portal.azure.com/) oturum açın.
2.  Hesabınız birden fazla erişim veriyorsa, sağ üst köşedeki hesabınıza tıklayın ve Portal oturumunuzu istenen Azure AD kiracısı olarak ayarlayın.
3.  Sol taraftaki Gezinti bölmesinde **Azure Active Directory** hizmetine tıklayın, **uygulama kayıtları**' a tıklayın ve **Yeni uygulama kaydı**' na tıklayın.

    ![SaaS AD uygulama kayıtları](./media/saas-offer-app-registration-v1.png)

4.  Oluştur sayfasında,\'uygulamanızın kayıt bilgilerini girin:
    -   **Ad**: anlamlı bir uygulama adı girin
    -   **Uygulama türü**: 
        - Bir cihaza yerel olarak yüklenen **istemci uygulamaları** için [Yerel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)'i seçin. Bu ayar OAuth ortak [yerel istemcileri](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) için kullanılır.
        - Güvenli bir sunucuda yüklü olan [istemci uygulamaları](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) ve [kaynak/API uygulamaları](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) IÇIN **Web uygulaması/API** ' yi seçin. Bu ayar, OAuth gizli [Web istemcileri](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) ve genel [Kullanıcı Aracısı tabanlı istemciler](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)için kullanılır.
        Aynı uygulama gerek bir istemciyi, gerekse kaynağı/API'yi sunabilir.
    -   **Oturum açma URL 'si**: Web UYGULAMASı/API uygulamaları için uygulamanızın temel URL 'sini sağlayın. Örneğin, **http://localhost:31544** yerel makinenizde çalışan bir Web uygulamasının URL 'si olabilir. Kullanıcılar bu URL 'yi bir Web istemcisi uygulamasında oturum açmak için kullanır.
    -   **Yeniden yönlendirme URI 'si**: yerel uygulamalar için, belirteç yanıtlarını döndürmek üzere Azure AD tarafından kullanılan URI 'yi sağlayın. Uygulamanıza özgü bir değer girin, örneğin **http://MyFirstAADApp** .

        ![SaaS AD uygulama kayıtları](./media/saas-offer-app-registration-v1-2.png)

        Web uygulamaları veya yerel uygulamalar için belirli örnekler için, [Azure AD geliştiricileri Kılavuzu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)' nu *kullanmaya başlama* bölümünde bulunan hızlı başlangıç destekli kurulumları inceleyin.

5.  Tamamladığınızda **Oluştur**’a tıklayın. Azure AD uygulamanıza benzersiz bir *uygulama kimliği* atar ve uygulama\'ana kayıt sayfasına\'. Web uygulaması ya da yerel uygulama olmasına bağlı olarak uygulamanıza ek özellikler eklemek için değişik seçenekler sunulur.

>[!Note]
>Varsayılan olarak, yeni kaydedilen uygulama, yalnızca aynı Kiracıdaki kullanıcıların uygulamanızda oturum açmasını sağlamak üzere yapılandırılmıştır.


## <a name="using-the-azure-ad-security-token"></a>Azure AD güvenlik belirtecini kullanma

Uygulamanızı kaydettikten sonra programlı bir şekilde Azure AD güvenlik belirteci isteyebilirsiniz.  Yayımcının bu belirteci kullanması bekleniyordu ve çözümü çözümlemek için bir istek oluşturun.  Çeşitli karşılama API 'Leri kullanılırken, Kullanıcı Azure 'dan SaaS Web sitesine yeniden yönlendirildiğinde belirteç sorgu parametresi URL 'de bulunur.  Bu belirteç yalnızca bir saat için geçerlidir.  Ayrıca, kullanmadan önce tarayıcıdan belirteç değerinin kodunu çözmelisiniz.

Bu belirteçler hakkında daha fazla bilgi için bkz. [Azure Active Directory erişim belirteçleri](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Azure AD uygulaması 'nı temel alan bir belirteç alın

HTTP yöntemi

`POST`

*İstek URL'si*

**https://login.microsoftonline.com/ *{Tenantıd}* /OAuth2/Token**

*URI parametresi*

|  **Parametre adı**  | **Gerekli**  | **Açıklama**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| Değerine             | True          | Kayıtlı AAD uygulamasının kiracı KIMLIĞI   |
|  |  |  |


*İstek üst bilgisi*

|  **Üst bilgi adı**  | **Gerekli** |  **Açıklama**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  İçerik türü     | True         | İstekle ilişkilendirilmiş içerik türü. Varsayılan değer `application/x-www-form-urlencoded` ' dır.  |
|  |  |  |


*İstek gövdesi*

| **Özellik adı**   | **Gerekli** |  **Açıklama**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Verme türü. Varsayılan değer `client_credentials` ' dır.                    |
|  Client_id          | True         |  Azure AD uygulamasıyla ilişkili istemci/uygulama tanımlayıcısı.                  |
|  client_secret      | True         |  Azure AD uygulamasıyla ilişkili parola.                               |
|  Kaynak           | True         |  Belirtecin istendiği hedef kaynak. Varsayılan değer `62d94f6c-d599-489b-a797-3e10e42fbe22` ' dır. |
|  |  |  |


*Yanıt*

|  **Ad**  | **Tür**       |  **Açıklama**    |
| ---------- | -------------  | ------------------- |
| 200 TAMAM    | TokenResponse  | İstek başarılı oldu   |
|  |  |  |

*TokenResponse*

Örnek yanıt belirteci:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Sonraki adımlar

Azure AD özellikli uygulamanız artık [SaaS karşılama API 'Si sürüm 2](./pc-saas-fulfillment-api-v2.md)' i kullanabilir.
