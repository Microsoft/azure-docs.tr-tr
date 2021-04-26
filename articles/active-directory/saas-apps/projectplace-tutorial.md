---
title: 'Öğretici: Projectplace ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Projectplace arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.openlocfilehash: 508a3ca02ec6c0475e78d16691f6597e1a389f97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511862"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Öğretici: Projectplace 'i Azure Active Directory ile tümleştirin

Bu öğreticide, Projectplace 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Projectplace 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Projectplace 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Projectplace 'e otomatik olarak kaydolmalarına imkan tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.
* Kullanıcılar, Projectplace 'de otomatik olarak sağlanabilir.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Projectplace çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Projectplace, **SP ve ıDP** tarafından başlatılan SSO 'yu destekler ve **yalnızca zamanında** Kullanıcı sağlamayı destekler.

## <a name="adding-projectplace-from-the-gallery"></a>Galeriden Projectplace ekleme

Projectplace 'in Azure AD 'ye tümleştirilmesini yapılandırmak için galerideki Projectplace 'i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Projectplace** yazın.
1. Sonuçlar panelinden **Projectplace** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Projectplace ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Projectplace içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Projectplace ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
2. Uygulama tarafında SSO ayarlarını yapılandırmak için **[Projectplace 'ı yapılandırın](#configure-projectplace)** .
3. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı olan Projectplace 'de B. Simon 'a karşılık gelen bir **[Projectplace test kullanıcısı oluşturun](#create-projectplace-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Projectplace** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız, uygulama önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulur. Kullanıcının **Kaydet** düğmesine tıklayarak yapılandırmayı kaydetmesi gerekir.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusuna bir URL yazın:`https://service.projectplace.com`

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, gereksiniminize göre **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve Not defteri 'ne kaydetmek için Kopyala **simgesi** ' ne tıklayın.

   ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

1. **Projectplace ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Projectplace yapılandırma

**Projectplace** tarafında çoklu oturum açmayı yapılandırmak için, Azure Portal kopyalanmış **uygulama Federasyon meta veri URL 'sini** [Projectplace destek ekibine](https://success.planview.com/Projectplace/Support)göndermeniz gerekir. Bu ekip, SAML SSO bağlantısının her iki tarafında da düzgün şekilde ayarlanmış olmasını sağlar.

>[!NOTE]
>Çoklu oturum açma yapılandırması [Projectplace destek ekibi](https://success.planview.com/Projectplace/Support)tarafından gerçekleştirilmelidir. Yapılandırma tamamlandıktan hemen sonra bir bildirim alırsınız. 

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B. Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Projectplace 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Projectplace**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-projectplace-test-user"></a>Projectplace test kullanıcısı oluşturma

>[!NOTE]
>Projectplace 'de sağlama etkinleştirilmişse, bu adımı atlayabilirsiniz. [Projectplace destek ekibinin](https://success.planview.com/Projectplace/Support) , ilk oturum açma Işlemi sırasında Projectplace içinde oluşturulduktan sonra, provizoning 'i etkinleştirmesini isteyebilirsiniz.

Azure AD kullanıcılarının Projectplace 'de oturum açmasını sağlamak için bunları Projectplace 'e eklemeniz gerekir. Bunları el ile eklemeniz gerekir.

**Bir kullanıcı hesabı oluşturmak için şu adımları uygulayın:**

1. **Projectplace** şirket sitenizde yönetici olarak oturum açın.

2. **Kişiler**' e gidin ve ardından **Üyeler**' i seçin:
   
    ![Kişiler ' e gidin ve ardından Üyeler ' i seçin.](./media/projectplace-tutorial/ic790228.png "People")

3. **Üye Ekle** öğesini seçin:
   
    ![Üye Ekle seçeneğini belirleyin](./media/projectplace-tutorial/ic790232.png "Üye Ekle")

4. **Üye Ekle** bölümünde aşağıdaki adımları uygulayın.
   
    ![Üye Ekle bölümü](./media/projectplace-tutorial/ic790233.png "Yeni Üyeler")
   
    1. **Yeni Üyeler** kutusunda, eklemek istediğiniz geçerli BIR Azure AD hesabının e-posta adresini girin.
   
    1. **Gönder**’i seçin.

   Hesabı etkin hale gelmeden önce onaylamak için bir bağlantı içeren bir e-posta, Azure AD hesap sahibine gönderilir.

>[!NOTE]
>Ayrıca, Azure AD Kullanıcı hesapları eklemek için Projectplace tarafından sunulan başka bir kullanıcı hesabı oluşturma aracını veya API 'YI de kullanabilirsiniz.


### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Projectplace kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Projectplace için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)