---
title: Azure 'da kuşışları kanıtlamanları
description: Gizli bilgi işlem güvenilir ortamınızın güvenli olduğunu doğrulamak için kanıtlama nasıl kullanabileceğinizi öğrenin
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: a7b0ca65329016b0a73f612115d8caba43dfbe2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551360"
---
# <a name="attesting-sgx-enclaves"></a>Attesting SGX Enclaven

Azure üzerinde gizli bilgi işlem, kodunuzun veya verilerinizin bir bölümünü ayırabilecekleri Intel SGX tabanlı sanal makineler sunar. Bu kuşışlarla [çalışırken, güvenilir](confidential-computing-enclaves.md)ortamınızın güvenli olduğunu doğrulama ve doğrulama almak isteyeceksiniz. Bu doğrulama, kanıtlama işlemidir. 

## <a name="overview"></a>Genel Bakış 

Kanıtlama, bağlı olan tarafın yazılımın (1) bir kuşsa ve (2) tarafından güncel ve güvenli olduğunu arttığı güvenlere erişmesini sağlar. Örneğin, bir kuşatma, temeldeki donanımın platformda bulunduğu kanıtı içeren bir kimlik bilgisi oluşturmasını ister. Rapor daha sonra raporun aynı platformda oluşturulduğunu doğrulayan ikinci bir kuşya verilebilir.

![kuşdaki onaylamasını sağlar kodu](media/attestation/attestation.png)



Kanıtlama, sistem yazılımıyla ve Silicon ile uyumlu olan güvenli bir kanıtlama hizmeti kullanılarak uygulanmalıdır. Kullanabileceğiniz bazı hizmetlere örnek olarak şunlar gösterilebilir

- [Microsoft Azure kanıtlama (Önizleme)](../attestation/overview.md) veya
- [Intel 'in kanıtlama ve sağlama hizmetleri](https://software.intel.com/sgx/attestation-services)


Azure gizli bilgi işlem Intel SGX altyapısı ile uyumlu olan. 

## <a name="next-steps"></a>Sonraki adımlar
[Şifreleme kullanan uygulamalar için Microsoft Azure kanıtlama örneklerini](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)deneyin.