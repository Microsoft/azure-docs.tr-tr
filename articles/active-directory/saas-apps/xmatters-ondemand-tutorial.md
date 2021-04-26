---
title: 'Öğretici: Xönemli OnDemand ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Xönemli OnDemand arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: jeedes
ms.openlocfilehash: 762bd1c536df0ca307149ba7c201f08f5bdfded5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735633"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Öğretici: Xönemli OnDemand ile tümleştirme Azure Active Directory

Bu öğreticide, Xönemli OnDemand Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Azure AD ile Xönemli OnDemand tümleştirme aşağıdaki avantajları sağlar:

* Azure AD 'de, Xönemli OnDemand erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak bir Xönemli OnDemand (çoklu oturum açma) ile oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Xönemli OnDemand ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Xönemli OnDemand çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Xönemli OnDemand **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Galeriden Xönemli OnDemand ekleme

Azure AD 'de Xönemli OnDemand tümleştirmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Xönemli OnDemand eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **xönemli OnDemand** yazın.
1. Sonuçlar panelinden **Xönemli OnDemand** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-xmatters-ondemand"></a>Azure AD SSO 'yu Xönemli OnDemand için yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Xönemli OnDemand ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında Xönemli OnDemand arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Xönemli OnDemand ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    2. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Xönemli ONDEMAND SSO 'Yu yapılandırın](#configure-xmatters-ondemand-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Xönemli bir OnDemand ' de Britta Simon 'ın bir karşılığı olacak şekilde **[xönemli bir test kullanıcısı oluşturun](#create-xmatters-ondemand-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Xönemli OnDemand** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    | Tanımlayıcı |
    | ---------- |
    | `https://<companyname>.au1.xmatters.com.au/` |
    | `https://<companyname>.cs1.xmatters.com/` |
    | `https://<companyname>.xmatters.com/` |
    | `https://www.xmatters.com` |
    | `https://<companyname>.xmatters.com.au/` |

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    | Yanıt URL'si |
    | ---------- |
    |  `https://<companyname>.au1.xmatters.com.au` |
    | `https://<companyname>.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.au1.xmatters.com.au/<instancename>` |

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için, [Xönemli OnDemand istemci destek ekibine](https://www.xmatters.com/company/contact-us/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

    > [!IMPORTANT]
    > Sertifikayı [Xönemli ondemandsupport ekibine](https://www.xmatters.com/company/contact-us/)iletmeniz gerekir. Çoklu oturum açma yapılandırmasını sonuçlayabilmeniz için önce sertifikanın, Xönemli destek ekibi tarafından yüklenmesi gerekir.

6. **Xönemli OnDemand ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Azure AD test kullanıcısını atama

Bu bölümde, Xönemli OnDemand erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Xönemli OnDemand** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.


## <a name=&quot;configure-xmatters-ondemand-sso&quot;></a>Xönemli OnDemand SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir yönetici olarak Xönemli OnDemand şirket sitenizde oturum açın.

2. **Yönetici**' ye ve ardından **Şirket ayrıntıları**' na tıklayın.

    ![Yönetici sayfası](./media/xmatters-ondemand-tutorial/admin.png &quot;Yönetici")

3. **SAML yapılandırması** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![SAML yapılandırma bölümü ](./media/xmatters-ondemand-tutorial/saml-configuration.png "SAML yapılandırması")

    a. **SAML etkinleştir**' i seçin.

    b. **Kimlik sağlayıcısı kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    c. **Çoklu oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. **Oturum kapatma URL 'Si yeniden yönlendirme** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'sini** yapıştırın.

    e. Azure portal indirdiğiniz **sertifikayı (base64)** karşıya yüklemek Için **Dosya Seç** ' e tıklayın. 

    f. Şirket ayrıntıları sayfasında, üstteki **Değişiklikleri Kaydet**' e tıklayın.

    ![Şirket ayrıntıları](./media/xmatters-ondemand-tutorial/save-button.png "Şirket ayrıntıları")

### <a name="create-xmatters-ondemand-test-user"></a>Xönemli OnDemand test kullanıcısı oluşturma

1. **Xönemli OnDemand** kiracınızda oturum açın.

2. **Kullanıcılar simgesi**  >  **Kullanıcılar** ' a gidin ve ardından **Kullanıcı Ekle**' ye tıklayın.

    ![Kullanıcılar](./media/xmatters-ondemand-tutorial/add-user.png "Kullanıcılar")

3. **Kullanıcıları Ekle** bölümünde gerekli alanları doldurup **Kullanıcı Ekle** düğmesine tıklayın.

    ![Kullanıcı ekleme](./media/xmatters-ondemand-tutorial/add-user-2.png "Kullanıcı ekleme")



### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız Xönemli OnDemand ' de otomatik olarak oturum açmış olmanız gerekir

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Xönemli OnDemand kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Xönemli OnDemand ' de otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Xönemli OnDemand ' ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).