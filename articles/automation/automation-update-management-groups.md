---
title: Azure Güncelleştirme Yönetimi ile dinamik grupları kullanma
description: Dinamik grupların Güncelleştirme Yönetimi ile nasıl çalıştığını açıklar
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: edc4384be0f1dc73f2e7e098114080d304d92ce8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377752"
---
# <a name="use-dynamic-groups-with-update-management"></a>Güncelleştirme Yönetimi ile dinamik grupları kullanma

Güncelleştirme Yönetimi, güncelleştirme dağıtımları için Azure veya Azure olmayan VM 'Lerin dinamik bir grubunu hedefleyebilme olanağı sağlar. Bu gruplar dağıtım zamanında değerlendirilir, böylece, makineleri eklemek için dağıtımınızı düzenlemeniz gerekmez.

## <a name="azure-machines"></a>Azure makineleri

Bu gruplar bir sorgu tarafından tanımlanır, bir güncelleştirme dağıtımı başladığında, o grubun üyeleri değerlendirilir. Dinamik Gruplar klasik VM 'Ler ile çalışmaz. Sorgunuzu tanımlarken, dinamik grubu doldurmak için aşağıdaki öğeler birlikte kullanılabilir:

* Abonelik
* Kaynak grupları
* Konumlar
* Etiketler

![Grupları seçin](./media/automation-update-management/select-groups.png)

Dinamik bir grubun sonuçlarını önizlemek için **Önizleme** düğmesine tıklayın. Bu önizlemede, bu sırada grup üyeliği gösterilmektedir. Bu örnekte, Tag **rolüne** sahip makineleri **BackEndServer**'a eşit olarak arıyor. Bu etiket daha fazla makineye eklendiyse, bu Etiketler bu gruba karşı gelecekteki tüm dağıtımlara eklenecektir.

![Önizleme grupları](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Azure dışı makineler

Azure dışı makinelerde, bilgisayar grupları olarak da bilinen kayıtlı aramalar, dinamik grubu oluşturmak için kullanılır. Kayıtlı bir aramanın nasıl oluşturulduğunu öğrenmek için bkz. [bilgisayar grubu oluşturma](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Grubunuz oluşturulduktan sonra, kaydedilen aramalar listesinden bunu seçebilirsiniz. Kaydedilen aramada bilgisayarları o anda önizlemek için **Önizleme** ' ye tıklayın.

![Grupları seçin](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Sonraki adımlar

Dinamik bir grup oluşturduktan sonra [bir güncelleştirme dağıtımı oluşturabilirsiniz](automation-tutorial-update-management.md)
