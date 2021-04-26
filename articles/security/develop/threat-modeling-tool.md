---
title: Microsoft Threat Modeling Tool genel bakış-Azure
description: Tehdit modelleme işlemi dahil olmak üzere araçla çalışmaya başlama hakkında bilgi içeren Microsoft Threat Modeling Tool genel bakış.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 02/16/2017
ms.openlocfilehash: 6a637dc431b0f9cd8c40cfa1c048559a56a6b97f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518065"
---
# <a name="microsoft-threat-modeling-tool"></a>Microsoft Threat Modeling Tool

Threat Modeling Tool, Microsoft Security Development Lifecycle (SDL) temel öğesidir. Yazılım mimarlarının olası güvenlik sorunlarını daha erken belirlemesine ve hafifletmesine ve bu sorunların çözülmesi için yüksek maliyetli olmasına olanak sağlar. Sonuç olarak, toplam geliştirme maliyetini büyük ölçüde azaltır. Ayrıca, tehdidi güvenlik dışı uzmanlar ile tasarlıyoruz, tehdit modellerini oluşturma ve çözümleme konusunda açık yönergeler sunarak tüm geliştiriciler için tehdit modellemesini kolaylaştırın. 

Araç şunları yapmasını sağlar:

* Sistemlerinin güvenlik tasarımı hakkında iletişim kurun
* Kanıtlanmış bir metodolojiyi kullanarak olası güvenlik sorunları için bu tasarımları çözümleyin
* Güvenlik sorunları için azaltıcı etkenleri önerin ve yönetin

İşte birkaç araç ve yenilikler aşağıda verilmiştir:

* **Otomasyon:** Model çizilirken kılavuz ve geri bildirim
* **Öğe başına ilerleme:** Tehditler ve azaltmaları Kılavuzlu analizler
* **Raporlama:** Doğrulama aşamasında güvenlik etkinlikleri ve test etme
* **Benzersiz metodolojisi:** Kullanıcıların tehditleri daha iyi görselleştirmesini ve anlamasını sağlar
* **Geliştiriciler Için tasarlanan ve yazılımda ortalanmış:** birçok yaklaşım, varlıklar veya saldırganlar üzerinde ortalanır. Yazılımda ortalıyoruz. Tüm yazılım geliştiricilerinin ve mimarlarının, yazılım mimarisine yönelik resim çizme gibi tüm yazılım geliştiricileri ve mimarlarının tanıdık olduğu etkinlikler üzerinde oluşturacağız
* **Tasarım analizine odaklanın:** "Tehdit modellemesi" terimi, bir gereksinimlere veya tasarım analizi tekniine başvurabilir. Bazen, ikisinin karmaşık bir Blend anlamına gelir. Tehdit modellemeye Microsoft SDL yaklaşımı, odaklanmış bir tasarım analizi tekniğidir

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki tabloda Threat Modeling Tool kullanmaya başlamanızı sağlamak için önemli bağlantılar yer almaktadır: Ayrıca bkz: [sistem gereksinimleri](threat-modeling-tool-releases.md)

| Adım  | Description                                                                                   |
| ----- | --------------------------------------------------------------------------------------------- |
| **1** | [Threat Modeling Tool indirin](https://aka.ms/threatmodelingtool)                                |
| **2** | [Başlarken kılavuzumuzu okuyun](threat-modeling-tool-getting-started.md)    |
| **3** | [Özelliklerle ilgili bilgi edinin](threat-modeling-tool-feature-overview.md)   |
| **4** | [Oluşturulan tehdit kategorileri hakkında bilgi edinin](threat-modeling-tool-threats.md)   |
| **5** | [Oluşturulan tehditlere yönelik azaltmaları bulun](threat-modeling-tool-mitigations.md) |

## <a name="resources"></a>Kaynaklar

Günümüzde tehdit modellemesi ile ilgili olan birkaç eski makale aşağıda verilmiştir:

* [Tehdit modellemesinin önemi hakkında makale](/archive/msdn-magazine/2009/january/security-briefs-getting-started-with-the-sdl-threat-modeling-tool)
* [Güvenilir bilgi Işlem tarafından yayımlanan Eğitim](https://www.microsoft.com/download/details.aspx?id=16420)

Threat Modeling Tool uzmanların ne yaptığını inceleyin:

* [Tehditler Yöneticisi](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Simone Curzi güvenlik blogu](https://simoneonsecurity.com/)