---
title: 'Öğretici: ufuk dokunma için çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Ufuk ve çoklu oturum açma Azure Active Directory arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.openlocfilehash: 5577b0cb480b372b64a9cb25f52617edfca3f2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98108203"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Öğretici: ufuk dokunma için çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile ufuk dokunma için çoklu oturum açmayı nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile ufuk için çoklu oturum açmayı tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, ufuk dokunma için çoklu oturum açma erişimine sahip olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla ufuk için çoklu oturum açmaya otomatik olarak oturum açmasını etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin, Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Ufuk için çoklu oturum açma (SSO) etkin abonelik için çoklu oturum açma.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Ufuk dokunma için çoklu oturum açma SP ve ıDP tarafından başlatılan SSO 'yu destekler.
* Ufuk tap için çoklu oturum açmayı yapılandırdıktan sonra, oturum denetimini zorunlu kılabilirsiniz. Bu, kuruluşunuzun hassas verilerinin, gerçek zamanlı olarak ayıklanma ve içe taşmayı korur. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Galerinin ufuk düğmesine yönelik çoklu oturum açma ekleme

Azure AD 'ye ufuk için çoklu oturum açma tümleştirmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize ufuk 'e dokunmada çoklu oturum açma eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ufuk Için çoklu oturum açma** yazın.
1. Sonuçlar panelinden **ufuk Için çoklu oturum açma** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Azure AD çoklu oturum açmayı, tek oturum açma için, ufuk dokunma için yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak, ufuk Için çoklu oturum açma Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında ufuk dokunma için çoklu oturum açma içinde bağlantılı bir ilişki kurun.

Azure AD SSO 'yu, ufuk dokunma için çoklu oturum açma ile yapılandırmak ve test etmek için genel adımlar şunlardır:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .

    a. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .

    b. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[ufuk dokunma SSO 'su Için çoklu oturum açmayı yapılandırın](#configure-single-sign-on-for-skytap-sso)** .

    a. Ufuk için bir çoklu oturum açma için, ufuk dokunma için çoklu oturum açma konusunda B. Simon 'a bir karşılık sahip olacak şekilde **[tek bir oturum açma oluşturun](#create-single-sign-on-for-skytap-test-user)** . Bu karşılık gelen bu, kullanıcının Azure AD gösterimine bağlıdır.
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **ufuk Için çoklu oturum açma** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun. **Çoklu oturum açma** seçeneğini belirleyin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Kalem simgesi vurgulanmış şekilde, SAML ile çoklu oturum açmayı ayarlama ekran görüntüsü](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:`http://pingone.com/<custom EntityID>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:`https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. İsteğe bağlı olarak **ek URL 'Ler ayarla**' yı seçebilir ve uygulamayı **SP** tarafından başlatılan modda yapılandırmak için aşağıdaki adımları uygulayabilirsiniz:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanan bir URL yazın:`https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. **Geçiş durumu** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:`https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI, oturum açma URL 'SI ve geçiş durumu ile güncelleştirin. Bu değerleri almak için, [ufuk, istemci desteği ekibine dokunarak çoklu oturum açma ile](mailto:support@skytap.com) iletişim kurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML**'i bulun. Meta veri dosyasını indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısının ekran görüntüsü](common/metadataxml.png)

1. **Ufuk Için çoklu oturum açmayı ayarla** bölümünde, gereksiniminize göre uygun URL 'Yi veya URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerinin ekran görüntüsü](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure Portal sol bölmeden, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** alanında gösterilen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, ufuk dokunma için çoklu oturum açma erişimine izin vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirin.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **ufuk dokunma Için çoklu oturum açma** seçeneğini belirleyin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   ![Kullanıcılar ve gruplar vurgulanmış şekilde Yönet bölümünün ekran görüntüsü](common/users-groups-blade.png)

1. **Kullanıcı ekle**'yi seçin. **Atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.

    ![Kullanıcılar ve gruplar sayfasının ekran görüntüsü, Kullanıcı Ekle vurgulanmış olarak](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** öğesini seçin. Ardından ekranın alt kısmındaki **Seç** düğmesini seçin.
1. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ardından ekranın alt kısmındaki **Seç** düğmesini seçin.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Ufuk dokunma SSO için çoklu oturum açmayı yapılandırma

Tek oturum açma 'yı ufuk dokunma tarafında çoklu oturum açma 'yı yapılandırmak için, indirilen **Federasyon meta VERI XML**'sini ve uygun kopyalanmış url 'Leri Azure Portal [ufuk dokunma istemci desteği ekibi Için çoklu oturum açma](mailto:support@skytap.com)'ya göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak için bu ayarı yapılandırır.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Ufuk tap test kullanıcısına çoklu oturum açma oluştur

Bu bölümde, ufuk dokunma için çoklu oturum açma bölümünde B. Simon adlı bir Kullanıcı oluşturacaksınız. Ufuk dokunma platformu için çoklu oturum açma içindeki kullanıcıları eklemek üzere, [ufuk Için çoklu oturum açma](mailto:support@skytap.com) ile çalışın. Kullanıcıları oluşturup etkinleştirene kadar çoklu oturum açma kullanamazsınız.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim paneli ' ni kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Access panel 'de ufuk dokunma kutucuğunun çoklu oturum açma bölmesini seçtiğinizde, SSO 'yu ayarladığınız ufuk dokunuz için çoklu oturum açma için otomatik olarak oturum açmış olmanız gerekir. Daha fazla bilgi için bkz. [erişim paneli 'Ne giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile bolluk 'i deneyin](https://aad.portal.azure.com/)
