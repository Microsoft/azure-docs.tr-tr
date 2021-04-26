---
title: 'Öğretici: GitHub Kurumsal bulut organizasyonu ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ile GitHub Kurumsal bulut organizasyonu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: c87f66e48aa7b39a7f3fc5f9b8572dd6925abbc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732169"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-a-github-enterprise-cloud-organization"></a>Öğretici: GitHub Kurumsal bulut organizasyonu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, bir GitHub Kurumsal bulut **organizasyonunun** Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Bir GitHub Kurumsal bulut organizasyonunu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de GitHub Kurumsal bulut kuruluşunuza erişimi olan denetim.
* GitHub Enterprise Cloud kuruluşunuza erişimi tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini bir GitHub Kurumsal bulut organizasyonu ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* GitHub Enterprise [Cloud](https://help.github.com/articles/github-s-products/#github-enterprise)'da oluşturulmuş GitHub [Enterprise faturalandırma planını](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) gerektiren bir GitHub kuruluşu

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* GitHub, **SP** tarafından başlatılan SSO 'yu destekler

* GitHub [ **Otomatik** Kullanıcı sağlamayı (kuruluş davetleri) destekler](github-provisioning-tutorial.md)


## <a name="adding-github-from-the-gallery"></a>Galeriden GitHub ekleme

GitHub 'ın Azure AD 'ye tümleştirmesini yapılandırmak için, Galeriden GitHub 'dan yönetilen SaaS uygulamaları listenize GitHub eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **GitHub** yazın.
1. Sonuçlar panelinden **GitHub Enterprise Cloud-Organization** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-github"></a>GitHub için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu GitHub ile yapılandırın ve test edin. SSO 'nun çalışması için, GitHub 'daki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu GitHub ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[GITHUB SSO 'Yu yapılandırma](#configure-github-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. GitHub 'da kullanıcının Azure AD gösterimine bağlı olan, GitHub 'da B. Simon 'a karşılık gelen bir buna sahip olmak için **[GitHub test kullanıcısı oluşturun](#create-github-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **GitHub** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

   a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://github.com/orgs/<Organization ID>/sso`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://github.com/orgs/<Organization ID>`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://github.com/orgs/<Organization ID>/saml/consume`


    > [!NOTE]
    > Bunların gerçek değerler olduğunu lütfen unutmayın. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirmeniz gerekir. Burada, Tanımlayıcıdaki benzersiz dize değerini kullanmanızı öneririz. Bu değerleri almak için GitHub Yöneticisi bölümüne gidin.

5. GitHub uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde, **benzersiz kullanıcı tanımlayıcısının (ad kimliği)** **User. UserPrincipalName** ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. GitHub uygulaması, **benzersiz kullanıcı tanımlayıcısının (ad kimliği)** **Kullanıcı. Mail** ile eşlenmesini bekler, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    !["Düzenle" simgesi seçili "Kullanıcı öznitelikleri" bölümünü gösteren ekran görüntüsü.](common/edit-attribute.png)

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. GitHub 'ı **Ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, GitHub 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **GitHub**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.

    ![kullanıcı rolü](./media/github-tutorial/user-role.png)

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-github-sso"></a>GitHub SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, GitHub kuruluş sitenizde yönetici olarak oturum açın.

2. **Ayarlar** ' a gidin ve **güvenlik**' e tıklayın.

    !["Güvenlik" seçiliyken GitHub "kuruluş ayarları" menüsünü gösteren ekran görüntüsü.](./media/github-tutorial/security.png)

3. **SAML kimlik doğrulamasını etkinleştir** kutusunu Işaretleyin, çoklu oturum açma yapılandırma alanlarını ve aşağıdaki adımları gerçekleştirin:

    !["A m l kimlik doğrulamasını etkinleştir" başlıklı ve U R L metin kutuları vurgulanmış olan ekran görüntüsü.](./media/github-tutorial/saml-sso.png)

    a. **Çoklu oturum açma URL 'si** değerini kopyalayın ve bu değeri Azure Portal **temel SAML yapılandırmasındaki** **URL 'yi aç** metin kutusuna yapıştırın.
    
    b. **Onaylama tüketici hizmeti URL 'si** değerini kopyalayın ve bu değeri Azure Portal **temel SAML yapılandırmasındaki** **yanıt URL 'si** metin kutusuna yapıştırın.

4. Aşağıdaki alanları yapılandırın:

    !["Oturum açma URL 'SI", "verenin" ve "genel sertifika" metin kutularının gösterildiği ekran görüntüsü.](./media/github-tutorial/configure.png)

    a. Oturum açma **URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **Veren** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    c. İndirilen sertifikayı not defteri 'nde Azure portal açın, içeriği **ortak sertifika** metin kutusuna yapıştırın.

    d. Şu şekilde gösterildiği gibi **RSA-SHA1** ve **SHA1** ile **RSA-SHA256** ve **SHA256** arasındaki **imza yöntemini** ve **Özet yöntemini** düzenlemek için **Düzenle** simgesine tıklayın.
    
    e. GitHub 'daki URL 'nin Azure uygulama kaydı 'ndaki URL ile eşleşmesi için, **onaylama tüketici hizmeti URL 'sini (yanıt URL 'si)** varsayılan URL 'den güncelleştirin.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. SSO sırasında doğrulama hatası veya hata olmadığını doğrulamak için **Test SAML yapılandırması** ' na tıklayın.

    ![Ayarlar](./media/github-tutorial/test.png)

6. **Kaydet**’e tıklayın

> [!NOTE]
> GitHub 'daki çoklu oturum açma, GitHub 'daki belirli bir kuruluşun kimliğini doğrular ve GitHub 'ın kimlik doğrulamasını değiştirmez. Bu nedenle, kullanıcının github.com oturumunun süresi dolmuşsa, çoklu oturum açma işlemi sırasında GitHub 'ın KIMLIĞI/parolasıyla kimlik doğrulaması yapmanız istenebilir.

### <a name="create-github-test-user"></a>GitHub test kullanıcısı oluşturma

Bu bölümün amacı, GitHub 'da Britta Simon adlı bir Kullanıcı oluşturmaktır. GitHub, varsayılan olarak etkinleştirilen Otomatik Kullanıcı sağlamayı destekler. Otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](github-provisioning-tutorial.md) .

**Kullanıcı el ile oluşturmanız gerekiyorsa aşağıdaki adımları gerçekleştirin:**

1. GitHub şirket sitenizde yönetici olarak oturum açın.

2. **Kişiler**' e tıklayın.

    ![Ekran görüntüsü, seçili kişileri içeren GitHub sitesini gösterir.](./media/github-tutorial/people.png "People")

3. **Üyeyi davet et**' e tıklayın.

    ![Kullanıcıları davet et](./media/github-tutorial/invite-member.png "Kullanıcıları davet et")

4. **Üye davet et** iletişim sayfasında, aşağıdaki adımları uygulayın:

    a. **E-posta** metin kutusunda Britta Simon hesabının e-posta adresini yazın.

    ![Kişileri davet et](./media/github-tutorial/email-box.png "Kişileri davet et")

    b. **Davet gönder**' e tıklayın.

    !["Üye" seçiliyken ve "davet gönder" düğmesinin seçili olduğu "üye davet etme" iletişim sayfasını gösteren ekran görüntüsü.](./media/github-tutorial/send-invitation.png "Kişileri davet et")

    > [!NOTE]
    > Azure Active Directory hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını onaylamak için bir bağlantıyı izler.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz GitHub oturum açma URL 'sine yeniden yönlendirilir. 

* GitHub oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım 'daki GitHub kutucuğuna tıkladığınızda bu işlem GitHub oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

GitHub 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)