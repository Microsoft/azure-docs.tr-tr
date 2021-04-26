---
title: 'Öğretici: iProva ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve iProva arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 717696053a742abae6756655a15416ac81221144
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459758"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Öğretici: iProva ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, iProva 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. İProva 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de iProva 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla iProva otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* iProva çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* iProva **SP** tarafından başlatılan SSO 'yu destekler

* İProva 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>Galeriden iProva ekleme

İProva tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden iProva yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **iProva** yazın.
1. Sonuçlar panelinden **iProva** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>İProva için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu iProva ile yapılandırın ve test edin. SSO 'nun çalışması için, iProva içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu iProva ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Sonraki adımlar için hazırlık olarak **[iProva adresinden yapılandırma bilgilerini alın](#retrieve-configuration-information-from-iprova)** .
1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Kullanıcının Azure AD gösterimine bağlı olan iProva 'de B. Simon 'ya karşılık gelen bir **[iProva test kullanıcısı oluşturun](#create-iprova-test-user)** .
1. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[IPROVA SSO 'Yu yapılandırın](#configure-iprova-sso)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="retrieve-configuration-information-from-iprova"></a>İProva adresinden yapılandırma bilgilerini al

Bu bölümde, Azure AD çoklu oturum açma 'yı yapılandırmak için iProva adresinden bilgi alırsınız.

1. Bir Web tarayıcısı açın ve aşağıdaki URL modelini kullanarak iProva içindeki **SAML2 Info** sayfasına gidin:

    ```https
    https://SUBDOMAIN.iprova.nl/saml2info
    https://SUBDOMAIN.iprova.be/saml2info
    ```

    ![İProva SAML2 Info sayfasını görüntüleme](media/iprova-tutorial/iprova-saml2-info.png)

1. Başka bir tarayıcı sekmesindeki sonraki adımlarla devam ederken tarayıcı sekmesini açık bırakın.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **iProva** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. Oturum açma **URL 'si** kutusunu, **iProva SAML2 Info** sayfasındaki etiket **oturum açma URL 'sinin** arkasında görüntülenen değerle doldurur. Bu sayfa hala diğer tarayıcı sekmesinizdeki açıktır.

    b. **Tanımlayıcı** kutusunu, **iProva SAML2 Info** sayfasında **EntityId** etiketinin arkasında görüntülenen değerle doldurur. Bu sayfa hala diğer tarayıcı sekmesinizdeki açıktır.

    c. **Reply-URL** kutusunu, **iProva SAML2 Info** sayfasında etiket **yanıt URL 'sinin** arkasında görüntülenen değerle doldurur. Bu sayfa hala diğer tarayıcı sekmesinizdeki açıktır.

1. iProva uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. İProva uygulaması, yukarıdakine ek olarak, aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği| Ad Alanı  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

## <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, iProva 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **iProva**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="create-iprova-test-user"></a>İProva test kullanıcısı oluştur

1. **Yönetici** hesabını kullanarak iProva 'de oturum açın.

2. **Git** menüsünü açın.

3. **Uygulama yönetimi**' ni seçin.

4. Kullanıcılar **ve Kullanıcı grupları** panelinde **Kullanıcılar** ' ı seçin.

5. **Add (Ekle)** seçeneğini belirleyin.

6. Kullanıcı **adı** kutusuna, gibi kullanıcının Kullanıcı adını girin `B.Simon@contoso.com` .

7. **Tam ad** kutusuna **B. Simon** gibi bir kullanıcının tam adını girin.

8. **Parola yok (çoklu oturum açma kullan)** seçeneğini belirleyin.

9. **E-posta adresi** kutusuna, gibi kullanıcının e-posta adresini girin `B.Simon@contoso.com` .

10. Sayfanın sonuna kadar aşağı kaydırın ve **son**' u seçin.

## <a name="configure-iprova-sso"></a>İProva SSO 'yu yapılandırma

1. **Yönetici** hesabını kullanarak iProva 'de oturum açın.

2. **Git** menüsünü açın.

3. **Uygulama yönetimi**' ni seçin.

4. **Sistem ayarları** panelinde **genel** ' i seçin.

5. **Düzenle**'yi seçin.

6. **Access Control**'a gidin.

    ![iProva erişim denetimi ayarları](media/iprova-tutorial/iprova-accesscontrol.png)

7. **Kullanıcıların kendi ağ hesaplarıyla otomatik olarak oturum açtığı** ayarı bulun ve bunu **Evet, SAML aracılığıyla kimlik doğrulaması** ile değiştirin. Ek seçenekler artık görüntülenir.

8. **Ayarla** seçeneğini belirleyin.

9. **İleri**’yi seçin.

10. iProva Federasyon verilerini bir URL 'den indirmek veya bir dosyadan karşıya yüklemek isteyip istemediğinizi sorar. **URL** 'yi seçin seçeneğini belirleyin.

    ![Azure AD meta verilerini indirin](media/iprova-tutorial/iprova-download-metadata.png)

11. "Azure AD çoklu oturum açmayı yapılandırma" bölümünün son adımında kaydettiğiniz meta veri URL 'sini yapıştırın.

12. Meta verileri Azure AD 'den indirmek için ok şeklinde düğmesini seçin.

13. İndirme tamamlandığında, onay iletisi **geçerli Federasyon verileri dosyası indirilir** .

14. **İleri**’yi seçin.

15. Şimdilik **test oturum açma** seçeneğini atlayın ve **İleri**' yi seçin.

16. **Kullanılacak talep** açılan kutusunda **deposundaki kullanıcıların windowsaccountname**' yi seçin.

17. **Son**'u seçin.

18. Şimdi **Genel Ayarları Düzenle** ekranına geri dönersiniz. Sayfanın alt kısmına gidin ve yapılandırmanızı kaydetmek için **Tamam** ' ı seçin.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde iProva kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız iProva için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile iProva deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve denetimlerle iProva koruma](/cloud-app-security/proxy-intro-aad)