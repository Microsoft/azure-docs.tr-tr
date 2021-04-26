---
title: 'Öğretici: MobileIron ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve MobileIron arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: c47092b1488a79805db69308bcb9a8efde1c0d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653082"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Öğretici: MobileIron ile tümleştirme Azure Active Directory

 Bu öğreticide, MobileIron 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. MobileIron 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, MobileIron erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak MobileIron 'e kaydolmalarına olanak tanır.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* MobileIron çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* MobileIron **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-mobileiron-from-the-gallery"></a>Galeriden MobileIron ekleme

MobileIron 'in tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize MobileIron eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol bölmede **Azure Active Directory**' yi seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **mobileiron** yazın.
1. Sonuçlardan **MobileIron** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>MobileIron için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu MobileIron ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve MobileIron içindeki ilgili Kullanıcı arasında bağlı bir ilişki kurmanız gerekir.

Azure AD SSO 'yu MobileIron ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
     1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[MobileIron SSO 'Yu yapılandırın](#configure-mobileiron-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan MobileIron 'da Britta Simon 'a sahip olmak için **[mobileiron test kullanıcısı oluşturun](#create-mobileiron-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Bu bölümde, Azure portal Azure AD SSO 'yu etkinleştirirsiniz.
 
1. Azure portal, **MobileIron** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Tek bir Sign-On yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.MobileIron.com/<key>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

     **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve Sign-On URL 'siyle güncelleştirin. Anahtar ve ana bilgisayar değerlerini, öğreticide daha sonra açıklanan MobileIron yönetim portalından alacaksınız.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure Portal sol bölmeden, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu işaretleyin ve parolayı aşağı yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, MobileIron 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirin.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **MobileIron**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı ekle**'yi seçin. Sonra **atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, Kullanıcı listesinden **B. Simon** öğesini seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-mobileiron-sso"></a>MobileIron SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, MobileIron şirket sitenizde yönetici olarak oturum açın.

2. **Yönetici**  >  **kimliği** ' ne gidin ve **Cloud IDP kurulumu alanındaki bilgiler** ' de **AAD** seçeneğini belirleyin.

    ![Ekran görüntüsü, seçilen kimlik ile MobileIron sitesinin Yönetim sekmesini gösterir.](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. **Anahtar** ve **ana bilgisayar** değerlerini kopyalayın ve Azure Portal içindeki **temel SAML yapılandırması** bölümünde bulunan URL 'leri doldurmak için bunları yapıştırın.

    ![Ekran görüntüsü, bir anahtar ve ana bilgisayar değeri ile SAML ayarlama seçeneğini gösterir.](./media/MobileIron-tutorial/key.png)

4. **AAD 'den meta veri dosyasını dışarı aktar ve MobileIron bulutuna aktar alanına** indirilen meta verileri Azure Portal karşıya yüklemek Için **Dosya Seç** ' e tıklayın. Karşıya yüklendikten sonra **bitti** ' ye tıklayın.

    ![Tek Sign-On yönetici meta verilerini Yapılandır düğmesi](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>MobileIron test kullanıcısı oluşturma

Azure AD kullanıcılarının, MobileIron 'de oturum açmasını sağlamak için bunların MobileIron 'e sağlanması gerekir.  
MobileIron söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. MobileIron şirket sitenizde yönetici olarak oturum açın.

1. **Kullanıcılar** ' a gidin ve   >  **Tek Kullanıcı** Ekle ' ye tıklayın.

    ![Tek Sign-On Kullanıcı düğmesini yapılandırma](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. **"Tek kullanıcılı"** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Tek Sign-On Kullanıcı ekleme düğmesini yapılandırma](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. **E-posta adresi** metin kutusuna kullanıcının e-postasını girin brittasimon@contoso.com .

    b. **Ad** metin kutusuna, ilk Kullanıcı adını Britta gibi girin.

    c. **Soyadı** metin kutusuna, Simon gibi kullanıcı adının soyadını girin.

    d. **Bitti**’ye tıklayın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz MobileIron oturum açma URL 'sine yeniden yönlendirilir.  

* Doğrudan MobileIron oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız MobileIron 'te otomatik olarak oturum açmış olmanız gerekir.

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içinde MobileIron kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız MobileIron 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

MobileIron 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimlerini zorunlu kılabilirsiniz. Oturum denetimleri koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
