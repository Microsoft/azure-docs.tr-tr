---
title: 'Öğretici: Askışınsal ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Askkol arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 8ef2dadfc0099822d68a5b1cfb2cc35759e1a866
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457682"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askspoke"></a>Öğretici: Askkol ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile bir askın nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile Askkol tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Askteli erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla şirket içinde otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Askkol çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Askbağlı bileşen **SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Askışınsal 'nin **tam zamanında** Kullanıcı sağlamasını desteklediği
* Askıı 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-askspoke-from-the-gallery"></a>Galeriden Askkol ekleme

Azure AD 'ye Askışınsal 'nin tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize askını eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **askışınsal** yazın.
1. Sonuçlar panelinden **Askkol** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-askspoke"></a>Askışınsal için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Askışınsal ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Askışınsal içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Askışınsal ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Askkol SSO 'Yu yapılandırın](#configure-askspoke-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Askkol 'de B. Simon 'un bir karşılığı olacak şekilde, **[askkol test kullanıcısı oluşturun](#create-askspoke-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **askkol** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.askspoke.com/saml/callback`

    > [!NOTE]
    > Yanıt URL 'SI değeri gerçek değil. Bu değeri gerçek yanıt URL 'siyle güncelleştirin. Bu değeri almak için, [Askkol istemci destek ekibine](mailto:support@askspoke.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL** 'si metin kutusuna URL 'yi yazın:`https://askspoke.com/login`

1. Askkol uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdakine ek olarak, Askbağlı bileşen uygulaması aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name |  Kaynak özniteliği|
    | ---------------| --------- |
    | firstName | Kullanıcı. |
    | lastName | User. soyadı |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Askışınsal 'Yi ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak Askışınsal 'ye erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Askkol**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-askspoke-sso"></a>Askkol SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak Askkol Web sitesinde oturum açın.

1. Sol gezinti bölmesinden **Ayarlar** sekmesine tıklayın.

    ![Askkol ayarları sekmesi](./media/askspoke-tutorial/configure1.png)

1. **SSO** 'ya kaydırın ve **Bağlan**'a tıklayın.

    ![Askbağlı bileşen bağlantısı](./media/askspoke-tutorial/configure2.png)

1. **SAML & SCıM 'ı etkinleştir** bölümünde aşağıdaki adımları uygulayın:

    ![Askışınsal bağlı SAML & SCıM bölümünü etkinleştir](./media/askspoke-tutorial/configure3.png)

    1. Oturum açma **URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    1. **Veren** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    1. İndirilen **sertifika (base64)** dosyasını Azure Portal Not defteri ' nden açın ve sertifika dosyasının Içeriğini kopyalayıp **ortak sertifika** metin kutusuna yapıştırın.

    1. **ACS URL 'si** değerini kopyalayın ve Azure Portal **temel SAML YAPıLANDıRMASı** bölümünde **yanıt URL** metin kutusuna yapıştırın.

    1. **SAML bağlantısını sına**' ya tıklayın.

### <a name="create-askspoke-test-user"></a>Askkol test kullanıcısı oluşturma

Bu bölümde, Askkol içinde B. Simon adlı bir Kullanıcı oluşturulur. Askkol, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı, askın içinde zaten mevcut değilse, Askkol 'a erişmeye çalıştığınızda yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Askkol kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız askın üzerinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Güvenkiyle deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)