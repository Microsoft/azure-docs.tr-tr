---
title: Azure Iletişim Hizmetleri Kullanıcı arabirimi çerçevesi özellikleri
titleSuffix: An Azure Communication Services conceptual document
description: UI çerçevesi özellikleri hakkında bilgi edinin
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5b1aab8b38614249d6b502044b5c4c8170f46b3c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492226"
---
# <a name="ui-framework-capabilities"></a>UI çerçevesi özellikleri

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Iletişim Hizmetleri Kullanıcı arabirimi çerçevesi, yeniden kullanılabilir bir bileşen kümesi kullanarak iletişim deneyimleri oluşturmanıza olanak tanır. Bu bileşenler iki şekilde sunulur: **temel** BILEŞENLER, UI deneyiminizin en temel yapı taşlarıdır. bu temel bileşenlerin birleşimleri **bileşik** bileşenler olarak adlandırılır.

## <a name="ui-framework-composite-components"></a>UI çerçevesi bileşik bileşenleri

| Bileşik               | Description                                               | Web   | Android | iOS   |
|-------------------------|-----------------------------------------------------------|-------|---------|-------|
| Bileşik çağıran Grup | Akıcı Kullanıcı arabirimi tasarım varlıklarını kullanarak Azure Iletişim Hizmetleri için hafif ses ve video giden çağrı deneyimi. Azure Communication Services grup KIMLIĞINI kullanarak Grup çağrılmasını destekler. Bileşik, bir Azure Iletişim Hizmetleri kimliğine veya Azure üzerinden temin telefon numarası kullanılarak PSTN için bir telefon numarasına başvuruda bulunarak bire bir çağırmanın kullanılmasına izin verir.                                    | React |  |  |
| Grup sohbeti Composite    | Akıcı Kullanıcı arabirimi tasarım varlıklarını kullanarak Azure Iletişim Hizmetleri için hafif sohbet deneyimi. Bu deneyim, Azure Iletişim Hizmetleri iş parçacıklarıyla bağlantı kurmak için basit bir sohbet istemcisi sunma konusunda yoğunlaşır. Kullanıcıların ileti göndermesini ve yazma göstergeleri ve okundu alındıları ile alınan iletileri görmesini sağlar. 1:1, sohbet senaryolarını grup olarak ölçeklendirir. Tek bir sohbet iş parçacığını destekler.                         | React |  |  |

## <a name="ui-framework-base-components"></a>UI çerçevesi temel bileşenleri

| Bileşen             | Açıklama                                                                                                                                                                                                                                                                        | Web   | Android | iOS |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|---------|-----|
| Çağıran sağlayıcı    | Çağıran bileşeni çağırmak için çekirdek başlatılıyor. Daha sonra üzerine diğer bileşenleri başlatmak için gerekli bileşen. Azure Communication Services erişim belirteçlerini kullanarak çağıran istemciyi başlatmak için çekirdek mantığı işler. Gruba katılmayı destekler. | React | Yok     | Yok |
| Medya denetimleri   | Kullanıcıların, sessiz geçiş yaparak, videoyu açıp kapatarak ve çağrıyı sonlandırarak geçerli çağrıyı yönetmesine olanak tanır.                                                                                                                                                              | React | Yok     | Yok |
| Medya Galerisi   | Tüm çağrı katılımcılarını tek bir galeride gösterin. Galeri hem video etkin hem de statik katılımcıları destekler. Video etkin katılımcılar için video işlenir.                                                                                                                | React | Yok     | Yok |
| Mikrofon ayarları | Çağırmak için kullanılacak mikrofonu seçin. Bu denetim, mikrofon cihazını seçme çağrısı sırasında ve öncesinde kullanılabilir.                                                                                                                                               | React | Yok     | Yok |
| Kamera ayarları     | Video çağırma için kullanılacak kamerayı seçin. Bu denetim, video cihazını seçme çağrısı sırasında ve öncesinde kullanılabilir.                                                                                                                                             | React | Yok     | Yok |
| Cihaz ayarları     | Mikrofon ve kamera ayarlarını tek bir bileşende birleştirir                                                                                                 | React | Yok     | Yok |
| Sohbet sağlayıcısı       | Sohbet için bileşen başlatılıyor. Daha sonra üzerine diğer bileşenleri başlatmak için gerekli bileşen. Bir Azure Iletişim Hizmetleri erişim belirteci ve katılacağız iş parçacığı ile sohbet istemcisini başlatmak için çekirdek mantığı işler.                                     | React | Yok     | Yok |
| Gönderme kutusu          | Kullanıcıların sohbet iş parçacığına ileti göndermesini sağlayan giriş bileşeni. Giriş, diğer alfabeller dahil metin, köprü, emojıs ve diğer Unicode karakterlerini destekler.                                                                                                                         | React | Yok     | Yok |
| Sohbet Iş parçacığı           | Kullanıcının gönderici bilgilerine sahip ve gönderilen iletileri gösteren iş parçacığı bileşeni. İş parçacığı, yazma göstergelerini ve okundu bilgilerini destekler. Bu iş parçacıklarını, sohbet geçmişini gözden geçirmek için kaydırabilirsiniz.
| Katılımcı listesi      | Çağrının veya sohbet iş parçacığının tüm katılımcılarını liste olarak gösterin.  | React | Yok     | Yok |

## <a name="ui-framework-capabilities"></a>UI çerçevesi özellikleri

| Özellik                                                             | Bileşik çağıran Grup | Grup sohbeti Composite | Temel bileşenler |
|---------------------------------------------------------------------|-------------------------|----------------------|-----------------|
| Ekip toplantısına katılarak                                                  |                         |                      |           
| Ekipler canlı olayına katılın                                               |                         |                      | 
| Ekipler kullanıcısına VoIP çağrısı Başlat                                       |                         |                      | 
| Bir ekipte sohbet toplantısına katılın                                           |                         |                      |            
| Grup kimliğiyle Azure Communication Services çağrısına katılarak                | ✔                      |                      | ✔
| Bir veya daha fazla Azure iletişim hizmetleri kullanıcısı için bir VoIP çağrısı başlatın |                         |                      |           
| Azure Communication Services sohbet iş parçacığına katılın                    |                         | ✔                   | ✔
| Çağrıyı susturma/aç                                                    | ✔                       |                      | ✔
| Arama sırasında video açık/kapalı                                                | ✔                       |                      | ✔
| Ekran paylaşımı                                                      | ✔                       |                      | ✔
| Katılımcı Galerisi                                                 | ✔                       |                      | ✔
| Mikrofon yönetimi                                               | ✔                       |                      | ✔
| Kamera yönetimi                                                   | ✔                       |                      | ✔
| Lobide çağır                                                          |                         |                      | ✔
| Sohbet iletisi gönder                                                   |                         | ✔                   |            
| Sohbet iletisi al                                                |                         | ✔                   | ✔
| Yazma göstergeleri                                                   |                         | ✔                   | ✔
| Okundu bilgisi                                                        |                         | ✔                   | ✔
| Katılımcı listesi                                                    |                         |                      | ✔


## <a name="customization-support"></a>Özelleştirme desteği

| Bileşen türü            | Temalar     | Layout                                                              | Veri modelleri |
|---------------------------|------------|---------------------------------------------------------------------|-------------|
| Bileşik bileşen       |     Yok    | Yok                                                                 |     Yok     |
| Temel bileşen            |     Yok    | Bileşenlerin düzeni, dış stil oluşturma kullanılarak değiştirilebilir         |     Yok     |


## <a name="platform-support"></a>Platform desteği

| SDK    | Windows            | Mac OS                | Ubuntu   | Linux    | Android  | iOS        |
|--------|--------------------|----------------------|----------|----------|----------|------------|
| Uı SDK 'Sı | Chrome \* , yeni kenar | Chrome \* , Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Uygulamasını\*\* |

\*Chrome 'un en son sürümünün önceki iki sürüme ek olarak desteklendiğini unutmayın.

\*\*Safari sürümlerinin 13.1 + desteklendiğini unutmayın. Safari macOS için giden video henüz desteklenmiyor, ancak iOS üzerinde destekleniyor. Giden ekran paylaşımı yalnızca masaüstü iOS üzerinde destekleniyor.
