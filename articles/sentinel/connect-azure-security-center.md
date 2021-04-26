---
title: Azure Defender verilerini Azure Sentinel 'e bağlama
description: Azure Defender uyarılarını Azure Güvenlik Merkezi ' nden bağlamayı ve Azure Sentinel 'e akışını öğrenin.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: bb188aa79015c2123b9d9d8b6baf277dfadf2f9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633053"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Azure Güvenlik Merkezi 'nden Azure Defender uyarı verilerini bağlama

Azure [Güvenlik Merkezi](../security-center/security-center-introduction.md) 'Nden Azure Defender uyarılarını almak ve bunları Azure Sentinel 'e aktarmak Için Azure Defender uyarı bağlayıcısını kullanın. 

## <a name="prerequisites"></a>Önkoşullar

- Kullanıcı, akış yaptığınız günlüklerin aboneliğinde güvenlik okuyucusu rolüne sahip olmalıdır.

- Azure Güvenlik Merkezi 'nde Azure Defender 'ı etkinleştirmeniz gerekir. (Standart katman artık mevcut değildir ve artık bir lisans gereksinimi değildir.)

## <a name="connect-to-azure-defender"></a>Azure Defender 'a bağlanma

1. Azure Sentinel 'de, gezinti menüsünden **veri bağlayıcıları** ' nı seçin.

1. Veri bağlayıcıları galerisinden **ASC 'Den Azure Defender uyarıları** ' nı seçin (yine de Azure Güvenlik Merkezi olarak adlandırılabilir) ve **bağlayıcı sayfası aç** düğmesine tıklayın.

1. **Yapılandırma** altında, uyarıları Azure Sentinel 'e aktarmak istediğiniz her aboneliğin yanındaki **Bağlan** ' a tıklayın. Bağlan düğmesi yalnızca gerekli izinleriniz varsa kullanılabilir.

1. Azure Defender 'daki uyarıların Azure Sentinel 'de otomatik olarak olay oluşturmasını isteyip istemediğinizi seçebilirsiniz. **Olayları oluştur** altında, otomatik olarak uyarılardan olayları oluşturan varsayılan analiz kuralını açmak için **etkin** ' i seçin. Daha sonra bu kuralı, **etkin kurallar** sekmesinde **analiz** altında düzenleyebilirsiniz.

1. Azure Defender uyarıları için Log Analytics ilgili şemayı kullanmak için, **Securityalert** aratın.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede Azure Defender 'ı Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
