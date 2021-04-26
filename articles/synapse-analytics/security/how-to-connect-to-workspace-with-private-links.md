---
title: Özel bağlantıları kullanarak bir Synapse çalışma alanına bağlanma
description: Bu makalede, özel bağlantıları kullanarak Azure SYNAPSE çalışma alanınıza nasıl bağlanabileceğiniz anlatılmaktadır
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9782cce4165487b612c0295dc893d120ed043225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98218272"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links"></a>Özel bağlantıları kullanarak Azure Synapse çalışma alanınıza bağlanma

Bu makale, Azure SYNAPSE çalışma alanınızda özel bir uç nokta oluşturmayı öğretir. Daha fazla bilgi için bkz. [özel bağlantılar ve özel uç noktalar](../../private-link/index.yml) .

## <a name="step-1-register-network-resource-provider"></a>1. Adım: ağ kaynak sağlayıcısını kaydetme

Daha önce yapmadıysanız, ağ kaynak sağlayıcısını kaydedin. Kaynak sağlayıcısı kaydı, aboneliğinizi kaynak sağlayıcısıyla çalışacak şekilde yapılandırır. [Kayıt](../../azure-resource-manager/management/resource-providers-and-types.md)sırasında kaynak sağlayıcıları listesinden *Microsoft. Network* ' ü seçin. Ağ kaynak sağlayıcısı zaten kaydedilmişse adım 2 ' ye ilerleyin.

## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>2. Adım: Azure SYNAPSE çalışma alanınızı Azure portal açın

**Güvenlik** altında **Özel uç nokta bağlantısı** ' nı seçin. 
![Azure SYNAPSE çalışma alanını Azure portal içinde açın](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

Sonraki ekranda **+ Özel uç nokta**' ı seçin.

![Azure portal içinde özel uç nokta aç](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1a.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>3. Adım: aboneliğinizi ve bölge ayrıntılarınızı seçin

**Özel uç nokta oluştur** penceresindeki **temel bilgiler** sekmesinde, **aboneliğiniz** ve **kaynak grubunuz**' ı seçin. Oluşturmak istediğiniz özel uç noktaya bir **ad** verin. Özel uç noktanın oluşturulmasını istediğiniz **bölgeyi** seçin.

Özel uç noktalar bir alt ağda oluşturulur. Abonelik, kaynak grubu ve Seçili bölge özel uç nokta alt ağlarını filtreleyin. **İleri ' yi seçin: bittiğinde kaynak >** .
![Abonelik ve bölge ayrıntıları ' nı seçin 1](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>4. Adım: Azure SYNAPSE çalışma alanı ayrıntılarınızı seçin

**Kaynak** sekmesinde **Dizinimde bir Azure kaynağına bağlan '** ı seçin. Azure SYNAPSE çalışma alanınızı içeren **aboneliği** seçin. Azure SYNAPSE çalışma alanına özel uç noktalar oluşturmak için **kaynak türü** *Microsoft. SYNAPSE/çalışma alanlardır*.

**Kaynak** olarak Azure SYNAPSE çalışma alanınızı seçin. Her Azure SYNAPSE çalışma alanı için özel bir uç nokta oluşturabileceğiniz üç **hedef alt kaynağı** vardır: SQL, SqlOnDemand ve dev.

Ileri ' yi seçin: Kurulum 'un bir sonraki bölümüne ilerlemek için **yapılandırma>** .
![Abonelik ve bölge ayrıntıları seçme 2](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

**Yapılandırma** sekmesinde, Özel uç noktanın oluşturulması gereken **sanal ağı** ve **alt ağı** seçin. Ayrıca özel uç nokta ile eşleşen bir DNS kaydı oluşturmanız gerekir.

Özel uç noktanızı özel bir DNS bölgesiyle bütünleştirmek için özel **DNS bölgesi Ile tümleştir** için **Evet** ' i seçin. Microsoft Azure Sanal Ağ ilişkili özel bir DNS bölgeniz yoksa, yeni bir özel DNS bölgesi oluşturulur. Tamamlandığında **gözden geçir + oluştur** ' u seçin.

![Abonelik ve bölge ayrıntıları seçme 3](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Dağıtım tamamlandığında, Azure SYNAPSE çalışma alanınızı Azure portal açın ve **Özel uç nokta bağlantıları**' nı seçin. Özel uç noktayla ilişkili yeni özel uç nokta ve özel uç nokta bağlantısı adı gösterilir.

![Abonelik ve bölge ayrıntıları 4 ' ü seçin](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Sonraki adımlar

[Yönetilen çalışma alanı sanal ağı](./synapse-workspace-managed-vnet.md) hakkında daha fazla bilgi edinin

[Yönetilen özel uç noktalar](./synapse-workspace-managed-private-endpoints.md) hakkında daha fazla bilgi edinin

[Veri kaynaklarınızda yönetilen özel uç noktalar oluşturun](./how-to-create-managed-private-endpoints.md)