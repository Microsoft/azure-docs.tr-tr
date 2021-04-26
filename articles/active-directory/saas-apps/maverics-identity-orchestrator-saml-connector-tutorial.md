---
title: 'Öğretici: Maverics Identity Orchestrator SAML Bağlayıcısı ile çoklu oturum açma (SSO) Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Maverics Identity Orchestrator SAML Bağlayıcısı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 402f6cd6961108cdf1e9c94fb4f93309fbf15ead
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599035"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Azure AD çoklu oturum açmayı Maverics Identity Orchestrator SAML Bağlayıcısı ile tümleştirme

Strata 'nın Maverics kimlik Orchestrator, kimlik doğrulama ve erişim denetimi için şirket içi uygulamaları Azure Active Directory (Azure AD) ile tümleştirmenin basit bir yolunu sunar. Maverics Orchestrator, şu anda üst bilgileri, tanımlama bilgilerini ve diğer özel kimlik doğrulama yöntemlerini kullanan uygulamalar için kimlik doğrulama ve yetkilendirme işlemlerini modernleştirmektedir. Maverics Orchestrator örnekleri şirket içinde veya bulutta dağıtılabilir. 

Bu karma erişim öğreticisinde, kimlik doğrulama ve erişim denetimi için Azure AD 'yi kullanmak üzere eski bir Web erişimi yönetim ürünüyle korunan şirket içi bir Web uygulamasının nasıl geçirileceğini gösterilmektedir. Temel adımlar şunlardır:

1. Maverics Orchestrator 'ı ayarlama
1. Proxy uygulama
1. Azure AD 'de bir kurumsal uygulamayı kaydetme
1. Azure aracılığıyla kimlik doğrulaması yapın ve uygulamaya erişim yetkisi verin
1. Sorunsuz uygulama erişimi için üst bilgiler ekleyin
1. Birden çok uygulamayla çalışma

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Maverics Identity Orchestrator SAML Bağlayıcısı SSO özellikli aboneliği. Maverics yazılımını almak için [Strata satışları](mailto:sales@strata.io)ile iletişim kurun.
* Üst bilgi tabanlı kimlik doğrulaması kullanan en az bir uygulama. Örnekler adresinde barındırılan Connectulum adlı bir uygulamaya karşı çalışır `https://app.connectulum.com` .
* Maverics Orchestrator 'ı barındıracak bir Linux makinesi
  * İşletim sistemi: RHEL 7,7 veya üzeri, CentOS 7 +
  * Disk: >= 10 GB
  * Bellek: >= 4 GB
  * Bağlantı noktaları: 22 (SSH/SCP), 443, 7474
  * Yükler/yönetim görevleri için kök erişimi
  * Maverics kimlik Orchestrator 'ı barındıran sunucudan korumalı uygulamanıza giden ağ çıkışı

## <a name="step-1-set-up-the-maverics-orchestrator"></a>1. Adım: Maverics Orchestrator 'ı ayarlama

### <a name="install-maverics"></a>Maverics 'yi yükler

1. En son Maverics RPM 'Yi alın. Paketi, Maverics yazılımını yüklemek istediğiniz sisteme kopyalayın.

1. Maverics paketini yükleyerek dosya adınızı yerine koyun `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Maverics 'yı yükledikten sonra, altında bir hizmet olarak çalışacaktır `systemd` . Hizmetin çalıştığını doğrulamak için aşağıdaki komutu yürütün:

   `sudo systemctl status maverics`

1. Orchestrator 'ı yeniden başlatmak ve günlükleri izlemek için aşağıdaki komutu çalıştırabilirsiniz:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Maverics yükledikten sonra, varsayılan `maverics.yaml` Dosya `/etc/maverics` dizininde oluşturulur. Yapılandırmanızı ve dahil etmek üzere düzenlemeden önce `appgateways` `connectors` yapılandırma dosyanız şu z gibi görünür:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>DNS yapılandırma

DNS, Orchestrator sunucusunun IP 'sini hatırlamanız gerekmiyorsa yararlı olacaktır.

12.34.56.78 'ın kuramsal Orchestrator IP 'sini kullanarak tarayıcı makinesinin (dizüstü bilgisayarınızın) ana bilgisayar dosyasını düzenleyin. Linux tabanlı işletim sistemlerinde bu dosya içinde bulunur `/etc/hosts` . Windows 'da konumunda bulunur `C:\windows\system32\drivers\etc` .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

DNS 'in beklenen şekilde yapılandırıldığını doğrulamak için, Orchestrator 'ın durum uç noktasına bir istek yapabilirsiniz. Tarayıcınızdan, ister http://sonar.maverics.com:7474/status .

### <a name="configure-tls"></a>TLS’yi yapılandırma

Güvenlik sağlamak için güvenli kanallar üzerinden iletişim kurmak üzere Orchestrator ile iletişim kurun. Bunu başarmak için, bölümıza bir sertifika/anahtar çifti ekleyebilirsiniz `tls` .

Orchestrator sunucusu için otomatik olarak imzalanan bir sertifika ve anahtar oluşturmak için, aşağıdaki komutu Dizin içinden çalıştırın `/etc/maverics` :

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> Üretim ortamlarında, tarayıcıdaki uyarıları önlemek için büyük olasılıkla bilinen bir CA tarafından imzalanmış bir sertifika kullanmak isteyeceksiniz. Güvenilir bir CA arıyorsanız, [şifrelemem](https://letsencrypt.org/) iyi ve ücretsiz bir seçenektir.

Şimdi, Orchestrator için yeni oluşturulan sertifikayı ve anahtarı kullanın. Yapılandırma dosyanız şu kodu içermelidir:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

TLS 'nin beklendiği gibi yapılandırıldığını doğrulamak için, Maverics hizmetini yeniden başlatın ve durum uç noktasına bir istek yapın.

## <a name="step-2-proxy-an-application"></a>2. Adım: uygulama proxy 'Si

Ardından, kullanarak Orchestrator 'da temel proxy yapılandırın `appgateways` . Bu adım, Orchestrator 'ın korumalı uygulama için gerekli bağlantıyı içerdiğini doğrulamanıza yardımcı olur.

Yapılandırma dosyanız şu kodu içermelidir:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

Proxy 'nin beklendiği gibi çalıştığını doğrulamak için Maverics hizmetini yeniden başlatın ve Maverics proxy 'si aracılığıyla uygulamaya bir istek yapın. İsteğe bağlı olarak belirli uygulama kaynaklarına bir istek yapabilirsiniz.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>3. Adım: Kurumsal uygulamayı Azure AD 'ye kaydetme

Şimdi, Azure AD 'de son kullanıcıların kimliğini doğrulamak için kullanılacak yeni bir kurumsal uygulama oluşturun.

> [!NOTE]
> Koşullu erişim gibi Azure AD özelliklerini kullandığınızda, şirket içi uygulama başına bir kurumsal uygulama oluşturmak önemlidir. Bu, uygulama başına koşullu erişim, uygulama başına risk değerlendirmesi, uygulama başına atanan izinler vb. için izin verir. Genel olarak, Azure AD 'deki bir kurumsal uygulama Maverics içindeki bir Azure Bağlayıcısı ile eşlenir.

Azure AD 'de bir kurumsal uygulamayı kaydetmek için:

1. Azure AD kiracınızda **Kurumsal uygulamalar**' a gidin ve ardından **Yeni uygulama**' yı seçin. Azure AD galerisinde, **Maverics Identity Orchestrator SAML bağlayıcısını** arayın ve ardından seçin.

1. Uygulamanın dizininizdeki tüm kullanıcılar için çalışmasını sağlamak için, Maverics Identity Orchestrator SAML Bağlayıcısı **Özellikler** bölmesinde, **gereken Kullanıcı atamasını** **Hayır** olarak ayarlayın.

1. Maverics kimlik Orchestrator SAML bağlayıcısına **genel bakış** bölmesinde, **Tekli oturum açmayı ayarla**' yı seçin ve ardından **SAML**' yi seçin.

1. Maverics kimlik Orchestrator SAML Bağlayıcısı **SAML tabanlı oturum açma** bölmesinde, **Düzenle** (kurşun kalem simgesi) düğmesini seçerek **temel SAML yapılandırmasını** düzenleyin.

   !["Temel SAML yapılandırması" düzenleme düğmesinin ekran görüntüsü.](common/edit-urls.png)

1. Bir **VARLıK kimliği** girin `https://sonar.maverics.com` . Varlık KIMLIĞI, Kiracıdaki uygulamalar arasında benzersiz olmalıdır ve rastgele bir değer olabilir. Bu değeri, `samlEntityID` sonraki bölümde Azure Bağlayıcınız için alan tanımladığınızda kullanacaksınız.

1. **Yanıt URL 'si** girin `https://sonar.maverics.com/acs` . Bu değeri, `samlConsumerServiceURL` sonraki bölümde Azure Bağlayıcınız için alan tanımladığınızda kullanacaksınız.

1. Bir **oturum açma URL 'si** girin `https://sonar.maverics.com/` . Bu alan Maverics tarafından kullanılmaz, ancak kullanıcıların Azure AD My Apps portalından uygulamaya erişimini sağlamak için Azure AD 'de kullanılması gerekir.

1. **Kaydet**’i seçin.

1. **SAML Imzalama sertifikası** bölümünde, **Kopyala** düğmesini seçerek **uygulama Federasyon meta verileri URL 'si** değerini kopyalayın ve ardından bilgisayarınıza kaydedin.

   !["SAML Imzalama sertifikası" kopyalama düğmesinin ekran görüntüsü.](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>4. Adım: Azure aracılığıyla kimlik doğrulama ve uygulamaya erişim yetkisi verme

Ardından, yeni oluşturduğunuz kurumsal uygulamayı Maverics içindeki Azure bağlayıcısını yapılandırarak kullanın. `connectors`Blokla eşleştirilmiş bu yapılandırma, `idps` Orchestrator 'ın kullanıcıların kimliğini doğrulamasına izin verir.

Yapılandırma dosyanız artık aşağıdaki kodu içermelidir. `METADATA_URL`Önceki adımda bulunan uygulama Federasyon meta verileri URL 'si değeriyle değiştirdiğinizden emin olun.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Kimlik doğrulamanın beklendiği gibi çalıştığını doğrulamak için Maverics hizmetini yeniden başlatın ve Maverics proxy 'si aracılığıyla bir uygulama kaynağına istek yapın. Kaynağa erişmeden önce kimlik doğrulaması için Azure 'a yönlendirilmelisiniz.

## <a name="step-5-add-headers-for-seamless-application-access"></a>5. Adım: sorunsuz uygulama erişimi için üst bilgiler ekleme

Yukarı akış uygulamasına henüz üst bilgi göndermiyorsunuz. `headers`Ayrıca, yukarı akış uygulamasının kullanıcıyı belirlemesine olanak tanımak Için Maverics ara sunucusu üzerinden geçerken isteğe ekleyelim.

Yapılandırma dosyanız şu kodu içermelidir:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Kimlik doğrulamasının beklenen şekilde çalıştığını onaylamak için, Maverics proxy 'si aracılığıyla bir uygulama kaynağına bir istek oluşturun. Korumalı uygulama artık, istek üzerine üstbilgiler alıyor olmalıdır. 

Uygulamanız farklı üstbilgiler bekliyorsa başlık anahtarlarını düzenleyebilirsiniz. SAML akışının bir parçası olarak Azure AD 'den geri gelen tüm talepler, üst bilgilerde kullanılabilir. Örneğin, uygulamasının `secondary_email: azureSonarApp.email` `azureSonarApp` bağlayıcı adı olduğu ve `email` Azure AD 'den döndürülen bir talep olduğu başka bir üst bilgisini dahil edebilirsiniz. 

## <a name="step-6-work-with-multiple-applications"></a>6. Adım: birden çok uygulamayla çalışma

Şimdi, farklı konaklardaki birden çok uygulama için ara sunucu için gereklere göz atalım. Bu adımı gerçekleştirmek için başka bir uygulama ağ geçidi, Azure AD 'de başka bir kurumsal uygulama ve başka bir bağlayıcı yapılandırın.

Yapılandırma dosyanız şu kodu içermelidir:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

Kodun `host` App Gateway tanımlarınıza bir alan ekleyeceğini fark etmiş olabilirsiniz. `host`Alan, Maverics Orchestrator 'ın hangi yukarı akış konağını proxy trafiğine ayırabilmesini sağlar.

Yeni eklenen uygulama ağ geçidinin beklenen şekilde çalıştığını onaylamak için, ' a bir istek yapın `https://connectulum.maverics.com` .

## <a name="advanced-scenarios"></a>Gelişmiş senaryolar

### <a name="identity-migration"></a>Kimlik geçişi

Son kullanım için Web erişimi yönetim aracınız olamaz, ancak toplu parola sıfırlama olmadan kullanıcılarınızı geçirmek için bir yol yok mu? Maverics Orchestrator, kullanarak kimlik geçişini destekler `migrationgateways` .

### <a name="web-server-gateways"></a>Web sunucusu ağ geçitleri

Maverics Orchestrator aracılığıyla ağ ve proxy trafiğinizi yeniden kullanmak istemiyor musunuz? Sorun değil. Maverics Orchestrator, proxy olmadan aynı çözümleri sunmak için Web sunucusu ağ geçitleri (modüller) ile eşleştirilebilir.

## <a name="wrap-up"></a>Yukarı kaydırın

Bu noktada, Maverics Orchestrator 'ı yüklediniz, Azure AD 'de bir kurumsal uygulama oluşturup yapılandırdık ve bir kimlik doğrulaması ve ilkeyi zorunlu tutarken, Orchestrator 'ı korumalı bir uygulamaya yapılandırmış olarak yapılandırdınız. Maverics Orchestrator 'ın dağıtılmış kimlik yönetimi kullanım durumları için nasıl kullanılabileceği hakkında daha fazla bilgi edinmek için, [Strata ile iletişim kurun](mailto:sales@strata.io).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)
