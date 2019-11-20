---
title: Yönetilen örnek VNet/alt ağ boyutunu belirleme
description: Bu konu başlığı altında, Azure SQL veritabanı yönetilen örneklerinin dağıtılacağı alt ağın boyutunun nasıl hesaplanacağı açıklanır.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 7f0ef26343284b7b668e71676114586f4bec8b9e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825749"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Azure SQL veritabanı yönetilen örneği için VNet alt ağ boyutunu belirleme

Azure SQL veritabanı yönetilen örneği bir Azure [sanal ağı (VNet)](../virtual-network/virtual-networks-overview.md)içinde dağıtılmalıdır.

VNet alt ağında dağıtılabilecek yönetilen örneklerin sayısı alt ağın boyutuna bağlıdır (alt ağ aralığı).

Yönetilen bir örnek oluşturduğunuzda Azure, sağlama sırasında seçtiğiniz katmana bağlı olarak bir dizi sanal makine ayırır. Bu sanal makineler alt ağınızda ilişkili olduğundan, IP adresleri gerektirir. Düzenli işlemler ve hizmet bakımı sırasında yüksek kullanılabilirlik sağlamak için Azure, ek sanal makineler ayırabilir. Sonuç olarak, bir alt ağdaki gerekli IP adreslerinin sayısı o alt ağdaki yönetilen örnek sayısından daha büyük.

Tasarıma göre, yönetilen bir örnek bir alt ağda en az 16 IP adresine ihtiyaç duyuyor ve en fazla 256 IP adresi kullanıyor olabilir. Sonuç olarak, alt ağ IP aralıklarını tanımlarken/28 ile/24 arasında bir alt ağ maskesi kullanabilirsiniz. Ağ maskesi biti/28 (ağ başına 14 ana bilgisayar), tek bir genel amaç veya iş açısından kritik dağıtım için iyi bir boyutdir. Bir maske bit/27 (ağ başına 30 ana bilgisayar), aynı VNet içindeki birden çok yönetilen örnek dağıtımı için idealdir. Maske bit ayarları/26 (62 ana bilgisayar) ve/24 (254 ana bilgisayar), ek yönetilen örnekleri desteklemek için VNet 'ten daha fazla ölçeklendirme yapılmasına izin verir.

> [!IMPORTANT]
> 16 IP adresi olan bir alt ağ boyutu, sanal çekirdek boyutu değişikliği gibi bir ölçeklendirme işleminin desteklenmediği sınırlı potansiyel olan en düşük gereksinimdir. Önek/27 veya en uzun ön ek olan alt ağ seçilmesi kesinlikle önerilir.

## <a name="determine-subnet-size"></a>Alt ağ boyutunu belirle

Alt ağ içinde birden fazla yönetilen örnek dağıtmayı planlıyorsanız ve alt ağ boyutunu iyileştirmeniz gerekiyorsa, bir hesaplama oluşturmak için bu parametreleri kullanın:

- Azure, alt ağda kendi ihtiyaçları için beş IP adresi kullanır
- Her bir Genel Amaçlı örneğinin iki adresi olmalıdır
- Her bir İş Açısından Kritik örneği için dört adres gerekir

**Örnek**: üç genel amaçlı ve Iki iş açısından kritik yönetilen örnek olduğunu planlayın. Bu, 5 + 3 * 2 + 2 * 4 = 19 IP adresiniz olması gerektiği anlamına gelir. IP aralıkları 2 ' nin gücüyle tanımlandığından, IP aralığı 32 (2 ^ 5) IP adresi olmalıdır. Bu nedenle, alt ağı/27 alt ağ maskesiyle ayırmanız gerekir.

> [!IMPORTANT]
> Yukarıda görüntülenecek hesaplama, daha fazla geliştirmelerle kullanım dışı olur.

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [yönetilen örnek nedir?](sql-database-managed-instance.md).
- [Yönetilen örnek Için bağlantı mimarisi](sql-database-managed-instance-connectivity-architecture.md)hakkında daha fazla bilgi edinin.
- [Yönetilen örnekleri dağıtacağınız VNET oluşturma](sql-database-managed-instance-create-vnet-subnet.md) konusuna bakın
- DNS sorunları için bkz. [özel DNS yapılandırma](sql-database-managed-instance-custom-dns.md)
