---
title: 'Öğretici: SAP Analytics bulutu ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SAP Analytics bulutu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 19bdb6d2a586dcb279687673c7fa4e302dc4928b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652649"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Öğretici: SAP Analytics bulutunu Azure Active Directory tümleştirin

Bu öğreticide SAP Analytics bulutunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. SAP Analytics bulutu 'nı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de SAP Analytics bulutuna erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla SAP Analytics bulutuna otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SAP Analytics bulutu çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* SAP Analytics bulutu, **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-sap-analytics-cloud-from-the-gallery"></a>Galeriden SAP Analytics bulutu ekleme

SAP Analytics bulutu 'nın Azure AD ile tümleştirilmesini yapılandırmak için, galerisinden SAP Analytics bulutu 'nı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **SAP Analytics bulutu** yazın.
1. Sonuçlar panelinden **SAP Analytics bulutu** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-sap-analytics-cloud"></a>SAP Analytics bulutu için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test KULLANıCıSı kullanarak SAP Analytics bulutu Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, SAP Analytics bulutu 'ndaki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu SAP Analytics bulutu ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[SAP Analytics Cloud SSO 'Yu yapılandırma](#configure-sap-analytics-cloud-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. SAP Analytics **[Cloud test kullanıcısı oluşturun](#create-sap-analytics-cloud-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan SAP Analytics bulutu 'nda B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **SAP Analytics bulutu** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki desenlerden birini kullanarak bir URL yazın:

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > Bu URL 'Lerdeki değerler yalnızca tanıtım amaçlıdır. Değerleri, gerçek oturum açma URL 'SI ve tanımlayıcı URL 'SI ile güncelleştirin. Oturum açma URL 'sini almak için [SAP Analytics bulut istemci desteği ekibine](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)başvurun. Yönetim konsolundan SAP Analytics bulut meta verilerini indirerek tanımlayıcı URL 'sini alabilirsiniz. Bu, öğreticide daha sonra açıklanmaktadır.

4. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **SAP Analytics bulutu ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, SAP Analytics bulutuna erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **SAP Analytics bulutu**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-sap-analytics-cloud-sso"></a>SAP Analytics Cloud SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, SAP Analytics bulut şirket sitenizde yönetici olarak oturum açın.

2. **Menü**  >  **sistem**  >  **yönetimi**' ni seçin.
    
    ![Menü, sistem ve ardından Yönetim ' i seçin](./media/sapboc-tutorial/configure-1.png)

3. **Güvenlik** sekmesinde **Düzenle** (kalem) simgesini seçin.
    
    ![Güvenlik sekmesinde, Düzenle simgesini seçin.](./media/sapboc-tutorial/configure-2.png)  

4. **Kimlik doğrulama yöntemi** Için **SAML SINGLE Sign-On (SSO)** öğesini seçin.

    ![Kimlik doğrulama yöntemi için SAML tek Sign-On seçin](./media/sapboc-tutorial/configure-3.png)  

5. Hizmet sağlayıcısı meta verilerini indirmek için (1. adım), **İndir**' i seçin. Meta veri dosyasında **EntityId** değerini bulup kopyalayın. Azure portal, **temel SAML yapılandırması** iletişim kutusunda, değeri **tanımlayıcı** kutusuna yapıştırın.

    ![EntityId değerini kopyalayıp yapıştırın](./media/sapboc-tutorial/configure-4.png)  

6. Azure portal indirdiğiniz dosyadaki hizmet sağlayıcısı meta verilerini (2. adım) karşıya yüklemek için, **kimlik sağlayıcısı meta verilerini karşıya yükle** altında **karşıya yükle**' yi seçin.  

    ![Kimlik sağlayıcısı meta verilerini karşıya yükle altında Yükle ' yi seçin.](./media/sapboc-tutorial/configure-5.png)

7. **Kullanıcı özniteliği** listesinde, uygulamanız için kullanmak istediğiniz kullanıcı özniteliğini (3. adım) seçin. Bu kullanıcı özniteliği kimlik sağlayıcısına eşlenir. Kullanıcının sayfasına özel bir öznitelik girmek için **özel SAML eşleme** seçeneğini kullanın. Ya da Kullanıcı özniteliği olarak **e-posta** veya **Kullanıcı kimliği** ' ni seçebilirsiniz. Örneğimizde, Azure portal Kullanıcı **öznitelikleri & talepler** bölümündeki **userPrincipalName** özniteliğiyle Kullanıcı tanımlayıcısı talebini eşleştirdiğimiz için **e-posta** seçtik. Bu, her başarılı SAML yanıtında SAP Analytics bulut uygulamasına gönderilen benzersiz bir kullanıcı e-postası sağlar.

    ![Kullanıcı özniteliği seçin](./media/sapboc-tutorial/configure-6.png)

8. Kimlik sağlayıcısıyla hesabı doğrulamak için (4. adım), **oturum açma kimlik bilgileri (e-posta)** kutusuna kullanıcının e-posta adresini girin. Ardından, **hesabı doğrula**' yı seçin. Sistem, Kullanıcı hesabına oturum açma kimlik bilgilerini ekler.

    ![E-posta girin ve hesabı Doğrula ' yı seçin.](./media/sapboc-tutorial/configure-7.png)

9. **Kaydet** simgesini seçin.

    ![Kaydet simgesi](./media/sapboc-tutorial/save.png)

### <a name="create-sap-analytics-cloud-test-user"></a>SAP Analytics bulut testi Kullanıcı Oluştur

SAP Analytics bulutu 'nda oturum açabilmeniz için önce Azure AD kullanıcıları 'nın SAP Analytics bulutu 'nda sağlanması gerekir. SAP Analytics bulutu 'nda sağlama, el ile gerçekleştirilen bir görevdir.

Bir kullanıcı hesabı sağlamak için:

1. SAP Analytics bulut şirket sitenizde yönetici olarak oturum açın.

2. **Menü**  >  **güvenliği**  >  **kullanıcıları**' nı seçin.

    ![Çalışan Ekle](./media/sapboc-tutorial/user-1.png)

3. **Kullanıcılar** sayfasında, yeni kullanıcı ayrıntıları eklemek için öğesini seçin **+** . 

    ![Kullanıcı Ekle sayfası](./media/sapboc-tutorial/user-4.png)

    Ardından, aşağıdaki adımları izleyin:

    1. **Kullanıcı kimliği** kutusuna kullanıcının kullanıcı kimliğini ( **B** gibi) girin.

    1. **Ilk ad** kutusuna, **B** gibi, kullanıcının adını girin.

    1. **Soyadı** kutusuna kullanıcının soyadı ( **Simon** gibi) girin.

    1. **Görünen ad** kutusuna, **B. Simon** gibi kullanıcının tam adını girin.

    1. **E-posta** kutusuna kullanıcının e-posta adresini girin, örneğin `b.simon@contoso.com` .

    1. **Rolleri seçin** sayfasında, Kullanıcı için uygun rolü seçin ve ardından **Tamam**' ı seçin.

        ![Rol seçin](./media/sapboc-tutorial/user-3.png)

    1. **Kaydet** simgesini seçin.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz SAP Analytics bulutu oturum açma URL 'sine yeniden yönlendirilir. 

* SAP Analytics bulutu oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki SAP Analytics bulutu kutucuğuna tıkladığınızda bu, SAP Analytics bulutu oturum açma URL 'sine yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

SAP Analytics bulutunu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve infillerini koruyan oturum denetimini uygulayabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).