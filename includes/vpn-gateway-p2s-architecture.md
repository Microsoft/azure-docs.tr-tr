---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93145012"
---
Noktadan siteye yerel Azure sertifika kimlik doğrulaması bağlantıları, Bu alıştırmada yapılandırdığınız aşağıdaki öğeleri kullanır:

* RouteBased VPN ağ geçidi.
* Azure’a yüklenmiş bir kök sertifikanın ortak anahtarı (.cer dosyası). Sertifika karşıya yüklendikten sonra güvenilen bir sertifika olarak kabul edilir ve kimlik doğrulaması için kullanılır.
* Kök sertifikasından oluşturulan bir istemci sertifikası. Sanal ağa bağlanacak her istemci bilgisayarda yüklü olan istemci sertifikası. Bu sertifika, istemci kimlik doğrulaması için kullanılır.
* VPN istemci yapılandırması. VPN istemcisi VPN istemci yapılandırma dosyaları kullanılarak yapılandırılır. Bu dosyalar, istemcinin VNet 'e bağlanması için gereken bilgileri içerir. Dosyalar, işletim sisteminde yerel olarak bulunan VPN istemcisini yapılandırır. Bağlanan her istemcinin yapılandırma dosyalarındaki ayarlar kullanılarak yapılandırılması gerekir.
