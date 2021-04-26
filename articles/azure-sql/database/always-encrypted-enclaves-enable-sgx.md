---
title: Azure SQL veritabanınız için Intel SGX 'yi etkinleştirme
description: Azure SQL veritabanı 'nda, SGX özellikli bir donanım oluşturma seçerek Always Encrypted için Intel SGX 'i nasıl etkinleştireceğinizi öğrenin.
keywords: verileri şifreleme, SQL şifreleme, veritabanı şifreleme, hassas veriler, Always Encrypted, güvenli şifreleme, SGX, kanıtlama
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: ded1406c47bb3f00c366da7a5b28319f3712f8a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733765"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Azure SQL veritabanınız için Intel SGX 'yi etkinleştirme 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL veritabanı için güvenli şifreleme ile Always Encrypted Şu anda **genel önizleme** aşamasındadır.

Azure SQL veritabanı 'nda [güvenli şifrelerle Always Encrypted,](/sql/relational-databases/security/encryption/always-encrypted-enclaves) [Intel Software Guard UZANTıLARı (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) şifreleme kullanır. Intel SGX 'nin kullanılabilmesi için, veritabanının [Vcore modelini](service-tiers-vcore.md) ve [DC Serisi](service-tiers-vcore.md#dc-series) donanım üretimini kullanması gerekir.

Intel SGX kuşağını etkinleştirmek için DC Serisi donanım oluşturmayı yapılandırma, Azure SQL veritabanı yöneticisinin sorumluluğundadır. [SGX şifreleme ve kanıtlama yapılandırılırken rol ve sorumluluklara](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)bakın.

> [!NOTE]
> Intel SGX, DC Serisi dışındaki donanım nesilleri içinde kullanılamaz. Örneğin, Intel SGX, 5. nesil donanımları için kullanılamaz ve [DTU modelini](service-tiers-dtu.md)kullanan veritabanları için kullanılamaz.

> [!IMPORTANT]
> Veritabanınız için DC Serisi donanım üretimini yapılandırmadan önce, DC-Series ' in bölgesel kullanılabilirliğini denetleyin ve performans sınırlamalarını anladığınızdan emin olun. Daha fazla bilgi için bkz. [DC-Series](service-tiers-vcore.md#dc-series).

Belirli bir donanım oluşturmayı kullanmak üzere yeni veya mevcut bir veritabanının nasıl yapılandırılacağı hakkında ayrıntılı yönergeler için, bkz. [donanım oluşturma seçme](service-tiers-vcore.md#selecting-a-hardware-generation).
   
## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL veritabanı sunucunuz için Azure kanıtlama yapılandırma](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Ayrıca bkz.

- [Öğretici: Azure SQL veritabanında güvenli şifreleme ile Always Encrypted kullanmaya başlama](always-encrypted-enclaves-getting-started.md)