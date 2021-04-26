---
title: 'Öğretici Azure Active Directory: denetim noktası kimliği tanıma ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve Check Point Identity tanımayı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 68705d4d1f870820e30563d2a197ac56349d2445
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520949"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-identity-awareness"></a>Öğretici: denetim noktası kimliği tanıma ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, denetim noktası kimlik tanımayı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Denetim noktası kimlik tanımayı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Denetim noktası kimlik tanıma erişimi olan Azure AD 'de denetim.
* Kullanıcıların Azure AD hesaplarıyla Işaret kimliği tanımayı denetlemek için otomatik olarak oturum açmaya olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Check Point Identity tanıma çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Check Point Identity tanımayı **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-check-point-identity-awareness-from-the-gallery"></a>Galeriden denetim noktası kimlik tanımayı ekleme

Check Point Identity tanımayı Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize denetim noktası kimliği tanımayı eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Check Point Identity tanımayı** yazın.
1. Sonuçlar panelinden **Check Point Identity tanımayı** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-check-point-identity-awareness"></a>Denetim noktası kimliği tanıma için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Check Point Identity tanımayı yapılandırın ve test edin. SSO 'nun çalışması için, denetim noktası kimliği tanıma 'da bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu denetim noktası kimliği tanıma ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Check Point Identity tanıma SSO 'Yu yapılandırın](#configure-check-point-identity-awareness-sso)** .
    1. Denetim noktası **[kimlik tanıma test kullanıcısı](#create-check-point-identity-awareness-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Check Point kimlik tanıma konusunda B. Simon 'ın bir karşılığı olacak şekilde.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **denetim noktası kimliği tanıma** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<GATEWAY_IP>/connect/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<GATEWAY_IP>/connect/spPortal/ACS/Login/<IDENTIFIER_UID>`

    c. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<GATEWAY_IP>/connect`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'si ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [Check Point Identity tanıma istemci desteği ekibine](mailto:support@checkpoint.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Denetim noktası kimliğini ayarlama tanıma** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, denetim noktası kimlik tanıma erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Check Point Identity tanımayı** seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-check-point-identity-awareness-sso"></a>Check Point kimlik tanıma SSO 'yu yapılandırma

1. Denetim noktası kimlik tanıma şirket sitesinde yönetici olarak oturum açın.

1. SmartConsole > **ağ geçitleri & sunucular** görünümünde, **yeni > > kullanıcı/kimlik > kimlik sağlayıcısı**' na tıklayın.

    ![Yeni kimlik sağlayıcısı için ekran görüntüsü.](./media/check-point-identity-awareness-tutorial/identity-provider.png)

1. **Yeni kimlik sağlayıcısı** penceresinde aşağıdaki adımları gerçekleştirin.

    ![Kimlik sağlayıcısı bölümünün ekran görüntüsü.](./media/check-point-identity-awareness-tutorial/new-identity-provider.png)

    a. **Ağ geçidi** alanında, SAML kimlik doğrulamasını gerçekleştirmesi gereken güvenlik ağ geçidini seçin.

    b. **Hizmet** alanında, açılan listeden **kimlik tanımayı** seçin.

    c. **Tanımlayıcı (VARLıK kimliği)** değerini Kopyala, bu değeri Azure Portal **temel SAML yapılandırması** bölümündeki **tanımlayıcı** metin kutusuna yapıştırın.

    d. **Yanıt URL 'si** değerini kopyalayın, bu değeri Azure Portal **temel SAML yapılandırması** bölümündeki **yanıt URL 'si** metin kutusuna yapıştırın.

    e. İndirilen **sertifikayı (base64)** Azure Portal yüklemek Için **meta veri dosyasını içeri aktar** ' ı seçin.

    > [!NOTE]
    > Alternatif olarak, **VARLıK kimliği** ve **oturum açma URL 'si** değerlerini karşılık gelen alanlara yapıştırmak ve **sertifika dosyasını** Azure Portal yüklemek için **el ile Ekle** ' yi de seçebilirsiniz.

    f. **Tamam**'a tıklayın.

### <a name="create-check-point-identity-awareness-test-user"></a>Check Point kimlik tanıma test kullanıcısı oluştur

Bu bölümde, Check Point kimlik tanıma 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. Check Point Identity tanıma platformunda kullanıcıları eklemek için [Check Point Identity tanıma destek ekibi](mailto:support@checkpoint.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz denetim noktası kimliği tanıma oturum açma URL 'sine yönlendirecektir. 

* Check Point Identity tanıma oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Check Point Identity tanıma kutucuğuna tıkladığınızda bu, Check Point Identity tanımayı oturum açma URL 'sine yönlendirir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Sonraki adımlar

Check Point Identity tanımayı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve infilliğini koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


