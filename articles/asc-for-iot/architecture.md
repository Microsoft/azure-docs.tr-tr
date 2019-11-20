---
title: IoT çözüm mimarisi için Azure Güvenlik Merkezi 'ni anlama | Microsoft Docs
description: IoT hizmeti için Azure Güvenlik Merkezi 'nde bilgi akışı hakkında bilgi edinin.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: a013d4cfcfddc709e60e91adf57bc27c98934a96
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596563"
---
# <a name="azure-security-center-for-iot-architecture"></a>IoT mimarisi için Azure Güvenlik Merkezi

Bu makalede IoT çözümü için Azure Güvenlik Merkezi 'nin işlevsel sistem mimarisi açıklanmaktadır. 

## <a name="azure-security-center-for-iot-components"></a>IoT bileşenleri için Azure Güvenlik Merkezi

IoT için Azure Güvenlik Merkezi aşağıdaki bileşenlerden oluşur:
- IoT Hub tümleştirme
- Cihaz aracıları (isteğe bağlı)
- Güvenlik iletisi SDK 'sını gönder
- Analiz işlem hattı
 
### <a name="azure-security-center-for-iot-workflows"></a>IoT iş akışları için Azure Güvenlik Merkezi

IoT için Azure Güvenlik Merkezi, iki özellik iş akışının birinde çalışmaktadır: Yerleşik ve geliştirilmiş  

### <a name="built-in"></a>Yerleşik
**Yerleşik** modda, IoT Hub **güvenlik** seçeneğini etkinleştirmek Istediğinizde IoT için Azure Güvenlik Merkezi etkinleştirilir. Gerçek zamanlı izleme, öneriler ve uyarılar sunma, yerleşik mod, singe-adımlı cihaz görünürlüğü ve eşleşmeyen güvenlik sunar. Derleme modu, hiçbir cihazda aracı yüklemesi gerektirmez ve alan cihazınızı çözümlemek ve korumak için günlüğe kaydedilen etkinliklerde gelişmiş çözümlemeler kullanır. 

### <a name="enhanced"></a>Gelişmiş 
**Gelişmiş** modda, IoT Hub **güvenlik** seçeneğini açtıktan ve cihazlarınızda IoT cihaz aracıları için Azure Güvenlik Merkezi 'ni yükledikten sonra, aracılar cihazlarınızdan ham güvenlik olaylarını toplar, toplar ve analiz eder. Ham güvenlik olayları, IP bağlantıları, işlem oluşturma, Kullanıcı oturum açmaları ve güvenlikle ilgili diğer bilgileri içerebilir. IoT cihaz aracıları için Azure Güvenlik Merkezi, yüksek ağ aktarım hızını önlemeye yardımcı olmak için olay toplamayı da işler. Aracılar yüksek düzeyde özelleştirilebilir ve en hızlı SLA 'da yalnızca önemli bilgileri göndermek ya da kapsamlı güvenlik bilgilerini ve bağlamı daha büyük kesimlerde toplamak, daha yüksek hizmet maliyetlerine izin vermek gibi belirli görevler için bunları kullanmanıza olanak sağlar.

![IoT mimarisi için Azure Güvenlik Merkezi](./media/architecture/azure-iot-security-architecture.png)
 
Cihaz aracıları ve diğer uygulamalar, güvenlik bilgilerini Azure IoT Hub 'a göndermek için **Azure güvenlik iletisi gönder SDK 'sını** kullanır. IoT Hub, bu bilgileri alır ve IoT hizmeti için Azure Güvenlik Merkezi 'ne iletir.

IoT hizmeti için Azure Güvenlik Merkezi etkinleştirildikten sonra, iletilen verilere ek olarak, IoT Hub IoT için Azure Güvenlik Merkezi tarafından analiz için tüm iç verileri de gönderir. Bu veriler cihaz bulutu işlem günlükleri, cihaz kimlikleri ve hub yapılandırması içerir. Bu bilgilerin tümü, IoT Analytics işlem hattı için Azure Güvenlik Merkezi 'ni oluşturmaya yardımcı olur.
 
IoT Analytics için Azure Güvenlik Merkezi işlem hattı, Microsoft ve Microsoft iş ortakları içindeki çeşitli kaynaklardan ek tehdit bilgileri akışları da alır. Tüm analiz işlem hattının IoT için Azure Güvenlik Merkezi, hizmette yapılan tüm müşteri yapılandırmaları (özel uyarılar ve güvenlik iletisi gönder SDK 'Sı) ile birlikte kullanılır.
 
Analiz işlem hattını kullanarak IoT için Azure Güvenlik Merkezi, eyleme dönüştürülebilir öneriler ve uyarılar oluşturmak için tüm bilgi akışlarını birleştirir. İşlem hattı, güvenlik araştırmacıları ve uzmanlar tarafından oluşturulan özel kuralların yanı sıra standart cihaz davranışından ve risk analizinden sapma arayan makine öğrenimi modellerini içerir.
 
IoT önerileri ve uyarıları için Azure Güvenlik Merkezi (analiz ardışık düzen çıkışı), her müşterinin Log Analytics çalışma alanına yazılır. Çalışma alanındaki ham olayların yanı sıra uyarı ve öneriler de dahil olmak üzere, algılanan şüpheli etkinliklerin tam ayrıntılarını kullanarak derinlemesine araştırma ve sorgular sunar.  

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede IoT çözümü için Azure Güvenlik Merkezi 'nin temel mimarisi ve iş akışı hakkında bilgi edindiniz. Önkoşullar hakkında daha fazla bilgi edinmek ve IoT Hub ' de Güvenlik çözümünüzü etkinleştirmek için aşağıdaki makalelere bakın:

- [Hizmet önkoşulları](service-prerequisites.md)
- [Başlarken](getting-started.md)
- [Çözümünüzü yapılandırın](quickstart-configure-your-solution.md)
- [IoT Hub güvenliği etkinleştir](quickstart-onboard-iot-hub.md)
- [IoT için Azure Güvenlik Merkezi SSS](resources-frequently-asked-questions.md)
- [IoT güvenlik uyarıları için Azure Güvenlik Merkezi](concept-security-alerts.md)
