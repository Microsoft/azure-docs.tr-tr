---
title: HSM fiziksel güvenliği-Azure ayrılmış HSM | Microsoft Docs
description: Azure ayrılmış HSM cihazları hakkında bilgi, veri merkezlerinde fiziksel güvenlik
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 8437d12075a148a3e8062f62e195bc019d89a85b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606972"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Azure ayrılmış HSM fiziksel güvenliği

Azure adanmış HSM, anahtar depolamaya yönelik gelişmiş güvenlik gereksinimlerini karşılamanıza yardımcı olur. Müşterilerin ihtiyaçlarını karşılamak için tam yaşam döngüsü boyunca sıkı güvenlik uygulamalarının yönetiliyor olması.

## <a name="security-through-procurement"></a>Tedarik üzerinden güvenlik

Microsoft, güvenli bir tedarik sürecini takip eder. Koruyucu zincirini yönetiyoruz ve sipariş edilen ve sevk edilen belirli bir cihazın veri merkezlerimize ulaşan cihaz olduğundan emin olun. Cihazlar, serileştirilmiş ve olay Plastik plastik ve kapsayıcılardır. Bunlar veri merkezinin veri galerisinde, güvenli bir depolama alanında depolanır.  HSM cihazlarını içeren raflar, yüksek iş etkisi (HBı) olarak değerlendirilir. Cihazlar kilitlenir ve her zaman önde ve geri doğru video gözetimi altında.

## <a name="security-through-deployment"></a>Dağıtım üzerinden güvenlik

HSM 'ler, ilişkili ağ bileşenleriyle birlikte raflara yüklenir. Yüklendikten sonra, Azure ayrılmış HSM hizmeti 'nin bir parçası olarak kullanılabilir hale getirilmeleri için yapılandırılması gerekir. Bu yapılandırma etkinliği, bir arka plan denetimi gerçekleştirilen Microsoft çalışanları tarafından gerçekleştirilir. "Tam zamanında" (JıT) yönetimi, erişimi yalnızca doğru çalışanlara ve yalnızca erişimin gerekli olduğu zamana göre sınırlamak için kullanılır. Kullanılan yordamlar ve sistemler ayrıca HSM cihazlarıyla ilgili tüm etkinliklerin günlüğe kaydedildiğinden emin olmanızı sağlar.

## <a name="security-in-operations"></a>İşlemlerde güvenlik

HSM 'ler, donanım gereçlerini (gerçek HSM 'nin gereç içindeki bir PCI kartı olması), bu sayede bileşen düzeyi sorunları ortaya çıkabilir. Olası sorunlar şunlardır, ancak fan ve güç kaynağı hatalarıyla sınırlı değildir. Bu tür bir olay, tüm takas özellikli bileşenleri değiştirecek bakım veya onarım etkinlikleri gerektirir.

### <a name="component-replacement"></a>Bileşen değiştirme

Bir cihaz sağlandıktan ve müşteri yönetimi altına alındıktan sonra, değiştirilebilir güç kaynağı, değiştirilebilen tek bileşendir. Bu bileşen güvenlik sınırının dışında ve bir yetkisiz olay oluşmasına neden olmaz. Bir Microsoft mühendisinin HBı rafın sonuna erişmesini yetkilendirmek için bir bilet oluşturma sistemi kullanılır. Bilet işlendiğinde geçici bir fiziksel anahtar verilir. Bu anahtar, cihaza mühendise erişim sağlar ve etkilenen bileşeni takas etmesine izin verir. Bir cihaz müşteriye ayrılmadığı zaman, güvenlik ve kullanılabilirlik riskini en aza indirmek için başka bir erişim (yani, soruna neden olan) yapılır.  

### <a name="device-replacement"></a>Cihaz değiştirme

Toplam cihaz arızası durumunda, bileşen hatası sırasında kullanılanlara benzer bir işlem izlenir. Bir müşteri, cihazı sıfırlama yapamaz veya cihaz bilinmeyen bir durumdaysa, cihazları çıkarılan veriler kaldırılır ve raf dışı bir yok etme sepetine yerleştirilir. Bin içine yerleştirilmiş cihazlar denetimli ve güvenli bir şekilde yok edilir. Bir HBI rafınızdan cihaz içermeyen hiçbir veri, bir Microsoft veri merkezinde kalacak.

### <a name="other-rack-access-activities"></a>Diğer raf erişimi etkinlikleri

Bir Microsoft mühendisinin HSM cihazları tarafından kullanılan rafa erişmesi gerekiyorsa (örneğin, ağ cihazı bakımı), HBı güvenli rafa erişim kazanmak için standart güvenlik yordamları kullanılacaktır. Tüm erişim, video gözetimi kapsamında olacaktır. HSM cihazları [fıps 140-2 düzey 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) ' e onaylanır, bu nedenle HSM cihazlarına yönelik tüm yetkisiz erişimlerden biri müşteriye bildirilecektir ve veriler sıfır olarak ayarlanır.

## <a name="logical-level-security-considerations"></a>Mantıksal düzey güvenlik konuları

HSM 'ler, müşterinin özel IP adresi alanındaki müşteri tarafından oluşturulan bir sanal ağ için sağlanır.  Bu yapılandırma, değerli bir mantıksal ağ düzeyi yalıtımı sağlar ve yalnızca müşteri tarafından erişim sağlar. Bu, tüm mantıksal düzey güvenlik denetimlerinin müşterinin sorumluluğu olduğunu gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Hizmetin yüksek kullanılabilirlik ve güvenlik ve desteklenebilirlik gibi tüm temel kavramlarının cihaz sağlama, uygulama tasarımı veya dağıtımdan önce iyi anlaşıldığından emin olmanız önerilir.

* [Yüksek kullanılabilirlik](high-availability.md)
* [Ağ](networking.md)
* [Desteklenebilirlik](supportability.md)
* [İzleme](monitoring.md)
* [Dağıtım mimarisi](deployment-architecture.md)
