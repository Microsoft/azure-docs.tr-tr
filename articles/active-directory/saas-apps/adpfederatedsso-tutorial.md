---
title: 'Öğretici: ADP ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve ADP arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: cff4a75468181354a2ff61c0f9ac36bf6c78b9dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736159"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Öğretici: ADP ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, ADP 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. ADP 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, ADP erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak ADP 'ye oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* ADP çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* ADP **IDP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-adp-from-the-gallery"></a>Galeriden ADP ekleme

ADP 'yi Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden ADP 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ADP** yazın.
1. Sonuçlar panelinden **ADP** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-adp"></a>ADP için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu ADP ile yapılandırın ve test edin. SSO 'nun çalışması için, ADP 'de bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu ADP ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[ADP SSO 'Yu yapılandırın](#configure-adp-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan ADP 'de B. Simon 'a sahip olmak için, **[ADP test kullanıcısı oluşturun](#create-adp-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **ADP** uygulama tümleştirmesi sayfasında, **Özellikler sekmesine** tıklayın ve aşağıdaki adımları uygulayın: 

    ![Çoklu oturum açma özellikleri](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. **Kullanıcıların oturum açma** alanı değerini **Evet** olarak ayarlayın.

    b. **Kullanıcı erişim URL 'sini** kopyalayın ve bunu Öğreticinin ilerleyen kısımlarında açıklanan **oturum açma URL 'sini yapılandırın bölümüne** yapıştırmanız gerekir.

    c. **Kullanıcı Ataması gerekli** alan değerini **Evet** olarak ayarlayın.

    d. **Kullanıcılar Için görünür** alan değerini **Hayır** olarak ayarlayın.

1. Azure portal, **ADP** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    **Tanımlayıcı (VARLıK kimliği)** metin kutusuna bir URL yazın:`https://fed.adp.com`

4. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **ADP ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, B. Simon 'u, ADP 'ye erişim vererek Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **ADP**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-adp-sso"></a>ADP SSO 'yu yapılandırma

**ADP** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **meta veri XML** 'sini [ADP Web sitesine](https://adpfedsso.adp.com/public/login/index.fcc)yüklemeniz gerekir.

> [!NOTE]  
> Bu işlem birkaç gün sürebilir.

### <a name="configure-your-adp-services-for-federated-access"></a>Federasyon erişimi için ADP hizmetinizi yapılandırma

>[!Important]
> ADP hizmetinize federasyon erişimi gerektiren çalışanlarınız, ADP hizmeti uygulamasına atanmalıdır ve daha sonra kullanıcıların belirli ADP hizmetine yeniden atanması gerekir.
ADP temsilcinizden onay alındıktan sonra, ADP hizmetinizi yapılandırın ve belirli ADP hizmetine Kullanıcı erişimini denetlemek için kullanıcıları atayın/yönetin.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ADP** yazın.
1. Sonuçlar panelinden **ADP** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.
1. Azure portal, **ADP** uygulama tümleştirme sayfanızda, **Özellikler sekmesine** tıklayın ve aşağıdaki adımları uygulayın:  

    ![Çoklu oturum açma bağlantılı özellikleri](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    1. **Kullanıcıların oturum açma** alanı değerini **Evet** olarak ayarlayın.

    1. **Kullanıcı Ataması gerekli** alan değerini **Evet** olarak ayarlayın.

    1. **Kullanıcılar Için görünür** alan değerini **Evet** olarak ayarlayın.

1. Azure portal, **ADP** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.

1. **Çoklu oturum açma yöntemi seç** iletişim kutusunda, **bağlı** olarak **mod** ' u seçin. Uygulamanızı **ADP**'e bağlamak için.

    ![Çoklu oturum açma bağlı](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. **Oturum açma URL 'Sini yapılandırma** bölümüne gidin ve aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açma Prop](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    1. Yukarıdaki **Özellikler sekmesinden** kopyaladığınız **Kullanıcı erişim URL 'sini** yapıştırın (ana ADP uygulamasından).

    1. Farklı **geçiş durumu URL 'lerini** destekleyen 5 uygulama aşağıda verilmiştir. Belirli bir uygulama için uygun **geçiş durumu URL 'si** değerini **Kullanıcı erişim URL**'sine el ile eklemeniz gerekir.

        * **ADP iş gücü artık**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

        * **ADP iş gücü artık gelişmiş saat**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`

        * **ADP Vanu HCM**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

        * **ADP kurumsal HR**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

        * **MyADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

1. Değişikliklerinizi **kaydedin** .

1. ADP temsilcinizden onay alındıktan sonra, bir veya iki kullanıcıyla testi başlatın.

    1. Federasyon erişimini test etmek için, ADP hizmeti uygulamasına birkaç Kullanıcı atayın.

    1. Kullanıcılar galerideki ADP hizmeti uygulamasına erişirken ve ADP hizmetine erişebillerinde test başarılı olur.

1. Başarılı bir testi onaylamada, Federasyon ADP hizmetini ayrı kullanıcılara veya Kullanıcı gruplarına atayın. Bu, öğreticide daha sonra açıklanarak çalışanlarınıza ulaşın.

### <a name="create-adp-test-user"></a>ADP test kullanıcısı oluştur

Bu bölümün amacı, ADP 'de B. Simon adlı bir Kullanıcı oluşturmaktır. ADP hesabındaki kullanıcıları eklemek için [ADP destek ekibi](https://www.adp.com/contact-us/overview.aspx) ile çalışın. 

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız ADP 'de otomatik olarak oturum açmış olmanız gerekir

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki ADP kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız ADP 'de otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

ADP 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).