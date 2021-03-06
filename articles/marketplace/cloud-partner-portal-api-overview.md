---
title: Bulut İş Ortağı Portalı API başvurusu-Microsoft ticari Market
description: Açıklaması, kullanım önkoşulları ve Market API işlemlerinin listesi.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 57d3d1c6bb14db3eb2ca499069934a628d2f7fea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92425772"
---
# <a name="cloud-partner-portal-api-reference"></a>Bulut İş Ortağı Portalı API Başvurusu

> [!NOTE]
> Bulut İş Ortağı Portalı API 'Leri ile tümleşiktir ve Iş Ortağı Merkezi 'nde çalışmaya devam edecektir. Geçiş küçük değişiklikler sunar. Iş Ortağı Merkezi 'ne geçtikten sonra kodunuzun çalışmaya devam ettiğinden emin olmak için bu belgede listelenen [cpp API 'Lerinde yapılan değişiklikleri](#changes-to-cpp-apis-after-the-migration-to-partner-center) gözden geçirin. CPP API 'Leri yalnızca Iş Ortağı Merkezi 'ne geçişten önce tümleştirilmiş mevcut ürünler için kullanılmalıdır; Yeni ürünlerin Iş Ortağı Merkezi gönderme API 'Leri kullanması gerekir.

Bulut İş Ortağı Portalı REST API 'Leri, iş yüklerinin, tekliflerin ve yayımcı profillerinin programlı olarak alınmasına ve işlemesine olanak sağlar. API 'Ler, işleme zamanında doğru izinleri zorlamak için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanır.

Bu başvuru Bulut İş Ortağı Portalı REST API 'Leri için teknik ayrıntılar sağlar. Bu belgedeki yük örnekleri yalnızca başvuru amaçlıdır ve yeni işlevsellik eklendikçe değişikliğe tabidir.

## <a name="prerequisites-and-considerations"></a>Önkoşullar ve konular

API 'Leri kullanmadan önce şunları gözden geçirmeniz gerekir:

- Hesabınıza bir hizmet sorumlusu ekleme ve kimlik doğrulaması için bir Azure Active Directory (Azure AD) erişim belirteci alma hakkında bilgi edinmek için [Önkoşul](./cloud-partner-portal-api-prerequisites.md) makalesi.
- Bu API 'Leri çağırmak için kullanılabilen iki [eşzamanlılık denetim](./cloud-partner-portal-api-concurrency-control.md) stratejisi.
- Sürüm oluşturma ve hata işleme gibi ek API [konuları](./cloud-partner-portal-api-considerations.md).

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Iş Ortağı Merkezi 'ne geçişten sonra CPP API 'Lerinde yapılan değişiklikler

| **API** | **Açıklamayı Değiştir** | **Etki** |
| ------- | ---------------------- | ---------- |
| Yayınlama SONRASı, GoLive, Iptal | Geçirilen tekliflere, yanıt üst bilgisi farklı bir biçime sahip olur ancak işlem durumunu almak için göreli bir yol belirten aynı şekilde çalışmaya devam edecektir. | Bir teklif için karşılık gelen POST isteklerinin herhangi birini gönderirken, teklifin geçiş durumuna bağlı olarak konum üstbilgisi iki biçimden birine sahip olur:<ul><li>Geçirilmeyen teklifler<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Geçirilmiş teklifler<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Işlemi al | Yanıtta daha önce ' bildirim-e-posta ' alanının desteklendiği teklif türleri için, bu alan kullanım dışı bırakılır ve artık geçirilmiş teklifler için döndürülmez. | Geçirilmiş teklifleriniz için, isteklerde belirtilen e-posta listesine artık bildirim gönderemeyecektir. Bunun yerine, API hizmeti, e-posta göndermek için Iş Ortağı Merkezi 'nde bildirim e-postası işlemiyle hizalanır. Özellikle, işlem ilerleme durumunu bildirmek için Iş ortağı merkezindeki hesap ayarlarınızın satıcı iletişim bilgileri bölümünde bulunan e-posta adresine bildirim gönderilir.<br><br>Bildirimler için doğru e-postanın sağlandığından emin olmak için Iş Ortağı Merkezi 'ndeki [hesap ayarlarınızın](https://partner.microsoft.com/dashboard/account/management) satıcı iletişim bilgileri bölümünde ayarlanan e-posta adresini gözden geçirin.  |

## <a name="common-tasks"></a>Genel görevler

Bu başvuru, aşağıdaki ortak görevleri gerçekleştirmek için API 'Ler hakkında bilgi sağlar.

### <a name="offers"></a>Teklifler

- [Tüm teklifleri al](./cloud-partner-portal-api-retrieve-offers.md)
- [Belirli bir teklifi alma](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Teklif durumunu alma](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Teklif oluşturma](./cloud-partner-portal-api-creating-offer.md)
- [Teklif yayımlama](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operations

- [İşlemleri alma](./cloud-partner-portal-api-retrieve-operations.md)
- [İşlemleri iptal etme](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Uygulama yayımlama

- [Canlı yayına geçme](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Diğer görevler

- [Sanal makine teklifleri için fiyatlandırmayı ayarla](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Sorun giderme

- [Kimlik doğrulama hatalarını giderme](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
