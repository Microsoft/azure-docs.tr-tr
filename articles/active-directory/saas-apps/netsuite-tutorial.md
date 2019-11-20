---
title: 'Öğretici: NetSuite ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve NetSuite arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d578b5d08fecde733bb7b257057e480fef83c4e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754427"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Öğretici: Azure AD çoklu oturum açma (SSO) ile NetSuite tümleştirme

Bu öğreticide, NetSuite 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. NetSuite 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de NetSuite 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla NetSuite 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin, Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* NetSuite çoklu oturum açma (SSO) özellikli bir abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. 

NetSuite şunları destekler:

* IDP tarafından başlatılan SSO.
* JıT (tam zamanında) Kullanıcı hazırlama.
* [Otomatik Kullanıcı sağlama](NetSuite-provisioning-tutorial.md).

> [!NOTE]
> Bu uygulamanın tanımlayıcısı sabit bir dize değeri olduğundan, tek bir kiracıda yalnızca bir örnek yapılandırılabilir.

## <a name="add-netsuite-from-the-gallery"></a>Galeriden NetSuite ekleme

NetSuite 'in Azure AD ile tümleştirilmesini yapılandırmak için, aşağıdaki işlemleri yaparak Galeriden NetSuite 'i yönetilen SaaS uygulamaları listenize ekleyin:

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabıyla ya da kişisel bir Microsoft hesabı oturum açın.
1. Sol bölmede **Azure Active Directory** hizmeti seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Netsuite** yazın.
1. Sonuçlar bölmesinde **Netsuite**' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>NetSuite için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Netsuite ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve NetSuite içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu NetSuite ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .

    a. Kullanıcı B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .  
    b. Azure AD çoklu oturum açma özelliğini kullanmak için Kullanıcı B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [NetSuite SSO 'Yu yapılandırın](#configure-netsuite-sso) .
    * [Netsuite test kullanıcısını](#create-the-netsuite-test-user) , kullanıcının Azure AD gösterimine bağlı olan Netsuite 'e ait B. Simon kullanıcısına karşılık gelen bir şekilde oluşturun.
1. Yapılandırmanın çalıştığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için aşağıdakileri yapın:

1. [Azure Portal](https://portal.azure.com/), **Netsuite** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** bölmesinde **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarla** bölmesinde, **temel SAML yapılandırması**' nın yanındaki **Düzenle** ("kurşun kalem") simgesini seçin.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **yanıt URL 'si** metin kutusuna AŞAĞıDAKI biçimlerden birine bir URL yazın:

    ```
    https://<tenant-name>.NetSuite.com/saml2/acs
    https://<tenant-name>.na1.NetSuite.com/saml2/acs
    https://<tenant-name>.na2.NetSuite.com/saml2/acs
    https://<tenant-name>.sandbox.NetSuite.com/saml2/acs
    https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs
    https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    > [!NOTE]
    > Yukarıdaki URL 'Lerdeki değerler gerçek değildir. Bunları gerçek yanıt URL 'siyle güncelleştirin. Değeri almak için, [Netsuite istemci desteği ekibine](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen biçimlere de başvurabilirsiniz.

    NetSuite uygulaması, SAML onaylamaları 'nın belirli bir biçimde görüntülenmesini bekliyor. SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemeniz gerekir. 
    
1. **Kullanıcı öznitelikleri** bölmesini açmak için **Düzenle** ("kurşun kalem") simgesini seçin. Bölmesinde, aşağıdaki görüntüde gösterildiği gibi varsayılan özniteliklerin bir listesi görüntülenir: 

    ![Kullanıcı öznitelikleri bölmesi](common/edit-attribute.png)

    Bu özniteliklere ek olarak, NetSuite uygulaması SAML yanıtına birkaç özniteliğin daha geri geçirilmesini bekler. 

1. **Kullanıcı öznitelikleri** bölmesinde, **Kullanıcı talepleri**altında, aşağıdaki tabloda gösterilen SAML belirteci özniteliğini eklemek için aşağıdaki adımları gerçekleştirin:

    | Adı | Kaynak özniteliği | 
    | ---------------| --------------- |
    | account  | `account id` |

    a. **Kullanıcı taleplerini Yönet** bölmesini açmak için **yeni talep Ekle** ' yi seçin.

    b. **Ad** kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanı** kutusunu boş bırakın.

    d. **Kaynak** açılan listesinde **öznitelik**' i seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini girin.

    f. **Tamam**’ı seçin.

    g. **Kaydet**’i seçin.

    >[!NOTE]
    >Hesap özniteliğinin değeri gerçek değil. Bu değeri, Bu öğreticinin ilerleyen kısımlarında açıklandığı gibi güncelleştireceksiniz.

1. **SAML ile çoklu oturum açmayı ayarla** bölmesinde, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML**'i arayın.

1. Sertifikayı indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika Indirme bağlantısı](common/metadataxml.png)

1. **NetSuite 'ı ayarla** bölümünde, gereksiniminize bağlı olarak uygun URL 'Yi veya URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmede  > **kullanıcılar**  > **tüm kullanıcılar**' ı **Azure Active Directory** seçin.

1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

1. **Kullanıcı** özellikleri bölmesinde, şu adımları izleyin:

   a. **Ad** kutusuna **B. Simon**girin.  
   b. **Kullanıcı adı** kutusuna username@companydomain.extension girin (örneğin, B.Simon@contoso.com).  
   c. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.  
   d. **Oluştur**'u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, NetSuite 'e erişim izni vererek Kullanıcı B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Netsuite**' i seçin.
1. Genel Bakış bölmesinde **Yönet** bölümünü bulun ve ardından **Kullanıcılar ve gruplar** bağlantısını seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle** ' yi seçin ve sonra **atama Ekle** bölmesinde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcı Ekle" düğmesi](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** bölmesinde, **Kullanıcılar** açılan listesinde, **B. Simon**' ı seçin ve ardından ekranın altındaki **Seç** düğmesini seçin.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız şunları yapın:

   a. **Rol Seç** bölmesindeki aşağı açılan listede, Kullanıcı için uygun rolü seçin.  
   b. Ekranın alt kısmında **Seç** düğmesini seçin.
1. **Atama Ekle** bölmesinde **ata** düğmesini seçin.

## <a name="configure-netsuite-sso"></a>NetSuite SSO 'yu yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve NetSuite şirket sitenizde yönetici olarak oturum açın.

2. Üst gezinti çubuğunda, **Kurulum**' u ve ardından **Şirket**  > **özellikleri etkinleştir**' i seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Sayfanın ortasındaki araç çubuğunda **Suitecloud**' ı seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-suitecloud.png)

4. **Kimlik doğrulamasını Yönet**altında, Netsuite 'teki SAML çoklu oturum açma seçeneğini etkinleştirmek Için **SAML çoklu oturum açma** onay kutusunu seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Üst gezinti çubuğunda **Kurulum**' u seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-setup.png)

6. **Kurulum görevleri** listesinde, **tümleştirme**' i seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-integration.png)

7. **Kimlik doğrulamasını Yönet**altında **SAML çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-saml.png)

8. **SAML kurulum** bölmesindeki **Netsuite yapılandırması**altında aşağıdakileri yapın:

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. **Birincil kimlik doğrulama yöntemi** onay kutusunu seçin.

    b. **SAMLV2 Identity Provider meta verileri**altında, **IDP meta veri dosyasını karşıya yükle**' yi seçin ve ardından Azure Portal indirdiğiniz meta veri dosyasını karşıya yüklemek için **Araştır** ' ı seçin.

    c. **Gönder**' i seçin.

9. NetSuite üst gezinti çubuğunda **Kurulum**' **u seçin ve şirket  >  şirket** **bilgileri**' ni seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-com.png)

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-account-id.png)

    b. **Şirket bilgileri** bölmesindeki sağ SÜTUNDA **hesap kimliği** değerini kopyalayın.

    c. NetSuite hesabından kopyaladığınız **hesap kimliğini** Azure AD 'de **öznitelik değeri** kutusuna yapıştırın. 

10. Kullanıcıların NetSuite 'te çoklu oturum açma işlemi gerçekleştirebilmesi için önce NetSuite 'e uygun izinlerin atanması gerekir. Bu izinleri atamak için şunları yapın:

    a. Üst gezinti çubuğunda **Kurulum**' u seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-setup.png)

    b. Sol bölmede **Kullanıcılar/roller**' i seçin ve ardından **rolleri Yönet**' i seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. **Yeni rol**' i seçin.

    d. Yeni rol için bir **ad** girin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-new-role.png)

    e. **Kaydet**’i seçin.

    f. Üst gezinti çubuğunda **izinler**' i seçin. Ardından **Kurulum**' u seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-sso.png)

    g. **SAML çoklu oturum açma**' yı seçin ve ardından **Ekle**' yi seçin.

    h. **Kaydet**’i seçin.

    i. Üst gezinti çubuğunda **Kurulum**' u ve ardından **Kurulum Yöneticisi**' ni seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-setup.png)

    j. Sol bölmede **Kullanıcılar/roller**' i seçin ve ardından **Kullanıcıları Yönet**' i seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Bir test kullanıcısı seçin, **Düzenle**' yi seçin ve sonra **erişim** sekmesini seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-edit-user.png)

    girişindeki. **Roller** bölmesinde, oluşturduğunuz uygun rolü atayın.

    ![Çoklu oturum açmayı yapılandırma](./media/NetSuite-tutorial/ns-add-role.png)

    m. **Kaydet**’i seçin.

### <a name="create-the-netsuite-test-user"></a>NetSuite test kullanıcısını oluşturma

Bu bölümde, NetSuite 'te B. Simon adlı bir Kullanıcı oluşturulur. NetSuite, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı NetSuite 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde NetSuite kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız NetSuite 'te otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Azure AD ile NetSuite 'i deneyin](https://aad.portal.azure.com/)

