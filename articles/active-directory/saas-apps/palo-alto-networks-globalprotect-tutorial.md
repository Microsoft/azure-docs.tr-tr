---
title: 'Öğretici: Palo Alto Networks ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory-GlobalProtect | Microsoft Docs'
description: Azure Active Directory ve Palo Alto Networks-GlobalProtect arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 72072feebfcf8dba249d2045a399e09714177698
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963698"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Öğretici: Palo Alto Networks ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory-GlobalProtect

Bu öğreticide, Palo Alto Networks-GlobalProtect Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile Palo Alto ağlarını tümleştirdiğinizde, şunları yapabilirsiniz:

* Azure AD 'de, Palo Alto Networks-GlobalProtect erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla ağları genele almak için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Palo Alto Networks-GlobalProtect çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Palo Alto ağları-GlobalProtect, **SP** tarafından başlatılan SSO 'yu destekler
* Palo Alto ağları-GlobalProtect **, tam zamanında** Kullanıcı sağlamasını destekler

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Izo Alto Networks 'e ekleme-galerideki GlobalProtect

Azure AD 'de Palo Alto Networks-GlobalProtect tümleştirmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Palo Alto ağlarını-GlobalProtect ' i eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Palo Alto Networks-GlobalProtect** yazın.
1. **Palo Alto ağları-sonuçlar panelinden GlobalProtect** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---globalprotect"></a>Palo Alto Networks için Azure AD SSO 'Yu yapılandırma ve test etme-GlobalProtect

Azure AD SSO 'Yu, Palo Alto Networks-GlobalProtect ile, **B. Simon** adlı bir test kullanıcısı kullanarak yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Palo Alto Networks-GlobalProtect içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'Yu Palo Alto Networks-GlobalProtect ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[Palo Alto ağlarını-GlobalProtect SSO 'Yu yapılandırın](#configure-palo-alto-networks---globalprotect-sso)** .
    1. **[Palo Alto Networks-GlobalProtect test kullanıcısını oluşturma](#create-palo-alto-networks---globalprotect-test-user)** -Azure AD gösterimine bağlı olan Palo Alto Networks-GlobalProtect.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Palo Alto Networks-GlobalProtect** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<Customer Firewall URL>`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Palo Alto Networks-GlobalProtect istemci destek ekibine](https://support.paloaltonetworks.com/support) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Palo Alto ağlarını ayarla-GlobalProtect** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Palo Alto Networks-GlobalProtect 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Palo Alto Networks-GlobalProtect**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Palo Alto ağlarını yapılandırma-GlobalProtect SSO

1. Diğer bir tarayıcı penceresinde, Palo Alto ağlarını-GlobalProtect ' u yönetici olarak açın.

2. **Cihaz**' a tıklayın.

    ![Palo Alto 'ı çoklu oturum açma 1 ' i yapılandırma](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Sol gezinti çubuğundan **SAML kimlik sağlayıcısı** ' nı seçin ve meta veri dosyasını içeri aktarmak Için "Al" a tıklayın.

    ![Palo Alto çoklu oturum açmayı yapılandırma 2](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Içeri aktarma penceresinde aşağıdaki eylemleri gerçekleştirin

    ![Palo Alto 'ı çoklu oturum açma 3 olarak yapılandırma](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. **Profil adı** metin kutusuna bir ad girin; Örneğin, Azure AD GlobalProtect.

    b. **Kimlik sağlayıcısı meta verileri**' nde, **Araştır** ' a tıklayın ve indirdiğiniz metadata.xml dosyayı seçin Azure Portal

    c. **Tamam 'a** tıklayın

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Palo Alto Networks oluşturma-GlobalProtect test kullanıcısı

Bu bölümde, Palo Alto Networks-GlobalProtect içinde B. Simon adlı bir Kullanıcı oluşturulur. Palo Alto Networks-GlobalProtect, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Palo Alto Networks-GlobalProtect içinde mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu işlem, oturum açma akışını başlatabileceğiniz bir oturum açma URL 'SI olan, Palo Alto Networks 'a yönlendirilir. 

* Palo Alto Networks 'e gidin-GlobalG oturum açma URL 'sini doğrudan koruyun ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Palo Alto Networks-GlobalProtect kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Palo Alto Networks-GlobalProtect ' de otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Palo Alto ağlarını-GlobalProtect ' ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).