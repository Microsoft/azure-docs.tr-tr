---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563252"
---
| Kullanım modeli | Önbelleğe alma modu | Arka uç doğrulaması | En fazla geri yazma gecikmesi |
|--|--|--|--|
| Yoğun, seyrek okunan yazma işlemleri | Okuma | Asla | Yok |
| %15 yazma boyutundan büyük | Okuma/yazma | 8 saat | 20 dakika |
| İstemcileri önbelleği atlar | Okuma | 30 saniye | Yok |
| %15 yazma boyutundan büyük, sık kullanılan arka uç denetimi (30 saniye) | Okuma/yazma | 30 saniye | 20 dakika |
| %15 yazma daha fazla, sık kullanılan arka uç denetimi (60 saniye) | Okuma/yazma | 60 saniye | 20 dakika |
| %15 yazma boyutundan büyük, sık geri yazma | Okuma/yazma | 30 saniye | 30 saniye |
| Yoğun okuma, her 3 saatte bir yedekleme sunucusu denetleniyor | Okuma | 3 saat | Yok |
