---
title: 'Öğretici: başarıyla Azure Active Directory çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve başarılı faktörleri arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: a903eb6000cdd5212ad358cae4952e27a4719070
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725269"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Öğretici: başarıyla Azure Active Directory çoklu oturum açma (SSO) Tümleştirmesi

Bu öğreticide, başarılı faktörleri Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Başarılı faktörleri Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de başarılı faktörlere erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla başarılı bir şekilde oturum açmaya yönelik otomatik olarak oturum açmaya olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli abonelik başarıyla etkinleştirildi.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Başarılı etken **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-successfactors-from-the-gallery"></a>Galeriden başarılı faktör ekleme

Başarılı faktörlerin Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize başarılı bir faktör eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **başarılı faktörleri** yazın.
1. Sonuçlar panelinden **başarılı etmenler** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Başarılı faktörlere yönelik Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu başarılı faktörlerle yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında başarılı bir şekilde bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu başarılı faktörlerle yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[başarılı ETMENLERI SSO-yapılandırma](#configure-successfactors-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan başarılı faktörlerdeki B. Simon 'a sahip olmak için **[başarılı bir test kullanıcısı oluşturun](#create-successfactors-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **başarılı etken** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.eu`

    b. **Tanımlayıcı** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    - `https://www.successfactors.com/<companyname>`
    - `https://www.successfactors.com`
    - `https://<companyname>.successfactors.eu`
    - `https://www.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://hcm4preview.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.eu`
    - `https://www.successfactors.cn`
    - `https://www.successfactors.cn/<companyname>`

    c. **Yanıt URL** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.successfactors.com`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.successfactors.eu`
    - `https://<companyname>.sapsf.eu`
    - `https://<companyname>.sapsf.eu/<companyname>`
    - `https://<companyname>.sapsf.cn`
    - `https://<companyname>.sapsf.cn/<companyname>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [başarılı olan istemci desteği ekibine](https://www.sap.com/support.html) başvurun.

4. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Başarılı etmenleri ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, başarılı etkenlere erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **başarılı etmenler**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-successfactors-sso"></a>Başarılı etken SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak başarılı bir şekilde **Yönetim Portalı** 'nda oturum açın.

2. **Uygulama güvenliğini** ve yerel olarak **Çoklu oturum açma özelliğini** ziyaret edin.

3. Herhangi bir değeri **sıfırlama belirtecine** koyun ve SAML SSO 'yu etkinleştirmek Için **belirteci kaydet** ' e tıklayın.

    ![Ekran görüntüsü, bir belirteç girebileceğiniz şekilde adlandırılan çoklu oturum açma özellikleriyle uygulama güvenliği sekmesini gösterir.][11]

    > [!NOTE]
    > Bu değer, açık/kapalı anahtar olarak kullanılır. Herhangi bir değer kaydedilirse, SAML SSO 'SU açık olur. Boş bir değer kaydedilirse, SAML SSO KAPALıDıR.

4. ' In altında yerel ekran görüntüsü ve aşağıdaki eylemleri gerçekleştirir:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz, SAML tabanlı S S O bölmesini gösterir.][12]
  
    a. **SAML V2 SSO** radyo düğmesini seçin
  
    b. **SAML asserting parti adını**(ÖRNEĞIN, SAML veren + şirket adı) ayarlayın.

    c. **Veren URL** metin kutusuna, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    d. **Zorunlu Imza ıste** **onay** seçimini yapın.

    e. **ETKIN** **SAML bayrağını etkinleştir**' i seçin.

    f. **Oturum açma Isteği imzası olarak Hayır (SF tarafından oluşturulan/SP/RP)** seçeneğini belirleyin. 

    örneğin: **SAML profili** olarak **tarayıcı/gönderi profili** ' ni seçin.

    h. **Sertifikayı zorla geçerli süre** olarak **Hayır** ' ı seçin.

    i. İndirilen sertifika dosyasının içeriğini Azure portal kopyalayın ve ardından bunu **SAML sertifikası** metin kutusuna yapıştırın.

    > [!NOTE] 
    > Sertifika içeriğinin başlangıç sertifikası ve bitiş sertifikası etiketleri olmalıdır.

5. SAML V2 ' ye gidin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü SAML V2 S P başlatılan oturum kapatma bölmesini gösterir; burada, açıklanan değerleri girebilirsiniz.][13]

    a. **SP tarafından başlatılan genel oturum kapatma desteği** olarak **Evet** ' i seçin.

    b. **Global oturum kapatma hizmeti URL 'si (LogoutRequest Destination)** metin kutusunda, Azure Portal formunu kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

    c. **Hayır** , **SP ıste tüm NameID öğelerini şifrelemelidir**.

    d. **NameID biçimi** olarak **belirtilmemiş** öğesini seçin.

    e. **SP tarafından başlatılan oturum açmayı etkinleştir (authbir)** olarak **Evet** ' i seçin.

    f. **Istek gönder Company-Wide veren** metin kutusu, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

6. Oturum açma kullanıcı adları büyük/küçük harfe duyarsız hale getirmek istiyorsanız bu adımları gerçekleştirin.

    ![Tek Sign-On yapılandırma][29]

    a. **Şirket ayarlarını** ziyaret edin (alt kısma yakın).

    b. **Büyük/küçük harfe duyarlı olmayan kullanıcı adını etkinleştir** yakınında onay kutusunu seçin.

    c. **Kaydet**’e tıklayın.

    > [!NOTE]
    > Bunu etkinleştirmeye çalışırsanız, sistem yinelenen bir SAML oturum açma adı oluşturup oluşturmadığını denetler. Örneğin, müşteri için Kullanıcı1 ve Kullanıcı1 Kullanıcı adları varsa. Büyük/küçük harf duyarlılığı bu yinelemeleri yapar. Sistem size bir hata iletisi verir ve özelliği etkinleştirmez. Müşterinin, farklı bir şekilde yazılması için kullanıcı adlarıyla birini değiştirmesi gerekir.

### <a name="create-successfactors-test-user"></a>Başarılı etken testi Kullanıcı Oluştur

Azure AD kullanıcılarının başarılı bir faktörde oturum açmasını sağlamak için, bunların başarılı bir şekilde sağlanması gerekir. Başarılı etken durumunda, sağlama el ile gerçekleştirilen bir görevdir.

Kullanıcıları başarılı bir şekilde oluşturulan kullanıcılara almak için, [başarılı olan destek ekibine](https://www.sap.com/support.html)başvurmanız gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz başarılı bir oturum açma URL 'sine yönlendirecektir. 

* Başarılı bir şekilde oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki başarılı etmenler kutucuğuna tıkladığınızda, bu işlem başarılı bir şekilde oturum açma URL 'sine yönlendirecektir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Başarılı faktörleri yapılandırdıktan sonra, kuruluşunuzun gizli verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimlerini uygulayabilirsiniz. Oturum denetimleri koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png