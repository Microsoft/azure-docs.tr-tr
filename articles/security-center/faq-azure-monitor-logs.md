---
title: Azure Güvenlik Merkezi hakkında SSS-mevcut Log Analytics aracılarıyla ilgili sorular
description: Bu SSS, Log Analytics aracısını kullanan müşterilere yönelik soruları yanıtlar ve tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olan Azure Güvenlik Merkezi 'nin bir ürünüdür.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 1a52b2fec6155959a570f2438a59c14d9f79f368
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701969"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Zaten Azure Izleyici günlüklerini kullanan müşteriler için SSS<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Güvenlik Merkezi, VM 'Ler ve çalışma alanları arasındaki mevcut bağlantıları geçersiz kılar mi?

Bir VM 'de zaten bir Azure uzantısı olarak yüklü Log Analytics Aracısı varsa, güvenlik merkezi mevcut çalışma alanı bağlantısını geçersiz kılmaz. Bunun yerine, güvenlik merkezi mevcut çalışma alanını kullanır. "Güvenlik" veya "SecurityCenterFree" çözümünün, raporlama yaptığı çalışma alanına yüklenmiş olması için VM korunacaktır. 

Zaten mevcut değilse, veri toplama ekranında seçilen çalışma alanına bir güvenlik merkezi çözümü yüklenir ve bu çözüm yalnızca ilgili VM 'lere uygulanır. Bir çözüm eklediğinizde, varsayılan olarak, Log Analytics çalışma alanınıza bağlı tüm Windows ve Linux aracılarına otomatik olarak dağıtılır. [Çözüm hedefleme](../azure-monitor/insights/solution-targeting.md) , çözümlerinizi bir kapsam uygulamanıza olanak tanır.

> [!TIP]
> Log Analytics Aracısı doğrudan VM 'ye (Azure uzantısı olarak değil) yüklenirse, güvenlik merkezi Log Analytics Aracısı 'nı yüklemez ve güvenlik izleme sınırlı olur.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Güvenlik Merkezi, mevcut Log Analytics çalışma alanlarıma çözüm yüklesin mi? Faturalandırma etkileri nelerdir?
Güvenlik Merkezi, bir VM 'nin oluşturduğunuz bir çalışma alanına zaten bağlı olduğunu belirlediğinde, Güvenlik Merkezi, fiyatlandırma yapılandırmanıza göre bu çalışma alanındaki çözümleri sağlar. Çözümler, [çözüm hedefleme](../azure-monitor/insights/solution-targeting.md)aracılığıyla yalnızca Ilgili Azure sanal makinelerine uygulanır, böylece faturalandırma aynı kalır.

- **Azure Defender kapalı** – Güvenlik Merkezi "SecurityCenterFree" çözümünü çalışma alanına yükleme. Faturalandırılmaz.
- **Azure Defender on** -Security Center, çalışma alanına ' Güvenlik ' çözümünü yüklüyor.

   ![Varsayılan çalışma alanındaki çözümler](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Ortammda çalışma alanım zaten var, bunları güvenlik verilerini toplamak için kullanabilir miyim?
Bir VM 'de zaten bir Azure uzantısı olarak yüklü Log Analytics Aracısı varsa, güvenlik merkezi var olan bağlı çalışma alanını kullanır. Zaten mevcut değilse, bir güvenlik merkezi çözümü çalışma alanına yüklenir ve çözüm [hedefleme](../azure-monitor/insights/solution-targeting.md)aracılığıyla yalnızca ilgili sanal makinelere uygulanır.

Güvenlik Merkezi, VM 'Lere Log Analytics aracısını yüklediğinde, güvenlik merkezi mevcut bir çalışma alanına işaret yoksa Güvenlik Merkezi tarafından oluşturulan varsayılan çalışma alanlarını kullanır.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Çalışma alanlarım üzerinde zaten güvenlik çözümüdür. Faturalandırma etkileri nelerdir?
Güvenlik & denetim çözümü, **sunucular Için Azure Defender 'ı** etkinleştirmek üzere kullanılır. Güvenlik & denetim çözümü bir çalışma alanında zaten yüklüyse, güvenlik merkezi mevcut çözümü kullanır. Faturalandırma sırasında değişiklik yapılmaz.