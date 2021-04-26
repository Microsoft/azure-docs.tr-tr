---
title: 'Öğretici: FortiGate SSL VPN ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: FortiGate SSL VPN 'i Azure Active Directory (Azure AD) ile bütünleştirmek için gerçekleştirmeniz gereken adımları öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: 9852752799fd010ebb069637f55008d9c4f68bf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732151"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Öğretici: FortiGate SSL VPN ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, FortiGate SSL VPN 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. FortiGate SSL VPN 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* FortiGate SSL VPN 'ye kimlerin erişebileceğini denetlemek için Azure AD 'yi kullanın.
* Kullanıcılarınızın Azure AD hesaplarıyla SSL VPN 'yi yasakladığından otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli bir FortiGate SSL VPN aboneliği.

## <a name="tutorial-description"></a>Öğretici açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edeceksiniz.

FortiGate SSL VPN, SP tarafından başlatılan SSO 'yu destekler.


## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>Galeriden FortiGate SSL VPN 'i ekleme

FortiGate SSL VPN 'nin tümleştirmesini Azure AD ile yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Fortıgate SSL VPN eklemeniz gerekir:

1. Azure portal bir iş veya okul hesabıyla ya da kişisel bir Microsoft hesabı oturum açın.
1. Sol bölmede **Azure Active Directory**’yi seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **FORTIGATE SSL VPN** girin.
1. Sonuçlar panelinde **Fortigate SSL VPN** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>FortiGate SSL VPN için Azure AD SSO 'yu yapılandırma ve test etme

Azure AD SSO 'yu FortiGate SSL VPN ile, B. Simon adlı bir test kullanıcısı kullanarak yapılandırıp test edersiniz. SSO 'nun çalışması için, FortiGate SSL VPN içindeki bir Azure AD kullanıcısı ile buna karşılık gelen kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

FortiGate SSL VPN ile Azure AD SSO 'yu yapılandırmak ve test etmek için şu üst düzey adımları tamamlayacaksınız:

1. Kullanıcılarınıza yönelik özelliği etkinleştirmek için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Bu Kullanıcı için Azure AD çoklu oturum açmayı etkinleştirmek üzere **[Test kullanıcısına erişim Izni verin](#grant-access-to-the-test-user)** .
1. Uygulama tarafında **[FortiGate SSL VPN SSO 'Yu yapılandırın](#configure-fortigate-ssl-vpn-sso)** .
    1. Kullanıcının Azure AD gösterimine karşılık gelen bir **FortiGate SSL VPN test kullanıcısı oluşturun** .
1. Yapılandırmanın çalıştığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. Azure portal, **Fortigate SSL VPN** uygulama tümleştirmesi sayfasında, **Yönet** bölümünde **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem düğmesini seçin:

   ![Temel SAML yapılandırmasını düzenlemenin kalem düğmesini gösteren ekran görüntüsü.](common/edit-urls.png)

1. **SAML Ile tek Sign-On ayarlama** sayfasında, aşağıdaki değerleri girin:

    a. **Oturum açma URL 'si** kutusuna, modele bir URL girin `https://<FQDN>/remote/login` .

    b. **Tanımlayıcı** kutusunda, modele bir URL girin `https://<FQDN>/remote/saml/metadata` .

    c. **Yanıt URL 'si** kutusuna, modele bir URL girin `https://<FQDN>/remote/saml/login` .

    d. **Oturum kapatma URL 'si** kutusuna, modele bir URL girin `https://<FQDN>/remote/saml/logout` .

    > [!NOTE]
    > Bu değerler yalnızca desenlerdir. Gerçek **oturum açma URL 'sini**, **TANıMLAYıCıYı**, **yanıt URL**'sini ve oturum **kapatma URL**'sini kullanmanız gerekir. Rehberlik için [Fortinet desteğiyle](https://support.fortinet.com) iletişim kurun. Ayrıca, Azure portal Fortinet belgelerinde ve **temel SAML yapılandırması** bölümünde gösterilen örnek desenlere de bakabilirsiniz.

1. FortiGate SSL VPN uygulaması, belirli bir biçimde SAML onayları bekler, bu da yapılandırmaya özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![Varsayılan öznitelikleri gösteren ekran görüntüsü.](common/default-attributes.png)

1. FortiGate SSL VPN için gereken iki ek talep aşağıdaki tabloda gösterilmiştir. Bu taleplerin adları, Bu öğreticinin **FortiGate komut satırı yapılandırma** bölümünde kullanılan adlarla eşleşmelidir. 

   | Name |  Kaynak özniteliği|
   | ------------ | --------- |
   | username | User. UserPrincipalName |
   | group | Kullanıcı. gruplar |
   
   Bu ek talepleri oluşturmak için:

   a. **Kullanıcı öznitelikleri & talepler**' ın yanındaki **Düzenle**' yi seçin.

   b. **Yeni talep Ekle**' yi seçin.

   c. **Ad** için **Kullanıcı adı** girin.

   d. **Kaynak özniteliği** için **User. UserPrincipalName**' i seçin.

   e. **Kaydet**’i seçin.

   f. **Grup talebi ekle**' yi seçin.

   örneğin: **Tüm uygulamalar**’ı seçin.

   h. **Grup talebinin adını Özelleştir** onay kutusunu seçin.

   i. **Ad** için **Grup** girin.
   
   j. **Kaydet**’i seçin.   

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, sertifikayı Indirip bilgisayarınıza kaydetmek için **sertifika (base64)** yanındaki **indirme** bağlantısını seçin:

    ![Sertifika indirme bağlantısını gösteren ekran görüntüsü.](common/certificatebase64.png)

1. **FortiGate SSL VPN ayarla** bölümünde, gereksinimlerinize göre uygun URL 'Yi veya URL 'leri kopyalayın:

    ![Yapılandırma URL 'Lerini gösteren ekran görüntüsü.](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal B. Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmesinde **Azure Active Directory**' ı seçin. **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları uygulayın:
   1. **Ad** kutusuna **B. Simon** girin.  
   1. **Kullanıcı adı** kutusuna \<username> @ \<companydomain> .. yazın. \<extension> Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster**' i seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’u seçin.

#### <a name="grant-access-to-the-test-user"></a>Test kullanıcısına erişim izni verme

Bu bölümde, bu kullanıcıya FortiGate SSL VPN 'e erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Fortigate SSL VPN**' yi seçin.
1. Uygulamanın genel bakış sayfasında, **Yönet** bölümünde **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinde **B. Simon** öğesini seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

#### <a name="create-a-security-group-for-the-test-user"></a>Test kullanıcısı için bir güvenlik grubu oluşturma

Bu bölümde, test kullanıcısı için Azure Active Directory bir güvenlik grubu oluşturacaksınız. FortiGate, VPN üzerinden Kullanıcı ağ erişimini sağlamak için bu güvenlik grubunu kullanacaktır.

1. Azure portal sol bölmesinde **Azure Active Directory**' ı seçin. Sonra **gruplar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Grup** ' u seçin.
1. **Yeni Grup** özelliklerinde şu adımları uygulayın:
   1. **Grup türü** listesinde **güvenlik**' i seçin.
   1. **Grup adı** kutusuna **Fortigateaccess** yazın.
   1. **Grup açıklaması** kutusunda, **FORTIGATE VPN erişimi vermek için Grup** girin.
   1. **Azure AD rolleri grup (Önizleme) ayarlarına atanabilir** , **Hayır**' ı seçin.
   1. **Üyelik türü** kutusunda, **atandı**' ı seçin.
   1. **Üyeler** altında, **Seçili üye yok**' u seçin.
   1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinden **B. Simon** öğesini seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
   1. **Oluştur**’u seçin.
1. Azure Active Directory **gruplar** bölümüne geri döndüğünüzde, **Fortigate erişim** grubunu bulun ve **nesne kimliğini** aklınızda olun. Daha sonra ihtiyacınız olacak.

### <a name="configure-fortigate-ssl-vpn-sso"></a>FortiGate SSL VPN SSO 'yu yapılandırma

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>FortiGate gerecine Base64 SAML sertifikası yükleme

Kiracınızdaki FortiGate uygulamasının SAML yapılandırmasını tamamladıktan sonra base64 kodlu SAML sertifikasını indirdiniz. Bu sertifikayı Fortıgate gerecine yüklemeniz gerekir:

1. FortiGate gerecinizin yönetim portalında oturum açın.
1. Sol bölmede **sistem**' i seçin.
1. **Sistem** altında **Sertifikalar**' ı seçin.
1. Uzak **sertifikayı içeri aktar**' ı seçin  >  .
1. Azure kiracısındaki FortiGate uygulama dağıtımından indirilen sertifikaya gidin, seçin ve ardından **Tamam**' ı seçin.

Sertifika karşıya yüklendikten sonra, **sistem**  >  **sertifikaları**  >  **uzak sertifikası** altında adını göz önünde atın. Varsayılan olarak, REMOTE_Cert_ *n* olarak adlandırılır; burada *n* bir tamsayı değeridir.

#### <a name="complete-fortigate-command-line-configuration"></a>FortiGate komut satırı yapılandırmasını tamamlanma

Aşağıdaki adımlar, Azure oturum kapatma URL 'sini yapılandırmanızı gerektirir. Bu URL bir soru işareti karakteri (?) içeriyor. Bu karakteri başarıyla göndermek için belirli adımları gerçekleştirmeniz gerekir. FortiGate CLı konsolundan bu adımları tamamlayamazsınız. Bunun yerine, PuTTY gibi bir araç kullanarak FortiGate gereci için bir SSH oturumu oluşturun. FortiGate gereci bir Azure sanal makinesi ise, Azure sanal makineler için seri konsolundan aşağıdaki adımları tamamlayabilirsiniz.

Bu adımları tamamlayabilmeniz için, daha önce kaydettiğiniz değerlere ihtiyacınız olacaktır:

- Varlık KIMLIĞI
- Yanıt URL'si
- Oturum kapatma URL 'SI
- Azure oturum açma URL 'SI
- Azure AD tanımlayıcısı
- Azure oturum kapatma URL 'SI
- Base64 SAML sertifikası adı (REMOTE_Cert_ *N*)

1. FortiGate gerecinizde bir SSH oturumu oluşturun ve bir FortiGate yönetici hesabıyla oturum açın.
1. Şu komutları çalıştırın:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > Azure oturum kapatma URL 'SI bir `?` karakter içeriyor. Bu URL 'YI FortiGate seri konsoluna doğru bir şekilde sağlamak için özel bir anahtar sırası girmeniz gerekir. URL genellikle olur `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .
   >
   > Seri konsoluna Azure Logout URL 'sini girmek için girin `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` .
   > 
   > Sonra CTRL + V ' yi seçin ve satırı tamamladıktan sonra URL 'nin geri kalanını yapıştırın: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .

#### <a name="configure-fortigate-for-group-matching"></a>Grup eşleştirme için FortiGate yapılandırma

Bu bölümde, FortiGate 'i test kullanıcısını içeren güvenlik grubunun nesne KIMLIĞINI tanıyacak şekilde yapılandıracaksınız. Bu yapılandırma, FortiGate 'in grup üyeliğine göre erişim kararları almasına izin verir.

Bu adımları tamamlayabilmeniz için, bu öğreticide daha önce oluşturduğunuz FortiGateAccess güvenlik grubunun nesne KIMLIĞINE ihtiyacınız vardır.

1. FortiGate gerecinizde bir SSH oturumu oluşturun ve bir FortiGate yönetici hesabıyla oturum açın.
1. Şu komutları çalıştırın:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>Bir FortiGate VPN portalları ve güvenlik duvarı Ilkesi oluşturma

Bu bölümde, bu öğreticide daha önce oluşturduğunuz FortiGateAccess güvenlik grubuna erişim izni veren bir FortiGate VPN portalı ve güvenlik duvarı Ilkesi yapılandıracaksınız.

Fortigate VPN platformuna VPN portalları ve güvenlik duvarı Ilkesi eklemek için [Fortigate destek ekibi](mailto:tac_amer@fortinet.com) ile çalışın. Çoklu oturum açma kullanmadan önce bu adımı gerçekleştirmeniz gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz FortiGate VPN oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan FortiGate VPN oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki FortiGate VPN kutucuğuna tıkladığınızda, bu, FortiGate VPN oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

FortiGate VPN 'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)