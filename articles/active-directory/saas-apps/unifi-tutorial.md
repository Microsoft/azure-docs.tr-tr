---
title: 'Öğretici: sınıfı olmayan Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve sınıfı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 1fa1c40c96694b71bc762050998257102b687c18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650648"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Öğretici: sınıfı olmayan tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile bir sınıfı tümleştirmeyi nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile bir sınıfı tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, sınıfı olmayan bir denetime erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla ilgili bir sınıfı etkinleştirmek için otomatik olarak oturum açma olanağı sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) etkin aboneliğini devre dışı bırak.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* SıNıFı **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.
* Sınıfı kaldırma **Otomatik** Kullanıcı sağlamayı destekler.

## <a name="add-unifi-from-the-gallery"></a>Galeriden sınıfı ekleme

Örneği KALDıRMA ile Azure AD arasındaki tümleştirmeyi yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize bir sınıfı eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **sınıfı kaldır** ' ı yazın.
1. Sonuçlar panelinden **sınıfı kaldır** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-unifi"></a>Azure AD SSO 'yu sınıfı için yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu oluşturma ve test etme. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve bu kullanıcı ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[sınıfı olmayan SSO 'Yu yapılandırın](#configure-unifi-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan, örneği oluşturma bölümünde B. Simon 'ın bir karşılığı olacak şekilde, bir **[sınıfı olmayan test kullanıcısı oluşturun](#create-unifi-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **sınıfı olmayan** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    **Tanımlayıcı** metin kutusuna URL 'yi yazın:`INVIEWlabs`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL** 'si metin kutusuna URL 'yi yazın:`https://app.discoverunifi.com/login`

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **Sınıfı ayarlamayı ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Azure 'un tek başına oturum açma özelliğini kullanarak, sınıfı olmayan bir erişim izni vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **sınıfı kaldır**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-unifi-sso"></a>Sınıfı olmayan SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, **sınıfı olmayan** şirket sitenizde yönetici olarak oturum açın.

2. **Kullanıcılara** tıklayın.

    ![Ekran görüntüsü, sınıfı olmayan siteden seçilen kullanıcıları gösterir.](./media/unifi-tutorial/app-1.png)

3. **Yeni kimlik sağlayıcısı ekle**' ye tıklayın.

    ![Ekran görüntüsü, ad yeni kimlik sağlayıcısını seçili olarak gösterir.](./media/unifi-tutorial/app-2.png)

4. **Kimlik sağlayıcısı ekle** bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz kimlik sağlayıcısı ekle ' nin gösterir.](./media/unifi-tutorial/app-3.png) 

    a. **Sağlayıcı adı** metin kutusuna kimlik sağlayıcısının adını yazın.

    b. **Sağlayıcı URL** metin kutusuna, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. Not defteri 'ndeki Azure portal indirdiğiniz sertifikayı açın, **---sertifikayı başlat---** ve **---son sertifika---** etiketini kaldırın ve ardından geri kalan içeriği **sertifika** metin kutusuna yapıştırın.

    d. **Varsayılan sağlayıcı** onay kutusunu seçin.

### <a name="create-unifi-test-user"></a>Sınıfı olmayan test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı bir Kullanıcı oluşturacaksınız. , El ile yapılan bir adım gerekmeden otomatik Kullanıcı **sağlamayı destekler.** Kullanıcılar, Azure AD 'den başarılı bir şekilde kimlik doğrulamasından sonra otomatik olarak oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz sınıfı olmayan oturum açma URL 'sine yeniden yönlendirilir.  

* Sınıfı olmayan oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal ' de **Bu uygulamayı test et** ' e tıklayın ve SSO 'YU ayarladığınız sınıfı için otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamda yerleşik olmayan Kutucuğa tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız sınıfı oluşturmak için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

SıNıFı oluşturmayı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve GERI alımını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).