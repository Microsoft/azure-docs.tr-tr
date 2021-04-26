---
title: Azure ön kapıda özel HTTPS 'yi etkinleştirmek için izin verilen CA
description: Azure ön kapısı özel etki alanında HTTPS 'yi etkinleştirmek için kendi sertifikanızı kullanıyorsanız, bunu oluşturmak için izin verilen bir sertifika yetkilisi (CA) kullanmanız gerekir.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91613688"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Azure ön kapıda özel HTTPS 'yi etkinleştirmek için izin verilen sertifika yetkilileri

Azure ön kapı özel etki alanı için [kendi sertifikanızı kullanarak https özelliğini etkinleştirdiğinizde](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate) . TLS/SSL sertifikanızı oluşturmak için izin verilen bir sertifika yetkilisine (CA) ihtiyacınız vardır. Aksi takdirde, izin verilmeyen bir CA veya otomatik olarak imzalanan bir sertifika kullanıyorsanız isteğiniz reddedilir.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
