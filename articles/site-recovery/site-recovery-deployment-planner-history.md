---
title: Azure Site Recovery Dağıtım Planlayıcısı sürümü geçmişi
description: Bilinen farklı Site Recovery Dağıtım Planlayıcısı sürüm düzeltmeleri ve bilinen sınırlamalar, sürüm tarihleriyle birlikte.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433420"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure Site Recovery Dağıtım Planlayıcısı sürümü geçmişi

Bu makalede, tüm Azure Site Recovery Dağıtım Planlayıcısı sürümlerinin yanı sıra düzeltmeler, her biri ve bunların yayın tarihlerinde bilinen sınırlamaları sunulmaktadır.

## <a name="version-251"></a>Sürüm 2,51

**Yayın tarihi: 22 Ağustos 2019**

**Düzeltilen**

- Dağıtım Planlayıcısı sürüm 2,5 ile ilgili maliyet önerisi sorunu düzeltildi

## <a name="version-25"></a>Sürüm 2,5

**Yayın tarihi: 29 Temmuz 2019**

**Düzeltilen**

- VMware sanal makineleri ve fiziksel makineler için, öneriler yönetilen disklere çoğaltmaya göre olacak şekilde güncelleştirilir.
- Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 veya üzeri için destek eklendi

## <a name="version-24"></a>Sürüm 2,4

**Yayın tarihi: 17 Nisan 2019**

**Düzeltilen**

- Özellikle yerelleştirme tabanlı hataları işlerken geliştirilmiş işletim sistemi uyumluluğu.
- Uyumluluk denetim listelerine en fazla 20 Mbps veri değişim oranı (dalgalanma) içeren VM 'Ler eklendi.
- Geliştirilmiş hata iletileri
- VCenter 6,7 için destek eklendi.
- Windows Server 2019 ve Red Hat Enterprise Linux (RHEL) iş istasyonu için destek eklendi.



## <a name="version-23"></a>Sürüm 2,3

**Yayın tarihi: 3 Aralık 2018**

**Düzeltilen**

- Dağıtım Planlayıcısı, belirtilen hedef konum ve abonelikle rapor oluşturmasını önleyen bir sorun düzeltildi.

## <a name="version-22"></a>Sürüm 2,2 

**Yayın tarihi: 25 Nisan 2018**

**Düzeltilen**

- GetVMList işlemleri:
  - Belirtilen klasör mevcut değilse GetVMList 'in başarısız olmasına neden olan bir sorun düzeltildi. Artık varsayılan dizini oluşturur ya da ÇıktıDosyası parametresinde belirtilen dizini oluşturur.
  - GetVMList için daha ayrıntılı hata nedenleri eklendi.
- Dağıtım Planlayıcısı raporun uyumlu VM 'Leri sayfasında VM türü bilgileri bir sütun olarak eklendi.
- Hyper-V-Azure olağanüstü durum kurtarma:
  - Paylaşılan VHD 'Ler ve profil oluşturma işleminden geçiş diskleri olan VM 'Ler dışlandı. Startprofil oluşturma işlemi, konsolunda dışlanan VM 'lerin listesini gösterir.
  - Uyumsuz VM 'Ler listesine 64 'den fazla diski olan VM 'Ler eklendi.
  - İlk çoğaltma (IR) ve Delta çoğaltma (DR) sıkıştırma faktörünü güncelleştirildi.
  - SMB depolaması için sınırlı destek eklendi.

## <a name="version-21"></a>Sürüm 2,1

**Yayın tarihi: 3 Ocak 2018**

**Düzeltilen**

- Excel raporu güncelleştirildi.
- Getüretilen Iş işleminde düzeltilen hatalar.
- VM sayısını profile veya rapor oluşturacak şekilde sınırlamak için seçeneği eklendi. Varsayılan sınır 1.000 VM 'dir.
- VMware 'den Azure 'a olağanüstü durum kurtarma:
  - Uyumsuz tabloya giden Windows Server 2016 VM 'nin bir sorunu düzeltildi. 
  - Genişletilebilir Bellenim Arabirimi (EFı) Windows VM 'Leri için güncelleştirilmiş uyumluluk iletileri.
- VMware 'den Azure 'a ve Hyper-V ' d e güncelleştirilmiş, VM başına VM veri değişim sınırı. 
- VM listesi dosyası ayrıştırma 'nin güvenilirliği geliştirildi.

## <a name="version-201"></a>Sürüm 2.0.1

**Yayın tarihi: 7 Aralık 2017**

**Düzeltilen**

- Ağ bant genişliğini iyileştirmek için öneri eklendi.

## <a name="version-20"></a>Sürüm 2,0

**Yayın tarihi: 28 Kasım 2017**

**Düzeltilen**

- Azure olağanüstü durum kurtarma için Hyper-V desteği eklendi.
- Maliyet Hesaplayıcı eklendi.
- VM 'nin koruma için uyumlu olup olmadığını veya uyumsuz olduğunu öğrenmek için VMware 'den Azure 'a olağanüstü durum kurtarma için işletim sistemi sürümü denetimi eklendi. Araç, bu VM için vCenter Server tarafından döndürülen işletim sistemi sürümü dizesini kullanır. Bu, VMware 'de VM oluştururken kullanıcının seçtiği Konuk işletim sistemi sürümüdür.

**Bilinen sınırlamalar:**

- Hyper-V ' d e Azure olağanüstü durum kurtarma için, adı: `,`, `"`, `[`, `]` ve ``` ` ``` gibi karakterleri içeren VM desteklenmez. Profili oluşturulmuş ise, rapor oluşturma başarısız olur veya hatalı bir sonuca sahip olur.
- VMware 'den Azure 'a olağanüstü durum kurtarma için, virgül içeren ada sahip VM desteklenmez. Profil oluşturma, rapor oluşturma başarısız olursa veya hatalı bir sonuç verir.

## <a name="version-131"></a>Sürüm 1.3.1

**Yayın tarihi: 19 Temmuz 2017** 

**Düzeltilen**

- Rapor oluşturmada büyük diskler (> 1 TB) için destek eklendi. Artık, 1 TB 'den büyük (4095 GB 'a kadar) disk boyutlarına sahip sanal makineler için çoğaltma planlamak üzere Dağıtım Planlayıcısı kullanabilirsiniz.
[Azure Site Recovery'de büyük disk desteği](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Sürüm 1,3

**Yayın tarihi: 9 Mayıs 2017**

**Düzeltilen**

- Rapor oluşturmada yönetilen disk için destek eklendi. Tek bir depolama hesabına yerleştirilebilecek sanal makine sayısı, yönetilen diskin yük devretme/yük devretme testi için seçili olup olmadığı temel alınarak hesaplanır.

## <a name="version-12"></a>Sürüm 1,2

**Yayın tarihi: 7 Nisan 2017**

**Düzeltilen**

- VM 'nin koruma için uyumlu olup olmadığını veya uyumsuz olduğunu anlamak için her VM için önyükleme türü (BIOS veya EFı) denetimleri eklendi.
- Uyumlu VM 'ler ve uyumsuz VM çalışma sayfalarındaki her bir sanal makine için işletim sistemi türü bilgileri eklendi.
- ABD devlet ve Çin Microsoft Azure bölgeleri için Getüretilen Iş desteği eklendi.
- vCenter ve ESXi Server için birkaç tane daha ön koşul denetimi eklendi.
- Yerel ayarlar Ingilizce dışında bir şekilde ayarlandığında, hatalı rapor alma sorunu düzeltildi.

## <a name="version-11"></a>Sürüm 1,1

**Yayın tarihi: 9 Mart 2017**

**Düzeltilen**

- Çeşitli vCenter ESXi konakları arasında aynı ada veya IP adresine sahip iki veya daha fazla VM olduğunda, profil oluşturma VM 'lerinin önlendiği bir sorun düzeltildi.
- Uyumlu VM 'ler ve uyumsuz VM 'Ler çalışma sayfaları için kopyalama ve arama 'nın devre dışı bırakılmasının neden olduğu bir sorun düzeltildi.

## <a name="version-10"></a>Sürüm 1,0

**Yayın tarihi: 23 Şubat 2017**

**Bilinen sınırlamalar:**

- Yalnızca VMware 'den Azure 'a olağanüstü durum kurtarma senaryolarını destekler. Hyper-V ile Azure olağanüstü durum kurtarma senaryoları için, [Hyper-v kapasite planlayıcısı aracını](./site-recovery-capacity-planning-for-hyper-v-replication.md)kullanın.
- ABD devlet ve Çin Microsoft Azure bölgeleri için Getüretilen Iş işlemini desteklemez.
- VCenter Server, çeşitli ESXi konakları arasında aynı ada veya IP adresine sahip iki veya daha fazla sanal makineye sahipse araç, VM 'Leri profilgiden kaldırılmıştır.
Bu sürümde, araç VMListFile dosyasındaki yinelenen sanal makine adları veya IP adresleri için profil oluşturmayı atlar. Bunun geçici çözümü, sanal makine profillerinin vCenter sunucusu yerine bir ESXi ana bilgisayarı kullanılarak oluşturulmasıdır. Her ESXi konağı için bir örnek çalıştırmanın emin olun.
