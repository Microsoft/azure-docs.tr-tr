---
title: Özel HTTPS 'yi etkinleştirmek için izin verilen CA
titleSuffix: Azure Content Delivery Network
description: Özel bir etki alanında HTTPS 'yi etkinleştirmek için kendi sertifikanızı kullanıyorsanız, bu sertifikayı oluşturmak için izin verilen bir sertifika yetkilisini (CA) kullanmanız gerekir.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99573407"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>Özel HTTPS 'yi etkinleştirmek için izin verilen sertifika yetkilileri

Bir Azure CDN (Content Delivery Network) özel etki alanı için [kendi sertifikanızı kullanarak https özelliğini etkinleştirdiğinizde](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) belirli sertifika gereksinimleri gereklidir. 

* **Microsoft profilinden Azure CDN Standard** , aşağıdaki listede onaylanan sertifika YETKILILERINDEN (CA) birinden bir sertifika gerektirir. Onaylanmamış bir CA 'dan bir sertifika veya otomatik olarak imzalanan bir sertifika kullanılıyorsa, istek reddedilir. 

* Verizon profillerden Verizon ve **Azure CDN Premium** **'dan Azure CDN Standart** , geçerli bir CA 'dan geçerli herhangi bir sertifikayı kabul eder. Verizon profilleri otomatik olarak imzalanan sertifikaları desteklemez.

> [!NOTE]
> Özel etki alanı HTTPS özelliğini etkinleştirmek için kendi sertifikanızı kullanma seçeneği, **Akamai profillerinden Azure CDN Standart** *için kullanılamaz.* 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

