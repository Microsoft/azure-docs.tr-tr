---
title: 'Azure AD Connect: ADSync hizmet hesabı | Microsoft Docs'
description: Bu konu, ADSync hizmet hesabını açıklar ve firmayla ilgili en iyi yöntemleri sağlar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca99a997d621bfd2455e909b36b6802775b20ac2
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074632"
---
# <a name="adsync-service-account"></a>AD Eşitleme hizmeti hesabı
Azure AD Connect, Active Directory ve Azure Active Directory arasındaki eşitlemeyi düzenleyen bir şirket içi hizmeti yüklüyor.  Microsoft Azure AD eşitleme eşitleme hizmeti (ADSync), şirket içi ortamınızdaki bir sunucuda çalışır.  Hizmetin kimlik bilgileri hızlı yüklemelerde varsayılan olarak ayarlanır ancak kuruluş güvenlik gereksinimlerinizi karşılayacak şekilde özelleştirilebilir.  Bu kimlik bilgileri, şirket içi ormanlarınız veya Azure Active Directory bağlanmak için kullanılmaz.

ADSync hizmeti hesabını seçmek, Azure AD Connect yüklemeden önce yapmanız için önemli bir planlama karardır.  Yüklemeden sonra kimlik bilgilerini değiştirme girişimleri, hizmetin başlamalamaması, eşitleme veritabanına erişiminin kaybedilmesi ve bağlı dizinlerinizde kimlik doğrulaması başarısız olur (Azure ve AD DS).  Özgün kimlik bilgileri geri yüklenene kadar hiçbir eşitleme gerçekleşmeyecektir.

Eşitleme hizmeti farklı hesaplar altında çalıştırılabilir. Bir sanal hizmet hesabı (VSA), yönetilen hizmet hesabı (gMSA/sMSA) veya normal bir kullanıcı hesabı altında çalışabilir. Yeni bir yükleme yaptığınızda desteklenen seçenekler 2017 Nisan sürümü ve 2021 Mart sürümü ile değiştirilmiştir Azure AD Connect. Azure AD Connect önceki bir sürümünden yükseltiyorsanız, bu ek seçenekler kullanılamaz. 


|Hesap türü|Yükleme seçeneği|Description| 
|-----|------|-----|
|Sanal hizmet hesabı|Express ve Custom, 2017 Nisan ve üzeri| Bir sanal hizmet hesabı, bir etki alanı denetleyicisindeki yüklemeler hariç tüm Express yüklemeleri için kullanılır. Özel yükleme kullanılırken, başka bir seçenek kullanılmadığı takdirde bu varsayılan seçenektir.| 
|Yönetilen Hizmet Hesabı|Özel, 2017 Nisan ve üzeri|Uzak bir SQL Server kullanıyorsanız, grup tarafından yönetilen bir hizmet hesabı kullanmanızı öneririz. |
|Yönetilen Hizmet Hesabı|Express ve Custom, 2021 Mart ve üzeri|ADSyncMSA_ ön eki olan tek başına yönetilen hizmet hesabı, bir etki alanı denetleyicisine yüklendiğinde hızlı yüklemeler için yükleme sırasında oluşturulur. Özel yükleme kullanılırken, başka bir seçenek kullanılmadığı takdirde bu varsayılan seçenektir.|
|Kullanıcı Hesabı|Express ve Custom, 2017 Nisan-2021 Mart|AAD_ önekli bir kullanıcı hesabı, bir etki alanı denetleyicisine yüklendiğinde hızlı yüklemeler için yükleme sırasında oluşturulur. Özel yükleme kullanılırken, başka bir seçenek kullanılmadığı takdirde bu varsayılan seçenektir.|
|Kullanıcı Hesabı|Express ve Custom, 2017 Mart ve önceki sürümleri|AAD_ önekli bir kullanıcı hesabı, hızlı yüklemeler için yükleme sırasında oluşturulur. Özel yükleme kullanılırken, başka bir hesap belirtilebilir.| 

>[!IMPORTANT]
> 2017 Mart veya daha önceki bir yapıyla Bağlan ' ı kullanırsanız, Windows şifreleme anahtarlarını güvenlik nedenleriyle yok etmek için hizmet hesabındaki parolayı sıfırlamamalısınız. Azure AD Connect yüklemeden hesabı başka bir hesaba değiştiremezsiniz. 2017 Nisan veya sonraki bir sürüme yükseltme yaparsanız, hizmet hesabındaki parolanın değiştirilmesi desteklenir, ancak kullanılan hesabı değiştiremezsiniz. 

> [!IMPORTANT]
> Yalnızca ilk yüklemede hizmet hesabını ayarlayabilirsiniz. Yükleme tamamlandıktan sonra hizmet hesabını değiştirmek desteklenmez. Hizmet hesabı parolasını değiştirmeniz gerekiyorsa, bu desteklenir ve yönergeler [burada](how-to-connect-sync-change-serviceacct-pass.md)bulunabilir.

Eşitleme hizmeti hesabı için varsayılan, önerilen ve desteklenen seçeneklerin bir tablosu aşağıda verilmiştir. 

Açıklama: 

- **Kalın** , varsayılan seçeneği ve çoğu durumda önerilen seçeneği gösterir. 
- *İtalik* varsayılan seçenek olmadığında önerilen seçeneği gösterir. 
- Kalın olmayan desteklenen seçenek 
- Yerel hesap-sunucuda yerel kullanıcı hesabı 
- Etki alanı hesabı-etki alanı kullanıcı hesabı 
- sMSA- [tek başına yönetilen hizmet hesabı](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA- [Grup yönetilen hizmet hesabı](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

|Makine türü |**LocalDB </br> Express**|**LocalDB/LocalSQL </br> özel**|**Uzak SQL </br> özel**|
|-----|-----|-----|-----|
|**etki alanına katılmış makine**|**'NıN**|**'NıN**</br> *SMSA 'yı*</br> *gMSA*</br> Yerel hesap</br> Etki alanı hesabı| *gMSA* </br>Etki alanı hesabı|
|Etki Alanı Denetleyicisi| **SMSA 'yı**|**SMSA 'yı** </br>*gMSA*</br> Etki alanı hesabı|*gMSA*</br>Etki alanı hesabı| 

## <a name="virtual-service-account"></a>Sanal hizmet hesabı 

Bir sanal hizmet hesabı, parolası olmayan ve Windows tarafından otomatik olarak yönetilen özel bir yönetilen yerel hesap türüdür. 

 ![Sanal hizmet hesabı](media/concept-adsync-service-account/account-1.png)

Sanal hizmet hesabı, eşitleme altyapısının ve SQL 'in aynı sunucuda olduğu senaryolarla birlikte kullanılmak üzere tasarlanmıştır. Uzak SQL kullanıyorsanız, bunun yerine grup tarafından yönetilen bir hizmet hesabı kullanmanızı öneririz. 

[Windows Data PROTECTION API (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx) sorunları nedeniyle, sanal hizmet hesabı bir etki alanı denetleyicisinde kullanılamaz. 

## <a name="managed-service-account"></a>Yönetilen Hizmet Hesabı 

Uzak bir SQL Server kullanıyorsanız, grup tarafından yönetilen bir hizmet hesabı kullanmanızı öneririz. Grup tarafından yönetilen hizmet hesabı için Active Directory hazırlama hakkında daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)). 

Bu seçeneği kullanmak için, [gerekli bileşenleri yüklensin](how-to-connect-install-custom.md#install-required-components) sayfasında, **var olan bir hizmet hesabını kullan**' ı seçin ve **yönetilen hizmet hesabı**' nı seçin. 

 ![yönetilen hizmet hesabı](media/concept-adsync-service-account/account-2.png)

Tek başına yönetilen hizmet hesabı kullanmak için de desteklenir. Ancak bunlar yalnızca yerel makinede kullanılabilir ve bunları varsayılan sanal hizmet hesabı üzerinden kullanmanın bir avantajı yoktur. 

### <a name="auto-generated-standalone-managed-service-account"></a>Otomatik olarak oluşturulan tek başına yönetilen hizmet hesabı 

Bir etki alanı denetleyicisine Azure AD Connect yüklerseniz, Yükleme Sihirbazı tarafından tek başına yönetilen hizmet hesabı oluşturulur (özel ayarlarda kullanılacak hesabı belirtmediğiniz müddetçe). Hesap ön eki **ADSyncMSA_** ve gerçek eşitleme hizmetinin farklı çalışması için kullanılır. 

Bu hesap, parolası olmayan ve Windows tarafından otomatik olarak yönetilen bir yönetilen etki alanı hesabıdır. 

Bu hesap, eşitleme altyapısının ve SQL 'in etki alanı denetleyicisinde bulunduğu senaryolarla birlikte kullanılmak üzere tasarlanmıştır. 

## <a name="user-account"></a>Kullanıcı Hesabı 

Bir yerel hizmet hesabı, Yükleme Sihirbazı tarafından oluşturulur (özel ayarlarda kullanılacak hesabı belirtmediğiniz durumlar dışında). Hesap ön eki AAD_ ve gerçek eşitleme hizmetinin farklı çalışması için kullanılır. Bir etki alanı denetleyicisine Azure AD Connect yüklüyorsanız, hesap etki alanında oluşturulur. Şu durumlarda AAD_ hizmet hesabının etki alanında bulunması gerekir: 
- SQL Server çalıştıran bir uzak sunucu kullanıyorsunuz 
- kimlik doğrulaması gerektiren bir proxy kullanıyorsunuz 

 ![Kullanıcı hesabı](media/concept-adsync-service-account/account-3.png)

Hesap, sona ermeyecek uzun bir karmaşık parolayla oluşturulur. 

Bu hesap, diğer hesapların parolalarını güvenli bir şekilde depolamak için kullanılır. Bu diğer hesaplar parolaları, veritabanında şifreli olarak depolanır. Şifreleme anahtarlarına yönelik özel anahtarlar, Windows Data Protection API (DPAPI) kullanılarak şifreleme hizmetleri gizli anahtar şifrelemesi ile korunur. 

Tam bir SQL Server kullanıyorsanız, hizmet hesabı eşitleme altyapısının oluşturulan veritabanının DBO 'su olur. Hizmet başka bir izinle istendiği şekilde çalışmaz. Bir SQL oturum açma da oluşturulur. 

Hesaba Ayrıca dosyalar, kayıt defteri anahtarları ve eşitleme altyapısıyla ilgili diğer nesneler için izin verilir. 


## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
