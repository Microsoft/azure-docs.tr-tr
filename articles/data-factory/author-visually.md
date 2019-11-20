---
title: Azure Data Factory 'de görsel yazma
description: Azure Data Factory 'de görsel yazma kullanmayı öğrenin
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 71f0e283c9609e949cc9b09f20a16c6f5c9c3e50
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681607"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory 'de görsel yazma

Azure Data Factory Kullanıcı arabirimi deneyimi (UX), herhangi bir kod yazmak zorunda kalmadan veri fabrikanızın kaynaklarını görsel olarak yazmanızı ve dağıtmanızı sağlar. Etkinlikleri bir işlem hattı tuvaline sürükleyebilir, test çalıştırmaları gerçekleştirebilir, tekrarlayarak hata ayıklayın ve işlem hattı çalıştırmalarınızın dağıtımını yapabilir ve izleyebilirsiniz.

## <a name="authoring-canvas"></a>Yazı tuvali

**Yazma tuvalini**açmak için kalem simgesine tıklayın. 

![Yazı tuvali](media/author-visually/authoring-canvas.png)

Burada, fabrikanızı oluşturan işlem hatları, Etkinlikler, veri kümeleri, bağlı hizmetler, veri akışları, Tetikleyiciler ve tümleştirme çalışma zamanları yazacaktır. Yazma tuvali 'ni kullanarak bir işlem hattı oluşturmaya başlamak için bkz. [kopyalama etkinliğini kullanarak verileri kopyalama](tutorial-copy-data-portal.md). 

Varsayılan görsel yazma deneyimi, Data Factory hizmetiyle doğrudan çalışır. Veri Fabrikası işlem hatlarınız üzerinde çalışma için kaynak denetimi ve işbirliği yapmak üzere Azure Repos git veya GitHub tümleştirmesi de desteklenir. Bu yazma deneyimleri arasındaki farklar hakkında daha fazla bilgi edinmek için [Azure Data Factory Içindeki kaynak denetimi](source-control.md)konusuna bakın.

## <a name="expressions-and-functions"></a>İfadeler ve işlevler

Azure Data Factory ' de birçok özellik belirtmek için, statik değerler yerine ifadeler ve işlevler kullanılabilir.

Bir özellik değeri için bir ifade belirtmek üzere, **dinamik Içerik Ekle** ' yi seçin veya alana odaklanırken **alt + P** ' ye tıklayın.

![Dinamik Içerik Ekle](media/author-visually/dynamic-content-1.png)

Bu, desteklenen sistem değişkenlerinden, etkinlik çıktısından, işlevlerden ve Kullanıcı tarafından belirtilen değişkenlerle ya da parametrelerden ifade oluşturabileceğiniz **Data Factory Ifade oluşturucusunu** açar. 

![İfade oluşturucusu](media/author-visually/dynamic-content-2.png)

İfade dili hakkında daha fazla bilgi için, [Azure Data Factory Içindeki ifadeler ve işlevler](control-flow-expression-language-functions.md)bölümüne bakın.

## <a name="provide-feedback"></a>Geri bildirimde bulunma

Özellikler hakkında yorum yapmak veya araçla ilgili sorunları bildirmek için **geri bildirim** ' ı seçin:

![Geri Bildirim](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Sonraki adımlar

İşlem hatlarını izleme ve yönetme hakkında daha fazla bilgi edinmek için bkz. işlem [hatlarını programlama yoluyla izleme ve yönetme](monitor-programmatically.md).
