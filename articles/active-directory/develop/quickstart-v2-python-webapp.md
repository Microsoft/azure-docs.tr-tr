---
title: Microsoft Identity platform Python web uygulaması hızlı başlangıç | Mavisi
description: OAuth2 kullanarak bir Python web uygulamasında Microsoft oturum açma uygulamasını nasıl uygulayacağınızı öğrenin
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: d9349391ad9af1a4ec1c84b586f825f3f7632ff8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73815763"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Hızlı başlangıç: Microsoft 'a Python web uygulamasına oturum açma ekleme

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Bu hızlı başlangıçta, bir Python web uygulamasını Microsoft Identity platformu ile tümleştirmeyi öğreneceksiniz. Uygulamanız bir kullanıcıya oturum açacaktır, Microsoft Graph API 'sini çağırmak için bir erişim belirteci alır ve Microsoft Graph API 'sine bir istek yapar.

Kılavuzu tamamladığınızda, uygulamanız kişisel Microsoft hesaplarının (outlook.com, live.com ve diğerleri dahil) ve Azure Active Directory kullanan herhangi bir şirketten veya kuruluştan iş veya okul hesapları için oturum açma işlemlerini kabul eder.

![Bu hızlı başlangıç tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Ön koşullar

Bu örneği çalıştırmak için şunlar gerekir:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) veya [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-oturum](https://pythonhosted.org/Flask-Session/), [istekler](https://requests.kennethreitz.org//en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Hızlı başlangıç uygulamanızı kaydetme ve indirme
>
> Hızlı başlangıç uygulamanızı başlatmak için iki seçeneğiniz vardır: Express (seçenek 1) ve el ile (seçenek 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. Seçenek: Uygulamanızı otomatik olarak kaydedip yapılandırın ve ardından kod örneğinizi indirin
>
> 1. [Azure portal uygulama kayıtları](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)gidin.
> 1. **Yeni kayıt**seçeneğini belirleyin.
> 1. Uygulamanız için bir ad girin ve **Kaydet**'i seçin.
> 1. Yeni uygulamanızı indirip otomatik olarak yapılandırmak için yönergeleri izleyin.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. Seçenek: Uygulamanızı ve kod örneğinizi el ile kaydetme ve yapılandırma
>
> #### <a name="step-1-register-your-application"></a>1\. Adım: Uygulamanızı kaydetme
>
> Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze el ile eklemek için şu adımları izleyin:
>
> 1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
> 1. Hesabınız birden fazla Azure AD kiracısına erişim sunuyorsa sağ üst köşeden hesabınızı seçin ve portal oturumunuzu kullanmak istediğiniz Azure AD kiracısına ayarlayın.
> 1. Geliştiriciler için Microsoft Identity platformu [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin.
> 1. **Yeni kayıt**seçeneğini belirleyin.
> 1. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin:
>      - **Ad** alanına uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `python-webapp`.
>      - **Desteklenen hesap türleri**altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
>      - **Yeniden yönlendirme URI 'si** bölümünde, açılan listede, **Web** platformunu seçin ve ardından değeri `http://localhost:5000/getAToken`olarak ayarlayın.
>      - **Kaydol**’u seçin. Uygulamaya **genel bakış** sayfasında, daha sonra kullanılmak üzere **uygulama (istemci) kimliği** değerini aklınızda edin.
> 1. Sol taraftaki menüde **sertifikalar & gizlilikler** ' ı seçin ve **istemci** gizli dizileri bölümünde **yeni istemci parolası** ' na tıklayın:
>
>      - Bir anahtar açıklaması (örnek uygulama gizli anahtarı) yazın.
>      - **1 yılda**bir anahtar süresi seçin.
>      - **Ekle**' ye tıkladığınızda, anahtar değeri görüntülenecektir.
>      - Anahtarın değerini kopyalayın. Buna daha sonra ihtiyacınız olacak.
> 1. **API izinleri** bölümünü seçin
>
>      - **Izin Ekle** düğmesine tıklayın ve ardından
>      - **Microsoft API 'leri** sekmesinin seçili olduğundan emin olun
>      - *Yaygın olarak kullanılan Microsoft API 'leri* bölümünde, **Microsoft Graph** ' ye tıklayın.
>      - **Temsilci izinleri** bölümünde, doğru izinlerin işaretli olduğundan emin olun: **User. Readbasic. All**. Gerekirse arama kutusunu kullanın.
>      - **Izin Ekle** düğmesini seçin
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1\. Adım: Uygulamanızı Azure portalında yapılandırma
>
> Bu hızlı başlangıçta çalışması için kod örneği için şunları yapmanız gerekir:
>
> 1. `http://localhost:5000/getAToken`olarak bir yanıt URL 'SI ekleyin.
> 1. Bir Istemci gizli dizisi oluşturun.
> 1. Microsoft Graph API 'nin User. ReadBasic. All temsilci izinleri ekleyin.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişiklikleri benim için yap]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-aspnet-webapp/green-check.png) Uygulamanız bu öznitelikle yapılandırılmış

#### <a name="step-2-download-your-project"></a>2\. Adım: Projenizi indirme

[Kod örneğini indirin](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>3\. Adım: uygulamayı yapılandırma

1. Zip dosyasını kök klasöre yakın bir yerel klasöre (örneğin **C:\Azure-Samples**) açın
1. Tümleşik bir geliştirme ortamı kullanıyorsanız, örneği en sevdiğiniz IDE (isteğe bağlı) içinde açın.
1. Kök klasörde bulunan **app_config. Kopyala** dosyasını açın ve aşağıdaki kod parçacığı ile değiştirin:

```python
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
```

> [!div renderon="docs"]
> Konumlar:
>
> - `Enter_the_Application_Id_here` - Kaydettiğiniz uygulamanın Uygulama Kimliği değeridir.
> - `Enter_the_Client_Secret_Here`, **sertifikalarında** oluşturduğunuz ve kaydettiğiniz uygulamanın gizli dizileri & **gizli anahtar olan istemci sırrı** .
> - `Enter_the_Tenant_Name_Here`-kaydettiğiniz uygulamanın **Dizin (kiracı) kimlik** değeridir.

#### <a name="step-4-run-the-code-sample"></a>4\. Adım: kod örneğini çalıştırma

1. Sunucu tarafı oturum yönetimi için MSAL Python kitaplığını, Flask çerçevesini, Flask oturumlarını ve PIP 'yi şu şekilde kullanarak istekleri yüklemeniz gerekir:

   ```Shell
   pip install -r requirements.txt
   ```

2. App.py from Shell veya komut satırından Çalıştır:

   ```Shell
   python app.py
   ```
   > [!IMPORTANT]
   > Bu hızlı başlangıç uygulaması, kendisini gizli istemci olarak tanımlamak için bir istemci gizli anahtarı kullanır. İstemci parolası proje dosyalarınıza düz metin olarak eklendiğinden, güvenlik nedenleriyle, uygulamayı üretim uygulaması olarak düşünmeden önce istemci parolası yerine bir sertifika kullanmanız önerilir. Sertifika kullanma hakkında daha fazla bilgi için [Bu yönergelere](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials)bakın.

## <a name="more-information"></a>Daha fazla bilgi

### <a name="getting-msal"></a>MSAL alma
MSAL, kullanıcıların oturum açması ve Microsoft Identity platformu tarafından korunan bir API 'ye erişmek için kullanılan belirteçleri istemek için kullanılan bir kitaplıktır.
PIP kullanarak uygulamanıza MSAL Python ekleyebilirsiniz.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>MSAL başlatma
MSAL kullanacağınız dosyanın en üstüne aşağıdaki kodu ekleyerek MSAL Python öğesine başvuru ekleyebilirsiniz:

```Python
import msal
```

## <a name="next-steps"></a>Sonraki adımlar

Kullanıcılara oturum açma ve sonra Web API 'Lerini çağıran Web Apps hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Senaryo: kullanıcılar oturum açtığında Web Apps](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
