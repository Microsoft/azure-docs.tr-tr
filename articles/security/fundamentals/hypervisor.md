---
title: Azure filo-Azure güvenliği üzerinde hiper yönetici güvenliği
description: Azure Fleet 'de hiper yönetici güvenliğine teknik genel bakış.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 766266edd663b75b893a5883e30bb48eed7bbfdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696125"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Azure filo 'de hiper yönetici güvenliği

Azure hiper yönetici sistemi, Windows Hyper-V ' d i temel alır. Hiper yönetici sistemi, bilgisayar yöneticisinin ayrı adres alanları olan konuk bölümleri belirtmesini sağlar. Ayrı adres alanları, bilgisayarın kök bölümünde çalıştırılan (ana bilgisayar) işletim sisteminin paralel olarak çalışan bir işletim sistemini ve uygulamaları yüklemeye olanak tanır. Konak işletim sistemi (ayrıcalıklı kök bölüm olarak da bilinir), sistemdeki tüm fiziksel cihazlara ve çevre birimlerine (depolama denetleyicileri, ağ uyarlamalar) doğrudan erişim sağlar. Konak işletim sistemi, Konuk bölümlerin her konuk bölümüne "sanal cihazlar" sunarak bu fiziksel cihazların kullanımını paylaşmasına izin verir. Bu nedenle, bir Konuk bölümünde çalıştırılan bir işletim sistemi, Sanallaştırma Hizmetleri tarafından sağlanmış olan sanallaştırılmış çevresel cihazlara, kök bölümünde yürütülen bir erişim sağlar.

Azure Hiper Yöneticisi, aşağıdaki güvenlik hedeflerini göz önünde bulundurarak oluşturulmuştur:

| Amaç | Kaynak |
|--|--|
| Yalıtım | Bir güvenlik ilkesi, VM 'Ler arasında hiçbir bilgi aktarımı yok. Bu kısıtlama, belleğin, cihazların, ağın ve kalıcı veriler gibi yönetilen kaynakların yalıtımına yönelik Virtual Machine Manager (VMM) ve donanımda yetenek gerektirir. |
| VMM bütünlüğü | Genel sistem bütünlüğünü sağlamak için, tek tek hiper yönetici bileşenlerinin bütünlüğü oluşturulur ve korunur. |
| Platform bütünlüğü | Hiper yöneticinin bütünlüğü, temel aldığı donanım ve yazılımların bütünlüğüyle farklılık gösterir. Hiper yönetici platformun bütünlüğü üzerinde doğrudan denetime sahip olmasa da, Azure temel platform bütünlüğünü korumak ve algılamak için [Cerberus](project-cerberus.md) yonga gibi donanım ve bellenim mekanizmalarını kullanır. Platform bütünlüğü tehlikeye girerse VMM ve konukların çalışması engellenir. |
| Kısıtlı erişim | Yönetim işlevleri yalnızca güvenli bağlantılar üzerinden bağlanmış yetkili yöneticiler tarafından uygulanır. En az ayrıcalık ilkesi, Azure rol tabanlı erişim denetimi (Azure RBAC) mekanizmaları tarafından zorlanır. |
| Denetim | Azure, daha sonra incelenebilmesi için bir sistemde ne olacağı hakkında verileri yakalamak ve korumak için denetim yeteneği sağlar. |

Microsoft 'un Azure Hiper Yöneticisi 'ni sağlamlaştırma yaklaşımı ve sanallaştırma alt sistemi aşağıdaki üç kategoriye ayrılabilir.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>Hiper yönetici tarafından zorlanan, kesin olarak tanımlanmış güvenlik sınırları

Azure Hiper Yöneticisi arasında birden çok güvenlik sınırı uygular:

- Sanallaştırılmış "Konuk" bölümler ve ayrıcalıklı bölüm ("ana bilgisayar")
- Birden çok Konuk
- Kendisi ve ana bilgisayar
- Kendisini ve tüm konukları

Gizlilik, bütünlük ve kullanılabilirlik hiper yönetici güvenlik sınırları için garanti edilir. Sınırlar, yan kanallı bilgi sızıntıları, hizmet reddi ve ayrıcalık yükselmesi gibi bir dizi saldırıyı savunmakta.

Hiper yönetici güvenlik sınırı, ağ trafiği, sanal aygıtlar, depolama alanı, işlem kaynakları ve diğer tüm VM kaynakları için kiracılar arasında segmentleme de sağlar.

## <a name="defense-in-depth-exploit-mitigations"></a>Derinlemesine savunma güvenlik açığı azaltmaları

Büyük olasılıkla olayda güvenlik sınırının bir güvenlik açığı vardır; Azure Hiper Yöneticisi, aşağıdakiler dahil olmak üzere birden çok azaltıcı etken katmanı içerir:

- VM 'ler arası bileşenleri barındıran ana bilgisayar tabanlı işlemin yalıtımı
- Güvenli bir dünyanın Kullanıcı ve çekirdek modu bileşenlerinin bütünlüğünü sağlamaya yönelik sanallaştırma tabanlı güvenlik (VBS)
- Birden çok güvenlik açığı düzeyi azaltmaları. Adres alanı düzeni rastgele seçme (ASLR), Veri Yürütme Engellemesi (DEP), rastgele kod koruyucusu, denetim akışı bütünlüğü ve veri bozulması engellemesi gibi azaltmalar dahildir
- Derleyici düzeyinde yığın değişkenlerinin otomatik olarak başlatılması
- Hyper-V tarafından oluşturulan çekirdek yığın ayırmalarını otomatik olarak Sıfırla başlatması yapan çekirdek API 'Leri

Bu hafifletmelerde, bir VM 'ler arası güvenlik açığı için bir yararlanma geliştirmesi sağlamak üzere tasarlanmıştır.

## <a name="strong-security-assurance-processes"></a>Güçlü güvenlik güvencesi süreçlerini

Hiper yöneticiyle ilgili saldırı yüzeyi, yazılım ağını, sanal cihazları ve tüm VM 'ler arası yüzeyleri içerir. Saldırı yüzeyi, düzenli güvenlik incelemelerini tetikleyen otomatik derleme tümleştirmesi aracılığıyla izlenir.

Tüm VM saldırı yüzeyleri tehdit modellenir, kod incelenir, belirsizlik ve güvenlik sınırı ihlalleri için kırmızı takımımız tarafından test edilmiştir. Microsoft, Microsoft Hyper-V için uygun ürün sürümlerindeki ilgili güvenlik açıklarına yönelik bir ödül ödeyen bir [hata sıçrama programı](https://www.microsoft.com/msrc/bounty-hyper-v) içerir.

> [!NOTE]
> Hyper-V ' d i [güçlü güvenlik güvencesi işlemleriyle](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) ilgili daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar
Platform bütünlüğünü ve güvenliğini sağlamak için yaptığımız hakkında daha fazla bilgi edinmek için bkz.:

- [Üretici yazılımı güvenliği](firmware.md)
- [Güvenli önyükleme](secure-boot.md)
- [Ölçülen önyükleme ve konak kanıtlama](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Bekleme sırasında şifreleme](encryption-atrest.md)