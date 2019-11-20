---
title: F5 BIG-IP verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: F5 büyük IP verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: rkarlin
ms.openlocfilehash: ff5a8f940348c36ca2a19fb31a173b2dfc4f4d6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514140"
---
# <a name="connect-your-f5-big-ip-appliance"></a>F5 BIG-IP gerecinizi bağlama 

> [!IMPORTANT]
> Azure Sentinel 'de F5 BIG-IP veri Bağlayıcısı Şu anda genel önizlemededir.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

F5 BIG-IP Bağlayıcısı, çalışma kitaplarını görüntülemek, özel uyarılar oluşturmak ve araştırmayı geliştirmek için tüm F5 büyük IP günlüklerinizi Azure Sentinel 'e kolayca bağlamanıza olanak tanır. Bu, kuruluşunuzun ağı hakkında daha ayrıntılı bilgi verir ve güvenlik işlemi yeteneklerini geliştirir. F5 BIG-IP ve Azure Sentinel arasındaki tümleştirme REST API kullanır.


> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="configure-and-connect-f5-big-ip"></a>F5 BIG-IP ' i yapılandırma ve bağlama 

F5 BIG-IP, günlükleri doğrudan Azure Sentinel 'e tümleştirebilir ve dışarı aktarabilir.

1. Azure Sentinel portalında, **veri bağlayıcıları** ' na tıklayın ve **F5 BIG-IP** ' yi seçin ve ardından **bağlayıcı sayfası**' nı açın. 
1. F5 BIG-IP 'nizi bağlamak için sistemin API uç noktasına bir JSON bildirimi nakletmeniz gerekir. Bunun nasıl yapılacağı hakkında yönergeler için bkz. [F5 BIG-IP 'Yi Azure Sentinel Ile tümleştirme](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. F5 BIG-IP bağlayıcı sayfasında, çalışma alanı KIMLIĞI ve birincil anahtarı kopyalayın ve [akış verileri ' ni Azure Log Analytics 'e](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)göre gösterildiği gibi yapıştırın.
1. F5 BIG-IP yönergelerini tamamladıktan sonra, Azure Sentinel bağlayıcı sayfasında, bağlı veri türlerini görürsünüz.
1. F5 BIG-IP olayları için Log Analytics ilgili şemayı kullanmak için, **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL**ve **F5Telemetry_ASM_CL**aratın.


## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünene kadar 20 dakikadan bu kadar bir zaman çıkabilir. 



## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, F5 BIG-IP 'yi Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.

