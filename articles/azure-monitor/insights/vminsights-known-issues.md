---
title: VM'ler için Azure İzleyici (Önizleme) bilinen sorunlar | Microsoft Docs
description: Bu makalede, Azure 'da sistem durumunu, uygulama bağımlılığı bulmayı ve Azure VM işletim sisteminin performans izlemesini birleştiren VM'ler için Azure İzleyici, Azure 'daki bir çözüm olan bilinen sorunlar ele alınmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.openlocfilehash: f6719a8c28571faceb6ebad0567d13a4edc60fe6
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553769"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>VM'ler için Azure İzleyici ile ilgili bilinen sorunlar (Önizleme)

Bu makalede, Azure 'da sistem durumunu, uygulama bileşenlerini bulmayı ve Azure VM işletim sisteminin performansını izlemeyi birleştiren VM'ler için Azure İzleyici, Azure 'daki bir çözüm olan bilinen sorunlar ele alınmaktadır. 

## <a name="health"></a>Sağlık 
Aşağıda durum özelliğinin geçerli sürümüyle ilgili bilinen sorunlar verilmiştir:

- Bir Azure VM kaldırılırsa veya silinirse, bir süre için VM listesi görünümünde görüntülenir. Ayrıca, kaldırılan veya silinen bir VM 'nin durumuna tıkladığınızda **sistem durumu tanılama** görünümü açılır ve sonra bir yükleme döngüsü başlatılır. Silinen VM 'nin adını seçtiğinizde VM 'nin silindiğini belirten bir ileti içeren bir bölme açılır.
- Bir eşiği güncelleştirme gibi yapılandırma değişiklikleri, portal veya Iş yükü Izleyicisi API 'sinin hemen güncelleştirilemeyebilir, 30 dakikaya kadar sürer. 
- Durum tanılama deneyimi diğer görünümlerden daha hızlı güncelleştirilir. Aralarında geçiş yaptığınızda bilgiler gecikiyor olabilir. 
- Linux VM 'Ler için, tek bir VM görünümüne yönelik durum ölçütlerini listeleme sayfasının başlığı, Kullanıcı tanımlı VM adı yerine sanal makinenin tüm etki alanı adına sahiptir. 
- Desteklenen yöntemlerden birini kullanarak bir VM için izlemeyi devre dışı bıraktıktan sonra yeniden dağıtmayı denerseniz, aynı çalışma alanında dağıtmanız gerekir. Farklı bir çalışma alanı seçer ve bu VM 'nin sistem durumunu görüntülemeyi denerseniz, bu durum tutarsız bir davranış gösterebilir.
- Çalışma alanınızdan çözüm bileşenlerini kaldırdıktan sonra, Azure sanal makinelerinizden sistem durumunu görmeye devam edebilirsiniz; Portalda herhangi bir görünüme gittiğinizde özellikle performans ve eşleme verileri. Veriler bir süre sonra performans ve harita görünümünden görünmeye devam eder; Ancak sistem durumu görünümü sanal makinelerinizin sistem durumunu göstermeye devam edecektir. **Şimdi dene** seçeneği yalnızca performans ve eşleme görünümlerinden yeniden eklenebilir.

## <a name="next-steps"></a>Sonraki adımlar
Sanal makinelerinizin izlenmesini etkinleştirmeye yönelik gereksinimleri ve yöntemleri anlamak için, [VM'ler için Azure izleyici etkinleştir](vminsights-enable-overview.md)' i gözden geçirin.
