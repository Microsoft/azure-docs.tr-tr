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
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826212"
---
# <a name="what-is-opc-vault"></a>OPC Kasası nedir?

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

> [!div class="nextstepaction"]
> [OPC Kasası mimarisi](overview-opc-vault-architecture.md)
