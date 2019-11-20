---
title: Android 'de aracılı kimlik doğrulaması | Mavisi
description: Microsoft Identity platformunda Android için aracılı kimlik doğrulama & yetkilendirmesi 'ne genel bakış
services: active-directory
documentationcenter: ''
author: shoatman
manager: nadima
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a535cbefc3520cbf0c0fc14fbcfd0dd9ebd92ac
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175649"
---
# <a name="brokered-auth-in-android"></a>Android 'de aracılı kimlik doğrulaması

## <a name="introduction"></a>Tanıtım

Cihaz genelinde çoklu oturum açma 'ya (SSO) katılmak ve kuruluş koşullu erişim ilkelerini karşılamak için Microsoft 'un kimlik doğrulama aracılarından birini kullanmanız gerekir. Bir aracı ile tümleştirme aşağıdaki avantajları sağlar:

- Cihaz çoklu oturum açma
- İçin koşullu erişim:
  - Intune Uygulama Koruması
  - Cihaz kaydı (Workplace Join)
  - Mobil Cihaz Yönetimi
- Cihaz genelinde hesap yönetimi
  -  Android AccountManager & hesap ayarlarını kullanarak
  - "İş hesabı"-özel hesap türü

Android 'de, Microsoft kimlik doğrulama Aracısı, [Microsoft Authenticator uygulama](https://play.google.com/store/apps/details?id=com.azure.authenticator) ve [Intune şirket portalı](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) birlikte bulunan bir bileşendir.

> [!TIP]
> Tek seferde aracı olarak yalnızca aracıyı barındıran bir uygulama etkin olur. Aracı olarak etkin olan uygulama, cihazdaki yükleme sırasına göre belirlenir. İlk yüklenecek olan veya cihazda bulunan son, etkin aracı olur.

Aşağıdaki diyagramda, uygulamanız, Microsoft kimlik doğrulama kitaplığı (MSAL) ve Microsoft 'un kimlik doğrulama aracıları arasındaki ilişki gösterilmektedir.

![Aracı Dağıtım diyagramı](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Aracı barındıran uygulamalar yükleme

Aracı barındırma uygulamaları, cihaz sahibi tarafından, uygulama mağazalarından (genellikle Google Play Store) herhangi bir zamanda yüklenebilir. Ancak bazı API 'Ler (kaynaklar), cihazların şunları gerektiren koşullu erişim Ilkeleriyle korunmaktadır:

- kayıtlı (çalışma alanına katılmış) ve/veya
- Cihaz yönetimine kaydolmuş veya
- Intune Uygulama Koruması kayıtlı

Bir cihazda zaten yüklü bir aracı uygulaması yoksa, MSAL, uygulamanın bir belirteci etkileşimli olarak almaya çalıştığı anda kullanıcıyı bir tane yüklemesini ister. Daha sonra uygulamanın, cihazı gerekli ilkeyle uyumlu hale getirmek için kullanıcıyı bu adımları izleyerek yapması gerekir.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Aracıyı yükleme ve kaldırma etkileri

### <a name="when-a-broker-is-installed"></a>Bir aracı yüklendiğinde

Bir aracı bir cihaza yüklendiğinde, sonraki tüm etkileşimli Belirteç istekleri (`acquireToken()`çağrıları), MSAL tarafından yerel olarak değil, aracı tarafından işlenir. Daha önce MSAL için kullanılabilir olan tüm SSO durumları, aracıda kullanılamaz. Sonuç olarak, kullanıcının yeniden kimlik doğrulaması yapması veya cihaz tarafından bilinen mevcut hesapların listesinden bir hesap seçmeniz gerekir.

Bir aracı yüklemek için kullanıcının yeniden oturum açması gerekmez. Yalnızca kullanıcının bir `MsalUiRequiredException` çözümlemesi gerektiğinde, bir sonraki istek aracıya gider. `MsalUiRequiredException` birkaç nedenden dolayı oluşturulur ve etkileşimli olarak çözülmesi gerekir. Bunlar bazı yaygın nedenlerdir:

- Kullanıcı, hesabıyla ilişkili parolayı değiştirdi.
- Kullanıcının hesabı artık bir koşullu erişim ilkesini karşılamamaktadır.
- Kullanıcı, uygulamanın hesabıyla ilişkilendirilmesi için onayını iptal etti.

### <a name="when-a-broker-is-uninstalled"></a>Bir aracı kaldırıldığında

Yalnızca bir aracı barındırma uygulaması yüklüyse ve kaldırılırsa, kullanıcının yeniden oturum açması gerekecektir. Etkin Aracıyı kaldırmak, hesabı ve ilişkili belirteçleri cihazdan kaldırır.

Intune Şirket Portalı yüklenip etkin aracı olarak çalışıyorsa ve Microsoft Authenticator da yüklüyse, Intune Şirket Portalı (etkin aracı) kaldırılırsa, kullanıcının yeniden oturum açması gerekir. Yeniden oturum açtıklarında, Microsoft Authenticator uygulama etkin aracı olur.

## <a name="integrating-with-a-broker"></a>Bir aracı ile tümleştirme

### <a name="generating-a-redirect-uri-for-a-broker"></a>Bir aracı için yeniden yönlendirme URI 'SI oluşturma

Aracıda uyumlu bir yeniden yönlendirme URI 'SI kaydetmeniz gerekir. Aracının yeniden yönlendirme URI 'sinin uygulamanızın paket adının yanı sıra, uygulamanızın imzasının Base64 kodlamalı gösterimini içermesi gerekir.

Yeniden yönlendirme URI 'sinin biçimi: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Uygulamanızın imzalama anahtarlarını kullanarak Base64 URL kodlamalı imzanızı oluşturun. Hata ayıklama imzalama anahtarlarınızı kullanan bazı örnek komutlar aşağıda verilmiştir:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Uygulamanızı imzalama hakkında [bilgi için bkz. uygulamanızı imzalama](https://developer.android.com/studio/publish/app-signing) .

> [!IMPORTANT]
> Uygulamanızın üretim sürümü için üretim imzalama anahtarınızı kullanın.

### <a name="configure-msal-to-use-a-broker"></a>Aracıyı kullanmak için MSAL yapılandırma

Uygulamanızda bir aracı kullanmak için, aracı yeniden yönlendirmeyi yapılandırdığınız için test etmeniz gerekir. Örneğin, hem aracının etkin yeniden yönlendirme URI 'sini, hem de MSAL yapılandırma dosyanıza aşağıdakileri ekleyerek kaydettiğiniz olduğunu belirtin:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> Yeni Azure portal uygulama kaydı kullanıcı arabirimi, aracı yeniden yönlendirme URI 'sini oluşturmanıza yardımcı olur. Uygulamanızı eski deneyimle kaydettiniz veya Microsoft uygulama kayıt portalı 'nı kullanmaya devam ederseniz, yeniden yönlendirme URI 'sini oluşturmanız ve portaldaki yeniden yönlendirme URI 'lerinin listesini el ile güncelleştirmeniz gerekebilir.

### <a name="broker-related-exceptions"></a>Aracıda ilgili özel durumlar

MSAL, aracı ile iki şekilde iletişim kurar:

- Aracı ile bağlantılı hizmet
- Android AccountManager

MSAL önce bu hizmeti çağırmak herhangi bir Android izni gerektirmediğinden, önce aracı ile ilişkili hizmeti kullanır. Bağlama hizmetine bağlama başarısız olursa, MSAL Android AccountManager API 'sini kullanır. MSAL yalnızca uygulamanız `"READ_CONTACTS"` izin verildiyse bunu yapar.

Hata kodu `"BROKER_BIND_FAILURE"``MsalClientException` alırsanız iki seçenek vardır:

- Kullanıcıdan Microsoft Authenticator uygulaması ve Intune Şirket Portalı için güç iyileştirmesini devre dışı vermesini isteyin.
- Kullanıcıdan `"READ_CONTACTS"` iznini vermesini isteyin
