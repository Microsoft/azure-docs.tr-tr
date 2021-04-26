---
title: Azure AD uygulamaları ekleme ve yönetme-Azure Marketi
description: Iş Ortağı Merkezi 'nde ticari Market programı için Azure AD uygulamaları ekleme ve yönetme hakkında bilgi edinin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: be527647466ad76455585e16baabb26e39e42193
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108485"
---
# <a name="add-and-manage-azure-ad-applications"></a>Azure AD uygulamaları ekleme ve yönetme

**Uygun roller**

- Sahip
- Yönetici

Şirketinizin Azure Active Directory (Azure AD) parçası olan uygulamaların veya hizmetlerin Iş Ortağı Merkezi hesabınıza erişmesine izin verebilirsiniz.

## <a name="add-existing-azure-ad-applications"></a>Mevcut Azure AD uygulamalarını ekleme

Şirketinizin Azure Active Directory zaten var olan uygulamaları eklemek için:

1. **Kullanıcılar** sayfasından ( **Hesap ayarları** altında) **Azure AD uygulamaları Ekle**' yi seçin.
1. Görüntülenen listeden bir veya daha fazla Azure AD uygulaması seçin. Belirli Azure AD uygulamalarını aramak için arama kutusunu kullanabilirsiniz. Iş Ortağı Merkezi hesabınıza eklemek için birden fazla Azure AD uygulaması seçerseniz, bunlara aynı rol veya özel izin kümesi atamanız gerekir. Farklı rollere/izinlere sahip birden çok Azure AD uygulaması eklemek için, bu adımları her bir rol veya özel izin kümesi için tekrarlayın.
1. Azure AD uygulamaları ' nı seçtikten sonra **Seçili Ekle**' yi seçin.
1. **Roller** bölümünde, SEÇILI Azure AD uygulamaları için rol (ler) veya özelleştirilmiş izinleri belirtin.
1. **Kaydet**’i seçin.

## <a name="add-new-azure-ad-applications"></a>Yeni Azure AD uygulamaları ekleme

Yeni bir Azure AD uygulama hesabına Iş ortağı merkezi erişimi vermek istiyorsanız, **Kullanıcılar** bölümünde bir tane oluşturabilirsiniz. Bu işlem, yalnızca Iş Ortağı Merkezi hesabınızda değil, şirketinizin iş hesabınızda (Azure AD kiracısı) yeni bir hesap oluşturur. Birincil olarak bu Azure AD uygulamasını Iş ortağı merkezi kimlik doğrulaması için kullanıyorsanız ve kullanıcılara doğrudan erişim gereksinimi yoksa, bu değerler dizininizde başka bir Azure AD uygulaması tarafından kullanılmıyorsa, **yanıt URL** 'Si ve **uygulama kimliği URI 'si** için geçerli bir adres girebilirsiniz.

1. **Kullanıcılar** sayfasından ( **Hesap ayarları** altında) **Azure AD uygulamaları Ekle**' yi seçin.
1. Sonraki sayfada, **yeni Azure AD uygulaması**' nı seçin.
1. Yeni Azure AD uygulamasının **yanıt URL 'sini** girin. Bu URL, kullanıcıların oturum açmak ve Azure AD uygulamanızı kullanabilmesi (bazen uygulama URL 'SI veya Sign-On URL 'SI olarak da bilinir). *Yanıt URL 'si* 256 karakterden uzun olamaz ve dizininiz içinde benzersiz olmalıdır.
1. Yeni Azure AD uygulaması için **uygulama KIMLIĞI URI** 'sini girin. Bu, Azure AD 'ye çoklu oturum açma isteği gönderildiğinde sunulan Azure AD uygulaması için mantıksal bir tanıtıcıdır. Dizininizdeki her bir Azure AD uygulaması için *uygulama kimliği URI 'si* benzersiz olmalıdır. Bu KIMLIK 256 karakterden daha uzun olamaz. Uygulama KIMLIĞI URI 'SI hakkında daha fazla bilgi için bkz. [uygulamaları Azure Active Directory tümleştirme](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts).
1. **Roller** bölümünde, Azure AD uygulaması için rol (ler) veya özelleştirilmiş izinleri belirtin.
1. **Kaydet**’i seçin.

Bir Azure AD uygulaması ekledikten veya oluşturduktan sonra, **Kullanıcı** bölümüne dönüp Kiracı kimliği, istemci kimliği, yanıt URL 'si ve uygulama kimliği URI 'si de dahil olmak üzere uygulamanın ayarlarını gözden geçirmek için uygulama adını seçebilirsiniz.

## <a name="remove-an-azure-ad-application"></a>Azure AD uygulamasını kaldırma

İş hesabınızdan (Azure AD kiracısı) bir uygulamayı kaldırmak için **kullanıcılara** gidin ( **Hesap ayarları** altında), en sağdaki sütunda onay kutusunu kullanarak kaldırmak istediğiniz uygulamayı seçin ve ardından kullanılabilir eylemlerden **Kaldır** ' ı seçin. Seçili uygulamaları kaldırmak istediğinizi onaylamanız için bir açılır pencere görüntülenir.

## <a name="manage-keys-for-an-azure-ad-application"></a>Bir Azure AD uygulaması için anahtarları yönetme

Azure AD uygulamanız Microsoft Azure AD verileri okuyup yazıyorsa, anahtar gerekir. Iş Ortağı Merkezi 'nde bilgilerini düzenleyerek bir Azure AD uygulaması için anahtarlar oluşturabilirsiniz. Artık gerekli olmayan anahtarları da kaldırabilirsiniz.

1. **Kullanıcılar** sayfasından ( **Hesap ayarları** altında) Azure AD uygulamasının adını seçin. Anahtarın oluşturulduğu tarih ve ne zaman sona ereceği 50 dahil olmak üzere Azure AD uygulaması için etkin anahtarların tümünü görürsünüz.
1. Artık gerekli olmayan bir anahtarı kaldırmak için **Kaldır**' ı seçin.
1. Yeni bir anahtar eklemek için **Yeni anahtar Ekle**' yi seçin.
1. **ISTEMCI kimliği** ve **anahtar değerlerini** gösteren bir ekran görürsünüz. Bu sayfadan ayrıldıktan sonra bir daha erişemeyeceksiniz, bu bilgileri yazdırdığınızdan veya kopyalamadığınızdan emin olun.
1. Daha fazla anahtar oluşturmak istiyorsanız **başka bir anahtar Ekle**' yi seçin.
