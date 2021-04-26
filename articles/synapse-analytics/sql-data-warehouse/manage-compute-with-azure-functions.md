---
title: 'Öğretici: Azure Işlevleri ile işlem yönetme'
description: Azure Işlevleri 'ni kullanarak, Azure SYNAPSE Analytics 'te adanmış SQL havuzunuzun (eski adıyla SQL DW) işlem yönetimi.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f0731f0deaf46ec419cfe43037804e10f2b73fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96448386"
---
# <a name="use-azure-functions-to-manage-compute-resources-for-your-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzunuzun (eski adıyla SQL DW) işlem kaynaklarını yönetmek için Azure Işlevleri 'ni kullanma

Bu öğretici, Azure SYNAPSE Analytics 'te adanmış bir SQL havuzunun (eski adıyla SQL DW) işlem kaynaklarını yönetmek için Azure Işlevleri 'ni kullanır.

Bir Azure İşlev Uygulaması adanmış bir SQL Havuzu (eski adıyla SQL DW) ile kullanmak için bir [hizmet sorumlusu hesabı](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)oluşturmanız gerekir. Hizmet sorumlusu hesabının, Özel SQL havuzunuz (eski adıyla SQL DW) örneği ile aynı abonelik altında katkıda bulunan erişimine ihtiyacı vardır.

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Azure Resource Manager şablonuyla Zamanlayıcı tabanlı ölçeklendirmeyi dağıtma

Şablonu dağıtmak için aşağıdaki bilgilere ihtiyacınız vardır:

- Adanmış SQL havuzunuzun adı (eskiden SQL DW) örneğinin bulunduğu kaynak grubunun adı
- Adanmış SQL havuzunuzun (eskiden SQL DW) örneğinin bulunduğu sunucunun adı
- Adanmış SQL havuzunuzun adı (eski adıyla SQL DW) örneği
- Azure Active Directory'nizin Kiracı Kimliği (Dizin Kimliği)
- Abonelik Kimliği
- Hizmet Sorumlusu Uygulama Kimliği
- Hizmet Sorumlusu Gizli Anahtarı

Yukarıdaki bilgilere sahip olduktan sonra bu şablonu dağıtın:

[!["Azure 'a dağıt" etiketli bir düğmeyi gösteren resim.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

Şablonu dağıttıktan sonra, üç yeni kaynak bulmanız gerekir: ücretsiz bir Azure App Service planı, tüketim tabanlı bir İşlev Uygulaması planı ve günlüğü ve işlemler kuyruğunu işleyen bir depolama hesabı. Dağıtılan işlevlerin gereksinimlerinize uyacak şekilde nasıl değiştirileceğini görmek için okumaya devam edin.

## <a name="change-the-compute-level"></a>İşlem düzeyini değiştirme

1. İşlev Uygulaması hizmetinize gidin. Şablonu varsayılan değerlerle dağıttıysanız, bu hizmetin adı *DWOperations* olacaktır. İşlev Uygulamanız açıldığında, İşlev Uygulaması Hizmetinize beş işlevin dağıtılmış olduğunu görürsünüz.

   ![Şablonla dağıtılan işlevler](./media/manage-compute-with-azure-functions/five-functions.png)

2. Ölçeği azaltmak veya ölçeklendirmek için *Dwscaledowntrigger* veya *dwscaleuptrigger* seçeneklerinden birini belirleyin. Açılan menüde tümleştirin ' ı seçin.

   ![İşlev için Tümleştir'i seçme](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Şu anda görüntülenen değer *%ScaleDownTime%* veya *%ScaleUpTime%* olmalıdır. Bu değerler, zamanlamanın [Uygulama Ayarları](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) altında tanımlanmış değerleri temel alacağını gösterir. Şimdilik, bu değeri yoksayabilir ve sonraki adımlara göre zamanlamayı tercih ettiğiniz zamana dönüştürebilirsiniz.

4. Zamanlama alanında, Azure SYNAPSE Analytics 'in ölçeğini ne sıklıkta ölçeklendirmek istediğinizi yansıtmak istediğiniz CRON ifadesini ekleyin.

   ![İşlev zamanlamasını değiştirme](./media/manage-compute-with-azure-functions/change-schedule.png)

   `schedule` değeri, şu altı alanı içeren bir [CRON ifadesidir](https://en.wikipedia.org/wiki/Cron#CRON_expression):

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Örneğin, *"0 30 9 * * 1-5"* bir tetikleyiciyi 9:30 ' da her hafta içinde yansıtır. Daha fazla bilgi için Azure İşlevleri[zamanlama örnekleri](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) sayfasını ziyaret edin.

## <a name="change-the-time-of-the-scale-operation"></a>Ölçek işleminin saatini değiştirme

1. İşlev Uygulaması hizmetinize gidin. Şablonu varsayılan değerlerle dağıttıysanız, bu hizmetin adı *DWOperations* olacaktır. İşlev Uygulamanız açıldığında, İşlev Uygulaması Hizmetinize beş işlevin dağıtılmış olduğunu görürsünüz.

2. İşlem değerinin ölçeğini artırmak veya azaltmak için *Dwscaledowntrigger* veya *dwscaleuptrigger* seçeneklerinden birini belirleyin. İşlevleri seçtikten sonra, bölmenizde *index.js* dosyası gösterilmelidir.

   ![İşlev tetikleyicisi işlem düzeyini değiştirme](././media/manage-compute-with-azure-functions/index-js.png)

3. *Servicelevelamacın* değerini istediğiniz düzeye değiştirip Kaydet ' i seçin. *Servicelevelamaç* , veri ambarı örneğinizin tümleştirin bölümünde tanımlanan zamanlamaya göre ölçeklendirileceği işlem düzeyidir.

## <a name="use-pause-or-resume-instead-of-scale"></a>Ölçek yerine duraklatma veya sürdürme kullanma

Şu anda, varsayılan olarak açık olan işlevler *DWScaleDownTrigger* ve *DWScaleUpTrigger*'dır. Bunun yerine duraklatma ve sürdürme işlevselliğini kullanmak isterseniz, *DWPauseTrigger* veya *DWResumeTrigger*'ı etkinleştirebilirsiniz.

1. İşlevler bölmesine gidin.

   ![İşlevler bölmesi](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Etkinleştirmek istediğiniz ilgili tetikleyicilerle ilgili kayan geçişli geçiş seçeneğini belirleyin.

3. Zamanlamalarını değiştirmek için ilgili tetikleyicilerin *Tümleştir* sekmelerine gidin.

   > [!NOTE]
   > Ölçeklendirme Tetikleyicileri ve duraklatma/devam Tetikleyicileri arasındaki işlevsel fark, kuyruğa gönderilen iletidir. Daha fazla bilgi için bkz. [Yeni tetikleyici Işlevi ekleme](manage-compute-with-azure-functions.md#add-a-new-trigger-function).

## <a name="add-a-new-trigger-function"></a>Yeni tetikleyici işlevi ekleme

Şu anda, şablona dahil edilmiş yalnızca iki ölçeklendirme işlevi vardır. Bu işlevlerle, bir gün boyunca yalnızca bir kez ve bir kez ölçeği azaltabilirsiniz. Gün başına birden çok kez ölçekleme veya hafta sonları üzerinde farklı ölçeklendirme davranışına sahip gibi daha ayrıntılı denetim için, başka bir tetikleyici eklemeniz gerekir.

1. Yeni boş bir işlev oluşturun. *+* İşlev şablonu bölmesini göstermek Için işlevlerinizin konumunun yakınındaki düğmeyi seçin.

   !["Işlevler" seçeneğinin yanında "Plus" simgesiyle "Işlev uygulamaları" menüsünü gösteren ekran görüntüsü.](./media/manage-compute-with-azure-functions/create-new-function.png)

2. Dilden *JavaScript*' i seçin, sonra *timertrigger*' ı seçin.

   ![Yeni işlev oluşturma](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. İşlevinizi adlandırın ve zamanlamanızı ayarlayın. Resimde, işlevin her Cumartesi gece yarısı (Cuma gecesi) nasıl tetiklenebileceği gösterilir.

   ![Cumartesi ölçeği azaltma](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Diğer tetikleyici işlevlerinden birinin *index.js* içeriğini kopyalayın.

   ![Index.js'yi kopyalama](././media/manage-compute-with-azure-functions/index-js.png)

5. İşlem değişkeninizi istenen davranışa aşağıdaki şekilde ayarlayın:

   ```JavaScript
   // Resume the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the dedicated SQL pool (formerly SQL DW)l instance to DW600c
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600c"
   }
   ```

## <a name="complex-scheduling"></a>Karmaşık zamanlama

Bu bölüm, duraklatma, devam etmeyi ve ölçeklendirme özelliklerini daha karmaşık bir şekilde planlamak için gereken öğeleri kısaca gösterir.

### <a name="example-1"></a>Örnek 1

Günlük ölçeği 00 ' da DW600c ve ölçeği 8pm ile DW200c arasında ölçeklendirin.

| İşlev  | Zamanla     | İşlem                                |
| :-------- | :----------- | :--------------------------------------- |
| İşlev1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600c"}` |
| İşlev2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-2"></a>Örnek 2

Günlük ölçeği, 10:00 ' da DW1000c ' ye kadar bir kez ölçeklendirin, 4pm 'de DW600.

| İşlev  | Zamanla     | İşlem                                |
| :-------- | :----------- | :--------------------------------------- |
| İşlev1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| İşlev2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| İşlev3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-3"></a>Örnek 3

00 ' da DW1000c ' ye kadar ölçeklendirin, hafta içi haftanın 4:00 ' da bir kez ölçeğini DW600c. Cuma 23:00'da duraklatılır, Pazartesi sabahı 07:00'da sürdürülür.

| İşlev  | Zamanla       | İşlem                                |
| :-------- | :------------- | :--------------------------------------- |
| İşlev1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| İşlev2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| İşlev3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| İşlev4 | 0 0 7 * * 1    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Sonraki adımlar

[Zamanlayıcı tetikleyicisi](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Azure işlevleri hakkında daha fazla bilgi edinin.

Bkz. adanmış SQL Havuzu (eski adıyla SQL DW) [örnekleri deposu](https://github.com/Microsoft/sql-data-warehouse-samples).
