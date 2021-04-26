---
title: "Öğretici: çözüm GmbH göre Bitbucket için SAML SSO 'SU ile tümleştirme Azure Active Directory | Microsoft Docs"
description: GmbH ile Bitbucket için SAML SSO ve Azure Active Directory arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: aa5e3e88ceb957728799f27482de7477ba6b7b48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621255"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Öğretici: çözüm GmbH göre Bitbucket için SAML SSO ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile çözümleme GmbH ile Bitbucket için SAML SSO 'SU tümleştirmeyi öğreneceksiniz. GmbH ile Bitbucket için SAML SSO 'yu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Resolution GmbH tarafından Bitbucket için toSAML SSO 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla çözünürlüklü GmbH tarafından Bitbucket için toSAML SSO 'da otomatik olarak imzalanmasını etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.


## <a name="prerequisites"></a>Önkoşullar

GmbH tarafından Bitbucket için SAML SSO 'SU ile Azure AD tümleştirmesini yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Çözünürlükte GmbH çoklu oturum açma özellikli aboneliğe göre Bitbucket için SAML SSO 'SU

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Resolution GmbH tarafından Bitbucket için SAML SSO **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Resolution GmbH tarafından desteklenen Bitbucket için SAML SSO **, tam zamanında** Kullanıcı sağlamayı destekler


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Galeriden çözüm GmbH göre Bitbucket için SAML SSO 'SU ekleme

GmbH ile Bitbucket için SAML SSO 'yu Azure AD 'ye tümleştirme özelliğini yapılandırmak için, Galeriden, yönetim SaaS uygulamaları listenize çözünürlüklü Bitbucket için SAML SSO 'SU ' nu çözüm GmbH ile eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol bölmede **Azure Active Directory**' yi seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **GmbH ile Bitbucket için SAML SSO** yazın.
1. Sonuçlardan **çözüm GmbH tarafından Bitbucket Için SAML SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Resolution GmbH tarafından Bitbucket için SAML SSO 'SU için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak, GmbH tarafından sunulan Bitbucket için SAML SSO 'Su Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve GmbH ile Bitbucket için SAML SSO 'daki ilgili Kullanıcı arasında bağlı bir ilişki kurmanız gerekir.

GmbH tarafından Bitbucket için SAML SSO 'su ile Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:


1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, **[Resolution GmbH SSO 'su tarafından Bitbucket IÇIN SAML SSO 'Yu yapılandırın](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** .
    1. User 'ın Azure AD gösterimine bağlı olan, Resolution GmbH 'e göre Bitbucket için SAML SSO 'SU ' nde bir Britta Simon 'ın bir karşılığı olması için, **[GmbH test kullanıcısına göre Bitbucket IÇIN SAML SSO 'Su oluşturun](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Bu bölümde, Azure portal Azure AD SSO 'yu etkinleştirirsiniz.
 
1. Azure portal, **GmbH Ile Bitbucket Için SAML SSO** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma** seçeneğini belirleyin.
1. **Tek bir Sign-On yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları gerçekleştirin:


    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/samlsso`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:
    
    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için, [Resolution GmbH istemci desteği ekibine göre Bitbucket Için SAML SSO 'su ile](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) iletişim kurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

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

Bu bölümde, Resolution GmbH tarafından Bitbucket için SAML SSO 'SU erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirin.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **çözüm GmbH göre Bitbucket Için SAML SSO**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı ekle**'yi seçin. Sonra **atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, Kullanıcı listesinden **B. Simon** öğesini seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>GmbH SSO 'SU ile Bitbucket için SAML SSO 'yu yapılandırma

1. Yönetici olarak çözüm GmbH Şirket sitesine göre Bitbucket için SAML SSO 'ünüz üzerinde oturum açın.

2. Ana araç çubuğunun sağ tarafında, **Ayarlar**' a tıklayın.

3. HESAPLAR bölümüne gidin ve menü çubuğunda **SAML SingleSignOn** öğesine tıklayın.

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. **SAML SIngleSignOn eklenti yapılandırması sayfasında** **IDP Ekle**' ye tıklayın. 

    ![IDP ekleme](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. **SAML kimlik sağlayıcınızı seçin** sayfasında aşağıdaki adımları uygulayın:

    ![Kimlik sağlayıcısı](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. **IDP türünü** **Azure AD** olarak seçin.

    b. **Ad** metin kutusuna adı yazın.

    c. **Açıklama** metin kutusuna açıklamayı yazın.

    d. **İleri**’ye tıklayın.

6. **Kimlik sağlayıcısı yapılandırma sayfasında** **İleri**' ye tıklayın.

    ![Kimlik yapılandırması](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Azure portal 'den indirdiğiniz **meta VERI XML** dosyasını karşıya yüklemek Için **SAML IDP meta verilerini Içeri aktar** sayfasında **Dosya Yükle** ' ye tıklayın.

    ![Idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. **İleri**’ye tıklayın.

9. **Ayarları kaydet**’e tıklayın.

    ![Kaydet](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Resolution GmbH test kullanıcısına göre Bitbucket için SAML SSO oluşturma

Bu bölümün amacı, GmbH tarafından verilen Bitbucket için SAML SSO 'da Britta Simon adlı bir Kullanıcı oluşturmaktır. Resolution GmbH tarafından desteklenen Bitbucket için SAML SSO, tam zamanında sağlamayı destekler ve ayrıca kullanıcılar el ile oluşturulabilir, gereksiniminize göre, BT [GmbH istemci desteği ekibine göre Bitbucket Için SAML SSO 'su](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) ile iletişim kurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum akışını başlatabileceğiniz çözüm GmbH oturum açma URL 'sine göre Bitbucket için SAML SSO 'ya yönlendirilir.  

* Bit demeti için SAML SSO 'SU için doğrudan çözüm GmbH oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal ' de **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız GmbH tarafından ayarlanan BITBUCKET için SAML SSO 'su için otomatik olarak oturum açmış olmanız gerekir.

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım için SAML SSO for Resolution GmbH kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, bu durumda SSO 'yu ayarladığınız için SAML SSO 'SU için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

GmbH tarafından Bitbucket için SAML SSO 'yu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimlerini uygulayabilirsiniz. Oturum denetimleri koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).