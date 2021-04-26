---
title: Azure Purview Hesabı bağlama 
description: Bir Azure purview hesabını bir Synapse çalışma alanına bağlayın.
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ad9250d022f02db06c9c5251c1fccfbfb730af67
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567885"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Hızlı başlangıç: bir Azure purview hesabını bir Synapse çalışma alanına bağlama 


Bu hızlı başlangıçta bir Azure purview hesabını bir Synapse çalışma alanına kaydedecaksınız. Bu bağlantı, Azure purview varlıklarını keşfetmenize ve SYNAPSE özellikleri aracılığıyla bunlarla etkileşime geçmesini sağlar. 

SYNAPSE içinde aşağıdaki görevleri gerçekleştirebilirsiniz: 
- Anahtar sözcüklere göre varlıkları bulmak için üstteki arama kutusunu kullanın 
- Meta veriler, kökenini, ek açıklamalar temelinde verileri anlayın 
- Bağlı hizmetler veya tümleştirme veri kümeleriyle bu verileri çalışma alanınıza bağlayın 
- Bu veri kümelerini SYNAPSE Apache Spark, SYNAPSE SQL ve veri akışı ile çözümleyin 

## <a name="prerequisites"></a>Önkoşullar 
- [Azure purview hesabı](../../purview/create-catalog-portal.md) 
- [SYNAPSE çalışma alanı](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Bir Synapse çalışma alanında oturum açın 

 [https://web.azuresynapse.net](https://web.azuresynapse.net)Çalışma alanınıza gidin ve oturum açın. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Azure purview hesabını bağlama izinleri 

- Bir Azure purview hesabını bir Synapse çalışma alanına bağlamak için Azure portal ıAM 'den SYNAPSE çalışma alanında **katkıda** bulunan bir rol gerekir ve bu Azure purview hesabına erişmeniz gerekir. Daha fazla ayrıntı için bkz. [Azure purview izinleri](../../purview/catalog-permissions.md).

## <a name="connect-an-azure-purview-account"></a>Azure Purview Hesabı bağlama  

- SYNAPSE çalışma alanında   ->  **Azure purview**'ı Yönet ' e gidin. **Bir purview hesabına Bağlan**' ı seçin. 
- **Azure aboneliği** arasından seçim yapabilir veya **el ile girebilirsiniz**. **Azure aboneliğinden** erişiminiz olan hesabı seçebilirsiniz. 
- Bağlandıktan sonra, **Azure purview hesabı** sekmesinden purview hesabının adını görmeniz gerekir. 
- Veri aramak için SYNAPSE çalışma alanının en üst merkezinde bulunan arama çubuğunu kullanabilirsiniz. 

## <a name="nextsteps"></a>Sonraki adımlar 

[Azure SYNAPSE varlıklarını Azure purview 'da kaydetme ve tarama](../../purview/register-scan-azure-synapse-analytics.md)

[Azure Purview’ı kullanarak Synapse’te verileri keşfedin, bağlayın ve inceleyin](how-to-discover-connect-analyze-azure-purview.md)   
