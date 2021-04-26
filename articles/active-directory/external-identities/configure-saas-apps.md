---
title: B2B işbirliği için SaaS uygulamalarını Yapılandırma-Azure AD
description: SaaS uygulamalarını Azure Active Directory B2B işbirliği için yapılandırmayı ve kullanılabilir ek kaynakları görüntülemeyi öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0db5e609a5e6b7d74809810a50f86d72ae85675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90705519"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>B2B işbirliği için SaaS uygulamalarını yapılandırma

Azure Active Directory (Azure AD) B2B işbirliği, Azure AD ile tümleştirilen birçok uygulama ile birlikte çalışmaktadır. Bu bölümde, Azure AD B2B ile kullanılmak üzere bazı popüler SaaS uygulamalarını yapılandırmaya yönelik yönergeleri adım adım inceleyeceğiz.

Uygulamaya özgü yönergelere bakmadan önce, Thumb 'in bazı kuralları aşağıda verilmiştir:

* Çoğu uygulama için, Kullanıcı kurulumunun el ile gerçekleşmesi gerekir. Diğer bir deyişle, kullanıcıların da uygulamada el ile oluşturulması gerekir.

* Dropbox gibi otomatik kurulumu destekleyen uygulamalar için, uygulamalardan ayrı davetler oluşturulur. Kullanıcılar her daveti kabul ettiğinizden emin olmalıdır.

* Kullanıcı öznitelikleri ' nde, Konuk kullanıcılar içindeki karıştırılmış Kullanıcı profili diski (UPD) ile ilgili sorunları azaltmak için Kullanıcı **tanımlayıcısını** her zaman **Kullanıcı. Mail** olarak ayarlayın.


## <a name="dropbox-business"></a>Dropbox Business

Kullanıcıların kuruluş hesaplarını kullanarak oturum açmasını sağlamak için Dropbox Business 'ı Security Assertion Markup Language (SAML) kimlik sağlayıcısı olarak Azure AD 'yi kullanacak şekilde el ile yapılandırmanız gerekir. Dropbox Business bunu yapmak üzere yapılandırılmamışsa, kullanıcıların Azure AD 'yi kullanarak oturum açmalarına izin vermez ya da izin vermez.

1. Dropbox Business uygulamasını Azure AD 'ye eklemek için sol bölmedeki **Kurumsal uygulamalar** ' ı seçin ve ardından **Ekle**' ye tıklayın.

   ![Kurumsal uygulamalar sayfasında "Ekle" düğmesi](media/configure-saas-apps/add-dropbox.png)

2. **Uygulama Ekle** penceresinde, arama kutusuna **Dropbox** girin ve ardından sonuçlar listesinde **iş için Dropbox** ' u seçin.

   ![Uygulama Ekle sayfasında "Dropbox" araması yapın](media/configure-saas-apps/add-app-dialog.png)

3. **Çoklu oturum** açma sayfasında sol bölmedeki **Çoklu oturum açma** ' yı seçin ve Kullanıcı **tanımlayıcısı** kutusuna **Kullanıcı. Mail** yazın. (Varsayılan olarak UPN olarak ayarlanır.)

   ![Uygulama için çoklu oturum açmayı yapılandırma](media/configure-saas-apps/configure-app-sso.png)

4. Dropbox yapılandırması için kullanılacak sertifikayı indirmek için **Dropbox 'ı Yapılandır**' ı seçin ve ardından listeden **SAML çoklu oturum açma hizmeti URL 'sini** seçin.

   ![Dropbox yapılandırması için sertifika indiriliyor](media/configure-saas-apps/download-certificate.png)

5. **Çoklu oturum** açma sayfasından oturum açma URL 'Si ile Dropbox 'ta oturum açın.

   ![Dropbox oturum açma sayfasını gösteren ekran görüntüsü](media/configure-saas-apps/sign-in-to-dropbox.png)

6. Menüsünde, **Yönetici Konsolu**' nu seçin.

   ![Dropbox menüsündeki "Yönetici Konsolu" bağlantısı](media/configure-saas-apps/dropbox-menu.png)

7. **Kimlik doğrulama** iletişim kutusunda, **daha fazla**' yı seçin, sertifikayı karşıya yükleyin ve ardından **oturum aç URL** 'SI kutusuna SAML çoklu oturum açma URL 'sini girin.

   ![Daraltılan kimlik doğrulama iletişim kutusunda "daha fazla" bağlantısı](media/configure-saas-apps/dropbox-auth-01.png)

   ![Genişletilmiş kimlik doğrulaması iletişim kutusunda "oturum aç URL 'SI"](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Azure portal otomatik Kullanıcı Kurulumu yapılandırmak için, sol bölmede **sağlama** ' yı seçin, **sağlama modu** kutusunda **Otomatik** ' i seçin ve ardından **Yetkilendir**' i seçin.

   ![Azure portal otomatik Kullanıcı sağlamayı yapılandırma](media/configure-saas-apps/set-up-automatic-provisioning.png)

Konuk veya üye kullanıcıları Dropbox uygulamasında ayarladıktan sonra Dropbox 'tan ayrı bir davet alırlar. Dropbox çoklu oturum açma 'yı kullanmak için davetliler, içindeki bir bağlantıya tıklayarak daveti kabul etmelidir.

## <a name="box"></a>Box
SAML protokolünü temel alan Federasyonu kullanarak kullanıcıların Azure AD hesabıyla Box Konuk kullanıcıları için kimlik doğrulaması yapmasını sağlayabilirsiniz. Bu yordamda, meta verileri Box.com 'e yüklersiniz.

1. Kurumsal uygulamalardan Box uygulamasını ekleyin.

2. Çoklu oturum açmayı aşağıdaki sırada yapılandırın:

   ![Çoklu oturum açma yapılandırma ayarlarını gösteren ekran görüntüsü](media/configure-saas-apps/configure-box-sso.png)

   a. **Oturum açma URL 'si** kutusunda, oturum açma url 'Sinin Azure Portal kutusu için uygun şekilde ayarlandığından emin olun. Bu URL, Box.com kiracınızın URL 'sidir. Adlandırma kuralını izlemelidir *https://.box.com* .  
   **Tanımlayıcı** bu uygulama için uygulanmıyor, ancak yine de zorunlu bir alan olarak görünüyor.

   b. **Kullanıcı tanımlayıcısı** kutusuna **User. Mail** (Konuk hesapları için SSO) yazın.

   c. **SAML Imzalama sertifikası** altında **Yeni sertifika oluştur**' a tıklayın.

   d. Box.com kiracınızı bir kimlik sağlayıcısı olarak Azure AD kullanacak şekilde yapılandırmaya başlamak için, meta veri dosyasını indirin ve ardından yerel sürücünüze kaydedin.

   e. Meta veri dosyasını, sizin için çoklu oturum açmayı yapılandıran Box destek ekibine iletin.

3. Azure AD otomatik Kullanıcı Kurulumu için, sol bölmede **sağlama**' yı seçin ve ardından **Yetkilendir**' i seçin.

   ![Box 'a bağlanmak için Azure AD 'yi yetkilendirme](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Dropbox davetliler gibi Box davetliler, Box uygulamasının davetini kullanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD B2B işbirliği ile ilgili aşağıdaki makalelere bakın:

- [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
- [Dinamik Gruplar ve B2B işbirliği](use-dynamic-groups.md)
- [B2B işbirliği Kullanıcı talepleri eşleme](claims-mapping.md)
- [Dış paylaşım Microsoft 365](o365-external-user.md)

