---
title: 'Öğretici: FAKSLA çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory. ARTı | Microsoft Docs'
description: Azure Active Directory ve Faks arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin. Artı.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 528e1056574379f922b5de15f442b7fd92d8cf8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592464"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-faxplus"></a>Öğretici: FAKSLA çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory. ARTı

Bu öğreticide, FAKSı tümleştirmeyi öğreneceksiniz. Azure Active Directory (Azure AD) ile birlikte. FAKSı tümleştirdiğinizde. Ayrıca Azure AD ile şunları yapabilirsiniz:

* Azure AD 'de FAKSA erişimi olan denetim. Artı.
* Kullanıcılarınızın otomatik olarak FAKSLAMAK için oturum açabilmesi. Ayrıca Azure AD hesaplarıyla birlikte.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Faks. VE çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Faks. Ayrıca **SP ve ıDP** tarafından başlatılan SSO 'yu destekler.
* Faks. Ayrıca **, tam zamanında** Kullanıcı sağlamayı destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-faxplus-from-the-gallery"></a>Faks Ekle. Galeriden ve

Faks tümleştirmesini yapılandırmak için. Ayrıca Azure AD 'ye Faks eklemeniz gerekir. Galerisi 'nden yönetilen SaaS uygulamaları listenize ek olarak.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde **Faks yazın. VE** arama kutusuna.
1. Faks ' ı seçin **. Ve sonuçlar** panelinden uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-faxplus"></a>Faks için Azure AD SSO 'yu yapılandırın ve test edin. ARTı

Azure AD SSO 'yu FAKSLA yapılandırın ve test edin. Ayrıca, **B. Simon** adlı bir test kullanıcısı kullanılarak. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile FAKSLA ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir. Artı.

Azure AD SSO 'yu FAKSLA yapılandırmak ve test etmek için. Ayrıca, aşağıdaki adımları uygulayın:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Faks Yapılandırma. Ayrıca SSO](#configure-faxplus-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. **[Faks oluştur. Ayrıca](#create-faxplus-test-user)** , faksla birlikte B. Simon 'a sahip olmak için kullanıcıyı test edin. Ayrıca, kullanıcının Azure AD gösterimine bağlı.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Faks üzerinde. Ayrıca** , uygulama tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama zaten Azure ile önceden tümleştirildiği için kullanıcının herhangi bir adım yapması gerekmez.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL** 'si metin kutusuna URL 'yi yazın:`https://www.fax.plus/login`

1. **Kaydet**’e tıklayın.

1. Faks. Ayrıca uygulama, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdaki, FAKSA ek olarak. Ayrıca uygulama, aşağıda gösterilen SAML yanıtına birkaç özniteliğin geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name | Kaynak özniteliği|
    | --------------- | --------- |
    | FirstName  | Kullanıcı. |
    | Soyadı   | User. soyadı   |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. Kurulumu yapın **. Ayrıca** bölüm, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Faks erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz. Artı.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde Faks ' ı seçin **. PLUS**.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-faxplus-sso&quot;></a>Faks Yapılandırma. PLUS SSO

1. FAKSTA oturum açın. Ayrıca, yönetici olarak Şirket sitesi.

2. Yönetici profilinizde **güvenlik** bölümüne gidin ve **Gelişmiş**' e kaydırın.

3. **Yapılandırma** panelinde **Çoklu oturum açmayı etkinleştir** düğmesine tıklayın ve aşağıdaki adımları gerçekleştirin.
    
    ![Hesabı](./media/fax.plus-tutorial/configuration.png &quot;Hesap") 

    a. **VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    b. **Tek Sign-On URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. İndirilen **sertifikayı (base64)** Azure Portal Not defteri ' nden açın ve içeriği **X. 509.440 sertifikası** metin kutusuna yapıştırın.

    d. SSO aracılığıyla oturum açmak istiyorsanız **Yönetici Kullanıcı Için yalnızca SSO 'Ya Izin ver** onay kutusunu etkinleştirin. 

    e. **Onayla**'ya tıklayın.

### <a name="create-faxplus-test-user"></a>Faks oluştur. Ayrıca test kullanıcısı

Bu bölümde, Faks 'ta Britta Simon adlı bir Kullanıcı oluşturulur. Artı. Faks. Ayrıca, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten FAKSTA yoksa. Ayrıca, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, FAKSA yönlendirilir. Ayrıca oturum açma akışını başlatabileceğiniz URL 'de oturum açın.  

* Faks sayfasına gidin. Ayrıca oturum açma URL 'sini doğrudan açın ve buradan oturum açma akışını başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve faksa otomatik olarak oturum açmış olmanız gerekir. SSO 'yu ayarladığınız artı. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. FAKSı tıklattığınızda. Ayrıca, uygulamamda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, FAKSA otomatik olarak oturum açmış olmanız gerekir. SSO 'yu ayarladığınız artı. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

FAKSı yapılandırdıktan sonra. Ayrıca, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve infilini koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).