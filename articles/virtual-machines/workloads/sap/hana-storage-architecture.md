---
title: Azure 'da SAP HANA depolama mimarisi (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA dağıtma (büyük örnekler) depolama mimarisi.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 256aaf94175394fd737e53c6281f2d8b45e8af41
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099641"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA (büyük örnekler) depolama mimarisi

Azure 'da SAP HANA için depolama düzeni (büyük örnekler), SAP tarafından önerilen yönergeler başına klasik dağıtım modelinde SAP HANA tarafından yapılandırılır. Yönergeler [SAP HANA depolama gereksinimleri](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) teknik incelemesi bölümünde belgelenmiştir.

T türü sınıfının HANA büyük örneği, bellek hacminin depolama birimi olarak dört kat gelir. HANA büyük örnek birimlerinin tür II sınıfı için, depolama dört kat daha fazla değildir. Birimler, HANA işlem günlüğü yedeklemelerini depolamak için tasarlanan bir birimle gelir. Daha fazla bilgi için bkz. [Azure 'da SAP HANA (büyük örnekler) yükleyip yapılandırma](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Depolama alanı ayırma açısından aşağıdaki tabloya bakın. Tablo, farklı HANA büyük örnek birimleri ile sunulan farklı birimler için kaba kapasiteyi listeler.

| HANA büyük örnek SKU 'SU | Hana/veri | Hana/günlük | Hana/paylaşılan | Hana/günlüğe yedeklemeler |
| --- | --- | --- | --- | --- |
| S72 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3\.328 GB | 768 GB |1\.280 GB | 768 GB |
| S96 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4\.608 GB | 1\.024 GB | 1\.536 GB | 1\.024 GB |
| S192m | 11.520 GB | 1\.536 GB | 1\.792 GB | 1\.536 GB |
| S192xm |  11.520 GB |  1\.536 GB |  1\.792 GB |  1\.536 GB |
| S384 | 11.520 GB | 1\.536 GB | 1\.792 GB | 1\.536 GB |
| S384m | 12.000 GB | 2\.050 GB | 2\.050 GB | 2\.040 GB |
| S384xm | 16.000 GB | 2\.050 GB | 2\.050 GB | 2\.040 GB |
| S384xxm |  20.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S576m | 20.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S576xm | 31.744 GB | 4\.096 GB | 2\.048 GB | 4\.096 GB |
| S768m | 28.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S768xm | 40.960 GB | 6\.144 GB | 4\.096 GB | 6\.144 GB |
| S960m | 36.000 GB | 4\.100 GB | 2\.050 GB | 4\.100 GB |


Dağıtılan gerçek birimler, dağıtıma ve birim boyutlarını göstermek için kullanılan araca göre farklılık gösterebilir.

Bir HANA büyük örnek SKU 'sunu bölümlere ayırmak istiyorsanız olası bölüm parçalarının birkaç örneği şöyle görünebilir:

| GB cinsinden bellek bölümü | Hana/veri | Hana/günlük | Hana/paylaşılan | Hana/günlük/yedekleme |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1\.792 GB | 640 GB | 1\.024 GB | 640 GB |
| 1536 | 3\.328 GB | 768 GB | 1\.280 GB | 768 GB |


Bu boyutlar, dağıtıma ve birimlere bakmak için kullanılan araçlara göre değişebilen kabaca birim numaralarıdır. Ayrıca, 2,5 TB gibi başka bölüm boyutları da mevcuttur. Bu depolama boyutları, önceki bölümlerde kullanılan bir formülle benzer bir formül ile hesaplanır. "Partitions" terimi, işletim sisteminin, belleğin veya CPU kaynaklarının bölümlenmiş herhangi bir şekilde bölümlenmiş anlamına gelmez. Tek bir HANA büyük örnek biriminde dağıtmak isteyebileceğiniz farklı HANA örneklerinin depolama bölümlerini belirtir. 

Daha fazla depolama alanı gerekebilir. Ek depolamayı 1 TB 'lik birimlerde satın alarak depolama alanı ekleyebilirsiniz. Bu ek depolama alanı, ek birim olarak eklenebilir. Ayrıca, mevcut birimlerden bir veya daha fazlasını genişletmek için de kullanılabilir. Birim boyutları, ilk dağıtıldığı ve genellikle önceki tablolar tarafından belgelendiği için azalabilir. Ayrıca, birimlerin adlarını veya takma adlarını değiştirmek mümkün değildir. Daha önce açıklanan depolama birimleri, NFS4 birimleri olarak HANA büyük örnek birimlerine eklenir.

Yedekleme ve geri yükleme ve olağanüstü durum kurtarma amacıyla depolama anlık görüntülerini kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure 'da SAP HANA (büyük örnekler) yüksek kullanılabilirlik ve olağanüstü durum kurtarma](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Senaryonuza yönelik depolama düzeni ayrıntıları için [HLI desteklenen senaryolar](hana-supported-scenario.md) bölümüne bakın.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Birden çok SAP HANA örneğini tek bir HANA büyük örnek biriminde Çalıştır

HANA büyük örnek birimlerinde birden fazla etkin SAP HANA örneği barındırmak mümkündür. Depolama anlık görüntülerinin ve olağanüstü durum kurtarmanın yeteneklerini sağlamak için, bu tür bir yapılandırma, örnek başına bir birim kümesi gerektirir. Şu anda, HANA büyük örnek birimleri şu şekilde alt ayrılabilir:

- **S72, S72m, S96, S144, S192**: 256 GB 'lik artışlarla, 256 GB, en küçük başlangıç birimidir. 256 GB ve 512 GB gibi farklı Artımlar birimin en fazla bellek ile birleştirilebilir.
- **S144m ve S192m**: 256 GB 'lik artışlarla, 512 GB en küçük birimdir. 512 GB ve 768 GB gibi farklı Artımlar birimin en fazla bellek ile birleştirilebilir.
- **Tür II sınıfı**: En küçük başlangıç birimi olan 2 TB 'lik artışlarla 512 GB. 512 GB, 1 TB ve 1,5 TB gibi farklı artışlarla birimin en fazla bellek ile birleştirilebilir.

Birden çok SAP HANA örneği çalıştırmanın birkaç örneği aşağıdaki gibi görünebilir.

| SKU | Bellek boyutu | Depolama boyutu | Birden çok veritabanı içeren Boyutlar |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768-GB HANA örneği<br /> veya 1x512 GB örnek + 1x256 GB örnek<br /> veya 3x256 GB örnek | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA örnekleri<br />veya 1x512-GB örnek + 1x1-TB örnek<br />veya 6x256 GB örnek<br />veya 1x 1.5-TB örnek | 
| S192m | 4 TB | 16 TB | 8x512 GB örnek<br />ya da 4x1-TB örnekleri<br />ya da 4x512 GB örnek + 2x1-TB örnekleri<br />ya da 4x768-GB örnek + 2x512 GB örnek<br />veya 1x4-TB örnek |
| S384xm | 8 TB | 22 TB | 4x2-TB örnekleri<br />veya 2x4-TB örnekleri<br />veya 2x3-TB örnek + 1x2-TB örnekleri<br />veya 2x 2.5-TB örnek + 1x3-TB örnekleri<br />veya 1x8 TB örneği |


Başka Çeşitlemeler de vardır. 

## <a name="encryption-of-data-at-rest"></a>Bekleyen veri şifrelemesi
HANA büyük örnek için kullanılan depolama, 2018 yılı bitmesinden bu yana disklerde depolanan veriler için saydam şifrelemeyi kullanır. Önceki dağıtımlarda birimleri şifrelenmiş olarak almayı seçebilirsiniz. Bu seçeneğe karşı karar verirseniz, çevrimiçi olarak şifrelenen birimleri almayı isteyebilirsiniz. Şifrelenmeyen şifreli birimlerden geçiş işlemi saydamdır ve kapalı kalma süresi gerektirmez. 

SKU 'ların tür ı sınıfı ile, önyükleme LUN ' de depolanan birim şifrelenir. 3\. düzeltme 3 HANA büyük örnek Damgalarında, HANA büyük örnek SKU 'Larının tür II sınıfını kullanarak önyükleme LUN 'unu OS yöntemleriyle şifrelemeniz gerekir. Düzeltme 4 HANA büyük örnek damgaları ' nda, tür II birimleri kullanılarak önyükleme LUN 'si depolanır ve varsayılan olarak geri kalanı şifrelenir. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>HANA büyük örneklerinde daha büyük HANA örnekleri için gerekli ayarlar
HANA büyük örneklerde kullanılan depolamanın dosya boyutu sınırlaması vardır. [Boyut sınırlaması dosya başına 16 TB 'tır](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) . EXT3 dosya sistemlerindeki dosya boyutu sınırlamalarından farklı olarak, HANA, HANA büyük örnekler depolaması tarafından zorlanan depolama sınırlamasını açıkça bilmez. Sonuç olarak, 16 TB dosya boyutu sınırına ulaşıldığında HANA otomatik olarak yeni bir veri dosyası oluşturmaz. HANA, 16 TB 'ın ötesinde dosyayı büyümeye çalışır, HANA hataları rapor eder ve dizin sunucusu sonda çökecektir.

> [!IMPORTANT]
> Hana büyük örnek depolama alanının 16 TB 'lık dosya boyutu sınırının ötesinde veri dosyalarını büyümeye çalışmasını engellemek için, HANA 'nın Global. ini yapılandırma dosyasında aşağıdaki parametreleri ayarlamanız gerekir.
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - Ayrıca bkz. SAP Note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285) farkında olun




**Sonraki adımlar**
- [Hana büyük örnekleri Için desteklenen senaryolara](hana-supported-scenario.md) bakın