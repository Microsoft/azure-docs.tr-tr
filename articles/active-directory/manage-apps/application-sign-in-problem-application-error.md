---
title: Hata iletisi, oturum açtıktan sonra uygulama sayfasında görünür | Microsoft Docs
description: Uygulamayı bir hata iletisi döndürdüğünde Azure AD oturum açma ile ilgili sorunları gidermek nasıl.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e6a3d0b533dccc3c3111382b014907d5c026ab
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612656"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Kullanıcı oturum açtıktan sonra bir uygulama sayfası, hata iletisi gösterir.

Bu senaryoda, Azure Active Directory (Azure AD) kullanıcı oturum açtığında. Ancak, uygulama bir hata iletisi görüntüler ve kullanıcının son oturum açma akışını izin vermez. Uygulamayı Azure AD'ye verilen yanıt kabul etmedi sorunudur.

Uygulamayı Azure AD'ye yanıtından neden kabul etmedi birkaç olası nedeni vardır. Hata iletisi açıkça yanıttan eksik tanımlamazsa, aşağıdakileri deneyin:

-   Azure AD galeri uygulaması ise adımları izlediğinizi doğrulamak [Azure AD'de SAML tabanlı çoklu oturum açma uygulamaları için hata ayıklama](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Gibi bir araç kullanmak [Fiddler](https://www.telerik.com/fiddler) SAML isteği, yanıt ve belirteç yakalamak için.

-   Uygulama satıcısıyla SAML yanıta göndermek ve neler eksik isteyin.

## <a name="attributes-are-missing-from-the-saml-response"></a>SAML yanıttan öznitelikler eksik

Azure AD Yanıtta gönderilen Azure AD yapılandırmasında bir öznitelik eklemek için aşağıdaki adımları izleyin:

1. Açık [ **Azure portalında** ](https://portal.azure.com/) ve bir genel yönetici veya ortak yönetici olarak oturum

2. Sol taraftaki gezinti bölmesinin en üstünde seçin **tüm hizmetleri** Azure AD uzantısı'nı açın.

3. Tür **Azure Active Directory** filtre arama kutusuna ve ardından **Azure Active Directory**.

4. Seçin **kurumsal uygulamalar** Azure AD Gezinti bölmesinde.

5. Seçin **tüm uygulamaları** , uygulamaların bir listesini görüntülemek için.

   > [!NOTE]
   > İstediğiniz uygulamayı göremiyorsanız, kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini**. Ayarlama **Göster** seçeneği "Tüm uygulamalara."

6. Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra seçin **çoklu oturum açma** Gezinti bölmesinde.

8. İçinde **kullanıcı öznitelikleri** bölümünden **görünümü ve diğer tüm kullanıcı özniteliklerini düzenleyin**. Kullanıcılar oturum açtığında uygulamaya SAML belirtecindeki göndermek için hangi özniteliklerin değiştirebilirsiniz.

   Bir öznitelik eklemek için:

   1. Seçin **eklemek agentconfigutil**. Girin **adı**seçip **değer** aşağı açılan listeden.

   1.  **Kaydet**’i seçin. Yeni öznitelik tabloda görürsünüz.

9. Yapılandırmayı kaydedin.

   Uygulamaya kullanıcı oturum açtığında bir sonraki açışınızda Azure AD içinde SAML yanıtını yeni öznitelik gönderir.

## <a name="the-app-doesnt-identify-the-user"></a>Uygulama kullanıcıyı tanımlamak değil

SAML yanıtını bir rolü gibi bir özniteliği eksik olduğundan uygulamasında oturum açma başarısız olur. Veya farklı bir biçimi veya değeri uygulamanın beklediği için başarısız **Nameıd** (kullanıcı tanımlayıcı) özniteliği.

Kullanıyorsanız [Azure AD'ye otomatik kullanıcı hazırlama](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) oluşturmak için Bakımı ve uygulamada kullanıcıları kaldırmak, kullanıcının SaaS uygulamasına sağlandığını doğrulayın. Daha fazla bilgi için [Azure AD galeri uygulaması için hiçbir kullanıcı sağlanmıyor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Bir öznitelik için Azure AD Uygulama Yapılandırması Ekle

Kullanıcı tanımlayıcısı değeri değiştirmek için aşağıdaki adımları izleyin:

1. Açık [ **Azure portalında** ](https://portal.azure.com/) ve bir genel yönetici veya ortak yönetici olarak oturum

2. Seçin **tüm hizmetleri** Azure AD uzantısı'nı açmak için sol taraftaki gezinti bölmesinin üst.

3. Tür **Azure Active Directory** filtre arama kutusuna ve ardından **Azure Active Directory**.

4. Seçin **kurumsal uygulamalar** Azure AD Gezinti bölmesinde.

5. Seçin **tüm uygulamaları** , uygulamaların bir listesini görüntülemek için.

   > [!NOTE]
   > İstediğiniz uygulamayı göremiyorsanız, kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini**. Ayarlama **Göster** seçeneği "Tüm uygulamalara."

6. SSO için yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra seçin **çoklu oturum açma** Gezinti bölmesinde.

8. Altında **kullanıcı öznitelikleri**, kullanıcıyı benzersiz tanımlayıcı seçin **kullanıcı tanımlayıcısı** aşağı açılan listesi.

## <a name="change-the-nameid-format"></a>Nameıd biçimi değiştirme

Uygulama için başka bir biçime bekliyorsa **Nameıd** (kullanıcı tanımlayıcı) özniteliği, bkz: [düzenleme Nameıd](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) Nameıd biçimi değiştirmek için.

Azure AD biçimi seçen **Nameıd** özniteliği (kullanıcı tanımlayıcısı) tabanlı seçilen değer veya SAML AuthRequest uygulama tarafından istenen biçimi. Daha fazla bilgi için "NameIDPolicy" bölümüne bakın. [çoklu oturum açma SAML Protokolü](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Uygulama SAML yanıtını için farklı imza yöntemi bekliyor

SAML belirtecinin hangi bölümlerinin Azure AD tarafından dijital olarak imzalandığını değiştirmek için aşağıdaki adımları izleyin:

1. Açık [Azure portalında](https://portal.azure.com/) ve bir genel yönetici veya ortak yönetici olarak oturum

2. Seçin **tüm hizmetleri** Azure AD uzantısı'nı açmak için sol taraftaki gezinti bölmesinin üst.

3. Tür **Azure Active Directory** filtre arama kutusuna ve ardından **Azure Active Directory**.

4. Seçin **kurumsal uygulamalar** Azure AD Gezinti bölmesinde.

5. Seçin **tüm uygulamaları** , uygulamaların bir listesini görüntülemek için.

   > [!NOTE]
   > İstediğiniz uygulama görmüyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini**. Ayarlama **Göster** seçeneği "Tüm uygulamalara."

6. Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra seçin **çoklu oturum açma** Gezinti bölmesinde.

8. Altında **SAML imzalama sertifikası**seçin **gelişmiş sertifika imzalama ayarlarını göster**.

9. Seçin **imzalama seçeneği** Bu seçenekler arasından uygulamanın beklediği:

   * **SAML yanıtını imzala**
   * **SAML yanıtını ve onayını imzala**
   * **SAML onayını imzala**

   Uygulamaya kullanıcı oturum açtığında bir sonraki açışınızda, seçtiğiniz SAML yanıtını parçası Azure AD imzalar.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>SHA-1 imzalama algoritması uygulama bekliyor

Azure AD, varsayılan olarak, SAML belirteci en güvenli algoritmasını kullanarak imzalar. İmzalama algoritması için değiştirmemenizi öneririz *SHA-1* SHA-1 uygulama gerektirmediği sürece.

İmzalama Algoritması değiştirmek için aşağıdaki adımları izleyin:

1. Açık [Azure portalında](https://portal.azure.com/) ve bir genel yönetici veya ortak yönetici olarak oturum

2. Seçin **tüm hizmetleri** Azure AD uzantısı'nı açmak için sol taraftaki gezinti bölmesinin üst.

3. Tür **Azure Active Directory** filtre arama kutusuna ve ardından **Azure Active Directory**.

4. Seçin **kurumsal uygulamalar** Azure AD Gezinti bölmesinde.

5. Seçin **tüm uygulamaları** uygulamalarınızın bir listesini görüntülemek için.

   > [!NOTE]
   > İstediğiniz uygulama görmüyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini**. Ayarlama **Göster** seçeneği "Tüm uygulamalara."

6. Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra seçin **çoklu oturum açma** uygulamasının sol tarafındaki gezinti bölmesinden.

8. Altında **SAML imzalama sertifikası**seçin **gelişmiş sertifika imzalama ayarlarını göster**.

9. Seçin **SHA-1** olarak **imza algoritması**.

   Uygulamaya kullanıcı oturum açtığında bir sonraki açışınızda, SHA-1 algoritmasını kullanarak Azure AD SAML belirteci imzalar.

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD'de SAML tabanlı çoklu oturum açma uygulamaları için hata ayıklama](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
