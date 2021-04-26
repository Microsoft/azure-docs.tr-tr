---
title: Azure HDInsight kümelerini özelleştirmek için betik eylemleri geliştirme
description: HDInsight kümelerini özelleştirmek için bash betiklerini nasıl kullanacağınızı öğrenin. Betik eylemleri, küme yapılandırma ayarlarını değiştirmek veya ek yazılım yüklemek için küme oluşturma sırasında veya sonrasında betikleri çalıştırmanızı sağlar.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/28/2019
ms.openlocfilehash: b6705728fddc9a5a3c9cb8eb2f1811412fb3a290
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945478"
---
# <a name="script-action-development-with-hdinsight"></a>HDInsight ile betik eylemi geliştirme

Bash betiklerini kullanarak HDInsight kümenizi özelleştirmeyi öğrenin. Betik eylemleri, küme oluşturma sırasında veya sonrasında HDInsight özelleştirmek için bir yoldur.

## <a name="what-are-script-actions"></a>Betik eylemleri nelerdir?

Betik eylemleri, Azure 'un yapılandırma değişiklikleri yapmak veya yazılım yüklemek için küme düğümlerinde çalıştığı Bash betikleridir. Bir betik eylemi kök olarak yürütülür ve küme düğümlerine tam erişim hakları sağlar.

Betik eylemleri aşağıdaki yöntemlerle uygulanabilir:

| Betik uygulamak için bu yöntemi kullanın... | Küme oluşturma sırasında... | Çalışan bir kümede... |
| --- |:---:|:---:|
| Azure portalı |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Azure Klasik CLI |&nbsp; |✓ |
| HDInsight .NET SDK 'Sı |✓ |✓ |
| Azure Resource Manager Şablonu |✓ |&nbsp; |

Betik eylemlerini uygulamak için bu yöntemleri kullanma hakkında daha fazla bilgi için bkz. [betik eylemlerini kullanarak HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Betik geliştirme için en iyi uygulamalar

HDInsight kümesi için özel bir komut dosyası geliştirirken aklınızda bulundurmanız gereken birkaç en iyi yöntem vardır:

* [Apache Hadoop sürümünü hedefleyin](#bPS1)
* [İşletim sistemi sürümünü hedefleme](#bps10)
* [Betik kaynaklarına kararlı bağlantılar sağlama](#bPS2)
* [Önceden derlenen kaynakları kullanma](#bPS4)
* [Küme özelleştirme betiğinin ıdempotent olduğundan emin olun](#bPS3)
* [Küme mimarisinin yüksek oranda kullanılabilir olmasını sağlayın](#bPS5)
* [Özel bileşenleri Azure Blob depolamayı kullanacak şekilde yapılandırma](#bPS6)
* [STDOUT ve STDERR 'e bilgi yazma](#bPS7)
* [Dosyaları LF satır sonları ile ASCII olarak kaydet](#bps8)
* [Geçici hatalardan kurtarmak için yeniden deneme mantığını kullanma](#bps9)

> [!IMPORTANT]  
> Betik eylemlerinin 60 dakika içinde tamamlanmalı veya işlem başarısız olur. Düğüm sağlama sırasında, komut dosyası diğer kurulum ve yapılandırma işlemleriyle eşzamanlı olarak çalışır. CPU süresi veya ağ bant genişliği gibi kaynaklara yarışma, komut dosyasının geliştirme ortamınızda olduğundan daha uzun sürmesine neden olabilir.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Apache Hadoop sürümünü hedefleyin

Farklı HDInsight sürümlerinin farklı Hadoop Hizmetleri ve bileşenleri yüklü. Betiğinizin belirli bir hizmet veya bileşen sürümünü beklediğinde, komut dosyasını yalnızca gerekli bileşenleri içeren HDInsight sürümü ile kullanmanız gerekir. HDInsight [Bileşen sürümü oluşturma](hdinsight-component-versioning.md) belgesini kullanarak HDInsight 'ta bulunan bileşen sürümleri hakkında bilgi edinebilirsiniz.

### <a name="checking-the-operating-system-version"></a>İşletim sistemi sürümü denetleniyor

HDInsight 'ın farklı sürümleri, Ubuntu 'ın belirli sürümlerini kullanır. Betikte denetlemeniz gereken işletim sistemi sürümleri arasında farklılıklar olabilir. Örneğin, Ubuntu sürümüne bağlı bir ikili dosya yüklemeniz gerekebilir.

İşletim sistemi sürümünü denetlemek için kullanın `lsb_release` . Örneğin, aşağıdaki komut dosyası, işletim sistemi sürümüne bağlı olarak belirli bir tar dosyasına nasıl başvurulacağını göstermektedir:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="target-the-operating-system-version"></a><a name="bps10"></a> İşletim sistemi sürümünü hedefleyin

HDInsight, Ubuntu Linux dağıtımına dayalıdır. Farklı HDInsight sürümleri Ubuntu 'ın farklı sürümlerine dayanır ve bu, betiğinizin davranışını değiştirebilir. Örneğin, HDInsight 3,4 ve önceki sürümleri, Upstart kullanan Ubuntu sürümlerini temel alır. 3,5 ve üzeri sürümleri, systemd kullanan Ubuntu 16,04 ' i temel alır. Systemd ve Upstart farklı komutlara güventiğinden, betiğinizin her ikisiyle de çalışacak şekilde yazılması gerekir.

HDInsight 3,4 ve 3,5 arasındaki diğer önemli fark, `JAVA_HOME` artık Java 8 ' i işaret ediyor. Aşağıdaki kod, betiğin Ubuntu 14 veya 16 ' da çalışıp çalışmadığını nasıl belirleyebileceğinizi göstermektedir:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Bu kod parçacıklarını içeren tam betiği ' de bulabilirsiniz https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh .

HDInsight tarafından kullanılan Ubuntu sürümü için bkz. [HDInsight bileşen sürümü](hdinsight-component-versioning.md) belgesi.

Systemd ve Upstart arasındaki farkları anlamak için bkz. [Upstart kullanıcıları Için systemd](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Betik kaynaklarına kararlı bağlantılar sağlama

Betiğin ve ilgili kaynakların, kümenin kullanım ömrü boyunca kullanılabilir kalması gerekir. Ölçek işlemleri sırasında kümeye yeni düğümler eklenirse, bu kaynaklar gereklidir.

En iyi uygulama, aboneliğinizdeki bir Azure depolama hesabındaki her şeyi indirip arşivlemedir.

> [!IMPORTANT]  
> Kullanılan depolama hesabı, küme için varsayılan depolama hesabı ya da başka bir depolama hesabında genel, salt okuma kapsayıcısı olmalıdır.

Örneğin, Microsoft tarafından sağlanan örnekler [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) depolama hesabında depolanır. Bu konum, HDInsight ekibi tarafından tutulan genel, salt tanımlı bir kapsayıcıdır.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Önceden derlenen kaynakları kullanma

Betiği çalıştırmak için geçen süreyi azaltmak için kaynak koddan kaynakları derleyen işlemlerden kaçının. Örneğin, kaynakları önceden derleyin ve bunları HDInsight ile aynı veri merkezindeki bir Azure depolama hesabı blobuna depolayın.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Küme özelleştirme betiğinin ıdempotent olduğundan emin olun

Betikler ıdempotent olmalıdır. Betik birden çok kez çalışırsa, kümeyi her seferinde aynı duruma döndürmelidir.

Örneğin, yapılandırma dosyalarını değiştiren bir betik birden çok kez çalıştırıldıysa yinelenen girdileri eklemememelidir.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>Küme mimarisinin yüksek oranda kullanılabilir olmasını sağlayın

Linux tabanlı HDInsight kümeleri, küme içinde etkin olan iki baş düğüm sağlar ve betik eylemleri her iki düğümde de çalışır. Yüklediğiniz bileşenler yalnızca bir baş düğüm beklediğinde, bileşenleri her iki baş düğüme de yüklemeyin.

> [!IMPORTANT]  
> HDInsight 'ın bir parçası olarak sunulan hizmetler gerektiğinde iki baş düğüm arasında yük devretmek için tasarlanmıştır. Bu işlev, betik eylemleri aracılığıyla yüklenen özel bileşenlere genişletilmez. Özel bileşenler için yüksek kullanılabilirliğe ihtiyacınız varsa, kendi yük devretme mekanizmanızı uygulamanız gerekir.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Özel bileşenleri Azure Blob depolamayı kullanacak şekilde yapılandırma

Kümeye yüklediğiniz bileşenlerin, Apache Hadoop Dağıtılmış Dosya Sistemi (bir) depolama alanı kullanan bir varsayılan yapılandırması olabilir. HDInsight, varsayılan depolama alanı olarak Azure Storage veya Data Lake Storage kullanır. Her ikisi de, küme silindikten sonra bile verileri sürdüren bir, bir, uyumlu bir dosya sistemi sağlar. Yüklediğiniz bileşenleri, "yerine te b veya ADL kullanmak üzere yapılandırmanız gerekebilir.

Çoğu işlem için dosya sistemini belirtmeniz gerekmez. Örneğin, aşağıdaki, Hadoop-Common. jar dosyasını yerel dosya sisteminden küme depolama alanına kopyalar:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

Bu örnekte, `hdfs` komut saydam olarak varsayılan küme depolamayı kullanır. Bazı işlemler için URI belirtmeniz gerekebilir. Örneğin, `adl:///example/jars` Azure Data Lake Storage 1. için `abfs:///example/jars` Data Lake Storage 2. veya `wasb:///example/jars` Azure depolama için.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>STDOUT ve STDERR 'e bilgi yazma

HDInsight, STDOUT ve STDERR 'e yazılan betik çıkışını günlüğe kaydeder. Bu bilgileri, ambarı Web Kullanıcı arabirimini kullanarak görüntüleyebilirsiniz.

> [!NOTE]  
> Apache ambarı yalnızca küme başarıyla oluşturulduysa kullanılabilir. Küme oluşturma sırasında bir betik eylemi kullanırsanız ve oluşturma başarısız olursa, bkz. günlüğe kaydedilen bilgilere erişmenin diğer yolları için [betik eylemlerine sorun giderme](./troubleshoot-script-action.md) .

Çoğu yardımcı program ve yükleme paketi zaten STDOUT ve STDERR 'e bilgi yazar, ancak daha fazla günlük kaydı eklemek isteyebilirsiniz. STDOUT 'a metin göndermek için kullanın `echo` . Örnek:

```bash
echo "Getting ready to install Foo"
```

Varsayılan olarak, `echo` DIZEYI stdout ' a gönderir. Bunu STDERR 'e yönlendirmek için, daha `>&2` önce ekleyin `echo` . Örnek:

```bash
>&2 echo "An error occurred installing Foo"
```

Bu, STDOUT 'a yazılan bilgileri STDERR 'e (2) yönlendirir. GÇ yönlendirmesi hakkında daha fazla bilgi için bkz [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html) ..

Betik eylemleri tarafından günlüğe kaydedilen bilgileri görüntüleme hakkında daha fazla bilgi için bkz. [betik eylemlerine sorun giderme](./troubleshoot-script-action.md).

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a> Dosyaları LF satır sonları ile ASCII olarak kaydet

Bash betikleri, LF tarafından sonlandırılan satırlar ile ASCII biçiminde depolanmalıdır. UTF-8 olarak depolanan dosyalar ya da satır sonu olarak CRLF kullanımı aşağıdaki hatayla başarısız olabilir:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a> Geçici hatalardan kurtarmak için yeniden deneme mantığını kullanma

Dosyaları indirirken, apt-get veya Internet üzerinden veri aktaran diğer işlemleri kullanarak paket yüklerken, geçici ağ hataları nedeniyle işlem başarısız olabilir. Örneğin, iletişim kurduğunuz uzak kaynak bir yedekleme düğümüne yük devretme sürecinde olabilir.

Komut dosyanızı geçici hatalara karşı dayanıklı hale getirmek için yeniden deneme mantığını uygulayabilirsiniz. Aşağıdaki işlev, yeniden deneme mantığının nasıl uygulanacağını gösterir. İşlemi başarısız olmadan önce üç kez yeniden dener.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Aşağıdaki örneklerde bu işlevin nasıl kullanılacağı gösterilmektedir.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Özel betikler için yardımcı yöntemler

Betik eylemi Yardımcısı yöntemleri, Özel betikler yazarken kullanabileceğiniz yardımcı programlardır. Bu yöntemler betiğe dahil edilir [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) . Komut dosyanızı bir parçası olarak indirmek ve kullanmak için aşağıdakileri kullanın:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Aşağıdaki yardımcılar betiğinizdeki kullanım için kullanılabilir:

| Yardımcı kullanım | Description |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Kaynak URI 'den belirtilen dosya yoluna bir dosya indirir. Varsayılan olarak, varolan bir dosyanın üzerine yazmaz. |
| `untar_file TARFILE DESTDIR` |Bir tar dosyasını (kullanarak `-xf` ) hedef dizine ayıklar. |
| `test_is_headnode` |Betik bir küme baş düğümünde çalıştırıldıysa 1 döndürür. Aksi takdirde, 0. |
| `test_is_datanode` |Geçerli düğüm bir veri (çalışan) düğümüdür, 1 döndürür. Aksi takdirde, 0. |
| `test_is_first_datanode` |Geçerli düğüm ilk veri (çalışan) düğümüdür (workernode0 adlı), bir 1 döndürür. Aksi takdirde, 0. |
| `get_headnodes` |Kümedeki baş tam etki alanı adını döndürün. Adlar virgülle ayrılır. Hatada boş bir dize döndürüldü. |
| `get_primary_headnode` |Birincil headnode 'un tam etki alanı adını alır. Hatada boş bir dize döndürüldü. |
| `get_secondary_headnode` |İkincil headnode 'un tam etki alanı adını alır. Hatada boş bir dize döndürüldü. |
| `get_primary_headnode_number` |Birincil headnode 'un sayısal sonekini alır. Hatada boş bir dize döndürüldü. |
| `get_secondary_headnode_number` |İkincil headnode 'un sayısal sonekini alır. Hatada boş bir dize döndürüldü. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Ortak kullanım desenleri

Bu bölüm, kendi özel betiğinizi yazarken çalıştırabileceğiniz bazı yaygın kullanım desenlerinin uygulanması hakkında rehberlik sağlar.

### <a name="passing-parameters-to-a-script"></a>Parametreleri bir betiğe geçirme

Bazı durumlarda, betiğinizin parametreleri gerekebilir. Örneğin, ambarı REST API kullanılırken küme için yönetici parolası gerekebilir.

Betiğe geçirilen parametreler *Konumsal parametreler* olarak bilinir ve `$1` ilk parametre için, `$2` ikincisi için atanır ve bu şekilde-açık. `$0` betiğin adını içerir.

Komut dosyasına parametre olarak geçirilen değerler tek tırnak (') içine alınmalıdır. Bunun yapılması, geçen değerin değişmez değer olarak değerlendirilmesini sağlar.

### <a name="setting-environment-variables"></a>Ortam değişkenlerini ayarlama

Bir ortam değişkenini ayarlamak aşağıdaki ifade tarafından gerçekleştirilir:

```bash
VARIABLENAME=value
```

Yukarıdaki örnekte, `VARIABLENAME` değişkenin adıdır. Değişkenine erişmek için kullanın `$VARIABLENAME` . Örneğin, bir Konumsal parametre tarafından belirtilen bir değeri parola adlı bir ortam değişkeni olarak atamak için aşağıdaki ifadeyi kullanın:

```bash
PASSWORD=$1
```

Bilgilere sonraki erişim daha sonra kullanabilir `$PASSWORD` .

Betik içinde ayarlanan ortam değişkenleri yalnızca betiğin kapsamı içinde bulunur. Bazı durumlarda, komut dosyası tamamlandıktan sonra devam edecek sistem genelinde ortam değişkenleri eklemeniz gerekebilir. Sistem genelinde ortam değişkenleri eklemek için, değişkenini öğesine ekleyin `/etc/environment` . Örneğin, aşağıdaki ifade şunları ekler `HADOOP_CONF_DIR` :

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Özel betiklerin depolandığı konumlara erişim

Bir kümeyi özelleştirmek için kullanılan betikler aşağıdaki konumlardan birinde depolanmalıdır:

* Kümeyle ilişkilendirilmiş bir __Azure depolama hesabı__ .

* Kümeyle ilişkili __ek bir depolama hesabı__ .

* __Genel olarak okunabilir BIR URI__. Örneğin, OneDrive, Dropbox veya diğer dosya barındırma hizmeti 'nde depolanan verilerin URL 'SI.

* HDInsight kümesiyle ilişkili bir __Azure Data Lake Storage hesabı__ . HDInsight ile Azure Data Lake Storage kullanma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: HDInsight 'ta kümeleri ayarlama](./hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Hizmet sorumlusu HDInsight 'ın erişim için kullandığı Data Lake Storage, betikte okuma erişiminin olması gerekir.

Komut dosyası tarafından kullanılan kaynaklar da genel kullanıma açık olmalıdır.

Dosyaları Azure depolama hesabında depolama veya Azure Data Lake Storage, hem Azure ağı içinde hem de hızlı erişim sağlar.

> [!NOTE]  
> Komut dosyasına başvurmak için kullanılan URI biçimi, kullanılan hizmete bağlı olarak farklılık gösterir. HDInsight kümesiyle ilişkili depolama hesapları için `wasb://` veya kullanın `wasbs://` . Genel olarak okunabilen URI 'Ler için `http://` veya kullanın `https://` . Data Lake Storage için kullanın `adl://` .

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Betik eylemi dağıtmaya yönelik denetim listesi

Aşağıda, bir betiği dağıtmaya hazırlanırken yapılacak adımlar verilmiştir:

* Özel betikleri içeren dosyaları, dağıtım sırasında küme düğümleri tarafından erişilebilen bir yere yerleştirin. Örneğin, kümenin varsayılan depolaması. Dosyalar, genel olarak okunabilen barındırma hizmetlerinde de depolanabilir.
* Betiğin ıdempotent olduğunu doğrulayın. Bunun yapılması betiğin aynı düğümde birden çok kez yürütülmesini sağlar.
* Betikler tarafından kullanılan indirilen dosyaları tutmak için/tmp geçici dosya dizinini kullanın ve ardından betikler yürütüldükten sonra bunları temizleyin.
* İşletim sistemi düzeyi ayarları veya Hadoop hizmeti yapılandırma dosyaları değiştirilirse, HDInsight hizmetlerini yeniden başlatmak isteyebilirsiniz.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Betik eylemi çalıştırma

Aşağıdaki yöntemleri kullanarak HDInsight kümelerini özelleştirmek için betik eylemleri kullanabilirsiniz:

* Azure portalı
* Azure PowerShell
* Azure Resource Manager şablonları
* HDInsight .NET SDK 'Sı.

Her bir yöntemi kullanma hakkında daha fazla bilgi için bkz. [betik eylemini kullanma](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Özel Betik örnekleri

Microsoft, bir HDInsight kümesine bileşen yüklemek için örnek betikler sağlar. Bkz. [HDInsight kümelerinde ton 'u](hdinsight-hadoop-hue-linux.md) örnek betik eylemi olarak yükleyip kullanma.

## <a name="troubleshooting"></a>Sorun giderme

Aşağıda, geliştirmiş olduğunuz betikleri kullanırken karşılaşabileceğiniz hatalar verilmiştir:

**Hata**: `$'\r': command not found` . Bazen sonrasında `syntax error: unexpected end of file` .

*Neden*: Bu hata, bir betikteki satırlar CRLF ile bitmediğinde oluşur. UNIX sistemleri, son satır olarak yalnızca LF bekler.

Bu sorun çoğu zaman, komut dosyası bir Windows ortamında yazıldığı için, CRLF Windows üzerinde birçok metin Düzenleyicisi için biten ortak bir satır olduğundan, çoğu zaman oluşur.

*Çözüm*: metin düzenleyicinizde bir seçenek varsa, satır sonu için UNIX BIÇIMI veya LF 'yi seçin. CRLF 'yi bir LF olarak değiştirmek için bir UNIX sisteminde aşağıdaki komutları da kullanabilirsiniz:

> [!NOTE]  
> Aşağıdaki komutlar, CRLF satır sonlarını LF olarak değiştirmeleri için kabaca eşdeğerdir. Sisteminizde bulunan yardımcı programlara göre birini seçin.

| Komut | Notlar |
| --- | --- |
| `unix2dos -b INFILE` |Özgün dosya ile yedeklenir. BAK uzantısı |
| `tr -d '\r' < INFILE > OUTFILE` |ÇıKıŞDOSYASı yalnızca LF sonları olan bir sürüm içeriyor |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Dosyayı doğrudan değiştirir |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |ÇıKıŞDOSYASı yalnızca LF sonları olan bir sürüm içeriyor. |

**Hata**: `line 1: #!/usr/bin/env: No such file or directory` .

*Neden*: komut dosyası UTF-8 olarak bir bayt sırası IŞARETIYLE (BOM) kaydedildiğinde bu hata oluşur.

*Çözüm*: dosyayı ASCII ya da bir BOM olmadan UTF-8 olarak kaydedin. Ayrıca, bir Linux veya UNIX sisteminde, BOM olmadan bir dosya oluşturmak için aşağıdaki komutu kullanabilirsiniz:

```bash
awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE
```

`INFILE`Bom içeren dosyayla değiştirin. `OUTFILE` , BOM olmadan betiği içeren yeni bir dosya adı olmalıdır.

## <a name="next-steps"></a><a name="seeAlso"></a>Sonraki adımlar

* [Betik eylemi kullanarak HDInsight kümelerini özelleştirmeyi](hdinsight-hadoop-customize-cluster-linux.md) öğrenin
* HDInsight 'ı yöneten .NET uygulamaları oluşturma hakkında daha fazla bilgi edinmek için [HDInsight .NET SDK başvurusunu](/dotnet/api/overview/azure/hdinsight) kullanın
* HDInsight kümelerinde yönetim eylemleri gerçekleştirmek için REST 'i nasıl kullanacağınızı öğrenmek için [hdınsight REST API](/rest/api/hdinsight/) kullanın.