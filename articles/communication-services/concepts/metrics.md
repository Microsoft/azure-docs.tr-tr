---
title: Azure Iletişim hizmeti için Ölçüm tanımları
titleSuffix: An Azure Communication Services concept document
description: Bu belge Azure portal bulunan ölçümlerin tanımlarını içerir.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 129a718175fdda80d4d6852e3d3b4cea609da64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492430"
---
# <a name="metrics-overview"></a>Ölçümlere genel bakış

Azure Iletişim Hizmetleri şu anda sohbet ve SMS için ölçümler sunmaktadır. [Azure Ölçüm Gezgini](../../azure-monitor/essentials/metrics-getting-started.md) , kendi grafiklerinizi çizmek, ölçüm değerlerinizde yer alan anormallikleri araştırmak ve sohbet ve SMS isteklerinin yayılacağı ölçüm VERILERINI kullanarak API trafiğinizi anlamak için kullanılabilir.

## <a name="where-to-find-metrics"></a>Ölçümlerin nerede bulunacağı

Azure Iletişim Hizmetleri 'ndeki sohbet ve SMS Hizmetleri, API istekleri için ölçümleri yaymalıdır. Bu ölçümler Iletişim Hizmetleri kaynağınızın altındaki ölçümler dikey penceresinde bulunabilir. Ayrıca Iletişim Hizmetleri kaynağınızın altındaki çalışma kitapları dikey penceresini kullanarak kalıcı panolar oluşturabilirsiniz.

## <a name="metric-definitions"></a>Ölçüm tanımları

Iletişim Hizmetleri ölçümleri içinde temsil edilen iki tür istek vardır: **sohbet API istekleri** ve **SMS API istekleri**.

Hem sohbet hem de SMS API istek ölçümleri, ölçüm verilerinizi filtrelemek için kullanabileceğiniz üç boyut içerir. Bu boyutlar toplama türü kullanılarak birlikte toplanabilir ve,, `Count` ve dahil tüm standart Azure toplama zaman serisini destekler `Sum` `Average` `Min` `Max` .

Desteklenen toplama türleri ve zaman serisi toplamaları hakkında daha fazla bilgi için [Azure Ölçüm Gezgini gelişmiş özellikleri](../../azure-monitor/essentials/metrics-charts.md#aggregation) bulunabilir

- **İşlem** -ACS sohbet ağ geçidinde çağrılabilecek tüm işlemler veya rotalar.
- **Durum kodu** -istekten sonra gönderilen durum kodu yanıtı.
- **Statusalt sınıfı** -yanıttan sonra gönderilen durum kodu serisi. 


### <a name="chat-api-request-metric-operations"></a>Sohbet API isteği ölçüm işlemleri

Sohbet API 'SI istek ölçümlerinde aşağıdaki işlemler mevcuttur:

| İşlem/rota    | Description                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | İleti kimliğine göre bir ileti alır. |
| ListChatMessages     | Bir iş parçacığından gelen sohbet iletilerinin listesini alır. |
| SendChatMessage      | Bir iş parçacığına sohbet iletisi gönderir. |
| UpdateChatMessage    | Sohbet iletisini güncelleştirir. |
| DeleteChatMessage    | Bir sohbet iletisini siler. |
| GetChatThread        | Bir sohbet iş parçacığı alır. |
| ListChatThreads      | Bir kullanıcının sohbet iş parçacıklarının listesini alır. |
| UpdateChatThread     | Bir sohbet iş parçacığının özelliklerini güncelleştirir. |
| CreateChatThread     | Sohbet iş parçacığı oluşturur. |
| DeleteChatThread     | Bir iş parçacığını siler. |
| Getreadalındılar      | Bir iş parçacığının okundu bilgilerini alır. |
| Sendreadalındısı      | Bir kullanıcı adına bir iş parçacığına okundu bilgisi olayı gönderir. |
| Sendtypingındicator           | Bir kullanıcı adına bir iş parçacığına yazma olayı gönderir. |
| ListChatThreadParticipants    | Bir iş parçacığının üyelerini alır. |
| AddChatThreadParticipants     | İş parçacığına iş parçacığı üyeleri ekler. Üyeler zaten varsa, hiçbir değişiklik gerçekleşmez. |
| RemoveChatThreadParticipant   | Bir üyeyi iş parçacığından kaldırın. |

:::image type="content" source="./media/chat-metric.png" alt-text="Sohbet API Isteği ölçümü.":::

Tanınmayan bir işleme bir istek yapılırsa, "Hatalı yol" değeri yanıtını alırsınız.

### <a name="sms-api-requests"></a>SMS API istekleri

SMS API istek ölçümlerinde aşağıdaki işlemler mevcuttur:

| İşlem/rota    | Description                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| Smsiletiçi       | SMS iletisi gönderir. |
| Smsdeliveryreportsalındı     | SMS teslim raporlarını alır |
| Smsiletiiletileri alındı      | SMS iletilerini alır. |


:::image type="content" source="./media/sms-metric.png" alt-text="SMS API Isteği ölçümü.":::

### <a name="authentication-api-requests"></a>Kimlik doğrulama API 'SI istekleri

Kimlik doğrulama API 'SI istek ölçümlerinde aşağıdaki işlemler mevcuttur:

| İşlem/rota    | Description                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| CreateIdentity       | Tek bir kullanıcıyı temsil eden bir kimlik oluşturur. |
| Deleteıdentity       | Bir kimliği siler. |
| Belirteçoluşturma          | Erişim belirteci oluşturur. |
| Iptal belirteci          | Kimliği verilen bir süre önce bir kimlik için oluşturulan tüm erişim belirteçlerini iptal eder. |

:::image type="content" source="./media/acs-auth-metrics.png" alt-text="Kimlik doğrulama Isteği ölçümü.":::

## <a name="next-steps"></a>Sonraki Adımlar

- [Veri platformu ölçümleri](../../azure-monitor/essentials/data-platform-metrics.md) hakkında daha fazla bilgi edinin
