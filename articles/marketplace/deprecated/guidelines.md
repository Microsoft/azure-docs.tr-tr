---
title: Azure Market ve AppSource yayımcısı için yönergeler | Mavisi
description: Uygulama ve hizmet yayımcıları için Azure Market ve AppSource yönergeleri
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 371f36e13c244439a583cbeb7ff06c6a1283d272
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825944"
---
# <a name="guidelines"></a>Yönergeler  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Azure Marketi için yönergeler  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Market hesabını yönetmek için Microsoft KIMLIĞI oluşturma yönergeleri  
Market hesabınızı oluşturmak için kullanılan aynı Microsoft KIMLIĞINE birden fazla kişi erişimi gerektiriyorsa, bir şirket hesabı oluşturmanıza yardımcı olması için bu yönergeleri izlemeniz gerekir. 

>[!IMPORTANT]
>Birden çok kullanıcının Microsoft Geliştirici Merkezi (Geliştirme Merkezi) hesabınıza erişmesini yetkilendirmek için, Microsoft bireysel kullanıcılara roller atamak üzere Azure Active Directory (Azure AD) kullanmanızı önerir. Her kullanıcının, tek tek Azure AD kimlik bilgileriyle oturum açarak hesaba erişmesi gerekir. Microsoft KIMLIĞINIZI şirketinizde kayıtlı bir etki alanında e-posta adresi kullanarak oluşturun Microsoft, e-postanın bir kişiye atanmadığından emin olur. `windowsapps@fabrikam.com` bunun bir örneğidir.  
>*   Daha fazla bilgi için, [Azure AD Federasyon etki alanındaki MICROSOFT kimliği](#issue-microsoft-id-in-an-azure-ad-federated-domain) bölümüne gidin.  

*   Microsoft KIMLIĞINE erişimi, mümkün olan en az sayıda geliştiriciyle sınırlayın. 
*   Geliştirme Merkezi hesabınıza erişmesi gereken herkesi içeren bir kurumsal e-posta dağıtım listesi (DL) ayarlayın. Güvenlik bilgilerinizin DL e-posta adresini ekleyin. DL, listedeki tüm çalışanların istendiğinde güvenlik kodları almasına ve Microsoft KIMLIĞINIZ için güvenlik bilgilerini yönetmesine olanak sağlar. Dağıtım listesi ayarlamak uygun değilse, her bir e-posta hesabının sahibi, istendiğinde güvenlik koduna erişmek ve bu kodun paylaşılması için kullanılabilir olmalıdır.  
    *   Örneğin, Microsoft KIMLIĞINE yeni güvenlik bilgileri eklendiğinde veya Microsoft KIMLIĞINE yeni bir cihazdan erişildiğinde sahip sahibi sorulur.  
*   Uzantı gerektirmeyen ve anahtar takım üyeleri tarafından erişilebilen bir şirket telefonu numarası ekleyin.  
*   Genel olarak, geliştiricilerin Geliştirici Merkezi hesabınızda oturum açmak için güvenilir cihazları kullanmalarını gerektirmelisiniz. Tüm anahtar takım üyelerinin güvenilen cihazlara erişimi olmalıdır. Erişim için güvenilen cihazların kullanılması, birisi Geliştirme Merkezi hesabına erişirken güvenlik kodları gönderme gereksinimini azaltır.  
*   Geliştirme Merkezi hesabına güvenilmeyen bir bilgisayardan erişim izni vermeniz gerekiyorsa, erişimi en fazla beş geliştirici ile sınırlandırmalısınız. İdeal olarak, geliştiricileriniz aynı coğrafi ve ağ konumunu paylaşan bilgisayarlardan hesaba erişmelidir.  
*   Genellikle güvenlik bilgilerinizi gözden geçirin ve doğrulayın.  
    *   Güvenlik bilgilerinizi görüntülemek için, [account.Live.com/proofs/Manage](https://account.live.com/proofs/Manage)adresinde bulunan güvenlik ayarları sayfasını ziyaret edin.

Geliştirme Merkezi hesabınıza öncelikle güvenilen bilgisayarlardan erişilmelidir. Her hafta Dev Center hesabı başına oluşturulan kodların sayısıyla sınırlı olduğundan, güvenilen bilgisayarlardan erişmeniz önemlidir. Güvenilen bilgisayarların kullanılması, en güvenli ve tutarlı oturum açma deneyimine de izin vermez. 
*   Ek geliştirme merkezi hesap yönergeleri ve güvenliği hakkında daha fazla bilgi için, [docs.Microsoft.com/Windows/UWP/Publish/Opening-a-Developer-Account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account)adresinde bulunan bir geliştirici hesabı açma sayfasını ziyaret edin. 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Sorun: Azure AD Federasyon etki alanında Microsoft KIMLIĞI  
Şirket hesabınız Azure Active Directory (Azure AD) aracılığıyla federe olabilir. Azure AD ile federe bir kurumsal e-posta adresi kullanarak bir Microsoft KIMLIĞI oluşturmayı denerseniz, bir hata alırsınız. Bir hata alırsanız, hesabınızın Azure AD aracılığıyla federe olduğunu onaylamak için BT ekibinize göz önüne almanız gerekir. Azure AD Federasyon e-postası bilinen bir sorundur ve Microsoft bu sorunu çözmeye çalışmaktadır.  
*   Azure AD hakkında daha fazla bilgi için, [docs.Microsoft.com/Azure/Active-Directory](https://docs.microsoft.com/azure/active-directory)adresinde bulunan Azure Active Directory belge sayfasını ziyaret edin.

Microsoft bir geçici çözüm önerir. `outlook.com` etki alanında yeni bir e-posta adresi oluşturmak ve iletişimlerinizi iletmek için bir kural oluşturmak için aşağıdaki adımları izleyin.  
1.  Hesap Oluştur sayfasına gidin ve yeni e-posta adresi al bağlantısına tıklayın. 
    *   Microsoft KIMLIĞINIZ için kaydolmak üzere [signup.Live.com/signup](https://signup.live.com/signup)adresinde bulunan hesap oluştur sayfasını ziyaret edin.  
2.  Yeni e-posta adresini oluşturun ve bir parola girin. `outlook.com` etki alanında yeni bir Microsoft KIMLIĞI ve bir e-posta posta kutusu oluşturulur. Hesap oluşturuluncaya kadar kayıt işlemine devam edin.  

    >[!IMPORTANT]
    >Geliştirme Merkezi 'ne kaydolmak için Microsoft KIMLIĞI olarak kaydedilmiş bir e-posta adresi veya dağıtım listesi kullanmanız gerekir. Microsoft, kişilerden bağımlılığı kaldırmak için bir dağıtım listesi kullanmanızı önerir. E-posta adresiniz veya dağıtım listeniz kayıtlı değilse, şimdi kaydolmanız gerekir.    

    >[!Important]
    >Herhangi bir e-posta adresiniz `Microsoft` şirket etki alanında bulunuyorsa, bunu geliştirme merkezi 'nde kayıt için kullanamazsınız.  

3.  Outlook e-posta adresiyle Microsoft KIMLIĞI oluşturduktan sonra Outlook posta kutunuzda oturum açın. E-posta iletme kuralı oluşturun. E-posta iletme kuralı, Outlook posta kutusunda alınan tüm e-postaları Market hesabınızı yönetmek için oluşturduğunuz etki alanındaki e-posta adresine taşımalıdır.  
    *   Outlook posta kutunuzda oturum açmak için, [Outlook.Live.com/OWA](https://outlook.live.com/owa)adresinde bulunan Outlook sayfasını ziyaret edin.  
    *   Kuralları iletme hakkında daha fazla bilgi için, [support.Office.com/article/Use-Rules-in-Outlook-Web-App-to-automatically-Forward-messages-to-another-Account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed)adresinde bulunan başka bir hesaba iletileri otomatik olarak Iletmek üzere Outlook Web App 'teki kullanım kuralları sayfasını ziyaret edin.  

1.  İletme kuralı, Outlook e-posta hesabında alınan tüm e-posta ve iletişimleri, şirketinizde kayıtlı bir etki alanındaki e-posta adresine gönderir. `outlook.com` e-posta adresiniz geliştirme merkezi ve Bulut İş Ortağı Portalı kimlik doğrulaması için kullanılmalıdır.  

## <a name="next-steps"></a>Sonraki adımlar

*   [Azure Marketi ve AppSource yayımcı Kılavuzu](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) sayfasını ziyaret edin. 
 
---
