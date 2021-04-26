---
title: Internet eşlemesi-SSS
titleSuffix: Azure
description: Internet eşlemesi-SSS
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75775504"
---
# <a name="internet-peering---faqs"></a>Internet eşlemesi-SSS

Genel sorular için aşağıdaki bilgileri gözden geçirebilirsiniz.

**Internet eşlemesi ve eşleme hizmeti arasındaki fark nedir?**

Eşleme hizmeti, kurumsal müşterileri için Microsoft 'a kurumsal düzeyde genel IP bağlantısı sağlamayı amaçlayan bir hizmettir. Kurumsal sınıf Internet, bir HA bağlantısı için Microsoft ve yedeklilik ile yüksek aktarım hızı bağlantısı olan ISS 'Ler aracılığıyla bağlantı içerir. Ayrıca, Kullanıcı trafiği en yakın Microsoft Edge gecikmesi için en iyi duruma getirilmiştir. Eşleme hizmeti, iş ortağı taşıyıcısı ile eşleme bağlantısı oluşturur. İş ortağı ile eşleme bağlantısı, Exchange eşleme aksine doğrudan eşleme olmalıdır. Doğrudan eşleme yerel ve coğrafi yedeklilik içermelidir.

**Eski eşleme nedir?**

Azure PowerShell kullanılarak ayarlanan eşleme bağlantısı, bir Azure kaynağı olarak yönetilir. Geçmişte ayarlanan eşleme bağlantıları sistemimizde, Azure kaynağı olarak yönetmeye dönüştürmek üzere, eski eşleme olarak depolanır.

**New-AzPeeringDirectConnectionObject çağrıldığında, Microsoft ve eş cihazlara hangi IP adresleri verilir?**

New-AzPeeringDirectConnectionObject cmdlet 'i çağrılırken bir/31 adresi (a. b. c. d/31) veya/30 adresi (a. b. c. d/30) girilir. İlk IP adresi (a. b. c. d + 0), eşin cihazına ve ikinci IP adresine (a. b. c. d + 1) Microsoft cihaza verilir.

**New-AzPeeringDirectConnectionObject cmdlet 'inde MaxPrefixesAdvertisedIPv4 ve MaxPrefixesAdvertisedIPv6 parametreleri nedir?**

MaxPrefixesAdvertisedIPv4 ve MaxPrefixesAdvertisedIPv6 parametreleri, bir eşin Microsoft 'un kabul etmesini istediğini en fazla IPv4 ve IPv6 öneki sayısını temsil eder. Bu parametreler dilediğiniz zaman değiştirilebilir.