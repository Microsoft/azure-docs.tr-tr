---
title: Azure Iletişim Hizmetleri için sohbet SDK 'ya genel bakış
titleSuffix: An Azure Communication Services concept document
description: Azure Iletişim Hizmetleri sohbet SDK 'Sı hakkında bilgi edinin.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 561855704d157f9ad826b5db83600a79d9437fc6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500694"
---
# <a name="chat-sdk-overview"></a>Sohbet SDK genel bakış 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Azure Iletişim Hizmetleri sohbet SDK 'Ları, uygulamalarınıza zengin ve gerçek zamanlı sohbet eklemek için kullanılabilir.
    
## <a name="chat-sdk-capabilities"></a>Sohbet SDK özellikleri    

Aşağıdaki listede, Iletişim Hizmetleri sohbet SDK 'lerinde Şu anda kullanılabilir olan özellikler kümesi sunulmaktadır.  

| Özellik grubu | Özellik | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Temel yetenekler | 2 veya daha fazla kullanıcı arasında sohbet iş parçacığı oluşturma                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Sohbet iş parçacığının konusunu güncelleştirme                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Sohbet iş parçacığından katılımcı ekleme veya kaldırma                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Sohbet iletisi geçmişini eklenmekte olan katılımcı ile paylaşıp paylaşmeyeceğinizi seçin                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Sohbet iş parçacığında katılımcıların bir listesini alın                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Sohbet iş parçacığını silme                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Bir iletişim kullanıcısı verildiğinde, kullanıcının bir parçası olduğu sohbet iş parçacıklarının listesini alın                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Belirli bir sohbet iş parçacığı için bilgi alın                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Sohbet iş parçacığında ileti gönderme ve alma                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Gönderilen iletinizin içeriğini güncelleştirme                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Daha önce gönderdiğiniz bir iletiyi silme                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Sohbetteki diğer katılımcılar tarafından okunan iletiler için okundu bilgileri                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Katılımcılar bir sohbet iş parçacığına etkin bir şekilde ileti yazarken bildirim alın                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Sohbet iş parçacığında tüm iletileri al                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | İleti içeriğinin bir parçası olarak Unicode emojıs gönder                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Gerçek zamanlı bildirimler (özel sinyal paketi tarafından etkin * *)|  Sohbet istemcileri gelen iletiler ve sohbet iş parçacığında gerçekleşen diğer işlemler için gerçek zamanlı güncelleştirmeler almak üzere abone olabilir. Gerçek zamanlı bildirimler için desteklenen güncelleştirmelerin listesini görmek için bkz. [sohbet kavramları](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| Azure Event Grid ile tümleştirme             | Özel bildirim hizmetleri eklemek veya bu olayı bir Web kancasına göndermek için Azure Event Grid 'de bulunan sohbet olaylarını kullanarak bir sohbet tamamlandıktan sonra CRM kayıtlarını güncelleştirme gibi iş mantığını yürütün   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Raporlama </br>(Bu bilgi, Azure portal üzerinde Iletişim Hizmetleri kaynağınızın Izleme sekmesinde kullanılabilir)      | Azure Ölçüm Gezgini yayımlanan ölçümleri izleyerek sohbet uygulamaınızdan API trafiğini anlayın ve tüm bunları tespit etmek için Uyarılar ayarlayın     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Kaynağınız için tanılama günlüğü etkinleştirerek Iletişim Hizmetleri çözümünüzü izleyip hatalarını ayıklayın    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


* * Özel sinyal paketi Web Yuvaları kullanılarak uygulanır. Web Yuvaları desteklenmiyorsa uzun yoklamaya geri dönüş yapılır.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>OS ve tarayıcı tarafından JavaScript sohbet SDK desteği    

Aşağıdaki tablo, şu anda kullanılabilir olan desteklenen tarayıcı ve sürümlerin kümesini temsil eder.
    
|                                  | Windows          | Mac OS          | Ubuntu | Linux  | Android | iOS    | iPad işletim sistemi|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Sohbet SDK 'Sı** | Firefox *, Chrome*, yeni kenar | Firefox *, Chrome*, Safari * | Mu  | Mu | Mu | Uygulamasını | Uygulamasını |

* Önceki iki sürüme ek olarak en son sürümün desteklendiğini unutmayın.<br/>   

## <a name="next-steps"></a>Sonraki adımlar   

> [!div class="nextstepaction"] 
> [Sohbetle çalışmaya başlama](../../quickstarts/chat/get-started.md)    

Aşağıdaki belgeler sizin için ilginç olabilir:  
- [Sohbet kavramları](../chat/concepts.md) hakkında bilgi edinin
- [Fiyatlandırmanın](../pricing.md#chat) sohbet için nasıl çalıştığını anlayın
