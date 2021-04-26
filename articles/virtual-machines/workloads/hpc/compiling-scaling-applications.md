---
title: HPC uygulamalarını ölçeklendirme-Azure sanal makineleri | Microsoft Docs
description: Azure VM 'lerinde HPC uygulamalarını ölçeklendirmeyi öğrenin.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f81d40abdf402b1e19090c5375dfa8c335418248
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600973"
---
# <a name="scaling-hpc-applications"></a>HPC uygulamalarını ölçeklendirme

Azure üzerinde HPC uygulamalarının en iyi ölçeği artırma ve genişleme performansı, belirli bir iş yükü için performans ayarlama ve iyileştirme denemeleri gerektirir. Bu bölüm ve VM dizisine özgü sayfalar, uygulamalarınızın ölçeklendirilmesine yönelik genel rehberlik sunar.

## <a name="application-setup"></a>Uygulama kurulumu
[Azurehpc deposunun](https://github.com/Azure/azurehpc) birçok örneği vardır:
- [Uygulamaları](https://github.com/Azure/azurehpc/tree/master/apps) en iyi şekilde ayarlama ve çalıştırma.
- [Dosya sistemleri ve kümelerin](https://github.com/Azure/azurehpc/tree/master/examples)yapılandırması.
- Bazı ortak uygulama iş akışlarıyla kolayca çalışmaya nasıl başlayacağınız hakkında [öğreticiler](https://github.com/Azure/azurehpc/tree/master/tutorials) .

## <a name="optimally-scaling-mpi"></a>MPı en iyi şekilde ölçeklendirme 

En iyi uygulama ölçeklendirme verimliliği, performansı ve tutarlılığı için aşağıdaki öneriler geçerlidir:

- Daha küçük ölçekli işler (yani < 256K bağlantı) için seçeneğini kullanın:
   ```bash
   UCX_TLS=rc,sm
   ```

- Daha büyük ölçekli işler (yani > 256K bağlantı) için seçeneğini kullanın:
   ```bash
   UCX_TLS=dc,sm
   ```

- Yukarıdaki ' de, MPı işinizin bağlantı sayısını hesaplamak için şunu kullanın:
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>Uyarlamalı yönlendirme
Uyarlamalı yönlendirme (AR), EDR ve HDR InfiniBand çalıştıran Azure sanal makinelerinin (VM 'Ler), daha iyi en iyi ağ yolunu dinamik olarak seçerek ağ tıkanıklığını otomatik olarak algılamasına ve ortadan kaldırmanıza Sonuç olarak, "InfiniBand ağında gelişmiş gecikme süresi ve bant genişliği, daha yüksek performans ve ölçeklendirme verimliliği sağlar. Daha fazla ayrıntı için, [Techcommunity makalesine](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217)başvurun.

## <a name="process-pinning"></a>İşlem sabitleme

- Sıralı sabitleme yaklaşımını (bir oto Dengeleme yaklaşımına karşılık olarak) kullanarak süreçler çekirdekleri sabitleyin. 
- NUMA/çekirdek/HwThread 'e göre bağlama varsayılan bağlamadan daha iyidir.
- Karma paralel uygulamalar (OpenMP + MPı) için, HB ve HBv2 VM boyutlarında 4 iş parçacığı ve CCX başına 1 MPı derecelendirmesi kullanın.
- Saf MPı uygulamaları için, HB ve HBv2 VM boyutlarında en iyi performans için, CCX başına 1-4 MPı derecelendirimiyle denemeler yapın.
- Bellek bant genişliğine karşı çok fazla duyarlılık içeren bazı uygulamalar, CCX başına azaltılmış sayıda çekirdek kullanmaktan faydalanabilir. Bu uygulamalar için, CCX başına 3 veya 2 çekirdek kullanılması, bellek bant genişliği çekişmesini azaltabilir ve gerçek zamanlı performansı veya daha tutarlı ölçeklenebilirlik elde edebilir. Bu yaklaşımda, özellikle de MPı 'yi azaltabilirsiniz.
- Önemli ölçüde daha büyük ölçek çalıştırmaları için, UD veya hibrit RC + UD taşımalarını kullanmanız önerilir. Birçok MPı Kitaplığı/çalışma zamanı kitaplığı bunu dahili olarak (UCX veya MVAPICH2 gibi) ister. Büyük ölçekli çalıştırmalar için aktarım yapılandırmalarınızı denetleyin.

## <a name="compiling-applications"></a>Uygulamaları derleme
<br>
<details>
<summary>Genişletmek için tıklayın</summary>

Gerekli olmasa da, uygun iyileştirme bayraklarıyla uygulamaları derlemek, HB ve HC Serisi VM 'lerde en iyi genişleme performansını sağlar.

### <a name="amd-optimizing-cc-compiler"></a>AMD Iyileştirmeli C/C++ derleyicisi

AMD Iyileştirmeli C/C++ derleyicisi (AOCC) derleyicisi sistemi, genel iyileştirme, vektörleştirme, işlem tabanlı çözümlemeler, döngü dönüştürmeleri ve kod oluşturma içeren yüksek düzeyde gelişmiş iyileştirmeler, çok iş parçacıklı ve işlemci desteği sunar. AOCC derleyicisi ikili dosyaları, GNU C Kitaplığı (OCBC) sürüm 2,17 ve üzeri olan Linux sistemleri için uygundur. Derleyici paketi bir C/C++ derleyicisi (Clang), bir FORTRAN derleyicisi (FLANG) ve bir FORTRAN ön ucu ile Clang (Ejyumurg) oluşur.

### <a name="clang"></a>Clang

Clang, bir C, C++ ve amaç-C derleyicisi, ön işleme, ayrıştırma, iyileştirme, kod oluşturma, derleme ve bağlamayı işleme. Clang,  `-march=znver1` AMD 'Nin Zen tabanlı x86 mimarisi için en iyi kod oluşturmayı ve ayarlamayı etkinleştirme bayrağını destekler.

### <a name="flang"></a>FLANG

FLANG derleyicisi, AOCC Suite 'e son eklenen bir ektir (2018 Nisan 'a eklenmiştir) ve şu anda geliştiricilerin indirmesi ve test etmek için önceden yayın sürümündedir. FORTRAN 2008 temelinde, AMD, FLANG 'nin GitHub sürümünü genişletiyor ( https://github.com/flang-compiler/flang) . FLANG derleyicisi, tüm Clang derleyici seçeneklerini ve ek sayıda FLANG özel derleyici seçeneğini destekler.

### <a name="dragonegg"></a>DragonEgg

DragonEgg, GCC 'nin en iyi hale icilerini ve kod oluşturucularını LLVM projesinden değiştiren bir GCC eklentisidir. AOCC ile birlikte gelen DragonEgg, GCC-4.8. x ile birlikte çalışarak, x86-32/x86-64 hedefleri için test edilmiştir ve çeşitli Linux platformlarında başarıyla kullanıldı.

GFortran, GCC GIMPLE ara gösterimini (IR) oluşturan ön işleme, ayrıştırma ve anlam analizinden sorumlu olan FORTRAN programlarının gerçek ön ucunda. DragonEgg, GFortran derleme akışına takmak üzere bir GNU eklentisidir. GNU eklentisi API 'sini uygular. Eklenti mimarisi sayesinde, DragonEgg derleyici sürücüsü haline gelir ve bu da derlemenin farklı aşamalarını kullanır.  İndirme ve yükleme yönergelerinden sonra, aşağıdaki kullanılarak bir Ejpg çağrılabilir: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI derleyicisi
PGI Community Edition sürümü. 17, AMD EPYıC ile çalışmayı onayladı. STREAM 'in bir PGI derlenmiş sürümü, platformun tam bellek bant genişliğini sunar. Yeni Community Edition 18,10 (Kasım 2018) de aynı şekilde çalışır. Aşağıda Intel derleyicisi ile en iyi şekilde derleyiciye örnek CLı verilmiştir:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel derleyicisi
Intel derleyicisi ver. 18, AMD EPYıC ile çalışmayı onayladı. Aşağıda Intel derleyicisi ile en iyi şekilde derleyiciye örnek CLı verilmiştir.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC derleyicisi 
HPC için AMD, GCC derleyicisi 7,3 veya daha yeni bir sürümü önerir. RHEL/CentOS 7,4 ile 4.8.5 dahil olmak üzere eski sürümlerin kullanılması önerilmez. GCC 7,3 ve üzeri, HPL, HPCG ve DGEMM testlerinde önemli ölçüde daha yüksek performans sağlar.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'DA HPC uygulamalarını en iyi duruma getirmeye yönelik bir öğrenme modülüyle](https://docs.microsoft.com/learn/modules/optimize-tightly-coupled-hpc-apps/)bilginizi test edin.
- [HBv3-Series genel bakış](hbv3-series-overview.md) ve [HC Serisi genel bakış](hc-series-overview.md)konusunu gözden geçirin.
- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- Azure 'da [HPC](/azure/architecture/topics/high-performance-computing/) hakkında daha fazla bilgi edinin.
