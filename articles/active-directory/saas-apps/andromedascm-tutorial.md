---
title: 'Öğretici: Andromeda ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Andromeda arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 678bb46efb19f3451566306ddbbe372c631c717e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736046"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Öğretici: Andromeda ile tümleştirme Azure Active Directory

Bu öğreticide, Andromeda 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Andromeda Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Andromeda 'e erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Andromeda (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Andromeda ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Andromeda çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Andromeda **SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Andromeda **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-andromeda-from-the-gallery"></a>Galeriden Andromeda ekleme

Andromeda tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Andromeda yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Andromeda** yazın.
1. Sonuçlar panelinden **Andromeda** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Andromeda için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Andromeda ile yapılandırın ve test edin. SSO 'nun çalışması için, Andromeda içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Andromeda ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:


1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Andromeda SSO 'Yu yapılandırın](#configure-andromeda-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Andromeda 'de Britta Simon 'ın bir karşılığı olacak şekilde **[Andromeda test kullanıcısı oluşturun](#create-andromeda-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Andromeda** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantURL>.ngcxpress.com/`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Ekran görüntüsü, U R L 'ye bir Işaret girebileceğiniz ek U R 'Leri ayarlamayı gösterir.](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Bu değerler gerçek değildir. Değeri gerçek tanımlayıcı, yanıt URL 'SI ile ve Öğreticinin ilerleyen kısımlarında açıklanan Sign-On URL 'SI ile güncelleşceksiniz.

6. Andromeda uygulaması, SAML onaylamalarını belirli bir biçimde bekliyor. Bu uygulama için aşağıdaki talepleri yapılandırın. Bu özniteliklerin değerlerini, uygulama tümleştirme sayfasındaki **Kullanıcı öznitelikleri** bölümünden yönetebilirsiniz. **SAML Ile tek Sign-On ayarlama** sayfasında, **Kullanıcı öznitelikleri** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![Ekran görüntüsü,,, Kullanıcı. mda ve emaadresi Kullanıcı. Mail gibi kullanıcı özniteliklerini gösterir.](common/edit-attribute.png)

    > [!Important]
    > Bu ayarları ayarlarken ad alanı tanımlarını temizleyin.

7. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, yukarıdaki görüntüde gösterildiği gibi, **Düzen simgesini** kullanarak talepleri DÜZENLEYIN veya aşağıdaki resimde gösterildiği gibi SAML belirteci özniteliğini yapılandırmak için **yeni talep Ekle** ' yi kullanarak talepleri ekleyin ve aşağıdaki adımları gerçekleştirin: 

    | Name | Kaynak özniteliği|
    | ------ | -----------|
    | rol        | Uygulamaya özel rol |
    | tür        | Uygulama Türü |
    | şirket       | CompanyName |

    > [!NOTE]
    > Andromeda, uygulamaya atanan kullanıcılara roller bekliyor. Kullanıcılara uygun roller atanabilmeleri için lütfen bu rolleri Azure AD 'de ayarlayın. Azure AD 'de rolleri nasıl yapılandıracağınızı anlamak için [buraya](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)bakın.

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![Ekran görüntüsü, yeni talep ekleme ve kaydetme seçenekleri ile Kullanıcı taleplerini gösterir.](common/new-save-attribute.png)

    ![Ekran görüntüsünde, bu adımla ilgili değer girebileceğiniz Kullanıcı taleplerini yönetme gösterilir.](common/new-attribute-details.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik** olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Tamam 'a** tıklayın

    örneğin: **Kaydet**’e tıklayın.

8. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

9. **Andromeda ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Andromeda 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Andromeda**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Rolleri yukarıda açıklanan şekilde ayarlarsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="configure-andromeda-sso"></a>Andromeda SSO 'yu yapılandırma

1. Andromeda şirket sitenizde yönetici olarak oturum açın.

2. Menü çubuğu üst kısmında **yönetici** ' ye tıklayıp **Yönetim**' e gidin.

    ![Andromeda Yöneticisi](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Araç çubuğunun sol tarafında, **arabirimler** bölümünde **SAML yapılandırması**' na tıklayın.

    ![Andromeda SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. **SAML yapılandırması** bölüm sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Andromeda yapılandırması](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. **SAML Ile SSO 'Yu etkinleştir**' i işaretleyin.

    b. **Andromeda bilgileri** bölümünde, **SP kimlik** DEĞERINI kopyalayın ve **temel SAML yapılandırması** bölümünün **tanımlayıcı** metin kutusuna yapıştırın.

    c. **Tüketici URL 'si** değerini kopyalayın ve **temel SAML YAPıLANDıRMASı** bölümünün **yanıt URL** metin kutusuna yapıştırın.

    d. **Oturum açma URL 'si** değerini kopyalayın ve **temel SAML yapılandırması** bölümünün **oturum açma URL 'si** metin kutusuna yapıştırın.

    e. **SAML kimlik sağlayıcısı** bölümünde IDP adınızı yazın.

    f. **Çoklu oturum açma uç noktası** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    örneğin: İndirilen **Base64 kodlamalı sertifikayı** Not defteri 'ndeki Azure Portal açın, **X 509 Certificate** metin kutusuna yapıştırın.
    
    h. Azure AD 'den SSO oturumu açmayı kolaylaştırmak için aşağıdaki öznitelikleri ilgili değerle eşleyin. Oturum açmak için **Kullanıcı kimliği** özniteliği gereklidir. Sağlama, **e-posta**, **Şirket**, **Kullanıcı türü** ve **rol** için gereklidir. Bu bölümde, Azure portal içinde tanımlananlarla bağıntılı öznitelik eşlemesini (ad ve değerler) tanımlayacağız

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. **Kaydet**’e tıklayın.


### <a name="create-andromeda-test-user"></a>Andromeda test kullanıcısı oluştur

Bu bölümde, Andromeda içinde Britta Simon adlı bir Kullanıcı oluşturulur. Andromeda, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Andromeda içinde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur. Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, [Andromeda istemci desteği ekibine](https://www.ngcsoftware.com/support/)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Andromeda oturum açma URL 'sine yeniden yönlendirilir.  

* Doğrudan Andromeda oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Andromeda otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamda Andromeda kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Andromeda için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Andromeda yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
