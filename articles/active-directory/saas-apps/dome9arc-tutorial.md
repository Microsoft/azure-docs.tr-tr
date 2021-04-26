---
title: 'Öğretici Azure Active Directory: Check Point CloudGuard Dome9 Arc ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve Check Point CloudGuard Dome9 Arc arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: e5bb8a32cfd73e67141a25531594e8a3b6f793c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732236"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Öğretici: Check Point CloudGuard Dome9 Arc ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Check Point CloudGuard Dome9 yay Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Check Point CloudGuard Dome9 Arc 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Check Point CloudGuard Dome9 yaya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Point CloudGuard Dome9 yay denetimine otomatik olarak kaydolmalarına imkan tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Check Point CloudGuard Dome9 ARC çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Check Point CloudGuard Dome9 Arc **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Galeriden Check Point CloudGuard Dome9 yay ekleme

Check Point CloudGuard Dome9 Arc 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden denetim noktası CloudGuard Dome9 yay ' i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Check Point CloudGuard Dome9 Arc** yazın.
1. Sonuçlar panelinden **Check Point CloudGuard Dome9 Arc** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-check-point-cloudguard-dome9-arc"></a>Check Point CloudGuard Dome9 Arc için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Check Point CloudGuard Dome9 Arc ile yapılandırın ve test edin. SSO 'nun çalışması için, Check Point CloudGuard Dome9 Arc içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Check Point CloudGuard Dome9 Arc ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Check Point CloudGuard Dome9 Arc SSO 'Yu yapılandırın](#configure-check-point-cloudguard-dome9-arc-sso)** .
    1. Check Point CloudGuard **[Dome9 Arc test kullanıcısı oluşturun](#create-check-point-cloudguard-dome9-arc-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Check Point CloudGuard Dome9 Arc içindeki B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal **Check Point CloudGuard Dome9 Arc** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. `<company name>`Bu değeri, Öğreticinin ilerleyen kısımlarında açıklanan **Check Point CloudGuard DOME9 Arc SSO 'yu yapılandırma** bölümünde bulabilirsiniz. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Check Point CloudGuard Dome9 Arc uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/edit-attribute.png)

1. Yukarıdaki ' a ek olarak, Check Point CloudGuard Dome9 Arc uygulaması, daha fazla özniteliğin aşağıda gösterilen SAML yanıtına geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksiniminize göre bunları gözden geçirebilirsiniz.
    
    | Name |  Kaynak özniteliği|
    | ---------------| --------------- |
    | üyesi | Kullanıcı. atandroles |

    >[!NOTE]
    >Azure AD 'de rol oluşturmayı öğrenmek için [buraya](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) tıklayın.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Check Point CloudGuard Dome9 yay ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Check Point CloudGuard Dome9 yaya erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Check Point CloudGuard Dome9 Arc**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Rolleri yukarıda açıklanan şekilde ayarlarsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Check Point CloudGuard Dome9 Arc SSO 'yu yapılandırma

1. Check Point CloudGuard Dome9 Arc içindeki yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarım güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, **Kurulum onay noktası CloudGuard Dome9 yay** ' a tıklayarak sizi Check Point CloudGuard Dome9 Arc uygulamasına yönlendirirsiniz. Buradan, Check Point CloudGuard Dome9 Arc 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Check Point CloudGuard Dome9 yayı el ile kurmak istiyorsanız yeni bir Web tarayıcı penceresi açın ve Check Point CloudGuard Dome9 Arc şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

2. Sağ üst köşedeki **profil ayarları** ' na tıklayın ve ardından **Hesap ayarları**' na tıklayın. 

    !["Hesap ayarları" seçiliyken "profil ayarları" menüsünü gösteren ekran görüntüsü.](./media/dome9arc-tutorial/configure1.png)

3. **SSO** 'ya gidin ve **Etkinleştir**' e tıklayın.

    !["S O" sekmesini ve "etkinleştir" seçili olduğunu gösteren ekran görüntüsü.](./media/dome9arc-tutorial/configure2.png)

4. SSO yapılandırma bölümünde aşağıdaki adımları uygulayın:

    ![Check Point CloudGuard Dome9 Arc yapılandırması](./media/dome9arc-tutorial/configure3.png)

    a. **Hesap kimliği** metin kutusuna şirket adını girin. Bu değer, Azure portal **temel SAML yapılandırması** bölümünde bahsedilen **Yanıtla** ve **oturum aç** URL 'sinde kullanılır.

    b. **Veren** metin kutusunda, Azure Portal formunu KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    c. **IDP uç nokta URL 'si** metin kutusunda, Azure Portal formunu kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. İndirilen Base64 kodlamalı sertifikanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve **X. 509.440 sertifika** metin kutusuna yapıştırın.

    e. **Kaydet**’e tıklayın.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Check Point CloudGuard Dome9 Arc test kullanıcısı oluşturma

Azure AD kullanıcılarının Check Point CloudGuard Dome9 Arc ' da oturum açmasını sağlamak için, uygulamaya sağlanması gerekir. Check Point CloudGuard Dome9 Arc tam zamanında sağlamayı destekler, ancak düzgün şekilde çalışması için, kullanıcının belirli **rolü** seçmesini ve kullanıcıya aynı atamasını sağlaması gerekir.

   >[!Note]
   >**Rol** oluşturma ve diğer Ayrıntılar Için [denetim noktası CloudGuard Dome9 Arc istemci destek ekibi](mailto:Dome9@checkpoint.com)' ne başvurun.

**Bir kullanıcı hesabını el ile sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Denetim noktası CloudGuard Dome9 Arc şirket sitenizde yönetici olarak oturum açın.

2. **Kullanıcılar & roller** ' e ve ardından **Kullanıcılar**' a tıklayın.

    !["Kullanıcılar & rolleri" seçiliyken görüntülenen ekran görüntüsü.](./media/dome9arc-tutorial/user1.png)

3. **Kullanıcı Ekle**' ye tıklayın.

    !["Kullanıcı Ekle" düğmesi seçiliyken "kullanıcıları & rolleri" gösteren ekran görüntüsü.](./media/dome9arc-tutorial/user2.png)

4. **Kullanıcı oluştur** bölümünde aşağıdaki adımları uygulayın:

    ![Çalışan Ekle](./media/dome9arc-tutorial/user3.png)

    a. **E-posta** metin kutusuna, gibi kullanıcının e-postasını yazın B.Simon@contoso.com .

    b. **Ilk ad** metin kutusuna B gibi kullanıcının adını yazın.

    c. **Soyadı** metin kutusunda, Simon adlı kullanıcının soyadını yazın.

    d. **SSO kullanıcısını** **Açık** yapın.

    e. **Oluştur**' a tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu işlem, oturum akışını başlatabileceğiniz Check Point CloudGuard Dome9 Arc oturum açma URL 'sine yeniden yönlendirilir.  

* Check Point CloudGuard Dome9 Arc oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test** et ' e tıklayın ve SSO 'Yu ayarladığınız Check Point CloudGuard Dome9 Arc ' da otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki Check Point CloudGuard Dome9 yay kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Check Point CloudGuard Dome9 yayı içinde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Check Point CloudGuard Dome9 Arc ' ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
