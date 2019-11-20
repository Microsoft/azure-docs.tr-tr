---
title: IoT için Azure Güvenlik Merkezi 'nin kimlik doğrulama yöntemleri | Microsoft Docs
description: IoT hizmeti için Azure Güvenlik Merkezi 'ni kullanırken kullanılabilecek farklı kimlik doğrulama yöntemleri hakkında bilgi edinin.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 16f7f91e02d118d9f9a295ebb79a6cd0187dd9fd
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596461"
---
# <a name="security-agent-authentication-methods"></a>Güvenlik Aracısı kimlik doğrulama yöntemleri 

Bu makalede, IoT Hub kimlik doğrulaması yapmak için AzureIoTSecurity aracısında kullanabileceğiniz farklı kimlik doğrulama yöntemleri açıklanmaktadır.

IoT Hub IoT için Azure Güvenlik Merkezi 'ndeki her cihaz eklendi için bir güvenlik modülü gereklidir. Cihazın kimliğini doğrulamak için IoT için Azure Güvenlik Merkezi iki yöntemden birini kullanabilir. Mevcut IoT çözümünüz için en iyi şekilde çalışacak yöntemi seçin. 

> [!div class="checklist"]
> * SecurityModule seçeneği
> * Cihaz seçeneği

## <a name="authentication-methods"></a>Kimlik doğrulama yöntemleri

AzureIoTSecurity aracısının kimlik doğrulaması gerçekleştirmesi için iki yöntemi:

 - **Securitymodule** kimlik doğrulama modu<br>
   Aracının kimliği, cihaz kimliğinden bağımsız olarak güvenlik modülü kimliği kullanılarak doğrulanır.
   Güvenlik aracısının güvenlik modülü aracılığıyla adanmış bir kimlik doğrulama yöntemi kullanmasını istiyorsanız bu kimlik doğrulaması türünü kullanın (yalnızca simetrik anahtar).
        
 - **Cihaz** kimlik doğrulama modu<br>
    Bu yöntemde, güvenlik aracısının ilk olarak cihaz kimliğiyle kimliğini doğrular. İlk kimlik doğrulamasından sonra IoT Aracısı için Azure Güvenlik Merkezi, REST API cihaz kimlik doğrulama verileriyle birlikte IoT Hub bir **rest** çağrısı yapar. IoT Aracısı için Azure Güvenlik Merkezi, IoT Hub güvenlik modülü kimlik doğrulama yöntemini ve verileri ister. Son adımda IoT Aracısı için Azure Güvenlik Merkezi, IoT modülü için Azure Güvenlik Merkezi 'Ne karşı bir kimlik doğrulaması gerçekleştirir.
    
    Güvenlik aracısının mevcut bir cihaz kimlik doğrulama yöntemini (otomatik olarak imzalanan sertifika veya simetrik anahtar) yeniden kullanmasını istiyorsanız bu kimlik doğrulaması türünü kullanın. 

' Nin nasıl yapılandırılacağını öğrenmek için bkz. [Güvenlik Aracısı yükleme parametreleri](#security-agent-installation-parameters) .
                                
## <a name="authentication-methods-known-limitations"></a>Kimlik doğrulama yöntemleri bilinen sınırlamalar

- **Securitymodule** kimlik doğrulama modu yalnızca simetrik anahtar kimlik doğrulamasını destekler.
- CA Imzalı sertifika, **cihaz** kimlik doğrulama modu tarafından desteklenmiyor.  

## <a name="security-agent-installation-parameters"></a>Güvenlik Aracısı yükleme parametreleri

[Bir güvenlik Aracısı dağıtıldığında](how-to-deploy-agent.md), kimlik doğrulaması ayrıntıları bağımsız değişken olarak sağlanmalıdır.
Bu bağımsız değişkenler aşağıdaki tabloda belgelenmiştir.


|Linux parametre adı | Windows parametre adı | Toplu parametre |Açıklama|Seçenekler|
|---------------------|---------------|---------|---------------|---------------|
|kimlik doğrulama-kimlik|Authenticationıdentity|AUI|Kimlik doğrulama kimliği| **Securitymodule** veya **cihaz**|
|Authentication-yöntemi|AuthenticationMethod|AUD|Kimlik doğrulama Yöntemi|**SymmetricKey** veya **Selfsignedcertificate**|
|dosya yolu|FilePath|vadeli|Sertifikayı veya simetrik anahtarı içeren dosyanın mutlak tam yolu| |
|konak adı|ana bilgisayar adı|hn|IoT Hub FQDN 'SI|Örnek: ContosoIotHub.azure-devices.net|
|cihaz kimliği|DeviceId|içerik|Cihaz Kimliği|Örnek: MyDevice1|
|Sertifika-konum-tür|CertificateLocationKind|CL|Sertifika depolama konumu|**YerelDosya** veya **Mağaza**|
|


Güvenlik aracısının yüklenmesi betiği kullanılırken, aşağıdaki yapılandırma otomatik olarak gerçekleştirilir. Güvenlik Aracısı kimlik doğrulamasını el ile düzenlemek için yapılandırma dosyasını düzenleyin. 

## <a name="change-authentication-method-after-deployment"></a>Dağıtımdan sonra kimlik doğrulama yöntemini değiştir

Bir güvenlik Aracısı yükleme betiği ile dağıtıldığında, bir yapılandırma dosyası otomatik olarak oluşturulur.

Dağıtımdan sonra kimlik doğrulama yöntemlerini değiştirmek için yapılandırma dosyasını el ile düzenlemeniz gerekir.


### <a name="c-based-security-agent"></a>C#tabanlı güvenlik Aracısı

_Authentication. config dosyasını_ aşağıdaki parametrelerle düzenleyin:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C tabanlı güvenlik Aracısı

_Localconfiguration. JSON öğesini_ şu parametrelerle düzenleyin:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Ayrıca bkz.
- [Güvenlik aracılarına genel bakış](security-agent-architecture.md)
- [Güvenlik aracısını dağıt](how-to-deploy-agent.md)
- [Ham güvenlik verilerine erişin](how-to-security-data-access.md)
