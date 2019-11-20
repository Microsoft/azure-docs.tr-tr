---
title: 'Öğretici: G Suite ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve G Suite arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d8e13a4e042146ef2b99728e41e14f1dcb3435
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885368"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>Öğretici: G Suite ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, G Suite 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. G Suite 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de G Suite 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla G Suite 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Ön koşullar

Başlamak için aşağıdaki öğeler gereklidir:

- Bir Azure AD aboneliği.
- G Suite çoklu oturum açma (SSO) etkin aboneliği.
- Bir Google Apps aboneliği veya Google Cloud Platform aboneliği.

> [!NOTE]
> Bu öğreticideki adımları test etmek için, üretim ortamının kullanılmasını önermiyoruz. Bu belge Yeni Kullanıcı çoklu oturum açma deneyimi kullanılarak oluşturulmuştur. Hala eskisini kullanıyorsanız, kurulum farklı görünür. Yeni deneyimi G-Suite uygulamasının çoklu oturum açma ayarlarındaki etkin bir şekilde etkinleştirebilirsiniz. **Azure AD, kurumsal uygulamalar**' a gidin, **G Suite**' i seçin, **Çoklu oturum açma** ' yı seçin ve ardından **Yeni deneyimimizi dene**' ye tıklayın.

Bu öğreticideki adımları test etmek için aşağıdaki önerileri izlemeniz gerekir:

- Gerekli olmadığı takdirde üretim ortamınızı kullanmayın.
- Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

## <a name="frequently-asked-questions"></a>Sık Sorulan Sorular

1. **S: Bu tümleştirme, Azure AD ile SSO tümleştirmesi Google Cloud Platform destekliyor mu?**

    C: Evet. Google Cloud Platform ve Google Apps aynı kimlik doğrulama platformunu paylaşır. Bu nedenle, GCP tümleştirmesini yapmak için SSO 'yu Google Apps ile yapılandırmanız gerekir.

2. **S: Azure AD çoklu oturum açma ile uyumlu Kınbook 'Lar ve diğer Chrome cihazları mı var?**
  
    Y: Evet, kullanıcılar, Azure AD kimlik bilgilerini kullanarak Kmebook cihazlarında oturum açabilirler. Kullanıcıların kimlik bilgilerini iki kez nasıl izleyedikleri hakkında bilgi edinmek için bu [G Suite destek makalesine](https://support.google.com/chrome/a/answer/6060880) bakın.

3. **S: çoklu oturum açmayı etkinleştirdiğimde, kullanıcılar Azure AD kimlik bilgilerini Google Classroom, GMail, Google Drive, YouTube vb. gibi herhangi bir Google ürününde oturum açmak için kullanabilir.**

    A: kuruluşunuzda etkinleştirmek veya devre dışı bırakmak istediğiniz [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) 'e bağlı olarak Evet.

4. **S: G Suite Kullanıcılarım yalnızca bir alt kümesi için çoklu oturum açmayı etkinleştirebilir miyim?**

    Y: Hayır, çoklu oturum açmayı açmak hemen, tüm G Suite kullanıcılarınızın Azure AD kimlik bilgileriyle kimlik doğrulaması yapmasını gerektirir. G Suite birden çok kimlik sağlayıcısı 'nı desteklemediğinden, G Suite ortamınız için kimlik sağlayıcısı Azure AD veya Google--olabilir, ancak aynı anda ikisi de olamaz.

5. **S: bir Kullanıcı Windows aracılığıyla oturum açmışsa, bir parola istenmeden G Suite 'e otomatik olarak kimlik doğrulaması yapabilir mi?**

    Y: Bu senaryoyu etkinleştirmek için iki seçenek vardır. İlk olarak, kullanıcılar [Azure Active Directory JOIN](../device-management-introduction.md)aracılığıyla Windows 10 cihazlarında oturum açabilirler. Alternatif olarak, kullanıcılar, bir [Active Directory Federasyon Hizmetleri (AD FS) (AD FS)](../hybrid/plan-connect-user-signin.md) dağıtımı aracılığıyla Azure AD 'de çoklu oturum açma için etkinleştirilen bir şirket içi Active Directory etki alanına katılmış Windows cihazlarında oturum açabilirler. Her iki seçenek de, Azure AD ve G Suite arasında çoklu oturum açmayı etkinleştirmek için aşağıdaki öğreticideki adımları gerçekleştirmenizi gerektirir.

6. **S: "geçersiz e-posta" hata iletisi aldığımda ne yapmam gerekir?**

    Y: Bu kurulum Için, kullanıcıların oturum açabiliyor olması için e-posta özniteliği gereklidir. Bu öznitelik el ile ayarlanamaz.

    E-posta özniteliği, geçerli bir Exchange lisansına sahip herhangi bir kullanıcı için oto doldurulur. Kullanıcı e-posta etkin değilse bu hata, uygulamanın erişim vermek için bu özniteliğin alınması gerektiği için alınır.

    Yönetici hesabıyla portal.office.com adresine giderek Yönetim Merkezi, faturalandırma, abonelikler ' e tıklayabilir, Office 365 aboneliğinizi seçebilir ve kullanıcılara ata ' ya tıklayabilirsiniz, aboneliklerini denetlemek istediğiniz kullanıcıları seçebilir ve sağ bölmede açık ' a tıklayın. lisansları düzenleyin.

    O365 lisansı atandıktan sonra uygulanması birkaç dakika sürebilir. Bundan sonra, Kullanıcı. mail özniteliği yeniden doldurulur ve sorun çözümlenmelidir.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* G Suite **SP** tarafından başlatılan SSO 'yu destekler

* G Suite [ **Otomatik** Kullanıcı sağlamasını destekler](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

## <a name="adding-g-suite-from-the-gallery"></a>Galeriden G Suite ekleme

G Suite 'in Azure AD ile tümleştirilmesini yapılandırmak için galerideki G Suite 'i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **G Suite** yazın.
1. Sonuçlar panelinden **G Suite** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>G Suite için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu G Suite ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve G Suite 'deki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu G Suite ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[G SUITE SSO 'Yu yapılandırın](#configure-g-suite-sso)** .
    1. G **[Suite test kullanıcısı oluşturun](#create-g-suite-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan g Suite 'e ait B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **G Suite** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **Gmail** için yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

1. **Temel SAML yapılandırması** bölümünde **Google Cloud Platform** için yapılandırmak istiyorsanız aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. G Suite çoklu oturum açma yapılandırmasında varlık KIMLIĞI/tanımlayıcı değeri sağlamıyor; bu nedenle, **etki alanına özel veren** seçeneğinin Işaretini kaldırdığınızda tanımlayıcı değeri `google.com`olur. **Etki alanına özel veren** seçeneğini denetederseniz `google.com/a/<yourdomainname.com>`olacaktır. **Etki alanına özel veren** seçeneğini denetlemek/işaretini kaldırmak için Öğreticinin ilerleyen kısımlarında açıklanan **G paketi SSO 'yu Yapılandır** bölümüne gitmeniz gerekir. Daha fazla bilgi için [G Suite istemci desteği ekibine](https://www.google.com/contact/)başvurun.

1. G Suite uygulamanız belirli bir biçimde SAML onayları bekler, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde buna bir örnek gösterilmektedir. **Benzersiz kullanıcı tanımlayıcısının** varsayılan değeri **User. UserPrincipalName** , ancak G Suite bunun kullanıcının e-posta adresiyle eşleştirilmesini bekliyor. Bu şekilde, listeden **User. Mail** özniteliğini kullanabilir veya kuruluşunuzun yapılandırmasına göre uygun öznitelik değerini kullanabilirsiniz.

    ![image](common/edit-attribute.png)

1. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, yukarıdaki görüntüde gösterildiği gibi, **Düzen simgesini** kullanarak talepleri DÜZENLEYIN veya aşağıdaki resimde gösterildiği gibi SAML belirteci özniteliğini yapılandırmak için **yeni talep Ekle** ' yi kullanarak talepleri ekleyin ve aşağıdaki adımları gerçekleştirin:

    | Ad | Kaynak özniteliği |
    | ---------------| --------------- |
    | Benzersiz kullanıcı tanımlayıcısı | Kullanıcı. Mail |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik**olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Tamam 'a** tıklayın

    g. **Kaydet** düğmesine tıklayın.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **G Suite 'ı ayarlama** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, G Suite 'e erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **G Suite**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-g-suite-sso"></a>G Suite SSO 'yu yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve yönetici hesabınızı kullanarak [G Suite yönetici konsolunda](https://admin.google.com/) oturum açın.

2. **Güvenlik**' e tıklayın. Bağlantıyı görmüyorsanız, ekranın alt kısmındaki **daha fazla denetim** menüsünde gizli olabilir.

    ![Güvenlik'e tıklayın.][10]

3. **Güvenlik** sayfasında, **Çoklu oturum açma (SSO) seçeneğini belirleyin.**

    ![SSO ' ya tıklayın.][11]

4. Aşağıdaki yapılandırma değişikliklerini gerçekleştirin:

    ![SSO 'yu yapılandırma][12]

    a. **Üçüncü taraf kimlik sağlayıcısıyla bırlıkte SSO ayarla**' yı seçin.

    b. G Suite 'teki **oturum açma sayfası URL 'si** alanında, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. G Suite 'teki **oturum kapatma sayfası URL 'si** alanında, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    d. G Suite 'teki **parola URL 'Sini Değiştir** alanında, Azure Portal kopyaladığınız **parola URL 'si Değiştir** değerini yapıştırın.

    e. G Suite 'de, **doğrulama sertifikası**için Azure Portal indirdiğiniz sertifikayı karşıya yükleyin.

    f. Azure AD 'deki Yukarıdaki **temel SAML yapılandırması** bölümünde bahsedilen nota göre, **etki alanına özgü sertifikayı kullanma** seçeneğini işaretleyin/işaretini kaldırın.

    g. **Değişiklikleri Kaydet**' e tıklayın.

### <a name="create-g-suite-test-user"></a>G Suite test kullanıcısı oluştur

Bu bölümün amacı, G Suite 'te B. Simon adlı [bir Kullanıcı oluşturmaktır](https://support.google.com/a/answer/33310?hl=en) . Kullanıcı G Suite 'te el ile oluşturulduktan sonra, Kullanıcı artık Office 365 oturum açma kimlik bilgilerini kullanarak oturum açabilir.

G Suite ayrıca otomatik Kullanıcı sağlamayı da destekler. Otomatik Kullanıcı sağlamayı yapılandırmak için, önce [Otomatik Kullanıcı sağlaması Için G Suite 'i yapılandırmanız](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)gerekir.

> [!NOTE]
> Azure AD 'de sağlama, çoklu oturum açmayı test etmeden önce açık bırakılmadığından, kullanıcının G Suite 'de zaten mevcut olduğundan emin olun.

> [!NOTE]
> El ile bir kullanıcı oluşturmanız gerekiyorsa, [Google destek ekibine](https://www.google.com/contact/)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde G Suite kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız G Suite 'e otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Kullanıcı sağlamayı yapılandırma](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
- [Azure AD ile G Suite 'i deneyin](https://aad.portal.azure.com/)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
