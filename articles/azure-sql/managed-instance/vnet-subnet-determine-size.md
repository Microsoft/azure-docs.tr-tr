---
title: Gerekli alt ağ boyutunu & aralığı belirle
titleSuffix: Azure SQL Managed Instance
description: Bu konu başlığı altında, Azure SQL yönetilen örneğinin dağıtılacağı alt ağın boyutunun nasıl hesaplanacağı açıklanır.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012467"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği için gereken alt ağ boyutunu & aralığını belirleme
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL yönetilen örneğinin bir Azure [sanal ağı (VNet)](../../virtual-network/virtual-networks-overview.md)içinde dağıtılması gerekir.

Bir sanal ağın alt ağında dağıtılabilecek yönetilen örneklerin sayısı alt ağın boyutuna bağlıdır (alt ağ aralığı).

Yönetilen bir örnek oluşturduğunuzda Azure, sağlama sırasında seçtiğiniz katmana bağlı olarak bir dizi sanal makine ayırır. Bu sanal makineler alt ağınızda ilişkili olduğundan, IP adresleri gerektirir. Düzenli işlemler ve hizmet bakımı sırasında yüksek kullanılabilirlik sağlamak için Azure, ek sanal makineler ayırabilir. Sonuç olarak, bir alt ağdaki gerekli IP adreslerinin sayısı o alt ağdaki yönetilen örnek sayısından daha büyük.

Yönetilen örnek tasarım gereği alt ağda en az 32 IP adresine ihtiyaç duyar. Dolayısıyla alt ağ IP aralıklarınızı tanımlarken alt ağ maskesi değeri olarak en az /27 belirtmeniz gerekir. Yönetilen örnek dağıtımlarınızın alt ağ boyutunu dikkatli bir şekilde planlamanız önerilir. Planlama sırasında dikkate alınması gereken girişler şunlardır:

- Aşağıdaki örnek parametreleri de dahil olmak üzere yönetilen örnek sayısı:
  - hizmet katmanı
  - donanım oluşturma
  - Sanal çekirdek sayısı
- Ölçek artırma/azaltma veya hizmet katmanını değiştirme planları

> [!IMPORTANT]
> 16 IP adresi (alt ağ maskesi/28) olan bir alt ağ boyutu, yönetilen örneği bu içinde dağıtmaya izin verir, ancak yalnızca değerlendirme için kullanılan tek bir örneği dağıtmak için veya geliştirme/test senaryolarında kullanılması gerekir, bu da ölçeklendirme işlemlerinin gerçekleştirilmeyecektir.

## <a name="determine-subnet-size"></a>Alt ağ boyutunu belirle

Alt ağlarınızı gelecekteki örnek dağıtımına ve ölçekleme ihtiyaçlarına göre boyutlandırın. Aşağıdaki parametreler, bir hesaplama yaparken size yardımcı olabilir:

- Azure, alt ağda kendi ihtiyaçları için beş IP adresi kullanır
- Her sanal küme ek sayıda adres ayırır 
- Her yönetilen örnek, fiyatlandırma katmanına ve donanım oluşturmaya bağlı olan adres sayısını kullanır

> [!IMPORTANT]
> Alt ağda kaynak mevcut olması durumunda alt ağ adres aralığını değiştirmek mümkün değildir. Yönetilen örnekleri bir alt ağdan diğerine taşımak mümkün değildir. Mümkün olduğunda, gelecekteki sorunları engellemek için lütfen daha büyük alt ağları kullanmayı deneyin.

GP = genel amaçlı; BC = iş açısından kritik; VC = sanal küme

| **Donanım genel** | **Fiyatlandırma katmanı** | **Azure kullanımı** | **VC kullanımı** | **Örnek kullanımı** | **Toplamda*** |
| --- | --- | --- | --- | --- | --- |
| 4. nesil | GP | 5 | 1 | 5 | 11 |
| 4. nesil | BC | 5 | 1 | 5 | 11 |
| 5. nesil | GP | 5 | 6 | 3 | 14 |
| 5. nesil | BC | 5 | 6 | 5 | 16 |

  \* Toplam sütun alt ağda dağıtıldığında gerçekleştirilecek adreslerin sayısını görüntüler. Alt ağdaki her ek örnek, örnek kullanım sütunuyla temsil edilen adres sayısını ekler. Azure kullanımı sütunuyla temsil edilen adresler birden çok sanal kümede paylaşılır, ancak VC kullanımı sütunuyla temsil edilen adresler bu sanal kümeye yerleştirilmiş örneklerle paylaşılır.

Güncelleştirme işlemi genellikle sanal küme yeniden boyutlandırmayı gerektirir. Bazı durumlarda, güncelleştirme işlemi sanal küme oluşturmayı gerektirir (daha fazla ayrıntı için [yönetim işlemleri makalesini](sql-managed-instance-paas-overview.md#management-operations)inceleyin). Sanal küme oluşturulması durumunda, gereken ek adres sayısı, bu sanal kümede (örnek kullanım sütunu) yer alan örnekler için gereken adreslerle birlikte toplanan VC kullanım sütunuyla temsil edilen adres sayısına eşittir.

**Örnek 1**: bir genel amaçlı yönetilen örnek (4. nesil donanım) ve bir iş açısından kritik yönetilen örnek (5. nesil donanım) olduğunu planlayın. Bu, dağıtabilmeniz için en az 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 IP adresiniz olması gerektiği anlamına gelir. 2 ' nin gücüyle IP aralıkları tanımlandığından, alt ağınız bu dağıtım için minimum IP aralığı olan 32 ' i (2 ^ 5) gerektirir.<br><br>
Yukarıda belirtildiği gibi, bazı durumlarda güncelleştirme işlemi sanal küme oluşturmayı gerektirir. Bu, örneğin, sanal küme oluşturma gerektiren 5. nesil iş açısından kritik yönetilen örnek için bir güncelleştirme olması durumunda ek 6 + 5 = 11 IP adresiniz olması gerektiği anlamına gelir. Zaten 22 ' nin 32 adresini kullandığından, bu işlem için kullanılabilir bir adres yok. Bu nedenle, alt ağı/26 alt ağ maskesiyle ayırmanız gerekir (64 adres).

**Örnek 2**: aynı alt ağa yerleştirilmiş üç genel amaçlı (5. nesil donanım) ve iş açısından kritik yönetilen örnek (5. nesil donanım) olduğunu planlayın. Bu, 5 + 6 + 3 * 3 + 2 * 5 = 30 IP adresiniz olması gerektiği anlamına gelir. Bu nedenle, alt ağı/26 alt ağ maskesiyle ayırmanız gerekir. /27 alt ağ maskesini seçmek, kalan adres sayısının 2 (32 – 30) olmasına neden olur. Bu, örnek ölçeklendirmeyi gerçekleştirmek için alt ağda ek adresler gerekli olduğundan tüm örnekler için güncelleştirme işlemlerini önler.

**Örnek 3**: bir genel amaçlı yönetilen örneği (5. nesil donanım) ve zaman zaman, sanal çekirdekler güncelleştirme işlemini gerçekleştirmenizi planlarsınız. Bu, 5 + 6 + 1 * 3 + 3 = 17 IP adresiniz olması gerektiği anlamına gelir. IP aralıkları 2 ' nin gücüyle tanımlandığından, IP aralığı 32 (2 ^ 5) IP adresi olmalıdır. Bu nedenle, alt ağı/27 alt ağ maskesiyle ayırmanız gerekir.

### <a name="address-requirements-for-update-scenarios"></a>Güncelleştirme senaryoları için Adres gereksinimleri

Ölçeklendirme işlemi örnekleri sırasında, fiyatlandırma katmanına ve donanım oluşturmaya bağlı ek IP kapasitesi geçici olarak gerekir

| **Donanım genel** | **Fiyatlandırma katmanı** | **Senaryo** | **Ek adresler*** |
| --- | --- | --- | --- |
| 4. nesil | GP veya BC | Sanal çekirdekleri ölçeklendirme | 5 |
| 4. nesil | GP veya BC | Depolamayı ölçeklendirme | 5 |
| 4. nesil | GP veya BC | GP 'den BC veya BC 'e geçiş yapma | 5 |
| 4. nesil | GP | 5. nesil * öğesine geçme | 9 |
| 4. nesil | BC | 5. nesil * öğesine geçme | 11 |
| 5. nesil | GP | Sanal çekirdekleri ölçeklendirme | 3 |
| 5. nesil | GP | Depolamayı ölçeklendirme | 0 |
| 5. nesil | GP | BC 'a geçme | 5 |
| 5. nesil | BC | Sanal çekirdekleri ölçeklendirme | 5 |
| 5. nesil | BC | Depolamayı ölçeklendirme | 5 |
| 5. nesil | BC | GP 'e geçiş yapma | 3 |

  \* 4. nesil donanım kullanıma alınıyor ve Yeni dağıtımlar için artık kullanılamıyor. 5. nesil donanım oluşturmaya özgü özelliklerden yararlanmak için 4. nesil 'ten 5. nesil 'e donanım oluşturmayı güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [Azure SQL yönetilen örneği nedir?](sql-managed-instance-paas-overview.md).
- [SQL yönetilen örneği için bağlantı mimarisi](connectivity-architecture-overview.md)hakkında daha fazla bilgi edinin.
- Bkz. [SQL yönetilen örneği dağıtacağınız sanal ağ oluşturma](virtual-network-subnet-create-arm-template.md).
- DNS sorunları için bkz. [Özel BIR DNS yapılandırma](custom-dns-configure.md).
