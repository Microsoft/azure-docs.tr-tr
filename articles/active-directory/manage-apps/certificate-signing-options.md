---
title: Önceden tümleştirilmiş Azure AD uygulamaları için gelişmiş SAML belirteci sertifika imzalama seçenekleri | Microsoft Docs
description: Azure Active Directory 'de önceden tümleştirilmiş uygulamalar için SAML belirtecinde gelişmiş sertifika imzalama seçeneklerini nasıl kullanacağınızı öğrenin
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea99344399dbbbc17f0d7381e54ab68ae4bcc78f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381272"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory 'daki Galeri uygulamaları için SAML belirtecinde gelişmiş sertifika imzalama seçenekleri

Bugün Azure Active Directory (Azure AD), Azure Active Directory Uygulama galerisinde binlerce önceden tümleştirilmiş uygulamayı destekler. 500 ' den fazla uygulama, [Netsuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) uygulaması gibi [Security assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2,0 protokolünü kullanarak çoklu oturum açmayı destekler. Bir müşteri, SAML kullanarak Azure AD aracılığıyla bir uygulamanın kimliğini doğruladığında, Azure AD uygulamaya bir belirteç gönderir (bir HTTP POST aracılığıyla). Daha sonra uygulama, Kullanıcı adı ve parola istemek yerine müşterinin oturum açması için belirteci doğrular ve kullanır. Bu SAML belirteçleri, Azure AD 'de ve belirli standart algoritmalarda oluşturulan benzersiz sertifikayla imzalanır.

Azure AD, Galeri uygulamaları için varsayılan ayarların bazılarını kullanır. Varsayılan değerler, uygulamanın gereksinimlerine göre ayarlanır.

Azure AD 'de sertifika imzalama seçeneklerini ve sertifika imzalama algoritmasını ayarlayabilirsiniz.

## <a name="certificate-signing-options"></a>Sertifika imzalama seçenekleri

Azure AD üç sertifika imzalama seçeneğini destekler:

* **SAML onaylama Işlemi imzalayın**. Bu varsayılan seçenek, Galeri uygulamalarının çoğu için ayarlanır. Bu seçeneği belirlerseniz, kimlik sağlayıcısı (IDP) olarak Azure AD, SAML onaylama ve sertifikayı uygulamanın [X. 509.440](https://wikipedia.org/wiki/X.509) sertifikasıyla imzalar.

* **SAML yanıtını imzala**. Bu seçeneği belirlerseniz, IDP olarak Azure AD, SAML yanıtını uygulamanın X. 509.440 sertifikasıyla imzalar.

* **SAML yanıtı ve onaylama imzası**. Bu seçeneği belirlerseniz, IDP olarak Azure AD, tüm SAML belirtecini uygulamanın X. 509.440 sertifikasıyla imzalar.

## <a name="certificate-signing-algorithms"></a>Sertifika imzalama algoritmaları

Azure AD, SAML Yanıtını imzalamak için iki imzalama algoritmasını veya güvenli karma algoritmaları (Swith) destekler:

* **SHA-256**. Azure AD, SAML Yanıtını imzalamak için bu varsayılan algoritmayı kullanır. Bu en yeni algoritmadır ve SHA-1 ' den daha güvenlidir. Uygulamaların çoğu SHA-256 algoritmasını destekler. Bir uygulama imzalama algoritması olarak yalnızca SHA-1 ' i destekliyorsa, bunu değiştirebilirsiniz. Aksi takdirde, SAML Yanıtını imzalamak için SHA-256 algoritmasını kullanmanızı öneririz.

* **SHA-1**. Bu algoritma daha eski ve SHA-256 ' den daha az güvenli olarak değerlendirilir. Bir uygulama yalnızca bu imzalama algoritmasını destekliyorsa, **Imzalama algoritması** açılan listesinden bu seçeneği belirleyebilirsiniz. Ardından Azure AD, SAML yanıtını SHA-1 algoritmasıyla imzalar.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Sertifika imzalama seçeneklerini ve imzalama algoritmasını Değiştir

Bir uygulamanın SAML sertifika imzalama seçeneklerini ve sertifika imzalama algoritmasını değiştirmek için, söz konusu uygulamayı seçin:

1. [Azure Active Directory portalında](https://aad.portal.azure.com/)hesabınızda oturum açın. **Azure Active Directory Yönetim Merkezi** sayfası görüntülenir.
1. Sol bölmede **Kurumsal uygulamalar**’ı seçin. Hesabınızdaki kurumsal uygulamaların bir listesi görüntülenir.
1. Bir uygulama seçin. Uygulama için bir genel bakış sayfası görüntülenir.

   ![Örnek: Uygulamaya genel bakış sayfası](./media/certificate-signing-options/application-overview-page.png)

Ardından, bu uygulama için SAML belirtecindeki sertifika imzalama seçeneklerini değiştirin:

1. Uygulamaya Genel Bakış sayfasının sol bölmesinde, **Çoklu oturum açma**' yı seçin.
1. **SAML-Preview Ile çoklu oturum açmayı ayarla** sayfası görünürse, 5. adıma gidin.
1. **Çoklu oturum açma yöntemi seç** sayfası görünmezse, bu sayfayı göstermek için **Çoklu oturum açma modlarını Değiştir** ' i seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında, varsa **SAML** ' yi seçin. ( **SAML** kullanılamıyorsa, uygulama SAML 'yi desteklemez ve bu yordamın ve makalenin geri kalanını yoksayabilirsiniz.)
1. **SAML-Preview Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** başlığını bulun ve **düzenleme** simgesini (bir kurşun kalem) seçin. **SAML Imzalama sertifikası** sayfası görüntülenir.

   ![Örnek: SAML imzalama sertifikası sayfası](./media/certificate-signing-options/saml-signing-page.png)

1. **Imzalama seçeneği** açılan LISTESINDE, **SAML yanıtı imzala**, **SAML onayını imzala**veya **SAML yanıtı ve onaylama**imzası ' nı seçin. Bu seçeneklerin açıklamaları [sertifika imzalama seçeneklerinde](#certificate-signing-options)Bu makalede daha önce görüntülenir.
1. **Imzalama algoritması** açılan listesinde, **SHA-1** veya **SHA-256**' ı seçin. Bu seçeneklerin açıklamaları, bu makalede daha önce [sertifika imzalama algoritmaları](#certificate-signing-algorithms) bölümünde görüntülenir.
1. Seçimlerinizden memnun kaldıysanız, yeni SAML imzalama sertifikası ayarlarını uygulamak için **Kaydet** ' i seçin. Aksi takdirde, değişiklikleri atmak için **X** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Active Directory Uygulama galerisinde olmayan uygulamalarda çoklu oturum açmayı yapılandırma](configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML tabanlı çoklu oturum açma sorunlarını giderme](../develop/howto-v1-debug-saml-sso-issues.md)
