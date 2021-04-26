---
title: 'Öğretici: splan ziyaretçisi ile Azure Active Directory çoklu oturum açmayı (SSO) tümleştirin | Microsoft Docs'
description: Azure Active Directory ile splan ziyaretçisi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: 20f49c174dde90bc7f1a9b34f3dea3132e9b177e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644706"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Öğretici: splan ziyaretçisi ile Azure Active Directory çoklu oturum açmayı (SSO) tümleştirin

Bu öğreticide, splan ziyaretçisini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Splan ziyaretçisini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Splan ziyaretçiye kimlerin erişebileceğini denetlemek için Azure AD 'yi kullanın.
* Kullanıcıların Azure AD hesaplarıyla splan ziyaretçisinden otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin, Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için gerekli olanlar:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Bir splan ziyaretçisi çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edeceksiniz.

Splan ziyaretçisi IDP tarafından başlatılan SSO 'yu destekler.

## <a name="add-splan-visitor-from-the-gallery"></a>Galeriden splan ziyaretçisi ekleme

Splan ziyaretçisinin Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden splan ziyaretçisini yönetilen SaaS uygulamaları listenize ekleyin.

1. İş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol bölmede **Azure Active Directory** hizmeti seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **splan ziyaretçisi** girin.
1. Sonuçlar panelinden **splan ziyaretçisi** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Splan ziyaretçisi için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu splan ziyaretçisinden yapılandırın ve test edin. SSO 'nun çalışması için, splan ziyaretçisinden bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu splan ziyaretçisinden yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. Azure AD 'de oturum açmayı test eden **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** B. Simon.
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Splan ziyaretçisinden çoklu oturum açma ayarlarını yapılandırmak için **[splan ZIYARETÇI SSO 'Yu yapılandırın](#configure-splan-visitor-sso)** .
    1. Kullanıcı Azure AD gösterimine bağlı olan splan ziyaretçisinden B. Simon 'ya karşılık gelen bir **[splan ziyaretçi test kullanıcısı oluşturun](#create-a-splan-visitor-test-user)** .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. Azure portal, **splan ziyaretçi** uygulaması tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için **Düzenle/kalem** simgesini seçin.

   ![Temel SAML yapılandırması için düzenleme/kalem simgesini vurgulayan ekran görüntüsü.](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulur. Yapılandırmayı kaydetmek için **Kaydet** düğmesini seçin.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML**'i bulun. Sertifikayı indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Federasyon meta verileri XML indirme bağlantısını vurgulayan ekran görüntüsü.](common/metadataxml.png)

1. **Splan ziyaretçisini ayarla** bölümünde, gereksiniminize göre uygun URL 'Yi veya URL 'leri kopyalayın.

    ![Yapılandırma URL 'Leri bölümünü vurgulayan ekran görüntüsü.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal B. Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmede **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına **B. Simon** girin.  
   1. **Kullanıcı adı** alanına Kullanıcı adınızı _username@companydomain.extension_ biçiminde girin. Örneğin, girin **B.Simon@contoso.com** .
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, splan ziyaretçiye erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, uygulama genel bakış ' ı açmak için **splan ziyaretçisi** ' nı seçin.
1. **Yönet** bölümünü bulun ve ardından **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinden **B. Simon** öğesini seçin ve ardından ekranın alt kısmında **Seç** ' e tıklayın.
1. Kullanıcıya bir rol atanırsa, **rol seçin** açılır menüsünde bunu seçin. Bu uygulama için ayarlanmış bir rol yoksa, **varsayılan erişim** rolünü seçili bırakın.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-splan-visitor-sso"></a>Splan ziyaretçi SSO 'yu yapılandırma

Splan ziyaretçisi ile çoklu oturum açmayı yapılandırmak için, indirdiğiniz **Federasyon meta VERI XML** 'sini ve Azure Portal kopyalanan URL 'Leri [splan ziyaretçi destek ekibine](mailto:support@splan.com)gönderin. Bu, SAML SSO bağlantısının her iki tarafında da düzgün şekilde ayarlanmış olmasını sağlar.

### <a name="create-a-splan-visitor-test-user"></a>Bir splan ziyaretçi test kullanıcısı oluşturma

Splan ziyaretçisinden **Britta Simon** adlı bir test kullanıcısı oluşturun. Kullanıcı splan ziyaretçiye eklemek için [splan ziyaretçi desteği ekibi](mailto:support@splan.com) ile çalışın. Çoklu oturum açma kullanmadan önce Kullanıcı oluşturmanız ve etkinleştirmeniz gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerden biriyle test edin:

* **Azure Portal**: SSO 'Yu ayarladığınız splan ziyaretçisinden otomatik olarak oturum açmak için **Bu uygulamayı test** et ' i seçin.
* **Microsoft uygulamalarım portalı**: SSO 'Yu ayarladığınız splan ziyaretçisinden otomatik olarak oturum açmak Için **splan ziyaretçi** kutucuğunu seçin. Uygulamalarım portalı hakkında daha fazla bilgi için bkz. [uygulamalarım portalından oturum açma ve uygulamaları başlatma](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Splan ziyaretçisini yapılandırdıktan sonra, [Microsoft Cloud App Security ' de oturum denetimlerini nasıl zorlayabileceğinizi öğrenebilirsiniz](/cloud-app-security/proxy-deployment-any-app). Oturum denetimleri, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanma ve zaman denetimini korumanıza yardımcı olur. Oturum denetimleri koşullu erişimden genişletilir.