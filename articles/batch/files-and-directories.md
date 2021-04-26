---
title: Azure Batch dosya ve dizinler
description: Dosyalar ve dizinler hakkında bilgi edinin ve bunların bir geliştirme açısından Azure Batch iş akışında nasıl kullanıldığını öğrenin.
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: eafea6c234c3b261521f8a791b7a03e25388f02a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87552654"
---
# <a name="files-and-directories-in-azure-batch"></a>Azure Batch dosya ve dizinler

Azure Batch, her görevin altında dosya ve Dizin oluşturabileceğiniz bir çalışma dizini vardır. Bu çalışma dizini, görev tarafından çalıştırılan programı, işlediği verileri ve gerçekleştirdiği işlemin çıktısını depolamak için kullanılabilir. Bir görevin tüm dosyaları ve dizinleri görev kullanıcısına aittir.

Batch hizmeti bir düğümdeki dosya sisteminin bir bölümünü *kök dizin* olarak kullanıma sunar. Bu kök dizin, doğrudan işletim sistemi sürücüsünde değil, VM 'nin geçici depolama sürücüsünde bulunur.

Görevler `AZ_BATCH_NODE_ROOT_DIR` ortam değişkenine başvurarak kök dizine erişebilir. Ortam değişkenlerini kullanma hakkında daha fazla bilgi için bkz. [Görevler için ortam ayarları](jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="root-directory-structure"></a>Kök dizin yapısı

Kök dizin aşağıdaki dizin yapısını içerir:

![İşlem düğümü dizin yapısının ekran görüntüsü.](media\files-and-directories\node-folder-structure.png)

- **uygulamalar**: işlem düğümünde yüklü olan uygulama paketlerinin ayrıntıları hakkında bilgiler içerir. Görevler `AZ_BATCH_APP_PACKAGE` ortam değişkenine başvurarak bu dizine erişebilir.

- **fstakal**: Dizin, bir işlem düğümüne bağlı herhangi bir dosya sistemini içerir. Görevler `AZ_BATCH_NODE_MOUNTS_DIR` ortam değişkenine başvurarak bu dizine erişebilir. Daha fazla bilgi için bkz. bir [Batch havuzunda sanal dosya sistemi bağlama](virtual-file-mount.md).

- **paylaşılan**: Bu dizin, düğüm üzerinde çalışan *tüm* görevler için okuma/yazma erişimi sağlar. Düğüm üzerinde çalışan herhangi bir görev bu dizinde dosya oluşturabilir, okuyabilir, güncelleştirebilir ve silebilir. Görevler `AZ_BATCH_NODE_SHARED_DIR` ortam değişkenine başvurarak bu dizine erişebilir.

- **başlangıç**: Bu dizin, bir başlangıç görevi tarafından çalışma dizini olarak kullanılır. Başlangıç görevi tarafından düğüme indirilen tüm dosyalar buraya depolanır. Başlangıç görevleri bu dizin altında dosya oluşturabilir, okuyabilir, güncelleştirebilir ve silebilir. Görevler `AZ_BATCH_NODE_STARTUP_DIR` ortam değişkenine başvurarak bu dizine erişebilir.

- **geçici**: Bu dizin iç amaçlar içindir. Bu dizindeki dosyaların veya dizinin kendisinin gelecekte mevcut olacağı garanti yoktur.

- **WORKITEMS**: Bu dizin, işlem düğümündeki işlerin ve görevlerinin dizinlerini içerir.

    **Workitems** dizininde, düğüm üzerinde çalışan her görev Için bir **Görevler** dizini oluşturulur. Ortam değişkenine başvurarak bu dizine erişilebilir `AZ_BATCH_TASK_DIR` .

    Batch hizmeti, her **görev** dizininde, `wd` ortam değişkeni tarafından benzersiz yolu belirtilen bir çalışma dizini () oluşturur `AZ_BATCH_TASK_WORKING_DIR` . Bu dizin göreve okuma/yazma erişimi sağlar. Görev bu dizin altında dosya oluşturabilir, okuyabilir, güncelleştirebilir ve silebilir. Bu dizin, görev için belirtilen *RetentionTime* kısıtlamasına göre tutulur.

    `stdout.txt`Ve `stderr.txt` dosyaları, görevin yürütülmesi sırasında **Görevler** klasörüne yazılır.

> [!IMPORTANT]
> Bir düğüm havuzdan kaldırıldığında, düğümde depolanan dosyaların tümü kaldırılır.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Batch 'de [hata işleme ve algılama](error-handling.md) hakkında bilgi edinin.