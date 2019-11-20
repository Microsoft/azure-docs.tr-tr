---
title: Azure DPS cihazları için müşteri verileri isteği özellikleri
description: Bu makalede, Azure cihaz sağlama hizmeti 'nde (DPS) yönetilen cihazlarda kişisel verilerin nasıl dışarı aktarılacağı veya silineceği, yöneticiler tarafından gösterilir.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 1dcf1b9f62f94b8f75ef2fe77f3e237a387c53eb
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890645"
---
# <a name="summary-of-customer-data-request-features"></a>Müşteri verileri istek özelliklerinin Özeti

Azure IoT Hub cihaz sağlama hizmeti, kurumsal müşterilere yönelik sorunsuz, otomatik sıfır dokunma özellikli bir bulut hizmetidir ve cihazda başlayan ve bulut ile biten güvenlikle birlikte cihazların Azure IoT Hub için sorunsuz REST API bir şekilde otomatik olarak bir şekilde sağlanmasına olanak sağlar.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Ayrı cihazlara bir kiracı yöneticisi tarafından bir kayıt KIMLIĞI ve cihaz KIMLIĞI atanır. Bu cihazlarla ve bu cihazların verileri bu kimliklere göre belirlenir. Microsoft hiçbir bilgi vermez ve bu cihazların bir birede bağıntılı olmasını sağlayacak verilere erişemez.

Cihaz sağlama hizmeti 'nde yönetilen cihazların birçoğu, bir Office termostat veya fabrika robot gibi kişisel cihazlar değildir. Ancak müşteriler, bazı cihazları kişisel olarak tanımlanabilir bir şekilde ele alabilir ve bu cihazların, cihazları kişilere bağlayan kendi varlığını veya envanter izleme yöntemlerini koruyabilir. Cihaz sağlama hizmeti, cihazlarla ilişkili tüm verileri kişisel veriler gibi yönetir ve depolar.

Kiracı yöneticileri, bir cihaz KIMLIĞI veya kayıt KIMLIĞIYLE ilişkili verileri dışarı aktararak veya silerek bilgi isteklerini karşılamak için Azure portal veya hizmetin REST API 'Lerini kullanabilir.

> [!NOTE]
> Azure IoT Hub cihaz sağlama hizmeti aracılığıyla sağlanan cihazlarda, Azure IoT Hub hizmetinde depolanan ek veriler vardır. Belirli bir cihaz için tam isteği tamamlamaya yönelik [Azure IoT Hub başvuru belgelerine](../iot-hub/iot-hub-customer-data-requests.md) bakın.

## <a name="deleting-customer-data"></a>Müşteri verilerini silme

Cihaz sağlama hizmeti kayıtları ve kayıt kayıtlarını depolar. Kayıtlar, sağlanabilen cihazlarla ilgili bilgiler içerir ve kayıt kayıtları, sağlama sürecinde hangi cihazların zaten geçmiş olduğunu gösterir.

Kiracı yöneticileri Azure portal kayıtları kaldırabilir ve bu, ilişkili kayıt kayıtlarını da kaldırır.

Daha fazla bilgi için bkz. [cihaz kayıtlarını yönetme](how-to-manage-enrollments.md).

REST API 'Leri kullanarak kayıtlar ve kayıt kayıtları için silme işlemleri gerçekleştirmek de mümkündür:

* Tek bir cihazın kayıt bilgilerini silmek için, [cihaz kaydı-sil](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment)' i kullanabilirsiniz.
* Bir cihaz grubunun kayıt bilgilerini silmek için, [Cihaz Kayıt Grubu-Sil](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup)' i kullanabilirsiniz.
* Sağlanmış cihazlarla ilgili bilgileri silmek için [kayıt durumu-kayıt silme durumunu](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate)kullanabilirsiniz.

## <a name="exporting-customer-data"></a>Müşteri verilerini dışa aktarma

Cihaz sağlama hizmeti kayıtları ve kayıt kayıtlarını depolar. Kayıtlar, sağlanabilen cihazlarla ilgili bilgiler içerir ve kayıt kayıtları, sağlama sürecinde hangi cihazların zaten geçmiş olduğunu gösterir.

Kiracı yöneticileri Azure portal aracılığıyla kayıtları ve kayıt kayıtlarını görüntüleyebilir ve Kopyala ve Yapıştır kullanarak bunları dışarı aktarabilir.

Kayıtları yönetme hakkında daha fazla bilgi için bkz. cihaz kayıtlarını [yönetme](how-to-manage-enrollments.md).

REST API 'Leri kullanarak kayıtlar ve kayıt kayıtları için dışarı aktarma işlemleri gerçekleştirmek de mümkündür:

* Tek bir cihaz için kayıt bilgilerini dışarı aktarmak için, [cihaz kaydı-al](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment)' ı kullanabilirsiniz.
* Bir cihaz grubu için kayıt bilgilerini dışarı aktarmak için, [cihaz kayıt grubu-Al](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup)' ı kullanabilirsiniz.
* Zaten sağlanmış olan cihazlarla ilgili bilgileri dışarı aktarmak için [kayıt durumu-kayıt durumunu Al](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate)' ı kullanabilirsiniz.

> [!NOTE]
> Microsoft 'un kurumsal hizmetlerini kullandığınızda, Microsoft, sistem tarafından oluşturulan Günlükler olarak bilinen bazı bilgiler üretir. Bazı cihaz sağlama hizmeti sistem tarafından oluşturulan günlüklere kiracı yöneticileri tarafından erişilebilir veya dışarı aktarılabilir. Bu Günlükler, hizmet içinde yürütülen ve ayrı cihazlarla ilgili tanılama verileri için yapılan özel eylemleri oluşturur.

## <a name="links-to-additional-documentation"></a>Ek belgelere bağlantılar

Cihaz sağlama hizmeti API 'Leri için tam belgeler [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps)konumunda bulunur.

Azure IoT Hub [müşteri verileri isteği özellikleri](../iot-hub/iot-hub-customer-data-requests.md).