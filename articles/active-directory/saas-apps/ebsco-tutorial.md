---
title: 'Öğretici: EBSCO ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ve EBSCO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f37085744b9a0e7785ef3a411d53e4df5d15e494
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595020"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Öğretici: EBSCO ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, EBSCO 'yı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. EBSCO 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, EBSCO 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla, EBSCO 'da otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* EBSCO çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* EBSCO **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* EBSCO **, tam zamanında** Kullanıcı sağlamayı destekliyor

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-ebsco-from-the-gallery"></a>Galeriden EBSCO ekleme

EBSCO 'nın tümleştirmesini Azure AD 'ye göre yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize EBSCO eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **EBSCO** yazın.
1. Sonuçlar panelinden **EBSCO** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>EBSCO için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak EBSCO Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve EBSCO 'daki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu EBSCO ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[EBSCO SSO 'Yu yapılandırma](#configure-ebsco-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * **[EBSCO test kullanıcısı oluşturun](#create-ebsco-test-user)** ; bu, kullanıcının Azure AD gösterimine bağlı olan EBSCO 'da B. Simon 'a karşılık gelen bir karşılığına sahip olur.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **EBSCO** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    **Tanımlayıcı** metin kutusuna bir URL yazın: `pingsso.ebscohost.com`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Oturum açma URL 'SI değeri gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [EBSCO istemci destek ekibine](mailto:support@ebsco.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

    o **benzersiz öğeler:**  

    o **CustId** = benzersiz EBSCO müşteri kimliğini girin 

    o **profile** = istemciler, kullanıcıları belirli bir profile (EBSCO 'dan satın aldıklarına bağlı olarak) yönlendirebilir. Belirli bir profil KIMLIĞI girebilirler. Ana kimlikler EDS (EBSCO bulma hizmeti) ve ehost (EBSOCOhost veritabanları) ' dir. Aynı yönergeler [burada](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)verilmiştir.

1. EBSCO uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

    > [!Note]
    > **Name** özniteliği zorunludur ve EBSCO uygulamasındaki **ad tanımlayıcı değeriyle** eşlenir. Bu, varsayılan olarak eklenerek el ile eklemeniz gerekmez.

1. Daha fazlasına ek olarak, EBSCO uygulaması aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Adı | Kaynak özniteliği|
    | ---------------| --------------- |
    | firstName   | Kullanıcı. |
    | Soyadı   | User. soyadı |
    | E-posta   | Kullanıcı. Mail |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **EBSCO ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, EBSCO 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **EBSCO**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-ebsco-sso"></a>EBSCO SSO 'yu yapılandırma

**EBSCO** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve uygun kopyalanmış URL 'Leri Azure Portal ' den [EBSCO destek ekibine](mailto:support@ebsco.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-ebsco-test-user"></a>EBSCO test kullanıcısı oluşturma

EBSCO durumunda, Kullanıcı sağlama otomatiktir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

Azure AD, gerekli verileri EBSCO uygulamasına geçirir. EBSCO 'nın Kullanıcı sağlaması otomatik olabilir veya tek seferlik bir form gerektirebilir. Bu, istemcinin kişisel ayarları kayıtlı olan çok sayıda daha önceden var olan EBSCOhost hesabına sahip olup olmamasına bağlıdır. Aynı şekilde, uygulama sırasında [EBSCO destek ekibi](mailto:support@ebsco.com) ile de tartışılmış olabilir. Her iki durumda da, istemci test etmeden önce herhangi bir EBSCOhost hesabı oluşturmak zorunda değildir.

   > [!Note]
   > EBSCOhost Kullanıcı sağlama/kişiselleştirmesini otomatik hale getirebilirsiniz. Tam zamanında Kullanıcı hazırlama hakkında [EBSCO destek ekibine](mailto:support@ebsco.com) başvurun.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

1. Erişim panelinde EBSCO kutucuğuna tıkladığınızda, EBSCO uygulamanızda otomatik olarak oturum açmış olmanız gerekir.
Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/active-directory-saas-access-panel-introduction.md).

1. Uygulamada oturum açtıktan sonra sağ üst köşedeki **oturum aç** düğmesine tıklayın.

    ![Uygulamalar listesinde EBSCO oturum açma](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. **Mevcut MyEBSCOhost hesabınızı şimdi kurum hesabınıza bağlamak** veya **Yeni bir Myebscohost hesabı oluşturmak ve bunu kurum hesabınıza bağlamak**için tek seferlik bir istem alacaksınız. Hesap, EBSCOhost uygulamasında kişiselleştirme için kullanılır. **Yeni hesap oluştur** seçeneğini belirleyin ve aşağıdaki ekran görüntüsünde gösterildiği gibi, kişiselleştirme formunun SAML yanıtından alınan değerlerle önceden tamamlandığını görürsünüz. Bu seçimi kaydetmek için **' devam '** düğmesine tıklayın.
    
     ![Uygulamalar listesindeki EBSCO kullanıcısı](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Yukarıdaki kurulumu tamamladıktan sonra, tanımlama bilgileri/önbellek seçimini kaldırın ve yeniden oturum açın. El ile yeniden oturum açmanız gerekmez ve kişiselleştirme ayarları hatırlanır.

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile EBSCO 'yi deneyin](https://aad.portal.azure.com/)