---
title: Azure Güvenlik Merkezi verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Azure Güvenlik Merkezi verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240214"
---
# <a name="connect-data-from-azure-security-center"></a>Azure Güvenlik Merkezi 'nden verileri bağlama





Azure Sentinel, uyarıları [Azure Güvenlik Merkezi](../security-center/security-center-intro.md) ' nden bağlamanıza ve Azure Sentinel 'e akışla göndermenizi sağlar. 

## <a name="prerequisites"></a>Önkoşullar

- Uyarıları Azure Güvenlik Merkezi 'nden dışarı aktarmak istiyorsanız, abone olduğunuz abonelik üzerinde bir katkıda bulunan olması gerekir.

- Abonelikte çalışan [Azure Güvenlik Merkezi Standart katmanının](../security-center/security-center-pricing.md) olması gerekir. Aksi takdirde, [aboneliğinizi standart sürümüne yükseltin](https://azure.microsoft.com/pricing/details/security-center/).

- Bağlanmak istediğiniz her abonelik üzerinde genel yönetici veya Güvenlik Yöneticisi izinlerine sahip bir kullanıcıyla oturum açmalısınız.


## <a name="connect-to-azure-security-center"></a>Azure Güvenlik Merkezi 'ne bağlanma

1. Azure Sentinel 'de, **veri bağlayıcıları** ' nı seçin ve ardından **Azure Güvenlik Merkezi** kutucuğuna tıklayın.

1. Sağ tarafta, uyarıları Azure Sentinel 'e akışa almak istediğiniz her aboneliğin yanındaki **Bağlan** ' a tıklayın. Azure Sentinel 'e uyarı akışı sağlamak için her bir aboneliği Azure Güvenlik Merkezi Standart katmanına yükseltdiğinizden emin olun.

1. Azure Güvenlik Merkezi 'ndeki uyarıların otomatik olarak Azure Sentinel 'de olay oluşturmasını isteyip istemediğinizi seçebilirsiniz. Olayları **Oluştur** altında, bağlı güvenlik hizmetinde oluşturulan uyarılardan olayları otomatik olarak oluşturan varsayılan analitik kuralı etkinleştirmek için **Etkinleştir** ' i seçin. Daha sonra bu kuralı **analiz** ve ardından **etkin kurallar**altında düzenleyebilirsiniz.

3. **Bağlan**'a tıklayın.

4. Azure Güvenlik Merkezi uyarıları için Log Analytics ilgili şemayı kullanmak için, **Securityalert**aratın.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Azure Güvenlik Merkezi 'ni Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
