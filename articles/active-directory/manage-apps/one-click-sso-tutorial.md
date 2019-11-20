---
title: Tek tıklamayla, çoklu oturum açma (SSO), Azure Marketi uygulamanızın yapılandırma | Microsoft Docs
description: Uygulamanızı Azure Market'ten için tek tıklamayla yapılandırması SSO için adımlar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a83d27af4fd783b95c53ef3a9169cb72bfc29d34
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872436"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Çoklu oturum açma tek tıklamayla uygulama yapılandırması

 Bu öğreticide, SAML-destek, Azure Market'teki Azure Active Directory (Azure AD) uygulamaları için oturum açma (SSO) yapılandırması tek tıklamayla, tek gerçekleştirmeyi öğrenin.

## <a name="introduction-to-one-click-sso"></a>Tek tıklamayla SSO giriş

Tek tıklamayla SSO özelliği, SAML protokolü destekleyen Azure Market uygulamaları için çoklu oturum açmayı yapılandırmak için tasarlanmıştır. Azure AD SSO yapılandırma sayfasında, bu seçenek otomatik olarak Azure AD'ye meta veriler uygulama tarafta yapılandırmanıza olanak tanır. Bu şekilde, el ile çok az çabayla SSO'yu hızlı bir şekilde ayarlayabilirsiniz.

## <a name="advantages-of-one-click-sso"></a>Tek tıklamayla SSO avantajları

- Uygulama tarafında el ile Kurulum gerektiren Azure Marketi uygulamalar hızlı SSO yapılandırması.
- Daha verimli ve doğru SSO yapılandırması.
- İş ortağı iletişim veya kurulumu için gereken destek. Uygulama SAML Yapılandırması kullanıcı Arabirimi sağlar.

## <a name="prerequisites"></a>Önkoşullar

- SSO ile yapılandırmak için uygulamanın etkin bir abonelik. Ayrıca yönetici kimlik bilgileri gerekir.
- **My Apps güvenli oturum açma uzantısı** tarayıcıda yüklü Microsoft gelen. Daha fazla bilgi için [erişim ve kullanım uygulamaları uygulamalarım portalında](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Tek tıklamayla SSO yapılandırma adımları

1. Azure Market'ten uygulama ekleyin.

2. Seçin **çoklu oturum açma**.

3. Seçin **çoklu oturum açmayı etkinleştirme**.

4. Zorunlu yapılandırma değerleri doldurmak **temel SAML yapılandırma** bölümü.

    > [!NOTE]
    > Uygulamayı yapılandırmak için gereken özel talep varsa, bunları tek tıklamayla SSO gerçekleştirmeden önce işler.

5. Azure Marketi uygulamanız için tek tıklamayla SSO özelliği varsa, ekranı görürsünüz. Yüklemeniz gerekebilir **My Apps güvenli oturum açma tarayıcı uzantısı** seçerek **uzantıyı yükleme**.

   ![My Apps güvenli oturum açma tarayıcı uzantısı yükleme](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Uzantı tarayıcıya ekledikten sonra seçin **Kurulum \<uygulama adı\>**. Uygulama Yönetim portalına yönlendirilirsiniz. sonra yönetici olarak oturum açın.

   ![Kurulum uygulama adı](./media/one-click-sso-tutorial/setup-sso.png)

7. Tarayıcı uzantısı, uygulamada SSO otomatik olarak yapılandırır. Seçerek onaylayın **Evet**.

   ![Otomatik olarak doldurulan verileri kaydetme](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Uygulamanız için SSO'yu yapılandırma adımları gerçekleştirmek için istemleri izleyerek ek adımlar gerekiyorsa.

8. Yapılandırma tamamlandıktan sonra seçin **Tamam** değişiklikleri kaydedin.

   ![Otomatik olarak doldurulan veri kaydetme](./media/one-click-sso-tutorial/save-data.png)

9. SSO ayarlarını başarılı bir şekilde yapılandırıldığını bildiren bir onay penceresi görüntüler.

   ![Yapılandırılmış SSO](./media/one-click-sso-tutorial/sso-configured.png)

10. Yapılandırma başarılı olduktan sonra uygulamadaki oturumunu kapatmasından ve Azure portalında döndürdü.

11. Seçebileceğiniz **Test** çoklu oturum açmayı test etmek için.

## <a name="additional-resources"></a>Ek kaynaklar

* [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [My Apps güvenli oturum açma tarayıcı uzantısı nedir?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
