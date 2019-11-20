---
title: 'Öğretici Azure Active Directory: BeyondTrust uzaktan destek ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve BeyondTrust uzaktan desteği arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff21c3ee7721c82232e668ddb9645895080cf79
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081995"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Öğretici: BeyondTrust uzak desteğiyle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, BeyondTrust uzaktan desteğini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. BeyondTrust uzaktan desteğini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, BeyondTrust uzaktan desteğe erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla uzak desteğe güvenmesini sağlamak için kullanıcılarınızın otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* BeyondTrust, uzaktan çoklu oturum açma (SSO) özellikli aboneliği destekler.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* BeyondTrust uzaktan destek, **SP** tarafından başlatılan SSO 'yu destekler
* BeyondTrust uzak destek **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Galeriden BeyondTrust uzaktan desteği ekleme

Azure AD 'de BeyondTrust uzaktan destek tümleştirmesini yapılandırmak için, Galeriden BeyondTrust uzaktan desteğini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Beyondtrust uzak desteği** yazın.
1. Sonuçlar panelinden **Beyondtrust uzaktan desteği** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>BeyondTrust uzak destek için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu BeyondTrust uzak desteğiyle yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve BeyondTrust uzaktan destek içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu, BeyondTrust uzak desteğiyle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[BeyondTrust uzaktan destek SSO 'Yu yapılandırın](#configure-beyondtrust-remote-support-sso)** .
    * **[Beyondtrust uzak destek testi kullanıcısına](#create-beyondtrust-remote-support-test-user)** , kullanıcının Azure AD gösterimine bağlı olan BeyondTrust uzak destek ile Ilgili olarak B. Simon 'ın bir karşılığı olacak şekilde bir bağlantı oluşturun.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Beyondtrust uzaktan destek** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<HOSTNAME>.bomgar.com/saml`

    b. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<HOSTNAME>.bomgar.com`

    c. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri öğreticide ilerleyen kısımlarında bulabilirsiniz.

1. BeyondTrust uzaktan destek uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdaki ' a ek olarak, BeyondTrust uzaktan destek uygulaması, daha fazla özniteliğin aşağıda gösterilen SAML yanıtına geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Ad |  Kaynak özniteliği|
    | ---------------| ----------|
    | givenName | Kullanıcı. |
    | EmailAddress | Kullanıcı. Mail |
    | Ad | User. UserPrincipalName |
    | Kullanıcı adı | User. UserPrincipalName |
    | Gruplar | Kullanıcı. gruplar |
    | Benzersiz kullanıcı tanımlayıcısı | User. UserPrincipalName |

    > [!NOTE]
    > BeyondTrust uzaktan destek uygulaması için Azure AD grupları atarken, ' talepte döndürülen gruplar ' seçeneğinde, None iken SecurityGroup olarak değiştirilmesi gerekir. Gruplar, nesne kimlikleri olarak uygulamaya aktarılır. Azure AD grubunun nesne KIMLIĞI, Azure Active Directory arabirimindeki Özellikler denetlenerek bulunabilir. Bu işlem, Azure AD gruplarının doğru grup ilkelerine başvurması ve atanması için gerekli olacaktır.

1. Benzersiz kullanıcı tanımlayıcısı ayarlanırken, bu değerin NameID-Format: **persistent**olarak ayarlanması gerekir. Bu, izinleri için kullanıcıyı doğru bir şekilde tanımlamak ve doğru grup ilkeleriyle ilişkilendirmek üzere kalıcı bir tanımlayıcı olmasını gerektiririz. Benzersiz kullanıcı tanımlayıcı değerini düzenlemek için **Kullanıcı öznitelikleri & talepler** iletişim kutusunu açmak için Düzenle simgesine tıklayın.

1. **Talebi Yönet** bölümünde **ad tanımlayıcı biçimini Seç** ' e tıklayın ve değeri **kalıcı** olarak ayarlayın ve **Kaydet**' e tıklayın.

    ![Kullanıcı öznitelikleri ve talepler](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **BeyondTrust uzaktan desteğe ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’ tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, BeyondTrust uzak destek 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Beyondtrust uzak desteği**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-beyondtrust-remote-support-sso"></a>BeyondTrust uzaktan destek SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir yönetici olarak BeyondTrust uzaktan destek ' te oturum açın.

1. **Durum** menüsüne tıklayın ve **tanımlayıcıyı**kopyalayın, URL 'YI **yanıtlayın** ve **url 'ye oturum açın** ve bu değerleri Azure Portal **temel SAML yapılandırması** bölümünde kullanın.

    ![BeyondTrust uzaktan desteğini yapılandırma](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. **Support.example.com** 'in gerecinizin birincil ana bilgisayar adı olduğu ve yönetici kimlik bilgilerinizi kullanarak kimlik doğrulayan `https://support.example.com/login` BeyondTrust Remote Support/Login arabirimine gidin.

1. **Kullanıcılar & güvenlik** > **güvenlik sağlayıcıları**' na gidin.

1. Açılan menüden **SAML** ' yi seçin ve **sağlayıcı oluştur** düğmesine tıklayın.

1. Kimlik sağlayıcısı ayarları bölümünde, kimlik sağlayıcısı meta verilerini karşıya yükleme seçeneği vardır. Azure portal indirdiğiniz meta veri XML dosyasını bulun ve **karşıya yükle** düğmesine tıklayın. **VARLıK kimliği**, **Çoklu oturum açma hizmeti URL 'si** ve sertifika otomatik olarak karşıya yüklenecek ve **protokol bağlamasının** **http post**olarak değiştirilmesi gerekir. Aşağıdaki ekran görüntüsüne bakın:

    ![BeyondTrust uzaktan desteğini yapılandırma](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>BeyondTrust uzaktan destek testi kullanıcısı oluşturma

Kullanıcı sağlama ayarlarını burada yapılandıracağız. Bu bölümde kullanılan değerlere, Azure portal **Kullanıcı öznitelikleri & talepleri** bölümünden başvurulur. Bu değeri, oluşturma sırasında zaten içeri aktarılmış olan varsayılan değerler olarak yapılandırdık, ancak gerekirse değer özelleştirilebilir.

![Kullanıcı oluşturuluyor](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> Bu uygulama için gruplar ve e-posta özniteliği gerekli değildir. Azure AD grupları 'nı kullandıysanız ve izinler için BeyondTrust uzak destek grubu Ilkelerine atarken, grubun nesne KIMLIĞININ Azure portal özellikleri aracılığıyla başvurulması ve ' kullanılabilir gruplar ' bölümüne yerleştirilmesi gerekir. Bu işlem tamamlandıktan sonra, nesne KIMLIĞI/AD grubu artık izinler için bir Grup ilkesine atanmak üzere kullanılabilir.

![Kullanıcı oluşturuluyor](./media/bomgarremotesupport-tutorial/config-user2.png)

![Kullanıcı oluşturuluyor](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> Alternatif olarak, SAML2 güvenlik sağlayıcısı üzerinde varsayılan bir grup ilkesi ayarlanabilir. Bu seçeneği tanımlayarak, bu, SAML aracılığıyla kimlik doğrulayan tüm kullanıcıları Grup İlkesi içinde belirtilen izinler olarak atar. Genel Üyeler ilkesi, kimlik doğrulamasını test etmek ve doğru ilkelere Kullanıcı atamak için kullanılabilen sınırlı izinlerle, BeyondTrust uzak destek/ayrıcalıklı uzaktan erişim içinde bulunur. Kullanıcılar, ilk başarılı kimlik doğrulama girişimine kadar güvenlik & > Kullanıcı aracılığıyla SAML2 Users listesine doldurmaz. Grup ilkeleriyle ilgili ek bilgiler aşağıdaki bağlantıda bulunabilir: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde BeyondTrust uzak destek kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız BeyondTrust uzak desteğiniz için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile birlikte uzak destek için BeyondTrust 'ı deneyin](https://aad.portal.azure.com/)
