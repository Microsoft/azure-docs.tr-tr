---
title: Uygulama proxy 'Si tanımlama bilgisi ayarları-Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD), uygulama proxy 'Si aracılığıyla şirket içi uygulamalara erişmek için erişim ve oturum tanımlama bilgilerine sahiptir. Bu makalede, tanımlama bilgisi ayarlarını nasıl kullanacağınızı ve yapılandırabileceğinizi öğreneceksiniz.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62afe97b44f45bc0b7aa12b33b6a65dd94ecf095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99252211"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Azure Active Directory içindeki şirket içi uygulamalara erişim için tanımlama bilgisi ayarları

Azure Active Directory (Azure AD), uygulama proxy 'Si aracılığıyla şirket içi uygulamalara erişmek için erişim ve oturum tanımlama bilgilerine sahiptir. Uygulama proxy 'Si tanımlama bilgisi ayarlarını nasıl kullanacağınızı öğrenin. 

## <a name="what-are-the-cookie-settings"></a>Tanımlama bilgisi ayarları nelerdir?

[Uygulama proxy 'si](application-proxy.md) aşağıdaki erişim ve oturum tanımlama bilgisi ayarlarını kullanır.

| Tanımlama bilgisi ayarı | Varsayılan | Description | Öneriler |
| -------------- | ------- | ----------- | --------------- |
| HTTP-Only tanımlama bilgisi kullan | **Hayır** | **Evet** seçeneği, uygulama proxy 'sinin http yanıt üst bilgilerinde HTTPOnly bayrağını içermesini sağlar. Bu bayrak ek güvenlik avantajları sağlar, örneğin, istemci tarafı komut dosyasının (CSS) tanımlama bilgilerini kopyalamasını veya değiştirmesini engeller.<br></br><br></br>HTTP-Only ayarını desteklemeden önce, uygulama proxy 'Si, değişikliklere karşı koruma sağlamak için güvenli bir TLS kanalı üzerinden şifrelenmiş ve iletilen tanımlama bilgilerini. | Ek güvenlik avantajları nedeniyle **Evet** ' i kullanın.<br></br><br></br>Oturum tanımlama bilgisine erişmesi gereken istemciler veya kullanıcı aracıları için **Hayır** kullanın. Örneğin, uygulama proxy 'Si aracılığıyla bir Uzak Masaüstü Ağ Geçidi sunucusuna bağlanan bir RDP veya MTSC istemcisi için **Hayır** ' ı kullanın.|
| Güvenli tanımlama bilgisi kullan | **Hayır** | **Evet** seçeneği, uygulama proxy 'sinin http yanıt üst bilgilerinde güvenli bayrağını içermesini sağlar. Güvenli tanımlama bilgileri, HTTPS gibi bir TLS güvenli kanalı üzerinden tanımlama bilgilerini ileterek güvenliği geliştirir. Bu, tanımlama bilgisinin şifresiz taraflar tarafından gösterilmesini engeller. | Ek güvenlik avantajları nedeniyle **Evet** ' i kullanın.|
| Kalıcı tanımlama bilgisi kullan | **Hayır** | **Evet** seçeneği, Web tarayıcısı kapalıyken uygulama proxy 'sinin erişim tanımlama bilgilerini süre sonu olarak ayarlamasına izin verir. Kalıcılık, erişim belirtecinin süresi dolana kadar veya Kullanıcı kalıcı tanımlama bilgilerini elle silinceye kadar sürer. | Kullanıcıları kimlik doğrulamasından tutarak ilişkili güvenlik riski nedeniyle **Hayır** 'ı kullanın.<br></br><br></br>Yalnızca süreçler arasında tanımlama bilgilerini paylaşabilen eski uygulamalar için **Evet 'i** kullanmanızı öneririz. Kalıcı tanımlama bilgileri kullanmak yerine, uygulamanızı süreçler arasında paylaşım tanımlama bilgilerini işleyecek şekilde güncelleştirmek daha iyidir. Örneğin, bir kullanıcının Office belgelerini bir SharePoint sitesinden gezgin görünümünde açmasına izin vermek için kalıcı tanımlama bilgilerine ihtiyacınız vardır. Kalıcı tanımlama bilgileri olmadan, erişim tanımlama bilgileri tarayıcı, Gezgin işlemi ve Office işlemi arasında paylaşılmaması durumunda bu işlem başarısız olabilir. |

## <a name="samesite-cookies"></a>SameSite tanımlama bilgileri
Sürüm Chrome 80 ve sonuç olarak, Kmıum kullanan tarayıcılarda, [SameSite](https://web.dev/samesite-cookies-explained) özniteliğini belirtmeyen tanımlama bilgileri, **SameSite = LAX** olarak ayarlanmış gibi kabul edilir. SameSite özniteliği, tanımlama bilgilerinin aynı site bağlamıyla nasıl kısıtlanması gerektiğini bildirir. LAX olarak ayarlandığında, tanımlama bilgisi yalnızca aynı site isteklerine veya üst düzey gezintiye gönderilir. Ancak, uygulama proxy 'Si, kullanıcıların oturumu sırasında düzgün bir şekilde oturum açabilmesi için bu tanımlama bilgilerinin üçüncü taraf bağlamında korunması gerekir. Bu nedenle, bu değişiklikten olumsuz etkileri önlemek için uygulama proxy 'Si erişimi ve oturum tanımlama bilgilerinde güncelleştirmeler yapıyoruz. Güncelleştirmeler şunları içerir:

* **SameSite** özniteliği **none** olarak ayarlanıyor. Bu, uygulama proxy 'Si erişiminin ve oturum tanımlama bilgilerinin üçüncü taraf bağlamında düzgün şekilde gönderilmesini sağlar.
* **Güvenli tanımlama bilgisi kullan** ayarını varsayılan olarak **Evet** ' i kullanacak şekilde ayarlama. Chrome Ayrıca tanımlama bilgilerinin güvenli bayrağı belirtmesini gerektirir veya reddedildi. Bu değişiklik, uygulama proxy 'Si aracılığıyla yayımlanan tüm mevcut uygulamalar için geçerli olacaktır. Uygulama proxy 'Si erişim tanımlama bilgilerinin her zaman güvenli olarak ayarlandığını ve yalnızca HTTPS üzerinden iletildiğini unutmayın. Bu değişiklik yalnızca oturum tanımlama bilgileri için geçerlidir.

Uygulama proxy 'Si tanımlama bilgilerinde yapılan bu değişiklikler, Chrome 80 yayın tarihinden önceki birkaç haftada bir gelecek şekilde ele alınacaktır.

Ayrıca, arka uç uygulamanızın bir üçüncü taraf bağlamında kullanılabilir olması gereken tanımlama bilgileri varsa, uygulamanızı bu tanımlama bilgileri için SameSite = None kullanacak şekilde değiştirerek açıkça tercih etmeniz gerekir. Uygulama proxy 'Si Set-Cookie üst bilgisini URL 'lerine çevirir ve arka uç uygulaması tarafından ayarlanan bu tanımlama bilgilerinin ayarlarına göre değişir.



## <a name="set-the-cookie-settings---azure-portal"></a>Tanımlama bilgisi ayarlarını ayarla-Azure portal
Azure portal kullanarak tanımlama bilgisi ayarlarını yapmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın. 
2. **Azure Active Directory**   >  **Kurumsal uygulamalar**   >  **tüm uygulamalar**' a gidin.
3. Tanımlama bilgisi ayarını etkinleştirmek istediğiniz uygulamayı seçin.
4. **Uygulama proxy 'si**' ne tıklayın.
5. **Ek ayarlar** altında, tanımlama bilgisi ayarını **Evet** veya **Hayır** olarak ayarlayın.
6. Değişikliklerinizi uygulamak için **Kaydet**’e tıklayın. 

## <a name="view-current-cookie-settings---powershell"></a>Geçerli tanımlama bilgisi ayarlarını görüntüleme-PowerShell

Uygulamanın geçerli tanımlama bilgisi ayarlarını görmek için şu PowerShell komutunu kullanın:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Tanımlama bilgisi ayarlarını belirleme-PowerShell

Aşağıdaki PowerShell komutlarında, ```<ObjectId>``` uygulamanın NesneKimliği olur. 

**Yalnızca http tanımlama bilgisi** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Güvenli tanımlama bilgisi**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Kalıcı tanımlama bilgileri**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
