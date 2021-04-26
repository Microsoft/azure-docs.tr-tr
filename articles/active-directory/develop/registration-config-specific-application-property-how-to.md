---
title: Özel olarak geliştirilmiş uygulamalar için Azure portalı kayıt alanlarını bulma
description: Azure AD ile özel bir geliştirilmiş uygulamayı kaydetme Kılavuzu
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 82c3dd4ce7f5e7e9f3d5a226bfe65e27eca2d3d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103252"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Özel olarak geliştirilmiş uygulamalar için Azure portalı kayıt alanlarını bulma

Bu makale, [Azure Portal](https://portal.azure.com)uygulama kaydı formundaki tüm kullanılabilir alanlarla ilgili kısa bir açıklama sağlar.

## <a name="register-a-new-application"></a>Yeni uygulama kaydetme

-   Yeni bir uygulamayı kaydetmek için <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>gidin.

-   Sol gezinti bölmesinden Azure Active Directory ' ye tıklayın **.**

-   **Uygulama kayıtları** seçip **Ekle**' ye tıklayın.

-   Bu, uygulama kayıt formunu açar.

## <a name="fields-in-the-application-registration-form"></a>Uygulama kayıt formundaki alanlar

| Alan            | Açıklama                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Ad             | Uygulamanın adı. En az dört karakter uzunluğunda olmalıdır.                |
| Desteklenen hesap türleri| Uygulamanızın hangi hesaplara desteklemesini istediğinizi seçin: yalnızca bu kuruluş dizinindeki hesaplar, herhangi bir kuruluş dizinindeki hesaplar veya herhangi bir kuruluş dizinindeki hesaplar ve kişisel Microsoft hesapları.  |
| Yeniden yönlendirme URI 'SI (isteğe bağlı) | Oluşturmakta olduğunuz uygulamanın türünü, **Web** veya **ortak istemciyi (mobil & Masaüstü)** seçin ve ardından uygulamanızın yeniden yönlendirme URI 'SINI (veya yanıt URL 'si) girin. Web uygulamaları için, uygulamanızın temel URL'sini girin. Örneğin http://localhost:31544 yerel makinenizde çalışan bir web uygulamasının URL'si olabilir. Kullanıcılar, bir web istemci uygulamasında oturum açmak için bu URL'yi kullanır. Genel istemci uygulamaları için, Azure AD'nin belirteç yanıtlarını döndürmek üzere kullandığı URI'yi girin. Uygulamanıza özgü bir değer girin, örneğin, myapp://auth. Web uygulamalarına veya yerel uygulamalara yönelik belirli örnekleri görmek için [hızlı başlangıç](./index.yml)deneyimimize göz atın.|

Yukarıdaki alanları doldurduktan sonra uygulama Azure portal kaydedilir ve uygulamaya genel bakış sayfasına yönlendirilirsiniz. **Yönet** altında sol bölmedeki ayarlar sayfalarında uygulamanızı özelleştirmeniz için daha fazla alan vardır. Aşağıdaki tablolar tüm alanları anlatmaktadır. Bir Web uygulaması veya genel istemci uygulaması oluşturup oluşturdığınıza bağlı olarak, bu alanların yalnızca bir alt kümesini görürsünüz.

### <a name="overview"></a>Genel Bakış

| Alan           | Açıklama        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Uygulama Kimliği  | Bir uygulamayı kaydettiğinizde, Azure AD uygulamanıza uygulama KIMLIĞI atar. Uygulama KIMLIĞI, uygulamanızı Azure AD 'ye yönelik kimlik doğrulama isteklerinde benzersiz olarak tanımlamak ve Graph API gibi kaynaklara erişmek için kullanılabilir.                                                          |
| Uygulama Kimliği URI'si      | Bu, genellikle **https:// &lt; kiracı \_ adı &gt; / &lt; uygulama \_ adı &gt;** biçiminde benzersiz bir URI olmalıdır. Bu, yetkilendirme verme akışı sırasında Belirtecin verilmesi gereken kaynağı belirtmek için benzersiz bir tanımlayıcı olarak kullanılır. Ayrıca, verilen erişim belirtecinde ' AUD ' talebi de olur. |

### <a name="branding"></a>Marka

| Alan           | Açıklama        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Yeni logoyu karşıya yükle | Uygulamanızı bir logo yüklemek için bunu kullanabilirsiniz. Logo. bmp,. jpg veya. png biçiminde olmalıdır ve dosya boyutu 100 KB 'den az olmalıdır. Resim için Boyutlar, 94x94 piksel olan orta görüntü boyutları ile 215x215 piksel olmalıdır.|
| Giriş sayfası URL 'SI   | Bu, uygulama kaydı sırasında belirtilen oturum açma URL 'sidir.|

### <a name="authentication"></a>Kimlik Doğrulaması

| Alan           | Açıklama        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ön kanal oturum kapatma URL 'SI      | Bu, çoklu oturum kapatma oturum kapatma URL 'sidir. Azure AD, başka bir kayıtlı uygulamayı kullanarak Kullanıcı oturumunu Azure AD ile temizlediğinde bu URL 'ye bir oturum kapatma isteği gönderir.|
| Desteklenen hesap türleri  | Bu anahtar, uygulamanın birden çok kiracı tarafından kullanılıp kullanılamayacağını belirtir. Genellikle, bu, harici kuruluşların kendi kiracısına kaydederek ve kuruluşunuzun verilerine erişim izni vererek uygulamanızı kullanabileceği anlamına gelir.|
| Yeniden Yönlendirme URL'leri      | Yeniden yönlendirme veya yanıtlama, URL 'Ler, Azure AD 'nin uygulamanızın isteklerinizle ilgili belirteçleri döndürdüğü uç noktalardır. Yerel uygulamalar için, başarılı yetkilendirme sonrasında kullanıcının gönderildiği yerdir. Azure AD, uygulamanızın OAuth 2,0 isteğindeki sağladığı yeniden yönlendirme URI 'sinin, portaldaki kayıtlı değerlerden biriyle eşleşip eşleşmediğini denetler.|

### <a name="certificates-and-secrets"></a>Sertifikalar ve gizlilikler

| Alan           | Açıklama        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| İstemci gizli dizileri            | Kullanıcı etkileşimi olmadan Azure AD tarafından güvenliği sağlanmış Web API 'Lerine programlı bir şekilde erişmek için istemci gizli dizileri veya anahtarlar oluşturabilirsiniz. **Yeni istemci parolası** sayfasında, anahtar açıklaması ve sona erme tarihi girin ve anahtarı oluşturmak için kaydedin. Daha sonra erişemeyeceksiniz, bu dosyayı güvenli bir yerde kaydettiğinizden emin olun.             |

## <a name="next-steps"></a>Sonraki adımlar

[Uygulamaları Azure Active Directory ile yönetme](../manage-apps/what-is-application-management.md)