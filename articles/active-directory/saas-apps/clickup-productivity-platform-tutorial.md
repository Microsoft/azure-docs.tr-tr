---
title: 'Öğretici: tıklama üretkenlik platformuyla tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve tıklama üretkenlik platformu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: jeedes
ms.openlocfilehash: 29bc02466825aa2ea2c1a9ece2826b1262293392
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285221"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Öğretici: tıklama üretkenlik platformuyla tümleştirme Azure Active Directory

Bu öğreticide, tıklama üretkenlik platformunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Tıklama üretkenlik platformunu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Tıklaup üretkenlik platformuna erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla üretkenlik platformunu tıklayana otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Üretkenlik platformu çoklu oturum açma (SSO) etkin aboneliğini tıklama.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Tıklama verimliliği platformu **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-clickup-productivity-platform-from-the-gallery"></a>Galeriden tıklama üretkenlik platformunu ekleyin

Tıklama üretkenlik platformunun tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden tıklama üretkenlik platformunu galerisinden yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **tıklama üretkenlik platformunu** yazın.
1. Sonuçlar panelinden **tıklama verimliliği platformunu** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-clickup-productivity-platform"></a>Tıklama üretkenlik platformu için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak tıklama üretkenlik platformuyla Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve tıklama üretkenlik platformunda ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Tıklaup üretkenlik platformuyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Tıklama üretkenlik platformu SSO 'Su yapılandırma](#configure-clickup-productivity-platform-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. **[Tıklama üretkenlik platformu test kullanıcısı oluşturun](#create-clickup-productivity-platform-test-user)** -kullanıcının Azure AD gösterimine bağlı olan tıklama verimliliği platformunda B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **tıklama üretkenlik platformu** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusuna URL 'yi yazın:`https://app.clickup.com/login/sso`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Tanımlayıcı değeri gerçek değil. Bu değeri, Bu öğreticinin ilerleyen kısımlarında açıklanan gerçek tanımlayıcı ile güncelleştirin.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

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

Bu bölümde, tıklama üretkenlik platformuna erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **tıklama üretkenlik platformu**' nu seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-clickup-productivity-platform-sso"></a>Tıklama üretkenlik platformu SSO 'yu yapılandırma

1. Farklı bir Web tarayıcı penceresinde, yönetici olarak tıklama üretkenlik platformu kiracınızda oturum açın.

2. **Kullanıcı profili**' ne tıklayın ve ardından **Ayarlar**' ı seçin.

    ![Ekran görüntüsünde, Ayarlar simgesi seçili olarak Tıklaup üretkenlik kiracısını gösterir.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Ekran görüntüsü ayarları gösterir.](./media/clickup-productivity-platform-tutorial/configure-1.png)

3. Tek Sign-On (SSO) sağlayıcısı altında **Microsoft**' u seçin.

    ![Ekran görüntüsünde Microsoft 'un seçtiği kimlik doğrulama bölmesi görüntülenir.](./media/clickup-productivity-platform-tutorial/configure-2.png)

4. **Microsoft Çoklu oturum açma 'Yı Yapılandır** sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, b varlığını kopyalayabileceğiniz ve Azure Federasyon meta veri U R L 'sini kaydedebileceğiniz Microsoft Çoklu oturum açma sayfasını yapılandırma ' yı gösterir.](./media/clickup-productivity-platform-tutorial/configure-3.png)

    a. Varlık KIMLIĞI değerini kopyalamak için **Kopyala** ' ya tıklayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırın.

    b. **Azure Federasyon meta veri URL 'si** metin kutusunda, Azure Portal kopyaladığınız uygulama Federasyon meta veri URL 'si değerini yapıştırın ve **Kaydet**' e tıklayın.

5. Kurulumu tamamladıktan sonra kurulumu tamamlayıp Microsoft hesabıyla kimlik doğrulaması **yapmak Için Microsoft Ile kimlik doğrulaması** ' na tıklayın.

    ![Ekran görüntüsünde, kurulum 'u tamamlamaya yönelik olarak Microsoft ile kimlik doğrulaması gösterilmektedir.](./media/clickup-productivity-platform-tutorial/configure-4.png)


### <a name="create-clickup-productivity-platform-test-user"></a>Tıklama üretkenlik platformu test kullanıcısı oluştur

1. Farklı bir Web tarayıcı penceresinde, yönetici olarak tıklama üretkenlik platformu kiracınızda oturum açın.

2. **Kullanıcı profili**' ne tıklayın ve ardından **kişiler**' i seçin.

    ![Ekran görüntüsü tıklama verimliliği kiracısını gösterir.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Ekran görüntüsü kişiler bağlantısının seçili olduğunu gösterir.](./media/clickup-productivity-platform-tutorial/user-1.png)

3. Metin kutusundaki kullanıcının e-posta adresini girin ve **davet et**' e tıklayın.

    ![Ekran görüntüsü, kişileri e-posta ile davet edebileceğiniz ekip kullanıcılarının ayarlarını gösterir.](./media/clickup-productivity-platform-tutorial/user-2.png)

    > [!NOTE]
    > Kullanıcı bildirimi alır ve hesabı etkinleştirmek için daveti kabul etmelidir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz tıklama üretkenlik platformu oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan tıklama üretkenlik platformu oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki tıklama üretkenlik platformu kutucuğuna tıkladığınızda bu, tıklama üretkenlik platformu oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

Tıklama üretkenlik platformunu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve ölçeğini koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).