---
title: Microsoft Threat Modeling Tool Release 10/16/2019-Azure
description: Tehdit modelleme aracı sürümü 7.1.61015.1 için sürüm notlarını belgeleme.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 761ad45496e799a555b60480ff575b9d8f30e984
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516994"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool güncelleştirme sürümü 7.1.61015.1 - 16.10.2019

Microsoft Threat Modeling Tool (TMT) sürümü 7.1.61015.1, Ekim 16 2019 ' de yayımlanmıştır ve aşağıdaki değişiklikleri içerir:

- Erişilebilirlik geliştirmeleri
- Hata düzeltmeleri
- Azure Logic Apps ve Azure Veri Gezgini için yeni şablonlar

## <a name="notable-bug-fixes"></a>Notable hata düzeltmeleri

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>"Threat Modeling Tool 2016" içinde oluşturulan dosyalarla gelişmiş geriye dönük uyumluluk

"Threat Modeling Tool 2016" içinde oluşturulan tehdit modeli dosyalarını açma veya görüntüleme ile ilgili birkaç hata düzeltildi.

## <a name="feature-enhancements"></a>Özellik geliştirmeleri

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Azure Logic Apps ve Azure Veri Gezgini için yeni şablonlar

Azure Logic Apps ve Azure Veri Gezgini için yeni şablonlar, ilişkili tehditler ve azaltmaları ile birlikte Azure kalıbı 'na eklenmiştir.

![Azure Logic Apps ve Azure Veri Gezgini kalıpları](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Bilinen sorunlar

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Beklenen aralıkların dışındaki öncelik değerleriyle ilgili hatalar

Bazı müşteriler, "Threat Modeling Tool 2016" veya özel şablonlarda oluşturulan dosyaları açarken aşağıdaki hata iletisini almayı raporladı:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Bu sorun araştırma aşamasındadır

## <a name="system-requirements"></a>Sistem Gereksinimleri

- Desteklenen İşletim Sistemleri
  - [Microsoft Windows 10 yıldönümü güncelleştirmesi](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) veya üzeri
- .NET sürümü gerekli
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) veya üzeri
- Ek Gereksinimler
  - Araç ve şablonların güncelleştirmelerini almak için bir Internet bağlantısı gerekir.

## <a name="documentation-and-feedback"></a>Belgeler ve geri bildirim

- Threat Modeling Tool belgeleri [docs.Microsoft.com](./threat-modeling-tool.md)konumunda bulunur ve [Aracı kullanma hakkında](./threat-modeling-tool-getting-started.md)bilgiler içerir.

## <a name="next-steps"></a>Sonraki adımlar

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)en son sürümünü indirin.