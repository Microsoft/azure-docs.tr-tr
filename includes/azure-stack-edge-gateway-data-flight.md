---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96468044"
---
Uçuş 'daki veriler için:

- Standart Aktarım Katmanı Güvenliği (TLS) 1,2, cihaz ile Azure arasında taşınan veriler için kullanılır. TLS 1,1 ve önceki bir sürümü için geri dönüş yoktur. TLS 1,2 desteklenmiyorsa, aracı iletişimi engellenir. Ayrıca, Portal ve SDK yönetimi için TLS 1,2 de gereklidir.
- İstemciler, bir tarayıcının yerel Web Kullanıcı arabirimi aracılığıyla cihazınıza erişebilince, varsayılan güvenli protokol olarak Standart TLS 1,2 kullanılır.

  - En iyi uygulama, tarayıcınızı TLS 1,2 kullanacak şekilde yapılandırmaktır.
  - Cihazınız yalnızca TLS 1,2 ' i destekliyor ve daha eski sürüm TLS 1,1 veya TLS 1,0 'yi desteklemez.
- Veri sunucularınızdan verileri korumak için şifreleme ile SMB 3,0 kullanmanızı öneririz.
