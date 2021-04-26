---
title: Azure IoT Hub cihazları için müşteri verileri istekleri
description: Azure IoT Hub 'de yönetilen cihazların çoğu kişisel değildir, ancak bazıları. Bu makale, yöneticilerin kişisel verileri bir cihazdan dışarı veya daha fazla veri kaydedebilmekte olduğu konusunda bilgi verir.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: df1d4f3a12c6e15e2954bb0e02454ef58b22cb73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92535799"
---
# <a name="customer-data-request-features-for-azure-iot-hub-devices"></a>Azure IoT Hub cihazları için müşteri verileri isteği özellikleri

Azure IoT Hub, kurumsal müşterileri hedefleyen, milyonlarca cihaz ve bölümlenmiş bir Azure hizmeti arasında güvenli, çift yönlü iletişim sağlayan REST API tabanlı bir bulut hizmetidir.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Ayrı cihazlara, bir kiracı yöneticisi tarafından bir cihaz tanımlayıcısı (cihaz KIMLIĞI) atanır. Cihaz verileri atanan cihaz KIMLIĞINE göre belirlenir. Microsoft hiçbir bilgi vermez ve cihaz KIMLIĞINE Kullanıcı bağıntıya izin verecek verilere erişemez.

Azure IoT Hub 'de yönetilen cihazların birçoğu, bir Office termostat veya fabrika robot gibi kişisel cihazlar değildir. Ancak müşteriler, bazı cihazları kişisel olarak tanımlanabilir bir şekilde ele alabilir ve bu cihazların, cihazları kişilere bağlayan kendi varlığını veya envanter izleme yöntemlerini koruyabilir. Azure IoT Hub, cihazlarla ilişkili tüm verileri kişisel veriler gibi yönetir ve depolar.

Kiracı yöneticileri, bir cihaz KIMLIĞIYLE ilişkili verileri dışarı veya silerek bilgi isteklerini karşılamak için Azure portal veya hizmetin REST API 'Lerini kullanabilir.

Cihaz iletilerini diğer hizmetlere iletmek için Azure IoT Hub hizmeti 'nin yönlendirme özelliğini kullanırsanız, belirli bir cihaz için tam isteği tamamlayacak şekilde her bir yönlendirme uç noktası için kiracı yöneticisi tarafından veri isteklerinin gerçekleştirilmesi gerekir. Daha fazla ayrıntı için, her bitiş noktası için başvuru belgelerine bakın. Desteklenen uç noktalar hakkında daha fazla bilgi için bkz. [Reference-IoT Hub uç noktaları](iot-hub-devguide-endpoints.md).

Azure IoT Hub hizmetinin Azure Event Grid tümleştirme özelliğini kullanıyorsanız, bu olayların her abonesi için veri isteklerinin kiracı yöneticisi tarafından gerçekleştirilmesi gerekir. Daha fazla bilgi için bkz. [Event Grid kullanarak IoT Hub olaylara tepki](iot-hub-event-grid.md)verme.

Kaynak günlükleri oluşturmak için Azure IoT Hub hizmetinin Azure Izleyici tümleştirme özelliğini kullanıyorsanız, veri isteklerinin depolanan günlüklere karşı kiracı yöneticisi tarafından gerçekleştirilmesi gerekir. Daha fazla bilgi için bkz. [izleyici IoT Hub](monitor-iot-hub.md).

## <a name="deleting-customer-data"></a>Müşteri verilerini silme

Kiracı yöneticileri, bir cihazı silmek için Azure portal Azure IoT Hub uzantısının IoT cihazları Dikey penceresini kullanarak bu cihazla ilişkili verileri siler.

REST API 'Leri kullanan cihazlar için silme işlemlerini gerçekleştirmek de mümkündür. Daha fazla bilgi için bkz. [hizmet-cihaz silme](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice).

## <a name="exporting-customer-data"></a>Müşteri verilerini dışa aktarma

Kiracı yöneticileri, bir cihazla ilişkili verileri dışarı aktarmak için Azure portal Azure IoT Hub uzantısının IoT cihazları bölmesinde kopyalama ve yapıştırmayı kullanabilir.

REST API 'Leri kullanan cihazlar için dışarı aktarma işlemleri gerçekleştirmek da mümkündür. Daha fazla bilgi için bkz. [hizmet-cihazı al](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice).

> [!NOTE]
> Microsoft 'un kurumsal hizmetlerini kullandığınızda, Microsoft, sistem tarafından oluşturulan Günlükler olarak bilinen bazı bilgiler üretir. Bazı Azure IoT Hub sistem tarafından oluşturulan günlüklere, kiracı yöneticileri tarafından erişilebilir veya dışarı aktarılabilir. Bu Günlükler, hizmet içinde yürütülen ve ayrı cihazlarla ilgili tanılama verileri için yapılan özel eylemleri oluşturur.

## <a name="links-to-additional-documentation"></a>Ek belgelere bağlantılar

Azure IoT Hub Service API 'Leri için tam belgeler [IoT Hub hizmet API 'lerinde](/rest/api/iothub/service/configuration)bulunur.