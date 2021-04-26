---
title: 'Öğretici: en düşük çevrimiçi ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile çevrimiçi olarak çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 9fdcd8a82b901e00e28f0ddd89ba53d9a2e3fbae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952720"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Öğretici: uniFLOW Online ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Ifkılow online 'ı tümleştirmeyi öğreneceksiniz. Azure AD ile Ifkılow online 'ı tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, hiçbir Unkılow online 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla çevrimiçi olmadan oturum açmasını sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* En düşük çevrimiçi Kiracı.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* ıkılow Online, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-uniflow-online-from-the-gallery"></a>Galeriden çevrimiçi olmayan çevrimiçi Ekle

Ikılow online 'ın tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden, Galeri 'den yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **çevrimiçi olmayan** ' ı yazın.
1. Sonuçlar panelinden **olmayan çevrimiçi** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-uniflow-online"></a>Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Unkılow Online ile yapılandırın ve test edin. SSO 'nun çalışması için bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu yapılandırmak ve test etmek için, aşağıdaki adımları uygulayın:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
   1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
   1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Unkılow ONLINE SSO 'Yu yapılandırın](#configure-uniflow-online-sso)** .
    1. Uygulama tarafında Kullanıcı oturumunu test etmek için **[oluşturulan test kullanıcısı ' nı kullanarak hiç bir çevrimiçi ortamda oturum açın](#sign-in-to-uniflow-online-using-the-created-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **çevrimiçi** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki desenlerden birini kullanarak bir URL yazın:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için, en [düşük çevrimiçi istemci desteği ekibine](mailto:support@nt-ware.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz veya diğer bir çevrimiçi kiracınızda GÖSTERILEN yanıt URL 'sine başvurabilirsiniz.

1. En düşük çevrimiçi uygulama, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdakilerin yanı sıra, en düşük çevrimiçi uygulama aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name |  Kaynak özniteliği|
    | -----------| --------------- |
    | DisplayName | User. DisplayName |
    | takma ad | User. onpremisessamaccountname |

   > [!NOTE]
   > `user.onpremisessamaccountname`Özniteliği yalnızca Azure AD kullanıcılarınız yerel bir Windows Active Directory eşitlendiğinde bir değer içerir.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak, Ifkılow online 'a erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **çevrimiçi değil**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

> [!NOTE]
> Tüm kullanıcıların elle atama olmadan uygulamaya erişmesine izin vermek için, **Yönet** bölümüne gidin ve **Özellikler**' i seçin. Ardından, **Kullanıcı Ataması gerekli** parametresini **Hayır** olarak değiştirin.

## <a name="configure-uniflow-online-sso"></a>Unkılow online SSO yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak olmayan çevrimiçi Web sitesinde oturum açın.

1. Sol Gezinti panelinden **Kullanıcı** sekmesi ' ni seçin.

    ![Ekran görüntüsü Kullanıcı tarafından seçili olmayan çevrimiçi siteden görüntülenir.](./media/uniflow-online-tutorial/configure-1.png)

1. **Kimlik sağlayıcıları**' na tıklayın.

    ![Ekran görüntüsü kimlik sağlayıcılarının seçili olduğunu gösterir.](./media/uniflow-online-tutorial/configure-2.png)

1. **Kimlik sağlayıcısı ekle**' ye tıklayın.

    ![Ekran görüntüsü, seçilmiş kimlik sağlayıcısını gösterir.](./media/uniflow-online-tutorial/configure-3.png)

1. **KIMLIK sağlayıcısı ekle** bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz KIMLIK sağlayıcısı ekle bölümünü gösterir.](./media/uniflow-online-tutorial/configure-4.png)

    a. Ex: *Azuread SSO* görünen adını girin.

    b. **Sağlayıcı türü** için açılan listeden **WS-Besme** seçeneğini belirleyin.

    c. **WS-bestype** için açılan listeden **Azure Active Directory** seçeneğini belirleyin.

    d. **Kaydet**’e tıklayın.

1. **Genel** sekmesinde, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Genel sekmesini gösterir.](./media/uniflow-online-tutorial/configure-5.png)

    a. Ex: *Azuread SSO* görünen adını girin.

    b. **ADFS Federasyon meta verileri** için **Kimden URL 'si** seçeneğini belirleyin.

    c. **Federasyon meta veri URl 'si** metin kutusunda, Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın.

    d. **Kimlik sağlayıcısını** **etkin** olarak seçin.

    e. **Otomatik Kullanıcı kaydını** **etkinleştirildi** olarak seçin.

    f. **Kaydet**’e tıklayın.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Oluşturulan test kullanıcısını kullanarak Kaldırılow online 'da oturum açın

1. Farklı bir Web tarayıcısı penceresinde, kiracınız için Unkılow online URL 'sine gidin.

1. Azure AD örneğiniz aracılığıyla oturum açmak için önceden oluşturulmuş kimlik sağlayıcısını seçin.

1. Test kullanıcısını kullanarak oturum açın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz en düşük çevrimiçi oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan çevrimiçi oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım ' da olmayan çevrimiçi Kutucuğa tıkladığınızda, bu, bu, en düşük çevrimiçi oturum açma URL 'sine yönlendirecektir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Ifkılow online 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve geri alımını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).