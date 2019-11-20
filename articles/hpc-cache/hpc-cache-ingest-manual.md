---
title: Azure HPC önbellek verileri alma-el ile kopyalama
description: Azure HPC önbelleğinde bir BLOB depolama hedefine veri taşımak için CP komutlarını kullanma
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fc397088e46f0d2b623080f3deed24c386e7d8b4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168491"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Azure HPC önbelleği veri alma-el ile kopyalama yöntemi

Bu makalede, Azure HPC Cache ile kullanmak üzere verileri bir BLOB depolama kapsayıcısına el ile kopyalamak için ayrıntılı yönergeler sunulmaktadır. Kopyalama hızını iyileştirmek için çok iş parçacıklı paralel işlemler kullanır.

Azure HPC önbelleğiniz için verileri blob depolamaya taşıma hakkında daha fazla bilgi edinmek için [Azure Blob depolama 'ya veri taşıma](hpc-cache-ingest.md)makalesini okuyun.

## <a name="simple-copy-example"></a>Basit kopya örneği

Önceden tanımlanmış dosya veya yol kümelerine yönelik olarak, arka planda birden fazla kopyalama komutu çalıştırarak, bir istemcide çok iş parçacıklı bir kopyayı el ile oluşturabilirsiniz.

Linux/UNIX ``cp`` komutu, sahiplik ve mtime meta verilerini korumak için ``-p`` bağımsız değişkenini içerir. Bu bağımsız değişkeni aşağıdaki komutlara eklemek isteğe bağlıdır. (Bağımsız değişkeni eklemek, meta veri değişikliği için istemciden hedef dosya sistemine gönderilen dosya sistemi çağrılarının sayısını artırır.)

Bu basit örnek, paralel olarak iki dosya kopyalar:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Bu komutu verdikten sonra, `jobs` komutu iki iş parçacığının çalıştığını gösterir.

## <a name="copy-data-with-predictable-file-names"></a>Tahmin edilebilir dosya adlarıyla veri kopyalama

Dosya adlarınız tahmin edilebilir ise, paralel kopyalama iş parçacıkları oluşturmak için ifadeleri kullanabilirsiniz. 

Örneğin, dizininiz `1000``0001` sıralı olarak numaralandırılmış 1000 dosya içeriyorsa, her bir kopyalanan 100 dosyasını izleyen on paralel iş parçacığı oluşturmak için aşağıdaki ifadeleri kullanabilirsiniz:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Yapılandırılmamış dosya adlarıyla veri kopyalama

Dosya adlandırma yapınız tahmin edilebilir değilse, dosyaları dizin adlarına göre gruplandırabilirsiniz. 

Bu örnek, arka plan görevleri olarak çalıştırılan ``cp`` komutlarına göndermek için tüm dizinleri toplar:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Dosyalar toplandıktan sonra, alt dizinleri ve tüm içeriğini yinelemeli olarak kopyalamak için paralel kopyalama komutlarını çalıştırabilirsiniz:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Bağlama noktaları ne zaman eklenir

Tek bir hedef dosya sistemi bağlama noktasına karşı çok sayıda paralel iş parçacığına sahip olduktan sonra, daha fazla iş parçacığı eklemenin daha fazla verimlilik vermediği bir nokta olacaktır. (Aktarım hızı, veri türlerine bağlı olarak dosya/saniye veya bayt/saniye cinsinden ölçülecektir.) Ya da daha kötüleşiyor, iş parçacığı, bazen üretilen iş azalmasına neden olabilir.  

Bu durumda, aynı uzak dosya sistemi bağlama yolunu kullanarak diğer Azure HPC önbellek bağlama adreslerine istemci tarafı bağlama noktaları ekleyebilirsiniz:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

İstemci tarafı bağlama noktaları eklemek, ek `/mnt/destination[1-3]` bağlama noktalarına daha fazla paralellik elde etmenizi sağlar.  

Örneğin, dosyalarınız çok büyükse, farklı hedef yolları kullanmak için kopyalama komutlarını tanımlayabilir ve kopyayı gerçekleştiren istemciden paralel olarak daha fazla komut gönderebilirsiniz.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

Yukarıdaki örnekte, üç hedef bağlama noktası, istemci dosyası kopyalama işlemlerine yöneliktir.

## <a name="when-to-add-clients"></a>İstemcilerin ne zaman ekleneceği

Son olarak, istemcinin özelliklerine ulaştınız, daha fazla kopyalama iş parçacığı veya ek bağlama noktası eklenmesi ek dosya/sn veya bayt/sn artışı vermez. Bu durumda, kendi dosya kopyalama işlemi kümelerini çalıştıran aynı bağlama noktaları kümesiyle başka bir istemciyi dağıtabilirsiniz. 

Örnek:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Dosya bildirimleri oluşturma

Yukarıdaki yaklaşımlar anlaşıldıktan sonra (hedef başına birden çok kopya iş parçacığı, istemci başına birden çok hedef, ağ erişimli kaynak dosya sistemi başına birden çok istemci), şu öneriyi göz önünde bulundurun: dosya bildirimleri oluşturun ve sonra bunları kopyayla birlikte kullanın birden çok istemci arasındaki komutlar.

Bu senaryo, dosya veya dizinlerin bildirimlerini oluşturmak için UNIX ``find`` komutunu kullanır:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Bu sonucu bir dosyaya yeniden yönlendir: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Ardından, dosyaları saymak ve alt dizinlerin boyutlarını belirleyebilmek için BASH komutlarını kullanarak bildirimde yineleyebilirsiniz.

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Son olarak, gerçek dosya kopyalama komutlarını istemcilere kopyalamanız gerekir.  

Dört istemciniz varsa, şu komutu kullanın:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Beş istemciniz varsa, şöyle bir şey kullanın:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

Ve altı.... Gerektiğinde extrapogeç.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

`find` komutundan çıktının bir parçası olarak elde edilen düzey dört dizine ait yol adlarına sahip *n istemcilerinden her* biri için bir tane olmak üzere *n* sonuç dosyası alacaksınız. 

Kopyalama komutunu oluşturmak için her dosyayı kullanın:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Yukarıdaki, her biri her satırda bir kopyalama komutu olan *N* dosya sağlayacak ve bu, istemcide Bash betiği olarak çalıştırılabilirler. 

Amaç, birden çok istemcide paralel olarak bu betiklerin birden çok iş parçacığını her istemci için aynı anda çalıştırmaktır.
