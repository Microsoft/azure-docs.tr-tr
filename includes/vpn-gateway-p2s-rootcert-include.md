---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 59d18c89a8f66f781f01d1dace9d8968df17a626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041554"
---
Kök sertifika için. cer dosyasını alın. Bir kurumsal çözümle oluşturulmuş bir kök sertifika (önerilir) ya da kendinden imzalı bir sertifika oluşturabilirsiniz. Kök sertifikayı oluşturduktan sonra, ortak sertifika verilerini (özel anahtarı değil) Base64 kodlamalı X. 509.952. cer dosyası olarak dışarı aktarın. Bu dosyayı daha sonra Azure 'a yüklersiniz.

* **Kurumsal sertifika:** Kurumsal bir çözüm kullanıyorsanız, var olan sertifika zincirinizi kullanabilirsiniz. Kullanmak istediğiniz kök sertifika için. cer dosyasını alın.
* **Otomatik olarak imzalanan kök sertifika:** Kurumsal bir sertifika çözümü kullanmıyorsanız, otomatik olarak imzalanan bir kök sertifika oluşturun. Aksi halde, oluşturduğunuz sertifikalar P2S bağlantılarınız ile uyumlu olmayacaktır ve istemciler bağlanmaya çalıştıklarında bir bağlantı hatası alırlar. Azure PowerShell, MakeCert veya OpenSSL kullanabilirsiniz. Aşağıdaki makalelerdeki adımlarda, uyumlu bir otomatik olarak imzalanan kök sertifika oluşturma açıklanır:

  * [Windows 10 PowerShell yönergeleri](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): Bu yönergeler, sertifika oluşturmak için Windows 10 ve PowerShell gerektirir. Kök sertifikadan oluşturulan istemci sertifikaları tüm desteklenen P2S istemcilere yüklenebilir.
  * [MakeCert yönergeleri](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Sertifikaları oluşturmak için kullanabileceğiniz bir Windows 10 bilgisayarınızın yoksa MakeCert kullanın. MakeCert kullanım dışı olsa da, sertifika oluşturmak için kullanmaya devam edebilirsiniz. Kök sertifikadan oluşturduğunuz istemci sertifikaları, desteklenen herhangi bir P2S istemcisine yüklenebilir.
  * [Linux yönergeleri](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).