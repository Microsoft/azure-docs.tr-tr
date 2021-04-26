---
title: Öğretici-Azure AD Connect bulut eşitlemesi kullanarak, mevcut bir ormanı ve yeni bir ormanı tek bir Azure AD kiracısı ile tümleştirin.
description: Mevcut bir karma kimlik ortamına bulut eşitlemesi eklemeyi öğrenin.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64536024af7e939de2bd8f98a9ce14bb4df3303f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614698"
---
# <a name="integrate-an-existing-forest-and-a-new-forest-with-a-single-azure-ad-tenant"></a>Mevcut bir ormanı ve yeni bir ormanı tek bir Azure AD kiracısı ile tümleştirme

Bu öğreticide, mevcut bir karma kimlik ortamına bulut eşitlemesi ekleme işlemi adım adım gösterilmektedir. 

![Oluştur](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Bu öğreticide oluşturduğunuz ortamı test etmek veya karma kimliğin nasıl çalıştığı hakkında daha fazla bilgi almak için kullanabilirsiniz. 

Bu senaryoda, bir Azure AD kiracısına Azure AD Connect eşitleme kullanılarak eşitlenmiş mevcut bir orman vardır. Aynı Azure AD kiracısıyla eşitlemek istediğiniz yeni bir ormanınız vardır. Yeni orman için bulut eşitlemesi oluşturacaksınız. 

## <a name="prerequisites"></a>Önkoşullar
### <a name="in-the-azure-active-directory-admin-center"></a>Azure Active Directory Yönetim merkezinde

1. Azure AD kiracınızda yalnızca bulutta yer alan bir genel yönetici hesabı oluşturun. Bu şekilde, şirket içi hizmetleriniz başarısız olması veya kullanılamaz hale gelmesi için kiracınızın yapılandırmasını yönetebilirsiniz. [Yalnızca bulut genel yönetici hesabı ekleme](../fundamentals/add-users-azure-active-directory.md)hakkında bilgi edinin. Bu adımın tamamlanması, kiracınızdan kilitlenmemesini sağlamak açısından önemlidir.
2. Azure AD kiracınıza bir veya daha fazla [özel etki alanı adı](../fundamentals/add-custom-domain.md) ekleyin. Kullanıcılarınız bu etki alanı adlarından biriyle oturum açabilir.

### <a name="in-your-on-premises-environment"></a>Şirket içi ortamınızda

1. En az 4 GB RAM ve .NET 4.7.1 + Runtime ile Windows Server 2012 R2 veya üzeri çalıştıran etki alanına katılmış bir konak sunucusu belirler 

2. Sunucularınız ve Azure AD arasında bir güvenlik duvarı varsa, aşağıdaki öğeleri yapılandırın:
   - Aracıların Azure AD 'ye aşağıdaki bağlantı noktaları üzerinden *giden* istekler yapabilmeleri için emin olun:

     | Bağlantı noktası numarası | Nasıl kullanılır? |
     | --- | --- |
     | **80** | TLS/SSL sertifikası doğrulanırken sertifika iptal listelerini (CRL 'Ler) indirir |
     | **443** | Hizmetle tüm giden iletişimi işler |
     | **8080** (isteğe bağlı) | Aracılar 443, 8080 numaralı bağlantı noktası kullanılamıyorsa, her 10 dakikada bir bu durumu bağlantı noktası üzerinden raporlar. Bu durum Azure AD portalında görüntülenir. |
     
     Güvenlik duvarınız kaynak kullanıcılara göre kuralları zorunlu tutuyorsa ağ hizmeti olarak çalışan Windows hizmetlerinden gelen trafik için bu bağlantı noktalarını açın.
   - Güvenlik duvarınız veya ara sunucunuz güvenli sonekler belirtmenize izin veriyorsa, **\* . msappproxy.net** ve **\* . ServiceBus.Windows.net** öğesine bağlantı ekleyin. Aksi takdirde, haftalık olarak güncellenen [Azure veri MERKEZI IP aralıklarına](https://www.microsoft.com/download/details.aspx?id=41653)erişime izin verin.
   - Aracılarınızın ilk kayıt için **login.Windows.net** ve **login.microsoftonline.com** 'e erişmesi gerekir. Bu URL 'Ler için güvenlik duvarınızı da açın.
   - Sertifika doğrulaması için şu URL 'Leri engellemeyi kaldırın: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80** ve **www \. Microsoft.com:80**. Bu URL 'Ler diğer Microsoft ürünleriyle sertifika doğrulaması için kullanıldığından, bu URL 'Lerin engeli kaldırılmış olabilir.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Azure AD Connect sağlama aracısını yükler
1. Etki alanına katılmış sunucuda oturum açın.  [Temel ad ve Azure ortamı](tutorial-basic-ad-azure.md) ÖĞRETICISINI kullanıyorsanız DC1 olur.
2. Yalnızca bulutta bulunan genel yönetici kimlik bilgilerini kullanarak Azure portal oturum açın.
3. Sol tarafta **Azure Active Directory**' ı seçin, **Azure AD Connect** ' a tıklayın ve ardından **bulut eşitlemesini Yönet**' i seçin.</br>
![Azure portalda](media/how-to-install/install-6.png)</br>
4. "Aracıyı Indir" e tıklayın
5. Azure AD Connect sağlama aracısını çalıştırma
6. Giriş ekranında, lisans koşullarını **kabul edin** ve **yükler**' e tıklayın.</br>
!["Microsoft Azure AD Connect sağlama Aracısı paketi" Giriş ekranını gösteren ekran görüntüsü.](media/how-to-install/install-1.png)</br>

7. Bu işlem tamamlandıktan sonra Yapılandırma Sihirbazı başlatılır.  Azure AD Genel Yönetici hesabınızla oturum açın.  IE artırılmış güvenlik etkinse, bu, oturum açma 'nın engellenmesini unutmayın.  Bu durumda, yükleme işlemini kapatın, Sunucu Yöneticisi IE artırılmış güvenliği devre dışı bırakın ve **AAD Connect sağlama Aracısı sihirbazına** tıklayarak yüklemeyi yeniden başlatın.
8. **Bağlan Active Directory** ekranında, **Dizin Ekle** ' ye tıklayın ve ardından Active Directory etki alanı Yönetici hesabınızla oturum açın. Etki alanı yöneticisi hesabı parola değiştirme gereksinimlerine sahip olmamalıdır. Parolanın süresi dolarsa veya değişirse, aracıyı yeni kimlik bilgileriyle yeniden yapılandırmanız gerekecektir. Bu işlem, şirket içi dizininizi ekleyecek.  **İleri**’ye tıklayın.</br>
!["Connect Active Directory" ekranını gösteren ekran görüntüsü.](media/how-to-install/install-3a.png)</br>

9. **Yapılandırma Tamam** ekranında **Onayla**' ya tıklayın.  Bu işlem aracıyı kaydedip yeniden başlatacak.</br>
!["Yapılandırma Tamam" ekranını gösteren ekran görüntüsü.](media/how-to-install/install-4a.png)</br>

10. Bu işlem tamamlandıktan sonra, bir uyarı görmeniz gerekir: **Aracı yapılandırmanız başarıyla doğrulandı.**  **Çıkış**' a tıklayabilirsiniz.</br>
![Hoş Geldiniz ekranı](media/how-to-install/install-5.png)</br>
11. İlk giriş ekranını hala görüyorsanız **Kapat**' a tıklayın.


## <a name="verify-agent-installation"></a>Aracı yüklemesini doğrulama
Aracı doğrulaması Azure portal ve aracıyı çalıştıran yerel sunucu üzerinde oluşur.

### <a name="azure-portal-agent-verification"></a>Aracı doğrulama Azure portal
Aracının Azure tarafından görüldüğünü doğrulamak için şu adımları izleyin:

1. Azure portalında oturum açın.
2. Sol tarafta **Azure Active Directory**' ı seçin, **Azure AD Connect** ' a tıklayın ve ardından **bulut eşitlemesini Yönet**' i seçin.</br>
![Azure portalda](media/how-to-install/install-6.png)</br>

3.  **Azure AD Connect bulut eşitleme** ekranında **tüm aracıları gözden geçir**' e tıklayın.
![Azure AD sağlama](media/how-to-install/install-7.png)</br>
 
4. **Şirket içi sağlama aracıları ekranında** , yüklediğiniz aracıları görürsünüz.  Söz konusu aracının orada olduğunu ve **etkin** olarak işaretlendiğinden emin olun.
![Aracıları sağlama](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>Yerel sunucuda
Aracının çalıştığını doğrulamak için şu adımları izleyin:

1.  Yönetici hesabıyla sunucuda oturum açma
2.  Hizmetlere giderek veya Start/Run/Services. msc ' ye giderek **Hizmetleri** açın.
3.  **Hizmetler**' in altında **Microsoft Azure AD aracı güncelleştiricisi Connect** ve **Microsoft Azure AD Connect sağlama aracısının** mevcut olduğundan ve durumun **çalıştığından** emin olun.
![Hizmetler](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Azure AD Connect bulut eşitlemesini yapılandırma
 Sağlamayı yapılandırmak için aşağıdaki adımları kullanın

1.  Azure AD portalında oturum açın.
2.  **Azure Active Directory** tıklayın
3.  **Azure AD Connect** tıklayın
4.   
 ![ "Bulut eşitlemesini Yönet" bağlantısını gösteren bulut eşitlemesini Yönet ekran görüntüsünü seçin.](media/how-to-configure/manage-1.png)
5.   
 ![ "Yeni yapılandırma" bağlantısı vurgulanmış şekilde Azure AD Connect bulut eşitleme ekranının yeni yapılandırma ekran görüntüsü ' ne tıklayın.](media/tutorial-single-forest/configure-1.png)
7.  Yapılandırma ekranında bir **bildirim e-postası** girin, seçiciyi **etkinleştirmek** için taşıyın ve **Kaydet**' e tıklayın.
![Bildirim e-postasına sahip ekranı Yapılandır ekranının ekran görüntüsü doldurulmuş ve seçili şekilde etkin.](media/how-to-configure/configure-2.png)
1.  Yapılandırma durumu artık **sağlıklı** olmalıdır.
![Sağlıklı durumu gösteren Azure AD Connect bulut eşitleme ekranının ekran görüntüsü.](media/how-to-configure/manage-4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Kullanıcıların oluşturulduğunu ve eşitlemenin gerçekleştiğini doğrula
Artık şirket içi dizinimizde bulunan kullanıcıların Azure AD kiracımızda bulunduğundan emin olursunuz.  Bu işlem işleminin tamamlanması birkaç saat sürebilir.  Kullanıcıların eşitlendiğinden emin olmak için aşağıdakileri yapın.


1. [Azure portalına](https://portal.azure.com) gidip Azure aboneliği olan bir hesapla oturum açın.
2. Sol tarafta **Azure Active Directory** ' yi seçin.
3. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
4. Kiracımızda yeni kullanıcıları gördiğinizi doğrulayın</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Kullanıcılarımızdan biriyle oturum açma testi

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) adresine gidin
2. Yeni kiracımızda oluşturulmuş bir kullanıcı hesabıyla oturum açın.  Şu biçimi kullanarak oturum açmanız gerekir: ( user@domain.onmicrosoft.com ). Kullanıcının şirket içinde oturum açması için kullandığı parolayı kullanın.</br>
   ![Doğrulama](media/tutorial-single-forest/verify-1.png)</br>

Artık Azure 'un sunabileceği bir karma kimlik ortamını test etmek ve tanımak için kullanabileceğiniz bir karma kimlik ortamı oluşturdunuz.

## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)