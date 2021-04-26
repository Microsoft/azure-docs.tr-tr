---
title: Çevre 81 verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Çevre 81 verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: e70bfd12b018e785e7b3b91e098c99fea1101c04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093106"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>Çevre 81 etkinlik günlüklerinizi Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Azure Sentinel 'deki çevre 81 veri Bağlayıcısı Şu anda genel önizlemededir.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu makalede, [çevre 81 etkinlik günlükleri](https://www.perimeter81.com/) gerecinizi Azure Sentinel 'e nasıl bağlayabileceğiniz açıklanır. Çevre 81 etkinlik günlükleri Bağlayıcısı, çevre 81 verilerinizi Azure Sentinel 'e kolayca getirmenize olanak tanır; böylece, çalışma kitaplarında görüntüleyebilmeniz için, özel uyarılar oluşturmak için kullanabilir ve araştırmayı geliştirebilirsiniz.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanında okuma ve yazma izinlerine sahip olmanız gerekir.

- Çalışma alanı için paylaşılan anahtarlar için okuma izninizin olması gerekir.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>Çevre 81 etkinlik günlüklerini yapılandırma ve bağlama

Çevre 81 etkinlik günlükleri, günlükleri doğrudan Azure Sentinel 'e tümleştirebilir ve dışarı aktarabilir.

1. Azure Sentinel portalında, gezinti menüsünde **veri bağlayıcıları** ' na tıklayın.

1. Galeriden **çevre 81 etkinlik günlükleri** ' ni seçin ve ardından **bağlayıcı sayfası aç** düğmesine tıklayın.

1. Çevre 81 etkinlik günlükleri Bağlayıcısı sayfasında, **çalışma alanı kimliği** ve **birincil anahtarı** kopyalayın ve [burada belirtildiği gibi](https://support.perimeter81.com/hc/en-us/articles/360012680780)çevre 81 ' ye yapıştırın.

1. Yönergeleri tamamladıktan sonra, bağlantılı veri türlerini Azure Sentinel bağlayıcı sayfasında görürsünüz.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler **customlogs** Perimeter81_CL altındaki **günlüklerde** görüntülenir  -  .

Günlüklerinizin görünmeye başlaması 20 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, çevre 81 etkinlik günlüklerini Azure Sentinel 'e bağlamayı öğrendiniz. Bu veri bağlayıcısında yerleşik olarak bulunan özelliklerden tam olarak yararlanabilmek için, veri bağlayıcı sayfasındaki **sonraki adımlar** sekmesine tıklayın. Faydalı bilgiler bulmaya başlayabilmeniz için, kullanıma hazırlanmış bir çalışma kitabı ve bazı örnek sorgular bulacaksınız.

Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
