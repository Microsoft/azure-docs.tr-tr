---
title: 'Öğretici: SAP HANA ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SAP HANA arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a34fe5637e895ea69b6fc4c277b7722b306c97
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161190"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Öğretici: SAP HANA ile tümleştirme Azure Active Directory

Bu öğreticide, SAP HANA Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
SAP HANA Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* SAP HANA erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla SAP HANA (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini SAP HANA ile yapılandırmak için aşağıdaki öğeler gereklidir:

- Azure AD aboneliği
- Çoklu oturum açma (SSO) etkin SAP HANA aboneliği
- Azure 'da herkese açık IaaS, şirket içi, Azure VM veya SAP büyük örnekleri üzerinde çalışan bir HANA örneği
- HANA örneğinde yüklü olan XSA Yönetim Web arabiriminin yanı sıra HANA Studio

> [!NOTE]
> Bu öğreticideki adımları test etmek için SAP HANA üretim ortamının kullanılmasını önermiyoruz. Uygulamanın geliştirme veya hazırlama ortamında önce tümleştirmeyi test edin ve ardından üretim ortamını kullanın.

Bu öğreticideki adımları test etmek için aşağıdaki önerileri izleyin:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Çoklu oturum açma özellikli abonelik SAP HANA

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* SAP HANA **IDP** tarafından başlatılan SSO 'yu destekler
* SAP HANA **Just-In-Time** Kullanıcı sağlamasını destekler

## <a name="adding-sap-hana-from-the-gallery"></a>Galeriden SAP HANA ekleme

SAP HANA tümleştirmesini Azure AD ile yapılandırmak için, Galeriden SAP HANA yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden SAP HANA eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **SAP HANA**yazın, sonuç panelinden **SAP HANA** ' i seçin, sonra uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuçlar listesinde SAP HANA](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre SAP HANA ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve SAP HANA ile ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı SAP HANA ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[SAP HANA çoklu oturum açmayı yapılandırın](#configure-sap-hana-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı SAP HANA ' de Britta Simon 'un bir karşılığı olacak **[SAP HANA test kullanıcısı oluşturun](#create-sap-hana-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı SAP HANA ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **SAP HANA** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, aşağıdaki adımları uygulayın:

    ![Etki alanı ve URL 'Ler SAP HANA çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusuna şunu yazın: `HA100`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [SAP HANA istemci destek ekibine](https://cloudplatform.sap.com/contact.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. SAP HANA uygulama, SAML onaylamalarını belirli bir biçimde bekliyor. Bu uygulama için aşağıdaki talepleri yapılandırın. Bu özniteliklerin değerlerini, uygulama tümleştirme sayfasındaki **Kullanıcı öznitelikleri** bölümünden yönetebilirsiniz. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **Kullanıcı öznitelikleri** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![image](common/edit-attribute.png)

6. **Kullanıcı öznitelikleri & talepler** Iletişim kutusundaki **Kullanıcı öznitelikleri** bölümünde aşağıdaki adımları uygulayın:
 
    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **Düzenle simgesine** tıklayın.

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. **Dönüştürme** listesinden **Extractmailprefix ()** öğesini seçin.

    c. **Parametre 1** listesinden **Kullanıcı. Mail**' i seçin.

    d. **Kaydet** düğmesine tıklayın.

7. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>SAP HANA çoklu oturum açmayı yapılandırma

1. SAP HANA tarafında çoklu oturum açmayı yapılandırmak için, ilgili HTTPS uç noktasına giderek **Hana XSA Web konsolunuza** oturum açın.

    > [!NOTE]
    > Varsayılan yapılandırmada URL, kimliği doğrulanmış bir SAP HANA veritabanı kullanıcısının kimlik bilgilerini gerektiren bir oturum açma ekranına yeniden yönlendirir. Oturum açan kullanıcının SAML yönetim görevlerini gerçekleştirmek için izinleri olmalıdır.

2. XSA web arabiriminde **SAML Identity Provider**' a gidin. Buradan, **kimlik sağlayıcı bilgileri ekle** bölmesini göstermek için ekranın altındaki **+** düğmesini seçin. Ardından aşağıdaki adımları uygulayın:

    ![Kimlik sağlayıcısı ekle](./media/saphana-tutorial/sap1.png)

    a. **Kimlik sağlayıcı bilgileri ekle** bölmesinde meta veri XML içeriğini (Azure Portal Indirdiğiniz) **meta veriler** kutusuna yapıştırın.

    ![Kimlik sağlayıcısı ayarları ekle](./media/saphana-tutorial/sap2.png)

    b. XML belgesinin içeriği geçerliyse, ayrıştırma işlemi, **genel veri** ekran alanındaki **konu, varlık kimliği ve veren** alanları için gereken bilgileri ayıklar. Ayrıca, **hedef** ekran alanındaki URL alanları için gereken bilgileri (örneğin, **temel URL ve tekeroturum URL 'si (*)** alanlarını da ayıklar.

    ![Kimlik sağlayıcısı ayarları ekle](./media/saphana-tutorial/sap3.png)

    c. **Genel veri** ekranı alanının **ad** kutusuna yeni SAML SSO kimlik sağlayıcısı için bir ad girin.

    > [!NOTE]
    > SAML ıDP 'nin adı zorunludur ve benzersiz olmalıdır. SAP HANA XS uygulamalarının kullanacağı kimlik doğrulama yöntemi olarak SAML ' yi seçtiğinizde görüntülenen kullanılabilir SAML IDPs listesinde görüntülenir. Örneğin, bunu XS yapıt yönetim aracının **kimlik doğrulama** ekranı alanında yapabilirsiniz.

3. SAML kimlik sağlayıcısının ayrıntılarını kaydetmek ve bilinen SAML IDPs listesine yeni SAML ıDP eklemek için **Kaydet** ' i seçin.

    ![Kaydet düğmesi](./media/saphana-tutorial/sap4.png)

4. HANA Studio 'da, **yapılandırma** sekmesinin sistem özellikleri içinde, ayarları **SAML**'ye göre filtreleyin. Sonra **assertion_timeout** 'i **10 sn** 'den **120 sn**'ye ayarlayın.

    ![assertion_timeout ayarı](./media/saphana-tutorial/sap7.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanında **brittasıon\@yourşirketnotlarıetki alanı. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, SAP HANA erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve **SAP HANA**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, yazın ve **SAP HANA**seçin.

    ![Uygulamalar listesindeki SAP HANA bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-sap-hana-test-user"></a>SAP HANA test kullanıcısı oluştur

Azure AD kullanıcılarının SAP HANA oturum açmasını sağlamak için, bunları SAP HANA sağlamalısınız.
SAP HANA, varsayılan olarak etkin olan **tam zamanında sağlamayı**destekler.

Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, aşağıdaki adımları uygulayın:

>[!NOTE]
>Kullanıcının kullandığı dış kimlik doğrulamasını değiştirebilirsiniz. Kerberos gibi bir dış sistemle kimlik doğrulaması yapabilir. Dış kimlikler hakkında ayrıntılı bilgi için [etki alanı yöneticinizle](https://cloudplatform.sap.com/contact.html)iletişime geçin.

1. [SAP HANA Studio 'yu](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) yönetici olarak açın ve sonra SAML SSO 'SU Için DB kullanıcısını etkinleştirin.

    ![Kullanıcı Oluştur](./media/saphana-tutorial/sap5.png)

2. **SAML**'nin solundaki görünmez onay kutusunu seçin ve ardından **Yapılandır** bağlantısını seçin.

3. SAML ıDP eklemek için **Ekle** ' yi seçin.  Uygun SAML ıDP 'yi seçip **Tamam**' ı seçin.

4. **Dış kimliği** ekleyin (Bu durumda, Brittasıon) veya **birini**seçin. Sonra **Tamam**’ı seçin.

   > [!Note]
   > **Herhangi bir** onay kutusu seçili DEĞILSE, Hana 'daki Kullanıcı adının, etki alanı sonekinin önüne, UPN 'deki Kullanıcı adıyla tam olarak eşleşmesi gerekir. (Örneğin, BrittaSimon@contoso.com HANA 'da Brittasıda olur.)

5. Test amacıyla tüm **XS** rollerini kullanıcıya atayın.

    ![Rol atama](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Yalnızca kullanım durumlarınız için uygun izinleri vermeniz gerekir.

6. Kullanıcıyı kaydedin.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde SAP HANA kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız SAP HANA otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

