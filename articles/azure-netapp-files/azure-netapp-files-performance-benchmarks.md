---
title: Azure NetApp Files için performans kıyaslama test sonuçları | Microsoft Docs
description: Birim düzeyindeki Azure NetApp Files için performans kıyaslama testlerinin sonuçlarını açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881750"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Azure NetApp Files için performans kıyaslama test sonuçları

Bu makalede, birim düzeyindeki Azure NetApp Files yönelik performans kıyaslama testlerinin sonuçları açıklanmaktadır. 

## <a name="sample-application-used-for-the-tests"></a>Testler için kullanılan örnek uygulama

Performans testleri Azure NetApp Files kullanarak örnek bir uygulamayla çalıştırıldı. Uygulama aşağıdaki özelliklere sahiptir: 

* Bulut için oluşturulmuş Linux tabanlı bir uygulama
* Gerektiğinde işlem gücünü artırmak için eklenen sanal makineler (VM) ile doğrusal şekilde ölçeklendirebilir
* Data Lake için hızlı erişilebilirliği gerektirir
* Bazen rastgele ve bazen sıralı olan g/ç desenleri vardır 
    * Rastgele bir model, büyük miktarlarda g/ç için düşük gecikme süresi gerektirir. 
    * Sıralı bir model, büyük miktarda bant genişliği gerektirir. 

## <a name="about-the-workload-generator"></a>İş yükü Oluşturucu hakkında

Sonuçlar Vdbench Özet dosyalarından gelir. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) , depolama performansını doğrulamak için disk g/ç iş yükleri üreten bir komut satırı yardımcı programıdır. Kullanılan istemci-sunucu yapılandırması ölçeklenebilir.  Tek bir karışık ana/istemci ve 14 adanmış istemci VM 'Leri içerir.

## <a name="about-the-tests"></a>Testler hakkında

Testler, örnek uygulamanın sahip olabileceği limitleri ve sınırlara kadar eğen olan yanıt süresini tanımlamak için tasarlanmıştır.  

Aşağıdaki testler çalıştırıldı: 

* 100% 8-KiB rastgele okuma
* 100% 8-KiB rastgele yazma
* 100% 64-KiB sıralı okuma
* 100% 64-KiB sıralı yazma
* 50% 64-KiB sıralı okuma, 50% 64-KiB sıralı yazma
* 50% 8-KiB rastgele okuma, 50% 8-KiB rastgele yazma

## <a name="bandwidth"></a>Bant genişliği

Azure NetApp Files birden çok [hizmet düzeyi](azure-netapp-files-service-levels.md)sunar. Her hizmet düzeyi, TiB tarafından sağlanan kapasite (birim kotası) başına farklı miktarda bant genişliği sunar. Bir birim için bant genişliği sınırı, hizmet düzeyi ve birim kotasının birleşimine göre sağlanır. Bant genişliği sınırı, gerçekleşen gerçek aktarım hızı miktarını belirlemede yalnızca bir faktördür.  

Şu anda, test edilen tek bir birimde bir iş yükü tarafından gerçekleştirilen en yüksek aktarım hızı 4.500 MIB 'dir.  Premium hizmet düzeyiyle, 70,31 TiB 'nin bir birim kotası, bu aktarım hızını aşağıdaki hesaplama uyarınca sağlamak için yeterli bant genişliği sağlayacaktır: 

![Bant genişliği formülü](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Kota ve hizmet düzeyi](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Verimlilik yoğun iş yükleri

Vdbench kullanılan işleme testi ve 12xD32s v3 depolama VM 'lerinin bir birleşimi. Testteki örnek birim aşağıdaki işleme numaralarına ulaşıldı:

![Aktarım hızı testi](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>G/ç yoğun iş yükleri

Vdbench kullanılan g/ç testi ve 12xD32s v3 depolama VM 'lerinin bir birleşimi. Testteki örnek birim şu g/ç numaralarına ulaşıldı:

![G/ç testi](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Gecikme süresi

Test VM 'Leri ve Azure NetApp Files birimi arasındaki mesafe, g/ç performansını etkiler.  Aşağıdaki grafik iki farklı VM kümesi için ıOPS ile gecikme süresi yanıt eğrilerini karşılaştırır.  Bir sanal makine kümesi Azure NetApp Files yakındır ve diğer küme daha fazla dışarıda olur.  Daha fazla VM kümesi için artan gecikme süresi, belirli bir paralellik düzeyinde elde edilen ıOPS miktarı üzerinde bir etkiye sahiptir.  Ne olursa olsun, bir birime yönelik okumalar aşağıda gösterildiği gibi 300.000 ıOPS 'yi aşabilirler: 

![Gecikme süresi](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Özet

Gecikme süresine duyarlı iş yükleri (veritabanları) tek milisaniyelik yanıt süresine sahip olabilir. İşlem performansı, tek bir birim için 300k ıOPS üzerinde olabilir.

Aktarım hızı duyarlı uygulamalar (akış ve görüntüleme için), 4.5 GiB/sn aktarım hızına sahip olabilir.

## <a name="example-scripts"></a>Örnek betikler

Aşağıdaki örnek betikler yalnızca tanıtım amaçlı amaçlıdır.  Bunlar, üretim amacıyla kullanılmamalıdır.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
