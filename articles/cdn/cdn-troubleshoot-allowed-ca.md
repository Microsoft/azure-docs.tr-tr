---
title: Azure CDN 'de özel HTTPS 'yi etkinleştirmek için izin verilen CA
description: Özel bir etki alanında HTTPS 'yi etkinleştirmek için kendi sertifikanızı kullanıyorsanız, bu sertifikayı oluşturmak için izin verilen bir sertifika yetkilisini (CA) kullanmanız gerekir.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5462502514a3e327913122fe99fd699856891216
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083114"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Azure CDN 'de özel HTTPS 'yi etkinleştirmek için izin verilen sertifika yetkilileri

Microsoft uç noktasından bir **Azure CDN Standart** üzerinde Azure Content DELIVERY Network (CDN) özel etki alanı için, [kendı sertifikanızı kullanarak https ÖZELLIĞINI etkinleştirdiğinizde](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), SSL sertifikanızı oluşturmak için izin verilen BIR sertifika yetkilisi (CA) kullanmanız gerekir. Aksi takdirde, izin verilmeyen bir CA veya otomatik olarak imzalanan bir sertifika kullanıyorsanız isteğiniz reddedilir.

> [!NOTE]
> Özel HTTPS 'yi etkinleştirmek için kendi sertifikanızı kullanma seçeneği, yalnızca **Microsoft profillerinin Azure CDN standardı** ile kullanılabilir. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

