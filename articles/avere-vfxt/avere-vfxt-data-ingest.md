---
title: Azure için verileri avere vFXT 'ye taşıma
description: Azure için avere vFXT ile kullanılmak üzere yeni bir depolama birimine veri ekleme
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: f4696d9e2d45e99089c9a723024067bf3b2aabcc
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255444"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Verileri vFXT kümesine taşıma-Parallel Data ınest 

Yeni bir vFXT kümesi oluşturduktan sonra ilk göreviniz verileri yeni depolama birimine taşımak olabilir. Ancak, veri taşıma yöntemi bir istemciden basit bir kopyalama komutu yayınlıyorsa, büyük olasılıkla bir kopya performansı görürsünüz. Tek iş parçacıklı kopyalama, verileri avere vFXT kümesinin arka uç depolamasına kopyalamak için iyi bir seçenek değildir.

Avere vFXT kümesi ölçeklenebilir bir çoklu istemci önbelleği olduğundan, verileri buna kopyalamanın en hızlı ve en verimli yolu birden çok istemcidir. Bu teknik, dosyaların ve nesnelerin giriş alımını paralelleştirme.

![Çoklu istemci, çok iş parçacıklı veri hareketini gösteren diyagram: sol üst tarafta, şirket içi donanım depolamada bir simgenin bundan sonra gelen birden çok oku vardır. Oklar dört istemci makineye işaret noktasıdır. Her bir istemci makineden üç ok avere vFXT 'ye doğru işaret. Avere vFXT 'den birden çok ok, blob Storage ' a işaret noktasıdır.](media/avere-vfxt-parallel-ingest.png) 

Verileri bir depolama sisteminden diğerine aktarmak için yaygın olarak kullanılan ``cp`` veya ``copy`` komutları, tek seferde yalnızca bir dosyayı kopyalamak için tek iş parçacıklı işlemlerdir. Bu, dosya sunucusunun tek seferde yalnızca bir dosya olduğu anlamına gelir. Bu, kümenin kaynakları için bir atık olur.

Bu makalede, verileri avere vFXT kümesine taşımak için çok istemci, çok iş parçacıklı dosya kopyalama sistemi oluşturma stratejileri açıklanmaktadır. Birden çok istemci ve basit kopyalama komutları kullanılarak etkili veri kopyalama için kullanılabilen dosya aktarımı kavramlarını ve karar noktalarını açıklar.

Ayrıca yardımcı olabilecek bazı yardımcı programları da açıklar. @No__t-0 yardımcı programı, bir veri kümesini demetlere bölme ve rsync komutlarının kullanımı sürecini kısmen otomatikleştirebilmek için kullanılabilir. @No__t-0 betiği, kaynak dizini okuyan ve komutları otomatik olarak kopyalama ile ilgili başka bir yardımcı programdır.  

Bir bölüme gitmek için bağlantıya tıklayın:

* [El ile kopyalama örneği](#manual-copy-example) -Copy komutları kullanılarak kapsamlı bir açıklama
* [Kısmen otomatikleştirilen (msrsync) örneği](#use-the-msrsync-utility-to-populate-cloud-volumes) 
* [Paralel kopya örneği](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Veri alma VM şablonu

GitHub üzerinde, bu makalede bahsedilen paralel veri alma araçlarıyla otomatik olarak bir VM oluşturmak için bir Kaynak Yöneticisi şablonu kullanılabilir. 

![BLOB depolama, donanım depolama ve Azure dosya kaynaklarından her biri birden çok ok gösteren diyagram. Oklar bir "veri alma sanal makinesini" işaret ettikten sonra, avere vFXT 'ye işaret eden birden çok ok](media/avere-vfxt-ingestor-vm.png)

Veri alma sanal makinesi, yeni oluşturulan VM 'nin avere vFXT kümesini takar ve önyükleme betiğini kümeden indirdiği bir öğreticinin parçasıdır. Ayrıntılar için [veri alımı Sanal](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) makinesini okuyun.

## <a name="strategic-planning"></a>Stratejik planlama

Verileri paralel olarak kopyalamak için bir strateji oluştururken dosya boyutu, dosya sayısı ve Dizin derinliği içindeki avantajları anlamanız gerekir.

* Dosyalar küçük olduğunda, ilgilendiğiniz ölçüm, saniye başına dosya olur.
* Dosyalar büyükse (10 MIBI veya üzeri), ilgilendiğiniz ölçüm bayt/saniye olur.

Her kopyalama işleminin bir işleme hızı ve dosya-aktarım hızı vardır ve bu, kopyalama komutunun uzunluğu ve dosya boyutu ile dosya sayısı düzenleme ile ölçülebilir. Hızların nasıl ölçülmesi, bu belgenin kapsamı dışındadır, ancak küçük veya büyük dosyalarla ilgilenip işlenmeyeceğinizi anlamak için önemlidir.

## <a name="manual-copy-example"></a>El ile kopyalama örneği 

Önceden tanımlanmış dosya veya yol kümelerine yönelik olarak, arka planda birden fazla kopyalama komutu çalıştırarak, bir istemcide çok iş parçacıklı bir kopyayı el ile oluşturabilirsiniz.

Linux/UNIX ``cp`` komutu, sahiplik ve mtime meta verilerini korumak için ``-p`` bağımsız değişkenini içerir. Bu bağımsız değişkeni aşağıdaki komutlara eklemek isteğe bağlıdır. (Bağımsız değişkeni eklemek, meta veri değişikliği için istemciden hedef FileSystem 'a gönderilen dosya sistemi çağrılarının sayısını artırır.)

Bu basit örnek, paralel olarak iki dosya kopyalar:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Bu komutu verdikten sonra, `jobs` komutu iki iş parçacığının çalıştığını gösterir.

### <a name="predictable-filename-structure"></a>Öngörülebilir dosya adı yapısı 

Dosya adları tahmin edilebilir ise, paralel kopyalama iş parçacıkları oluşturmak için ifadeleri kullanabilirsiniz. 

Örneğin, dizininiz `0001` ' dan `1000` ' e ardışık olarak numaralandırılan 1000 dosyaları içeriyorsa, her bir Copy 100 dosyasını izleyen on paralel iş parçacığı oluşturmak için aşağıdaki ifadeleri kullanabilirsiniz:

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

### <a name="unknown-filename-structure"></a>Bilinmeyen dosya adı yapısı

Dosya adlandırma yapınız tahmin edilebilir değilse, dosyaları dizin adlarına göre gruplandırabilirsiniz. 

Bu örnek, ``cp`` komutlarına gönder arka plan görevleri olarak çalışacak tüm dizinleri toplar:

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

### <a name="when-to-add-mount-points"></a>Bağlama noktaları ne zaman eklenir

Tek bir hedef dosya sistemi bağlama noktasına karşı çok sayıda paralel iş parçacığına sahip olduktan sonra, daha fazla iş parçacığı eklemenin daha fazla verimlilik vermediği bir nokta olacaktır. (Aktarım hızı, veri türlerine bağlı olarak dosya/saniye veya bayt/saniye cinsinden ölçülecektir.) Ya da daha kötüleşiyor, iş parçacığı, bazen üretilen iş azalmasına neden olabilir.  

Bu durumda, aynı uzak dosya sistemi bağlama yolunu kullanarak diğer vFXT kümesi IP adreslerine istemci tarafı bağlama noktaları ekleyebilirsiniz:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

İstemci tarafı bağlama noktaları eklemek ek `/mnt/destination[1-3]` bağlama noktalarına ek kopyalama komutları ekleyerek daha fazla paralellik elde etmenizi sağlar.  

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

### <a name="when-to-add-clients"></a>İstemcilerin ne zaman ekleneceği

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

### <a name="create-file-manifests"></a>Dosya bildirimleri oluşturma

Yukarıdaki yaklaşımlar anlaşıldıktan sonra (hedef başına birden çok kopya iş parçacığı, istemci başına birden çok hedef, ağ erişimli kaynak dosya başına birden çok istemci), şu öneriyi göz önünde bulundurun: dosya bildirimleri oluşturun ve sonra bunları kopyayla birlikte kullanın birden çok istemci arasındaki komutlar.

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
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
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

@No__t-2 komutundan çıktının bir parçası olarak elde edilen düzey dört dizinlere sahip olan *n* istemcilerinden her biri için bir tane olmak üzere *n* sonuç dosyası alacaksınız. 

Kopyalama komutunu oluşturmak için her dosyayı kullanın:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Yukarıdaki, her biri her satırda bir kopyalama komutu olan *N* dosya sağlayacak ve bu, istemcide Bash betiği olarak çalıştırılabilirler. 

Amaç, birden çok istemcide paralel olarak bu betiklerin birden çok iş parçacığını her istemci için aynı anda çalıştırmaktır.

## <a name="use-the-msrsync-utility-to-populate-cloud-volumes"></a>Bulut birimlerini doldurmak için msrsync yardımcı programını kullanın

@No__t-0 aracı Ayrıca verileri avere kümesi için bir arka uç çekirdeği altına taşımak üzere kullanılabilir. Bu araç birden çok paralel ``rsync`` işlemi çalıştırarak bant genişliği kullanımını iyileştirmek üzere tasarlanmıştır. GitHub 'dan https://github.com/jbd/msrsync ' da kullanılabilir.

``msrsync``, kaynak dizinini ayrı "demetlere" ayırır ve sonra her bir demet üzerinde tek ``rsync`` işlem çalıştırır.

Dört çekirdekli bir VM kullanan ön test, 64 işlemleri kullanırken en iyi verimliliği gösteriyordu. İşlem sayısını 64 olarak ayarlamak için ``-p`` ``msrsync`` seçeneğini kullanın.

@No__t-0 ' ın yalnızca yerel birimlerden ve ' a yazabildiğini unutmayın. Kaynak ve hedef, kümenin sanal ağındaki yerel başlatmalar olarak erişilebilir olmalıdır.

Bir Azure bulut birimini bir avere kümesiyle doldurmak üzere msrsync 'i kullanmak için şu yönergeleri izleyin:

1. Msrsync ve önkoşullarını (rsync ve Python 2,6 veya üzeri) yükler
1. Kopyalanacak toplam dosya ve dizin sayısını belirleme.

   Örneğin, avere yardımcı programını ```prime.py --directory /path/to/some/directory```  (URL 'yi https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py) ) ile birlikte kullanın.

   @No__t-0 ' ı kullanmıyorsanız, GNU ``find`` aracıyla birlikte öğe sayısını aşağıdaki şekilde hesaplayabilirsiniz:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. İşlem başına öğe sayısını öğrenmek için öğe sayısını 64 göre bölün. Komutu çalıştırdığınızda demetlerin boyutunu ayarlamak için bu sayıyı ``-f`` seçeneğiyle kullanın.

1. Dosyaları kopyalamak için msrsync komutunu verme:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Örneğin, bu komut, 11.000 64 dosyalarını/test/source-Repository 'den/mnt/vfxt/Repository dizinine taşımak için tasarlanmıştır:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Paralel kopya betiğini kullanın

@No__t-0 betiği, verileri vFXT kümenizin arka uç depolamasına taşımak için de yararlı olabilir. 

Aşağıdaki komut dosyası yürütülebilir @no__t ekler-0. (Bu betik Ubuntu için tasarlanmıştır; başka bir dağıtım kullanılıyorsa, ``parallel`` ' ı ayrı ayrı yüklemelisiniz.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM 
#!/bin/bash

display_usage() { 
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n" 
} 

if [  \$# -le 1 ] ; then 
    display_usage
    exit 1
fi 
 
if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then 
    display_usage
    exit 0
fi 

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Paralel kopya örneği

Bu örnek, avere kümesinden kaynak dosyaları kullanarak ``glibc`` ' i derlemek için paralel kopyalama betiğini kullanır. 
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Kaynak dosyalar avere kümesi bağlama noktasında depolanır ve nesne dosyaları yerel sabit sürücüde depolanır.

Bu betik, yukarıdaki paralel kopya betiğini kullanır. @No__t-0 seçeneği, paralel hale getirme kazanmak için ``parallelcp`` ve ``make`` ile birlikte kullanılır.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```

