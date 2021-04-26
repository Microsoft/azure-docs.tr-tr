---
title: 'Öğretici: müşteri için SAP bulutu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Müşteri için Azure Active Directory ve SAP bulutu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 60381c7d8c452277b53e1af67ae7fc85349521c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735682"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Öğretici: müşteri için SAP bulutu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile müşteri için SAP bulutunu tümleştirmeyi öğreneceksiniz. Müşteri için SAP Cloud 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, müşteri için SAP bulutuna erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesapları ile müşteri için SAP bulutuna otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Müşteri için SAP bulutu çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Müşteri için SAP bulutu, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Galeriden müşteri için SAP bulutu ekleme

Müşteri için SAP Cloud 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize müşteri için SAP bulutu eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **müşteri için SAP Cloud** yazın.
1. Sonuçlar panelinden **Müşteri Için SAP bulutu** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-for-customer"></a>Müşteri için SAP bulutu için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu MÜŞTERI Için SAP bulutu ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve müşteri için SAP bulutu 'ndaki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu müşteri için SAP bulutu ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[Müşteri SSO 'su IÇIN SAP bulutu 'Nı yapılandırın](#configure-sap-cloud-for-customer-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan müşteriler için SAP bulutu 'nda B. Simon 'un bir karşılığı olacak şekilde, **[Müşteri IÇIN SAP bulutu oluşturun](#create-sap-cloud-for-customer-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Müşteri Için SAP bulutu** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server name>.crm.ondemand.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Müşteri Için SAP bulutu istemci desteği ekibine](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Müşteri için SAP Cloud uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Kullanıcı öznitelikleri iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    !["Düzenle" simgesi seçili "Kullanıcı öznitelikleri" iletişim kutusunu gösteren ekran görüntüsü.](common/edit-attribute.png)

1. **Kullanıcı öznitelikleri & talepler** Iletişim kutusundaki **Kullanıcı öznitelikleri** bölümünde aşağıdaki adımları uygulayın:

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **Düzenle simgesine** tıklayın.

    !["Düzenle" simgesi seçili "Kullanıcı özniteliklerini & taleplerini" gösteren ekran görüntüsü.](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. **Kaynak** olarak **dönüşüm** ' i seçin.

    c. **Dönüştürme** listesinden **Extractmailprefix ()** öğesini seçin.

    d. **Parametre 1** listesinden, uygulamanız için kullanmak istediğiniz kullanıcı özniteliğini seçin.
    Örneğin, EmployeeID 'yi benzersiz kullanıcı tanımlayıcısı olarak kullanmak istiyorsanız ve öznitelik değerini ExtensionAttribute2 içinde depoladıysanız User. ExtensionAttribute2 ' yi seçin.

    e. **Kaydet**’e tıklayın.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Müşteri IÇIN SAP Cloud ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, müşteri için SAP buluta erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Müşteri Için SAP bulutu**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-sap-cloud-for-customer-sso"></a>Müşteri SSO 'SU için SAP bulutu yapılandırma

1. Yeni bir Web tarayıcı penceresi açın ve müşteri için SAP Cloud şirket sitenizde yönetici olarak oturum açın.

2. Menünün sol tarafında, **kimlik sağlayıcıları**  >  **Kurumsal kimlik sağlayıcıları**' na tıklayın  >   ve açılır pencerede **Azure AD** gibi kimlik sağlayıcısı adını ekleyin, **Kaydet** ' e tıklayın, ardından **SAML 2,0 yapılandırması**' na tıklayın.

    !["Kimlik sağlayıcısı ekle" iletişim kutusu metin kutusu vurgulanmış ve "Kaydet" düğmesi seçili olan "kimlik sağlayıcıları" sayfasını gösteren ekran görüntüsü.](./media/sap-customer-cloud-tutorial/configure01.png)

3. **SAML 2,0 yapılandırma** bölümünde aşağıdaki adımları uygulayın:

    !["A M L 2,0 yapılandırmasını" gösteren ve "araştır" düğmesi seçili olan ekran görüntüsü.](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Azure portal 'ten indirdiğiniz Federasyon meta veri XML dosyasını karşıya yüklemek için, **Araştır** ' a tıklayın.

    b. XML dosyası başarıyla karşıya yüklendikten sonra, aşağıdaki değerler otomatik olarak doldurulur ve **Kaydet**' e tıklayın.

### <a name="create-sap-cloud-for-customer-test-user"></a>Müşteri için SAP bulutu test kullanıcısı oluşturma

Azure AD kullanıcılarının müşteri için SAP bulutu 'nda oturum açmasını sağlamak için, müşteri için SAP Cloud 'a sağlanması gerekir. Müşteri için SAP bulutu 'nda sağlama işlemi el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Bir güvenlik yöneticisi olarak müşteri için SAP bulutu 'nda oturum açın.

2. Menünün sol tarafında **Kullanıcılar & yetkilendirmeler**  >  **Kullanıcı yönetimi**  >  **Kullanıcı Ekle**' ye tıklayın.

    !["Kullanıcı Ekle" düğmesinin seçili olduğu "Kullanıcı Yönetimi" sayfasını gösteren ekran görüntüsü.](./media/sap-customer-cloud-tutorial/configure03.png)

3. **Yeni Kullanıcı Ekle** bölümünde aşağıdaki adımları uygulayın:

    ![SAP yapılandırması](./media/sap-customer-cloud-tutorial/configure04.png)

    a. **Ad** metin kutusuna **B** gibi kullanıcının adını girin.

    b. **Soyadı** metin kutusuna **Simon** gibi kullanıcının adını girin.

    c. **E-posta** metin kutusuna kullanıcının e-postasını girin `B.Simon@contoso.com` .

    d. **Oturum açma adı** metin kutusuna **B. Simon** gibi kullanıcının adını girin.

    e. Gereksiniminize göre **Kullanıcı türü** ' nü seçin.

    f. Gereksiniminize göre **hesap etkinleştirme** seçeneğini belirleyin.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz müşteri oturum açma URL 'SI için SAP bulutuna yönlendirilir. 

* Müşteri için SAP Cloud oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki müşteri için SAP bulutu kutucuğunu tıklattığınızda, bu, müşteri oturum açma URL 'SI için SAP bulutu 'na yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Müşteri için SAP bulutunu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimlerini zorunlu kılabilirsiniz. Oturum denetimleri koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad).