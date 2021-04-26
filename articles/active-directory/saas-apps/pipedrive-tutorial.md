---
title: 'Öğretici: Pipedrive ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Pipedrive arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 96abe2209d4298f23c47c62fec970430f69cbaf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654434"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Öğretici: Pipedrive ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Pipedrive 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Pipedrive 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Pipedrive 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Pipedrive otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Pipedrive çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Pipedrive **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="add-pipedrive-from-the-gallery"></a>Galeriden Pipedrive ekleme

Pipedrive tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Pipedrive yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Pipedrive** yazın.
1. Sonuçlar panelinden **Pipedrive** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-pipedrive"></a>Pipedrive için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Pipedrive ile yapılandırın ve test edin. SSO 'nun çalışması için, Pipedrive içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Pipedrive ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[PIPEDRIVE SSO 'Yu yapılandırın](#configure-pipedrive-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan Pipedrive 'de B. Simon 'ya karşılık gelen bir **[Pipedrive test kullanıcısı oluşturun](#create-pipedrive-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Pipedrive** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [Pipedrive istemci destek ekibine](mailto:support@pipedrive.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Pipedrive uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Pipedrive uygulaması, yukarıdakine ek olarak, aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği|
    | ------------ | --------- |
    | e-posta | Kullanıcı. Mail |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, sertifika bulun **(base64)** ve sertifikayı indirmek için **Indir** ' i seçin ve ayrıca, **uygulama Federasyon meta verileri URL 'sini** kopyalayıp bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](./media/pipedrive-tutorial/certificate-data.png)

1. **Pipedrive ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Pipedrive 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Pipedrive**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-pipedrive-sso"></a>Pipedrive SSO 'yu yapılandırma

1. Farklı bir tarayıcı penceresinde, yönetici olarak Pipedrive Web sitesinde oturum açın.

1. **Kullanıcı profili** ' ne tıklayın ve **Ayarlar**' ı seçin.

    !["Kullanıcı profili" menüsünden Seçili "ayarları" gösteren ekran görüntüsü.](./media/pipedrive-tutorial/configure-1.png)

1. Aşağı kaydırarak Güvenlik Merkezi ' ne gidin ve **Çoklu oturum açma**' yı seçin.

    !["Güvenlik Merkezi" 'nde seçili "çoklu oturum açma" yı gösteren ekran görüntüsü.](./media/pipedrive-tutorial/configure-2.png)

1. **Pipedrive Için SAML yapılandırması** bölümünde aşağıdaki adımları uygulayın:

    ![Tüm metin kutularının vurgulandığına ilişkin "S A M L Configuration for Pipedrive" bölümünü gösteren ekran görüntüsü.](./media/pipedrive-tutorial/configure-3.png)

    a. **Veren** metin kutusunda, Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın.

    b. **Çoklu oturum açma (SSO) URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **Çoklu oturum kapatma (SLO) URL** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    d. **X. 509.440 sertifikası** metin kutusunda, indirilen **sertifika (Base64)** dosyasını Azure Portal Not defteri ' nden açın ve içeriğini kopyalayıp **x. 509.440 sertifika** metin kutusuna yapıştırın ve değişiklikleri kaydedin.

### <a name="create-pipedrive-test-user"></a>Pipedrive test kullanıcısı oluştur

1. Farklı bir tarayıcı penceresinde, yönetici olarak Pipedrive Web sitesinde oturum açın.

1. Şirket için aşağı kaydırın ve **Kullanıcıları Yönet**' i seçin.

    !["Şirket" menüsünden Seçili "Kullanıcıları Yönet" i gösteren ekran görüntüsü.](./media/pipedrive-tutorial/user-1.png)

1. **Kullanıcı Ekle**' ye tıklayın.
    
    ![Sağ tarafta "Kullanıcı Ekle" düğmesi seçili olan "Kullanıcıları Yönet" sayfasını gösteren ekran görüntüsü.](./media/pipedrive-tutorial/user-2.png)

1. **Kullanıcıları Yönet** bölümünde aşağıdaki adımları uygulayın:

    ![Pipedrive yapılandırması](./media/pipedrive-tutorial/user-3.png)

    a. **E-posta** metin kutusuna, gibi kullanıcının e-posta adresini girin `B.Simon@contoso.com` .

    b. **Ad** metin kutusuna ilk Kullanıcı adını girin.

    c. **Soyadı** metin kutusuna kullanıcının soyadını girin.

    d. **Onayla ve kullanıcıları davet et**' e tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 


Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Pipedrive oturum açma URL 'sine yeniden yönlendirilir.  

* Doğrudan Pipedrive oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Pipedrive otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamda Pipedrive kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Pipedrive için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Pipedrive yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).