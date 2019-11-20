---
title: 'Hızlı başlangıç: IoT için Azure Güvenlik Merkezi için özel uyarılar oluşturma'
description: Bu hızlı başlangıçta IoT için Azure Güvenlik Merkezi için özel cihaz uyarıları oluşturun ve atayın.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: eca5d69efb04cf8210b0b2aa502bcee5cd4f5264
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904133"
---
# <a name="quickstart-create-custom-alerts"></a>Hızlı başlangıç: özel uyarılar oluşturma


Özel güvenlik grupları ve uyarılar kullanarak, IoT çözümünüz genelinde daha iyi güvenlik sağlamak için uçtan uca güvenlik bilgilerinin ve kategorik cihaz bilgilerinin tam avantajlarından yararlanır. 

## <a name="why-use-custom-alerts"></a>Özel uyarılar neden kullanılmalıdır? 

IoT cihazlarınızı en iyi şekilde anlarsınız.

Beklenen cihaz davranışlarını tam olarak anlayan müşteriler için IoT için Azure Güvenlik Merkezi, bu anlalamayı, beklenen, normal davranıştaki herhangi bir sapmayla ilgili bir cihaz davranışı ilkesi ve uyarısına çevirmenizi sağlar.

## <a name="security-groups"></a>Güvenlik grupları

Güvenlik grupları, mantıksal cihaz gruplarını tanımlamanızı ve güvenlik durumlarını merkezi bir şekilde yönetmenizi sağlar.

Bu gruplar belirli donanımlar olan cihazları, belirli bir konumda dağıtılan cihazları veya özel gereksinimlerinize uygun diğer grupları temsil edebilir.

Güvenlik grupları, **SecurityGroup**adlı bir Device ikizi Tag özelliği tarafından tanımlanır. Varsayılan olarak, IoT Hub her bir IoT çözümünün **varsayılan**adlı bir güvenlik grubu vardır. Bir cihazın güvenlik grubunu değiştirmek için **SecurityGroup** özelliğinin değerini değiştirin.
 
Örneğin:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

Cihazlarınızı mantıksal kategoriler halinde gruplandırmak için güvenlik gruplarını kullanın. Grupları oluşturduktan sonra, en etkili uçtan uca IoT güvenlik çözümü için bunları tercih ettiğiniz özel uyarılara atayın. 

## <a name="customize-an-alert"></a>Bir uyarıyı özelleştirme

1. IoT Hub açın. 
2. **Güvenlik** bölümünde **özel uyarılar** ' a tıklayın. 
3. Özelleştirmeyi uygulamak istediğiniz güvenlik grubunu seçin. 
4. **Özel uyarı Ekle**' ye tıklayın.
5. Açılan listeden özel bir uyarı seçin. 
6. Gerekli özellikleri düzenleyin, **Tamam**' a tıklayın.
7. **Kaydet**' e tıkladığınızdan emin olun. Yeni uyarının kaydedilmeksizin, IoT Hub bir sonraki kapatıyorsunuz uyarı silinir.

 
## <a name="alerts-available-for-customization"></a>Özelleştirmeler için kullanılabilir uyarılar

Aşağıdaki tabloda, özelleştirme için kullanılabilen uyarıların bir özeti verilmiştir.


| Severity | Ad | Veri kaynağı | Açıklama | Önerilen düzeltme|
|---|---|---|---|---|
| Düşük      | Özel uyarı-AMQP protokolündeki cihaz iletilerinin bulut sayısı izin verilen aralığın dışında          | IoT Hub     | Belirli bir zaman penceresi içindeki bulut-cihaz iletileri (AMQP Protokolü) sayısı, şu anda yapılandırılmış ve izin verilen aralığın dışında.||
| Düşük      | Özel uyarı-AMQP protokolündeki cihaz iletilerine reddedilen bulut sayısı izin verilen aralığın dışında | IoT Hub     | Cihaz tarafından reddedilen bulut-cihaz iletileri (AMQP Protokolü) sayısı, belirli bir zaman penceresi içinde yapılandırılmış ve izin verilen aralığın dışında.||
| Düşük      | Özel uyarı-AMQP protokolündeki bulut iletilerine yönelik cihaz sayısı izin verilen aralığın dışında      | IoT Hub     | Belirli bir zaman penceresi içindeki bulut iletilerine (AMQP Protokolü) cihaz miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında.|   |
| Düşük      | Özel uyarı-çağıran doğrudan Yöntem sayısı izin verilen aralığın dışında | IoT Hub     | Belirli bir zaman penceresi içinde doğrudan yöntem çağıran miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında.||
| Düşük      | Özel uyarı-dosya yükleme sayısı izin verilen aralığın dışında | IoT Hub     | Belirli bir zaman penceresi içindeki dosya karşıya yükleme miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında.| |
| Düşük      | Özel uyarı-HTTP protokolündeki cihaz iletileri için bulut sayısı izin verilen aralığın dışında | IoT Hub     | Bir zaman penceresinde bulut-cihaz iletileri (HTTP protokolü) miktarı yapılandırılmış izin verilen aralıkta değil                                  |
| Düşük      | Özel uyarı-HTTP protokolündeki cihaz iletilerine reddedilen bulut sayısı izin verilen aralıkta değil | IoT Hub     | Belirli bir zaman penceresi içindeki bulut-cihaz iletileri (HTTP protokolü) miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |
| Düşük      | Özel uyarı-HTTP protokolündeki bulut iletilerine yönelik cihaz sayısı izin verilen aralığın dışında | IoT Hub| Belirli bir zaman penceresi içindeki bulut iletilerine (HTTP protokolü) cihaz miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında.|    |
| Düşük      | Özel uyarı-MQTT protokolünde cihaz iletilerine yönelik bulut sayısı izin verilen aralığın dışında | IoT Hub     | Belirli bir zaman penceresi içindeki bulut-cihaz iletileri (MQTT Protokolü) miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında.|   |
| Düşük      | Özel uyarı-MQTT protokolünde cihaz iletilerine reddedilen bulut sayısı izin verilen aralığın dışında | IoT Hub     | Belirli bir zaman penceresi içinde cihaz tarafından reddedilen bulut ve cihaz iletileri (MQTT Protokolü) miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |
| Düşük      | Özel uyarı-MQTT protokolünde bulut iletilerine yönelik cihaz sayısı izin verilen aralığın dışında          | IoT Hub     | Belirli bir zaman penceresi içindeki bulut iletilerine (MQTT Protokolü) cihaz miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında.|
| Düşük      | Özel uyarı-komut kuyruğu temizler sayısı izin verilen aralığın dışında                               | IoT Hub     | Komut kuyruğunun miktarı belirli bir zaman penceresi içinde temizler ve şu anda yapılandırılmış ve izin verilen aralığın dışında.||
| Düşük      | Özel uyarı-modül ikizi güncelleştirmelerinin sayısı izin verilen aralığın dışında                                       | IoT Hub     | Belirli bir zaman penceresi içindeki modül ikizi güncelleştirmelerinin miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında.|
| Düşük      | Özel uyarı-yetkisiz işlem sayısı izin verilen aralığın dışında  | IoT Hub     | Belirli bir zaman penceresi içindeki yetkisiz işlem miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında.|
| Düşük      | Özel uyarı-etkin bağlantı sayısı izin verilen aralığın dışında  | Aracı       | Belirli bir zaman penceresi içindeki etkin bağlantı sayısı, şu anda yapılandırılmış ve izin verilen aralığın dışında.|  Cihaz günlüklerini araştırın. Bağlantının nereden geldiğini öğrenin ve zararsız veya kötü amaçlı olup olmadığını öğrenin. Kötü amaçlı, olası kötü amaçlı yazılımları kaldırın ve kaynağı anlayın. Benign ise, kaynağı izin verilen bağlantı listesine ekleyin.  |
| Düşük      | Özel uyarı-izin verilmeyen bir IP 'ye giden bağlantı oluşturuldu                             | Aracı       | İzin verilen IP listenizin dışında bir IP 'ye giden bağlantı oluşturuldu. |Cihaz günlüklerini araştırın. Bağlantının nereden geldiğini öğrenin ve zararsız veya kötü amaçlı olup olmadığını öğrenin. Kötü amaçlı, olası kötü amaçlı yazılımları kaldırın ve kaynağı anlayın. Benign ise, kaynağı izin verilen IP listesine ekleyin.                        |
| Düşük      | Özel uyarı-başarısız yerel oturum açma sayısı izin verilen aralığın dışında                               | Aracı       | Belirli bir zaman penceresi içindeki başarısız olan yerel oturum açma miktarı, yapılandırılmış ve izin verilen aralığın dışında. |   |
| Düşük      | Özel uyarı-izin verilen kullanıcı listesinde olmayan bir kullanıcının oturum açması | Aracı       | İzin verilen Kullanıcı listenizin dışında, cihazda oturum açmış yerel bir kullanıcı.|  Ham verileri kaydediyorsanız, Log Analytics hesabınıza gidip cihazı araştırmak için verileri kullanın, kaynağı belirleyip, bu ayarlar için izin verme/engelleme listesini onarın. Şu anda ham verileri kaydettiğinizden cihaza gidin ve bu ayarlar için izin verme/engelleme listesini onarın.|
| Düşük      | Özel uyarı-izin verilmeyen bir işlem yürütüldü | Aracı       | Cihazda izin verilmeyen bir işlem yürütüldü. |Ham verileri kaydediyorsanız, Log Analytics hesabınıza gidip cihazı araştırmak için verileri kullanın, kaynağı belirleyip, bu ayarlar için izin verme/engelleme listesini onarın. Şu anda ham verileri kaydettiğinizden cihaza gidin ve bu ayarlar için izin verme/engelleme listesini onarın.  |
|


## <a name="next-steps"></a>Sonraki adımlar

Bir güvenlik aracısının nasıl dağıtılacağını öğrenmek için bir sonraki makaleye ilerleyin...

> [!div class="nextstepaction"]
> [Güvenlik aracısı dağıtma](how-to-deploy-agent.md)
