---
title: F5 BIG-IP verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: F5 büyük IP verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 17c31bbe54326962c26ab53f702cbd28d1e36c6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092800"
---
# <a name="connect-your-f5-big-ip-appliance"></a>F5 BIG-IP gerecinizi bağlama 

F5 BIG-IP Bağlayıcısı, çalışma kitaplarını görüntülemek, özel uyarılar oluşturmak ve araştırmayı geliştirmek için tüm F5 büyük IP günlüklerinizi Azure Sentinel 'e kolayca bağlamanıza olanak tanır. Bu, kuruluşunuzun ağı hakkında daha ayrıntılı bilgi verir ve güvenlik işlemi yeteneklerini geliştirir. F5 BIG-IP ve Azure Sentinel arasındaki tümleştirme REST API kullanır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="configure-and-connect-f5-big-ip"></a>F5 BIG-IP ' i yapılandırma ve bağlama 

F5 BIG-IP, günlükleri doğrudan Azure Sentinel 'e tümleştirebilir ve dışarı aktarabilir.

1. Azure Sentinel portalında, **veri bağlayıcıları** ' na tıklayın ve **F5 BIG-IP** ' yi seçin ve ardından **bağlayıcı sayfası**' nı açın. 
1. F5 BIG-IP 'nizi bağlamak için sistemin API uç noktasına bir JSON bildirimi nakletmeniz gerekir. Bunun nasıl yapılacağı hakkında yönergeler için bkz. [F5 BIG-IP 'Yi Azure Sentinel Ile tümleştirme](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. F5 BIG-IP bağlayıcı sayfasında, çalışma alanı KIMLIĞI ve birincil anahtarı kopyalayın ve [akış verileri ' ni Azure Log Analytics 'e](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)göre gösterildiği gibi yapıştırın.
1. F5 BIG-IP yönergelerini tamamladıktan sonra, Azure Sentinel bağlayıcı sayfasında, bağlı veri türlerini görürsünüz.
1. F5 BIG-IP olayları için Log Analytics ilgili şemayı kullanmak için **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL** ve **F5Telemetry_ASM_CL** aratın.


## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir. 



## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, F5 BIG-IP 'yi Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .


