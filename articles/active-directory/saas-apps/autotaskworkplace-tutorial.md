---
title: 'Öğretici: oto görev çalışma alanıyla Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve oto çalışma alanı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 1a2602ee3b8fad6e87a778ab966f7cb1f8aad8c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649980"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Öğretici: oto görev çalışma alanıyla Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile, oto görev çalışma alanını tümleştirmeyi öğreneceksiniz. Azure AD ile oto görev çalışma alanını tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, oto görev çalışma alanına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik görev çalışma alanına otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Oto görev çalışma alanı çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Oto görevi çalışma alanı **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="add-autotask-workplace-from-the-gallery"></a>Galeriden tekrar görev çalışma alanı Ekle

Oto görev çalışma alanının Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize yeniden görev çalışma alanı eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna, **oto görevi çalışma alanı** yazın.
1. Sonuçlar panelinden **oto görevi çalışma alanı** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-autotask-workplace"></a>Oto görev çalışma alanı için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, oto görev çalışma ALANıYLA Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve diğer görev çalışma alanındaki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu, oto görev çalışma alanıyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[oto görev çalışma alanı SSO 'Yu yapılandırın](#configure-autotask-workplace-sso)** .
    1. Kullanıcı Azure AD gösterimine bağlı olan, oto görev çalışma alanında B. Simon 'ya sahip olmak için, **[oto görev çalışma alanı test kullanıcısı oluşturun](#create-autotask-workplace-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **oto görevi çalışma alanı** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü; tanımlayıcı girebileceğiniz, yanıt U R L ve Kaydet ' i seçebileceğiniz temel SAML yapılandırmasını gösterir.](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Ekran görüntüsü, U R L 'ye bir Işaret girebileceğiniz ek U R 'Leri ayarlamayı gösterir.](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için, [oto görev çalışma alanı istemci desteği ekibine](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

7. Alt **görev çalışma alanını ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak, oto görev çalışma alanına erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **oto görevi çalışma alanı**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-autotask-workplace-sso"></a>Oto görevi çalışma alanı SSO 'SU yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici kimlik bilgilerini kullanarak çalışma alanı çevrimiçi olarak oturum açın.

    > [!Note]
    > IDP 'yi yapılandırırken bir alt etki alanının belirtilmesi gerekir. Doğru alt etki alanını onaylamak için çalışma alanına çevrimiçi oturum açın. Oturum açtıktan sonra URL 'deki alt etki alanına dikkat edin. Alt etki alanı, "https://" ve ". awp.autotask.net/" arasındaki kısmıdır ve ABD, AB, CA veya au olmalıdır.

2. **Yapılandırma**  >  **Çoklu oturum açma** sayfasına gidin ve aşağıdaki adımları gerçekleştirin:

    ![Oto görevi çoklu oturum açma yapılandırması](./media/autotaskworkplace-tutorial/configuration-1.png)

    a. **XML meta veri dosyası** seçeneğini belirleyin ve ardından Azure Portal Indirilen **Federasyon meta veri XML** dosyasını karşıya yükleyin.

    b. **SSO 'Yu etkinleştir**' e tıklayın.

    ![Oto görevi çoklu oturum açma onaylama yapılandırması](./media/autotaskworkplace-tutorial/configuration-2.png)

    c. **Bu bilgilerin doğru olduğunu ve bu IDP 'ye güveneceğim** onay kutusunu seçin.

    d. **Onayla**' ya tıklayın.

> [!Note]
> Yeniden görev çalışma alanını yapılandırmaya yönelik yardıma ihtiyacınız varsa, lütfen çalışma alanı hesabınızla ilgili yardım almak için [Bu sayfaya](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) bakın.

### <a name="create-autotask-workplace-test-user"></a>Oto görevi çalışma alanı test kullanıcısı oluştur

Bu bölümde, oto görevi çalışma alanında Britta Simon adlı bir Kullanıcı oluşturacaksınız. Lütfen, kullanıcıları yeniden görev çalışma alanı platformuna eklemek için, [oto görev çalışma alanı destek ekibi](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) ile çalışın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz tekrar görev çalışma alanı oturum açma URL 'sine yeniden yönlendirilir.  

* Doğrudan görev çalışma alanı oturum açma URL 'sine doğrudan gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız otomatik görev çalışma alanında otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamda otomatik görev çalışma alanı kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız otomatik görev çalışma alanında otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Oto görevi çalışma alanını yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).