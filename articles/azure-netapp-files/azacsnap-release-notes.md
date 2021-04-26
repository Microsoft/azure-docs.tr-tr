---
title: Azure NetApp Files için Azure uygulaması tutarlı anlık görüntü Aracı sürüm notları | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracı için sürüm notları sağlar.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111480"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulaması tutarlı anlık görüntü aracı için sürüm notları (Önizleme)

Bu sayfada, yeni işlevsellik sağlamak veya hataları çözmek için AzAcSnap 'e yapılan önemli değişiklikler listelenir.

## <a name="march-2021"></a>Mart-2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v 5.0 Önizleme (derleme: 20210318.30771)

AzAcSnap v 5.0 Önizlemesi (Build: 20210318.30771) aşağıdaki düzeltmeler ve geliştirmeler ile yayımlanmıştır:

- AZACSNAP kullanıcısını SAP HANA kiracısında ekleme gereksinimi kaldırıldı, [SAP HANA ile Iletişimi etkinleştirme](azacsnap-installation.md#enable-communication-with-sap-hana) bölümüne bakın.
- El ile QOS ile yapılandırılmış birimlerde [geri yüklemeye](azacsnap-cmd-ref-restore.md) izin vermek için düzeltilir.
- Azure büyük örneği için SSH bağlantılarını kısıtlamak üzere mutex denetimi eklendi.
- Boşluk ve diğer ilgili sorunları içeren yol adlarını işlemek için yükleyiciyi giderin.
- Diğer veritabanı sunucularını desteklemeye hazırlanmasından sonra '--hanasıd ' isteğe bağlı '--dbsıd ' parametresini değiştirdi.

Yükleyicinin [en son sürümünü](https://aka.ms/azacsnapdownload) indirin ve nasıl [başladığına](azacsnap-get-started.md)bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure uygulaması tutarlı anlık görüntü aracı 'nı kullanmaya başlama](azacsnap-get-started.md)
