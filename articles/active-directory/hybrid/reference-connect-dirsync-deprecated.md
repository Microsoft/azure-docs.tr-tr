---
title: DirSync 'den ve Azure AD Eşitleme yükseltin | Microsoft Docs
description: DirSync ve Azure AD Eşitleme Azure AD Connect sürümüne nasıl yükseltileceğini açıklar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53d9e2ea10aefc96c2d4d72ad0020704ba48709b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539425"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Windows Azure Active Directory Eşitleme’yi ve Azure Active Directory Eşitleme’yi yükseltme
Azure AD Connect, şirket içi dizininizi Azure AD ve Microsoft 365 bağlamak için en iyi yoldur. Bu araçlar artık kullanım dışı olduğundan ve 13 Nisan 2017 itibariyle artık desteklenmediğinden, Windows Azure Active Directory Sync (DirSync) veya Azure AD Eşitleme (AADSync) Azure AD Connect yükseltmek için harika bir süredir.

Kullanım dışı bırakılan iki kimlik eşitleme aracı, tek orman müşterileri (DirSync) ve çok ormanlı ve diğer gelişmiş müşteriler (Azure AD Eşitleme) için sunulmuştur. Bu eski araçlar, tüm senaryolar için kullanılabilen tek bir çözümle değiştirilmiştir: Azure AD Connect. Yeni senaryolar, özellik geliştirmeleri ve yeni senaryolar için destek sunar. Şirket içi kimlik verilerinizi Azure AD ve Microsoft 365 eşitlemeye devam edebilmek için, Azure AD Connect yükseltmeniz önemle önerilir. Microsoft bu eski sürümlerin 31 Aralık 2017 ' den sonra çalışmasını garanti etmez.

DirSync 'in son sürümü 2014 Temmuz 'da yayımlanmıştır ve Azure AD Eşitleme son sürümü 2015 Mayıs ayında yayımlanmıştır.

## <a name="what-is-azure-ad-connect"></a>Azure AD Connect nedir?
Azure AD Connect DirSync ve Azure AD Eşitleme ardıldır. Bu iki desteklenen tüm senaryoları birleştirir. Şirket [içi kimliklerinizi Azure Active Directory tümleştirmeyle](whatis-hybrid-identity.md)ilgili daha fazla bilgi edinebilirsiniz.

## <a name="deprecation-schedule"></a>Kullanımdan kaldırma zamanlaması
| Tarih | Yorum |
| --- | --- |
| 13 Nisan 2016 |Windows Azure Active Directory Sync ("DirSync") ve Microsoft Azure Active Directory Sync ("Azure AD Eşitleme") kullanım dışı olarak duyurulmuştur. |
| 13 Nisan 2017 |Destek sona erer. Müşteriler artık Azure AD Connect için yükseltme yapmadan bir destek talebi açamaz. |
|31 Aralık 2017|Azure AD artık Windows Azure Active Directory Sync ("DirSync") ve Microsoft Azure Active Directory Sync ("Azure AD Eşitleme") iletişimlerini kabul edemeyebilir.
|1 Nisan 2021| Windows Azure Active Directory Sync ("DirSync") ve Microsoft Azure Active Directory eşitleme ("Azure AD Eşitleme") artık çalışmayacak |

## <a name="how-to-transition-to-azure-ad-connect"></a>Azure AD Connect geçiş
DirSync çalıştırıyorsanız, iki farklı şekilde yükseltebilirsiniz: yerinde yükseltme ve paralel dağıtım. Çoğu müşteri için yerinde yükseltme önerilir ve en son bir işletim sisteminiz ve 50.000 ' den az nesne varsa. Diğer durumlarda, DirSync yapılandırmanızın Azure AD Connect çalıştıran yeni bir sunucuya taşındığı bir paralel dağıtım yapmanız önerilir.

| Çözüm | Senaryo |
| --- | --- |
| [DirSync’ten yükseltme](how-to-dirsync-upgrade-get-started.md) |<li>Zaten çalışmakta olan bir DirSync sunucunuz varsa.</li> |
| [Azure AD Eşitleme 'den yükselt](how-to-upgrade-previous-version.md) |<li>Azure AD Eşitleme taşıyorsanız.</li> |

DirSync 'ten Azure AD Connect yerinde yükseltme yapma hakkında bilgi almak istiyorsanız, bu Channel 9 videosunu inceleyin:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>SSS
**S: Azure ekibinden bir e-posta bildirimi ve/veya Microsoft 365 İleti merkezinden bir ileti aldım, ancak Bağlan kullanıyorum.**  
Bildirim, 1,0 derleme numarası ile Azure AD Connect kullanılarak müşterilere de \* gönderilmiştir. 0 (1,1 öncesi sürüm kullanılarak). Microsoft, müşterilerin Azure AD Connect sürümleriyle güncel kalmasını öneriyor. 1,1 ' de sunulan [otomatik yükseltme](how-to-connect-install-automatic-upgrade.md) özelliği, her zaman yeni bir Azure AD Connect sürümünün yüklü olmasını kolaylaştırır.

**S: 2017 Nisan ' de DirSync/Azure AD Eşitleme çalışmayı durduracak mi?**  
DirSync/Azure AD Eşitleme, 13 Nisan 2017 ' de çalışmaya devam edecektir.  Ancak, Azure AD, 31 Aralık 2017 ' den sonra DirSync/Azure AD Eşitleme 'ten gelen iletişimleri artık kabul edemeyebilir. DirSync ve Azure AD Eşitleme, 1 Nisan 2021 ' den sonra çalışmayacak

**S: hangi DirSync sürümlerini yükseltebilirim?**  
Kullanılmakta olan herhangi bir DirSync sürümünden yükseltme desteklenir. 

**S: FIM/MıM için Azure AD Bağlayıcısı hakkında ne olacak?**  
FIM/MıM için Azure AD Bağlayıcısı kullanım dışı olarak **duyurulmadı.** **Özellik dondurma** aşamasında. Yeni bir işlev eklenmez ve hata düzeltmeleri almaz. Microsoft, bu uygulamayı kullanarak bu müşterilerin Azure AD Connect 'e geçme planlaması gerçekleştirmesini önerir. Bunu kullanarak yeni dağıtımlar başlatmamak kesinlikle önerilir. Bu bağlayıcı gelecekte kullanımdan kaldırılmıştır duyurulacaktır.

## <a name="additional-resources"></a>Ek Kaynaklar
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)
