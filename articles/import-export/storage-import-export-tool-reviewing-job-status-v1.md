---
title: Azure Içeri/dışarı aktarma iş durumunu gözden geçirme-v1 | Microsoft Docs
description: İşin durumunu görmek için içeri veya dışarı aktarma işi tarafından oluşturulan günlük dosyalarını nasıl kullanacağınızı öğrenin.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: f634ceb60ae78d4d825c73bd2e98da2fb951b374
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706699"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Azure Içeri/dışarı aktarma iş durumunu kopyalama günlük dosyalarıyla gözden geçirme
Microsoft Azure İçeri/Dışarı Aktarma hizmeti bir içeri veya dışarı aktarma işiyle ilişkili sürücüleri işlediğinde, kopyalama günlük dosyalarını blob 'ları içeri veya dışarı aktarmak için kullandığınız depolama hesabına yazar. Günlük dosyası, içeri aktarılan veya aktarılan her dosya hakkında ayrıntılı durum içerir. Hizmet, tamamlanan bir işin durumunu sorgulayıp her bir kopya günlük dosyasının URL 'sini döndürür. Daha fazla bilgi için bkz. [Işi al](/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>Örnek URL 'Ler

Aşağıda, iki sürücü içeren bir içeri aktarma işine ait günlük dosyalarının kopyalanması için örnek URL 'Ler verilmiştir:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Kopya günlüklerinin biçimi ve durum kodlarının tam listesi için bkz. [içeri/dışarı aktarma hizmeti günlük dosyası biçimi](/previous-versions/azure/storage/common/storage-import-export-file-format-log) .  

## <a name="next-steps"></a>Sonraki adımlar

 * [Azure Içeri/dışarı aktarma aracı 'nı ayarlama](storage-import-export-tool-setup-v1.md)   
 * [Sabit sürücüleri içeri aktarma işine hazırlama](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
 * [Bir içeri aktarma işini onarma](./storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Bir dışarı aktarma işini onarma](./storage-import-export-tool-repairing-an-export-job-v1.md)