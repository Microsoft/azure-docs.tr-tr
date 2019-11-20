---
title: HPC uygulamaları - Azure sanal makineler ölçeklendirme | Microsoft Docs
description: Azure Vm'lerinde HPC uygulamaları ölçeklendirmeyi öğrenin.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707833"
---
# <a name="scaling-hpc-applications"></a>HPC uygulamaları ölçeklendirme

Azure'da HPC uygulamaları en iyi ölçek büyütme ve ölçek genişletme performansı performans ayarlama gerektirir ve belirli iş yükü için en iyi duruma getirme denemeleri görüntüleyebilir. Bu bölümde ve VM serisi özgü sayfaları uygulamalarınızı ölçeklendirme için genel rehberlik sunar.

## <a name="compiling-applications"></a>Uygulamaları derleme

Gerekli'da, uygun iyileştirme bayrakları ile uygulamalar derlemek en iyi ölçek büyütme performansı HB ve HC serisi Vm'lerde sağlar.

### <a name="amd-optimizing-cc-compiler"></a>C en iyi duruma getirme AMD /C++ derleyici

AMD iyileştirme C /C++ derleyici (AOCC) derleme sistemi, Gelişmiş iyileştirmeler, çoklu iş parçacığı içeren genel iyileştirmeyi vektörleştirme, arası yordam analizleri, döngü dönüşümleri işlemci desteği ve yüksek düzeyde sunar ve kod oluşturma. AOCC derleyici ikili dosyaları ve üstü GNU C Kitaplığı (glibc) sürüm 2.17 sahip Linux sistemleri için uygundur. Bir C Derleyici suite oluşur /C++ derleyici (clang), Fortran derleyici (FLANG) ve Fortran ön uç için Clang (işlenmiş Ejderha Yumurta).

### <a name="clang"></a>Clang

Clang olan bir C C++ve işleme ön işleme, ayrıştırma, en iyi duruma getirme, kod oluşturma, derleme ve bağlama Objective-C derleyicisi. Clang destekler `-march=znver1` en iyi etkinleştirme bayrağını kod oluşturma ve x86 AMD'ın Zen tabanlı için ayarlama mimarisi.

### <a name="flang"></a>FLANG

FLANG derleyici (Nisan 2018'e eklenen) AOCC Suite son ektir ve indirin ve test etmek, geliştiriciler için yayın öncesi bulunmaktadır. FORTRAN 2008'de bağlı olarak, AMD FLANG GitHub sürümünü genişletir (https://github.com/flangcompiler/flang). FLANG derleyicinin tüm Clang derleyici seçenekleri ve ek çeşitli FLANG özgü derleyici seçeneklerini destekler.

### <a name="dragonegg"></a>DragonEgg

DragonEgg GCC'ın iyileştiricileri ve kod oluşturucuları LLVM projeden değerlerle değiştirir gcc bir eklentidir. Gcc-4.8.x AOCC çalışır birlikte DragonEgg x86 32/x86 64 hedefler için test edilmiştir ve çeşitli Linux platformlarına başarıyla kullandı.

GFortran gerçek ön uç Fortran programlar ön işleme, ayrıştırma ve anlam analizi GCC GIMPLE Ara gösterimi (IR) oluşturma için sorumlu olur. DragonEgg GFortran derleme akışına takma bir GNU eklenti olur. GNU eklentisi API uygular. Derleyici sürücü eklentisi mimarisi ile farklı bir derleme aşamaları sürüş DragonEgg haline gelir.  İndirme ve yükleme yönergeleri uyguladıktan sonra işlenmiş Ejderha Yumurta kullanılarak çağrılabilir: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI derleyici
PGI Community Edition ver. 17 AMD EPYC ile çalışmak için onaylandı. Akış PGI derlenmiş bir sürümünü platformun tam bellek bant genişliği sunun. Benzer şekilde, daha yeni Community Edition 18.10 (Kasım 2018) düzgün çalışmalıdır. Derleyici en uygun şekilde Intel derleyicisi ile CLI örnek aşağıdadır:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel derleyici
Intel derleyici ver. 18 AMD EPYC ile çalışmak için onaylandı. Derleyici en uygun şekilde Intel derleyicisi ile CLI örnek aşağıdadır.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC derleyici 
HPC için AMD GCC derleyici 7.3 ya da daha yeni önerir. RHEL/CentOS 7.4 ile dahil 4.8.5 gibi eski sürümleri önerilmez. GCC 7.3 ve daha yeni sürümü, HPL HPCG ve DGEMM testleri önemli ölçüde daha yüksek performans sunar.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Ölçeklendirme uygulamaları 

Aşağıdaki öneriler, en iyi uygulama ölçeklendirme verimliliğini, performansını ve tutarlılık için geçerlidir:

* PIN ardışık sabitleme yaklaşımını (bir otomatik dengelemek yaklaşım karşılık olarak) çekirdek 0-59 kullanmaya işler. 
* Bağlama tarafından Numa/çekirdek/HwThread varsayılan bağlama daha iyidir.
* Karma paralel uygulamalar için (OpenMP + MPI), 4 iş parçacıkları ve CCX başına 1 MPI derecelendirmesi kullanın.
* Saf MPI uygulamaları için 1-4 MPI CCX en iyi performans için sıralar ile denemeler yapın.
* Bellek bant genişliği ile olağanüstü hassasiyet bazı uygulamalar, azaltılmış bir CCX başına çekirdek sayısı kullanımından yararlanabilir. Bu uygulamalar için CCX başına 3 ya da 2 Çekirdek kullanarak bellek bant genişliği çekişmeyi azaltmak ve daha yüksek gerçek performans ve daha tutarlı ölçeklenebilirliği yield. Özellikle, MPI Allreduce bundan faydalanabilir.
* Önemli ölçüde daha büyük ölçek çalıştırmalarında, UD ya da karma RC + UD taşımalar kullanmak için önerilir. Dahili olarak (UCX veya MVAPICH2 gibi) bunu birçok MPI kitaplıkları/çalışma zamanı kitaplıkları. Büyük ölçekli çalıştırmalar için Aktarım yapılandırmalarınızı kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinin [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) azure'da.
