---
title: 'Öğretici: SAP HANA ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SAP HANA arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1a1e155974b66dce9a036a20cdebe19ded81fed5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727090"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Öğretici: SAP HANA ile tümleştirme Azure Active Directory

Bu öğreticide, SAP HANA Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. SAP HANA Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de SAP HANA erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla SAP HANA için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

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

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.


## <a name="adding-sap-hana-from-the-gallery"></a>Galeriden SAP HANA ekleme

SAP HANA tümleştirmesini Azure AD ile yapılandırmak için, Galeriden SAP HANA yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **SAP HANA** yazın.
1. Sonuçlar panelinden **SAP HANA** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>SAP HANA için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu SAP HANA ile yapılandırın ve test edin. SSO 'nun çalışması için, SAP HANA bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu SAP HANA ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[SAP HANA SSO 'Yu yapılandırın](#configure-sap-hana-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı SAP HANA ' de Britta Simon 'un bir karşılığı olacak **[SAP HANA test kullanıcısı oluşturun](#create-sap-hana-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **SAP HANA** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Yanıt URL 'SI değeri gerçek değil. Değeri gerçek yanıt URL 'siyle güncelleştirin. Değerleri almak için [SAP HANA istemci destek ekibine](https://cloudplatform.sap.com/contact.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. SAP HANA uygulama, SAML onaylamalarını belirli bir biçimde bekliyor. Bu uygulama için aşağıdaki talepleri yapılandırın. Bu özniteliklerin değerlerini, uygulama tümleştirme sayfasındaki **Kullanıcı öznitelikleri** bölümünden yönetebilirsiniz. **SAML Ile tek Sign-On ayarlama** sayfasında, **Kullanıcı öznitelikleri** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    !["Düzenle" simgesi seçili "Kullanıcı öznitelikleri" bölümünü gösteren ekran görüntüsü.](common/edit-attribute.png)

6. **Kullanıcı öznitelikleri & talepler** Iletişim kutusundaki **Kullanıcı öznitelikleri** bölümünde aşağıdaki adımları uygulayın:
 
    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **Düzenle simgesine** tıklayın.

    !["Düzenle" simgesi seçili "Kullanıcı öznitelikleri & talepleri" iletişim kutusunu gösteren ekran görüntüsü.](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. **Dönüştürme** listesinden **Extractmailprefix ()** öğesini seçin.

    c. **Parametre 1** listesinden **Kullanıcı. Mail**' i seçin.

    d. **Kaydet**’e tıklayın.

7. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

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

Bu bölümde, SAP HANA erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **SAP HANA**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-sap-hana-sso"></a>SAP HANA SSO 'yu yapılandırma

1. SAP HANA tarafında çoklu oturum açmayı yapılandırmak için, ilgili HTTPS uç noktasına giderek **Hana XSA Web konsolunuza**  oturum açın.

    > [!NOTE]
    > Varsayılan yapılandırmada URL, kimliği doğrulanmış bir SAP HANA veritabanı kullanıcısının kimlik bilgilerini gerektiren bir oturum açma ekranına yeniden yönlendirir. Oturum açan kullanıcının SAML yönetim görevlerini gerçekleştirmek için izinleri olmalıdır.

2. XSA web arabiriminde **SAML Identity Provider**' a gidin. Buradan, **+** **kimlik sağlayıcı bilgileri ekle** bölmesini göstermek için ekranın altındaki düğmeyi seçin. Ardından aşağıdaki adımları uygulayın:

    ![Kimlik sağlayıcısı ekle](./media/saphana-tutorial/sap1.png)

    a. **Kimlik sağlayıcı bilgileri ekle** bölmesinde meta veri XML içeriğini (Azure Portal Indirdiğiniz) **meta veriler** kutusuna yapıştırın.

    !["Meta veriler" ve "ad" kutuları vurgulanmış olan "kimlik sağlayıcı bilgilerini ekle" bölmesini gösteren ekran görüntüsü.](./media/saphana-tutorial/sap2.png)

    b. XML belgesinin içeriği geçerliyse, ayrıştırma işlemi, **genel veri** ekran alanındaki **konu, varlık kimliği ve veren** alanları için gereken bilgileri ayıklar. Ayrıca, **hedef** ekran alanındaki URL alanları için gereken bilgileri (örneğin, **temel URL ve tekeroturum URL 'si (*)** alanlarını da ayıklar.

    ![Kimlik sağlayıcısı ayarları ekle](./media/saphana-tutorial/sap3.png)

    c. **Genel veri** ekranı alanının **ad** kutusuna yeni SAML SSO kimlik sağlayıcısı için bir ad girin.

    > [!NOTE]
    > SAML ıDP 'nin adı zorunludur ve benzersiz olmalıdır. SAP HANA XS uygulamalarının kullanacağı kimlik doğrulama yöntemi olarak SAML ' yi seçtiğinizde görüntülenen kullanılabilir SAML IDPs listesinde görüntülenir. Örneğin, bunu XS yapıt yönetim aracının **kimlik doğrulama** ekranı alanında yapabilirsiniz.

3. SAML kimlik sağlayıcısının ayrıntılarını kaydetmek ve bilinen SAML IDPs listesine yeni SAML ıDP eklemek için **Kaydet** ' i seçin.

    ![Kaydet düğmesi](./media/saphana-tutorial/sap4.png)

4. HANA Studio 'da, **yapılandırma** sekmesinin sistem özellikleri içinde, ayarları **SAML**'ye göre filtreleyin. Sonra **assertion_timeout** **10 sn** 'den **120 sn**'ye ayarlayın.

    ![assertion_timeout ayarı](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>SAP HANA test kullanıcısı oluştur

Azure AD kullanıcılarının SAP HANA oturum açmasını sağlamak için, bunları SAP HANA sağlamalısınız.
SAP HANA, varsayılan olarak etkin olan **tam zamanında sağlamayı** destekler.

Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, aşağıdaki adımları uygulayın:

>[!NOTE]
>Kullanıcının kullandığı dış kimlik doğrulamasını değiştirebilirsiniz. Kerberos gibi bir dış sistemle kimlik doğrulaması yapabilir. Dış kimlikler hakkında ayrıntılı bilgi için [etki alanı yöneticinizle](https://cloudplatform.sap.com/contact.html)iletişime geçin.

1. [SAP HANA Studio 'yu](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) yönetici olarak açın ve ardından SAML SSO için DB-User etkinleştirin.

    ![Kullanıcı oluştur](./media/saphana-tutorial/sap5.png)

2. **SAML**'nin solundaki görünmez onay kutusunu seçin ve ardından **Yapılandır** bağlantısını seçin.

3. SAML ıDP eklemek için **Ekle** ' yi seçin.  Uygun SAML ıDP 'yi seçip **Tamam**' ı seçin.

4. **Dış kimliği** ekleyin (Bu durumda, Brittasıon) veya **birini** seçin. Ardından **Tamam**’ı seçin.

   > [!Note]
   > **Herhangi bir** onay kutusu seçili DEĞILSE, Hana 'daki Kullanıcı adının, etki alanı sonekinin önüne, UPN 'deki Kullanıcı adıyla tam olarak eşleşmesi gerekir. (Örneğin, BrittaSimon@contoso.com Hana 'Da Brittasıon olur.)

5. Test amacıyla tüm **XS** rollerini kullanıcıya atayın.

    ![Rol atama](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Yalnızca kullanım durumlarınız için uygun izinleri vermeniz gerekir.

6. Kullanıcıyı kaydedin.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız SAP HANA otomatik olarak oturum açmış olmanız gerekir

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki SAP HANA kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız SAP HANA otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

SAP HANA yapılandırdıktan sonra, kuruluşunuzun gizli verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).