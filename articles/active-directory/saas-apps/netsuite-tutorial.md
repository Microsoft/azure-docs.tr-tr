---
title: 'Öğretici: NetSuite ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve NetSuite arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: d99a19efcef0cae518d8d21d3371adaf37d32ff7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625489"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Öğretici: Azure AD çoklu oturum açma (SSO) ile NetSuite tümleştirme

Bu öğreticide, NetSuite 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. NetSuite 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de NetSuite 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla NetSuite 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin, Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* NetSuite çoklu oturum açma (SSO) özellikli bir abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. 

NetSuite şunları destekler:

* IDP tarafından başlatılan SSO.
* JıT (tam zamanında) Kullanıcı hazırlama.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı sabit bir dize değeri olduğundan, tek bir kiracıda yalnızca bir örnek yapılandırılabilir.

## <a name="add-netsuite-from-the-gallery"></a>Galeriden NetSuite ekleme

NetSuite 'in Azure AD ile tümleştirilmesini yapılandırmak için, aşağıdaki işlemleri yaparak Galeriden NetSuite 'i yönetilen SaaS uygulamaları listenize ekleyin:

1. Azure portal iş veya okul hesabıyla ya da kişisel bir Microsoft hesabı oturum açın.
1. Sol bölmede **Azure Active Directory** hizmeti seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Netsuite** yazın.
1. Sonuçlar bölmesinde **Netsuite**' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>NetSuite için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Netsuite ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve NetSuite içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu NetSuite ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
    * Kullanıcı B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .  
    * Azure AD çoklu oturum açma özelliğini kullanmak için Kullanıcı B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [NetSuite SSO 'Yu yapılandırın](#configure-netsuite-sso) .
    * [Netsuite test kullanıcısını](#create-the-netsuite-test-user) , kullanıcının Azure AD gösterimine bağlı olan Netsuite 'e ait B. Simon kullanıcısına karşılık gelen bir şekilde oluşturun.
1. Yapılandırmanın çalıştığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için aşağıdakileri yapın:

1. Azure portal, **Netsuite** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** bölmesinde **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarla** bölmesinde, **temel SAML yapılandırması**' nın yanındaki **Düzenle** ("kurşun kalem") simgesini seçin.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **yanıt URL 'si** metin kutusuna AŞAĞıDAKI biçimlerden birine bir URL yazın:

    ```https
    https://<Instance ID>.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.NetSuite.com/saml2/acs
    https://<Instance ID>.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    * Netsuite yapılandırma **<`Instance ID`>** bölümünde, Netsuite Yapılandırması altındaki 8. adımdaki Öğreticinin ilerleyen kısımlarında açıklanan değeri alacaksınız. Tam etki alanını (Bu durumda system.na0.netsuite.com gibi) bulacaksınız.

        ![Ekran görüntüsü, etki alanını alabileceğiniz SAML kurulum sayfasını gösterir.](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Yukarıdaki URL 'Lerdeki değerler gerçek değildir. Bunları gerçek yanıt URL 'siyle güncelleştirin. Değeri almak için, [Netsuite istemci desteği ekibine](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen biçimlere de başvurabilirsiniz.

1. NetSuite uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. NetSuite uygulaması, yukarıdakine ek olarak aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > Hesap özniteliğinin değeri gerçek değil. Bu değeri, Bu öğreticinin ilerleyen kısımlarında açıklandığı gibi güncelleştireceksiniz.

1. SAML ile çoklu oturum açmayı ayarlama sayfasında, SAML Imzalama sertifikası bölümünde, Federasyon meta verileri XML 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için Indir ' i seçin.

    ![Sertifika Indirme bağlantısı](common/metadataxml.png)

1. **NetSuite 'ı ayarla** bölümünde, gereksiniminize bağlı olarak uygun URL 'Yi veya URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure Portal sol bölmesinde, kullanıcıları **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.

1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

1. **Kullanıcı** özellikleri bölmesinde, şu adımları izleyin:

   a. **Ad** kutusuna **B. Simon** girin.  
   b. **Kullanıcı adı** kutusuna öğesini username@companydomain.extension (örneğin, B.Simon@contoso.com ) girin.  
   c. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.  
   d. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, NetSuite 'e erişim izni vererek Kullanıcı B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Netsuite**' i seçin.
1. Genel Bakış bölmesinde **Yönet** bölümünü bulun ve ardından **Kullanıcılar ve gruplar** bağlantısını seçin.
1. **Kullanıcı Ekle** ' yi seçin ve sonra **atama Ekle** bölmesinde **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** bölmesinde, **Kullanıcılar** açılan listesinde, **B. Simon**' ı seçin ve ardından ekranın altındaki **Seç** düğmesini seçin.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız şunları yapın:

   a. **Rol Seç** bölmesindeki aşağı açılan listede, Kullanıcı için uygun rolü seçin.  
   b. Ekranın alt kısmında **Seç** düğmesini seçin.
1. **Atama Ekle** bölmesinde **ata** düğmesini seçin.

## <a name="configure-netsuite-sso"></a>NetSuite SSO 'yu yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve NetSuite şirket sitenizde yönetici olarak oturum açın.

2. Üst gezinti çubuğunda, **Kurulum**' u seçin ve ardından **Şirket**  >  **Etkinleştir Özellikler**' i seçin.

    ![Ekran görüntüsü şirketten seçilen özellikleri etkinleştir ' i gösterir.](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Sayfanın ortasındaki araç çubuğunda **Suitecloud**' ı seçin.

    ![Ekran görüntüsünde SuiteCloud seçili gösterilmektedir.](./media/NetSuite-tutorial/ns-suitecloud.png)

4. **Kimlik doğrulamasını Yönet** altında, Netsuite 'teki SAML çoklu oturum açma seçeneğini etkinleştirmek Için **SAML çoklu oturum açma** onay kutusunu seçin.

    ![Ekran görüntüsünde, SAML çoklu oturum açma seçeneğini seçebileceğiniz kimlik doğrulamasını yönetme gösterilmektedir.](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Üst gezinti çubuğunda **Kurulum**' u seçin.

    ![Ekran görüntüsü NETSUITE gezinti çubuğundan seçilen kurulumu gösterir.](./media/NetSuite-tutorial/ns-setup.png)

6. **Kurulum görevleri** listesinde, **tümleştirme**' i seçin.

    ![Ekran görüntüsünde, kurulum GÖREVLERINDEN seçilen tümleştirme gösterilmektedir.](./media/NetSuite-tutorial/ns-integration.png)

7. **Kimlik doğrulamasını Yönet** altında **SAML çoklu oturum açma**' yı seçin.

    ![Ekran görüntüsü, kurulum GÖREVLERINDE tümleştirme öğesinden seçilen SAML çoklu oturum açmayı gösterir.](./media/NetSuite-tutorial/ns-saml.png)

8. **SAML kurulum** bölmesindeki **Netsuite yapılandırması** altında aşağıdakileri yapın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz SAML kurulumunu gösterir.](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. **Birincil kimlik doğrulama yöntemi** onay kutusunu seçin.

    b. **SAMLV2 Identity Provider meta verileri** altında, **IDP meta veri dosyasını karşıya yükle**' yi seçin ve ardından Azure Portal indirdiğiniz meta veri dosyasını karşıya yüklemek için **Araştır** ' ı seçin.

    c. **Gönder**’i seçin.

9. Netsuite üst gezinti çubuğunda **Kurulum**' u ve ardından **Şirket**  >  **Şirket bilgileri**' ni seçin.

    ![Ekran görüntüsü şirketten seçilen şirket bilgilerini gösterir.](./media/NetSuite-tutorial/ns-com.png)

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz bölmeyi gösterir.](./media/NetSuite-tutorial/ns-account-id.png)

    b. **Şirket bilgileri** bölmesindeki sağ SÜTUNDA **hesap kimliği** değerini kopyalayın.

    c. NetSuite hesabından kopyaladığınız **hesap kimliğini** Azure AD 'de **öznitelik değeri** kutusuna yapıştırın.

    ![Ekran görüntüsü hesap kimliği değerinin ekleneceğini gösterir](./media/netsuite-tutorial/attribute-value.png)

10. Kullanıcıların NetSuite 'te çoklu oturum açma işlemi gerçekleştirebilmesi için önce NetSuite 'e uygun izinlerin atanması gerekir. Bu izinleri atamak için şunları yapın:

    a. Üst gezinti çubuğunda **Kurulum**' u seçin.

    ![Ekran görüntüsü NETSUITE gezinti çubuğundan seçilen kurulumu gösterir.](./media/NetSuite-tutorial/ns-setup.png)

    b. Sol bölmede **Kullanıcılar/roller**' i seçin ve ardından **rolleri Yönet**' i seçin.

    ![Ekran görüntüsü rol yönetme bölmesini gösterir ve burada yeni rol seçebilirsiniz.](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. **Yeni rol**' i seçin.

    d. Yeni rol için bir **ad** girin.

    ![Ekran görüntüsü, rol için bir ad girebileceğiniz Kurulum Yöneticisini gösterir.](./media/NetSuite-tutorial/ns-new-role.png)

    e. **Kaydet**’i seçin.

    f. Üst gezinti çubuğunda **izinler**' i seçin. Ardından **Kurulum**' u seçin.

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Kurulum sekmesini gösterir.](./media/NetSuite-tutorial/ns-sso.png)

    örneğin: **SAML çoklu oturum açma**' yı seçin ve ardından **Ekle**' yi seçin.

    h. **Kaydet**’i seçin.

    i. Üst gezinti çubuğunda **Kurulum**' u ve ardından **Kurulum Yöneticisi**' ni seçin.

    ![Ekran görüntüsü NETSUITE gezinti çubuğundan seçilen kurulumu gösterir.](./media/NetSuite-tutorial/ns-setup.png)

    j. Sol bölmede **Kullanıcılar/roller**' i seçin ve ardından **Kullanıcıları Yönet**' i seçin.

    ![Ekran görüntüsü, Suite demo ekibini seçebileceğiniz kullanıcıları Yönet bölmesini gösterir.](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Bir test kullanıcısı seçin, **Düzenle**' yi seçin ve sonra **erişim** sekmesini seçin.

    ![Ekran görüntüsü kullanıcıları Yönet bölmesini gösterir ve burada Düzenle seçeneğini belirleyebilirsiniz.](./media/NetSuite-tutorial/ns-edit-user.png)

    l. **Roller** bölmesinde, oluşturduğunuz uygun rolü atayın.

    ![Ekran görüntüsü, çalışan tarafından seçilen yöneticiyi gösterir.](./media/NetSuite-tutorial/ns-add-role.png)

    m. **Kaydet**’i seçin.

### <a name="create-the-netsuite-test-user"></a>NetSuite test kullanıcısını oluşturma

Bu bölümde, NetSuite 'te B. Simon adlı bir Kullanıcı oluşturulur. NetSuite, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı NetSuite 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

- Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız NetSuite 'te otomatik olarak oturum açmış olmanız gerekir

- Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki NetSuite kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız NetSuite 'e otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

NetSuite 'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimlerini zorunlu kılabilirsiniz. Oturum denetimleri koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)