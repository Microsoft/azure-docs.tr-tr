---
title: 'Öğretici Azure Active Directory: FortiWeb Web uygulaması güvenlik duvarı ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve FortiWeb Web uygulaması güvenlik duvarı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e34664bd81023da7a50b8ff4645c670146ef2554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731962"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>Öğretici: FortiWeb Web uygulaması güvenlik duvarı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, FortiWeb Web uygulaması güvenlik duvarını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. FortiWeb Web uygulaması güvenlik duvarını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de FortiWeb Web uygulaması güvenlik duvarına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla, FortiWeb Web uygulaması güvenlik duvarı 'nda otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* FortiWeb Web uygulaması güvenlik duvarı çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* FortiWeb Web uygulaması güvenlik duvarı, **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>Galeriden FortiWeb Web uygulaması güvenlik duvarını ekleme

FortiWeb Web uygulaması güvenlik duvarının tümleştirmesini Azure AD ile yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize FortiWeb Web uygulaması güvenlik duvarı eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **FortiWeb Web uygulaması güvenlik duvarı** yazın.
1. Sonuçlar panelinden **FortiWeb Web uygulaması güvenlik duvarını** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>FortiWeb Web uygulaması güvenlik duvarı için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, FortiWeb Web uygulaması güvenlik duvarı Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, FortiWeb Web uygulaması güvenlik duvarında bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu FortiWeb Web uygulaması güvenlik duvarı ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[FortiWeb Web uygulaması güvenlik duvarı SSO 'Su yapılandırma](#configure-fortiweb-web-application-firewall-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. **[FortiWeb Web uygulaması güvenlik duvarı test kullanıcısı oluşturma](#create-fortiweb-web-application-firewall-test-user)** -kullanıcının Azure AD gösterimine bağlı olan FortiWeb Web uygulaması güvenlik duvarında B. Simon 'un bir karşılığı olacak şekilde.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **FortiWeb Web uygulaması güvenlik duvarı** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

   a. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.<CUSTOMER_DOMAIN>.com`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.<CUSTOMER_DOMAIN>.com`

    d. **Oturum kapatma URL** 'si metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` , FortiWeb 'e yapılandırma sağlanırken daha sonra kullanılacak bir ad tanımlayıcısıdır.
    > Gerçek URL değerlerini almak için [FortiWeb Web uygulaması güvenlik duvarı destek ekibine](mailto:support@fortinet.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)


### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, FortiWeb Web uygulaması güvenlik duvarı 'na erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **FortiWeb Web uygulaması güvenlik duvarı**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-fortiweb-web-application-firewall-sso"></a>FortiWeb Web uygulaması güvenlik duvarı SSO 'SU yapılandırma

1.  `https://<address>:8443` `<address>` FortiWeb VM 'SINE atanan FQDN veya genel IP adresi ' ne gidin.

2.  FortiWeb VM dağıtımı sırasında belirtilen yönetici kimlik bilgilerini kullanarak oturum açın.

1. Aşağıdaki sayfada aşağıdaki adımları gerçekleştirin.

    ![SAML sunucu sayfası](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  Sol taraftaki menüde **Kullanıcı**' ya tıklayın.

    b.  Kullanıcı altında **uzak sunucu**' ya tıklayın.

    c.  **SAML sunucusu**' na tıklayın.

    d.  **Yeni oluştur**' a tıklayın.

    e.  **Ad** alanında, `<fwName>` Azure AD yapılandırma bölümünde kullanılan değeri belirtin.

    f.  **VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    örneğin: **Meta veriler**' ın yanındaki **Dosya Seç** ' e tıklayın ve Azure Portal INDIRDIĞINIZ **Federasyon meta veri XML** dosyasını seçin.

    h.  **Tamam**'a tıklayın.

### <a name="create-a-site-publishing-rule"></a>Site yayımlama kuralı oluşturma

1.  `https://<address>:8443` `<address>` FortiWeb VM 'SINE atanan FQDN veya genel IP adresi ' ne gidin.

1.  FortiWeb VM dağıtımı sırasında belirtilen yönetici kimlik bilgilerini kullanarak oturum açın.
1. Aşağıdaki sayfada aşağıdaki adımları gerçekleştirin.

    ![Site yayımlama kuralı](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  Sol taraftaki menüde **uygulama teslimi**' na tıklayın.
    
    b.  **Uygulama teslimi** altında, **site Yayımla**' ya tıklayın.
    
    c.  **Site yayımlama** altında, **site Yayımla**' ya tıklayın.
    
    d.  **Site yayımlama kuralı**' na tıklayın.
    
    e.  **Yeni oluştur**' a tıklayın.
    
    f.  Site yayımlama kuralı için bir ad girin.
    
    örneğin:  **Yayımlanan site türü**' nün yanındaki **normal ifade**' e tıklayın.
    
    i.  **Yayımlanan site**' nin yanında, yayımlamakta olduğunuz Web sitesinin ana bilgisayar üstbilgisiyle eşleşecek bir dize sağlayın.
    
    j.  **Yol**' ın yanında bir/belirtin.
    
    k.  **Istemci kimlik doğrulama yöntemi**' nin yanında **SAML kimlik doğrulaması**' nı seçin.
    
    l.  **SAML sunucusu** açılır penceresinde, daha önce oluşturduğunuz SAML sunucusunu seçin.
    
    m.  **Tamam**'a tıklayın.


### <a name="create-a-site-publishing-policy"></a>Site Yayımlama Ilkesi oluşturma

1.  `https://<address>:8443` `<address>` FortiWeb VM 'SINE atanan FQDN veya genel IP adresi ' ne gidin.

2.  FortiWeb VM dağıtımı sırasında belirtilen yönetici kimlik bilgilerini kullanarak oturum açın.

1. Aşağıdaki sayfada aşağıdaki adımları gerçekleştirin.

    ![Site Yayımlama Ilkesi](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  Sol taraftaki menüde **uygulama teslimi**' na tıklayın.

    b.  **Uygulama teslimi** altında, **site Yayımla**' ya tıklayın.

    c.  **Site yayımlama** altında, **site Yayımla**' ya tıklayın.

    d.  **Site yayımlama ilkesi**' ne tıklayın.

    e.  **Yeni oluştur**' a tıklayın.

    f.  Site Yayımlama Ilkesi için bir ad belirtin.

    örneğin:  **Tamam**'a tıklayın.

    h.  **Yeni oluştur**' a tıklayın.

    i.  **Kural** açılan kutusunda, daha önce oluşturduğunuz site yayımlama kuralını seçin.

    j.  **Tamam**'a tıklayın.

### <a name="create-and-assign-a-web-protection-profile"></a>Web koruması profili oluşturma ve atama

1.  `https://<address>:8443` `<address>` FortiWeb VM 'SINE atanan FQDN veya genel IP adresi ' ne gidin.

2.  FortiWeb VM dağıtımı sırasında belirtilen yönetici kimlik bilgilerini kullanarak oturum açın.
3.  Sol taraftaki menüde **ilke**' ye tıklayın.
4.  **İlke** altında **Web koruması profili**' ne tıklayın.
5.  **Satır Içi standart koruma** ' ya tıklayıp **Kopyala**' ya tıklayın.
6.  Yeni Web koruması profili için bir ad girin ve **Tamam 'a** tıklayın.
7.  Yeni Web koruması profilini seçin ve **Düzenle**' ye tıklayın.
8.  **Site yayımlama**' nın yanındaki daha önce oluşturduğunuz site yayımlama ilkesini seçin.
9.  **Tamam**'a tıklayın.
 
    ![Site Yayımlama](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. Sol taraftaki menüde **ilke**' ye tıklayın.
11. **İlke** altında **sunucu ilkesi**' ne tıklayın.
12. Kimlik doğrulaması için Azure Active Directory kullanmak istediğiniz Web sitesini yayımlamak için kullanılan sunucu ilkesini seçin.
13. **Düzenle**’ye tıklayın.
14. **Web koruması profili** açılır penceresinde, az önce oluşturduğunuz Web koruması profilini seçin.
15. **Tamam**'a tıklayın.
16. FortiWeb 'in Web sitesini yayımladığı dış URL 'ye erişme girişimi. Kimlik doğrulaması için Azure Active Directory yönlendirilmelisiniz.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>FortiWeb Web uygulaması güvenlik duvarı test kullanıcısı oluşturma

Bu bölümde, FortiWeb Web uygulaması güvenlik duvarında Britta Simon adlı bir Kullanıcı oluşturacaksınız. FortiWeb Web uygulaması güvenlik duvarı platformunda kullanıcıları eklemek için [FortiWeb Web uygulaması güvenlik duvarı destek ekibi](mailto:support@fortinet.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz FortiWeb Web uygulaması oturum açma URL 'sine yeniden yönlendirilir. 

* FortiWeb Web uygulaması oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki FortiWeb Web uygulaması kutucuğuna tıkladığınızda, bu, FortiWeb Web uygulaması oturum açma URL 'sine yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

FortiWeb Web uygulaması güvenlik duvarını yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).