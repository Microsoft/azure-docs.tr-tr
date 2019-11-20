---
title: Mevcut bir kullanılabilirlik kümesine Azure VM 'lerinin eklenmesinin desteklenebilirliği | Microsoft Docs
description: Mevcut bir kullanılabilirlik kümesine Azure VM 'lerinin eklenmesinin desteklenebilirliği.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3954df389516aa7199022d713dc63d62dda961ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155452"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Mevcut bir kullanılabilirlik kümesine Azure VM 'lerinin eklenmesinin desteklenebilirliği

Mevcut bir kullanılabilirlik kümesine yeni sanal makineler (VM 'Ler) eklerken zaman zaman sınırlamalar yaşayabilirsiniz. Aşağıdaki grafikte, aynı Kullanılabilirlik kümesinde karıştırabilmeniz gereken VM Serisi ayrıntıları verilmiştir.

Farklı türlerde VM 'leri karıştırmak için desteklenebilirlik matrisi aşağıda verilmiştir:

Dizi & kullanılabilirlik kümesi|İkinci VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|İlk VM|||||||
|A||Tamam|Tamam|Tamam|Tamam|Tamam|
|Av2||Tamam|Tamam|Tamam|Tamam|Tamam|
|D||Tamam|Tamam|Tamam|Tamam|Tamam|
|Dv2||Tamam|Tamam|Tamam|Tamam|Tamam|
|Dv3||Tamam|Tamam|Tamam|Tamam|Tamam|

Tüm diğer seriler, belirli bir donanım gerektirdiğinden aynı Kullanılabilirlik kümesinde yer alamadı.

Adanmış RDMA arka uç ağı gereksinimi nedeniyle A8/A9 VM boyutu karıştırılamaz.
