---
title: Microsoft 365 Defender verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Microsoft 365 Defender 'dan Azure Sentinel 'e olayları, uyarıları ve Ham olay verilerini nasıl alabileceğinizi öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 6500805a4dc7e26f5e1bc601df9ea78279ae17e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709351"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Microsoft 365 Defender 'dan Azure Sentinel 'e veri bağlama

> [!IMPORTANT]
>
> **Microsoft 365 Defender** daha önce **Microsoft tehdit koruması** veya **MTP** olarak biliniyordu.
>
> **Uç nokta Için Microsoft Defender, daha** önce **Microsoft Defender Gelişmiş tehdit koruması** veya **mdadtp** olarak biliniyordu.
>
> Eski adların bir süre içinde hala kullanımda olduğunu görebilirsiniz.

## <a name="background"></a>Arka Plan

Azure Sentinel 'in olay tümleştirmesi ile [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) Bağlayıcısı, tüm M365D olaylarını ve uyarılarını Azure Sentinel 'e akışını ve olayların her iki Portal arasında eşitlenmiş olmasını sağlar. M365D olayları, tüm uyarılarını, varlıklarını ve diğer ilgili bilgileri içerir ve M365D's Bileşen Hizmetleri **Microsoft Defender for Endpoint**, **kimlik için** microsoft Defender, **Office 365 için** Microsoft Defender ve **Microsoft Cloud App Security**.

Bağlayıcı Ayrıca, Microsoft Defender uç noktası için **Gelişmiş** arama olaylarını Azure Sentinel 'e aktarmanızı sağlar. böylece, ek Öngörüler sağlamak için MDE gelişmiş arama sorgularını Azure Sentinel 'e, MDE Ham olay verileriyle birlikte zengin Sentinel uyarılara kopyalayabilir ve Log Analytics daha fazla bekletme ile günlüğe kaydedebilirsiniz.

Olay tümleştirme hakkında daha fazla bilgi için bkz. [Azure Sentinel ile Microsoft 365 Defender tümleştirmesi](microsoft-365-defender-sentinel-integration.md).

> [!IMPORTANT]
>
> Microsoft 365 Defender Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

## <a name="prerequisites"></a>Önkoşullar

- Microsoft 365 Defender için [Microsoft 365 Defender önkoşulları](/microsoft-365/security/mtp/prerequisites)bölümünde açıklandığı gibi geçerli bir lisansa sahip olmanız gerekir. 

- Azure Active Directory bir **genel yönetici** veya **Güvenlik Yöneticisi** olmanız gerekir.

## <a name="connect-to-microsoft-365-defender"></a>Microsoft 365 Defender 'a bağlanma

1. Azure Sentinel 'de **veri bağlayıcıları**' nı seçin, Galeriden **Microsoft 365 Defender (Önizleme)** öğesini seçin ve **bağlayıcı sayfasını aç**' ı seçin.

1. Yapılandırma **olayları & uyarıları** bölümünde **yapılandırma** altında **olayları Bağlan & uyarılar** düğmesine tıklayın.

1. Olayların çoğaltılmasını önlemek için, **Bu ürünlerin tüm Microsoft olay oluşturma kurallarını** kapat etiketli onay kutusunu işaretlemeniz önerilir.

    > [!NOTE]
    > Microsoft 365 Defender bağlayıcısını etkinleştirdiğinizde, tüm M365D Components bağlayıcıları (Bu makalenin başlangıcında bahsedilen) otomatik olarak arka plana bağlanır. Bileşenlerin bağlayıcılarından birinin bağlantısını kesmek için öncelikle Microsoft 365 Defender bağlayıcısının bağlantısını kesmeniz gerekir.

1. M365 Defender olay verilerini sorgulamak için sorgu penceresinde aşağıdaki ifadeyi kullanın:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Uç nokta için Microsoft Defender 'dan gelişmiş arama olayları toplamak istiyorsanız, ilgili gelişmiş arama tablolarından aşağıdaki olay türleri toplanabilir.

    1. Tabloların onay kutularını toplamak istediğiniz olay türleriyle işaretleyin:

       | Tablo adı | Olay türü |
       |-|-|
       | DeviceInfo | Makine bilgileri (işletim sistemi bilgileri dahil) |
       | Devicenetworkınfo | Makinelerin ağ özellikleri |
       | DeviceProcessEvents | İşlem oluşturma ve ilgili olaylar |
       | DeviceNetworkEvents | Ağ bağlantısı ve ilgili olaylar |
       | DeviceFileEvents | Dosya oluşturma, değiştirme ve diğer dosya sistemi olayları |
       | DeviceRegistryEvents | Kayıt defteri girişlerini oluşturma ve değiştirme |
       | DeviceLogonEvents | Oturum açma işlemleri ve diğer kimlik doğrulama olayları |
       | Deviceımageloadevents | DLL yükleme olayları |
       | DeviceEvents | Ek olay türleri |
       | Devicefilecertificateınfo | İmzalı dosyaların sertifika bilgileri |
       |

    1. **Değişiklikleri Uygula**' ya tıklayın.

    1. Log Analytics gelişmiş hunmiş tabloları sorgulamak için, sorgu penceresinde yukarıdaki listeden tablo adını girin.

## <a name="verify-data-ingestion"></a>Veri alımını doğrulama

Bağlayıcı sayfasındaki veri grafiği, verileri iade ettiğiniz anlamına gelir. Olayların, uyarıların ve olayların her biri için bir satır gösterdiğine ve olaylar hattının, etkin olan tüm tablolardaki olay hacmi toplamı olduğunu fark edeceksiniz. Bağlayıcıyı etkinleştirdikten sonra, daha belirli grafikler oluşturmak için aşağıdaki KQL sorgularını kullanabilirsiniz.

Gelen M365 Defender olaylarını grafik için aşağıdaki KQL sorgusunu kullanın:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Tek bir tabloya yönelik bir olay birimi grafiği oluşturmak için aşağıdaki KQL sorgusunu kullanın ( *Deviceevents* tablosunu seçtiğiniz gerekli tabloyla değiştirin):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

**Sonraki adımlar** sekmesinde, eklenen bazı yararlı çalışma kitapları, örnek sorgular ve analiz kuralı şablonları bulacaksınız. Bunları bir şekilde çalıştırabilir veya değiştirebilir ve kaydedebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Microsoft 365 Defender bağlayıcısını kullanarak Microsoft 365 Defender olaylarını tümleştirme ve uç nokta için Microsoft Defender 'daki olay verilerini Azure Sentinel 'e yönelik gelişmiş bir şekilde ele alma hakkında öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](./tutorial-detect-threats-built-in.md)başlayın.
