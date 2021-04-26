---
title: Azure AD Connect ve Connect Health nedir? | Microsoft Belgeleri
description: Şirket içi ortamınızı Azure AD ile senkronize etmek ve izlemek için kullanılan araçlar hakkında bilgi edinin.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: afc3d1d84d7b50cdaa7031230d7085e9f611c6c6
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304979"
---
# <a name="what-is-azure-ad-connect"></a>Azure AD Connect nedir?

Azure AD Connect, Microsoft'un karma kimlik hedeflerinizi karşılamak ve gerçekleştirmek için tasarladığı araçtır.  Aşağıdaki özellikleri sağlar:
     
- [Parola karması eşitlemesi](whatis-phs.md) -Azure AD ile ŞIRKET içi ad parolasının karmasını eşitleyen bir oturum açma yöntemi.
- [Geçişli kimlik doğrulaması](how-to-connect-pta.md) -kullanıcıların şirket içinde ve bulutta aynı parolayı kullanmasına izin veren bir oturum açma yöntemi, ancak Federasyon ortamında ek altyapı gerektirmez.
- [Federasyon tümleştirme](how-to-connect-fed-whatis.md) -federasyon, Azure AD Connect 'nin isteğe bağlı bir parçasıdır ve şirket içi AD FS altyapısını kullanarak karma ortam yapılandırmak için kullanılabilir. Ayrıca sertifika yenileme ve ek AD FS sunucu dağıtımları gibi AD FS yönetim özellikleri de sağlar.
- [Eşitleme](how-to-connect-sync-whatis.md) -Kullanıcı, Grup ve diğer nesneleri oluşturmaktan sorumludur.  Ayrıca, şirket içi kullanıcılarınız ve gruplarınız için kimlik bilgilerinin bulutla aynı olduğundan emin olun.  Bu eşitleme, parola karmaları da içerir.
- [Sistem durumu izleme](whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) -Azure AD Connect Health, bu etkinliği görüntülemek için güçlü izleme sağlayabilir ve Azure Portal merkezi bir konum sağlayabilir. 


![Azure AD Connect nedir?](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Azure AD Connect Health nedir?

Azure Active Directory (Azure AD) Connect Health, şirket içi kimlik altyapınızın sağlam bir şekilde izlenmesini sağlar. Microsoft 365 ve Microsoft Online Services ile güvenilir bir bağlantıyı korumanıza olanak sağlar.  Bu güvenilirlik, anahtar kimlik bileşenleriniz için izleme özellikleri sunarak elde edilir. Ayrıca, bu bileşenlerle ilgili önemli veri noktalarını kolayca erişilebilir hale getirir.

Bilgiler, [Azure AD Connect Health Portalı](https://aka.ms/aadconnecthealth)'nda sunulur. Uyarıları, performans izlemeyi, kullanım analizlerini ve diğer bilgileri görüntülemek için Azure AD Connect Health portalını kullanın. Azure AD Connect Health, anahtar kimlik bileşenleriniz için aynı yerde tek sistem durumu odağı sağlar.

![Azure AD Connect Health nedir?](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Azure AD Connect neden kullanılır?
Şirket içi dizinlerinizin Azure AD ile tümleştirilmesi, kullanıcılarınızın hem bulut kaynaklarına hem de şirket içi kaynaklara erişmesi için ortak bir kimlik oluşturarak daha üretken olmalarını sağlar. Kullanıcıların ve kuruluşların avantajlarından faydalanabilirsiniz:

* Kullanıcılar, Microsoft 365 gibi şirket içi uygulamalara ve bulut hizmetlerine erişmek için tek bir kimlik kullanabilir.
* Eşitleme ve oturum açmaya yönelik kolay bir dağıtım deneyimi sağlamak için tek araç.
* Senaryolarınız için en yeni işlevleri sağlar. Azure AD Connect DirSync ve Azure AD Eşitleme gibi kimlik tümleştirme araçlarının eski sürümlerini değiştirir. Daha fazla bilgi için bkz. [karma kimlik dizini tümleştirme araçları karşılaştırması](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Azure AD Connect Health neden kullanılır?
Azure AD ile kimlik doğrulaması yaparken, hem bulutta hem de şirket içi kaynaklara erişmek için ortak bir kimlik olduğundan kullanıcılarınız daha üretken olacaktır. Ortamın güvenilir olmasını sağlamak, kullanıcıların bu kaynaklara erişebilmesi için bir zorluk haline gelir.  Azure AD Connect Health, bu ortamın güvenilirliğini sağlamak için şirket içi kimlik altyapınızı izlemenize ve bu bilgileri elde etmenize yardımcı olur. Her bir şirket içi kimlik sunucunuza bir aracı yüklemek kadar basittir.

AD FS'ye ilişkin Azure AD Connect Health Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 ve Windows Server 2016 üzerinde AD FS 2.0'ı destekler. Ayrıca, extranet erişimi için kimlik doğrulaması desteği sağlayan AD FS ve web uygulaması ara sunucularının izlenmesini de destekler. AD FS için Azure AD Connect Health, çabucak ve kolayca kurulan Sistem Durumu Aracısı ile bir dizi başlıca özellik sunar.

Önemli avantajlar ve en iyi uygulamalar:

|Önemli Avantajlar|En İyi Uygulamalar|
|-----|-----|
|Artırılmış güvenlik|[Extranet kilitlemede trendler](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Başarısız oturum açma raporu](how-to-connect-health-adfs-risky-ip.md)</br>[Gizlilik uyumlu](reference-connect-health-user-privacy.md)|
|[Tüm KRITIK ADFS sistem sorunları](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services) hakkında uyarı alın|Sunucu yapılandırması ve kullanılabilirlik</br>[Performans ve bağlantı](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Düzenli bakım|
|Dağıtımı ve yönetimi kolay|[Hızlı aracı yüklemesi](how-to-connect-health-agent-install.md#install-the-agent-for-ad-fs)</br>Aracıyı en sün sürüme otomatik yükseltme</br>Dakikalar içinde portalda veri|
Zengin [kullanım ölçümleri](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|En çok kullanılan uygulamalar</br>Ağ konumları ve TCP bağlantısı</br>Sunucu başına belirteç istekleri|
|Harika kullanıcı deneyimi|Azure portaldan bir pano tarzında</br>[E-postayla uyarılar](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>Azure AD Connect kullanımı için lisans gereksinimleri

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Azure AD Connect Health kullanımı için lisans gereksinimleri
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Donanım ve önkoşullar](how-to-connect-install-prerequisites.md) 
- [Hızlı ayarlar](how-to-connect-install-express.md)
- [Özelleştirilmiş ayarlar](how-to-connect-install-custom.md)
- [Azure AD Connect Health aracılarını yükleme](how-to-connect-health-agent-install.md)
