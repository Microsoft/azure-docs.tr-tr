---
title: IoT API’leri için Defender ile çalışma
description: Sensörler ve yönetim konsolları tarafından bulunan verilere erişmek ve bu verilerle ilgili eylemler gerçekleştirmek için bir dış REST API kullanın.
ms.date: 12/14/2020
ms.topic: reference
ms.openlocfilehash: 0e3659d8d5e6829651012dae02ca74c5ecacaf0c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829971"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>IoT algılayıcısı ve Yönetim Konsolu API 'Leri için Defender

Sensörler ve yönetim konsolları tarafından bulunan verilere erişmek ve bu verilerle ilgili eylemler gerçekleştirmek için bir dış REST API kullanın.

Bağlantılar SSL üzerinden güvenli hale getirilir.

## <a name="getting-started"></a>Kullanmaya başlama

Genel olarak, IoT algılayıcısı veya şirket içi yönetim konsolu için Azure Defender 'da bir dış API kullanırken, bir erişim belirteci oluşturmanız gerekir. Algılayıcı ve şirket içi yönetim konsolunda kullandığınız kimlik doğrulama API 'Leri için belirteçler gerekli değildir.

Belirteç oluşturmak için:

1. **Sistem ayarları** penceresinde, **erişim belirteçleri**' ni seçin.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Erişim belirteçleri düğmesini vurgulayan sistem ayarları pencerelerinin ekran görüntüsü.":::

2. **Yeni belirteç oluştur**' u seçin.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Yeni bir belirteç oluşturmak için düğmeyi seçin.":::

3. Yeni belirtecin amacını açıklama ve **İleri ' yi** seçin.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Yeni bir belirteç oluşturun ve bununla ilişkili tümleştirmenin adını girin.":::

4. Erişim belirteci görünür. Yeniden görüntülenmeyeceği için onu kopyalayın.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Tümleştirme için erişim belirtecinizi kopyalayın.":::

5. **Son**'u seçin. Oluşturduğunuz belirteçler **erişim belirteçleri** iletişim kutusunda görünür.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Doldurulmuş belirteçlerle cihaz belirteçleri iletişim kutusunun ekran görüntüsü":::

   **Kullanılan** bu belirtece sahip bir dış çağrının son kez alındığını gösterir.

   Bu belirteç için **kullanılan** alanda **N/A** görüntüleniyorsa, algılayıcı ve bağlı sunucu arasındaki bağlantı çalışmaz.

6. İsteğinize **Yetkilendirme** BAŞLıKLı bir http üst bilgisi ekleyin ve değerini oluşturduğunuz belirtece ayarlayın.

## <a name="sensor-api-specifications"></a>Algılayıcı API 'SI belirtimleri

Bu bölümde aşağıdaki algılayıcı API 'Leri açıklanmaktadır:

- [Cihaz bilgilerini alma-/api/v1/Devices](#retrieve-device-information---apiv1devices)

- [Cihaz bağlantı bilgilerini alma-/api/v1/Devices/Connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [Cven 'ler-/api/v1/Devices/cven hakkında bilgi alın](#retrieve-information-on-cves---apiv1devicescves)

- [Uyarı bilgilerini al-/api/v1/Alerts](#retrieve-alert-information---apiv1alerts)

- [Zaman çizelgesi olaylarını alma-/api/v1/Events](#retrieve-timeline-events---apiv1events)

- [Güvenlik açığı bilgilerini alma-/api/v1/Reports/vulnerabilities/Devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Güvenlik açıklarını alma-/api/v1/Reports/vulnerabilities/Security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [İşlemsel güvenlik açıklarını alma-/api/v1/Reports/vulnerabilities/Operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Kullanıcı kimlik bilgilerini doğrulama-/api/External/Authentication/Validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Parolayı Değiştir-/External/Authentication/set_password](#change-password---externalauthenticationset_password)

- [Sistem Yöneticisi tarafından Kullanıcı parolası güncelleştirmesi-/External/Authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>Cihaz bilgilerini alma-/api/v1/Devices

Bir bir Defender for IoT algılayıcısı algıladığı tüm cihazların bir listesini istemek için bu API 'yi kullanın.

#### <a name="method"></a>Yöntem

**Al**

, IoT algılayıcısı için Defender tarafından algılanan tüm cihazların bir listesini ister.

#### <a name="query-parameters"></a>Sorgu parametreleri

- **yetkilendirildi**: yalnızca yetkili ve yetkisiz cihazları filtrelemek için.

  **Örnekler**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

Cihazları temsil eden JSON nesneleri dizisi.

#### <a name="device-fields"></a>Cihaz alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **id** | Sayısal | No | - |
| **IpAdresleri** | JSON dizisi | Yes | IP adresleri (Internet adresleri veya ikili NIC 'ler içeren bir cihaz olması durumunda birden fazla adres olabilir) |
| **name** | Dize | No | - |
| **türüyle** | Dize | No | Bilinmiyor, mühendislik Istasyonu, PLC, HMı, Historia, etki alanı denetleyicisi, DB sunucusu, kablosuz erişim noktası, yönlendirici, anahtar, sunucu, Iş Istasyonu, IP kamera, yazıcı, güvenlik duvarı, Terminal İstasyonu, VPN Gateway, Internet veya çok noktaya yayın ve yayın |
| **macAddresses** | JSON dizisi | Yes | MAC adresleri (çift NIC içeren bir cihaz olması durumunda birden fazla adres olabilir) |
| **operatingSystem** | Dize | Yes | - |
| **engineeringStation** | Boole | No | True veya false |
| **Tarayıcınız** | Boole | No | True veya false |
| **yetkisi** | Boole | No | True veya false |
| **satıcınıza** | Dize | Yes | - |
| **ekledikten** | JSON dizisi | Yes | Protokol nesnesi |
| **yazılımında** | JSON dizisi | Yes | Bellenim nesnesi |

#### <a name="protocol-fields"></a>Protokol alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **Ad** | Dize | No |  |
| **Adresler** | JSON dizisi | Yes | Ana veya sayısal değerler |

#### <a name="firmware-fields"></a>Bellenim alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **ardışık** | Dize | No | Yok veya gerçek değer |
| **modelinizi** | Dize | No | Yok veya gerçek değer |
| **firmwareVersion** | Çift | No | Yok veya gerçek değer |
| **additionalData** | Dize | No | Yok veya gerçek değer |
| **moduleAddress** | Dize | No | Yok veya gerçek değer |
| **rafı** | Dize | No | Yok veya gerçek değer |
| **kumar** | Dize | No | Yok veya gerçek değer |
| **adrestir** | Dize | No | Yok veya gerçek değer |

#### <a name="response-example"></a>Yanıt örneği

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/Devices | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https: <span> //127 <span> . 0.0.1/API/v1/Devices? yetkilendirilmiş = true |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Cihaz bağlantı bilgilerini alma-/api/v1/Devices/Connections

Cihaz başına tüm bağlantıların bir listesini istemek için bu API 'YI kullanın.

#### <a name="method"></a>Yöntem

**Al**

#### <a name="query-parameters"></a>Sorgu parametreleri

Sorgu parametrelerini ayarlamazsanız, tüm cihaz bağlantıları döndürülür.

**Örnek**:

`/api/v1/devices/connections`

- **DeviceID**: bağlantılarını görüntülemek için belirli BIR cihaz kimliğine göre filtreleyin.

  **Örnek**:

  `/api/v1/devices/<deviceId>/connections`

- **Lastactiveınminutes**: daha sonra, bağlantıların etkin olduğu dakika olarak geriye doğru zaman dilimi.

  **Örnek**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **Discoveredbefore**: yalnızca belirli bir saatten önce algılanan bağlantıları filtreleyin (MILISANIYE olarak UTC).

  **Örnek**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **Bulveredavfter**: yalnızca belirli bir süreden (MILISANIYE olarak UTC) sonra algılanan bağlantıları filtreleyin.

  **Örnek**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

Cihaz bağlantılarını temsil eden JSON nesneleri dizisi.

#### <a name="fields"></a>Alanlar

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **Firstdeviceıd** | Sayısal | No | - |
| **Seconddeviceıd** | Sayısal | No | - |
| **Lastgörüldü** | Sayısal | No | Dönem (UTC) |
| **tespit** | Sayısal | No | Dönem (UTC) |
| **adet** | Sayı dizisi | No | - |
| **ekledikten** | JSON dizisi | No | Protokol alanı |

#### <a name="protocol-field"></a>Protokol alanı

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **name** | Dize | No | - |
| **komut** | Dize dizisi | No | - |

#### <a name="response-example"></a>Yanıt örneği

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

#### <a name="curl-command"></a>Curl komutu

> [!div class="mx-tdBreakAll"]
> | Tür | API'ler | Örnek |
> |--|--|--|
> | GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/Devices/Connections | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/Devices/Connections |
> | GET | kıvrımlı-k-H "Authorization: <AUTH_TOKEN>" ' https://<IP_ADDRESS>/api/v1/Devices/ <deviceId> /Connections? Lastactiveınminutes =&discoveredBefore =&Bulveredafter = ' | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/api/v1/Devices/2/Connections? Lastactiveınminutes = 20&discoveredBefore = 1594550986000&Bulveredadfter = 1594550986000 ' |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>Cven 'ler-/api/v1/Devices/cven hakkında bilgi alın

Ağdaki cihazlarda bulunan tüm bilinen yolların bir listesini istemek için bu API 'yi kullanın.

#### <a name="method"></a>Yöntem

**Al**

#### <a name="query-parameters"></a>Sorgu parametreleri

Bu API, varsayılan olarak, her bir IP adresi için en fazla 100 üst puanlı olan tüm cihaz IP 'Lerinin listesini sağlar.

**Örnek**:

`/api/v1/devices/cves`

- **DeviceID**: belirli bir cihaz için en fazla 100 üst puanlanmış con 'a kadar, belirli BIR cihaz IP adresine göre filtreleyin.

  **Örnek**:

  `/api/v1/devices/<ipAddress>/cves`

- **top**: her BIR cihaz IP adresi için kaç tane en üst puanlanmış cdirme alınır.

  **Örnek**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

IP adreslerinde tanımlanan Cvileri temsil eden JSON nesneleri dizisi.

#### <a name="fields"></a>Alanlar

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **Cveıd** | Dize | No | - |
| **Belirlenemiyor** | Dize | No | IP Adresi |
| **ınızı** | Dize | No | 0,0-10,0 |
| **Saldırıda Kvector** | Dize | No | Ağ, bitişik ağ, yerel veya fiziksel |
| **açıklaması** | Dize | No | - |

#### <a name="response-example"></a>Yanıt örneği

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/Devices/CFER | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/Devices/cves |
| GET | kıvrımlı-k-H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/Devices/ <deviceIpAddress> /CFER? top = | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/Devices/10.10.10.15/cves? top = 50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>Uyarı bilgilerini al-/api/v1/Alerts

Bu API 'yi, IoT algılayıcısı için Defender 'ın algıladığı tüm uyarıların bir listesini istemek üzere kullanın.

#### <a name="method"></a>Yöntem

**Al**

#### <a name="query-parameters"></a>Sorgu parametreleri

- **durum**: yalnızca işlenmiş veya işlenmemiş uyarıları filtrelemek için.

  **Örnek**:

  `/api/v1/alerts?state=handled`

- **FromTime**: belirli bir zamandan oluşturulan uyarıları filtrelemek için (MILISANIYE olarak UTC).

  **Örnek**:

  `/api/v1/alerts?fromTime=<epoch>`

- **ToTime**: yalnızca belirli bir zamandan önce oluşturulan uyarıları filtrelemek için (MILISANIYE olarak UTC).

  **Örnek**:

  `/api/v1/alerts?toTime=<epoch>`

- uyarıları belirli bir türe göre filtrelemek için **yazın**. Filtrelenecek mevcut türler: beklenmeyen yeni aygıtlar, bağlantısız bağlantılar.

  **Örnek**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

Uyarıları temsil eden JSON nesneleri dizisi.

#### <a name="alert-fields"></a>Uyarı alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **ID** | Sayısal | No | - |
| **ışınızda** | Sayısal | No | Dönem (UTC) |
| **başlık** | Dize | No | - |
| **İleti** | Dize | No | - |
| **inin** | Dize | No | Uyarı, Ikincil, büyük veya kritik |
| **altyapısına** | Dize | No | Protokol Ihlali, Ilke Ihlali, kötü amaçlı yazılım, anomali veya operasyonel |
| **sourceDevice** | Sayısal | Yes | Cihaz Kimliği |
| **Hedef cihaz** | Sayısal | Yes | Cihaz Kimliği |
| **sourceDeviceAddress** | Sayısal | Yes | ıP, MAC |
| **destinationDeviceAddress** | Sayısal | Yes | ıP, MAC |
| **Düzeltme adımları** | Dize | Yes | Uyarı bölümünde açıklanan düzeltme adımları |
| **AdditionalInformation** | Ek bilgi nesnesi | Yes | - |

Aşağıdaki bilgiler için/api/v2/gereklidir:

- sourceDeviceAddress 
- destinationDeviceAddress
- Düzeltme adımları

#### <a name="additional-information-fields"></a>Ek bilgi alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **açıklaması** | Dize | No | - |
| **bilgi** | JSON dizisi | Hayır | Dize |

#### <a name="response-example"></a>Yanıt örneği

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

#### <a name="curl-command"></a>Curl komutu

> [!div class="mx-tdBreakAll"]
> | Tür | API'ler | Örnek |
> |--|--|--|
> | GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" ' https://<IP_ADDRESS>/api/v1/Alerts? State =&fromTime =&toTime =&Type = ' | kıvrımlı-k-H "yetkilendirmesi: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/api/v1/Alerts? State = işlenmemiş&fromTime = 1594550986000&toTime = 1594550986001&Type = disconnections ' |

### <a name="retrieve-timeline-events---apiv1events"></a>Zaman çizelgesi olaylarını alma-/api/v1/Events

Olay zaman çizelgesine bildirilen olayların bir listesini istemek için bu API 'yi kullanın.

#### <a name="method"></a>Yöntem

**Al**

#### <a name="query-parameters"></a>Sorgu parametreleri

- **Minutesmeframe**: daha sonra olayların bildirildiği, dakikaya göre geriye doğru zaman dilimi.

  **Örnek**:

  `/api/v1/events?minutesTimeFrame=20`

- olay listesini belirli bir türe göre filtrelemek için **şunu yazın**:.

  **Örnekler**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

Uyarıları temsil eden JSON nesneleri dizisi.

#### <a name="event-fields"></a>Olay alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|--|
| **ilişkin** | Sayısal | No | Dönem (UTC) |
| **başlık** | Dize | No | - |
| **inin** | Dize | No | BILGI, BILDIRIM veya uyarı |
| **inde** | Dize | Yes | Olay el ile oluşturulduysa, bu alan olayı oluşturan kullanıcı adını içerir |
| **içerik** | Dize | No | - |

#### <a name="response-example"></a>Yanıt örneği

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" ' https://<IP_ADDRESS>/api/v1/Events? Minutesmeframe =&Type = ' | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/api/v1/Events? Minutesmeframe = 20&Type = DEVICE_CONNECTION_CREATED ' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Güvenlik açığı bilgilerini alma-/api/v1/Reports/vulnerabilities/Devices

Her cihaz için güvenlik açığı değerlendirme sonuçları istemek üzere bu API 'yi kullanın.

#### <a name="method"></a>Yöntem

**Al**

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

Değerlendirilen cihazları temsil eden JSON nesneleri dizisi.

Cihaz nesnesi şunları içerir:

- Genel veriler

- Değerlendirme puanı

- Güvenlik Açıkları

#### <a name="device-fields"></a>Cihaz alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **name** | Dize | No | - |
| **IpAdresleri** | JSON dizisi | No | - |
| **securityScore** | Sayısal | No | - |
| **satıcınıza** | Dize | Yes |  |
| **firmwareVersion** | Dize | Yes | - |
| **modelinizi** | Dize | Yes | - |
| **ıbılessaccesspoint** | Boole | No | True veya false |
| **operatingSystem** | İşletim sistemi nesnesi | Yes | - |
| **'teki** | Güvenlik açıkları nesnesi | Yes | - |

#### <a name="operating-system-fields"></a>İşletim sistemi alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **Ad** | Dize | Yes | - |
| **Tür** | Dize | Yes | - |
| **Sürüm** | Dize | Yes | - |
| **latestVersion** | Dize | Yes | - |

#### <a name="vulnerabilities-fields"></a>Güvenlik açıkları alanları
 
| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **Antivirüsler** | JSON dizisi | Yes | Virüsten koruma adları |
| **Plaintextparolalar** | JSON dizisi | Yes | Parola nesneleri |
| **remoteAccess** | JSON dizisi | Yes | Uzaktan erişim nesneleri |
| **isBackupServer** | Boole | No | True veya false |
| **Openedport 'lar** | JSON dizisi | Yes | Açık bağlantı noktası nesneleri |
| **isEngineeringStation** | Boole | No | True veya false |
| **isKnownScanner** | Boole | No | True veya false |
| **cdirme** | JSON dizisi | Yes | CVE nesneleri |
| **Isyetkilendirilmemiş** | Boole | No | True veya false |
| **Malwareındicationsalgılanan** | Boole | No | True veya false |
| **Dalgalı bir uthenticAtion** | JSON dizisi | Yes | Zayıf kimlik doğrulaması kullanan uygulamalar algılandı |

#### <a name="password-fields"></a>Parola alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **parola** | Dize | No | - |
| **Protocol** | Dize | No | - |
| **gücüyle** | Dize | No | Çok zayıf, zayıf, orta veya güçlü |

#### <a name="remote-access-fields"></a>Uzaktan erişim alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **bağ** | Sayısal | No | - |
| **Aktarım** | Dize | No | TCP veya UDP |
| **istemcilerinin** | Dize | No | IP Adresi |
| **clientSoftware** | Dize | No | SSH, VNC, uzak masaüstü veya takım Görüntüleyicisi |

#### <a name="open-port-fields"></a>Bağlantı noktası alanlarını aç

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **bağ** | Sayısal | No | - |
| **Aktarım** | Dize | No | TCP veya UDP |
| **Protocol** | Dize | Yes | - |
| **isConflictingWithFirewall** | Boole | No | True veya false |

#### <a name="cve-fields"></a>CVE alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **ID** | Dize | No | - |
| **ınızı** | Sayısal | No | Çift |
| **açıklaması** | Dize | No | - |

#### <a name="response-example"></a>Yanıt örneği

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/Reports/vulnerabilities/Devices | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/Reports/vulnerabilities/Devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Güvenlik açıklarını alma-/api/v1/Reports/vulnerabilities/Security

Genel bir güvenlik açığı değerlendirmesinin sonuçlarını istemek için bu API 'YI kullanın. Bu değerlendirme, sisteminizin güvenlik düzeyi hakkında öngörüler sağlar.

Bu değerlendirme, belirli bir cihaz değerlendirmesi üzerine değil, genel ağ ve sistem bilgilerini temel alır.

#### <a name="method"></a>Yöntem

**Al**

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

Değerlendirilen sonuçları temsil eden JSON nesnesi. Her anahtar null olabilir. Aksi halde, null olamayan anahtarlar içeren bir JSON nesnesi içerir.

### <a name="result-fields"></a>Sonuç alanları

**Anahtarlar**

**unauthorizedDevices**

| Alan adı | Tür | Değer listesi |
| ---------- | ---- | -------------- |
| **adrestir** | Dize | IP Adresi |
| **name** | Dize | - |
| **firstDetectionTime** | Sayısal | Dönem (UTC) |
| Lastgörüldü | Sayısal | Dönem (UTC) |

**illegalTrafficByFirewallRules**

| Alan adı | Tür | Değer listesi |
| ---------- | ---- | -------------- |
| **Server** | Dize | IP Adresi |
| **istemcilerinin** | Dize | IP Adresi |
| **bağ** | Sayısal | - |
| **Aktarım** | Dize | TCP, UDP veya ıCMP |

**weakFirewallRules**

| Alan adı | Tür | Değer listesi |
| ---------- | ---- | -------------- |
| **sources** | JSON Kaynak dizisi. Her kaynak dört biçimde olabilir. | "Any", "IP adresi (ana bilgisayar)", "IP-IP (Aralık)", "IP adresi, alt ağ maskesi (ağ)" |
| **yeri** | Hedeflerin JSON dizisi. Her hedef dört biçimde olabilir. | "Any", "IP adresi (ana bilgisayar)", "IP-IP (Aralık)", "IP adresi, alt ağ maskesi (ağ)" |
| **adet** | Üç biçimdeki bağlantı noktalarının JSON dizisi | "Any", "Port (protokol, algılanan)", "bağlantı noktasından bağlantı noktası (algılanan protokol)" |

**Erişim noktaları**

| Alan adı | Tür | Değer listesi |
| ---------- | ---- | -------------- |
| **macAddress** | Dize | MAC adresi |
| **satıcınıza** | Dize | Satıcı adı |
| **Belirlenemiyor** | Dize | IP adresi veya yok |
| **name** | Dize | Cihaz adı veya yok |
| **SA** | Dize | Hayır, şüpheli veya Evet |

**Connectionsbetweenalt ağları**

| Alan adı | Tür | Değer listesi |
| ---------- | ---- | -------------- |
| **Server** | Dize | IP Adresi |
| **istemcilerinin** | Dize | IP Adresi |

**industrialMalwareIndicators**

| Alan adı | Tür | Değer listesi |
| ---------- | ---- | -------------- |
| **detectionTime** | Sayısal | Dönem (UTC) |
| **alertMessage** | Dize | - |
| **açıklaması** | Dize | - |
| **cihazlarınız** | JSON dizisi | Cihaz adları | 

**ınternetconnections**

| Alan adı | Tür | Değer listesi |
| ---------- | ---- | -------------- |
| **internalAddress** | Dize | IP Adresi |
| **yetkisi** | Boole | Evet veya Hayır | 
| **Externalaadresler** | JSON dizisi | IP Adresi |

#### <a name="response-example"></a>Yanıt örneği

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/Reports/vulnerabilities/Security | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/Reports/vulnerabilities/Security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>İşlemsel güvenlik açıklarını alma-/api/v1/Reports/vulnerabilities/Operational

Genel bir güvenlik açığı değerlendirmesinin sonuçlarını istemek için bu API 'YI kullanın. Bu değerlendirme ağınızın işletimsel durumuna ilişkin öngörüler sağlar. Bu, belirli bir cihaz değerlendirmesi üzerine değil, genel ağ ve sistem bilgilerini temel alır.

#### <a name="method"></a>Yöntem

**Al**

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

Değerlendirilen sonuçları temsil eden JSON nesnesi. Her anahtar, bir JSON sonuçları dizisi içerir.

#### <a name="result-fields"></a>Sonuç alanları

**Anahtarlar**

**backupServer**

| Alan adı | Tür | Değer listesi |
|--|--|--|
| **kaynaktaki** | Dize | IP Adresi |
| **hedefine** | Dize | IP Adresi |
| **bağ** | Sayısal | - |
| **Aktarım** | Dize | TCP veya UDP |
| **Backupmaximalınterval** | Dize | - |
| **lastSeenBackup** | Sayısal | Dönem (UTC) |

**ipNetworks**

| Alan adı | Tür | Değer listesi |
|--|--|--|
| **adresler** | Sayısal | - |
| **network** | Dize | IP Adresi |
| **maske** | Dize | Alt ağ maskesi |

**Protocolsorunlar**

| Alan adı | Tür | Değer listesi |
|--|--|--|
| **Protocol** | Dize | - |
| **gideren** | JSON dizisi | IP adresleri |
| **daki** | Dize | - |
| **reportTime** | Sayısal | Dönem (UTC) |

**protocolDataVolumes**

| Alan adı | Tür | Değer listesi |
|--|--|--|
| protokol | Dize | - |
| Ses düzeyi | Dize | "birim numarası MB" |

**bağlantı kesilmesi**

| Alan adı | Tür | Değer listesi |
|--|--|--|
| **assetAddress** | Dize | IP Adresi |
| **assetName** | Dize | - |
| **lastDetectionTime** | Sayısal | Dönem (UTC) |
| **backToNormalTime** | Sayısal | Dönem (UTC) |     

#### <a name="response-example"></a>Yanıt örneği

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/Reports/vulnerabilities/Operational | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/Reports/vulnerabilities/Operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Kullanıcı kimlik bilgilerini doğrulama-/api/External/Authentication/Validation

Bir Defender 'ı IoT Kullanıcı adı ve parolası için doğrulamak üzere bu API 'yi kullanın. IoT Kullanıcı rolleri için tüm Defender, API ile çalışabilir.

Bu API 'yi kullanmak için IoT erişim belirtecinin bir Defender 'a ihtiyacınız yoktur.

#### <a name="method"></a>Yöntem

**POST**

#### <a name="request-type"></a>İstek türü

**JSON**

#### <a name="query-parameters"></a>Sorgu parametreleri

| **Ad** | **Tür** | **Yapılamaz** |
|--|--|--|
| **nitelen** | Dize | No |
| **parola** | Dize | No |

#### <a name="request-example"></a>İstek örneği

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

İşlem durumu ayrıntıları içeren ileti dizesi:

- **Başarılı-msg**: kimlik doğrulaması başarılı

- **Hata-hata**: kimlik bilgileri doğrulaması başarısız oldu

#### <a name="response-example"></a>Yanıt örneği

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/External/Authentication/Validation | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/External/Authentication/Validation |

### <a name="change-password---externalauthenticationset_password"></a>Parolayı Değiştir-/External/Authentication/set_password

Kullanıcıların kendi parolalarını değiştirmesine izin vermek için bu API 'yi kullanın. IoT Kullanıcı rolleri için tüm Defender, API ile çalışabilir. Bu API 'yi kullanmak için IoT erişim belirtecinin bir Defender 'a ihtiyacınız yoktur.

#### <a name="method"></a>Yöntem

**POST**

#### <a name="request-type"></a>İstek türü

**JSON**

#### <a name="request-example"></a>İstek örneği

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

İşlem durumu ayrıntıları içeren ileti dizesi:

- **Başarılı – msg**: Parola değiştirildi

- **Hata – hata**: Kullanıcı kimlik doğrulama hatası

- **Hata – hata**: parola güvenlik ilkesiyle eşleşmiyor

#### <a name="response-example"></a>Yanıt örneği

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Cihaz alanları

| **Ad** | **Tür** | **Yapılamaz** |
|--|--|--|
| **nitelen** | Dize | No |
| **parola** | Dize | No |
| **new_password** | Dize | No |

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| POST | kıvrımlı-k-d ' {"kullanıcıadı": "<USER_NAME>", "parola": "<CURRENT_PASSWORD>", "new_password": "<NEW_PASSWORD>"} '-H ' Content-Type: Application/JSON ' https://<IP_address>/api/External/Authentication/set_password | kıvrımlı-k-d ' {"kullanıcıadı": "myUser", "Password": " 1234@abcd ", "new_password": " abcd@1234 "} '-H ' Content-Type: Application/JSON ' https:/ <span> /127.0.0.1/api/External/Authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Sistem Yöneticisi tarafından Kullanıcı parolası güncelleştirmesi-/External/Authentication/set_password_by_admin

Sistem yöneticilerinin belirtilen kullanıcıların parolalarını değiştirmesine izin vermek için bu API 'yi kullanın. IoT Yönetici Kullanıcı rolleri için Defender, API ile çalışabilir. Bu API 'yi kullanmak için IoT erişim belirtecinin bir Defender 'a ihtiyacınız yoktur.

#### <a name="method"></a>Yöntem

**POST**

#### <a name="request-type"></a>İstek türü

**JSON**

#### <a name="request-example"></a>İstek örneği

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

İşlem durumu ayrıntıları içeren ileti dizesi:

- **Başarılı – msg**: Parola değiştirildi

- **Hata – hata**: Kullanıcı kimlik doğrulama hatası

- **Hata – hata**: Kullanıcı yok

- **Hata – hata**: parola güvenlik ilkesiyle eşleşmiyor

- **Hata – hata**: kullanıcının parolayı değiştirme izinleri yok

#### <a name="response-example"></a>Yanıt örneği

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Cihaz alanları

| **Ad** | **Tür** | **Yapılamaz** |
|--|--|--|
| **admin_username** | Dize | No |
| **admin_password** | Dize | No |
| **nitelen** | Dize | No |
| **new_password** | Dize | No |

#### <a name="curl-command"></a>Curl komutu

> [!div class="mx-tdBreakAll"]
> | Tür | API'ler | Örnek |
> |--|--|--|
> | POST | kıvrımlı-k-d ' {"admin_username": "<ADMIN_USERNAME>", "admin_password": "<ADMIN_PASSWORD>", "kullanıcıadı": "<user_name>", "new_password": "<NEW_PASSWORD>"} '-H ' Content-Type: Application/JSON ' https://<IP_address>/api/External/Authentication/set_password_by_admin | kıvrımlı-k-d ' {"admin_user": "adminUser", "admin_password": " 1234@abcd ", "kullanıcıadı": "myUser", "new_password": " abcd@1234 "} '-H ' Content-Type: Application/JSON ' https:/ <span> /127.0.0.1/api/External/Authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>Şirket içi yönetim konsolu API 'SI belirtimleri ##

Bu bölümde, için şirket içi yönetim konsolu API 'Leri açıklanmaktadır:
- Uyarı dışlamaları
- Cihaz bilgileri
- Uyarı bilgileri

### <a name="alert-exclusions"></a>Uyarı dışlamaları ###

Uyarıların gönderileceği koşulları tanımlayın. Örneğin, durdurma ve başlangıç zamanlarını, uyarıları tetiklerken dışlanmayacak cihazları veya alt ağları ve dışlanmalıdır IoT motorları için Defender 'ı tanımlayın ve güncelleştirin. Örneğin, bakım penceresi sırasında, kritik cihazlarda kötü amaçlı yazılım uyarıları hariç olmak üzere tüm uyarıların teslimini durdurmak isteyebilirsiniz. Burada tanımladığınız öğeler, şirket içi yönetim konsolunun **Uyarı dışlamaları** penceresinde salt okuma dışlama kuralları olarak görüntülenir.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/External/Authentication/Validation**

- **Yanıt örneği**

- **yanıtıyla**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>Parolayı Değiştir-/External/Authentication/set_password 

Kullanıcıların kendi parolalarını değiştirmesine izin vermek için bu API 'yi kullanın. IoT Kullanıcı rolleri için tüm Defender, API ile çalışabilir. Bu API 'yi kullanmak için IoT erişim belirtecinin bir Defender 'a ihtiyacınız yoktur.

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Sistem Yöneticisi tarafından Kullanıcı parolası güncelleştirmesi-/External/Authentication/set_password_by_admin 

Sistem yöneticilerinin belirli kullanıcılar için parolaları değiştirmesine izin vermek için bu API 'yi kullanın. IoT Yönetici Kullanıcı rolleri için Defender, API ile çalışabilir. Bu API 'yi kullanmak için IoT erişim belirtecinin bir Defender 'a ihtiyacınız yoktur.

### <a name="retrieve-device-information---externalv1devices"></a>Cihaz bilgilerini alma-/External/v1/Devices ###

Bu API, bir şirket içi yönetim konsoluna bağlı olan IoT algılayıcılarının Defender tarafından algılanan tüm cihazların bir listesini ister.

#### <a name="method"></a>Yöntem

**Al**

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

Cihazları temsil eden JSON nesneleri dizisi.

#### <a name="query-parameters"></a>Sorgu parametreleri

- **yetkilendirildi**: yalnızca yetkili ve yetkisiz cihazları filtrelemek için.

- **siteID**: yalnızca belirli sitelerle ilişkili cihazları filtrelemek için.

- **bölge kimliği**: yalnızca belirli bölgelerle ilişkili cihazları filtrelemek için. [1](#1)

- **Sensorıd**: yalnızca belirli algılayıcılar tarafından algılanan cihazları filtrelemek için. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *SITE ve bölge kimliğiniz olmayabilir. Bu durumda, site ve bölge KIMLIĞINI almak için tüm cihazları sorgulayın.*

#### <a name="query-parameters-example"></a>Sorgu parametreleri örneği

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Cihaz alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **Sensorıd** | Sayısal | No | - |
| **Bölge kimliği** | Sayısal | Yes | - |
| **SiteId** | Sayısal | Yes | - |
| **IpAdresleri** | JSON dizisi | Yes | IP adresleri (Internet adresleri veya ikili NIC 'ler içeren bir cihaz olması durumunda birden fazla adres olabilir) |
| **name** | Dize | No | - |
| **türüyle** | Dize | No | Bilinmiyor, mühendislik Istasyonu, PLC, HMı, Historia, etki alanı denetleyicisi, DB sunucusu, kablosuz erişim noktası, yönlendirici, anahtar, sunucu, Iş Istasyonu, IP kamera, yazıcı, güvenlik duvarı, Terminal İstasyonu, VPN Gateway, Internet veya çok noktaya yayın ve yayın |
| **macAddresses** | JSON dizisi | Yes | MAC adresleri (çift NIC içeren bir cihaz olması durumunda birden fazla adres olabilir) |
| **operatingSystem** | Dize | Yes | - |
| **engineeringStation** | Boole | No | True veya false |
| **Tarayıcınız** | Boole | No | True veya false |
| **yetkisi** | Boole | No | True veya false |
| **satıcınıza** | Dize | Yes | - |
| **Protokoller** | JSON dizisi | Yes | Protokol nesnesi |
| **yazılımında** | JSON dizisi | Yes | Bellenim nesnesi |

#### <a name="protocol-fields"></a>Protokol alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| Name | Dize | No | - |
| Adresler | JSON dizisi | Yes | Ana veya sayısal değerler |

#### <a name="firmware-fields"></a>Bellenim alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **ardışık** | Dize | No | Yok veya gerçek değer |
| **modelinizi** | Dize | No | Yok veya gerçek değer |
| **firmwareVersion** | Çift | No | Yok veya gerçek değer |
| **additionalData** | Dize | No | Yok veya gerçek değer |
| **moduleAddress** | Dize | No | Yok veya gerçek değer |
| **rafı** | Dize | No | Yok veya gerçek değer |
| **kumar** | Dize | No | Yok veya gerçek değer |
| **adrestir** | Dize | No | Yok veya gerçek değer |

#### <a name="response-example"></a>Yanıt örneği

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" ' https://<>IP_ADDRESS>/External/v1/Devices? siteID =&ZoneId =&Sensorıd =&yetkilendirilmiş = ' | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/External/v1/Devices? siteID = 1&bölge kimliği = 2&Sensorıd = 5&yetkilendirilmiş = true ' |

### <a name="retrieve-alert-information---externalv1alerts"></a>Uyarı bilgilerini al-/External/v1/Alerts

Şirket içi yönetim konsolundan tüm veya filtrelenmiş uyarıları almak için bu API 'yi kullanın.

#### <a name="method"></a>Yöntem

**Al**

#### <a name="query-parameters"></a>Sorgu parametreleri

- **durum**: yalnızca işlenmiş ve işlenmemiş uyarıları filtrelemek için.

  **Örnek**:

  `/api/v1/alerts?state=handled`

- **FromTime**: belirli bir zamandan oluşturulan uyarıları filtrelemek için (MILISANIYE olarak UTC).

  **Örnek**:

  `/api/v1/alerts?fromTime=<epoch>`

- **ToTime**: yalnızca belirli bir zamandan önce oluşturulan uyarıları filtrelemek için (MILISANIYE olarak UTC).

  **Örnek**:

  `/api/v1/alerts?toTime=<epoch>`

- **siteID**: uyarının bulunduğu site.
- **bölge kimliği**: uyarının bulunduğu bölge.
- **algılayıcı**: uyarının bulunduğu algılayıcı.

*Site ve bölge KIMLIĞINIZ olmayabilir. Bu durumda, site ve bölge KIMLIĞINI almak için tüm cihazları sorgulayın.*

#### <a name="alert-fields"></a>Uyarı alanları 

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **ID** | Sayısal | No | - |
| **ışınızda** | Sayısal | No | Dönem (UTC) |
| **başlık** | Dize | No | - |
| **İleti** | Dize | No | - |
| **inin** | Dize | No | Uyarı, Ikincil, büyük veya kritik |
| **altyapısına** | Dize | No | Protokol Ihlali, Ilke Ihlali, kötü amaçlı yazılım, anomali veya operasyonel |
| **sourceDevice** | Sayısal | Yes | Cihaz Kimliği |
| **Hedef cihaz** | Sayısal | Yes | Cihaz Kimliği |
| **sourceDeviceAddress** | Sayısal | Yes | ıP, MAC |
| **destinationDeviceAddress** | Sayısal | Yes | ıP, MAC |
| **Düzeltme adımları** | Dize | Yes | Uyarı bölümünde gösterilen düzeltme adımları|
| **sensorName** | Dize | Yes | Kullanıcı tarafından tanımlanan sensör adı |
|**ZoneName & lt** | Dize | Yes | Sensörle ilişkili bölgenin adı|
| **siteName** | Dize | Yes | Sensörle ilişkili sitenin adı |
| **AdditionalInformation** | Ek bilgi nesnesi | Yes | - |

Aşağıdaki bilgiler için/api/v2/gereklidir:

- sourceDeviceAddress 
- destinationDeviceAddress
- Düzeltme adımları
- sensorName
- ZoneName & lt
- siteName

#### <a name="additional-information-fields"></a>Ek bilgi alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **açıklaması** | Dize | No | - |
| **bilgi** | JSON dizisi | Hayır | Dize |

#### <a name="response-example"></a>Yanıt örneği

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

#### <a name="curl-command"></a>Curl komutu

> [!div class="mx-tdBreakAll"]
> | Tür | API'ler | Örnek |
> |--|--|--|
> | GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" ' https://<>IP_ADDRESS>/External/v1/Alerts? State =&ZoneId =&fromTime =&toTime =&siteID =&algılayıcı = ' | kıvrımlı-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/External/v1/Alerts? durum = işlenmemiş&bölge kimliği = 1&fromTime = 0&toTime = 1594551777000&siteID = 1&algılayıcı = 1 ' |

### <a name="qradar-alerts"></a>QRadar uyarıları

IoT için Defender ile QRadar tümleştirmesi, IoT için Defender tarafından oluşturulan uyarıları tanımlamanızı ve bu uyarılarla ilgili eylemler gerçekleştirmenizi sağlar. QRadar, IoT için Defender 'daki verileri alır ve ardından genel API şirket içi yönetim konsolu bileşeniyle iletişim kurar.

Bir IoT için Defender tarafından bulunan verileri QRadar 'e göndermek için, IoT için Defender sisteminde bir iletme kuralı tanımlayın ve **Uzaktan destek uyarı işleme** seçeneğini belirleyin.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="İletme kurallarını gereksinimlerinize uyacak şekilde düzenleyin.":::

İletme kurallarını yapılandırma işlemi sırasında bu seçeneği belirlediğinizde, QRadar ' de aşağıdaki ek alanlar görüntülenir:

- **UUID**: 1-1555245116250 gibi benzersiz uyarı tanımlayıcısı.

- **Site**: uyarının bulunduğu site.

- **Bölge**: uyarının bulunduğu bölge.

QRadar 'e gönderilen yükün örneği:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/External/v1/Alerts/ &lt; UUID&gt;

#### <a name="method"></a>Yöntem

**PUT**

#### <a name="request-type"></a>İstek türü

**JSON**

#### <a name="request-content"></a>İstek içeriği

UUID 'yi içeren uyarı üzerinde gerçekleştirilecek eylemi temsil eden JSON nesnesi.

#### <a name="action-fields"></a>Eylem alanları

| Ad | Tür | Null Atanabilir | Değer listesi |
|--|--|--|--|
| **action** | Dize | No | tanıtıcı veya Handleandöğren |

#### <a name="request-example"></a>İstek örneği

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

Cihazları temsil eden JSON nesneleri dizisi.

#### <a name="response-fields"></a>Yanıt alanları


| Ad | Tür | Null Atanabilir | Description |
|--|--|--|--|
| **içerik/hata** | Dize | No | İstek başarılı olursa içerik özelliği görüntülenir. Aksi halde, Error özelliği görüntülenir. |

#### <a name="possible-content-values"></a>Olası içerik değerleri

| Durum kodu | İçerik değeri | Description |
|--|--|--|
| 200 | Uyarı güncelleştirme isteği başarıyla tamamlandı. | Güncelleştirme isteği başarıyla tamamlandı. Açıklama yok. |
| 200 | Uyarı zaten işlendi (**tanıtıcı**). | Uyarı için bir tanıtıcı isteği alındığında uyarı zaten işlendi.<br />Uyarı **işlenmeye** devam eder. |
| 200 | Uyarı zaten işlendi ve öğrenildi (**Handleandöğren**). | Uyarı zaten işlendi ve bir **Handleandöğren** isteği alındığında öğrenildi.<br />Uyarı, **Handledandöğren** durumunda kalır. |
| 200 | Uyarı zaten işlendi (**işlendi**).<br />Tanıtıcı ve öğrenme (**Handleandöğren**), uyarı üzerinde gerçekleştirildi. | **Handleandöğren** isteği alındığında uyarı zaten işlendi.<br />Uyarı **Handleandöğrenolur**. |
| 200 | Uyarı zaten işlendi ve öğrenildi (**Handleandöğren**). Tanıtıcı isteği yoksayıldı. | Uyarı zaten **Handleandalert** ve uyarının işlenmesi için bir Istek alındığında öğrendi. Uyarı **Handleandöğrenkalır**. |
| 500 | Geçersiz eylem. | Gönderilen eylem, uyarı üzerinde gerçekleştirilecek geçerli bir eylem değil. |
| 500 | Beklenmeyen bir hata oluştu. | Beklenmeyen bir hata oluştu. Sorunu çözmek için teknik desteğe başvurun. |
| 500 | Bu UUID için hiçbir uyarı bulunmadığından istek yürütülemedi. | Belirtilen uyarı UUID 'SI sistemde bulunamadı. |

#### <a name="response-example"></a>Yanıt örneği

**Başarısız**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Dır**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| PUT | kıvrımlı-k-X PUT-d ' {"Action": " <ACTION> "} '-H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/External/v1/Alerts/<UUID> | kıvrık-k-X PUT-d ' {"Action": "tanıtıcı"} '-H "yetkilendirme: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/External/v1/Alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Uyarı dışlamaları (bakım penceresi)-/external/v1/maintenanceWindow

Uyarıların gönderileceği koşulları tanımlayın. Örneğin, durdurma ve başlangıç zamanlarını, uyarıları tetiklerken dışlanmayacak cihazları veya alt ağları ve dışlanmalıdır IoT motorları için Defender 'ı tanımlayın ve güncelleştirin. Örneğin, bakım penceresi sırasında, kritik cihazlarda kötü amaçlı yazılım uyarıları hariç olmak üzere tüm uyarıların uyarı teslimini durdurmak isteyebilirsiniz.

Burada tanımladığınız API 'Ler, şirket içi yönetim konsolunun **Uyarı dışlamaları** penceresinde, salt okuma dışında tutma kuralı olarak görüntülenir.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Tüm dışlama kurallarının bir listesini gösteren uyarı dışlamaları penceresi. ":::

#### <a name="method---post"></a>Yöntem-GÖNDERI

#### <a name="query-parameters"></a>Sorgu parametreleri

- **Ticketıd**: kullanıcının sistemlerindeki bakım bileti kimliğini tanımlar.

- **TTL**: bakım penceresinin dakika cinsinden SÜRESI olan TTL 'yi (yaşam süresi) tanımlar. Bu parametrenin tanımladığı süre dolduktan sonra sistem otomatik olarak uyarı göndermeye başlar.

- **altyapılar**: bakım işlemi sırasında uyarıları bastırmak için hangi güvenlik altyapısından tanımlar:

   - ANOMALI

   - TESPIT

   - IŞLEMDEKI

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **Sensorıds**: bakım işlemi sırasında, IoT algılayıcısı Için hangi Defender 'ın uyarıları göstermemesini tanımlar. Bu,/api/v1/gereçlerden (GET) alınan aynı kimliğe sahip.

- **alt ağlar**: bakım işlemi sırasında hangi alt ağdan uyarıları göstermemek istediğinizi tanımlar. Alt ağ şu biçimde gönderilir: 192.168.0.0/16.

#### <a name="error-codes"></a>Hata kodları

- **201 (oluşturuldu)**: eylem başarıyla tamamlandı.

- **400 (hatalı istek)**: aşağıdaki durumlarda görünür:

   - **TTL** parametresi sayısal değil veya pozitif değil.

   - **Alt ağlar** parametresi yanlış bir biçim kullanılarak tanımlandı.

   - **Ticketıd** parametresi eksik.

   - **Motor** parametresi mevcut güvenlik altyapılarıyla eşleşmiyor.

- **404 (bulunamadı)**: sensörlerden biri yok.

- **409 (çakışma)**: bilet kimliği başka bir açık bakım penceresine bağlı.

- **500 (Iç sunucu hatası)**: herhangi bir beklenmeyen hata.

> [!NOTE]
> Bilet KIMLIĞININ mevcut bir açık pencereye bağlı olmadığından emin olun. Şu dışlama kuralı oluşturuldu: bakım-{belirteç adı}-{bilet KIMLIĞI}.

#### <a name="method---put"></a>Yöntem-PUT

**TTL** parametresini değiştirerek bakım işlemini başlattıktan sonra bakım penceresi süresinin güncelleştirilmesini sağlar. Yeni süre tanımı bir öncekini geçersiz kılar.

Bu yöntem, yapılandırılmış olan süreden daha uzun bir süre ayarlamak istediğinizde faydalıdır.

#### <a name="query-parameters"></a>Sorgu parametreleri

- **Ticketıd**: kullanıcının sistemlerindeki bakım bileti kimliğini tanımlar.

- **TTL**: pencerenin süresini dakika cinsinden tanımlar.

#### <a name="error-code"></a>Hata kodu

- **200 (Tamam)**: eylem başarıyla tamamlandı.

- **400 (hatalı istek)**: aşağıdaki durumlarda görünür:

   - **TTL** parametresi sayısal değil veya pozitif değil.

   - **Ticketıd** parametresi eksik.

   - **TTL** parametresi eksik.

- **404 (bulunamadı)**: bilet kimliği bir açık bakım penceresine bağlı değil.

- **500 (Iç sunucu hatası)**: herhangi bir beklenmeyen hata.

> [!NOTE]
> Bilet KIMLIĞININ mevcut bir açık pencereye bağlı olduğundan emin olun.

#### <a name="method---delete"></a>Yöntem-SIL

Var olan bir bakım penceresini kapatır.

#### <a name="query-parameters"></a>Sorgu parametreleri

- **Ticketıd**: kullanıcının sistemlerindeki bakım anahtarı kimliğini günlüğe kaydeder.

#### <a name="error-code"></a>Hata kodu

- **200 (Tamam)**: eylem başarıyla tamamlandı.

- **400 (hatalı istek)**: **ticketıd** parametresi eksik.

- **404 (bulunamadı)**: bilet kimliği bir açık bakım penceresine bağlı değil.

- **500 (Iç sunucu hatası)**: herhangi bir beklenmeyen hata.

> [!NOTE]
> Bilet KIMLIĞININ mevcut bir açık pencereye bağlı olduğundan emin olun.

#### <a name="method---get"></a>Yöntem-al

Bakım sırasında sistemde gerçekleştirilen tüm açık, kapalı ve güncelleştirme eylemlerinin günlüğünü alın. Yalnızca geçmişte etkin olan ve kapatılmış olan bakım pencereleri için bir günlük alabilirsiniz.

#### <a name="query-parameters"></a>Sorgu parametreleri

- **FromDate**: günlükleri önceden tanımlanmış tarih ve daha sonra filtreler. Biçim 2019-12-30 ' dir.

- **ToDate**: günlükleri önceden tanımlanmış tarihe göre filtreler. Biçim 2019-12-30 ' dir.

- **Ticketıd**: belirli BIR bilet kimliğiyle ilgili günlüklere filtre uygular.

- **Tokenname**: belirli bir belirteç adıyla ilgili günlüklere filtre uygular.

#### <a name="error-code"></a>Hata kodu 

- **200 (Tamam)**: eylem başarıyla tamamlandı.

- **400 (hatalı istek)**: tarih biçimi yanlış.

- **204 (Içerik yok)**: gösterilecek veri yok.

- **500 (Iç sunucu hatası)**: herhangi bir beklenmeyen hata.

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

Bakım penceresi işlemlerini temsil eden JSON nesneleri dizisi.

#### <a name="response-structure"></a>Yanıt yapısı

| Ad | Tür | Yorum | Null Atanabilir |
|--|--|--|--|
| **Hem** | Dize | Örnek: "2012-04-23T18:25:43.511 Z" | hayır |
| **Ticketıd** | Dize | Örnek: "9a5fe99c-d914-4bdav-9332-307384fe40bf" | hayır |
| **tokenName** | Dize | - | hayır |
| **yapılarının** | Dize dizisi | - | evet |
| **Sensorıds** | Dize dizisi | - | evet |
| **ağlarda** | Dize dizisi | - | evet |
| **ttl** | Sayısal | - | evet |
| **operationType** | Dize | Değerler şunlardır "Aç", "GÜNCELLEŞTIR" ve "Kapat" | hayır |

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| POST | kıvrımlı-k-X-d ' {"Ticketıd": "<TICKET_ID>", TTL ": <TIME_TO_LIVE>," motorları ": [<ENGINE1, ENGINE2... ENGINEn>], "Sensorıds": [<SENSOR_ID1, SENSOR_ID2... SENSOR_IDn>], "alt ağlar": [<SUBNET1, SUBNET2.... SUBNETn>]} '-H "yetkilendirme: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | kıvrımlı-k-X-d ' {"Ticketıd": "a5fe99c-d914-4bda-9332-307384fe40bf", "TTL": "20", "Engines": ["ANOMALI"], "Sensorıds": ["5", "3"], "alt ağlar": ["10.0.0.3"]} '-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| PUT | kıvrımlı-k-X PUT-d ' {"Ticketıd": "<TICKET_ID>", TTL ":" <TIME_TO_LIVE> "} '-H" Authorization: <AUTH_TOKEN> "https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | kıvrımlı-k-X PUT-d ' {"Ticketıd": "a5fe99c-d914-4bda-9332-307384fe40bf", "TTL": "20"} '-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| DELETE | kıvrımlı-k-X DELETE-d ' {"Ticketıd": "<TICKET_ID>"} '-H "yetkilendirme: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | kıvrımlı-k-X DELETE-d ' {"Ticketıd": "a5fe99c-d914-4bda-9332-307384fe40bf"} '-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| GET | kıvrımlı-k-H "yetkilendirme: <AUTH_TOKEN>" ' https://<IP_ADDRESS>/external/v1/maintenanceWindow? fromDate =&toDate =&Ticketıd =&tokenName = ' | kıvrımlı-k-H "Authorization: 1234b734a4b4d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/External/v1/maintenanceWindow? fromDate = 2020-01-01&toDate = 2020-07-14&Ticketıd = a5fe99c-d914-4bda-9332-307384fe40bf&tokenName = a ' |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Kullanıcı kimlik bilgilerini doğrulama-/External/Authentication/Validation

Kullanıcı kimlik bilgilerini doğrulamak için bu API 'YI kullanın. IoT Kullanıcı rolleri için tüm Defender, API ile çalışabilir. Bu API 'yi kullanmak için IoT erişim belirtecinin bir Defender 'a ihtiyacınız yoktur.

#### <a name="method"></a>Yöntem

**POST**

#### <a name="request-type"></a>İstek türü

**JSON**

#### <a name="request-example"></a>İstek örneği

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

İşlem durumu ayrıntıları içeren ileti dizesi:

- **Başarılı – msg**: kimlik doğrulaması başarılı

- **Hata – hata**: kimlik bilgileri doğrulaması başarısız oldu

#### <a name="device-fields"></a>Cihaz alanları

| **Ad** | **Tür** | **Yapılamaz** |
|--|--|--|
| **nitelen** | Dize | No |
| **parola** | Dize | No |

#### <a name="response-example"></a>Yanıt örneği

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| POST | kıvrımlı-k-d ' {"kullanıcıadı": "<USER_NAME>", "Password": "PASSWORD"} ' ' https://<IP_ADDRESS>/External/Authentication/Validation ' | kıvrımlı-k-d ' {"kullanıcıadı": "myUser", "Password": " 1234@abcd "} ' ' https:/ <span> /127.0.0.1/External/Authentication/Validation ' |

### <a name="change-password---externalauthenticationset_password"></a>Parolayı Değiştir-/External/Authentication/set_password

Kullanıcıların kendi parolalarını değiştirmesine izin vermek için bu API 'yi kullanın. IoT Kullanıcı rolleri için tüm Defender, API ile çalışabilir. Bu API 'yi kullanmak için IoT erişim belirtecinin bir Defender 'a ihtiyacınız yoktur.

#### <a name="method"></a>Yöntem

**POST**

#### <a name="request-type"></a>İstek türü

**JSON**

#### <a name="request-example"></a>İstek örneği

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

İşlem durumu ayrıntıları içeren ileti dizesi:

- **Başarılı – msg**: Parola değiştirildi

- **Hata – hata**: Kullanıcı kimlik doğrulama hatası

- **Hata – hata**: parola güvenlik ilkesiyle eşleşmiyor

#### <a name="response-example"></a>Yanıt örneği

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Cihaz alanları

| **Ad** | **Tür** | **Yapılamaz** |
|--|--|--|
| **nitelen** | Dize | No |
| **parola** | Dize | No |
| **new_password** | Dize | No |

#### <a name="curl-command"></a>Curl komutu

| Tür | API'ler | Örnek |
|--|--|--|
| POST | kıvrımlı-k-d ' {"kullanıcıadı": "<USER_NAME>", "parola": "<CURRENT_PASSWORD>", "new_password": "<NEW_PASSWORD>"} '-H ' Content-Type: Application/JSON ' https://<IP_address>/External/Authentication/set_password | kıvrımlı-k-d ' {"kullanıcıadı": "myUser", "Password": " 1234@abcd ", "new_password": " abcd@1234 "} '-H ' Content-Type: Application/JSON ' https:/ <span> /127.0.0.1/External/Authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Sistem Yöneticisi tarafından Kullanıcı parolası güncelleştirmesi-/External/Authentication/set_password_by_admin

Sistem yöneticilerinin belirtilen kullanıcıların parolalarını değiştirmesine izin vermek için bu API 'yi kullanın. IoT Yönetici Kullanıcı rolleri için Defender, API ile çalışabilir. Bu API 'yi kullanmak için IoT erişim belirtecinin bir Defender 'a ihtiyacınız yoktur.

#### <a name="method"></a>Yöntem

**POST**

#### <a name="request-type"></a>İstek türü

**JSON**

#### <a name="request-example"></a>İstek örneği

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Yanıt türü

**JSON**

#### <a name="response-content"></a>Yanıt içeriği

İşlem durumu ayrıntıları içeren ileti dizesi:

- **Başarılı – msg**: Parola değiştirildi

- **Hata – hata**: Kullanıcı kimlik doğrulama hatası

- **Hata – hata**: Kullanıcı yok

- **Hata – hata**: parola güvenlik ilkesiyle eşleşmiyor

- **Hata – hata**: kullanıcının parolayı değiştirme izinleri yok

#### <a name="response-example"></a>Yanıt örneği

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Cihaz alanları

| **Ad** | **Tür** | **Yapılamaz** |
|--|--|--|
| **admin_username** | Dize | No |
| **admin_password** | Dize | No |
| **nitelen** | Dize | No |
| **new_password** | Dize | No |

#### <a name="curl-command"></a>Curl komutu

> [!div class="mx-tdBreakAll"]
> | Tür | API'ler | Örnek |
> |--|--|--|
> | POST | kıvrımlı-k-d ' {"admin_username": "<ADMIN_USERNAME>", "admin_password": "<ADMIN_PASSWORD>", "kullanıcıadı": "<user_name>", "new_password": "<NEW_PASSWORD>"} '-H ' Content-Type: Application/JSON ' https://<IP_address>/External/Authentication/set_password_by_admin | kıvrımlı-k-d ' {"admin_user": "adminUser", "admin_password": " 1234@abcd ", "kullanıcıadı": "myUser", "new_password": " abcd@1234 "} '-H ' Content-Type: Application/JSON ' https:/ <span> /127.0.0.1/External/Authentication/set_password_by_admin |

## <a name="next-steps"></a>Sonraki adımlar

- [Bir cihaz envanterinde algılayıcı algılamalarını araştırma](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [Bir cihaz envanterinde tüm kurumsal algılayıcı algılamalarını araştırma](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
