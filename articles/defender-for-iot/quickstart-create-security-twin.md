---
title: 'Hızlı başlangıç: güvenlik modülü ikizi oluşturma'
description: Bu hızlı başlangıçta IoT için Azure Defender ile kullanmak üzere IoT modülü ikizi için bir Defender oluşturma hakkında bilgi edinin.
ms.topic: quickstart
ms.date: 1/21/2021
ms.openlocfilehash: 27447f00546ab0d01a33b286f4bb1fbc810c260b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384570"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Hızlı başlangıç: azureiotsecurity modülü oluşturma ikizi

Bu hızlı başlangıçta, yeni cihazlar için tek tek _azureiotsecurity_ Module TWINS oluşturma veya bir IoT Hub tüm cihazlar için toplu işlem modülü oluşturma işlemleri açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

- Yok

## <a name="understanding-azureiotsecurity-module-twins"></a>Azureiotsecurity Module TWINS 'i anlama

Azure 'da oluşturulan IoT çözümleri için, cihaz ikis, hem cihaz yönetimi hem de süreç otomasyonu 'nda anahtar rol oynar.

IoT için Defender, mevcut IoT cihaz yönetimi Platformunuzla tam tümleştirme sunarak cihazınızın güvenlik durumunu yönetmenizi ve mevcut cihaz denetimi yeteneklerini kullanmanızı sağlar.
IoT tümleştirmesi için Defender, IoT Hub ikizi mekanizması kullanılarak elde edilir.

Azure IoT Hub modül TWINS 'in genel kavramı hakkında daha fazla bilgi edinmek için bkz. [IoT Hub Module TWINS](../iot-hub/iot-hub-devguide-module-twins.md) .

IoT için Defender, Module ikizi mekanizmasını kullanır ve cihazlarınızın her biri için _azureiotsecurity_ adlı bir güvenlik modülünü ikizi.

Defender-IoT-Micro-Agent ikizi, cihazlarınızın her biri için cihaz güvenliğiyle ilgili tüm bilgileri tutar.

IoT özellikleri için Defender 'ın tam kullanımını yapmak üzere bu Defender-IoT-Micro-Agent TWINS hizmetini hizmette her cihaz için oluşturmanız, yapılandırmanız ve kullanmanız gerekir.

## <a name="create-azureiotsecurity-module-twin"></a>Azureiotsecurity modülü oluşturma ikizi

_azureiotsecurity_ Module TWINS iki şekilde oluşturulabilir:

1. [Modül Batch betiği](https://aka.ms/iot-security-github-create-module) -varsayılan yapılandırmayı kullanarak bir modül ikizi olmadan yeni cihazlar veya cihazlar için otomatik olarak modül ikizi oluşturur.
1. Her modül için her bir cihaz için belirli yapılandırmalara sahip her modülü ikizi el ile düzenleyin.

>[!NOTE]
> Batch yönteminin kullanılması, var olan azureiotsecurity Module TWINS 'in üzerine yazmaz. Batch yönteminin kullanılması, yalnızca bir güvenlik modülü ikizi olmayan cihazlar için yeni modül TWINS 'i oluşturur.

Var olan bir modülün ikizi yapılandırmasını değiştirme veya değiştirme hakkında bilgi edinmek için [Aracı yapılandırması](how-to-agent-configuration.md) bölümüne bakın.

Bir cihaz için el ile yeni bir _azureiotsecurity_ Module ikizi oluşturmak için:

1. IoT Hub, için güvenlik modülü ikizi oluşturmak istediğiniz cihazı bulun ve seçin.

1. Cihazınızda öğesini ve ardından **modül kimliği Ekle**' yi seçin.

1. **Modül kimliği adı** alanına **azureiotsecurity** girin.

1. **Kaydet**’i seçin.

## <a name="verify-creation-of-a-module-twin"></a>Modül ikizi oluşturmayı doğrulama

Belirli bir cihaz için bir güvenlik modülü ikizi varolup olmadığını doğrulamak için:

1. Azure IoT Hub, **araştırıcılar** menüsünden **IoT cihazları** ' nı seçin.

1. Cihaz KIMLIĞINI girin veya **sorgu cihazı alanında** bir seçenek belirleyin ve **sorgu cihazları**' nı seçin.

    :::image type="content" source="./media/quickstart/verify-security-module-twin.png" alt-text="Cihazları sorgula":::

1. Cihazı seçin veya çift seçerek cihaz ayrıntıları sayfasını açın.

1. **Modül kimlikleri** menüsünü seçin ve cihazla ilişkili modül kimlikleri listesinde **azureiotsecurity** modülünün varlığını onaylayın.

    :::image type="content" source="./media/quickstart/verify-security-module-twin-3.png" alt-text="Bir cihazla ilişkili modüller":::

IoT modül TWINS için Defender 'ın özelliklerini özelleştirme hakkında daha fazla bilgi edinmek için bkz. [Aracı yapılandırması](how-to-agent-configuration.md).

## <a name="next-steps"></a>Sonraki adımlar

Güvenlik önerilerini araştırmaya yönelik bilgi edinmek için sonraki makaleye ilerleyin...

> [!div class="nextstepaction"]
> [Güvenlik önerilerini araştırma](quickstart-investigate-security-recommendations.md)