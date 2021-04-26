---
title: Azure 'da bir zamanlamaya göre çalışan bir işlev oluşturma
description: Azure portal kullanarak, tanımladığınız bir zamanlamaya göre çalışan bir işlev oluşturma hakkında bilgi edinin.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 14d918cc41f49b954f5cabf48572db5df829fd10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035198"
---
# <a name="create-a-function-in-the-azure-portal-that-runs-on-a-schedule"></a>Azure portal zamanlamaya göre çalışan bir işlev oluşturma

Tanımladığınız bir zamanlamaya göre Azure 'da [sunucusuz](https://azure.microsoft.com/solutions/serverless/) çalıştıran bir işlev oluşturmak için Azure Portal nasıl kullanacağınızı öğrenin.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

+ Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Yeni işlev uygulamanız kullanıma hazırlanıyor. Ardından, yeni işlev uygulamasında bir işlev oluşturacaksınız.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="İşlev uygulaması başarıyla oluşturuldu." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Zamanlayıcı ile tetiklenen işlev oluşturma

1. İşlev uygulamanızda **işlevler**' i seçin ve **+ Ekle** ' yi seçin. 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Azure portal bir işlev ekleyin." border="true":::

1. **Zamanlayıcı tetikleyici** şablonunu seçin. 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Azure portal süreölçer tetikleyicisini seçin." border="true":::

1. Yeni tetikleyiciyi görüntünün altındaki tabloda belirtilen ayarlarla yapılandırın ve ardından **Işlev oluştur**' u seçin.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="Ekran görüntüsü, süreölçer tetikleyici şablonunun seçili olduğu yeni Işlev sayfasını gösterir." border="true":::
    
    | Ayar | Önerilen değer | Açıklama |
    |---|---|---|
    | **Ad** | Varsayılan | Zamanlayıcı ile tetiklenen işlevinizin adını tanımlar. |
    | **Zamanla** | 0 \* /1 \* \* \*\* | İşlevinizi her dakika çalışacak şekilde zamanlayan altı haneli bir [CRON ifadesi](functions-bindings-timer.md#ncrontab-expressions). |

## <a name="test-the-function"></a>İşlevi test etme

1. İşlevinizde **kod + test** ' i seçin ve Günlükler ' i genişletin.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Azure portal süreölçer tetikleyicisini test edin." border="true":::

1. Günlüklere yazılan bilgileri görüntüleyerek yürütmeyi doğrulayın.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Azure portal süreölçer tetikleyicisini görüntüleyin." border="true":::

Artık, işlevin zamanlamasını dakikada bir yerine saatte bir çalışacak şekilde değiştirebilirsiniz.

## <a name="update-the-timer-schedule"></a>Zamanlayıcı zamanlamasını güncelleştirme

1. İşlevinizde **tümleştirme**' i seçin. Burada, işleviniz için giriş ve çıkış bağlamaları tanımlayabilir ve zamanlamayı da ayarlarsınız. 

1. **Süreölçer (myTimer)** öğesini seçin.

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Azure portal Zamanlayıcı zamanlamasını güncelleştirin." border="true":::

1. **Zamanlama** değerini olarak güncelleştirin `0 0 */1 * * *` ve ardından **Kaydet**' i seçin.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Azure portal işlev Zamanlayıcı zamanlamasını güncelleştirin." border="true":::

Artık saatte bir kez çalışan işleviniz var.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bir zamanlamaya göre çalışan bir işlev oluşturdunuz. Zamanlayıcı Tetikleyicileri hakkında daha fazla bilgi için bkz. [Azure işlevleri ile kod yürütmeyi zamanlama](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
