---
title: Uyarı türleri ve açıklamaları
description: IoT uyarı açıklamaları için Defender 'ı inceleyin.
ms.date: 4/8/2021
ms.topic: how-to
ms.openlocfilehash: 483563b53a5849b0354986269568bc42b9124cc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478004"
---
# <a name="alert-types-and-descriptions"></a>Uyarı türleri ve açıklamaları

Bu makalede, IoT altyapılarına yönelik Defender 'dan oluşturulabilecek tüm uyarı türleri açıklanmaktadır. Uyarılar, uyarı olayını yönetmenizi sağlayan uyarılar penceresinde görüntülenir. 

## <a name="policy-engine-alerts"></a>İlke altyapısı uyarıları

İlke altyapısı uyarıları, öğrenilen taban çizgisi davranışından algılanan sapmaları tanımlıyor.

| Başlık  | Açıklama | Önem derecesi |
|--|--|--|
| MAC adreslerinin olağan dışı kullanımı | Ağda yeni bir kaynak cihaz algılandı, ancak yetkilendirilmedi. | İkincil |
| Beckhoff yazılımı değişti | Üretici yazılımı bir kaynak cihazda güncelleştirildi. Bu, örneğin planlı bir bakım yordamı gibi yetkili etkinlik olabilir. | Ana |
| Veritabanı oturum açma başarısız | Kaynak cihazdan hedef sunucuya başarısız bir oturum açma girişimi algılandı. Bu, insan hatasının sonucu olabilir, ancak aynı zamanda sunucu veya üzerindeki verileri tehlikeye atabilir. | Ana |
| Acil son ROC üretici yazılımı sürümü değişti | Üretici yazılımı bir kaynak cihazda güncelleştirildi. Bu, örneğin planlı bir bakım yordamı gibi yetkili etkinlik olabilir. | Ana |
| Internet ile iletilen ağ içindeki dış adres | Ağınızın bir parçası olarak tanımlanan bir kaynak cihaz, Internet adresleriyle iletişim kuruyor. Kaynak, Internet adresleriyle iletişim kurmayı yetkilendirilmemiş. | Kritik |
| Alan cihazının beklenmedik şekilde bulduğu | Ağda yeni bir kaynak cihaz algılandı, ancak yetkilendirilmedi. | Ana |
| Üretici yazılımı değişikliği algılandı | Üretici yazılımı bir kaynak cihazda güncelleştirildi. Bu, örneğin planlı bir bakım yordamı gibi yetkili etkinlik olabilir. | Ana |
| Üretici yazılımı sürümü değişti | Üretici yazılımı bir kaynak cihazda güncelleştirildi. Bu, örneğin planlı bir bakım yordamı gibi yetkili etkinlik olabilir. | Ana |
| Foxboro g/yetkisiz bir Işlem | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| FTP oturum açma başarısız | Kaynak cihazdan hedef sunucuya başarısız bir oturum açma girişimi algılandı. Bu, insan hatasının sonucu olabilir, ancak aynı zamanda sunucu veya üzerindeki verileri tehlikeye atabilir. | Ana |
| İşlev kodu, yetkisiz özel durum oluşturdu | Kaynak cihaz (bağımlı), hedef cihaza (ana) bir özel durum döndürdü. | Ana |
| GOOSE Ileti türü ayarları | Bir kaynak cihazda ileti (protokol KIMLIĞI ile tanımlanır) ayarları değiştirildi. | Uyarı |
| Honeywell üretici yazılımı sürümü değişti | Üretici yazılımı bir kaynak cihazda güncelleştirildi. Bu, örneğin planlı bir bakım yordamı gibi yetkili etkinlik olabilir. | Ana |
| Geçersiz HTTP Iletişimi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Internet erişimi algılandı | Ağınızın bir parçası olarak tanımlanan bir kaynak cihaz, Internet adresleriyle iletişim kuruyor. Kaynak, Internet adresleriyle iletişim kurmayı yetkilendirilmemiş. | Ana |
| Mitsubishi üretici yazılımı sürümü değişti | Üretici yazılımı bir kaynak cihazda güncelleştirildi. Bu, örneğin planlı bir bakım yordamı gibi yetkili etkinlik olabilir. | Ana |
| Modbus adres aralığı Ihlali | Bir ana cihaz yeni bir bağımlı bellek adresine erişim istedi. | Ana |
| Modbus üretici yazılımı sürümü değişti | Üretici yazılımı bir kaynak cihazda güncelleştirildi. Bu, örneğin planlı bir bakım yordamı gibi yetkili etkinlik olabilir. | Ana |
| Yeni etkinlik algılandı-CıP sınıfı | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-CıP sınıf hizmeti | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-CıP PCCC komutu | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-CıP simgesi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-EtherNet/IP g/ç bağlantısı | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-EtherNet/IP protokol komutu | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-GSM Ileti kodu | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-LonTalk komut kodları | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni bağlantı noktası bulma | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Uyarı |
| Yeni etkinlik algılandı-LonTalk ağ değişkeni | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-korumalı veri Isteği | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-okuma/yazma komutu (AMS dizin grubu) | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-okuma/yazma komutu (AMS Dizin boşluğu) | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-yetkisiz DeltaV Ileti türü | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-yetkisiz DeltaV ROC Işlemi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-yetkisiz RPC Ileti türü | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-yetkisiz RPC yordam çağırma | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-AMS protokol komutunu kullanma | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-Siemens SICAM komutu kullanılıyor | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-Suitelink protokol komutu kullanılıyor | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-Suitelink protokol oturumları kullanılıyor | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni etkinlik algılandı-Yokogawa Vnetıp komutu kullanılıyor | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yeni varlık algılandı | Ağda yeni bir kaynak cihaz algılandı, ancak yetkilendirilmedi. | Ana |
| Yeni LLDP cihaz yapılandırması | Ağda yeni bir kaynak cihaz algılandı, ancak yetkilendirilmedi. | Ana |
| Yeni bağlantı noktası bulma | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Uyarı |
| Omron FINS komutu | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| S7 Plus PLC üretici yazılımı değişti | Üretici yazılımı bir kaynak cihazda güncelleştirildi. Bu, örneğin planlı bir bakım yordamı gibi yetkili etkinlik olabilir. | Ana |
| Örneklenmiş değerler Ileti türü ayarları | Bir kaynak cihazda ileti (protokol KIMLIĞI ile tanımlanır) ayarları değiştirildi. | Uyarı |
| Geçersiz bütünlük taraması şüphesi | Bir DNP3 kaynak cihazında (outstation) tarama algılandı. Bu tarama, ağınızda öğrenilen trafik olarak yetkilendirilmedi. | Ana |
| Toshiba Computer bağlantısı yetkisiz komutu | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | İkincil |
| Yetkisiz ABB Totalflow dosyası Işlemi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz ABB Totalflow kayıt Işlemi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Siemens S7 veri bloğuna yetkisiz erişim | Kaynak cihaz, başka bir cihazdaki kaynağa erişmeye çalıştı. Bu iki cihaz arasında bu kaynağa erişim denemesi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. | Uyarı |
| Siemens S7 Plus nesnesine yetkisiz erişim | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Wonderware etiketine yetkisiz erişim | Kaynak cihaz, başka bir cihazdaki kaynağa erişmeye çalıştı. Bu iki cihaz arasında bu kaynağa erişim denemesi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. | Ana |
| Yetkisiz BACNet nesne erişimi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz BACNet rotası | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz veritabanı oturum açma | Kaynak istemci ile hedef sunucu arasında bir oturum açma denemesi algılandı. Bu cihazlar arasındaki iletişim, ağınızda öğrenilmiş trafik olarak yetkilendirilmemiştir. | Ana |
| Yetkisiz veritabanı Işlemi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz acil bir son ROC Işlemi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkilendirilmemiş GE SRTP dosya erişimi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz GE SRTP protokol komutu | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkilendirilmemiş GE SRTP sistem belleği Işlemi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz HTTP etkinliği | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz HTTP sunucusu | Kaynak cihazda yetkisiz bir uygulama algılandı. Uygulama, ağınızda öğrenilen bir uygulama olarak yetkilendirilmemiştir. | Ana |
| Yetkisiz HTTP SOAP eylemi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz HTTP Kullanıcı Aracısı | Kaynak cihazda yetkisiz bir uygulama algılandı. Uygulama, ağınızda öğrenilen bir uygulama olarak yetkilendirilmemiştir. | Ana |
| Yetkisiz Internet bağlantısı algılandı | Ağınızın bir parçası olarak tanımlanan bir kaynak cihaz, Internet adresleriyle iletişim kuruyor. Kaynak, Internet adresleriyle iletişim kurmayı yetkilendirilmemiş. | Kritik |
| Yetkisiz Mitsubishi MELSEC komutu | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz MMS program erişimi | Kaynak cihaz, başka bir cihazdaki kaynağa erişmeye çalıştı. Bu iki cihaz arasında bu kaynağa erişim denemesi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. | Ana |
| Yetkisiz MMS hizmeti | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz çok noktaya yayın/yayın bağlantısı | Kaynak cihaz ve diğer cihazlar arasında çok noktaya yayın/yayın bağlantısı algılandı. Çok noktaya yayın/yayın iletişimi yetkilendirilmemiş. | Kritik |
| Yetkisiz ad sorgusu | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz OPC UA etkinliği | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz OPC UA Isteği/yanıtı | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Kullanıcı tanımlı bir kural tarafından yetkisiz Işlem algılandı | İki cihaz arasında trafik algılandı. Bu etkinlik, bir kullanıcı tarafından tanımlanan özel bir uyarı kuralına bağlı olarak yetkilendirilmemiş. | Ana |
| Yetkisiz PLC yapılandırması okuma | Kaynak cihaz bir programlama aygıtı olarak tanımlanmamıştır, ancak hedef denetleyicide okuma/yazma işlemi gerçekleştirdi. Programlama değişiklikleri yalnızca programlama cihazları tarafından gerçekleştirilmelidir. Bu cihaza bir programlama uygulaması yüklenmiş olabilir. | Uyarı |
| Yetkisiz PLC yapılandırma yazma | Kaynak cihaz, hedef denetleyicinin programını okumak/yazmak için bir komut gönderdi. Bu etkinlik daha önce görülmedi. | Ana |
| Yetkisiz PLC Program yükleme | Kaynak cihaz, hedef denetleyicinin programını okumak/yazmak için bir komut gönderdi. Bu etkinlik daha önce görülmedi. | Ana |
| Yetkisiz PLC programlama | Kaynak cihaz bir programlama aygıtı olarak tanımlanmamıştır, ancak hedef denetleyicide okuma/yazma işlemi gerçekleştirdi. Programlama değişiklikleri yalnızca programlama cihazları tarafından gerçekleştirilmelidir. Bu cihaza bir programlama uygulaması yüklenmiş olabilir. | Kritik |
| Yetkisiz kullanıma al çerçeve türü | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz SAıA S-Bus komutu | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Denetim Işlevinin yetkisiz Siemens S7 yürütmesi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Kullanıcı tanımlı Işlevin yetkisiz Siemens S7 yürütmesi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz Siemens S7 Plus erişimi engelle | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz Siemens S7 Plus Işlemi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz SMB oturum açma | Kaynak istemci ile hedef sunucu arasında bir oturum açma denemesi algılandı. Bu cihazlar arasındaki iletişim, ağınızda öğrenilmiş trafik olarak yetkilendirilmemiştir. | Ana |
| Yetkisiz SNMP Işlemi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz SSH erişimi | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| Yetkisiz Windows Işlemi | Kaynak cihazda yetkisiz bir uygulama algılandı. Uygulama, ağınızda öğrenilen bir uygulama olarak yetkilendirilmemiştir. | Ana |
| Yetkisiz Windows hizmeti | Kaynak cihazda yetkisiz bir uygulama algılandı. Uygulama, ağınızda öğrenilen bir uygulama olarak yetkilendirilmemiştir. | Ana |
| Kullanıcı tanımlı bir kural tarafından yetkisiz Işlem algılandı | Yeni trafik parametreleri algılandı. Bu parametre bileşimi Kullanıcı tanımlı bir kuralı ihlal ediyor | Ana |
| İzin verilmeyen Modbus Schneider Elektrik uzantısı | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| İzin verilmeyen ASDU türleri kullanımı | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| İzin verilmeyen DNP3 Işlev kodu kullanımı | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |
| İzin verilmeyen Iç gösterge kullanımı (ııN) | Bir DNP3 kaynak aygıtı (outstation), ağınızda öğrenilmiş trafik olarak yetkilendirilmemiş bir iç gösterge (ııN) bildirdi. | Ana |
| Modbus Işlev kodunun izin verilmeyen kullanımı | Yeni trafik parametreleri algılandı. Bu parametre bileşimi, ağınızda öğrenilen trafik olarak yetkilendirilmemiştir. Aşağıdaki bileşim yetkilendirilmemiş. | Ana |

## <a name="anomaly-engine-alerts"></a>Anomali motoru uyarıları

Anomali altyapısı uyarıları, ağ etkinliğinde algılanan bozukluklar hakkında açıklama.

| Başlık | Açıklama | Önem derecesi |
|--|--|--|
| Bağımlı içindeki olağan dışı özel durum deseninin | Kaynak cihazda çok fazla sayıda hata algılandı. Bu işlem sorunu nedeniyle ortaya çıkabilir. | İkincil |
| Olağan dışı HTTP üst bilgisi uzunluğu | Kaynak cihaz olağan dışı bir ileti gönderdi. Bu, hedef cihaza saldırmayı denemek anlamına gelebilir. | Kritik |
| HTTP üstbilgisindeki anormal sayıda parametre | Kaynak cihaz olağan dışı bir ileti gönderdi. Bu, hedef cihaza saldırmayı denemek anlamına gelebilir. | Kritik |
| Iletişim kanalında olağan dışı düzenli davranış | Kaynak ve hedef aygıtlar arasındaki iletişimin sıklığında değişiklik algılandı. | İkincil |
| Uygulamaların olağan dışı sonlandırması | Kaynak cihazda çok fazla sayıda durdurma komutu algılandı. Bu, bir işlemsel sorunun veya cihazı değiştirme denemesinin sonucu olabilir. | Ana |
| Olağan dışı trafik bant genişliği | Bir kanalda olağan dışı bant genişliği algılandı. Bant genişliği, daha önce algılanan sayıdan önemli ölçüde düşük/yüksek görünüyor. Ayrıntılar için, toplam bant genişliği pencere öğesiyle çalışın. | Uyarı |
| Cihazlar arasında olağan dışı trafik bant genişliği | Bir kanalda olağan dışı bant genişliği algılandı. Bant genişliği, daha önce algılanan sayıdan önemli ölçüde düşük/yüksek görünüyor. Ayrıntılar için, toplam bant genişliği pencere öğesiyle çalışın. | Uyarı |
| Adres taraması algılandı | Ağ aygıtlarını taramak için bir kaynak cihaz algılandı. Bu cihaz bir ağ tarama cihazı olarak yetkilendirilmedi. | Kritik |
| ARP adres taraması algılandı | Ağ aygıtlarını, Adres Çözümleme Protokolü (ARP) kullanılarak taranan bir kaynak cihaz algılandı. Bu cihaz adresi geçerli bir ARP tarama adresi olarak yetkilendirilmemiş. | Kritik |
| ARP adres taraması algılandı | Ağ aygıtlarını, Adres Çözümleme Protokolü (ARP) kullanılarak taranan bir kaynak cihaz algılandı. Bu cihaz adresi geçerli bir ARP tarama adresi olarak yetkilendirilmemiş. | Kritik |
| ARP aldatmacası | Ağda anormal miktarda paket algılandı. Bu, örneğin bir ARP yanıltma veya ıCMP taşması saldırısı gibi bir saldırı olduğunu gösterebilir. | Uyarı |
| Fazla oturum açma denemesi | Bir hedef sunucuya çok fazla oturum açma denemesi gerçekleştirmede bir kaynak cihaz görüldü. Bu bir deneme yanılma saldırısı olabilir. Sunucu kötü niyetli bir aktör tarafından tehlikeye girebilir. | Kritik |
| Fazla sayıda oturum | Bir hedef sunucuya çok fazla oturum açma denemesi gerçekleştirmede bir kaynak cihaz görüldü. Bu bir deneme yanılma saldırısı olabilir. Sunucu kötü niyetli bir aktör tarafından tehlikeye girebilir. | Kritik |
| Bir Outstation aşırı yeniden başlatma oranı | Kaynak cihazda çok fazla sayıda yeniden başlatma komutu algılandı. Bu, bir işlemsel sorunun veya cihazı değiştirme denemesinin sonucu olabilir. | Ana |
| Aşırı SMB oturum açma denemesi | Bir hedef sunucuya çok fazla oturum açma denemesi gerçekleştirmede bir kaynak cihaz görüldü. Bu bir deneme yanılma saldırısı olabilir. Sunucu kötü niyetli bir aktör tarafından tehlikeye girebilir. | Kritik |
| ICMP taşması | Ağda anormal miktarda paket algılandı. Bu, örneğin bir ARP yanıltma veya ıCMP taşması saldırısı gibi bir saldırı olduğunu gösterebilir. | Uyarı |
| Geçersiz HTTP üstbilgisi Içeriği | Kaynak cihaz geçersiz bir istek başlattı. | Kritik |
| Etkin olmayan Iletişim kanalı | İki cihaz arasındaki iletişim kanalı, etkinliğin genellikle görülen bir dönemde etkin değil. Bu, bu trafiği üreten programın değiştirildiğini veya programın kullanılamadığını gösterebilir. Yüklü programın yapılandırmasını gözden geçirmeniz ve düzgün şekilde yapılandırıldığını doğrulamanız önerilir. | Uyarı |
| Uzun süreli adres taraması algılandı | Ağ aygıtlarını taramak için bir kaynak cihaz algılandı. Bu cihaz bir ağ tarama cihazı olarak yetkilendirilmedi. | Kritik |
| Parola tahmin girişimi algılandı | Bir hedef sunucuya çok fazla oturum açma denemesi gerçekleştirmede bir kaynak cihaz görüldü. Bu bir deneme yanılma saldırısı olabilir. Sunucu kötü niyetli bir aktör tarafından tehlikeye girebilir. | Kritik |
| PLC taraması algılandı | Ağ aygıtlarını taramak için bir kaynak cihaz algılandı. Bu cihaz bir ağ tarama cihazı olarak yetkilendirilmedi. | Kritik |
| Bağlantı noktası taraması algılandı | Ağ aygıtlarını taramak için bir kaynak cihaz algılandı. Bu cihaz bir ağ tarama cihazı olarak yetkilendirilmedi. | Kritik |
| Beklenmeyen ileti uzunluğu | Kaynak cihaz olağan dışı bir ileti gönderdi. Bu, hedef cihaza saldırmayı denemek anlamına gelebilir. | Kritik |
| Standart bağlantı noktası için beklenmeyen trafik | Başka bir protokol için ayrılmış bağlantı noktası kullanan bir cihazda trafik algılandı. | Ana |

## <a name="protocol-violation-engine-alerts"></a>Protokol ihlali altyapı uyarıları

Protokol altyapısı uyarıları, paket yapısında algılanan sapmaları veya protokol belirtimlerine kıyasla alan değerlerini anlatır.

| Başlık | Açıklama | Önem derecesi |
|--|--|--|
| Tek bir oturumda aşırı hatalı biçimlendirilmiş paketler | Kaynak cihazdan hedef cihaza gönderilen anormal sayıda hatalı biçimlendirilmiş paket. Bu durum, hatalı iletişimleri veya hedeflenen cihazı değiştirme denemesini gösterebilir. | Ana |
| Üretici Yazılımı Güncelleştirmesi | Kaynak cihaz, bir hedef cihazda bellenimi güncelleştirmek için bir komut gönderdi. Hedef cihaza yapılan son programlama, yapılandırma ve bellenim yükseltmelerinin geçerli olduğunu doğrulayın. | Uyarı |
| İşlev kodu Outstation tarafından desteklenmiyor | Hedef cihaz geçersiz bir istek aldı. | Ana |
| Geçersiz BACNet iletisi | Kaynak cihaz geçersiz bir istek başlattı. | Ana |
| 0 numaralı bağlantı noktasında geçersiz bağlantı girişimi | Kaynak cihaz, sıfır (0) numaralı bağlantı noktası numarasında hedef cihaza bağlanmaya çalıştı. TCP için, bağlantı noktası 0 ayrılmıştır ve kullanılamaz. UDP için bağlantı noktası isteğe bağlıdır ve 0 değeri bağlantı noktası olmaz. Genellikle, 0 numaralı bağlantı noktasını dinleyen bir sistem üzerinde hiçbir hizmet yoktur. Bu olay, hedef cihaza saldırmak için bir girişim olduğunu belirtebilir veya bir uygulamanın yanlış bir şekilde programlandığını gösteriyor olabilir. | İkincil |
| Geçersiz DNP3 Işlemi | Kaynak cihaz geçersiz bir istek başlattı. | Ana |
| Geçersiz MODBUS Işlemi (ana öğe tarafından oluşturulan özel durum) | Kaynak cihaz geçersiz bir istek başlattı. | Ana |
| Geçersiz MODBUS Işlemi (Işlev kodu sıfır) | Kaynak cihaz geçersiz bir istek başlattı. | Ana |
| Geçersiz protokol sürümü | Kaynak cihaz geçersiz bir istek başlattı. | Ana |
| Outstation 'a yanlış parametre gönderildi | Hedef cihaz geçersiz bir istek aldı. | Ana |
| Eski bir Işlev kodunun başlatılması (verileri başlatma) | Kaynak cihaz geçersiz bir istek başlattı. | İkincil |
| Eski bir Işlev kodunun başlatılması (yapılandırmayı Kaydet) | Kaynak cihaz geçersiz bir istek başlattı. | İkincil |
| Ana öğe bir uygulama katmanı onayı Istedi | Kaynak cihaz geçersiz bir istek başlattı. | Uyarı |
| Modbus özel durumu | Kaynak cihaz (bağımlı), hedef cihaza (ana) bir özel durum döndürdü. | Ana |
| Bağımlı cihaz geçersiz ASDU türü aldı | Hedef cihaz geçersiz bir istek aldı. | Ana |
| Bağımlı cihaz, aktarımın geçersiz komut nedenini aldı | Hedef cihaz geçersiz bir istek aldı. | Ana |
| Bağımlı cihaz geçersiz ortak adres aldı | Hedef cihaz geçersiz bir istek aldı. | Ana |
| Bağımlı cihaz geçersiz veri adresi parametresi aldı | Hedef cihaz geçersiz bir istek aldı. | Ana |
| Bağımlı cihaz geçersiz veri değeri parametresi aldı | Hedef cihaz geçersiz bir istek aldı. | Ana |
| Bağımlı cihaz geçersiz Işlev kodu aldı | Hedef cihaz geçersiz bir istek aldı. | Ana |
| Bağımlı cihaz geçersiz bilgi nesne adresi aldı | Hedef cihaz geçersiz bir istek aldı. | Ana |
| Bilinmeyen nesne Outstation 'a gönderildi | Hedef cihaz geçersiz bir istek aldı. | Ana |
| Ayrılmış Işlev kodu kullanımı | Kaynak cihaz geçersiz bir istek başlattı. | Ana |
| Outstation tarafından hatalı biçimlendirme kullanımı | Kaynak cihaz geçersiz bir istek başlattı. | Uyarı |
| Ayrılmış durum bayraklarının kullanımı (ııN) | Bir DNP3 kaynak aygıtı (outstation), ayrılmış Iç göstergeyi 2,6 kullandı. Cihazın yapılandırmasını denetlemeniz önerilir. | Uyarı |

## <a name="malware-engine-alerts"></a>Kötü amaçlı yazılım altyapısı uyarıları

Kötü amaçlı yazılım altyapısı uyarıları algılanan kötü amaçlı ağ etkinliğini tanımlıyor.

| Başlık | Açıklama| Önem derecesi |
|--|--|--|
| Bilinen kötü amaçlı IP 'ye bağlantı denemesi | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Ana |
| Geçersiz SMB Iletisi (DoublePulsar Backkapı yerleştirme) | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Kötü amaçlı etki alanı ad Isteği | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Ana |
| Kötü amaçlı yazılım test dosyası algılandı-EICAR AV başarılı | İki cihaz arasındaki trafikte bir EICAR AV test dosyası algılandı. Dosya kötü amaçlı yazılım değil. Virüsten koruma yazılımının doğru şekilde yüklendiğini doğrulamak için kullanılır; bir virüs bulunduğunda ne olacağını gösterir ve bir virüs bulunduğunda iç yordamları ve yeniden eylemleri kontrol edin. Virüsten koruma yazılımı, gerçek bir virüsle, EıCAR 'ı algılamalıdır. | Ana |
| Şüphesi, Conficker kötü amaçlı yazılımı | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Ana |
| Hizmet reddi saldırısı şüphesi | Kaynak cihaz, hedef cihaza çok fazla sayıda yeni bağlantı başlatmayı denedi. Bu, hedef cihaza karşı bir hizmet reddi (DOS) saldırısı olabilir ve cihaz işlevselliğini kesintiye uğratabilir, performansı ve hizmet kullanılabilirliğine yol açabilir veya kurtarılamaz hatalara neden olabilir. | Kritik |
| Kötü amaçlı şüphesi etkinliği | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Ana |
| Şüphesi (BlackEnergy) kötü amaçlı etkinlik | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Kötü amaçlı etkinlik şüphesi (koyu) | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Şüphesi (Duqu) kötü amaçlı etkinlik | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Kötü amaçlı etkinlik şüphesi (Flame) | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Şüphesi (Havex) kötü amaçlı etkinlik | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Kötü amaçlı etkinlik şüphesi (Karagany) | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Kötü amaçlı etkinlik şüphesi (açık Sout) | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Kötü amaçlı etkinlik şüphesi (ad sorguları) | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Ana |
| Şüphesi (Zeivy) kötü amaçlı etkinlik | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Şüphesi of Kötü_niyetli etkinlik (regin) | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Şüphesi of Kötü_niyetli etkinlik (Stuxnet) | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Şüphesi (WannaCry) kötü amaçlı etkinlik | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Ana |
| Şüphesi of NotPetya kötü amaçlı yazılımdan koruma-geçersiz SMB parametreleri algılandı | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| Şüphesi of NotPetya kötü amaçlı yazılımdan koruma-geçersiz SMB Işlemi algılandı | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |
| PsExec ile uzak kod yürütme şüphesi | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Ana |
| Uzak Windows hizmet yönetimi şüphesi | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Ana |
| Uç noktada şüpheli yürütülebilir dosya algılandı | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Ana |
| Şüpheli trafik algılandı | Şüpheli ağ etkinliği algılandı. Bu etkinlik, bilinen kötü amaçlı yazılım tarafından kullanılan bir yöntemden yararlanılan bir saldırıya karşı ilişkilendirilebilir. | Kritik |

## <a name="operational-engine-alerts"></a>İşlemsel altyapı uyarıları

İşletimsel altyapı uyarıları algılanan işletimsel olayları veya hatalı çalışan varlıkları tanımlıyor.

| Başlık | Açıklama | Önem derecesi |
|--|--|--|
| Bir S7 stop PLC komutu gönderildi | Kaynak cihaz, hedef denetleyiciye bir durdur komutu gönderdi. Başlatma komutu gönderilene kadar denetleyici işlemi durdurur. | Uyarı |
| BACNet Işlemi başarısız oldu | Sunucu bir hata kodu döndürdü. Bu, bir sunucu hatasını veya bir istemci tarafından geçersiz bir istek olduğunu gösterir. | Ana |
| Hatalı MMS cihaz durumu | MMS sanal üretim aygıtı (VMD) bir durum iletisi gönderdi. İleti, sunucunun doğru şekilde yapılandırılamayabilir, kısmen çalışır durumda veya hiç çalışmadığını gösterir. | Ana |
| Cihaz yapılandırması değişikliği | Kaynak cihazda bir yapılandırma değişikliği algılandı. | İkincil |
| Outstation 'de sürekli olay arabelleği taşması | Kaynak cihazda bir arabellek taşması olayı algılandı. Olay veri bozulmasına, program kilitlenmelerine veya kötü amaçlı kodların yürütülmesine neden olabilir. | Ana |
| Denetleyici sıfırlama | Kaynak cihaz, hedef denetleyiciye sıfırlama komutu gönderdi. Denetleyici geçici olarak çalışmayı durdurdu ve otomatik olarak yeniden başlatıldı. | Uyarı |
| Denetleyiciyi durdur | Kaynak cihaz, hedef denetleyiciye bir durdur komutu gönderdi. Başlatma komutu gönderilene kadar denetleyici işlemi durdurur. | Uyarı |
| Cihaz dinamik bir IP adresi alamadı | Kaynak cihaz DHCP sunucusundan dinamik bir IP adresi alacak ancak bir adres almamış olacak şekilde yapılandırılmıştır. Bu, cihazdaki bir yapılandırma hatasını veya DHCP sunucusunda bir işlem hatası olduğunu gösterir. Olayın ağ yöneticisine bildirimde olması önerilir | Ana |
| Cihazın bağlantısının kesilmesi şüpheli (yanıt vermiyor) | Kaynak cihaz kendisine gönderilen bir komuta yanıt vermedi. Komut gönderildiğinde bağlantısı kesilmiş olabilir. | Ana |
| EtherNet/IP CIP hizmeti Isteği başarısız oldu | Sunucu bir hata kodu döndürdü. Bu, bir sunucu hatasını veya bir istemci tarafından geçersiz bir istek olduğunu gösterir. | Ana |
| EtherNet/IP kapsülleme Protokolü komutu başarısız oldu | Sunucu bir hata kodu döndürdü. Bu, bir sunucu hatasını veya bir istemci tarafından geçersiz bir istek olduğunu gösterir. | Ana |
| Outstation 'de olay arabelleği taşması | Kaynak cihazda bir arabellek taşması olayı algılandı. Olay veri bozulmasına, program kilitlenmelerine veya kötü amaçlı kodların yürütülmesine neden olabilir. | Ana |
| Beklenen yedekleme Işlemi gerçekleşmedi | Beklenen yedekleme/dosya aktarımı etkinliği iki cihaz arasında gerçekleşmedi. Bu durum yedekleme/dosya aktarma işlemindeki hataları gösterebilir. | Ana |
| GE SRTP komut hatası | Sunucu bir hata kodu döndürdü. Bu, bir sunucu hatasını veya bir istemci tarafından geçersiz bir istek olduğunu gösterir. | Ana |
| GE SRTP stop PLC komutu gönderildi | Kaynak cihaz, hedef denetleyiciye bir durdur komutu gönderdi. Başlatma komutu gönderilene kadar denetleyici işlemi durdurur. | Uyarı |
| GOOSE denetim bloğu daha fazla yapılandırma gerektiriyor | Kaynak cihaz, cihazın komisyona ihtiyacı olduğunu belirten bir GOOSE iletisi gönderdi. Bu, GOOSE denetim bloğunun daha fazla yapılandırma ve GOOSE iletileri kısmen veya tamamen işlemsel olmayan bir şekilde gerektirmesidir. | Ana |
| GOOSE veri kümesi yapılandırması değiştirildi | Kaynak cihazda bir ileti (protokol KIMLIĞI ile tanımlanır) veri kümesi değiştirildi. Bu, cihazın bu ileti için farklı bir veri kümesi rapormayacağı anlamına gelir. | Uyarı |
| Honeywell denetleyicisi beklenmeyen durumu | Bir Honeywell denetleyicisi, bir durum değişikliğini belirten beklenmeyen bir tanılama iletisi gönderdi. | Uyarı |
| HTTP Istemci hatası | Kaynak cihaz geçersiz bir istek başlattı. | Uyarı |
| Geçersiz IP adresi | Sistem, bir kaynak cihaz ile IP adresi arasında geçersiz bir adres olan trafik algıladı. Bu, yanlış yapılandırma veya geçersiz trafik oluşturma girişimi gösterebilir. | İkincil |
| Master-Slave kimlik doğrulama hatası | Bir DNP3 kaynak aygıtı (ana) ve hedef cihaz (outstation) arasındaki kimlik doğrulama işlemi başarısız oldu. | İkincil |
| MMS hizmeti Isteği başarısız oldu | Sunucu bir hata kodu döndürdü. Bu, bir sunucu hatasını veya bir istemci tarafından geçersiz bir istek olduğunu gösterir. | Ana |
| Algılayıcı arabiriminde hiçbir trafik algılanmadı | Bir algılayıcı ağ arabirimindeki ağ trafiğini algılayıcıyı durdurdu. | Kritik |
| OPC UA sunucusu, kullanıcının dikkatini gerektiren bir olay oluşturdu | OPC UA sunucusu istemciye bir olay bildirimi gönderdi. Bu tür bir olay için Kullanıcı ilgilenilmesi gerekir | Ana |
| OPC UA hizmeti Isteği başarısız oldu | Sunucu bir hata kodu döndürdü. Bu, bir sunucu hatasını veya bir istemci tarafından geçersiz bir istek olduğunu gösterir. | Ana |
| Outstation yeniden başlatıldı | Kaynak cihazda soğuk yeniden başlatma algılandı. Bu, cihazın fiziksel olarak kapatıldığı ve tekrar tekrar etkinleştirildiği anlamına gelir. | Uyarı |
| Outstation yeniden başlatmaları sık | Kaynak cihazda aşırı sayıda soğuk yeniden başlatma algılandı. Bu, cihazın fiziksel olarak kapatıldığı ve çok fazla sayıda kez tekrar etkinleştirildiği anlamına gelir. | İkincil |
| Outstation 'nin yapılandırması değişti | Kaynak cihazda bir yapılandırma değişikliği algılandı. | Ana |
| Outstation 'ın bozuk yapılandırması algılandı | Bu DNP3 kaynak aygıtı (outstation) bozuk bir yapılandırma bildirdi. | Ana |
| Profinet DCP komutu başarısız oldu | Sunucu bir hata kodu döndürdü. Bu, bir sunucu hatasını veya bir istemci tarafından geçersiz bir istek olduğunu gösterir. | Ana |
| Profinet cihaz fabrikası sıfırlama | Bir kaynak cihaz, bir fabrika sıfırlaması komutunu bir Profinet hedef cihazına gönderdi. Reset komutu, Profinet cihaz yapılandırmasını temizler ve işlemini durduruyor. | Uyarı |
| RPC Işlemi başarısız oldu | Sunucu bir hata kodu döndürdü. Bu, bir sunucu hatasını veya bir istemci tarafından geçersiz bir istek olduğunu gösterir. | Ana |
| Örneklenmiş değerler Ileti veri kümesi yapılandırması değiştirildi | Kaynak cihazda bir ileti (protokol KIMLIĞI ile tanımlanır) veri kümesi değiştirildi. Bu, cihazın bu ileti için farklı bir veri kümesi rapormayacağı anlamına gelir. | Uyarı |
| Bağımlı cihaz kurtarılamaz hatası | Kaynak cihazda kurtarılamaz bir durum hatası algılandı. Bu tür bir hata genellikle bir donanım hatası olduğunu veya belirli bir komutu gerçekleştirme başarısızlığını gösterir. | Ana |
| Outstation 'da donanım sorunları şüphesi | Kaynak cihazda kurtarılamaz bir durum hatası algılandı. Bu tür bir hata genellikle bir donanım hatası olduğunu veya belirli bir komutu gerçekleştirme başarısızlığını gösterir. | Ana |
| Yanıt vermeyen MODBUS cihazının şüphesi | Kaynak cihaz kendisine gönderilen bir komuta yanıt vermedi. Komut gönderildiğinde bağlantısı kesilmiş olabilir. | İkincil |
| Algılayıcı arabiriminde algılanan trafik | Bir algılayıcı ağ arabirimindeki ağ trafiğini algılamaya devam ettirildi. | Uyarı |

## <a name="next-steps"></a>Sonraki adımlar

[Uyarı olaylarını yönetebilirsiniz](how-to-manage-the-alert-event.md).
[Uyarı bilgilerini iletmeyi](how-to-forward-alert-information-to-partners.md)öğrenin.
