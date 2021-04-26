---
title: ESR için grup ilkesi ve MDM ayarları-Azure Active Directory
description: Enterprise State Roaming için yönetim ayarları
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab24b3113f9dc69b8f3907037e228ba212a03106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85252942"
---
# <a name="group-policy-and-mdm-settings"></a>grup ilkesi ve MDM ayarları

Bu ilkeler kullanıcının tüm cihazına uygulandığından, bu Grup İlkesi ve mobil cihaz yönetimi (MDM) ayarlarını yalnızca şirkete ait cihazlarda kullanın. Kişisel, kullanıcıya ait bir cihaz için ayar eşitlemesini devre dışı bırakmak üzere MDM ilkesi uygulamak, bu cihazın kullanımını olumsuz yönde etkiler. Ayrıca, cihazdaki diğer Kullanıcı hesapları da ilkeden etkilenir.

Kişisel (yönetilmeyen) cihazlar için dolaşımı yönetmek isteyen kuruluşlar, grup ilkesi veya MDM kullanmak yerine, dolaşımını etkinleştirmek veya devre dışı bırakmak için Azure portal kullanabilir.
Aşağıdaki tablolarda kullanılabilir ilke ayarları açıklanır.

> [!NOTE]
> Bu makale, 2015 Temmuz sürümünde Windows 10 ile başlatılan Microsoft Edge eski HTML tabanlı tarayıcı için geçerlidir. Makale, 15 Ocak 2020 ' de yayınlanan yeni Microsoft Edge Bermıum tabanlı tarayıcı için de geçerlidir. Yeni Microsoft Edge için eşitleme davranışı hakkında daha fazla bilgi için [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)makalesine bakın.

## <a name="mdm-settings"></a>MDM ayarları

MDM ilke ayarları hem Windows 10 hem de Windows 10 Mobile için geçerlidir.  Windows 10 Mobile desteği yalnızca kullanıcının OneDrive hesabı aracılığıyla Microsoft hesabı tabanlı dolaşımda bulunur. Azure AD tabanlı eşitlemede desteklenen cihazların ayrıntılarını görmek için [cihazlar ve uç noktalar](enterprise-state-roaming-windows-settings-reference.md) bölümüne bakın.

| Ad | Açıklama |
| --- | --- |
| Microsoft hesabı bağlantısına izin ver |Kullanıcıların cihazda Microsoft hesabı kullanarak kimlik doğrulaması yapmasına izin verir |
| Ayarlarımı eşitlemeye izin ver |Kullanıcıların Windows ayarlarını ve uygulama verilerini dolaşımını sağlar; Bu ilkeyi devre dışı bırakmak, eşitlemenin yanı sıra mobil cihazlarda yedeklemeler de devre dışı bırakılır |

## <a name="group-policy-settings"></a>Grup ilkesi ayarları

Grup İlkesi ayarları, bir Active Directory etki alanına katılmış Windows 10 cihazlarına uygulanır. Tablo, eşitleme ayarlarını yönetmek için görünen eski ayarları da içerir, ancak bu, açıklamada ' kullanılamaz ' olarak belirtilen Windows 10 için Enterprise State Roaming çalışmaz.

Bu ayarlar şurada bulunur: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Ad | Açıklama |
| --- | --- |
| Hesaplar: Microsoft hesaplarını engelleyin |Bu ilke ayarı, kullanıcıların bu bilgisayarda yeni Microsoft hesapları eklemesini engeller |
| Eşitleme |Kullanıcıların Windows ayarlarını ve uygulama verilerini dolaşımını engeller |
| Kişiselleştirmeyi eşitleme |Temalar grubunun eşitlenmesini devre dışı bırakır |
| Tarayıcı ayarlarını eşitleme |Internet Explorer grubunun eşitlenmesini devre dışı bırakır |
| Parolaları eşitleme |Parola grubu eşitlemesini devre dışı bırakır |
| Diğer Windows ayarlarını eşitleme |Diğer Windows ayarları grubunu eşitlemeyi devre dışı bırakır |
| Masaüstü kişiselleştirmesini eşitleme |Kullanmayın; hiçbir etkisi yoktur |
| Tarifeli bağlantılarda eşitleme |Şebeke 3G gibi tarifeli bağlantılarda dolaşımı devre dışı bırakır |
| Uygulamaları eşitleme |Kullanmayın; hiçbir etkisi yoktur |
| Uygulama ayarlarını eşitleme |Uygulama verilerinin dolaşımını devre dışı bırakır |
| Başlangıç ayarlarını eşitleme |Kullanmayın; hiçbir etkisi yoktur |

## <a name="next-steps"></a>Sonraki adımlar

Genel bakış için bkz. [kuruluş durumu dolaşımına genel bakış](enterprise-state-roaming-overview.md).
