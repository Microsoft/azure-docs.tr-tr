---
title: 'Öğretici: Man Password Manager ile Azure Active Directory tümleştirme & dijital kasa | Microsoft Docs'
description: Azure Active Directory ve Man Password Manager & dijital kasa arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: b70c50e7c2900f884dd4d91c6650205bc626326e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96178050"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Öğretici: Man Password Manager ile Azure Active Directory tümleştirme & dijital kasa

Bu öğreticide, Man parola Yöneticisi & dijital kasasını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Bu tümleştirme size aşağıdaki avantajları sağlar:

* Azure AD 'de, Man Password Manager & dijital kasa erişimine erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla, bir dijital kasa (çoklu oturum açma) & bir şekilde, kullanıcılar için Man Password Manager 'da otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini, Man Password Manager & dijital kasa ile yapılandırmak için şunlar gerekir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, bir [aylık deneme sürümü](https://azure.microsoft.com/pricing/free-trial/)alabilirsiniz.
* Man Password Manager, çoklu oturum açma (SSO) için etkinleştirilen dijital kasa aboneliği &.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Man parola Yöneticisi & dijital kasa, SP tarafından başlatılan SSO 'yu destekler.

* Man parola Yöneticisi & dijital kasa, tam zamanında Kullanıcı sağlamayı destekler.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Galeriden bir dijital kasa & Man parola Yöneticisi ekleme

Man Password Manager & dijital kasasının Azure AD 'ye tümleştirilmesini yapılandırmak için, uygulamayı Galeriden yönetilen hizmet olarak yazılım (SaaS) uygulamaları listenize ekleyin.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol bölmede **Azure Active Directory** hizmeti seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle**' de, arama kutusuna **man Password Manager & dijital kasasını** yazın.
1. Sonuçlar panelinden bir **Man Password Manager & bir dijital kasa** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Azure AD SSO for Man Password Manager & dijital kasa yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Man Password Manager &, dijital kasasıyla yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında, Man Password Manager & dijital kasa arasında bağlı bir ilişki kurmanız gerekir.

Azure AD SSO 'yu, Man Password Manager & dijital kasa ile yapılandırmak ve test etmek için:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .

    * Britta Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    * Azure AD 'de çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirmek için [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .

1. Uygulama tarafında SSO ayarlarını yapılandırmak için, [Man Password Manager & dijital kasa SSO 'Yu yapılandırın](#configure-keeper-password-manager--digital-vault-sso) .
    * [Bir Man Password manager &, bir dijital kasa sınama kullanıcısına](#create-a-keeper-password-manager--digital-vault-test-user) , kullanıcının Azure AD gösterimine bağlı dijital kasadaki bir Man password Manager & bir Britta Simon 'a sahip olacak şekilde bir Man parola Yöneticisi oluşturun.
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için [test SSO 'su](#test-sso) .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Man Password Manager & dijital kasa** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun. **Çoklu oturum açma** seçeneğini belirleyin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Kalem simgesi vurgulanmış şekilde, SAML ile tek Sign-On ayarlama ekran görüntüsü.](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** için aşağıdaki kalıbı kullanan bir URL yazın:
    * Cloud SSO için: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Şirket içi SSO için: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. **Tanımlayıcı (VARLıK kimliği)** için aşağıdaki kalıbı kullanan bir URL yazın:
    * Cloud SSO için: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Şirket içi SSO için: `https://<KEEPER_FQDN>/sso-connect`

    c. **Yanıt URL 'si** için aşağıdaki kalıbı kullanan bir URL yazın:
    * Cloud SSO için: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Şirket içi SSO için: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için, [dijital kasa istemci desteği ekibine & Man parola Yöneticisi](https://keepersecurity.com/contact.html)ile iletişime geçin. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Man Password Manager & dijital kasa uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![Talepler & kullanıcı özniteliklerinin ekran görüntüsü.](common/default-attributes.png)

1. Ayrıca, Man Password Manager & dijital kasa uygulaması, SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. Bunlar aşağıdaki tabloda gösterilmiştir. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği|
    | ------------| --------- |
    | Birinci | Kullanıcı. |
    | Son | User. soyadı |
    | E-posta | Kullanıcı. Mail |

5. **SAML Ile tek Sign-On ayarlama** sırasında, **SAML Imzalama sertifikası** bölümünde **İndir**' i seçin. Bu, gereksiniminize göre seçeneklerden **Federasyon meta VERI XML** 'sini indirir ve bilgisayarınıza kaydeder.

    ![Yükleme vurgulanmış şekilde SAML Imzalama sertifikasının ekran görüntüsü.](common/metadataxml.png)

6. **Man Password Manager 'ı ayarlama & dijital kasasında** uygun URL 'leri gereksiniminize göre kopyalayın.

    ![URL 'Ler vurgulanmış şekilde, bir dijital kasa & Man Password Manager 'ı ayarlama ekran görüntüsü.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümde, adlı Azure portal bir test kullanıcısı oluşturacaksınız `B.Simon` .

1. Azure Portal sol bölmeden, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmında **Yeni Kullanıcı**' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** için girin `B.Simon` .  
   1. **Kullanıcı adı** için öğesini girin `username@companydomain.extension` . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster**' i seçin ve ardından gösterilen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Man Password Manager & dijital kasaya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirin.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Man Password Manager & dijital kasa**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı ekle**'yi seçin. **Atama Ekle** bölümünde **Kullanıcılar ve gruplar**’ı seçin.
1. **Kullanıcılar ve gruplar**' da, Kullanıcı listesinden **B. Simon** ' u seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, **varsayılan erişim** rolü seçilidir.
1. **Atama Ekle**' de, **ata**' yı seçin.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Man parola yöneticisini yapılandırma & dijital kasa SSO 'SU

Uygulamanın SSO 'yu yapılandırmak için, [Man Destek Kılavuzu](https://docs.keeper.io/sso-connect-guide/)'ndaki yönergelere bakın.

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>Bir Man parola Yöneticisi oluşturun & dijital kasa test kullanıcısı

Azure AD kullanıcılarının, dijital kasadaki bir Man Password Manager & oturum açmasını sağlamak için bunları sağlamanız gerekir. Uygulama, tam zamanında Kullanıcı sağlamayı destekler ve kimlik doğrulama kullanıcıları uygulamada otomatik olarak oluşturulur. Kullanıcıları el ile ayarlamak istiyorsanız, [Man desteğiyle](https://keepersecurity.com/contact.html)iletişime geçin.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal **Bu uygulamayı test et**' i seçin. Bu, oturum açma işlemini başlatabileceğiniz bir dijital kasa & Man Password Manager oturum açma URL 'sine yeniden yönlendirir. 

* Uygulamanın oturum açma URL 'sine doğrudan gidebilir ve oturum açma işlemini buradan başlatabilirsiniz.

* Microsoft Access panel ' i kullanabilirsiniz. Erişim paneli 'nde **Man Password Manager & dijital kasa** kutucuğunu seçtiğinizde, bu sizi uygulamanın oturum açma URL 'sine yönlendirir. Erişim paneli hakkında daha fazla bilgi için bkz. [uygulamalarım portalından oturum açma ve uygulamaları başlatma](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Man parola Yöneticisi & dijital kasa yapılandırdıktan sonra, oturum denetimini zorunlu kılabilirsiniz. Bu, kuruluşunuzun hassas verilerinin, gerçek zamanlı olarak ayıklanma ve içe taşmayı korur. Oturum denetimi koşullu erişimden genişletiliyor. Daha fazla bilgi için bkz. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad).