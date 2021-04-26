---
title: 'Öğretici: çözümleme GmbH ile Confluence için SAML SSO ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Çözümleme GmbH ile Confluence için Azure Active Directory ve SAML SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9290272920bbb20144f4e0d957ba2d9ce60d06a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651289"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Öğretici: çözümleme GmbH ile Confluence için SAML SSO ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile çözümleme GmbH tarafından Confluence için SAML SSO 'yu nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile çözümleme GmbH ile Confluence için SAML SSO 'yu tümleştirdiğinizde şunları yapabilirsiniz:

* Çözümleme GmbH tarafından Confluence için SAML SSO 'ya erişimi olan Azure AD 'de denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla çözüm GmbH tarafından Confluence için SAML SSO 'ya otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çözümleme GmbH çoklu oturum açma (SSO) özellikli abonelik tarafından Confluence için SAML SSO 'SU.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Çözümleme GmbH tarafından Confluence için SAML SSO **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Galeriden çözüm GmbH tarafından Confluence için SAML SSO 'SU ekleyin

SAML SSO 'yu çözüm GmbH tarafından Azure AD 'ye tümleştirme için yapılandırmak üzere, Galeri 'den yönetilen SaaS uygulamaları listenize çözüm GmbH tarafından Confluence için SAML SSO 'SU eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **çözüm GmbH için SAML SSO** yazın.
1. Sonuçlar panelinden **çözüm GmbH tarafından Confluence Için SAML SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Çözümleme GmbH tarafından Confluence için SAML SSO 'SU için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak, çözümleme GmbH tarafından çözümlenme için SAML SSO 'Su Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, çözümleme GmbH ile bir Azure AD kullanıcısı ve SAML SSO 'daki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Çözümleme GmbH tarafından Confluence için SAML SSO 'SU ile Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, **[Çözümleme GmbH SSO Ile Confluence IÇIN SAML SSO 'Yu yapılandırın](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** .
    1. **[Çözümleme GmbH test kullanıcısı tarafından Confluence için SAML SSO 'Su oluşturun](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** ; bu, kullanıcının Azure AD gösterimine bağlı olan çözüm GmbH tarafından Confluence için SAML SSO 'su Ile Ilgili Britta Simon 'a sahip olmalıdır.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Çözümleme Için SAML SSO 'su çözüm GmbH** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/samlsso`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/samlsso`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [Çözümleme GmbH istemci desteği ekibine yönelik SAML SSO 'su ile](https://www.resolution.de/go/support) iletişim kurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)


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

Bu bölümde, çözüm GmbH tarafından Confluence için SAML SSO 'SU erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Resolution GmbH tarafından Confluence Için SAML SSO**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>Resolution GmbH SSO 'SU ile Confluence için SAML SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak **çözüm GmbH Yönetici portalı Ile Confluence Için SAML SSO** 'ınızla oturum açın.

2. Dişli üzerine gelin ve **eklentilere** tıklayın.
    
    ![Seçili "COG" simgesini ve açılan listeden "Eklentiler" i gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. Yönetici erişimi sayfasına yönlendirilirsiniz. Parolayı girin ve **Onayla** düğmesine tıklayın.

    !["Onayla" düğmesinin seçili olduğu "yönetici erişimi" sayfasını gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. **Atlasme marketi** sekmesinde **yeni eklentiler bul**' a tıklayın. 

    !["Yeni eklentiler bul" seçiliyken "Attlassian Market" sekmesini gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on.png)

5. **Confluence Için SAML çoklu oturum açma (SSO)** araması yapın ve yeni SAML eklentisini yüklemek için **Install** düğmesine tıklayın.

    ![Arama kutusunda ve "Install" düğmesinin seçili olduğu "yeni eklentiler bul" sayfasını "Confluence için bir M L Single Sign on (S S O) ile gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. Eklenti yüklemesi başlar. **Kapat**’a tıklayın.

    !["Yükleme" iletişim kutusunu gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-8.png)

    !["Yüklü ve hazırlanmaya başlamaya!" gösteren ekran görüntüsü "Kapat" eylemi seçili iletişim kutusu.](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  **Yönet**'e tıklayın.

    !["Yönet" düğmesi seçili olarak "Confluence için M L tek oturum açma (S O) uygulama sayfası" gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Yeni eklentiyi yapılandırmak için **Yapılandır** ' a tıklayın.

    !["Yapılandır" düğmesinin seçili olduğu "Yönet" sayfasını gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Bu yeni eklenti Ayrıca, **kullanıcılar & güvenlik** sekmesi altında bulunabilir.

    !["Kullanıcılar & güvenliği" sekmesini "S A M L SingleSignOn" seçiliyken gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. **SAML SingleSignOn eklenti yapılandırması** sayfasında, kimlik sağlayıcısının ayarlarını yapılandırmak Için **Yeni IDP Ekle** düğmesine tıklayın.

    !["Yeni ı d P ekle" düğmesi seçiliyken "S A M L SingleSignOn eklenti yapılandırması" sayfasını gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. **SAML kimlik sağlayıcınızı seçin** sayfasında aşağıdaki adımları uygulayın:

    !["I d P türü", "ad" ve "Açıklama" metin kutularından oluşan "S A M L-u kimlik sağlayıcısını seçin" sayfasını gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. **Azure AD** 'Yi IDP türü olarak ayarlayın.
    
    b. Kimlik sağlayıcısının **adını** ekleyin (ör. Azure AD).
    
    c. Kimlik sağlayıcısının **açıklamasını** ekleyin (ör. Azure AD).
    
    d. **İleri**’ye tıklayın.
    
12. **Kimlik sağlayıcısı yapılandırma** sayfasında **İleri** düğmesine tıklayın.

    !["Ileri" düğmesi seçili "kimlik sağlayıcısı yapılandırması" sayfasını gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. **SAML IDP meta verilerini Içeri aktar** sayfasında, aşağıdaki adımları uygulayın:

    !["Içeri aktarma", "dosya yükleme" ve "Ileri" düğmelerinin seçili olduğu "a M L L L P meta verilerini Içeri aktarma" sayfasını gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. **Dosya Yükle** düğmesine tıklayın ve 5. adımda Indirdiğiniz meta veri xml dosyasını seçin.

    b. **Al** düğmesine tıklayın.
    
    c. İçeri aktarma başarılı olana kadar kısa bir süre bekleyin.
    
    d. **İleri** düğmesine tıklayın.
    
14. **Kullanıcı kimliği özniteliği ve dönüştürme** sayfasında, **İleri** düğmesine tıklayın.

    !["Ileri" düğmesi seçili "Kullanıcı KIMLIĞI özniteliği ve dönüştürme" sayfasını gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. **Kullanıcı oluşturma ve güncelleştirme** sayfasında, Ayarları Kaydet ' in **yanındaki & kaydet** ' e tıklayın.   
    
    !["Kullanıcı oluşturma ve güncelleştirme" sayfasını "& sonrakini Kaydet" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. **Ayarlarınızı test** etme sayfasında, şimdi için Kullanıcı testini atlamak üzere **testi atla & el ile yapılandır** ' ı tıklatın. Bu, sonraki bölümde gerçekleştirilecek ve Azure portal bazı ayarlar gerektirir. 
    
    !["Test & el ile yapılandırmayı atla" düğmesinin seçili olduğu "ayarlarınızı test etme" sayfasını gösteren ekran görüntüsü.](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. Görüntülenen iletişim kutusunda okuma **, test anlamına geliyor...**, **Tamam**' a tıklayın.
    
    ![Tek Sign-On yapılandırma](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Çözüm GmbH test kullanıcısı tarafından Confluence için SAML SSO oluşturma

Azure AD kullanıcılarının çözüm GmbH tarafından Confluence için SAML SSO 'da oturum açmasını sağlamak için, çözüm GmbH tarafından Confluence için SAML SSO 'ya sağlanması gerekir.  
Çözüm GmbH tarafından Confluence için SAML SSO 'da, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Çözümleme GmbH Şirket sitesini yönetici olarak çözümlemek için SAML SSO 'sitenizde oturum açın.

2. Dişli 'ye gelin ve **Kullanıcı yönetimine** tıklayın.

    !["COG" simgesinin seçili olduğunu gösteren ekran görüntüsü ve menüden "Kullanıcı Yönetimi" seçilidir.](./media/saml-sso-confluence-tutorial/user-1.png) 

3. Kullanıcılar bölümünde, **Kullanıcı Ekle** sekmesini tıklatın. **"Kullanıcı Ekle"** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Çalışan Ekle](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. Kullanıcı **adı** metin kutusuna, Britta Simon gibi kullanıcının e-postasını yazın.

    b. **Tam ad** metin kutusuna, Britta Simon gibi kullanıcının tam adını yazın.

    c. **E-posta** metin kutusuna, gibi kullanıcının e-posta adresini yazın Brittasimon@contoso.com .

    d. **Parola** metin kutusuna Britta Simon parolasını yazın.

    e. Parolayı **Onayla** ' ya tıklayarak parolayı yeniden girin.
    
    f. **Ekle** düğmesine tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu işlem, oturum akışını başlatabileceğiniz çözüm GmbH oturum açma URL 'SI için SAML SSO 'ya yeniden yönlendirilir.  

* Çözüm GmbH oturum açma URL 'sine doğrudan yönelik SAML SSO 'ya gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'yu ayarladığınız çözüm GmbH tarafından Confluence IÇIN SAML SSO 'ya otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım için SAML SSO 'SU çözüm GmbH kutucuğunda tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız çözüm GmbH tarafından yapılandırma için SAML SSO 'ya otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Çözümleme GmbH tarafından yapılandırma için SAML SSO 'yu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).