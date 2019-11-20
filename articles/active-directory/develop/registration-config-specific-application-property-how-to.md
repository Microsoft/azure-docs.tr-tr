---
title: Özel olarak geliştirilen bir uygulama için belirli alanları doldurun | Microsoft Docs
description: Özel bir geliştirilmiş uygulamayı Azure AD ile kaydederken belirli alanların nasıl doldurulduğunu gösteren kılavuz
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46bc6d2ebbb22ffc37ca73a3fff3b7b5a3a534b8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325347"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Özel olarak geliştirilen bir uygulama için belirli alanları doldurun

Bu makale, [Azure Portal](https://portal.azure.com)uygulama kaydı formundaki tüm kullanılabilir alanlarla ilgili kısa bir açıklama sağlar.

## <a name="register-a-new-application"></a>Yeni uygulama kaydetme

-   Yeni bir uygulamayı kaydetmek için [Azure Portal](https://portal.azure.com)gidin.

-   Sol gezinti bölmesinden Azure Active Directory ' ye tıklayın **.**

-   **Uygulama kayıtları** seçip **Ekle**' ye tıklayın.

-   Bu, uygulama kayıt formunu açar.

## <a name="fields-in-the-application-registration-form"></a>Uygulama kayıt formundaki alanlar


| Alan            | Açıklama                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Ad             | Uygulamanın adı. En az dört karakter uzunluğunda olmalıdır.                |
| Desteklenen hesap türleri| Uygulamanızın hangi hesaplara desteklemesini istediğinizi seçin: yalnızca bu kuruluş dizinindeki hesaplar, herhangi bir kuruluş dizinindeki hesaplar veya herhangi bir kuruluş dizinindeki hesaplar ve kişisel Microsoft hesapları.  |
| Yeniden yönlendirme URI 'SI (isteğe bağlı) | Oluşturmakta olduğunuz uygulamanın türünü, **Web** veya **ortak istemciyi (mobil & Masaüstü)** seçin ve ardından uygulamanızın yeniden yönlendirme URI 'SINI (veya yanıt URL 'si) girin. Web uygulamaları için, uygulamanızın temel URL'sini girin. Örneğin http://localhost:31544 yerel makinenizde çalışan bir web uygulamasının URL'si olabilir. Kullanıcılar, bir web istemci uygulamasında oturum açmak için bu URL'yi kullanır. Genel istemci uygulamaları için, Azure AD'nin belirteç yanıtlarını döndürmek üzere kullandığı URI'yi girin. Uygulamanıza özgü bir değer girin, örneğin, myapp://auth. Web uygulamalarına veya yerel uygulamalara özgü örnekler görmek için, [hızlı başlangıçlara](https://docs.microsoft.com/azure/active-directory/develop) göz atın.|

Yukarıdaki alanları doldurduktan sonra uygulama Azure portal kaydedilir ve uygulamaya genel bakış sayfasına yönlendirilirsiniz. **Yönet** altında sol bölmedeki ayarlar sayfalarında uygulamanızı özelleştirmeniz için daha fazla alan vardır. Aşağıdaki tablolar tüm alanları anlatmaktadır. Bir Web uygulaması veya genel istemci uygulaması oluşturup oluşturdığınıza bağlı olarak, bu alanların yalnızca bir alt kümesini görürsünüz.

### <a name="overview"></a>Genel Bakış
| Alan           | Açıklama        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Uygulama Kimliği  | Bir uygulamayı kaydettiğinizde, Azure AD uygulamanıza uygulama KIMLIĞI atar. Uygulama KIMLIĞI, uygulamanızı Azure AD 'ye yönelik kimlik doğrulama isteklerinde benzersiz olarak tanımlamak ve Graph API gibi kaynaklara erişmek için kullanılabilir.                                                          |
| Uygulama KIMLIĞI URI 'SI      | Bu, genellikle **https://&lt;kiracı\_adı&gt;/uygulamaadı&gt;biçimindebenzersiz bir URI olmalıdır.\_&lt;** Bu, yetkilendirme verme akışı sırasında Belirtecin verilmesi gereken kaynağı belirtmek için benzersiz bir tanımlayıcı olarak kullanılır. Ayrıca, verilen erişim belirtecinde ' AUD ' talebi de olur. |

### <a name="branding"></a>Olanağı

| Alan           | Açıklama        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Yeni logoyu karşıya yükle | Uygulamanızı bir logo yüklemek için bunu kullanabilirsiniz. Logo. bmp,. jpg veya. png biçiminde olmalıdır ve dosya boyutu 100 KB 'den az olmalıdır. Resim için Boyutlar, 94x94 piksel olan orta görüntü boyutları ile 215x215 piksel olmalıdır.|
| Giriş sayfası URL 'SI   | Bu, uygulama kaydı sırasında belirtilen oturum açma URL 'sidir.|

### <a name="authentication"></a>Authentication

| Alan           | Açıklama        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Oturum Kapatma URL'si      | Bu, çoklu oturum kapatma oturum kapatma URL 'sidir. Azure AD, başka bir kayıtlı uygulamayı kullanarak Kullanıcı oturumunu Azure AD ile temizlediğinde bu URL 'ye bir oturum kapatma isteği gönderir.|
| Desteklenen hesap türleri  | Bu anahtar, uygulamanın birden çok kiracı tarafından kullanılıp kullanılamayacağını belirtir. Genellikle, bu, harici kuruluşların kendi kiracısına kaydederek ve kuruluşunuzun verilerine erişim izni vererek uygulamanızı kullanabileceği anlamına gelir.|
| Yeniden yönlendirme URL 'Leri      | Yeniden yönlendirme veya yanıtlama, URL 'Ler, Azure AD 'nin uygulamanızın isteklerinizle ilgili belirteçleri döndürdüğü uç noktalardır. Yerel uygulamalar için, başarılı yetkilendirme sonrasında kullanıcının gönderildiği yerdir. Azure AD, uygulamanızın OAuth 2,0 isteğindeki sağladığı yeniden yönlendirme URI 'sinin, portaldaki kayıtlı değerlerden biriyle eşleşip eşleşmediğini denetler.|

### <a name="certificates-and-secrets"></a>Sertifikalar ve gizlilikler

| Alan           | Açıklama        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| İstemci gizli dizileri            | Kullanıcı etkileşimi olmadan Azure AD tarafından güvenliği sağlanmış Web API 'Lerine programlı bir şekilde erişmek için istemci gizli dizileri veya anahtarlar oluşturabilirsiniz. **Yeni istemci parolası** sayfasında, anahtar açıklaması ve sona erme tarihi girin ve anahtarı oluşturmak için kaydedin. Daha sonra erişemeyeceksiniz, bu dosyayı güvenli bir yerde kaydettiğinizden emin olun.             |

## <a name="next-steps"></a>Sonraki adımlar
[Uygulamaları Azure Active Directory ile yönetme](../manage-apps/what-is-application-management.md)
