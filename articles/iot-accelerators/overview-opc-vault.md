---
title: OPC Kasası nedir?-Azure | Microsoft Docs
description: Bu makalede OPC Kasası 'na bir genel bakış sunulmaktadır. Bulutta OPC UA uygulamaları için sertifika yaşam döngüsünü yapılandırabilir, kaydedebilir ve yönetebilir.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 715ed204e28d6260c28fa099b40fc78aa12de44d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646924"
---
# <a name="what-is-opc-vault"></a>OPC Kasası nedir?

> [!IMPORTANT]
> Bu makaleyi güncelleştirdiğimiz sürece, en güncel içerik için bkz. [Azure endüstriyel IoT](https://azure.github.io/Industrial-IoT/) .

OPC Kasası, buluttaki OPC UA sunucusu ve istemci uygulamaları için sertifika yaşam döngüsünü yapılandırabileceğiniz, kaydedebildiğiniz ve yönetebilen bir mikro hizmettir. Bu makalede OPC kasasının basit kullanım durumları açıklanmaktadır.

## <a name="certificate-management"></a>Sertifika yönetimi

Örneğin, bir üretim şirketinin OPC UA sunucu makinesini yeni oluşturulan istemci uygulamasına bağlanması gerekir. Üretici, sunucu makinesine ilk erişimi yaptığında, istemci uygulamanın güvenli olmadığını belirtmek için OPC UA sunucu uygulamasında hemen bir hata iletisi gösterilir. Bu mekanizma, tüm yetkisiz uygulama erişimini engellemek için OPC UA sunucu makinesinde oluşturulmuştur ve bu da, mağaza katında çarpıcı bir şekilde çalışmaya engel olur.

## <a name="application-security-management"></a>Uygulama güvenliği yönetimi
Bir güvenlik uzmanı, OPC UA sunucusunun herhangi bir istemci uygulamasıyla iletişim kurmasını sağlamak için OPC kasamikro hizmetini kullanır, çünkü OPC Kasası sertifika kayıt defteri, depolama ve yaşam döngüsü yönetimi için tüm işlevlere sahiptir. Artık OPC UA sunucusu güvenli bir şekilde bağlı olduğundan, yeni oluşturulan istemci uygulamasıyla iletişim kurabilir

## <a name="the-complete-opc-vault-architecture"></a>Tüm OPC Kasası mimarisi
Aşağıdaki diyagramda, tüm OPC Kasası mimarisi gösterilmektedir.

![OPC Kasası mimarisi](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık OPC kasası ve kullanımları hakkında bilgi edindiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

[OPC Kasası mimarisi](overview-opc-vault-architecture.md)