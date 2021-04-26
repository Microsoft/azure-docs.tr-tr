---
title: 'Öğretici: TickitLMS ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve TickitLMS öğrenimi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: b37a5332e7c8f21d4309102b6b111953c1765ea6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729387"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tickitlms-learn"></a>Öğretici: TickitLMS ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, TickitLMS hakkında Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. TickitLMS 'yi tümleştirdiğinizde Azure AD ile ilgili bilgi edinmek için şunları yapabilirsiniz:

* Azure AD 'de TickitLMS hakkında erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla ilgili olarak, TickitLMS için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* TickitLMS çoklu oturum açma (SSO) etkin aboneliğini öğrenin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* TickitLMS **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-tickitlms-learn-from-the-gallery"></a>TickitLMS ekleme galerisinden bilgi edinin

TickitLMS 'nin tümleştirmesini Azure AD 'ye yönelik olarak yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize bilgi edinmek için TickitLMS eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Tickitlms öğren** yazın.
1. Sonuçlar panelinden **Tickitlms öğrenin** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-tickitlms-learn"></a>TickitLMS için Azure AD SSO 'yu yapılandırın ve test edin

Azure AD SSO 'yu TickitLMS ile yapılandırın ve test edin **B. Simon** adlı bir test kullanıcısı kullanmayı öğrenin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve TickitLMS hakkında bilgi edinmek için ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu TickitLMS öğrenimiyle yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[TickitLMS 'Yi Yapılandır](#configure-tickitlms-learn-sso)** -uygulama tarafında çoklu oturum açma ayarlarını YAPıLANDıRMAK için SSO 'yu öğrenin.
    1. **[Tickitlms oluşturma test kullanıcısını öğrenin](#create-tickitlms-learn-test-user)** -Tickitlms 'de B. Simon 'un bir karşılığı olan kullanıcının Azure AD gösterimine bağlı olduğunu öğrenin.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal **Tickitlms** Uygulama tümleştirmesini öğrenin sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama zaten Azure ile önceden tümleştirildiği için kullanıcının herhangi bir adım yapması gerekmez.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL** 'si metin kutusuna URL 'yi yazın:`https:/learn.tickitlms.com/sso/login`

1. **Kaydet**’e tıklayın.

1. TickitLMS, uygulamanın SAML onaylamalarını belirli bir biçimde bekletir, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdaki, TickitLMS hakkında daha fazla özniteliğe daha fazla sayıda özniteliğin aşağıda gösterilen SAML yanıtına geri geçirilmesini bekliyor. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name | Kaynak özniteliği|
    | ---- | --------------- |
    | samlaccount | User. samlaccount |
    | çalışan | User. EmployeeID |
    | rol | User. Role |
    | bölüm | User. Departmanı |
    | raporlar | User. ReportsTo |

    > [!NOTE]
    > TickitLMS, uygulamaya atanan kullanıcılara roller bekliyor. Kullanıcılara uygun roller atanabilmeleri için lütfen bu rolleri Azure AD 'de ayarlayın. Azure AD 'de rolleri nasıl yapılandıracağınızı anlamak için [buraya](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)bakın.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)
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

Bu bölümde, TickitLMS öğrenimi için erişim izni vererek B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Tickitlms öğreni**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Rolleri yukarıda açıklanan şekilde ayarlarsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-tickitlms-learn-sso"></a>TickitLMS 'yi yapılandırma SSO 'yu öğrenin

**Tickitlms 'Yi öğrenme** tarafında çoklu oturum açmayı yapılandırmak Için, **uygulama Federasyon meta veri URL 'Sini** [tickitlms ' ye bilgi destek ekibi](mailto:support@tickitlms.com)' ne göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-tickitlms-learn-test-user"></a>TickitLMS oluşturma test kullanıcısını öğrenme

Bu bölümde, TickitLMS hakkında bilgi edinmek için Britta Simon adlı bir Kullanıcı oluşturacaksınız. Tickitlms ile çalışma, TickitLMS 'de kullanıcıları eklemek için [Destek ekibine bilgi edinin](mailto:support@tickitlms.com) . Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, TickitLMS 'ye yönlendirilir ve oturum açma akışını başlatabileceğiniz oturum açma URL 'sini öğrenin.  

* TickitLMS 'ye gidin doğrudan oturum açma URL 'sini öğrenin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve ardından, SSO 'Yu ayarladığınız TickitLMS hakkında bilgi edinmek için otomatik olarak oturum açmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım bölümünde TickitLMS öğrenimi ' ne tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Cekkitlms öğrenimi için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

TickitLMS 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
