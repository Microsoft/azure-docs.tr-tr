---
title: 'Öğretici: işlem hatlarla tümleştirmeye başlayın'
description: Bu öğreticide, SYNAPSE Studio kullanarak işlem hatlarını ve etkinlikleri tümleştirmeyi öğreneceksiniz.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 19bff62883341947eb5290118494b8244c5476ac
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518261"
---
# <a name="integrate-with-pipelines"></a>İşlem hatları ile tümleştirme

Bu öğreticide, SYNAPSE Studio kullanarak işlem hatlarını ve etkinlikleri tümleştirmeyi öğreneceksiniz. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>İşlem hattı oluşturma ve Not defteri etkinliği ekleme

1. SYNAPSE Studio 'da **tümleştirin** hub 'a gidin.
1. **+**  >  Yeni bir işlem hattı oluşturmak için işlem **hattı** ' nı seçin. Yeni işlem hattı nesnesini tıklatarak işlem hattı tasarımcısını açın.
1. **Etkinlikler**' ın altında **SYNAPSE** klasörünü genişletin ve bir **Not defteri** nesnesini tasarımcıya sürükleyin.
1. Not defteri etkinlik özelliklerinin **Ayarlar** sekmesini seçin. Geçerli SYNAPSE çalışma alanınızdan bir not defteri seçmek için açılır listeyi kullanın.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>İşlem hattını her saat çalışacak şekilde zamanlama

1. İşlem hattında, yeni **tetikleyici Ekle**  >  **/Düzenle**' yi seçin.
1. **Tetikleyiciyi seçin**' de, **Yeni**' yi seçin ve **yinelemeyi** "her 1 saat" olarak ayarlayın.
1. **Tamam**’ı seçin. 
1. **Tümünü Yayımla**. 

## <a name="forcing-a-pipeline-to-run-immediately"></a>İşlem hattını hemen çalışacak şekilde zorlama

İşlem hattı yayımlandıktan sonra, bir saatin geçmesi beklenmeden hemen çalıştırmak isteyebilirsiniz.

1. İşlem hattını açın.
1. **Tetikleyici tetikleyicisi Ekle**' ye  >  **Şimdi** tıklayın.

## <a name="monitor-pipeline-execution"></a>İşlem hattı yürütmeyi izleme

1. **İzleyici** hub 'ına gidin.
1. İşlem hattı yürütme ilerlemesini izlemek için işlem **hattı çalıştırmalarını** seçin.
1. Bu görünümde tablo **listesi** arasında geçiş yapabilirsiniz grafik **Gantt** grafiği görüntüleme. 
1. Bu işlem hattındaki etkinliklerin durumunu görmek için bir işlem hattı adına tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Power BI ile verileri görselleştirme](get-started-visualize-power-bi.md)
