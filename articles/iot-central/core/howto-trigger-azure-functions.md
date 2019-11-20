---
title: Azure 'da Web kancalarını kullanarak Azure Işlevlerini tetikleme IoT Central
description: Azure IoT Central 'de bir kural tetiklendiğinde çalışan bir işlev uygulaması oluşturun.
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a9590e5554956418859a07b43fb57724783343fe
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952855"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Azure 'da Web kancalarını kullanarak Azure Işlevlerini tetikleme IoT Central

*Bu konu, oluşturucular ve yöneticiler için geçerlidir.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

IoT Central kurallarından Web kancası çıkışında sunucusuz kod çalıştırmak için Azure Işlevleri 'ni kullanın. Azure Işlevlerini kullanmak için bir VM sağlamanız veya bir Web uygulaması yayımlamanız gerekmez, bunun yerine bu kodu sunucusuz olarak çalıştırabilirsiniz. Web kancası yükünü SQL veritabanı veya Event Grid gibi son hedefine göndermeden önce dönüştürmek için Azure Işlevleri 'ni kullanın.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="how-to-connect-azure-functions"></a>Azure Işlevleri 'ne bağlanma

1. [Azure Portal yeni bir işlev uygulaması oluşturun](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Azure portal yeni bir işlev uygulaması oluşturma](media/howto-trigger-azure-functions/createfunction.png)

2. İşlev uygulamanızı genişletin ve Işlevler ' in yanındaki **+ düğmesini** seçin. Bu işlev, işlev uygulamanızda ilk bir ise, **Portal 'da** geliştirme ortamı olarak ' ı seçin ve **devam**' ı seçin.

    ![Işlev uygulamasında özel işlev seçin](media/howto-trigger-azure-functions/customfunction.png)

3. **Web kancası + API** şablonu ' nu seçip **Oluştur**' u seçin. Bu konuda .NET tabanlı Azure işlevi kullanılmaktadır.

    ![Genel Web kancası tetikleyicisi seçin](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Yeni işlevinizde **</> işlev URL 'Sini al**' ı seçin, sonra değeri kopyalayın ve kaydedin. Bu değeri, web kancasını yapılandırmak için kullanırsınız.

    ![İşlevin URL 'sini alın](media/howto-trigger-azure-functions/getfunctionurl.png)

4. IoT Central, işlev uygulamanıza bağlamak istediğiniz kurala gidin.

5. Web kancası eylemi ekleyin. Bir **görünen ad** girin ve daha önce **geri arama URL**'sine kopyaladığınız işlev URL 'sini yapıştırın.

    ![Geri çağırma URL 'si alanına işlev URL 'sini girin](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Kuralı kaydedin. Artık kural tetiklendiğinde, Web kancası çalıştırılacak işlev uygulamasını çağırır. İşlev uygulamanızda, işlevin çağırma geçmişini görmek için **izleyici** ' yi seçebilirsiniz. Geçmişe bakmak için uygulama öngörülerini veya klasik görünümü kullanabilirsiniz.

    ![İşlevin çağırma geçmişini izleme](media/howto-trigger-azure-functions/monitorfunction.PNG)

Daha fazla bilgi için, [Genel Web kancası tarafından tetiklenen bir işlev oluşturma](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function)hakkında Azure işlevleri makalesini ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar
Web kancalarını ayarlama ve kullanma hakkında daha fazla öğrendiğinize göre, önerilen sonraki adım [Microsoft Flow iş akışı oluşturmayı](howto-add-microsoft-flow.md)araştırmaya hazır.
