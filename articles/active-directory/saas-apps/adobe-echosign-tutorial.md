---
title: 'Öğretici: Adobe Sign ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Adobe Işareti arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 7162c38aae2fec4ea21aae56fa8c3649f7a55425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649990"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Öğretici: Adobe Sign ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Adobe Sign 'ı tümleştirmeyi öğreneceksiniz. Adobe Sign 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Adobe Sign 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak oturum açmasını sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:
 
* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli aboneliği Adobe Sign.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Adobe Sign, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-adobe-sign-from-the-gallery"></a>Galeriden Adobe Sign ekleyin

Adobe Sign 'ın Azure AD 'de tümleştirilmesini yapılandırmak için, galerisindeki Adobe Sign 'ı yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Adobe Sign** yazın.
1. Sonuçlar panelinden **Adobe Sign** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Adobe Sign için Azure AD SSO 'yu yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına dayanarak Adobe Sign ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve Adobe Sign içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı Adobe Sign ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirmeniz gerekir:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
1. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Adobe SIGN SSO 'Yu yapılandırın](#configure-adobe-sign-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Adobe Sign 'da Britta Simon 'un bir karşılığı olacak şekilde **[Adobe Sign test kullanıcısı oluşturun](#create-adobe-sign-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Adobe Sign ile yapılandırmak için aşağıdaki adımları uygulayın:

1. Azure portal, **Adobe imza** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

1. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

1. **SAML Ile tek Sign-On ayarla** sayfasında, **temel SAML yapılandırması** iletişim kutusunu açmak için kalem simgesi ' ne tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.echosign.com/`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.echosign.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Adobe Sign Client destek ekibine](https://helpx.adobe.com/in/contact/support.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Adobe Sign 'ı ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Adobe Sign 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Adobe Sign**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="configure-adobe-sign-sso"></a>Adobe Sign SSO 'yu yapılandırma

1. Yapılandırmadan önce, Adobe [Sign Client destek ekibine](https://helpx.adobe.com/in/contact/support.html) başvurarak etki alanınızı Adobe Sign izin verilenler listesine ekleyin. Etki alanını ekleme:

    a. [Adobe Sign Client destek ekibi](https://helpx.adobe.com/in/contact/support.html) size rastgele oluşturulmuş bir belirteç gönderir. Etki alanınız için belirteç şu şekilde olacaktır: **Adobe-Sign-doğrulaması = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Doğrulama belirtecini bir DNS metin kaydında yayımlayın ve [Adobe Imza istemci desteği ekibine](https://helpx.adobe.com/in/contact/support.html)bildirin.

    > [!NOTE]
    > Bu, birkaç gün veya daha uzun sürebilir. DNS yayma gecikmelerinin, DNS 'de yayımlanan bir değerin saat veya daha uzun bir süre boyunca görülemeyebilir anlamına geldiğini unutmayın. BT yöneticiniz, bu belirteci bir DNS metin kaydında yayımlama hakkında bilgi sahibi olmalıdır.

    c. Destek bileti aracılığıyla [Adobe Sign Client destek ekibine](https://helpx.adobe.com/in/contact/support.html) bildirimde bulunduğunda, belirteç yayımlandıktan sonra etki alanını doğrular ve hesabınıza ekler.

    d. Genellikle, bir DNS kaydında belirtecin nasıl yayımlanacağı aşağıda verilmiştir:

    * Etki alanı hesabınızda oturum açın
    * DNS kaydını güncelleştirmek için sayfayı bulun. Bu sayfa DNS yönetimi, ad sunucusu yönetimi veya Gelişmiş ayarlar olarak adlandırılır.
    * Etki alanınız için TXT kayıtlarını bulun.
    * Adobe tarafından sağlanan tam belirteç değerine sahip bir TXT kaydı ekleyin.
    * Yaptığınız değişiklikleri kaydedin.

1. Farklı bir Web tarayıcısı penceresinde, Adobe Imza şirket sitenizde yönetici olarak oturum açın.

1. SAML menüsünde, **Hesap ayarları**  >  **SAML ayarları**' nı seçin.

    ![Adobe Sign SAML ayarları sayfasının ekran görüntüsü](./media/adobe-echosign-tutorial/settings.png "Hesap")

1. **SAML ayarları** bölümünde aşağıdaki adımları uygulayın:

    ![SAML zorunlu da dahil olmak üzere SAML ayarlarını vurgulayan ekran görüntüsü.](./media/adobe-echosign-tutorial/saml1.png "SAML ayarları")

   ![SAML ayarlarının ekran görüntüsü](./media/adobe-echosign-tutorial/saml.png "SAML ayarları")

   a. **SAML modu** altında **SAML zorunlu**' yı seçin.

   b. **Yankı Imzası hesap yöneticilerinin yankı Işaret kimlik bilgilerini kullanarak oturum açmasına Izin ver '** i seçin.

   c. **Kullanıcı oluşturma** altında **SAML aracılığıyla kimliği doğrulanmış kullanıcıları otomatik olarak ekle**' yi seçin.

   d. Azure portal, **IDP VARLıK kimliği** metin kutusuna kopyaladığınız **Azure ad tanımlayıcısını** yapıştırın.

   e. **IDP oturum açma URL 'si** metin kutusuna kopyaladığınız Azure Portal, **oturum açma URL 'sini** yapıştırın.

   f. Azure portal, **IDP oturum kapatma URL 'si** metin kutusuna kopyaladığınız **Logout URL 'sini** yapıştırın.

   örneğin: İndirilen **sertifika (base64)** Dosyanızı Not defteri 'nde açın. İçeriğini panonuza kopyalayın ve sonra **IDP sertifikası** metin kutusuna yapıştırın.

   h. **Değişiklikleri Kaydet**' i seçin.

### <a name="create-adobe-sign-test-user"></a>Adobe Sign test kullanıcısı oluştur

Azure AD kullanıcılarının Adobe oturum açmasını etkinleştirmek için, bunların Adobe Sign 'a sağlanması gerekir. Bu, el ile gerçekleştirilen bir görevdir.

>[!NOTE]
>Azure AD Kullanıcı hesapları sağlamak için Adobe Sign tarafından sunulan diğer herhangi bir Adobe Sign Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz. 

1. **Adobe imza** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde **Hesap**' ı seçin. Ardından, sol bölmede **Kullanıcılar & gruplar**  >  **Yeni Kullanıcı oluştur**' u seçin.

    ![Hesap, kullanıcılar &grupları ve vurgulanmış yeni bir Kullanıcı oluşturma ile birlikte Adobe Sign şirket sitesinin ekran görüntüsü](./media/adobe-echosign-tutorial/account.png "Hesap")

3. **Yeni Kullanıcı oluştur** bölümünde aşağıdaki adımları uygulayın:

    ![Yeni Kullanıcı oluştur bölümünün ekran görüntüsü](./media/adobe-echosign-tutorial/user.png "Kullanıcı Oluştur")

    a. İlgili metin kutularına sağlamak istediğiniz geçerli bir Azure AD hesabının **e-posta adresini**, **adını** ve **soyadını** yazın.

    b. **Kullanıcı oluştur**' u seçin.

>[!NOTE]
>Azure Active Directory hesap sahibi, hesabı etkin hale gelmeden önce, hesabı onaylamaya yönelik bir bağlantı içeren bir e-posta alır. 

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Adobe Sign oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan Adobe oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım 'daki Adobe Sign kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Adobe Sign 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Adobe Sign 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).