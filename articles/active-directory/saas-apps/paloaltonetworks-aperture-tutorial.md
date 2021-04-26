---
title: 'Öğretici: Palo Alto Networks-diyafram ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Palo Alto ağları açıklığı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 42a6bc9bfb06f1c80b719bdda686ae111a8884ab
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222033"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Öğretici: Palo Alto Networks-diyafram ile tümleştirme Azure Active Directory

Bu öğreticide, Palo Alto 'nın Azure Active Directory (Azure AD) ile ağlarla nasıl tümleştirileceğini öğreneceksiniz. Palo Alto ağlarını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Palo Alto Networks-diyafram erişimine sahip olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla ağları açıklık altına almak için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Palo Alto ağları-açıklık çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Palo Alto Networks-açıklık, **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-palo-alto-networks---aperture-from-the-gallery"></a>Galeriden Palo Alto Networks-açıklık ekleme

Palo Alto 'nın ağlar açıklığı ile Azure AD arasındaki tümleştirmeyi yapılandırmak için, galerideki Palo Alto Networks-diyafram ' i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Palo Alto Networks-açıklık** yazın.
1. Sonuçlar panelinden **Palo Alto Networks-diyafram** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma ve test etme

Bu bölümde, **B. Simon** adlı bir test kullanıcısına göre Palo Alto Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Palo Alto Networks-açıklık içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı, Palo Alto Networks-diyafram ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, **[Palo Alto Networks-DIYAFRAM SSO](#configure-palo-alto-networks---aperture-sso)** -öğesini yapılandırın.
    1. **[Palo Alto Networks-bir test kullanıcısına](#create-palo-alto-networks---aperture-test-user)** , Palo Alto 'Da kullanıcının Azure AD gösterimine bağlı olan ağların açıklık Ile Ilgili Britta Simon 'un bir karşılığı olacak şekilde.
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Palo Alto Networks-diyafram** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [Palo Alto Networks-açıklık istemci destek ekibine](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **Palo Alto Networks-diyafram ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Palo Alto Networks-diyafram erişimine izin vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Palo Alto Networks-diyafram**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-palo-alto-networks---aperture-sso"></a>Palo Alto Networks-diyafram SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Palo Alto ağları bir yönetici olarak da oturum açın.

2. Üstteki menü çubuğunda, **Ayarlar**' a tıklayın.

    ![Ayarlar sekmesi](./media/paloaltonetworks-aperture-tutorial/settings.png)

3. **Uygulama** Bölümü ' ne gidin, menünün sol tarafındaki **kimlik doğrulama** formu ' na tıklayın.

    ![Auth sekmesi](./media/paloaltonetworks-aperture-tutorial/authentication.png)
    
4. **Kimlik doğrulama** sayfasında, aşağıdaki adımları uygulayın:
    
    ![Kimlik doğrulama sekmesi](./media/paloaltonetworks-aperture-tutorial/tab.png)

    a. Çoklu oturum **açmayı etkinleştir (desteklenen SSP sağlayıcıları okta, bir oturum açma)** Ile **Çoklu oturum** açma alanını denetleyin.

    b. **Kimlik sağlayıcısı kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    c. **Kimlik sağlayıcısı sertifikası** alanında, Azure AD 'Den indirilen sertifikayı karşıya yüklemek Için **Dosya Seç** ' e tıklayın.

    d. **Kimlik sağlayıcısı SSO URL** metin kutusuna, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    e. **Açıklık bilgisi** bölümünde IDP bilgilerini gözden geçirin ve sertifikayı **açıklık anahtarı** alanından indirin.

    f. **Kaydet**’e tıklayın.


### <a name="create-palo-alto-networks---aperture-test-user"></a>Palo Alto Networks-açıklık test kullanıcısı oluşturma

Bu bölümde, Palo Alto Networks-diyafram ' de Britta Simon adlı bir Kullanıcı oluşturacaksınız. Palo [Alto Networks-](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) açıklık platformu içindeki kullanıcıları eklemek Için avuçiçi istemci destek ekibi ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu işlem, oturum akışını başlatabileceğiniz her yerde Palo Alto Networks-açıklık oturum açma URL 'sine yönlendirecektir.  

* Palo Alto ağları-açıklık oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Palo Alto ağları için otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki Palo Alto ağları-açıklık kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Palo Alto ağları için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Palo Alto Networks-diyafram ' i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).