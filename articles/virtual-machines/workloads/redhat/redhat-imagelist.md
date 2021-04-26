---
title: Azure 'da kullanılabilen görüntüleri Red Hat Enterprise Linux
description: Microsoft Azure Red Hat Enterprise Linux görüntüleri hakkında bilgi edinin
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 0a4d2ef5b7f367130151fabda3f1d97b65605931
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676037"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure 'da bulunan Red Hat Enterprise Linux (RHEL) görüntüleri
Azure, farklı kullanım durumları için çeşitli RHEL görüntüleri sunmaktadır.

> [!NOTE]
> Tüm RHEL görüntüleri Azure genel ve Azure Kamu bulutlarında kullanılabilir. Azure Çin bulutlarında mevcut değildir.

## <a name="list-of-rhel-images"></a>RHEL görüntülerinin listesi
Bu, Azure 'da kullanılabilen RHEL görüntülerinin bir listesidir. Aksi belirtilmedikçe, tüm görüntüler LVM bölümlidir ve normal RHEL depolarına (E4S değil) eklenir. Aşağıdaki görüntüler şu anda genel kullanım için kullanılabilir:

> [!NOTE]
> Ham görüntüler, LVM bölümlenmiş görüntülerin yerine artık üretilmez. LVM, önemli ölçüde daha esnek bölüm yeniden boyutlandırma seçenekleri de dahil olmak üzere eski ham (LVM olmayan) bölümleme şemasına birçok avantaj sağlar.

Sunduğu| SKU | Bölümleme | Sağlama | Notlar
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | Madde    | Linux Aracısı | 1 Aralık 'tan itibaren Uzatılmış yaşam döngüsü desteği sağlanıyor. [Burada daha fazla ayrıntı bulabilirsiniz.](redhat-extended-lifecycle-support.md)
|             | 6.8      | Madde    | Linux Aracısı | 1 Aralık 'tan itibaren Uzatılmış yaşam döngüsü desteği sağlanıyor. [Burada daha fazla ayrıntı bulabilirsiniz.](redhat-extended-lifecycle-support.md)
|             | 6,9      | Madde    | Linux Aracısı | 1 Aralık 'tan itibaren Uzatılmış yaşam döngüsü desteği sağlanıyor. [Burada daha fazla ayrıntı bulabilirsiniz.](redhat-extended-lifecycle-support.md)
|             | 6,10     | Madde    | Linux Aracısı | 1 Aralık 'tan itibaren Uzatılmış yaşam döngüsü desteği sağlanıyor. [Burada daha fazla ayrıntı bulabilirsiniz.](redhat-extended-lifecycle-support.md)
|             | 7-HAM    | Madde    | Linux Aracısı | RHEL 7. x resim ailesi. <br> Varsayılan olarak normal depolara eklenir (EUS değil).
|             | 7-LVM    | LVM    | Linux Aracısı | RHEL 7. x resim ailesi. <br> Varsayılan olarak normal depolara eklenir (EUS değil). Dağıtılacak standart bir RHEL görüntüsü arıyorsanız, bu görüntü kümesini ve/veya 2. kuşak karşılığı 'nı kullanın.
|             | 7lvm-Gen2| LVM    | Linux Aracısı | 2. nesil, RHEL 7. x resim ailesi. <br> Varsayılan olarak normal depolara eklenir (EUS değil). Dağıtılacak standart bir RHEL görüntüsü arıyorsanız, bu görüntü kümesini ve/veya 1. kuşak karşılığı kullanın.
|             | 7-RAW-CI | HAM-Cı | kabuğunuzda Cloud-init  | RHEL 7. x resim ailesi. <br> Varsayılan olarak normal depolara eklenir (EUS değil).
|             | 7.2      | Madde    | Linux Aracısı |
|             | 7.3      | Madde    | Linux Aracısı |
|             | 7.4      | Madde    | Linux Aracısı | 2019 Nisan itibariyle varsayılan olarak, EUS depolarına eklenmiş.
|             | 74-Gen2  | Madde    | Linux Aracısı | Varsayılan olarak, EUS depolarına eklendi.
|             | 7,5      | Madde    | Linux Aracısı | , Haziran 2019 itibariyle varsayılan olarak EUS depolarına eklendi.
|             | 75-Gen2  | Madde    | Linux Aracısı | Varsayılan olarak, EUS depolarına eklendi.
|             | 7.6      | Madde    | Linux Aracısı | Varsayılan olarak 2019 Mayıs itibariyle EUS depolarında kullanıma açıldı.
|             | 76-Gen2  | Madde    | Linux Aracısı | Varsayılan olarak, EUS depolarına eklendi.
|             | 7.7      | LVM    | Linux Aracısı | Varsayılan olarak, EUS depolarına eklendi.
|             | 77-Gen2  | LVM    | Linux Aracısı | Varsayılan olarak, EUS depolarına eklendi.
|             | 7.8      | LVM    | Linux Aracısı | Düzenli depolara ekli (RHEL 7,8 için kullanılamaz)
|             | 78-Gen2  | LVM    | Linux Aracısı | Düzenli depolara ekli (RHEL 7,8 için kullanılamaz)
|             | 7.9      | LVM    | Linux Aracısı | Düzenli depolara ekli (RHEL 7,9 için kullanılamaz)
|             | 79-Gen2  | LVM    | Linux Aracısı | Düzenli depolara ekli (RHEL 7,9 için kullanılamaz)
|             | 8-LVM    | LVM    | Linux Aracısı | RHEL 8. x resim ailesi. Düzenli depolara iliştirilir.
|             | 8-LVM-Gen2| LVM    | Linux Aracısı | Hyper-V 2. nesil-RHEL 8. x resim ailesi. Düzenli depolara iliştirilir.
|             | 8        | LVM    | Linux Aracısı | RHEL 8,0 görüntüleri.
|             | 8-Gen2   | LVM    | Linux Aracısı | Hyper-V 2. nesil-RHEL 8,0 görüntüleri.
|             | 8.1      | LVM    | Linux Aracısı | Varsayılan olarak, EUS depolarına eklendi.
|             | 81gen2   | LVM    | Linux Aracısı | Hyper-V 2. nesil-2020 Kasım itibariyle EUS depolarında kullanıma açıldı.
|             | 8,1-CI   | LVM    | Linux Aracısı | 2020 Kasım itibariyle EUS depolarına eklenmiş.
|             | 81-CI-Gen2| LVM    | Linux Aracısı | Hyper-V 2. nesil-2020 Kasım itibariyle EUS depolarında kullanıma açıldı.
|             | 8.2      | LVM    | Linux Aracısı | 2020 Kasım itibariyle EUS depolarına eklenmiş.
|             | 82gen2   | LVM    | Linux Aracısı | Hyper-V 2. nesil-2020 Kasım itibariyle EUS depolarında kullanıma açıldı.
|             | 8.3   | LVM    | Linux Aracısı |  Düzenli depolara ekli (RHEL 8,3 için kullanılamaz)
|             | 83-Gen2   | LVM    | Linux Aracısı |Hyper-V 2. nesil-normal depolara (RHEL 8,3 için kullanılamaz) eklendi
RHEL-SAP      | 7.4      | LVM    | Linux Aracısı | SAP HANA ve Iş uygulamaları için RHEL 7,4. E4S depolarına bağlı olarak SAP ve RHEL için bir Premium ve temel işlem ücreti de ücretlendirilir.
|             | 74sap-Gen2| LVM    | Linux Aracısı | SAP HANA ve Iş uygulamaları için RHEL 7,4. 2. nesil görüntü. E4S depolarına bağlı olarak SAP ve RHEL için bir Premium ve temel işlem ücreti de ücretlendirilir.
|             | 7,5       | LVM    | Linux Aracısı | SAP HANA ve Iş uygulamaları için RHEL 7,5. E4S depolarına bağlı olarak SAP ve RHEL için bir Premium ve temel işlem ücreti de ücretlendirilir.
|             | 75sap-Gen2| LVM    | Linux Aracısı | SAP HANA ve Iş uygulamaları için RHEL 7,5. 2. nesil görüntü. E4S depolarına bağlı olarak SAP ve RHEL için bir Premium ve temel işlem ücreti de ücretlendirilir.
|             | 7.6       | LVM    | Linux Aracısı | SAP HANA ve Iş uygulamaları için RHEL 7,6. E4S depolarına bağlı olarak SAP ve RHEL için bir Premium ve temel işlem ücreti de ücretlendirilir.
|             | 76sap-Gen2| LVM    | Linux Aracısı | SAP HANA ve Iş uygulamaları için RHEL 7,6. 2. nesil görüntü. E4S depolarına bağlı olarak SAP ve RHEL için bir Premium ve temel işlem ücreti de ücretlendirilir.
|             | 7.7       | LVM    | Linux Aracısı | SAP HANA ve Iş uygulamaları için RHEL 7,7. E4S depolarına bağlı olarak SAP ve RHEL için bir Premium ve temel işlem ücreti de ücretlendirilir.
RHEL-SAP-HANA (Kasım 2020 ' de kaldırılacak) | 6.7       | Madde    | Linux Aracısı | SAP HANA için RHEL 6,7. RHEL-SAP görüntülerinin kullanımı güncel değil. Bu resim, Kasım 2020 ' de kaldırılacak. Red Hat 'in SAP bulut teklifleri hakkında daha fazla ayrıntıya [buradan](https://access.redhat.com/articles/3751271)ulaşabilirsiniz.
|             | 7.2       | LVM    | Linux Aracısı | SAP HANA için RHEL 7,2. RHEL-SAP görüntülerinin kullanımı güncel değil. Bu resim, Kasım 2020 ' de kaldırılacak. Red Hat 'in SAP bulut teklifleri hakkında daha fazla ayrıntıya [buradan](https://access.redhat.com/articles/3751271)ulaşabilirsiniz.
|             | 7.3       | LVM    | Linux Aracısı | SAP HANA için RHEL 7,3. RHEL-SAP görüntülerinin kullanımı güncel değil. Bu resim, Kasım 2020 ' de kaldırılacak. Red Hat 'in SAP bulut teklifleri hakkında daha fazla ayrıntıya [buradan](https://access.redhat.com/articles/3751271)ulaşabilirsiniz.
RHEL-SAP-APPS | 6.8       | Madde    | Linux Aracısı | SAP Business Applications için RHEL 6,8. RHEL-SAP görüntülerinin kullanımı güncel değil.
|             | 7.3       | LVM    | Linux Aracısı | SAP Business Applications için RHEL 7,3. RHEL-SAP görüntülerinin kullanımı güncel değil.
|             | 7.4       | LVM    | Linux Aracısı | SAP Business Applications için RHEL 7,4.
|             | 7.6       | LVM    | Linux Aracısı | SAP Business Applications için RHEL 7,6.
|             | 7.7       | LVM    | Linux Aracısı | SAP Business Applications için RHEL 7,7.
|             | 77-Gen2       | LVM    | Linux Aracısı | SAP Business Applications için RHEL 7,7. 2. nesil görüntü
|             | 8.1       | LVM    | Linux Aracısı | SAP Business Applications için RHEL 8,1.
|             | 81-Gen2      | LVM    | Linux Aracısı | SAP Business Applications için RHEL 8,1. 2. nesil görüntü.
|             | 8.2       | LVM    | Linux Aracısı | SAP Business Applications için RHEL 8,2.
|             | 82-Gen2      | LVM    | Linux Aracısı | SAP Business Applications için RHEL 8,2. 2. nesil görüntü.
RHEL-HA       | 7.4       | LVM    | Linux Aracısı | HA eklentisi ile RHEL 7,4. Temel işlem ücretinin üzerine HA ve RHEL için Premium ücret alınacaktır. RHEL-SAP-HA görüntülerinin kullanımı güncel değildir.
|             | 7,5       | LVM    | Linux Aracısı | HA eklentisi ile RHEL 7,5. Temel işlem ücretinin üzerine HA ve RHEL için Premium ücret alınacaktır. RHEL-SAP-HA görüntülerinin kullanımı güncel değildir.
|             | 7.6       | LVM    | Linux Aracısı | HA eklentisi ile RHEL 7,6. Temel işlem ücretinin üzerine HA ve RHEL için Premium ücret alınacaktır. RHEL-SAP-HA görüntülerinin kullanımı güncel değildir.
RHEL-SAP-HA   | 7.4          | LVM    | Linux Aracısı | HA ve Update Services ile SAP için RHEL 7,4. E4S depolarına eklendi. SAP ve HA depoları ve RHEL 'nin yanı sıra temel işlem ücretleri üzerinde de Premium ücret ödeyecektir.
|             | 74sapha-Gen2 | LVM    | Linux Aracısı | HA ve Update Services ile SAP için RHEL 7,4. 2. nesil görüntü. E4S depolarına eklendi. SAP ve HA depoları ve RHEL 'nin yanı sıra temel işlem ücretleri üzerinde de Premium ücret ödeyecektir.
|             | 7,5          | LVM    | Linux Aracısı | HA ve Update Services ile SAP için RHEL 7,5. E4S depolarına eklendi. SAP ve HA depoları ve RHEL 'nin yanı sıra temel işlem ücretleri üzerinde de Premium ücret ödeyecektir.
|             | 7.6          | LVM    | Linux Aracısı | HA ve Update Services ile SAP için RHEL 7,6. E4S depolarına eklendi. SAP ve HA depoları ve RHEL 'nin yanı sıra temel işlem ücretleri üzerinde de Premium ücret ödeyecektir.
|             | 76sapha-Gen2 | LVM    | Linux Aracısı | HA ve Update Services ile SAP için RHEL 7,6. 2. nesil görüntü. E4S depolarına eklendi. SAP ve HA depoları ve RHEL 'nin yanı sıra temel işlem ücretleri üzerinde de Premium ücret ödeyecektir.
|             | 7.7          | LVM    | Linux Aracısı | HA ve Update Services ile SAP için RHEL 7,7. E4S depolarına eklendi. SAP ve HA depoları ve RHEL 'nin yanı sıra temel işlem ücretleri üzerinde de Premium ücret ödeyecektir.
|             | 77sapha-Gen2 | LVM    | Linux Aracısı | HA ve Update Services ile SAP için RHEL 7,7. 2. nesil görüntü. E4S depolarına eklendi. SAP ve HA depoları ve RHEL 'nin yanı sıra temel işlem ücretleri üzerinde de Premium ücret ödeyecektir.
|             | 8.1          | LVM    | Linux Aracısı | HA ve Update Services ile SAP için RHEL 8,1. E4S depolarına eklendi. SAP ve HA depoları ve RHEL 'nin yanı sıra temel işlem ücretleri üzerinde de Premium ücret ödeyecektir.
|             | 81sapha-Gen2          | LVM    | Linux Aracısı | HA ve Update Services ile SAP için RHEL 8,1. 2. nesil E4S depolarına ekli görüntüler. SAP ve HA depoları ve RHEL 'nin yanı sıra temel işlem ücretleri üzerinde de Premium ücret ödeyecektir.
|             | 8.2          | LVM    | Linux Aracısı | HA ve Update Services ile SAP için RHEL 8,2. SAP ve HA depoları ve RHEL 'nin yanı sıra temel işlem ücretleri üzerinde de Premium ücret ödeyecektir.
|             | 82sapha-Gen2          | LVM    | Linux Aracısı | HA ve Update Services ile SAP için RHEL 8,2. 2. nesil E4S depolarına ekli görüntüler. SAP ve HA depoları ve RHEL 'nin yanı sıra temel işlem ücretleri üzerinde de Premium ücret ödeyecektir.
RHEL-byos     |RHEL-lvm74| LVM    | Linux Aracısı | RHEL 7,4 BYOS görüntüleri, herhangi bir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm75| LVM    | Linux Aracısı | RHEL 7,5 BYOS görüntüleri, herhangi bir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm76| LVM    | Linux Aracısı | RHEL 7,6 BYOS görüntüleri, herhangi bir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm76-Gen2| LVM    | Linux Aracısı | RHEL 7,6 2. nesil KCG görüntüleri, hiçbir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm77| LVM    | Linux Aracısı | RHEL 7,7 BYOS görüntüleri, herhangi bir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm77-Gen2| LVM    | Linux Aracısı | RHEL 7,7 2. nesil KCG görüntüleri, hiçbir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm78| LVM    | Linux Aracısı | RHEL 7,8 BYOS görüntüleri, herhangi bir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm78-Gen2| LVM    | Linux Aracısı | RHEL 7,8 2. nesil KCG görüntüleri, hiçbir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm8 | LVM    | Linux Aracısı | RHEL 8,0 BYOS görüntüleri, herhangi bir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm8-Gen2 | LVM    | Linux Aracısı | RHEL 8,0 2. nesil KCG görüntüleri, hiçbir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm81 | LVM    | Linux Aracısı | RHEL 8,1 BYOS görüntüleri, herhangi bir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm81-Gen2 | LVM    | Linux Aracısı | RHEL 8,1 2. nesil KCG görüntüleri, hiçbir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm82 | LVM    | Linux Aracısı | RHEL 8,2 BYOS görüntüleri, herhangi bir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.
|             |RHEL-lvm82-Gen2 | LVM    | Linux Aracısı | RHEL 8,2 2. nesil KCG görüntüleri, hiçbir güncelleştirme kaynağına iliştirilmemiş bir RHEL Premium ücretlendirilmez.

> [!NOTE]
> RHEL-SAP-HANA ürün teklifi, Red Hat ile yaşam sonu olarak değerlendirilir. Mevcut dağıtımlar normal şekilde çalışmaya devam eder, ancak Red Hat müşterilerin RHEL-SAP-HANA görüntülerinden, SAP HANA depoları ve HA eklentisi de içeren RHEL-SAP-HA yansımalarına geçiş gerçekleştirmesini önerir. Red Hat 'in SAP bulut teklifleri hakkında daha fazla ayrıntıya [buradan](https://access.redhat.com/articles/3751271)ulaşabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure 'Da Red Hat görüntüleri](./redhat-images.md)hakkında daha fazla bilgi edinin.
* [Red Hat güncelleştirme altyapısı](./redhat-rhui.md)hakkında daha fazla bilgi edinin.
* [RHEL BYOS teklifi](./byos.md)hakkında daha fazla bilgi edinin.
* Tüm RHEL sürümleri için Red Hat destek ilkeleriyle ilgili bilgiler [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) sayfasında bulunabilir.
