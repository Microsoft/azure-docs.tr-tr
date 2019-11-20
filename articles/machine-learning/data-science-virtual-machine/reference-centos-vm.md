---
title: 'Başvuru: CentOS DSVM'
description: CentOS Veri Bilimi Sanal Makinesi bulunan araçlarla ilgili ayrıntılar
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 0f71a8af2f7d2cfbfe43c0cfcc84cc7c08109c32
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493720"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Başvuru: CentOS (Linux) Veri Bilimi Sanal Makinesi

Linux Veri Bilimi Sanal Makinesi (DSVM), bir CentOS tabanlı Azure sanal makinedir. Linux DSVM, veri analizi ve makine öğrenimi için kullanabileceğiniz bir önceden yüklenmiş araçlar koleksiyonuyla birlikte gelir. 

Bir Linux DSVM 'ye dahil edilen anahtar yazılım bileşenleri şunlardır:

* Linux CentOS dağıtım işletim sistemi.
* Microsoft Machine Learning Server.
* Popüler veri analizi kitaplıkları da dahil olmak üzere Anaconda Python dağıtımı (sürüm 3,5 ve 2,7).
* JuliaPro, Julia dilinin ve popüler bilimsel ve veri analizi kitaplıklarının seçkin bir dağılımı.
* Spark tek başına örneği ve tek düğümlü Hadoop (, YARN).
* JupyterHub, R, Python, PySpark ve Julia kernels 'yi destekleyen çok kullanıcılı bir Jupyter Not defteri sunucusudur.
* Azure Depolama Gezgini.
* Azure CLı, Azure kaynaklarını yönetmeye yönelik Azure komut satırı arabirimi.
* PostgresSQL veritabanı.
* Machine Learning araçları:
  * Microsoft Research 'tan derin bir öğrenme yazılım araç seti olan [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (cntk).
  * Çevrimiçi, karma, allazaltma, indirimleri, learning2search, etkin ve etkileşimli öğrenme gibi teknikleri destekleyen hızlı bir makine öğrenimi sistemi olan [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit).
  * [Xgboost](https://xgboost.readthedocs.org/en/latest/), hızlı ve doğru şekilde artırılmış ağaç uygulamasını sağlayan bir araçtır.
  * [Rattle](https://togaware.com/rattle/), veri analizi ve makine öğrenimi ile R 'yi kolay bir şekilde kullanmaya başlamanıza olanak tanıyan bir araçtır. Rattle, otomatik R kod üretimi kullanarak hem GUI tabanlı veri araştırması hem de modelleme sağlar.
* Java, Python, Node. js, Ruby ve PHP 'de Azure SDK.
* Azure Machine Learning ve diğer Azure hizmetlerinde kullanılacak olan R ve Python kitaplıkları.
* Geliştirme araçları ve düzenleyiciler (RStudio, Pylt, IntelliJ, Emacs, gedit, vi).

Veri bilimi, bir dizi görev için yineleme içerir:

1. Verileri bulun, yükleyin ve ön işleme yapın.
1. Yapı ve test modelleri.
1. Akıllı uygulamalardaki tüketim modellerini dağıtın.

Veri bilimcileri, bu görevleri tamamlamaya yönelik çeşitli araçlar kullanır. Yazılımın doğru sürümlerini bulmak ve ardından yazılımı indirmek, derlemek ve yüklemek zaman alabilir.

Linux DSVM bu yükü önemli ölçüde kolaylaştırabilir. Analiz projenize gitmek için Linux DSVM 'yi kullanın. Linux DSVM, R, Python, SQL, Java ve C++gibi çeşitli dillerdeki görevler üzerinde çalışmanıza yardımcı olur. Çakışan Küreler, kodunuzun geliştirilmesi ve test edilmesi için kullanımı kolay bir IDE sağlar. DSVM 'ye dahil edilen Azure SDK, Microsoft bulut platformu için Linux üzerinde çeşitli hizmetler kullanarak uygulamalarınızı oluşturmanıza yardımcı olur. Ruby, Perl, PHP ve Node. js dahil diğer diller önceden yüklenir.

DSVM görüntüsü için yazılım ücreti yok. Yalnızca DSVM görüntüsüyle sağladığınız sanal makinenin boyutuna bağlı olarak değerlendirilen Azure donanım kullanım ücretleri ödersiniz. İşlem ücretleri hakkında daha fazla bilgi için bkz. Azure Marketi 'nde [Linux için veri bilimi sanal makinesi (CentOS) listesi](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) .


## <a name="machine-learning-server"></a>Machine Learning Server

R, veri analizi ve makine öğrenimi için en popüler dillerden biridir. Analiz etmeniz için R 'yi kullanmak istiyorsanız, DSVM 'nin Microsoft R Open ve Math Kernel kitaplığı ile Machine Learning Server vardır. Matematik Çekirdek Kitaplığı, analitik algoritmalarda ortak matematik işlemlerini iyileştirir. R Open, CRAN R ile tamamen uyumludur. CRAN içinde Yayınlanan R kitaplıklarının herhangi biri R Open 'a yüklenebilir. 

R modellerini Web Hizmetleri 'nde ölçeklendirmek ve kullanıma almak için Machine Learning Server kullanabilirsiniz. R programlarınızı, RStudio, vi veya Emacs gibi varsayılan düzenleyicilerden birinde düzenleyebilirsiniz. Emacs Düzenleyicisi DSVM 'ye önceden yüklenir. Emacs ESS (Emacs Hoparlörks Istatistikleri) paketi Emacs düzenleyicisinde R dosyalarıyla çalışmayı basitleştirir.

R konsolunu açmak için, kabukta **r**yazın. Bu komut sizi etkileşimli bir ortama götürür. R programınızı geliştirmek için, genellikle Emacs veya VI gibi bir düzenleyici kullanır ve ardından komut dosyalarını R. RStudio 'da çalıştırmak, R programınızı geliştirmek için tam bir grafik IDE sunar.

[İlk 20 R paketini](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) yüklemek için kullanabileceğiniz bir R BETIĞI dsvm 'ye dahildir. R etkileşimli arabiriminizdeki bu betiği çalıştırabilirsiniz. Daha önce belirtildiği gibi, bu arabirimi açmak için kabukta **R**yazın.  

## <a name="python"></a>Python

Anaconda Python, Python 3,5 ve 2,7 ortamları ile birlikte yüklenir. 2,7 ortamına _kök_ adı verilir ve 3,5 ortamı _py35_olarak adlandırılır. Bu dağıtım, en popüler matematik, mühendislik ve veri analizi paketlerinin 300 hakkında temel Python 'u içerir.

Py35 ortamı varsayılandır. Kök (2,7) ortamını etkinleştirmek için şu komutu kullanın:

```bash
source activate root
```

Py35 ortamını yeniden etkinleştirmek için şu komutu kullanın:

```bash
source activate py35
```

Python etkileşimli oturumunu çağırmak için, kabukta **Python**girin. 

Conda veya PIP kullanarak ek Python kitaplıkları kurun. PIP için, varsayılan değer istemiyorsanız, önce doğru ortamı etkinleştirin:

```bash
source activate root
pip install <package>
```

Ya da PIP 'nin tam yolunu belirtin:

```bash
/anaconda/bin/pip install <package>
```

Conda 'nın her zaman ortam adını (py35 veya root) belirtmeniz gerekir:

```bash
conda install <package> -n py35
```

Grafik arabiriminiz varsa veya X11 iletmeyi ayarladıysanız, Pydüğme Python IDE 'yi açmak için **pydüğme** girebilirsiniz. Varsayılan metin düzenleyicilerini kullanabilirsiniz. Ayrıca, Anaconda Python dağıtımları ile paketlenmiş bir Python IDE olan Spyder kullanabilirsiniz. Spyder bir grafik masaüstü veya X11 iletme gerektirir. Grafik masaüstünde Spyder kısayolu bulunur.

## <a name="jupyter-notebook"></a>Jupyter Notebook

Anaconda dağıtımı, kod ve analiz paylaşmak için bir ortam Jupyter Notebook de birlikte gelir. Jupyıterhub üzerinden Jupyter Notebook erişin. Yerel Linux Kullanıcı adınızı ve parolanızı kullanarak oturum açın.

Jupyter Notebook sunucusu Python 2, Python 3 ve R kernels ile önceden yapılandırılmıştır. Tarayıcıyı açmak ve Jupyter Notebook sunucusuna erişmek için **Jupyter Notebook** masaüstü simgesini kullanın. DSVM 'ye SSH veya X2Go istemcisi aracılığıyla eriştiğinizde, https:\//localhost: 8000/konumundaki Jupyter Notebook sunucusuna da erişebilirsiniz.

> [!NOTE]
> Herhangi bir sertifika uyarısı alırsanız devam edin.

Jupyter Not defteri sunucusuna herhangi bir konaktan erişebilirsiniz. **Https:\//\<DSVM DNS adını veya IP adresini girin\>: 8000/** .

> [!NOTE]
> DSVM sağlandığında, bağlantı noktası 8000 güvenlik duvarında varsayılan olarak açılır. 

Microsoft, bir tane Python ve diğeri R 'de örnek Not defterleri paketlenir. Yerel Linux Kullanıcı adınızı ve parolanızı kullanarak Jupyter Notebook kimlik doğrulamasından geçtikten sonra Jupyter Notebook giriş sayfasındaki örneklerin bağlantısını görebilirsiniz. Yeni bir not defteri oluşturmak için **Yeni**' yi seçin ve ardından kullanmak istediğiniz dil çekirdeğini seçin. **Yeni** düğmesini görmüyorsanız, sol üstteki **Jupyter** simgesini seçerek not defteri sunucusunun ana sayfasına gidin.

## <a name="spark-standalone"></a>Spark tek başına 

Spark bağımsız modunun bir örneği, bunları test etmeden ve bunları büyük kümelerle dağıtmadan önce yerel olarak Spark uygulamaları geliştirmenize yardımcı olmak için Linux DSVM 'ye önceden yüklenir. 

PySpark programlarını Jupyıter çekirdeği aracılığıyla çalıştırabilirsiniz. Jupyter açtığınızda, **Yeni** düğmesini seçin ve kullanılabilir çekirdekler listesini görmeniz gerekir. **Spark-Python** , Python dilini kullanarak Spark uygulamaları oluşturmanıza olanak sağlayan pyspark çekirdeğidir. Spark programınızı oluşturmak için Pydüğme veya Spyder gibi bir Python IDE de kullanabilirsiniz. 

Bu tek başına örneğinde, Spark Stack çağıran istemci programında çalışır. Bu özellik, Spark kümesinde geliştirme ile karşılaştırıldığında sorun gidermeyi daha hızlı ve kolay hale getirir.

Jupyter bir örnek PySpark Not defteri sağlar. Bu dosyayı jupi 'nın giriş dizini ($HOME/Notebooks/mini can ml/pyspark) altında can ml dizininde bulabilirsiniz. 

Spark için R 'de Programlama yapıyorsanız Machine Learning Server, parlak r veya parlak LYR ' yi kullanabilirsiniz. 

Machine Learning Server bir Spark bağlamında çalıştırmadan önce, yerel bir tek düğümlü Hadoop ve YARN örneğini etkinleştirmek için bir kerelik Kurulum adımı yapmanız gerekir. Hadoop Hizmetleri varsayılan olarak DSVM 'de yüklüdür ancak devre dışı bırakılır. Hadoop hizmetlerini etkinleştirmek için, ilk kez kök olarak aşağıdaki komutları çalıştırın:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

`systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`çalıştırarak Hadoop ile ilgili hizmetleri durdurmanız gerekmez.

/Dsvm/samples/MRS dizini, uzak Spark bağlamında Machine Learning Server geliştirme ve test etme (DSVM 'deki tek başına Spark örneği) hakkında bir örnek sağlar.

## <a name="ides-and-editors"></a>Ides ve düzenleyiciler

VI/VıM, Emacs, gedit, Pylt, RStudio, tutulma, LaTeX ve IntelliJ gibi çeşitli kod düzenleyicilerinden seçim yapabilirsiniz. 

* gedit, tutulma, IntelliJ, R Studio ve Pyı düğmesi grafik düzenleyicilerdir. Bunları kullanmak için bir grafik masaüstünde oturum açmış olmanız gerekir. Bunları masaüstü ve uygulama menüsü kısayollarını kullanarak açarsınız.

* VIM ve Emacs metin tabanlı düzenleyicilerlerdir. Emacs 'de, ESS eklentisi paketi Emacs düzenleyicisinde R ile çalışmayı kolaylaştırır. [ESS Web sitesi](https://ess.r-project.org/)hakkında daha fazla bilgi edinebilirsiniz.

* Çakışan Küreler, birden çok dili destekleyen açık kaynaklı, genişletilebilir bir IDE 'dir. Java geliştiricileri için tutulma IDE, DSVM 'de yüklü olan sürümdür. Ortamı genişletmek için çeşitli popüler diller için eklentiler yükleyebilirsiniz. 

  Azure Toolkit for Eclipse eklentisi DSVM 'de tutulma ile birlikte yüklenir. Java gibi dilleri destekleyen tutulma geliştirme ortamını kullanarak Azure uygulamalarını oluşturmak, geliştirmek, test etmek ve dağıtmak için Azure Toolkit for Eclipse kullanabilirsiniz.

  Java için Azure SDK, DSVM 'deki Azure Toolkit for Eclipse de yüklenir. Java için Azure SDK, Java ortamının içinden farklı Azure hizmetlerine erişmenizi sağlar. 
  
  Daha fazla bilgi için bkz. [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* LaTeX, TeXLive paketiyle birlikte [Auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html)adlı bir Emacs eklenti paketi ile yüklenir. Bu paket, Emacs 'de LaTeX belgelerinizi yazmayı basitleştirir. 

## <a name="databases"></a>Veritabanları

Linux DSVM, çeşitli veritabanı ve komut satırı araçlarına erişmenizi sağlar.

### <a name="postgressql"></a>PostgresSQL

Açık kaynaklı veritabanı PostgresSQL, çalıştıran hizmetler ve ınitdb tamamlandığında DSVM 'de kullanılabilir. Veritabanları ve kullanıcılar oluşturmanız gerekir. Daha fazla bilgi için bkz. [Postgressql belgeleri](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQUIRREL SQL

SQUIRREL SQL, çeşitli veritabanlarına (SQL Server, PostgresSQL ve MySQL dahil) bağlanabilecek ve SQL sorguları çalıştırabilecek bir grafik SQL istemcidir. Bir masaüstü simgesi kullanarak, bir grafik Masaüstü oturumundan (örneğin, X2Go istemcisi aracılığıyla) SQUIRREL SQL 'i çalıştırabilirsiniz. Ya da kabuğu içinde aşağıdaki komutu kullanarak istemcisini çalıştırabilirsiniz:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

İlk kullanmadan önce, sürücülerinizi ve veritabanı diğer adlarını ayarlayın. JDBC sürücüleri/usr/share/Java/jdbcdrivers. adresinde bulunur

Daha fazla bilgi için bkz. [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>SQL Server erişmek için komut satırı araçları

SQL Server ODBC sürücü paketi ayrıca iki komut satırı aracı ile birlikte gelir:

* **bcp**: bcp Aracı, bir SQL Server örneği ve Kullanıcı tarafından belirtilen biçimdeki bir veri dosyası arasında verileri toplu olarak kopyalar. BCP aracını kullanarak çok sayıda yeni satırı SQL Server tablolarına aktarabilir veya tablolardaki verileri veri dosyalarına aktarabilirsiniz. Verileri bir tabloya aktarmak için, bu tablo için oluşturulmuş bir biçim dosyası kullanmanız gerekir. Ya da tablonun yapısını ve sütunlarının geçerli olan veri türlerini anlamanız gerekir.

  Daha fazla bilgi için bkz. [bcp Ile bağlanma](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: sqlcmd yardımcı programını kullanarak Transact-SQL deyimlerini, sistem yordamlarını ve komut dosyası dosyalarını komut istemine girebilirsiniz. Sqlcmd yardımcı programı Transact-SQL toplu işlerini yürütmek için ODBC kullanır.

  Daha fazla bilgi için bkz. [sqlcmd Ile bağlanma](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Bu araçta Linux ve Windows platformları arasında bazı farklılıklar vardır. Ayrıntılar için belgelere bakın.

### <a name="database-access-libraries"></a>Veritabanı erişim kitaplıkları

Veritabanı erişimi kitaplıkları R ve Python 'da kullanılabilir:

* R 'de, RODBC paketini veya dplyr paketini kullanarak veritabanı sunucusundaki SQL deyimlerini sorgulayabilir veya çalıştırabilirsiniz.
* Python 'da pyodbc kitaplığı, temel alınan katman olarak ODBC ile veritabanı erişimi sağlar.

## <a name="azure-tools"></a>Azure Araçları

Aşağıdaki Azure Araçları DSVM 'ye yüklenir:

* **Azure CLI**: Azure 'da kabuk komutları aracılığıyla Azure kaynakları oluşturmak ve yönetmek için komut satırı arabirimini kullanabilirsiniz. Azure araçlarını açmak için **Azure yardımı**'nı girin. Daha fazla bilgi için bkz. [Azure CLI belgeleri sayfası](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Depolama Gezgini**: Azure Depolama Gezgini, Azure depolama hesabınızda depoladığınız nesneler üzerinde gezinmek ve Azure bloblarına ve veri yüklemek için kullanabileceğiniz bir grafik aracıdır. Masaüstü kısayol simgesinden Depolama Gezgini erişebilirsiniz. Ayrıca, bunu bir kabuk isteminden **Storageexplorer**girerek açabilirsiniz. Bir X2Go istemcisinden oturum açmanız veya X11 iletme ayarlamış olmanız gerekir.
* **Azure kitaplıkları**: aşağıdaki kitaplıklar dsvm 'ye önceden yüklenir:
  
  * **Python**: Python 'daki Azure ile ilgili kitaplıklar *Azure*, *azureml*, *pydocumentdb*ve *pyodbc*. İlk üç kütüphaneden Azure depolama hizmetlerine, Azure Machine Learning ve Azure Cosmos DB (Azure 'da bir NoSQL veritabanı) erişebilirsiniz. Dördüncü kitaplık olan pyodbc (SQL Server için Microsoft ODBC sürücüsü ile birlikte), bir ODBC arabirimi kullanarak Python 'dan SQL Server, Azure SQL veritabanı ve Azure SQL veri ambarı 'na erişim sağlar. Listelenen tüm kitaplıkları görmek için **PIP listesini** girin. Bu komutu hem Python 2,7 hem de 3,5 ortamlarında çalıştırmayı unutmayın.
  * **R**: r 'deki Azure ile Ilgili kitaplıklar AZUREML ve rodbc.
  * **Java**: dsvm 'de/dsvm/sdk/AzureSDKJava dizininde Azure Java kitaplıklarının listesi bulunabilir. Anahtar kitaplıkları, SQL Server için Azure depolama ve yönetim API 'Leri, Azure Cosmos DB ve JDBC sürücülerdir.  

[Azure Portal](https://portal.azure.com) önceden yüklenmiş Firefox tarayıcısından erişebilirsiniz. Azure portal Azure kaynaklarını oluşturabilir, yönetebilir ve izleyebilirsiniz.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning, tahmine dayalı analiz çözümleri oluşturmak, dağıtmak ve paylaşmak için kullanabileceğiniz tam olarak yönetilen bir bulut hizmetidir. Azure Machine Learning Studio (klasik), denemeleri ve modellerinizi oluşturursunuz. DSVM 'deki bir Web tarayıcısından Azure Machine Learning erişmek için [Microsoft Azure Machine Learning](https://studio.azureml.net)gidin.

Azure Machine Learning Studio (klasik) ' da oturum açtıktan sonra, makine öğrenimi algoritmaları için bir mantıksal akış oluşturmak üzere deneme tuvalinden yararlanabilirsiniz. Ayrıca, Azure Machine Learning barındırılan bir Jupyter Notebook erişiminiz vardır. Not defteri, Azure Machine Learning Studio (klasik) içinde denemeleri ile sorunsuz bir şekilde çalışabilir. 

Oluşturduğunuz makine öğrenimi modellerini bir Web hizmeti arabirimine sarmalayarak oluşturun. Makine öğrenimi modelleri, herhangi bir dilde yazılmış istemcilerin bu modellerdeki tahminleri çağırmasına olanak sağlar. Daha fazla bilgi için [Machine Learning belgelerine](https://azure.microsoft.com/documentation/services/machine-learning/)bakın.

Ayrıca, modellerinizi DSVM 'de R veya Python 'da oluşturabilir ve ardından bunları Azure Machine Learning üretime dağıtabilirsiniz. Microsoft, bu işlevselliği desteklemek için R (**azureml**) ve Python (**azureml**) kitaplıklarını yükledi.

Azure Machine Learning için R ve Python modelleri dağıtma hakkında daha fazla bilgi için, bkz. [veri bilimi sanal makinesi üzerinde yapabileceğiniz on şey](vm-do-ten-things.md).

> [!NOTE]
> [Veri bilimi sanal makinesi üzerinde yapabileceğiniz on şey](vm-do-ten-things.md) , Dsvm 'nin Windows sürümü için yazılmıştır. Ancak Azure Machine Learning modelleri dağıtma hakkında bilgiler Linux DSVM için de geçerlidir.

## <a name="machine-learning-tools"></a>Machine Learning araçları

DSVM, önceden derlenmiş ve yerel olarak önceden yüklenmiş birkaç makine öğrenimi aracı ve algoritmalarıyla birlikte gelir. Bunlar:

* **Microsoft Cognitive Toolkit**: derin bir öğrenme araç seti.
* **Vowpal Wabbit**: hızlı bir çevrimiçi öğrenme algoritması.
* **Xgboost**: iyileştirilmiş, artırılmış ağaç algoritmaları sağlayan bir araç.
* **Python**: Anaconda Python, Scikit-öğren gibi kitaplıklar ile makine öğrenimi algoritmalarıyla birlikte sunulur. `pip install` komutunu kullanarak diğer kitaplıkları yükleyebilirsiniz.
* **R**: r için bir makine öğrenimi işlevleri zengin kitaplığı mevcuttur. önceden yüklenmiş kitaplıklar lm, GLM, rasgeleforest ve rpart ' ı içerir. `install.packages(<lib name>)`çalıştırarak diğer kitaplıkları yükleyebilirsiniz.

Microsoft Cognitive Toolkit, Vowpal Wabbit ve XGBoost, sonraki bölümlerde daha ayrıntılı bir şekilde ele alınmıştır.

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Bilişsel Araç Seti

Microsoft Cognitive Toolkit, açık kaynaklı bir ayrıntılı öğrenme araç seti. Bu bir komut satırı aracıdır (CNTK) ve zaten yolda.

Temel bir örnek çalıştırmak için, kabukta aşağıdaki komutları çalıştırın:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Daha fazla bilgi için bkz. [GITHUB cntk deposu](https://github.com/Microsoft/CNTK) ve [cntk wiki](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit, çevrimiçi, karma, allazaltma, indirimleri, learning2search, etkin ve etkileşimli öğrenimi gibi teknikler kullanan bir makine öğrenimi sistemidir.

Aracı temel bir örnekte çalıştırmak için aşağıdaki komutları çalıştırın:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Vowpal Wabbit tanıtım dizini diğer, daha büyük gösterileri içerir. Vowpal Wabbit hakkında daha fazla bilgi için [GitHub Vowpal Wabbit deposuna](https://github.com/JohnLangford/vowpal_wabbit) ve [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)bölümüne bakın.

### <a name="xgboost"></a>XGBoost

XGBoost kitaplığı, artırılmış (ağaç) algoritmaları için tasarlanmış ve iyileştirilmiştir. XGBoost kitaplığı amacı, ölçeklenebilir, taşınabilir ve doğru olan büyük ölçekli ağaç arttırma sağlamak için makinelerin hesaplama sınırlarını gereken aşırı uç 'e göndermektir.

XGBoost, bir komut satırı ve R kitaplığı olarak sağlanır.

R 'de XGBoost kitaplığını kullanmak için, etkileşimli bir R oturumu başlatın (kabukta **r**yazın) ve ardından kitaplığı yükleyin.

İşte, R isteminde çalıştırabileceğiniz basit bir örnek:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

XGBoost komut satırını çalıştırmak için, bu komutları kabukta çalıştırın:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Bir. model dosyası belirtilen dizine yazılır. GitHub 'da bu tanıtım örneği hakkında daha fazla bilgi için bkz. [Ikili sınıflandırma](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

XGBoost hakkında daha fazla bilgi için, bkz. [xgboost belgeleri](https://xgboost.readthedocs.org/en/latest/) ve [Xgboost GitHub deposu](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle (*R* *A*Nalitik *T*OOL *T*o *L*kazanın) GUI tabanlı veri araştırması ve modelleme kullanır. Rattle:
- Verilerin istatistiksel ve görsel özetlerini sunar.
- Kolay modellenebilir verileri dönüştürür.
- Verilerden hem denetimli hem de denetimli modeller oluşturur.
- Modellerin performansını grafik olarak gösterir.
- Yeni veri kümelerine puan koyar.
- R kodu oluşturur.
- Kullanıcı arabirimindeki, doğrudan R 'de çalıştırılabilen veya daha fazla analiz için bir başlangıç noktası olarak kullanılan işlemleri çoğaltır.

Rattle çalıştırmak için bir grafik Masaüstü oturumunda oturum açmış olmanız gerekir. Bir terminalde **r ortamını açmak için r** yazın. R isteminde aşağıdaki komutları girin:

```R
library(rattle)
rattle()
```

Bir sekme kümesi açılan grafik arabirimi açılır. Örnek bir hava durumu verisi kümesi kullanmak ve bir model oluşturmak için Rattle içindeki aşağıdaki hızlı başlangıç adımlarını kullanın. Bazı adımlarda, zaten sistemde olmayan bazı gerekli R paketlerini otomatik olarak yükleyip yüklemeniz istenir.

> [!NOTE]
> Paketi sistem dizinine (varsayılan) yüklemeye yönelik izinleriniz yoksa, kişisel kitaplığınıza paket yüklemek için R konsol pencerenizde bir istem görebilirsiniz. Bu istemler görürseniz **y**girin.

1. **Yürüt**’ü seçin.
1. Bir iletişim kutusu, örnek Hava durumu veri kümesini yüklemeniz için istemde bulunur. Örneği yüklemek için **Evet** ' i seçin.
1. **Model** sekmesini seçin.
1. Bir karar ağacı oluşturmak için **Yürüt** ' ü seçin.
1. Karar ağacını göstermek için **Çiz** ' i seçin.
1. **Orman** seçeneğini belirleyin ve sonra rastgele bir orman oluşturmak için **Yürüt** ' ü seçin.
1. **Değerlendir** sekmesini seçin.
1. **Risk** seçeneğini belirleyin ve ardından iki **risk (birikmeli)** performans grafiğini göstermek için **Yürüt** ' ü seçin.
1. Önceki işlemler için oluşturulan R kodunu göstermek üzere **günlük** sekmesini seçin. (Rattle 'in geçerli sürümündeki bir hata nedeniyle bu günlüğü, günlük metninde **dışarı aktar** önüne **#** bir karakter eklemeniz gerekir.)
1. Weather_script adlı R betiği dosyasını kaydetmek için **dışarı aktar** düğmesini seçin *.* Ana klasöre R.

Rattle ve R 'den çıkabilirsiniz. Artık oluşturulan R betiğini değiştirebilirsiniz. Ya da, komut dosyasını olduğu gibi kullanın ve Rattle Kullanıcı arabiriminde yapılan her şeyi yinelemek için dilediğiniz zaman çalıştırın. Özellikle R 'deki yeni başlayanlar için bu, basit bir grafik arabiriminde analiz ve makine öğrenimini hızlı bir şekilde yapmanın bir yoludur. bu sayede, R 'de değişiklik yapmak veya öğrenmede kod otomatik olarak oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar

Başka sorularınız mı var? Bir [destek bileti](https://azure.microsoft.com/support/create-ticket/)oluşturmayı düşünün.