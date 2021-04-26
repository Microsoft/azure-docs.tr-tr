---
title: include dosyası
description: include dosyası
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026460"
---
Azure Batch çalıştıran bir görev, çalıştırıldığında çıktı verileri üretebilir. Görev çıktısı verilerinin genellikle işteki diğer görevler tarafından alınması, işi yürüten istemci uygulaması veya her ikisinin de saklanması gerekir. Görevler, bir Batch işlem düğümünün dosya sistemine çıkış verileri yazar, ancak düğüm, havuzdan ayrıldığında veya düğüm havuzdan ayrıldığında, düğümdeki tüm veriler kaybedilir. Görevler, görev tarafından oluşturulan dosyaların silindiği bir dosya saklama süresine de sahip olabilir. Bu nedenlerden dolayı, daha sonra ihtiyaç duyacağınız görev çıkışının [Azure depolama](../articles/storage/index.yml)gibi bir veri deposuna kalıcı olması önemlidir.

Batch 'teki depolama hesabı seçenekleri için bkz. [Batch hesapları ve Azure depolama hesapları](../articles/batch/accounts.md#azure-storage-accounts).