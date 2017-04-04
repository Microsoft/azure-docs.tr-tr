---
title: "Azure AD’de parolaların güvenliğini sağlama ve Akıllı Parola Kilitleme ile engellenmiş parolaları sıfırlama | Microsoft Docs"
description: "Azure AD kiracısının ne olduğu ve Azure&quot;ın, Azure Active Directory üzerinden nasıl yönetileceği açıklanmaktadır."
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8e625a346c9495d436a99fcf9eadf8ffeffcfdff
ms.lasthandoff: 03/28/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>Azure AD’de parolaların güvenliğini sağlama ve Akıllı Parola Kilitleme ile engellenmiş parolaları sıfırlama
Bu makalede, Azure Active Directory (Azure AD) ve Microsoft Hesap Hizmeti hesaplarınızı korumak için kullanıcı veya yönetici olarak izleyebileceğiniz en iyi yöntemler ele alınmaktadır. 

 >[!NOTE]
 >Azure AD yöneticileri, dizin adına tıklayarak kullanıcı parolalarını sıfırlayabilir. [Azure Yönetim portalından](https://manage.windowsazure.com) Kullanıcılar sayfasını seçin, kullanıcının adına ve Parola Sıfırla’ya tıklayın. 
 >

Azure AD, parolaların güvenliğini sağlama konusunda aşağıdaki genel yaklaşımları benimser:
 *    Parola uzunluğu gereksinimleri
 *    Parola “karmaşıklığı” gereksinimleri
 *    Normal ve düzenli parola sona erme süresi 

Parola yönetimi özellikleri hakkında bilgi için bkz. [Azure Active Directory’de parolaları yönetme](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords). 

## <a name="azure-ad-password-protection"></a>Azure AD parola koruması
Azure AD ve Microsoft Hesap Sistemi, kullanıcı ve yönetici parolalarının güvenliğini sağlamak için sektörde kanıtlanmış yaklaşımları kullanır. 

Bu bölümde, Azure AD’nin aşağıdaki yöntemleri kullanarak parolaları nasıl koruduğu anlatılmaktadır:
 *    Dinamik olarak yasaklanmış parolalar
 *    Akıllı Parola Kilitleme

Güncel bir araştırmaya göre parola yönetimi hakkında bilgi almak için bkz. [Parola Yönergeleri](http://aka.ms/passwordguidance) teknik incelemesi. 

### <a name="dynamically-banned-passwords"></a>Dinamik olarak yasaklanmış parolalar
Azure AD ve Microsoft Hesap Sistemi, yaygın olarak kullanılan tüm parolaları yasaklayarak, parola korumasını gerçekleştirir. Azure ID Kimlik Koruma ekibi, yasaklı parola listelerini düzenli olarak analiz eder ve kullanıcıların yaygın olarak kullanılan parolaları seçmesini engeller. Bu hizmet, Azure AD ve Microsoft Hesap Hizmeti müşterileri tarafından kullanılabilir. 

Parola oluşturulurken, yöneticilerin kullanıcıları harf, sayı ve karakterlerin benzersiz bir birleşiminden oluşan nadir parola tümcecikleri seçmeye teşvik etmesi gerekir. Bunun yapılması, kullanıcı parolalarının tehlikeye girmesini neredeyse imkansız hale getirir. 

**İhlal listeleri**

Azure AD, siber suçluların her zaman bir adım önünde olmak için çalışmaktadır. Bunu yapmanın bir yolu, kullanıcıların güncel saldırgan listesinde bulunan parolalar oluşturmasını engellemektir.

Azure AD Kimlik Koruması ekibi, yaygın olarak kullanılan parolaları sürekli olarak analiz eder. Siber suçlular da saldırılarını geliştirmek üzere parola karmalarını çözmek için [şifre tablosu](https://en.wikipedia.org/wiki/Rainbow_table) oluşturmak gibi benzer stratejiler kullanır. 

Microsoft dinamik olarak güncellenen bir yasaklı parola listesi tutmak üzere [veri ihlallerini](https://www.privacyrights.org/data-breaches) sürekli olarak analiz eder; bu liste, güvenlik açığı olan parolaların Azure AD müşterileri için gerçek bir tehdit haline gelmesinden önce engellenmesini sağlar. Geçerli güvenlik çalışmalarımız hakkında daha fazla bilgi için bkz. [Microsoft Güvenlik Bilgileri Raporu](https://www.microsoft.com/security/sir/default.aspx). 

### <a name="smart-password-lockout"></a>Akıllı Parola Kilitleme

Azure AD bir kullanıcı parolasına saldırmaya çalışan olası bir siber suçluyu algıladığında, kullanıcı hesabı Akıllı Parola Kilitleme özelliğiyle kilitlenir. Azure AD, belirli oturumlarla ilişkili riski belirlemek için tasarlanmıştır. 

En güncel güvenlik verilerini kullanarak, siber tehditlere karşı kilitleme semantiği uyguluyoruz. Bu şekilde, bir siber suçlu ağınızdaki kullanıcı parolalarına saldırdığında kullanıcılar kilitlenmez.

Bir kullanıcı Azure AD’nin dışında kalırsa, ekranı aşağıdaki gibi görünür:

  ![Azure AD dışında kalmış](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
Diğer Microsoft hesapları için de ekran aşağıdaki gibi görünür:

  ![Microsoft hesabı dışında kalmış](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Azure Active Directory’de parola yönetimi hakkında bilgi için bkz. [Parola yönetiminin işleyişi](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works).

  >![NOT] Bir Azure AD yöneticisiyseniz, kullanıcıların geleneksel parolalar oluşturmasını önlemek için [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello) kullanabilirsiniz.
  >

## <a name="next-steps"></a>Sonraki adımlar
[Kendi parolanızı güncelleştirme](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Azure kimlik yönetimi ile ilgili temel bilgiler](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[Parola yönetimi raporları ile operasyonel öngörüler alma](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



