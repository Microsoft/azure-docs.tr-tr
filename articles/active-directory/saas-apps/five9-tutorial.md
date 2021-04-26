---
title: 'Öğretici: Five9 Plus bağdaştırıcısıyla Azure Active Directory tümleştirme (CTı, kişi merkezi aracıları) | Microsoft Docs'
description: Azure Active Directory ve Five9 Plus bağdaştırıcısı (CTı, kişi merkezi aracıları) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 85e953951d5368dc97312e7810f3c356bda7c6b6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218728"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Öğretici: Five9 Plus bağdaştırıcısıyla Azure Active Directory tümleştirme (CTı, kişi merkezi aracıları)

Bu öğreticide, Five9 Plus bağdaştırıcısını (CTı, Iletişim merkezi aracıları) Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Five9 Plus bağdaştırıcısını (CTı, kişi merkezi aracıları) Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Five9 Plus bağdaştırıcısına erişimi olan denetim (CTı, kişi merkezi aracıları).
* Kullanıcılarınızın Azure AD hesaplarıyla Five9 Plus bağdaştırıcısı (CTı, Iletişim merkezi aracıları) ile otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Five9 Plus bağdaştırıcısı (CTı, kişi merkezi aracıları) ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Five9 Plus bağdaştırıcısı (CTı, kişi merkezi aracıları) çoklu oturum açma özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Five9 Plus bağdaştırıcısı (CTı, kişi merkezi aracıları) **IDP** tarafından başlatılan SSO 'yu destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Galeriden Five9 Plus bağdaştırıcısı (CTı, kişi merkezi aracıları) ekleme

Five9 Plus bağdaştırıcısının (CTı, Ilgili kişi merkezi aracıları) tümleştirmesini Azure AD ile yapılandırmak için, galerideki Five9 Plus bağdaştırıcısı (CTı, Iletişim merkezi aracıları) öğesini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Five9 Plus Adapter (CTI, Contact Center aracıları)** yazın.
1. Sonuçlar panelinden **Five9 Plus bağdaştırıcısını (CTI, kişi merkezi aracıları)** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-five9-plus-adapter-cti-contact-center-agents"></a>Five9 Plus bağdaştırıcısı için Azure AD SSO 'yu yapılandırma ve test etme (CTı, kişi merkezi aracıları)

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Five9 Plus BAĞDAŞTıRıCıSı (CTI, kişi merkezi aracıları) ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Five9 Plus bağdaştırıcısı (CTı, kişi merkezi aracıları) içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Five9 Plus bağdaştırıcısı (CTı, kişi merkezi aracıları) ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Five9 Plus bağdaştırıcısını (CTI, kişi merkezi aracıları) SSO 'Yu yapılandırın](#configure-five9-plus-adapter-cti-contact-center-agents-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Five9 Plus bağdaştırıcısında (CTı, Ilgili merkezi aracılarında) B. Simon 'a sahip olmak için **[Five9 Plus bağdaştırıcısı (CTI, kişi merkezi aracıları) oluşturun](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Five9 Plus bağdaştırıcısı (CTI, kişi merkezi aracıları)** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **SAML Ile tek Sign-On ayarlama** sayfasında, aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki URL 'lerden birini yazın:
    
    |    Ortam      |       URL      |
    | :-- | :-- |
    | "Microsoft Dynamics CRM için Five9 Plus bağdaştırıcısı" için | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | "Zendesk için Five9 Plus bağdaştırıcısı" için | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Aracı masaüstü araç seti için Five9 Plus bağdaştırıcısı" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki URL 'lerden birini yazın:

    |      Ortam     |      URL      |
    | :--                  | :--           |
    | "Microsoft Dynamics CRM için Five9 Plus bağdaştırıcısı" için | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | "Zendesk için Five9 Plus bağdaştırıcısı" için | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Aracı masaüstü araç seti için Five9 Plus bağdaştırıcısı" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **Five9 Plus bağdaştırıcısı ayarlama (CTI, kişi merkezi aracıları)** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Five9 Plus bağdaştırıcısına (CTı, kişi merkezi aracıları) erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Five9 Plus bağdaştırıcısı (CTI, kişi merkezi aracıları)** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-five9-plus-adapter-cti-contact-center-agents-sso"></a>Five9 Plus bağdaştırıcısını yapılandırma (CTı, kişi merkezi aracıları) SSO

1. **Five9 Plus bağdaştırıcısı (CTI, kişi merkezi aracıları)** tarafında çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (base64)** ve uygun kopyalanmış URL 'leri [Five9 Plus bağdaştırıcı (CTI, bağlantı merkezi aracıları) destek ekibine](https://www.five9.com/about/contact)göndermeniz gerekir. Ayrıca, SSO 'yu daha fazla yapılandırmak için lütfen bağdaştırıcıya göre aşağıdaki adımları izleyin:

    a. "Aracı masaüstü araç seti için Five9 Plus bağdaştırıcısı" Yönetici Kılavuzu: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Microsoft Dynamics CRM için Five9 Plus bağdaştırıcısı" Yönetici Kılavuzu: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Zendesk için Five9 Plus bağdaştırıcısı" Yönetici Kılavuzu: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Five9 Plus bağdaştırıcısı oluşturma (CTı, kişi merkezi aracıları) test kullanıcısı

Bu bölümde, Five9 Plus bağdaştırıcısında (CTı, kişi merkezi aracıları) Britta Simon adlı bir Kullanıcı oluşturacaksınız. Kullanıcıları Five9 Plus bağdaştırıcı (CTı, Ilgili merkezi aracıları) platformunda eklemek için [Five9 Plus bağdaştırıcısı (CTI, kişi merkezi aracıları) destek ekibi](https://www.five9.com/about/contact) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir. 

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız Five9 Plus bağdaştırıcısında (CTı, Iletişim merkezi aracıları) otomatik olarak oturum açmış olmanız gerekir.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım 'daki Five9 Plus bağdaştırıcı (CTı, kişi merkezi aracıları) kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Five9 Plus bağdaştırıcısında (CTı, Ilgili kişi merkezi aracıları) otomatik olarak oturum açmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

Five9 Plus bağdaştırıcısını yapılandırdıktan sonra (CTı, kişi merkezi aracıları), kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
