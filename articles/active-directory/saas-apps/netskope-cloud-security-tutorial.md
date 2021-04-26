---
title: 'Öğretici: Netüse Yönetici Konsolu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Netüse Yönetici Konsolu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2021
ms.author: jeedes
ms.openlocfilehash: 5f07e9dbd574d8a27e6048154c72b9afc24b6ba4
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286196"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Öğretici: Netüse Yönetici Konsolu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Netüse Yönetici Konsolu Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Netüse Yönetici Konsolu 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Netüse Yönetici Konsolu erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla, Netüse Yönetici Konsolu için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Netüse Yönetici Konsolu çoklu oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Netüse Yönetici Konsolu **SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Galeriden Netüse Yönetici Konsolu ekleme

Netüse Yönetici Konsolu tümleştirmesini Azure AD ile yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Netüse Yönetici Konsolu eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **netüse Yönetici Konsolu** yazın.
1. Sonuçlar panelinden **Netüse Yönetici Konsolu** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-netskope-administrator-console"></a>Netüse Yönetici Konsolu için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Netüse Yönetici Konsolu ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Netüse Yönetici Konsolu içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Netüse Yönetici Konsolu ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Netüse yönetıcı konsolu SSO 'Yu yapılandırın](#configure-netskope-administrator-console-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Netüse Yönetici Konsolu 'de B. Simon 'a sahip olmak için **[netüse Yönetici Konsolu test kullanıcısı oluşturun](#create-netskope-administrator-console-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Netüse Yönetici Konsolu** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek yanıt URL 'siyle güncelleştirin. Öğreticide daha sonra açıklanacak değeri alacaksınız.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantname>.goskope.com`

    > [!NOTE]
    > Oturum açma URL 'SI değerleri gerçek değil. Oturum açma URL 'SI değerini, gerçek oturum açma URL 'SI ile güncelleştirin. Oturum açma URL 'SI değerini almak için [Netüse Yönetici Konsolu istemci desteği ekibine](mailto:support@netskope.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Netüse Yönetici Konsolu uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Daha yukarıya ek olarak, Netüse Yönetici Konsolu uygulaması aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name |  Kaynak özniteliği|
    | ---------| --------- |
    | yönetici-rol | Kullanıcı. atandroles |

    > [!NOTE]
    > Azure AD 'de rol oluşturmayı öğrenmek için [buraya](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) tıklayın.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Netüse Yönetici Konsolu ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

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

Bu bölümde, Netüse Yönetici Konsolu erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Netüse Yönetici Konsolu**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Rolleri yukarıda açıklanan şekilde ayarlarsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-netskope-administrator-console-sso"></a>Netüse Yönetici Konsolu SSO 'yu yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve Netüse Yönetici Konsolu şirket sitenizde yönetici olarak oturum açın.

1. Sol gezinti bölmesindeki **Ayarlar** sekmesine tıklayın.

    ![Ekran görüntüsü, gezinti bölmesinde seçilen ayarı gösterir.](./media/netskope-cloud-security-tutorial/configure-settings.png)

1. **Yönetim** sekmesini tıklatın.

    ![Ekran görüntüsü ayarlardan seçilen yönetimi gösterir.](./media/netskope-cloud-security-tutorial/administration.png)

1. **SSO** sekmesi ' ne tıklayın.

    ![Ekran görüntüsü, Yönetim bölümünde seçilen S 'yi gösterir.](./media/netskope-cloud-security-tutorial/tab.png)

1. **Ağ ayarları** bölümünde aşağıdaki adımları uygulayın:
    
    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz ağ ayarlarını gösterir.](./media/netskope-cloud-security-tutorial/network.png)

    a. **Onaylama tüketici hizmeti URL 'si** değerini kopyalayın ve Azure Portal **temel SAML YAPıLANDıRMASı** bölümündeki **yanıt URL** metin kutusuna yapıştırın.

    b. **Hizmet sağlayıcısı VARLıK kimliği** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **tanımlayıcı** metin kutusuna yapıştırın.

1. **SSO/SLO ayarları** bölümünün altındakı **düzenleme ayarları** ' na tıklayın.

    ![Ekran görüntüsü, Ayarları Düzenle ' yi seçebileceğiniz S S/S L O ayarlarını gösterir.](./media/netskope-cloud-security-tutorial/settings.png)

1. **Ayarlar** açılan penceresinde, aşağıdaki adımları gerçekleştirin;

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz ayarlar iletişim kutusunu gösterir.](./media/netskope-cloud-security-tutorial/configuration.png)

    a. **SSO 'Yu etkinleştir**' i seçin.

    b. **IDP URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **IDP VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    d. İndirilen Base64 kodlamalı sertifikanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve **IDP sertifika** metin kutusuna yapıştırın.

    e. **SSO 'Yu etkinleştir**' i seçin.

    f. **IDP SLO URL** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    örneğin: **Gönder**' e tıklayın.

### <a name="create-netskope-administrator-console-test-user"></a>Netüse Yönetici Konsolu test kullanıcısı oluşturma

1. Tarayıcınızda yeni bir sekme açın ve Netüse Yönetici Konsolu şirket sitenizde yönetici olarak oturum açın.

1. Sol gezinti bölmesindeki **Ayarlar** sekmesine tıklayın.

    ![Ekran görüntüsü seçili ayarları gösterir.](./media/netskope-cloud-security-tutorial/configure-settings.png)

1. **Etkin platform** sekmesi ' ne tıklayın.

    ![Ekran görüntüsü ayarlarından seçilen etkin platformu gösterir.](./media/netskope-cloud-security-tutorial/user-1.png)

1. **Kullanıcılar** sekmesi ' ne tıklayın.

    ![Ekran görüntüsü, etkin platformlardan seçilen kullanıcıları gösterir.](./media/netskope-cloud-security-tutorial/add-user.png)

1. **Kullanıcı Ekle**' ye tıklayın.

    ![Ekran görüntüsü, Kullanıcı Ekle ' yi seçebileceğiniz kullanıcılar iletişim kutusunu gösterir.](./media/netskope-cloud-security-tutorial/user-add.png)

1. Eklemek istediğiniz kullanıcının e-posta adresini girin ve **Ekle**' ye tıklayın.

    ![Ekran görüntüsünde, kullanıcı listesi girebileceğiniz Kullanıcı ekleme gösterilir.](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Netüse Yönetici Konsolu oturum açma URL 'sine yeniden yönlendirilir.  

* Doğrudan Netüse Yönetici Konsolu oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Netüse yönetici konsolu otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamdaki Netüse Yönetici Konsolu kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Netüse Yönetici Konsolu otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Netüse Yönetici Konsolu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
