---
title: include dosyası
description: include dosyası
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504725"
---
Bir sanal ağ geçidi oluşturduğunuzda, kullanmak istediğiniz ağ geçidi SKU’sunu belirtmeniz gerekir. Daha yüksek bir ağ geçidi SKU’su seçtiğinizde ağ geçidine daha fazla CPU ve ağ bant genişliği ayrılır ve sonuç olarak ağ geçidi, sanal ağda daha yüksek ağ verimliliğini destekleyebilir. 

ExpressRoute sanal ağ geçitleri aşağıdaki SKU 'Ları kullanabilir:

|     | VPN Gateway ve ExpressRoute bir arada | FastPath | En fazla devre bağlantı sayısı |
| --- | --- | --- | --- |
| **Standart** | Yes | Hayır | 4 |
| **HighPerformance** | Yes | Hayır | 4 |
| **UltraPerformance** | Yes | Yes | 16 |