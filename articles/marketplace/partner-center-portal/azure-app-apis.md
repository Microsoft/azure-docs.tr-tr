---
title: Microsoft Commercial Market 'te Azure uygulamalarını eklemek için iş ortağı merkezi gönderme API 'SI
description: Microsoft Iş Ortağı Merkezi 'nde ticari Market 'teki Azure uygulamaları için Iş Ortağı Merkezi gönderme API 'sini kullanma önkoşullarını öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9863ed24da9e427f885a4794bda7e103b0c1cc8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96455452"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>İş Ortağı Merkezi 'ne Azure uygulamaları eklemek için iş ortağı merkezi gönderme API 'SI

Program aracılığıyla sorgulamak, teslimleri oluşturmak ve Azure tekliflerini yayımlamak için *Iş Ortağı Merkezi gönderme API* 'sini kullanın.  Bu API, hesabınız birçok teklifi yönetirse ve bu teklifler için gönderme işlemini otomatikleştirmek ve iyileştirmek istiyorsanız yararlıdır.

## <a name="api-prerequisites"></a>API ön koşulları

Azure ürünleri için Iş Ortağı Merkezi API 'sini kullanmak üzere ihtiyacınız olan birkaç programlı varlık vardır: 

- bir Azure Active Directory uygulaması.
- Azure Active Directory (Azure AD) erişim belirteci.

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>1. Adım: Iş Ortağı Merkezi gönderme API 'SI kullanımı için tüm önkoşulları

Iş Ortağı Merkezi gönderme API 'sini çağırmak için kod yazmaya başlamadan önce, aşağıdaki önkoşulları tamamladığınızdan emin olun.

- Siz (veya kuruluşunuzun) bir Azure AD dizinine sahip olmanız ve dizin için [genel yönetici](../../active-directory/roles/permissions-reference.md) izninizin olması gerekir. Zaten Microsoft 'tan Microsoft 365 veya diğer iş hizmetlerini kullanıyorsanız, zaten Azure AD dizininiz vardır. Aksi takdirde, ek ücret ödemeden [Iş Ortağı Merkezi 'nde yeni bir Azure ad oluşturabilirsiniz](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) .

- [Bir Azure AD uygulamasını Iş Ortağı Merkezi hesabınızla ilişkilendirmeli](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) ve kiracı kimliğinizi, istemci kimliğini ve anahtarınızı edinmeniz gerekir. Microsoft Store gönderme API 'SI çağrılarında kullanacağınız bir Azure AD erişim belirteci edinmek için bu değerlere ihtiyacınız vardır.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Azure AD uygulamasını Iş Ortağı Merkezi hesabınızla ilişkilendirme

Microsoft Store gönderimi API 'sini kullanmak için, Iş Ortağı Merkezi hesabınızla bir Azure AD uygulaması ilişkilendirmeniz, uygulamanın kiracı KIMLIĞINI ve istemci KIMLIĞINI almanız ve bir anahtar oluşturmanız gerekir. Azure AD uygulaması, Iş Ortağı Merkezi gönderme API 'sini çağırmak istediğiniz uygulamayı veya hizmeti temsil eder. API 'ye geçirdiğiniz bir Azure AD erişim belirteci edinmek için kiracı KIMLIĞI, istemci KIMLIĞI ve anahtarı gereklidir.

>[!Note]
>Bu görevi yalnızca bir kez gerçekleştirmeniz gerekir. Kiracı KIMLIĞI, istemci KIMLIĞI ve anahtarı aldıktan sonra, yeni bir Azure AD erişim belirteci oluşturmak için gereken her seferinde bunları yeniden kullanabilirsiniz.

1. Iş Ortağı Merkezi 'nde [kuruluşunuzun Iş Ortağı Merkezi hesabını kuruluşunuzun Azure AD diziniyle ilişkilendirin](/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Ardından, Iş Ortağı Merkezi 'nin **Hesap ayarları** bölümündeki **Kullanıcılar** sayfasında, iş ortağı merkezi hesabınıza yönelik Gönderimlere erişmek için kullanacağınız uygulamayı veya HIZMETI temsil eden [Azure AD uygulamasını ekleyin](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) . Bu uygulamayı **yönetici** rolüne atadığınızdan emin olun. Uygulama henüz Azure AD dizininizde yoksa, [Iş Ortağı Merkezi 'nde yeni bir Azure AD uygulaması oluşturabilirsiniz](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. **Kullanıcılar** sayfasına dönün, uygulama ayarlarına gitmek IÇIN Azure AD uygulamanızın adına tıklayın ve **kiracı KIMLIĞI** ile **istemci kimliği** değerlerini kopyalayın.
1. **Yeni anahtar Ekle**' ye tıklayın. Aşağıdaki ekranda, **anahtar** değerini kopyalayın. Bu sayfadan ayrıldıktan sonra bu bilgilere bir daha erişemeyeceksiniz. Daha fazla bilgi için bkz. [Azure AD uygulaması için anahtarları yönetme](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>2. Adım: Azure AD erişim belirteci alma

Iş Ortağı Merkezi gönderme API 'sindeki yöntemlerden herhangi birini çağırabilmeniz için önce API 'deki her yöntemin **Yetkilendirme** üstbilgisine geçirdiğiniz BIR Azure AD erişim belirteci edinmeniz gerekir. Erişim belirteci elde ettikten sonra, süresi dolmadan önce kullanmanız 60 dakika sürer. Belirtecin süresi dolduktan sonra, API 'yi sonraki çağrılarında kullanmaya devam edebilmeniz için belirteci yenileyebilirsiniz.

Erişim belirtecini almak için, uç noktaya göndermek üzere [Istemci kimlik bilgilerini kullanarak çağrılara hizmet vermek Için Service](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) 'teki yönergeleri izleyin `HTTP POST` `https://login.microsoftonline.com/<tenant_id>/oauth2/token` . Örnek bir istek aşağıda verilmiştir:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

İçindeki *tenant_id* değeri `POST URI` ve *client_id* ve *Client_secret* parametreleri için, önceki bölümde Iş Ortağı Merkezi 'NDEN aldığınız uygulamanızın Kiracı kimliğini, istemci kimliğini ve anahtarını belirtin. *Kaynak* parametresi için, belirtmeniz gerekir `https://api.partner.microsoft.com` .

### <a name="step-3-use-the-microsoft-store-submission-api"></a>3. Adım: Microsoft Store gönderimi API 'sini kullanma

Bir Azure AD erişim belirteciniz olduktan sonra, Iş Ortağı Merkezi gönderme API 'sindeki yöntemleri çağırabilirsiniz. Gönderimler oluşturmak veya güncelleştirmek için genellikle Iş Ortağı Merkezi gönderme API 'sinde belirli bir sırada birden çok yöntemi çağırabilirsiniz. Her senaryo ve her yöntemin sözdizimi hakkında daha fazla bilgi için bkz. alma API 'SI Swagger.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Sonraki adımlar

* [Azure VM oluşturma teknik varlığı](../create-azure-container-technical-assets.md) oluşturma hakkında bilgi edinin
* [Azure Container teklifi](../create-azure-container-offer.md) oluşturmayı öğrenin