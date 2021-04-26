---
title: 'Öğretici: Cisco ıntertim ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ve Cisco ınterg arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 6efec61ec5866547ab6b432cb9c566642f3191ff
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580792"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-intersight"></a>Öğretici: Cisco ıntertim ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Cisco görüşmesini tümleştirmeyi öğreneceksiniz. Cisco Inter, Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Cisco görüşe erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Cisco görüşe otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Cisco ıntergörüş çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Cisco ıntergörüş, **SP** tarafından başlatılan SSO 'yu destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-cisco-intersight-from-the-gallery"></a>Galeriden Cisco görüşi ekleme

Cisco görüşmesinin Azure AD ile tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Cisco görüşimi eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Cisco ınterbox** yazın.
1. Sonuçlar panelinden **Cisco ıntergözetimi** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-cisco-intersight"></a>Cisco ıntertim için Azure AD SSO 'yu yapılandırma ve test etme

Azure AD SSO 'yu, **B. Simon** adlı bir test kullanıcısı kullanarak Cisco ıntertim ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Cisco ınterıltıondaki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Cisco ıntertim ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Cisco ıntertim SSO 'Yu yapılandırın](#configure-cisco-intersight-sso)** .
    1. Kullanıcı Azure AD gösterimi ile bağlantılı olan Cisco ınterım 'de B. Simon 'a sahip olmak için **[Cisco ınterıltıya yönelik test kullanıcısı oluşturun](#create-cisco-intersight-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Cisco ıntergörüş** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusuna URL 'yi yazın:`https://intersight.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metın kutusuna URL yazın:`www.intersight.com`

1. Cisco ıntertim uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde buna bir örnek gösterilmektedir. **Benzersiz kullanıcı tanımlayıcısının** varsayılan değeri **User. UserPrincipalName** ' dir ancak Cisco ıntergözetimi bunun kullanıcının e-posta adresiyle eşleştirilmesini bekliyor. Bu şekilde, listeden **User. Mail** özniteliğini kullanabilir veya kuruluşunuzun yapılandırmasına göre uygun öznitelik değerini kullanabilirsiniz.

    ![image](common/default-attributes.png)

1. Daha fazlasına ek olarak, Cisco ınteriletilen uygulama, aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name |  Kaynak özniteliği|
    | -------------- | --------- |
    | First_Name | Kullanıcı. |
    | Last_Name | User. soyadı |
    | Üyesi | Kullanıcı. gruplar |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Cisco ınterstatıon ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak Cisco görüşe erişim izni vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Cisco ıntertim**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-cisco-intersight-sso"></a>Cisco ıntertim SSO 'yu yapılandırma

**Cisco ıntertim** tarafında çoklu oturum açma 'yı yapılandırmak için, Indirilen **Federasyon meta verileri XML** 'sini ve Azure Portal ' den uygun kopyalanmış URL 'Leri [Cisco ınterıntertim destek ekibine](mailto:intersight-feedback@cisco.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-cisco-intersight-test-user"></a>Cisco ınterbir test kullanıcısı oluşturma

Bu bölümde, Cisco ıntertim 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. Cisco ınterıntertim platformunda kullanıcıları eklemek için [Cisco ıntertim destek ekibi](mailto:intersight-feedback@cisco.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Cisco ınterg oturum açma URL 'sine yönlendirecektir. 

* Doğrudan Cisco ınterg oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım 'daki Cisco görüş kutucuğuna tıkladığınızda bu, Cisco ıntergelen oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Sonraki adımlar

Cisco ıntertratıon 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


