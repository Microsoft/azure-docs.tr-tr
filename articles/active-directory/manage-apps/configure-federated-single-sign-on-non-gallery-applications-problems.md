---
title: Galeri dışı bir uygulama için Federasyon çoklu oturum açmayı yapılandırma sorunu | Microsoft Docs
description: Azure AD uygulama galerisinde listelenmeyen özel SAML uygulamanızda federe çoklu oturum açmayı yapılandırırken karşılaşabileceğiniz yaygın sorunlardan bazılarını çözün
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
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 99c5e4d99f45e2a642a46f7dc070fb7512ff4d73
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422545"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Galeri dışı bir uygulama için Federasyon çoklu oturum açmayı yapılandırma sorunu

Bir uygulamayı yapılandırırken sorunla karşılaşırsanız. [Azure Active Directory Uygulama galerisinde olmayan uygulamalarda çoklu oturum açmayı yapılandırma](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery) makalesindeki tüm adımları izlemediğinizi doğrulayın.

## <a name="cant-add-another-instance-of-the-application"></a>Uygulamanın başka bir örneği eklenemiyor

Uygulamanın ikinci bir örneğini eklemek için şunları yapmanız gerekir:

-   İkinci örnek için benzersiz bir tanımlayıcı yapılandırın. İlk örnek için kullanılan tanımlayıcıyı yapılandıramazsınız.

-   İlk örnek için kullanılandan farklı bir sertifika yapılandırın.

Uygulama, önceki bir sürümü desteklemiyorsa, ikinci bir örnek yapılandıramazsınız.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>EntityId (Kullanıcı tanımlayıcısı) biçimini nereden ayarlayabilirim?

Azure AD 'nin Kullanıcı kimlik doğrulamasından sonra yanıtta uygulamaya gönderdiği EntityId (Kullanıcı tanımlayıcısı) biçimini seçemezsiniz.

Azure AD, NameID özniteliği (Kullanıcı tanımlayıcısı) için seçilen değere veya SAML AuthRequest içinde uygulama tarafından istenen biçime göre biçim seçer. Daha fazla bilgi için, Nameıdpolicy bölümünde [Çoklu oturum açma SAML Protokolü](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) makalesini ziyaret edin.

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Azure AD 'den uygulama meta verilerini veya sertifikayı nereden alabilirim?

Azure AD 'den uygulama meta verilerini veya sertifikasını indirmek için şu adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Çoklu oturum açma yapılandırdığınız uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. **SAML Imzalama sertifikası** bölümüne gidin ve ardından sütun değerini **İndir** ' e tıklayın. Uygulamanın çoklu oturum açmayı yapılandırma ihtiyacı olduğuna bağlı olarak, meta veri XML 'sini veya sertifikayı indirme seçeneğini görürsünüz.

Azure AD meta verileri almak için bir URL sağlamıyor. Meta veriler yalnızca bir XML dosyası olarak alınabilir.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Bir uygulamaya gönderilen SAML taleplerini özelleştirmeyi bilmiyorum

Uygulamanıza gönderilen SAML öznitelik taleplerini özelleştirmeyi öğrenmek için, daha fazla bilgi için [Azure Active Directory 'Da talep eşleme](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) konusuna bakın.

## <a name="next-steps"></a>Sonraki adımlar
[Uygulamaları Azure Active Directory ile yönetme](what-is-application-management.md)
