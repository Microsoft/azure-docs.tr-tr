---
title: Identity (eski adıyla Azure ATP) verileri için Microsoft Defender 'ı Azure Sentinel 'e bağlama | Microsoft Docs
description: Kimlik (eskiden Azure Gelişmiş tehdit koruması) (ATP) için Microsoft Defender 'dan günlükleri tek bir tıklama ile Azure Sentinel 'e akışa alma hakkında bilgi edinin.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7a26091d4985b6fdb17120c6fd70476a750c94a9
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714120"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Kimlik için Microsoft Defender 'daki verileri bağlama (eski adıyla Azure Gelişmiş tehdit koruması)

> [!IMPORTANT]
> Azure Sentinel 'de Identity Data Connector için Microsoft Defender Şu anda genel önizleme aşamasındadır.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu makalede, [kimlik Için Microsoft Defender](/azure-advanced-threat-protection/what-is-atp) 'daki güvenlik uyarılarının Azure Sentinel 'e nasıl akışının yapılacağı açıklanır. 

Güvenlik uyarılarına ek olarak sistem durumu uyarılarını iletmek için, Microsoft Defender 'ı bir Syslog sunucusu ile kimlik için tümleştirin. Daha fazla bilgi için bkz. [Microsoft Defender for Identity belgeleri](/defender-for-identity/setting-syslog). 

## <a name="prerequisites"></a>Önkoşullar

- Genel yönetici veya Güvenlik Yöneticisi izinlerine sahip Kullanıcı
- Kimlik için Microsoft Defender 'ın önizleme müşterisi olmanız ve kimlik ve Microsoft Cloud App Security için Microsoft Defender arasında tümleştirmeyi etkinleştirmeniz gerekir. Daha fazla bilgi için bkz. [kimlik tümleştirmesi Için Microsoft Defender](/cloud-app-security/mdi-integration).

## <a name="connect-to-microsoft-defender-for-identity"></a>Kimlik için Microsoft Defender 'a bağlanma

Ağınızda kimlik önizleme sürümünün Microsoft Defender 'ın [etkinleştirildiğinden](/azure-advanced-threat-protection/install-atp-step1)emin olun.
Kimlik için Microsoft Defender dağıtılırsa ve verileriniz geri alıyorsa şüpheli uyarılar Azure Sentinel 'e kolayca akışını sağlayabilir. Uyarıların Azure Sentinel 'e akışını başlatması 24 saate kadar sürebilir.


1. Kimlik için Microsoft Defender 'ı Azure Sentinel 'e bağlamak için öncelikle kimlik ve Microsoft Cloud App Security Microsoft Defender arasındaki tümleştirmeyi etkinleştirmeniz gerekir. Bunun nasıl yapılacağı hakkında bilgi için bkz. [kimlik tümleştirmesi Için Microsoft Defender](/cloud-app-security/mdi-integration).

1. Azure Sentinel 'de **veri bağlayıcıları** ' nı seçin ve ardından **Microsoft Defender for Identity (Önizleme)** kutucuğuna tıklayın.

1. Kimlik için Microsoft Defender 'daki uyarıların otomatik olarak Azure Sentinel 'de otomatik olarak olay oluşturmasını isteyip istemediğinizi seçebilirsiniz. Olayları **Oluştur** altında, bağlı güvenlik hizmetinde oluşturulan uyarılardan olayları otomatik olarak oluşturan varsayılan analitik kuralı etkinleştirmek için **Etkinleştir** ' i seçin. Daha sonra bu kuralı **analiz** ve ardından **etkin kurallar** altında düzenleyebilirsiniz.

1. **Bağlan**'a tıklayın.

1. Kimlik uyarıları için Microsoft Defender Log Analytics ' de ilgili şemayı kullanmak için, **Securityalert** aratın.

> [!NOTE]
> Uyarılar 30 KB 'tan büyükse Azure Sentinel, uyarılarda varlıklar alanını görüntülemeyi durduruyor.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, kimlik için Microsoft Defender 'ı Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
