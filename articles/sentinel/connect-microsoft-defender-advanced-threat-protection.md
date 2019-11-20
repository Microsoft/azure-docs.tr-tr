---
title: Microsoft Defender ATP verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Microsoft Defender Gelişmiş tehdit koruması verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 19d496ebb61a3ceb47f69f661e30ab529dc64f3d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256753"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Microsoft Defender Gelişmiş tehdit koruması 'ndan uyarıları bağlama 


> [!IMPORTANT]
> Microsoft Defender Gelişmiş tehdit koruması günlüklerinin alımı Şu anda genel önizlemededir.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Tek bir tıklama ile [Microsoft Defender Gelişmiş tehdit koruması](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) Ile Azure Sentinel 'e uyarı akışı sağlayabilirsiniz. Bu bağlantı, Microsoft Defender Gelişmiş tehdit koruması 'ndan gelen uyarıları Azure Sentinel 'e akışla göndermenizi sağlar. 

## <a name="prerequisites"></a>Önkoşullar

- [Lisanslama sağlamasını doğrulama ve Microsoft Defender Gelişmiş tehdit koruması için ayarlamayı doğrulama](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)konusunda açıklandığı gibi etkinleştirilen Microsoft Defender Gelişmiş tehdit koruması için geçerli lisans. 
- Azure Sentinel kiracısında yönetici veya güvenlik yöneticisi olmanız gerekir.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Microsoft Defender Gelişmiş tehdit koruması 'na bağlanma

Microsoft Defender Gelişmiş tehdit koruması dağıtılırsa ve verileriniz geri alıyorsa, uyarılar Azure Sentinel 'e kolayca akışını sağlayabilir.


1. Azure Sentinel 'de **veri bağlayıcıları**' nı, **Microsoft Defender Gelişmiş tehdit koruması** kutucuğunu ve **bağlayıcı sayfasını aç**' ı seçin.
1. **Bağlan**'a tıklayın. 
1. Defender ATP uyarıları için Log Analytics ' de ilgili şemayı kullanmak için, **Securityalert** araması yapın ve **sağlayıcı adı** **mdadtp**olur.




## <a name="next-steps"></a>Sonraki adımlar
Bu belgede Microsoft Defender ATP 'yi Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats.md)başlayın.
