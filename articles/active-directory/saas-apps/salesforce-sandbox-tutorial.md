---
title: 'Öğretici: Salesforce korumalı alanı ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Salesforce korumalı alanı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: c82b00b1f107dc147cbef6a0ca406f2054321216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734890"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Öğretici: Salesforce korumalı alanı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Salesforce korumalı alanını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Salesforce korumalı alanını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Salesforce korumalı alana erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Salesforce korumalı alanı ' na otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Salesforce korumalı alanı çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Salesforce korumalı alanı **SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Salesforce korumalı alanı **, tam zamanında** Kullanıcı sağlamasını destekler
* Salesforce korumalı alanı [ **Otomatik** Kullanıcı sağlamayı destekler](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Galeriden Salesforce korumalı alanı ekleme

Salesforce korumalı alanının Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden Salesforce korumalı alanını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Salesforce korumalı alanı** yazın.
1. Sonuçlar panelinden **Salesforce korumalı alanı** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-salesforce-sandbox"></a>Salesforce korumalı alanı için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Salesforce korumalı alanı ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Salesforce korumalı alanında ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Salesforce korumalı alanı ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Salesforce korumalı alanı SSO 'Yu yapılandırma](#configure-salesforce-sandbox-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan Salesforce korumalı alanında B. Simon 'a sahip olmak için **[Salesforce korumalı alanı test kullanıcısı oluşturun](#create-salesforce-sandbox-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Salesforce korumalı alan** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız** varsa ve **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    ![Meta veri dosyasını karşıya yükle](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![meta veri dosyası seçin](common/browse-upload-metadata.png)

    > [!NOTE]
    > Hizmet sağlayıcı meta veri dosyasını, öğreticide daha sonra açıklanan Salesforce korumalı alan yönetim portalından alırsınız.

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra, yanıt URL **'si** değeri **yanıt URL 'si** metin kutusuna otomatik olarak doldurulur.

    ![image](common/both-replyurl.png)

    > [!Note]
    > **Yanıt URL 'si** değeri otomatik olarak yoksa, gereksinime göre değeri el ile girin.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Salesforce korumalı alanını ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak Salesforce korumalı alana erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Salesforce korumalı alanı**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-salesforce-sandbox-sso"></a>Salesforce korumalı alanı SSO 'yu yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve Salesforce korumalı alanı yönetici hesabınızda oturum açın.

2. Sayfanın sağ üst köşesindeki **Ayarlar simgesi** altında **Kurulum** ' a tıklayın.

    ![Sağ üstteki seçili "Ayarlar" simgesini ve açılan listeden "Kurulum" simgesini gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/configure1.png)

3. Sol gezinti bölmesindeki **Ayarlar** ' a gidin, ilgili bölümü genişletmek için **kimlik** ' e tıklayın. Sonra **tek Sign-On ayarları**' na tıklayın.

    !["Kimlik" menüsünden "tek Sign-On ayarları" seçiliyken sol bölmedeki "Ayarlar" menüsünü gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. **Tek Sign-On ayarları** sayfasında **Düzenle** düğmesine tıklayın.

    !["Düzenle" düğmesinin seçili olduğu "tek Sign-On ayarları" sayfasını gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/configure3.png)

5. **SAML etkin**' i seçin ve ardından **Kaydet**' e tıklayın.

    !["A M L etkin" onay kutusu seçili ve "Kaydet" düğmesinin seçili olduğu "tek Sign-On ayarları" sayfasını gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. SAML çoklu oturum açma ayarlarınızı yapılandırmak için **meta veri dosyasından yeni**' ye tıklayın.

    !["Meta veri dosyasından yeni" düğmesinin seçili olduğu "tek Sign-On ayarları" sayfasını gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Azure portal indirdiğiniz meta veri XML dosyasını karşıya yüklemek için **Dosya Seç** ' e tıklayın ve **Oluştur**' a tıklayın.

    !["Dosya Seç" ve "Oluştur" düğmelerinin seçildiği "tek Sign-On ayarları" sayfasını gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. **SAML tek Sign-On ayarları** sayfasında, alanlar otomatik olarak doldurulur ve Kaydet ' e tıklayın.

    ![Doldurulan alanları ve "Kaydet" düğmesinin seçili olduğu "tek Sign-On ayarları" sayfasını gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. **Tek Sign-On ayarları** sayfasında, hizmet sağlayıcısı meta veri dosyasını Indirmek Için **meta verileri indir** düğmesine tıklayın. Yukarıda açıklandığı gibi gereken URL 'Leri yapılandırmak için Azure portal **temel SAML yapılandırması** bölümünde bu dosyayı kullanın.

    !["Meta verileri Indir" düğmesinin seçili olduğu "tek Sign-On ayarları" sayfasını gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/configure4.png)

10. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdakiler için Önkoşullar verilmiştir:

    a. Doğrulanmış bir etki alanınız olmalıdır.

    b. Salesforce korumalı alanında etki alanınızı yapılandırmanız ve etkinleştirmeniz gerekir, bunun adımları Bu öğreticinin ilerleyen kısımlarında açıklanmıştır.

    c. Azure portal, **temel SAML yapılandırması** bölümünde **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:
  
    ![Salesforce korumalı alan etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/both-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak değeri yazın:`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Etki alanını etkinleştirdikten sonra bu değer Salesforce Sandbox portalından kopyalanmalıdır.

11. **SAML Imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** ' na tıklayın ve ardından XML dosyasını bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

12. Tarayıcınızda yeni bir sekme açın ve Salesforce korumalı alanı yönetici hesabınızda oturum açın.

13. Sayfanın sağ üst köşesindeki **Ayarlar simgesi** altında **Kurulum** ' a tıklayın.

    ![Sağ üst köşedeki "Ayarlar" simgesini ve açılan menüden "Kurulum" seçeneğini gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/configure1.png)

14. Sol gezinti bölmesindeki **Ayarlar** ' a gidin, ilgili bölümü genişletmek için **kimlik** ' e tıklayın. Sonra **tek Sign-On ayarları**' na tıklayın.

    !["Kimlik" menüsünden "tek Sign-On ayarları" seçiliyken sol gezinti bölmesindeki "Ayarlar" menüsünü gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. **Tek Sign-On ayarları** sayfasında **Düzenle** düğmesine tıklayın.

    !["Düzenle" düğmesinin seçili olduğu "tek Sign-On ayarları" sayfasını gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/configure3.png)

16. **SAML etkin**' i seçin ve ardından **Kaydet**' e tıklayın.

    !["A M L etkin" kutusu işaretli ve "Kaydet" düğmesi seçili olan "tek Sign-On ayarları" sayfasını gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. SAML çoklu oturum açma ayarlarınızı yapılandırmak için **meta veri dosyasından yeni**' ye tıklayın.

    !["Tek Sign-On ayarları" sayfasını ve "meta veri dosyasından yeni" düğmesini gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Meta veri XML dosyasını yüklemek için **Dosya Seç** ' e tıklayın ve **Oluştur**' a tıklayın.

    !["Dosya Seç" düğmesine ve "Oluştur" düğmesinin seçili olduğu "tek Sign-On ayarları" sayfasını gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. **SAML tek Sign-On ayarları** sayfasında, alanlar otomatik olarak doldurulur, **ad** metin kutusuna yapılandırmanın adını yazın (örneğin: *SPSSOWAAD_Test*) ve Kaydet ' e tıklayın.

    ![Doldurulmuş alanları olan "tek Sign-On ayarları" sayfasını, "ad" metin kutusunda bir örnek adını ve "Kaydet" düğmesini gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Etki alanınızı Salesforce korumalı alanı üzerinde etkinleştirmek için aşağıdaki adımları uygulayın:

    > [!NOTE]
    > Etki alanını etkinleştirmeden önce Salesforce korumalı alanında aynısını oluşturmanız gerekir. Daha fazla bilgi için bkz. [etki alanı adınızı tanımlama](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Etki alanı oluşturulduktan sonra lütfen doğru yapılandırıldığından emin olun.

21. Salesforce korumalı alanı 'ndaki sol gezinti bölmesinde, ilgili bölümü genişletmek için **Şirket ayarları** ' na tıklayın ve ardından **etki alanım**' a tıklayın.

    ![Sol gezinti bölmesinden "Şirket ayarları" ve "etki alanım" seçili olduğunu gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. **Kimlik doğrulama yapılandırması** bölümünde, **Düzenle**' ye tıklayın.

    !["Düzenle" düğmesinin seçili olduğu "kimlik doğrulama yapılandırması" bölümünü gösteren ekran görüntüsü.](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Kimlik doğrulama **yapılandırması** bölümünde, **kimlik doğrulama hizmeti** olarak, Salesforce korumalı alanında SSO yapılandırması sırasında ayarladığınız SAML Single Sign-On ayarının adını seçin ve **Kaydet**' e tıklayın.

    ![Tek Sign-On yapılandırma](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Salesforce korumalı alanı test kullanıcısı oluştur

Bu bölümde, Salesforce korumalı alanında Britta Simon adlı bir Kullanıcı oluşturulur. Salesforce korumalı alanı, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten Salesforce korumalı alanında yoksa, Salesforce korumalı alanına erişmeye çalıştığınızda yeni bir tane oluşturulur. Salesforce korumalı alanı otomatik Kullanıcı sağlamayı da destekler, Ayrıca, otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](salesforce-sandbox-provisioning-tutorial.md) .

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Salesforce Sandbox oturum açma URL 'sine yeniden yönlendirilir.  

* Doğrudan Salesforce Sandbox oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Salesforce korumalı alanında otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki Salesforce korumalı alanı kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Salesforce korumalı alanında otomatik olarak oturum açmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Salesforce korumalı alanını yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimlerini uygulayabilirsiniz. Oturum denetimleri koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)