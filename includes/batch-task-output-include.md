---
title: include dosyası
description: include dosyası
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7ba4c90811bd8051ed9c307d9f9fa33e08e69dc7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "67188724"
---
Azure Batch çalıştıran bir görev, çalıştırıldığında çıktı verileri üretebilir. Görev çıktısı verilerinin genellikle işteki diğer görevler tarafından alınması, işi yürüten istemci uygulaması veya her ikisinin de saklanması gerekir. Görevler, bir Batch işlem düğümünün dosya sistemine çıkış verileri yazar, ancak düğüm, havuzdan ayrıldığında veya düğüm havuzdan ayrıldığında, düğümdeki tüm veriler kaybedilir. Görevler, görev tarafından oluşturulan dosyaların silindiği bir dosya saklama süresine de sahip olabilir. Bu nedenlerden dolayı, daha sonra ihtiyaç duyacağınız görev çıkışının [Azure depolama](https://docs.microsoft.com/azure/storage/)gibi bir veri deposuna kalıcı olması önemlidir.

Batch’teki depolama hesabı seçenekleri için bkz. [Batch özelliğine genel bakış](../articles/batch/batch-api-basics.md#azure-storage-account).
