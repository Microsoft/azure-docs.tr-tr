---
title: Azure SQL veritabanı 'nda Intel SGX şifreleme ve kanıtlama planlaması
description: Azure SQL veritabanı 'nda güvenli şifreleme ile Always Encrypted dağıtımını planlayın.
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
ms.openlocfilehash: 4448ce051b0c9e73865e8057cc4f224c9cbeb571
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732753"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Azure SQL veritabanı 'nda Intel SGX şifreleme ve kanıtlama planlaması

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL veritabanı için güvenli şifreleme ile Always Encrypted Şu anda **genel önizleme** aşamasındadır.

Azure SQL veritabanı 'nda [güvenli şifreleme ile Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) , [Intel Software Guard UZANTıLARı (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) kuşları kullanır ve [Microsoft Azure kanıtlama](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation)gerektirir.

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Azure SQL veritabanı 'nda Intel SGX planlaması

Intel SGX, donanım tabanlı güvenilir bir yürütme ortamı teknolojisidir. Intel SGX, [sanal çekirdek modelini](service-tiers-vcore.md) ve [DC Serisi](service-tiers-vcore.md?#dc-series) donanım üretimini kullanan veritabanları için kullanılabilir. Bu nedenle, veritabanınızdaki güvenli şifrelerle Always Encrypted kullanabilmeniz için veritabanını oluştururken DC Serisi donanım oluşturma 'yı seçmeniz gerekir veya mevcut veritabanınızı DC Serisi donanım oluşturmayı kullanacak şekilde güncelleştirebilirsiniz.

> [!NOTE]
> Intel SGX, DC Serisi dışındaki donanım nesilleri içinde kullanılamaz. Örneğin, Intel SGX, 5. nesil donanımları için kullanılamaz ve [DTU modelini](service-tiers-dtu.md)kullanan veritabanları için kullanılamaz.

> [!IMPORTANT]
> Veritabanınız için DC Serisi donanım üretimini yapılandırmadan önce, DC-Series ' in bölgesel kullanılabilirliğini denetleyin ve performans sınırlamalarını anladığınızdan emin olun. Ayrıntılar için bkz. [DC-Series](service-tiers-vcore.md#dc-series).

## <a name="plan-for-attestation-in-azure-sql-database"></a>Azure SQL veritabanı 'nda kanıtlama planlaması

[Microsoft Azure kanıtlama](../../attestation/overview.md) (Önizleme), DC Serisi donanım oluşturma 'Yı kullanarak Azure SQL VERITABANLARıNDA Intel SGX şifreleme dahil, güvenilir yürütme ortamlarının (TEEs) atlaması için bir çözümdür.

Azure SQL veritabanı 'nda Intel SGX şifrelemesi için Azure kanıtlama 'nı kullanmak üzere şunları yapmanız gerekir:

1. Bir [kanıtlama sağlayıcısı](../../attestation/basic-concepts.md#attestation-provider) oluşturun ve bir kanıtlama ilkesiyle yapılandırın. 

2. Azure SQL mantıksal sunucunuza oluşturulan kanıtlama sağlayıcısına erişim izni verin.

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>SGX şifreli ve kanıtlama yapılandırılırken roller ve sorumluluklar

Ortamınızı, Azure SQL veritabanı 'nda Always Encrypted için Intel SGX kuşcüleri ve kanıtlamayı destekleyecek şekilde yapılandırmak, farklı türlerde bileşenleri ayarlamayı içerir: Microsoft Azure kanıtlama, Azure SQL veritabanı ve şifreleme kanıtlamasını tetikleyen uygulamalar. Her bir türün bileşenlerini yapılandırmak, aşağıdaki farklı rollerden birini kabul eden kullanıcılar tarafından gerçekleştirilir:

- Kanıtlama Yöneticisi-Microsoft Azure kanıtlama için bir kanıtlama sağlayıcısı oluşturur, kanıtlama ilkesini yazar, kanıtlama sağlayıcısına Azure SQL mantıksal sunucusu erişimi verir ve ilkeyi uygulama yöneticilerine işaret eden kanıtlama URL 'sini paylaşır.
- Azure SQL veritabanı yöneticisi-DC Serisi donanım oluşturma 'yı seçerek veritabanlarındaki SGX şifreli nesnelerin kullanımını sağlar ve kanıtlama yöneticisine, kanıtlama sağlayıcısına erişmesi gereken Azure SQL mantıksal sunucusu kimliğini sağlar.
- Uygulama Yöneticisi-kanıtlama yöneticisinden alınan kanıtlama URL 'SI ile uygulamaları yapılandırır.

Üretim ortamlarında (gerçek hassas verileri işleme), her ayrı rolün farklı kişiler tarafından kabul edildiği kanıtlamayı yapılandırırken kuruluşunuzun rol ayrımı olması önemlidir. Özellikle, kuruluşunuzda Always Encrypted dağıtmanın hedefi Azure SQL veritabanı yöneticilerinin hassas verilere erişemeyeceğinden saldırı yüzeyi alanını azaltmaktır, Azure SQL veritabanı yöneticileri kanıtlama ilkelerini denetlemelidir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL veritabanınız için Intel SGX 'yi etkinleştirme](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>Ayrıca bkz.

- [Öğretici: Azure SQL veritabanında güvenli şifreleme ile Always Encrypted kullanmaya başlama](always-encrypted-enclaves-getting-started.md)