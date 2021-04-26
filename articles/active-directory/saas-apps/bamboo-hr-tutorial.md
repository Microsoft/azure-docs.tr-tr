---
title: 'Öğretici: BambooHR ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve BambooHR arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 3926e98cb55d1afd80caf4af5d67910b66c09cdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180350"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Öğretici: BambooHR ile tümleştirme Azure Active Directory

Bu öğreticide, BambooHR 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. BambooHR 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de BambooHR erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla BambooHR ' de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* BambooHR çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* BambooHR **SP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.


## <a name="adding-bamboohr-from-the-gallery"></a>Galeriden BambooHR ekleme

BambooHR 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize BambooHR eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **BambooHR** yazın.
1. Sonuçlar panelinden **BambooHR** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>BambooHR için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak BambooHR Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, BambooHR içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu BambooHR ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[BambooHR SSO 'Yu yapılandırın](#configure-bamboohr-sso)** .
    * **[BambooHR test kullanıcısı oluşturun](#create-bamboohr-test-user)** -kullanıcının Azure AD gösterimine bağlı olan BambooHR 'Da Britta Simon 'a sahip olmak için.
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **BambooHR** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company>.bamboohr.com`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    | Yanıt URL'si |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI ve yanıt URL 'SI ile güncelleştirin. Değerleri almak için [BambooHR istemci destek ekibine](https://www.bamboohr.com/contact.php) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **BambooHR ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, BambooHR erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **BambooHR**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-bamboohr-sso"></a>BambooHR SSO 'yu yapılandırma

1. Yeni bir pencerede, BambooHR şirket sitenizde yönetici olarak oturum açın.

2. Giriş sayfasında, aşağıdakileri yapın:
   
    ![BambooHR Single Sign-On sayfası](./media/bamboo-hr-tutorial/ic796691.png "Çoklu Oturum Açma")   

    a. **Uygulamalar**' ı seçin.
   
    b. **Uygulamalar** bölmesinde, **Çoklu oturum açma**' yı seçin.
   
    c. **SAML çoklu oturum açma** seçeneğini belirleyin.

3. **SAML çoklu oturum açma** bölmesinde şunları yapın:
   
    ![SAML tek Sign-On bölmesi](./media/bamboo-hr-tutorial/IC796692.png "SAML tek Sign-On")
   
    a. **SSO oturum açma URL 'si** kutusuna, 6. adımdaki Azure Portal kopyaladığınız **oturum açma URL 'sini** yapıştırın.
      
    b. Not defteri 'nde, Azure portal indirdiğiniz Base-64 kodlu sertifikayı açın, içeriğini kopyalayın ve **X. 509.440 sertifika** kutusuna yapıştırın.
   
    c. **Kaydet**’i seçin.

### <a name="create-bamboohr-test-user"></a>BambooHR test kullanıcısı oluştur

Azure AD kullanıcılarının BambooHR 'da oturum açmasını etkinleştirmek için aşağıdakileri yaparak BambooHR içinde el ile ayarlayın:

1. **BambooHR** sitenizde yönetici olarak oturum açın.

2. Üstteki araç çubuğunda **Ayarlar**' ı seçin.
   
    ![Ayarlar düğmesi](./media/bamboo-hr-tutorial/IC796694.png "Ayar")

3. **Genel bakış**'ı seçin.

4. Sol bölmede **güvenlik**  >  **kullanıcıları**' nı seçin.

5. Ayarlamak istediğiniz geçerli Azure AD hesabının kullanıcı adını, parolasını ve e-posta adresini yazın.

6. **Kaydet**’i seçin.
        
>[!NOTE]
>Azure AD Kullanıcı hesaplarını ayarlamak için BambooHR Kullanıcı hesabı oluşturma araçları veya API 'Leri de kullanabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

1. Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz BambooHR oturum açma URL 'sine yeniden yönlendirilir. 

2. BambooHR oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

3. Microsoft Access panel ' i kullanabilirsiniz. Erişim panelinde BambooHR kutucuğuna tıkladığınızda, bu, BambooHR oturum açma URL 'sine yeniden yönlendirilir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

BambooHR 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).