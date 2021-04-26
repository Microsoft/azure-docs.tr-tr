---
title: Veri kaynağı sonuçlarınıza bağlanmak için yönetilen bir özel uç nokta oluşturma
description: Bu makalede, Azure SYNAPSE çalışma alanından veri kaynaklarınıza yönetilen bir özel uç nokta oluşturma hakkında öğretir.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0309b4c96b2ae25eb568e390717ba76cfd84fa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461317"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source"></a>Veri kaynağınıza yönetilen özel uç nokta oluşturma

Bu makalede, Azure 'daki veri kaynağınıza yönetilen bir özel uç nokta oluşturma hakkında öğretir. Daha fazla bilgi için bkz. [yönetilen özel uç noktalar](./synapse-workspace-managed-private-endpoints.md) .

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>1. Adım: Azure SYNAPSE çalışma alanınızı Azure portal açın

Azure SYNAPSE Studio 'dan veri kaynağınıza yönetilen bir özel uç nokta oluşturabilirsiniz. Azure portal ' deki **genel bakış** sekmesini seçin ve Başlarken bölümündeki Open SYNAPSE Studio kartında **Aç** ' ı seçin.

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>2. Adım: SYNAPSE Studio 'da yönetilen sanal ağlar sekmesine gidin

Azure SYNAPSE Studio 'da sol gezinti bölmesindeki **Yönet** sekmesini seçin. **Yönetilen özel uç noktaları** ' nı seçin ve **+ Yeni**' yi seçin.
![Yeni yönetilen özel uç nokta oluştur](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>3. Adım: veri kaynağı türünü seçin

Veri kaynağı türünü seçin. Bu durumda, hedef veri kaynağı bir ADLS 2. hesabıdır. **Devam**’ı seçin.
![Hedef veri kaynağı türü seçin](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>4. Adım: veri kaynağıyla ilgili bilgileri girin

Sonraki pencerede, veri kaynağıyla ilgili bilgileri girin. Bu örnekte, bir ADLS 2. hesabına yönetilen özel uç nokta oluşturacağız. Yönetilen özel uç nokta için bir **ad** girin. Bir **Azure aboneliği** ve **depolama hesabı adı** sağlayın. **Oluştur**’u seçin.
![Hedef veri kaynağı ayrıntılarını girin](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>5. Adım: yönetilen özel uç noktanızın başarıyla oluşturulduğunu doğrulama

İsteği gönderdikten sonra, durumunu görürsünüz. Yönetilen özel uç noktanızın başarıyla oluşturulmasını doğrulamak için *sağlama durumunu* kontrol edin. Sağlama durumunu güncelleştirmek için 1 dakika beklemeniz ve **Yenile** ' yi seçmeniz gerekebilir. ADLS 2. hesabının yönetilen özel uç noktasının başarıyla oluşturulduğunu görebilirsiniz.

*Onay durumunun* *bekliyor* olduğunu da görebilirsiniz. Hedef kaynağın sahibi özel uç nokta bağlantı isteğini onaylayabilir veya reddedebilir. Sahip özel uç nokta bağlantı isteğini onayladığında, özel bir bağlantı oluşturulur. Reddedilirse, özel bir bağlantı kurulmaz.
![Yönetilen özel uç nokta oluşturma isteği durumu](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Sonraki adımlar

[Yönetilen özel uç noktalar](./synapse-workspace-managed-private-endpoints.md) hakkında daha fazla bilgi edinin