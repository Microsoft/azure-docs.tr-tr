---
title: Dağıtılmış derin öğrenme için Azure HDInsight Spark Caffe kullanma
description: Azure HDInsight 'ta dağıtılmış derin öğrenme için Apache Spark 'da Caffe kullanın.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: e5988bf1955502d89cc31bcc30672de983a399ec
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083336"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Dağıtılmış derin öğrenme için Azure HDInsight Spark Caffe kullanma

## <a name="introduction"></a>Giriş

Derin öğrenme, sağlık hizmetleri 'nden üretime ulaşım 'e kadar olan her şeyi ve daha fazlasını etkiliyor. Şirketler, [görüntü sınıflandırması](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [konuşma tanıma](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), nesne tanıma ve makine çevirisi gibi sabit sorunları çözmeye yönelik derin öğrenmeleri kapatıyor.

[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Apache mxnet](https://mxnet.apache.org/), Theano, vb. gibi [popüler birçok çerçeve](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software)vardır. [Caffe](https://caffe.berkeleyvision.org/) , en yoğun sembolik olmayan (zorunlu) sinir ağ çerçevelerinden biridir ve bilgisayar vizyonu dahil pek çok alanda yaygın olarak kullanılır. Ayrıca, [Caffeonspark](https://github.com/yahoo/CaffeOnSpark) , caffe 'yi Apache Spark ile birleştirerek, bu durumda derin öğrenimi mevcut bir Hadoop kümesinde kolayca kullanılabilir. Ayrıntılı öğrenmeyi Spark ETL işlem hatları ile birlikte kullanabilirsiniz, sistem karmaşıklığını azaltır ve tüm çözüm öğrenimi için gecikme süresi.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) , Apache Spark, Apache Hive, Apache Hadoop, Apache hbase, Apache Storm, Apache Kafka ve ml Hizmetleri için iyileştirilmiş açık kaynaklı analitik kümeler sağlayan bir bulut Apache Hadoop sunumudur. HDInsight,% 99,9 SLA ile desteklenir. Bu büyük veri teknolojilerinin ve ISV uygulamalarının her biri, kuruluşlar için güvenlik ve izleme özellikli yönetilen kümeler olarak kolayca dağıtılabilir.

Bu makalede, bir HDInsight kümesi için [Spark üzerinde Caffe](https://github.com/yahoo/CaffeOnSpark) 'nin nasıl yükleneceği gösterilmektedir. Bu makalede ayrıca yerleşik kapsamlı öğrenmeyi kullanarak CPU 'Larda HDInsight Spark kullanarak nasıl kullanılacağı gösterilmektedir.

Görevi gerçekleştirmek için dört adım vardır:

1. Gerekli bağımlılıkları tüm düğümlere yükler
2. Baş düğüm üzerinde HDInsight için Spark Caffe oluşturun
3. Gerekli kitaplıkları tüm çalışan düğümlerine dağıt
4. Bir Caffe modeli oluşturun ve dağıtılmış bir şekilde çalıştırın.

HDInsight PaaS çözümü olduğundan harika platform özellikleri sunar; bu nedenle bazı görevleri gerçekleştirmek kolaydır. Bu blog Gönderinizde kullanılan özelliklerden birine, küme düğümlerini (baş düğüm, çalışan düğümü veya kenar düğümü) özelleştirmek için kabuk komutlarını yürütebileceğiniz [betik eylemi](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)denir.

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>1\. Adım: gerekli bağımlılıkları tüm düğümlere yükler

Başlamak için bağımlılıkları yüklemeniz gerekir. Caffe sitesi ve [Caffeonspark sitesi](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) , Yarn modunda Spark için bağımlılıkları yüklemeye yönelik bazı yararlı wiki olanakları sunmaktadır. HDInsight Ayrıca YARN modunda Spark kullanır. Ancak, HDInsight platformu için birkaç bağımlılık eklemeniz gerekir. Bunu yapmak için, bir betik eylemi kullanır ve tüm baş düğümlerde ve çalışan düğümlerinde çalıştırırsınız. Bu bağımlılıklar diğer paketlere de bağlı olduğundan, bu betik eylemi yaklaşık 20 dakika sürer. Bunu bir GitHub konumu veya varsayılan BLOB depolama hesabı gibi HDInsight kümeniz tarafından erişilebilen bir konuma koymanız gerekir.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in https://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"

Betik eyleminde iki adım vardır. İlk adım gereken tüm kitaplıkları yüklemektir. Bu kitaplıklar, hem Caffe 'nin (gflags, glog gibi) derlenmesi ve Caffe (sayısal tuş takımı gibi) için gerekli kitaplıkları içerir. CPU iyileştirmesi için libatlas kullanıyorsunuz, ancak MKL veya CUDA (GPU için) gibi diğer en iyi duruma getirme kitaplıklarını yüklerken CaffeOnSpark wiki 'yi her zaman takip edebilirsiniz.

İkinci adım, çalışma zamanı sırasında Caffe için proto2.5.0 yükleme, derleme ve yükleme. Protoarabellek 2.5.0 [gereklidir](https://github.com/yahoo/CaffeOnSpark/issues/87), ancak bu sürüm Ubuntu 16 üzerinde bir paket olarak kullanılamaz, bu yüzden bunu kaynak kodundan derlemeniz gerekir. Ayrıca, Internet 'te nasıl derleneceği hakkında birkaç kaynak vardır. Daha fazla bilgi için [buraya](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html)bakın.

Başlamak için, bu betik eylemini yalnızca tüm çalışan düğümleri ve baş düğümler (HDInsight 3,5 için) için kümenize çalıştırabilirsiniz. Betik eylemlerini mevcut bir kümede çalıştırabilir ya da küme oluşturma sırasında betik eylemleri kullanabilirsiniz. Betik eylemleri hakkında daha fazla bilgi için [buradaki](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)belgelere bakın.

![Bağımlılıkları yüklemek için betik eylemleri](./media/apache-spark-deep-learning-caffe/submit-script-action.png)

## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>2\. Adım: baş düğümde HDInsight için Apache Spark Caffe oluşturma

İkinci adım, headnode üzerinde Caffe oluşturmak ve ardından derlenen kitaplıkları tüm çalışan düğümlerine dağıtmaktır. Bu adımda, [baş düğümüne içinde SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)oluşturmanız gerekir. Bundan sonra [Caffeonspark derleme işlemini](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn)izlemeniz gerekir. Aşağıda, CaffeOnSpark 'ı birkaç ek adımla derlemek için kullanabileceğiniz komut dosyası verilmiştir.

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

CaffeOnSpark belgelerinin yazdıkından daha fazlasını yapmanız gerekebilir. Değişiklikler şunlardır:
- Yalnızca CPU olarak değiştirin ve bu belirli amaçla libatlas kullanın.
- Veri kümelerini, daha sonra kullanılmak üzere tüm çalışan düğümlerinin erişebileceği paylaşılan bir konum olan BLOB depolamaya koyun.
- Derlenen Caffe kitaplıklarını BLOB depolamaya yerleştirin ve daha sonra bu kitaplıkları, ek derleme süresinin önüne geçmek için betik eylemleri kullanarak tüm düğümlere kopyalayın.

### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Sorun giderme: bir ant BuildException oluştu: exec döndürüldü: 2

İlk olarak CaffeOnSpark oluşturmaya çalışırken şöyle diyor

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

Doğru bağımlılıklara sahip olduğunuz sürece bu sorunu çözmek için "temiz yap" ve ardından "derlemeyi oluştur" öğesini çalıştırın.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Sorun giderme: Maven depo bağlantısı zaman aşımı

Bazen Maven, aşağıdaki kod parçacığına benzer bir bağlantı zaman aşımı hatası verir:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Birkaç dakika sonra yeniden denemeniz gerekir.

### <a name="troubleshooting-test-failure-for-caffe"></a>Sorun giderme: Caffe için test hatası

CaffeOnSpark için son denetimi yaparken büyük olasılıkla bir test hatası görürsünüz. Bu muhtemelen UTF-8 kodlaması ile ilgilidir, ancak Caffe 'nin kullanımını etkilememelidir.

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>3\. Adım: gerekli kitaplıkları tüm çalışan düğümlerine dağıtma

Sonraki adım, kitaplıkları (temel olarak CaffeOnSpark/Caffe-public/dağıt/lib/and CaffeOnSpark/Caffe-Distri/dağıt/lib/) tüm düğümlere dağıtmaktır. 2\. adımda bu kitaplıkları BLOB depolamaya yerleştirir ve bu adımda, betik eylemlerini kullanarak tüm baş düğümlere ve çalışan düğümlerine kopyalayabilirsiniz.

Bunu yapmak için, aşağıdaki kod parçacığında gösterildiği gibi bir betik eylemi çalıştırın:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Kümenize özgü doğru konuma işaret ettiğinizden emin olun.

2\. adımda, tüm düğümlerin erişebileceği BLOB depolama alanına yerleştirmeniz, bu adımda bunu yalnızca tüm düğümlere kopyalamanız yeterlidir.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>4\. Adım: bir Caffe modeli oluşturun ve dağıtılmış bir şekilde çalıştırın

Caffe, önceki adımları çalıştırdıktan sonra yüklenir. Sonraki adım bir Caffe modeli yazmaktır. 

Caffe, model oluştururken yalnızca bir yapılandırma dosyası tanımlamanız ve herhangi bir kodlama yapmadan (çoğu durumda) bir "ifade eden mimari" kullanıyor. Şimdi oraya göz atalım. 

Eğitmeniz gereken model, bir model oluşturma eğitimi için örnek bir modeldir. El ile yazılan basamakların veri tabanı, 60.000 örnek bir eğitim kümesine ve bir 10.000 örnek test kümesine sahiptir. NıST 'den kullanılabilen daha büyük bir kümenin alt kümesidir. Rakamlar normal boyuttadır ve sabit boyutlu bir görüntüde ortalanmıştır. CaffeOnSpark, veri kümesini indirmek ve doğru biçime dönüştürmek için bazı betikler içerir.

CaffeOnSpark, bazı ağ topolojileri örneği sağlar. Ağ mimarisini bölmek için iyi bir tasarıma sahiptir (ağın topolojisi) ve iyileştirmesi. Bu durumda, iki dosya gerekir:

"çözücü" dosyası ($ {CAFFE_ON_SPARK}/Data/lenet_memory_solver. prototxt), iyileştirme ve parametre güncelleştirmelerini oluşturmak için kullanılır. Örneğin, CPU veya GPU 'nun kullanılıp kullanılmadığını, itici güç ne olduğunu, kaç yinelemenin olduğunu tanımlar. Ayrıca programın kullanması gereken neuron ağ topolojisini tanımlar (Bu, ihtiyacınız olan ikinci dosyadır). Çözücü hakkında daha fazla bilgi için bkz. [Caffe belgeleri](https://caffe.berkeleyvision.org/tutorial/solver.html).

Bu örnekte, GPU yerine CPU kullandığından, son satırı şu şekilde değiştirmelisiniz:

    # solver mode: CPU or GPU
    solver_mode: CPU

![HDInsight Caffe yapılandırma örneği](./media/apache-spark-deep-learning-caffe/caffe-configuration1.png
)

Diğer satırları gerektiği gibi değiştirebilirsiniz.

İkinci dosya ($ {CAFFE_ON_SPARK}/Data/lenet_memory_train_test. prototxt), neuron ağının nasıl göründüğünü ve ilgili giriş ve çıkış dosyasını tanımlar. Eğitim verilerinin konumunu yansıtmak için dosyayı da güncelleştirmeniz gerekir. Lenet_memory_train_test. prototxt ' de aşağıdaki parçayı değiştirin (kümenize özgü doğru konuma işaret etmeniz gerekir):

- "File:/Users/Mridul/bigml/demodl/mnist_train_lmdb" değerini "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb" olarak değiştirin
- "File:/Users/Mridul/bigml/demodl/mnist_test_lmdb/" değerini "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb" olarak değiştirin

![HDInsight Caffe yapılandırma örneği yeniden](./media/apache-spark-deep-learning-caffe/caffe-configuration2.png)

Ağı tanımlama hakkında daha fazla bilgi için, [MNIST veri kümesindeki Caffe belgelerini](https://caffe.berkeleyvision.org/gathered/examples/mnist.html) denetleyin

Bu makalenin amacına yönelik olarak, bu ilgili bir örnek kullanırsınız. Baş düğümden aşağıdaki komutları çalıştırın:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Yukarıdaki komut, her YARN kapsayıcısına gerekli dosyaları (lenet_memory_solver. prototxt ve lenet_memory_train_test. prototxt) dağıtır. Komut ayrıca her Spark sürücüsünün/yürütücsürümünün ilgili yolunu LD_LIBRARY_PATH olarak ayarlar. LD_LIBRARY_PATH, önceki kod parçacığında tanımlanır ve CaffeOnSpark kitaplıklarının bulunduğu konuma işaret eder. 

## <a name="monitoring-and-troubleshooting"></a>İzleme ve sorun giderme

YARN küme modunu kullandığınızdan, Spark sürücüsü rastgele bir kapsayıcıya (ve rastgele bir çalışan düğümüne) zamanlanabileceği için, konsolda yalnızca şunun gibi bir şey çıktısını görmeniz gerekir:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Ne olduğunu biliyorsanız, genellikle Spark sürücüsünün günlüğünü almanız gerekir, bu da daha fazla bilgi içerir. Bu durumda, ilgili YARN günlüklerini bulmak için YARN Kullanıcı arabirimine gitmeniz gerekir. YARN Kullanıcı arabirimini şu URL ile edinebilirsiniz:

    https://yourclustername.azurehdinsight.net/yarnui

![Apache Yarn Zamanlayıcı tarayıcı görünümü](./media/apache-spark-deep-learning-caffe/apache-yarn-window-1.png)

Bu belirli uygulama için ayrılan kaynak sayısına göz atabilirsiniz. "Scheduler" bağlantısına tıklayabilirsiniz ve bu uygulama için çalışan dokuz kapsayıcı olduğunu görürsünüz. YARN 'nin sekiz yürütme sağlamasını ve başka bir kapsayıcı de sürücü işlemi için olduğunu sorabilirsiniz. 

![HDI Apache YARN Zamanlayıcı görünümü](./media/apache-spark-deep-learning-caffe/apache-yarn-scheduler.png)

Başarısızlık durumunda sürücü günlüklerini veya kapsayıcı günlüklerini denetlemek isteyebilirsiniz. Sürücü günlükleri için, YARN Kullanıcı arabiriminde uygulama KIMLIĞI ' ne tıklayabilir ve ardından "Günlükler" düğmesine tıklayabilirsiniz. Sürücü günlükleri stderr 'e yazılır.

![Apache Yarn pencere tarayıcısı görünümü](./media/apache-spark-deep-learning-caffe/apache-yarn-window-2.png)

Örneğin, daha fazla sayıda yürütme ayırdığını belirten sürücü günlüklerinden aşağıdaki bazı hataları görebilirsiniz.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

Bazen sorun, sürücüler yerine yürüticilere de gerçekleşebilir. Bu durumda, kapsayıcı günlüklerini denetlemeniz gerekir. Her zaman kapsayıcı günlüklerini alabilir ve ardından başarısız kapsayıcıyı alabilirsiniz. Örneğin, Caffe çalıştırırken bu hatayı karşılayabilirsiniz.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

Bu durumda, başarısız kapsayıcı KIMLIĞINI almanız gerekir (Yukarıdaki örnekte, container_1485916338528_0008_05_000005). Ardından şunu çalıştırmanız gerekir 

    yarn logs -containerId container_1485916338528_0008_03_000005

. Kapsayıcı başarısızlığını denetledikten sonra, lenet_memory_solver. prototxt içinde GPU modunun (Bunun yerine CPU modunu kullanmanız gerekir) kullanılması neden olur.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.

## <a name="getting-results"></a>Sonuçlar alınıyor

8 yürüticileri ayırdığından ve ağ topolojisi basit olduğundan, sonucu çalıştırmak için yalnızca 30 dakika sürer. Komut satırından, modeli wasb:///mnist.model 'e yerleştiristediğinizi görebilir ve sonuçları wasb:///mnist_features_result adlı bir klasöre yerleştirebilirsiniz.

Şu şekilde çalıştırarak sonuçları alabilirsiniz

    hadoop fs -cat hdfs:///mnist_features_result/*

ve sonuç şöyle görünür:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

Sampleıd, MNIST veri kümesindeki KIMLIĞI temsil eder ve etiket modelin tanımladığı sayıdır.

## <a name="conclusion"></a>Sonuç

Bu belgede, basit bir örnek çalıştırmaya yönelik CaffeOnSpark 'ı yüklemeye çalıştınız. HDInsight, tam olarak yönetilen bir bulut dağıtılmış işlem platformudur ve büyük veri kümesinde makine öğrenimi ve gelişmiş analiz iş yüklerini çalıştırmak için en iyi yerdir ve dağıtılmış derin öğrenme için, HDInsight Spark üzerinde Caffe 'yi kullanarak derin öğrenme yapabilirsiniz görevlerinize.

## <a name="seealso"></a>Ayrıca bkz.

* [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>Senaryolar

* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
