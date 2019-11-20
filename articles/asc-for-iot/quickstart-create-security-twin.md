---
title: 'Hızlı başlangıç: IoT için Azure Güvenlik Merkezi için güvenlik modülü ikizi oluşturma'
description: Bu hızlı başlangıçta, IoT için Azure Güvenlik Merkezi ile kullanmak üzere IoT modülü ikizi için bir Azure Güvenlik Merkezi oluşturmayı öğrenin.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: b362130c2b717f813a6332f81a3c8179bea4166a
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904157"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Hızlı başlangıç: azureiotsecurity modülü oluşturma ikizi

Bu hızlı başlangıçta, yeni cihazlar için tek tek _azureiotsecurity_ Module TWINS oluşturma veya bir IoT Hub tüm cihazlar için toplu işlem modülü oluşturma işlemleri açıklanmaktadır.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Azureiotsecurity Module TWINS 'i anlama 

Azure 'da oluşturulan IoT çözümleri için, cihaz ikis, hem cihaz yönetimi hem de süreç otomasyonu 'nda anahtar rol oynar. 

IoT için Azure Güvenlik Merkezi, mevcut IoT cihaz yönetimi Platformunuzla tam tümleştirme sunarak, cihaz güvenlik durumunuzu yönetmenize ve mevcut cihaz denetimi yeteneklerini kullanmanıza olanak sağlar.
IoT tümleştirmesi için Azure Güvenlik Merkezi, IoT Hub ikizi mekanizması kullanılarak elde edilir.  

Azure IoT Hub modül TWINS 'in genel kavramı hakkında daha fazla bilgi edinmek için bkz. [IoT Hub Module TWINS](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) . 
 
IoT için Azure Güvenlik Merkezi, Module ikizi mekanizmasını kullanır ve cihazlarınızın her biri için _azureiotsecurity_ adlı bir güvenlik modülünü ikizi.

İkizi güvenlik modülü, cihazlarınızın her biri için cihaz güvenliğiyle ilgili tüm bilgileri tutar. 
 
IoT özellikleri için Azure Güvenlik Merkezi ' ni tam olarak kullanabilmek için, bu güvenlik modülü sayısını hizmette her cihaz için oluşturmanız, yapılandırmanız ve kullanmanız gerekir.  

## <a name="create-azureiotsecurity-module-twin"></a>Azureiotsecurity modülü oluşturma ikizi 

_azureiotsecurity_ Module TWINS iki şekilde oluşturulabilir:
1. [Modül Batch betiği](https://aka.ms/iot-security-github-create-module) -varsayılan yapılandırmayı kullanarak bir modül ikizi olmadan yeni cihazlar veya cihazlar için otomatik olarak modül ikizi oluşturur.
2. Her modül için her bir cihaz için belirli yapılandırmalara sahip her modülü ikizi el ile düzenleyin.

>[!NOTE] 
> Batch yönteminin kullanılması, var olan azureiotsecurity Module TWINS 'in üzerine yazmaz. Batch yönteminin kullanılması, yalnızca bir güvenlik modülü ikizi olmayan cihazlar için yeni modül TWINS 'i oluşturur. 

Var olan bir modülün ikizi yapılandırmasını değiştirme veya değiştirme hakkında bilgi edinmek için [Aracı yapılandırması](how-to-agent-configuration.md) bölümüne bakın. 

Bir cihaz için el ile yeni bir _azureiotsecurity_ Module ikizi oluşturmak için aşağıdaki yönergeleri kullanın: 

1. IoT Hub, için güvenlik modülü ikizi oluşturmak istediğiniz cihazı bulun ve seçin.
1. Cihazınıza ve ardından **modül kimliği Ekle**' ye tıklayın.
1. **Modül kimliği adı** alanına **azureiotsecurity**girin.

1. **Save (Kaydet)** düğmesine tıklayın. 

## <a name="verify-creation-of-a-module-twin"></a>Modül ikizi oluşturmayı doğrulama

Belirli bir cihaz için bir güvenlik modülü ikizi varolup olmadığını doğrulamak için:

1. Azure IoT Hub, **araştırıcılar** menüsünden **IoT cihazları** ' nı seçin.    
1. Cihaz KIMLIĞINI girin veya **sorgu cihazı alanında** bir seçenek belirleyin ve **sorgu cihazları**' na tıklayın. 
    ![sorgu cihazları](./media/quickstart/verify-security-module-twin.png)
1. Cihazı seçin veya çift tıklayarak cihaz ayrıntıları sayfasını açın. 
1. **Modül kimlikleri** menüsünü seçin ve cihazla ilişkili modül kimlikleri listesinde **azureiotsecurity** modülünün varlığını onaylayın. 
    bir cihaz](./media/quickstart/verify-security-module-twin-3.png) ilişkili ![modüller


IoT modülü TWINS için Azure Güvenlik Merkezi 'nin özelliklerini özelleştirme hakkında daha fazla bilgi edinmek için bkz. [Aracı yapılandırması](how-to-agent-configuration.md).

## <a name="next-steps"></a>Sonraki adımlar

Özel uyarıların nasıl yapılandırılacağını öğrenmek için sonraki makaleye ilerleyin...

> [!div class="nextstepaction"]
> [Özel uyarıları yapılandırma](quickstart-create-custom-alerts.md)
