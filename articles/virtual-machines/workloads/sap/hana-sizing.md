---
title: Azure 'da SAP HANA boyutlandırma (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA boyutlandırma (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2540a0f9ea702ff620ccd4b68208cbb2e70aafd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101137"
---
# <a name="sizing"></a>Boyutlandırma

HANA büyük örneğinin boyutlandırılması, genel olarak HANA için boyutdan farklı değildir. Diğer RDBMS 'den HANA 'ya taşımak istediğiniz mevcut ve dağıtılan sistemler için SAP, mevcut SAP sistemlerinizde çalışan bir dizi rapor sağlar. Veritabanı HANA 'ya taşınırsa, bu raporlar verileri denetler ve HANA örneği için bellek gereksinimlerini hesaplar. Bu raporları çalıştırma ve en son düzeltme eklerini veya sürümlerini edinme hakkında daha fazla bilgi için aşağıdaki SAP notlarını okuyun:

- [SAP Note #1793345-HANA 'da SAP Suite için boyutlandırma](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 HANA ve S/4 HANA boyutlandırma raporunda Suite](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330-SSS: HANA boyutlandırma raporunda SAP BW](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976-HANA 'da siyah beyaz için boyutlandırma raporu](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290-bit GENIŞLIĞI için HANA 'da yeni boyutlandırma raporu](https://launchpad.support.sap.com/#/notes/2296290)

Yeşil alan uygulamaları için SAP Quick sizer, HANA 'nın en üstünde SAP yazılımı uygulamasının bellek gereksinimlerini hesaplamak için kullanılabilir.

Veri hacmi büyüdükçe, HANA için bellek gereksinimleri artırın. Gelecekte neler olduğunu tahmin etmenize yardımcı olması için geçerli bellek tüketiminizi göz önünde bulundurun. Bellek gereksinimlerine bağlı olarak, talep içeriğinizi HANA büyük örnek SKU 'larından birine eşleyebilirsiniz.

**Sonraki adımlar**
- [Ekleme gereksinimlerini](hana-onboarding-requirements.md) inceleyin