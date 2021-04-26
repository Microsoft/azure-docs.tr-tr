---
title: 'Öğretici: Çevhouse ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Çevhouse arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: jeedes
ms.openlocfilehash: 6a14b16e34faa827228594bf6d4f0bd9ed48cf72
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221764"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Öğretici: Çevhouse ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile bir doğa ile tümleşmeyi öğreneceksiniz. Azure AD ile doğa tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Çevhouse 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak yerleşik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Doğa Merkezi çoklu oturum açma (SSO) etkin aboneliği.

> [!NOTE] 
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz. 

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Çevhouse **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-greenhouse-from-the-gallery"></a>Galeriden Çevhouse ekleme

Çevhouse 'un Azure AD ile tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize bir Çevhouse eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna bir **çevhouse** yazın.
1. Sonuçlar panelinden **Çevhouse** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>Doğa için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu, bir Kullanıcı adı ile birlikte yapılandırın ve sınayın. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Çevhouse 'da ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu bir doğa ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. Tek Sign-On ayarlarını uygulama tarafında yapılandırmak için, **[doğa ve SSO 'Yu yapılandırın](#configure-greenhouse-sso)** .
    1. Kullanıcı Azure AD gösterimi ile bağlantılı olan bir Köperon 'da, bir köperon ile ilgili bir en yüksek Kölükli **[test kullanıcısı oluşturun](#create-greenhouse-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Çevhouse** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<COMPANYNAME>.greenhouse.io`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:
    
    | Yanıt URL'si|
    | -------------- |
    | `https://<COMPANYNAME>.greenhouse.io/users/saml/consume` |
    | `https://app.greenhouse.io/<ENTITY ID>/users/saml/consume` |
    |

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<COMPANYNAME>.greenhouse.io`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'si ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için, [Çevhouse istemci destek ekibine](https://www.greenhouse.io/contact) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Çevhouse ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak, Çevhouse 'a erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Çevhouse**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-greenhouse-sso"></a>Doğa bir SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir yönetici olarak bir Çevhouse Web sitesinde oturum açın.

1. **> Geliştirici merkezi > çoklu oturum açma '** ya gidin.

    ![SSO sayfası ekran görüntüsü](./media/greenhouse-tutorial/configure.png)

1. **Çoklu oturum açma** sayfasında aşağıdaki adımları gerçekleştirin.

    ![SSO yapılandırma sayfası için ekran görüntüsü](./media/greenhouse-tutorial/sso-page.png)

    a. **SSO onaylama tüketici URL 'si** değerini kopyalayın, bu değeri Azure Portal **temel SAML yapılandırması** bölümündeki **yanıt URL 'si** metin kutusuna yapıştırın.

    b. **VARLıK kimliği/veren** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    c. **Tek Sign-On URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. İndirilen **Federasyon meta VERI XML** dosyasını Azure Portal Notepad 'e açın ve Içeriği **IDP sertifika parmak izi** metin kutusuna yapıştırın.

    e. Açılan listeden **ad tanımlayıcı biçim** değerini seçin.

    f. **Teste başla**' ya tıklayın.

    >[!NOTE]
    >Alternatif olarak, **Dosya Seç** seçeneğine tıklayarak **Federasyon meta veri XML** dosyasını da karşıya yükleyebilirsiniz.

### <a name="create-greenhouse-test-user&quot;></a>Doğa test kullanıcısı oluşturma

Azure AD kullanıcılarının, Çevhouse 'da oturum açmasını sağlamak için, bunların bir Çevhouse 'a sağlanması gerekir. Büyük bir örnekte, sağlama işlemi el ile gerçekleştirilen bir görevdir.

>[!NOTE]
>Azure AD Kullanıcı hesapları sağlamak için, herhangi bir diğer tüm özel kullanıcı hesabı oluşturma aracını veya çıplak şirket tarafından sunulan API 'Leri kullanabilirsiniz. 

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **Çevhouse** şirket sitenizde yönetici olarak oturum açın.

2. **Yeni kullanıcılar > yapılandırma > kullanıcılara** git
   
    ![Kullanıcılar](./media/greenhouse-tutorial/create-user-1.png &quot;Kullanıcılar")

4. **Yeni Kullanıcı Ekle** bölümünde aşağıdaki adımları uygulayın:
   
    ![Yeni Kullanıcı Ekle](./media/greenhouse-tutorial/create-user-2.png "Yeni Kullanıcı Ekle")

    a. **Kullanıcı e-postalarını girin** metin kutusuna, sağlamak istediğiniz geçerli bir Azure Active Directory hesabının e-posta adresini yazın.

    b. **Kaydet**’e tıklayın.    
   
      >[!NOTE]
      >Azure Active Directory hesap sahipleri, hesabı etkin hale gelmeden önce onaylama bağlantısı içeren bir e-posta alır.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Yeşilhouse oturum açma URL 'sine yeniden yönlendirilir.  

* Doğrudan sağ taraf oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal ' de **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız çevet 'de otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki Çevhouse kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Kstaya otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).



## <a name="next-steps"></a>Sonraki adımlar

Orevyi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).