---
title: Azure Machine Learning Team Data Science süreci için senaryoları tanımla
description: Team Data Science Işlemiyle gelişmiş tahmine dayalı analiz yapmak için uygun senaryoları seçin.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: de02e94014fa2a28e364c4f85bcb31a1a4827b8c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495063"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Azure Machine Learning’de gelişmiş analiz senaryoları
Bu makalede, [ekip veri bilimi işlemi (TDSP)](overview.md)tarafından işlenebilen çeşitli örnek veri kaynakları ve hedef senaryolar özetlenmektedir. TDSP, ekiplerin akıllı uygulamalar oluşturma konusunda işbirliği yapmasına yönelik sistematik bir yaklaşım sağlar. Burada sunulan senaryolar, Azure 'daki veri özelliklerine, kaynak konumlarına ve hedef depolarına bağlı olan veri işleme iş akışında sunulan seçenekleri gösterir.

Verilerinize ve amacınıza uygun örnek senaryolar seçmeye yönelik **karar ağacı** , son bölümde sunulur.

Aşağıdaki bölümlerin her biri örnek bir senaryo sunar. Her senaryo için, olası bir veri bilimi veya gelişmiş analiz akışı ve Azure kaynaklarını destekleme listelenmiştir.

> [!NOTE]
> **Aşağıdaki senaryoların tümü için şunları yapmanız gerekir:**
> <br/>
> 
> * [Depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md)
>   <br/>
> * [Azure Machine Learning çalışma alanı oluşturma](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Senaryo \#1: yerel dosyalarda küçük ve orta ölçekli tablo veri kümesi
![Küçük ve orta ölçekli yerel dosyalar][1]

#### <a name="additional-azure-resources-none"></a>Ek Azure kaynakları: yok
1. [Azure Machine Learning Studio](https://studio.azureml.net/)oturum açın.
1. Bir veri kümesini karşıya yükleyin.
1. Karşıya yüklenen veri kümeleri ile başlayan bir Azure Machine Learning deneme akışı oluşturun.

## <a name="smalllocalprocess"></a>Senaryo \#2: yerel dosyaların işlenmesi gereken küçük ve orta ölçekli veri kümesi
![İşleme sahip küçük ve orta ölçekli yerel dosyalar][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ek Azure kaynakları: Azure sanal makinesi (IPython Not defteri sunucusu)
1. IPython Not defteri çalıştıran bir Azure sanal makinesi oluşturun.
1. Verileri bir Azure depolama kapsayıcısına yükleyin.
1. IPython not defterindeki verileri Azure depolama kapsayıcısından erişen, önceden işleyin ve temizleyin.
1. Verileri temizlenen, tablolu biçimde Dönüştür.
1. Dönüştürülmüş verileri Azure bloblarına kaydedin.
1. [Azure Machine Learning Studio](https://studio.azureml.net/)oturum açın.
1. [Verileri Içeri aktarma][import-data] modülünü kullanarak Azure Bloblarındaki verileri okuyun.
1. Alınan veri kümeleri ile başlayan bir Azure Machine Learning deneme akışı oluşturun.

## <a name="largelocal"></a>Senaryo \#3: yerel dosyaların büyük veri kümesi, Azure Bloblarını hedefleme
![Büyük yerel dosyalar][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ek Azure kaynakları: Azure sanal makinesi (IPython Not defteri sunucusu)
1. IPython Not defteri çalıştıran bir Azure sanal makinesi oluşturun.
1. Verileri bir Azure depolama kapsayıcısına yükleyin.
1. IPython not defterindeki verileri Azure Bloblarındaki verilere erişen, ön işleme ve temizleme.
1. Gerekirse, verileri temizlenen, tablolu biçimde dönüştürün.
1. Verileri keşfedebilir ve gerektiğinde Özellikler oluşturun.
1. Küçük ve orta ölçekli bir veri örneğini ayıklayın.
1. Örneklenmiş verileri Azure bloblarına kaydedin.
1. [Azure Machine Learning Studio](https://studio.azureml.net/)oturum açın.
1. [Verileri Içeri aktarma][import-data] modülünü kullanarak Azure Bloblarındaki verileri okuyun.
1. Alınan veri kümeleri ile başlayan Azure Machine Learning deneme akışı oluşturun.

## <a name="smalllocaltodb"></a>Senaryo \#4: yerel dosyaların Azure sanal makinesinde SQL Server hedefleme için küçük ve orta ölçekli veri kümesi
![Azure 'da küçük ve orta ölçekli yerel dosyalar SQL DB 'ye][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ek Azure kaynakları: Azure sanal makinesi (SQL Server/IPython Not defteri sunucusu)
1. SQL Server + IPython Not defteri çalıştıran bir Azure sanal makinesi oluşturun.
1. Verileri bir Azure depolama kapsayıcısına yükleyin.
1. IPython Not defteri 'ni kullanarak Azure depolama kapsayıcısında önceden işlem yapın ve verileri temizleyin.
1. Gerekirse, verileri temizlenen, tablolu biçimde dönüştürün.
1. VM 'ler yerel dosyalara veri kaydetme (VM 'de IPython Not defteri çalışıyor, yerel sürücüler VM sürücülerine başvurur).
1. Verileri bir Azure VM üzerinde çalışan SQL Server veritabanına yükleyin.
   
   Seçenek \#1: SQL Server Management Studio kullanma.
   
   * SQL Server VM oturum açma
   * SQL Server Management Studio çalıştırın.
   * Veritabanı ve hedef tablolar oluşturun.
   * Verileri VM yerel dosyalarından yüklemek için toplu içeri aktarma yöntemlerinden birini kullanın.
   
   Seçenek \#2: IPython Not defterini kullanma – orta ve daha büyük veri kümeleri için önerilmez
   
   <!-- -->    
   * VM 'deki SQL Server erişmek için ODBC bağlantı dizesi kullanın.
   * Veritabanı ve hedef tablolar oluşturun.
   * Verileri VM yerel dosyalarından yüklemek için toplu içeri aktarma yöntemlerinden birini kullanın.
1. Verileri araştırma, gerektiğinde özellikler oluşturma. Özelliklerin veritabanı tablolarında gerçekleştirilmiş olması gerekmediğini unutmayın. Yalnızca bunları oluşturmak için gereken sorguyu aklınızda edin.
1. Gerekirse bir veri örneği boyutuna karar verin ve/veya isterseniz.
1. [Azure Machine Learning Studio](https://studio.azureml.net/)oturum açın.
1. Verileri [Içeri aktarma][import-data] modülünü kullanarak doğrudan SQL Server verileri okuyun. Doğrudan [Içeri aktarma veri][import-data] sorgusunda alanları çıkaran, özellik oluşturan ve örnek verileri sağlayan gerekli sorguyu yapıştırın.
1. Alınan veri kümeleri ile başlayan Azure Machine Learning deneme akışı oluşturun.

## <a name="largelocaltodb"></a>Senaryo \#5: yerel dosyalardaki büyük veri kümesi, Azure VM 'de hedef SQL Server
![Azure 'da SQL DB 'ye büyük yerel dosyalar][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ek Azure kaynakları: Azure sanal makinesi (SQL Server/IPython Not defteri sunucusu)
1. SQL Server ve IPython Notebook Server çalıştıran bir Azure sanal makinesi oluşturun.
1. Verileri bir Azure depolama kapsayıcısına yükleyin.
1. Seçim Ön işleme ve verileri temizleme.
   
   a.  IPython not defterindeki verileri önceden işleme ve Temizleme, Azure 'daki verilere erişme
   
       blobs.
   
   b.  Gerekirse, verileri temizlenen, tablolu biçimde dönüştürün.
   
   c.  VM 'ler yerel dosyalara veri kaydetme (VM 'de IPython Not defteri çalışıyor, yerel sürücüler VM sürücülerine başvurur).
1. Verileri bir Azure VM üzerinde çalışan SQL Server veritabanına yükleyin.
   
   a.  SQL Server VM oturum açın.
   
   b.  Veriler henüz kaydedilmediğinden, Azure 'dan veri dosyalarını indirin
   
       storage container to local-VM folder.
   
   c.  SQL Server Management Studio çalıştırın.
   
   d.  Veritabanı ve hedef tablolar oluşturun.
   
   e.  Verileri yüklemek için toplu içeri aktarma yöntemlerinden birini kullanın.
   
   f.  Tablo katılımları gerekliyse, birleştirmeleri hızlandırmak için dizinler oluşturun.
   
   > [!NOTE]
   > Büyük veri boyutlarının daha hızlı yüklenmesi için bölümlenmiş tablolar oluşturmanız ve verileri paralel olarak içeri aktarmanız önerilir. Daha fazla bilgi için bkz. [SQL bölümlenmiş tablolara paralel veri aktarma](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Verileri araştırma, gerektiğinde özellikler oluşturma. Özelliklerin veritabanı tablolarında gerçekleştirilmiş olması gerekmediğini unutmayın. Yalnızca bunları oluşturmak için gereken sorguyu aklınızda edin.
1. Gerekirse bir veri örneği boyutuna karar verin ve/veya isterseniz.
1. [Azure Machine Learning Studio](https://studio.azureml.net/)oturum açın.
1. Verileri [Içeri aktarma][import-data] modülünü kullanarak doğrudan SQL Server verileri okuyun. Doğrudan [Içeri aktarma veri][import-data] sorgusunda alanları çıkaran, özellik oluşturan ve örnek verileri sağlayan gerekli sorguyu yapıştırın.
1. Karşıya yüklenen veri kümesiyle başlayan basit Azure Machine Learning deneme akışı

## <a name="largedbtodb"></a>\#6. Senaryo: bir Azure sanal makinesinde SQL Server hedefleme, şirket içi bir SQL Server veritabanında büyük veri kümesi
![Azure 'da büyük SQL DB-SQL DB 'ye şirket içi][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ek Azure kaynakları: Azure sanal makinesi (SQL Server/IPython Not defteri sunucusu)
1. SQL Server ve IPython Notebook Server çalıştıran bir Azure sanal makinesi oluşturun.
1. Verileri SQL Server döküm dosyalarına aktarmak için veri dışa aktarma yöntemlerinden birini kullanın.
   
   > [!NOTE]
   > Şirket içi veritabanındaki tüm verileri taşımaya karar verirseniz, tam veritabanını Azure 'daki SQL Server örneğine taşımak için alternatif (daha hızlı) bir yöntem. Verileri dışarı aktarma, veritabanı oluşturma ve hedef veritabanına veri yükleme/içeri aktarma adımlarını atlayın ve alternatif yöntemi izleyin.
   > 
   > 
1. Döküm dosyalarını Azure depolama kapsayıcısına yükleyin.
1. Verileri bir Azure sanal makinesinde çalışan bir SQL Server veritabanına yükleyin.
   
   a.  SQL Server VM oturum açın.
   
   b.  Veri dosyalarını bir Azure depolama kapsayıcısından yerel VM klasörüne indirin.
   
   c.  SQL Server Management Studio çalıştırın.
   
   d.  Veritabanı ve hedef tablolar oluşturun.
   
   e.  Verileri yüklemek için toplu içeri aktarma yöntemlerinden birini kullanın.
   
   f.  Tablo katılımları gerekliyse, birleştirmeleri hızlandırmak için dizinler oluşturun.
   
   > [!NOTE]
   > Büyük veri boyutlarının daha hızlı yüklenmesi için bölümlenmiş tablolar oluşturun ve verileri paralel olarak içeri aktarın. Daha fazla bilgi için bkz. [SQL bölümlenmiş tablolara paralel veri aktarma](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Verileri araştırma, gerektiğinde özellikler oluşturma. Özelliklerin veritabanı tablolarında gerçekleştirilmiş olması gerekmediğini unutmayın. Yalnızca bunları oluşturmak için gereken sorguyu aklınızda edin.
1. Gerekirse bir veri örneği boyutuna karar verin ve/veya isterseniz.
1. [Azure Machine Learning Studio](https://studio.azureml.net/)oturum açın.
1. Verileri [Içeri aktarma][import-data] modülünü kullanarak doğrudan SQL Server verileri okuyun. Doğrudan [Içeri aktarma veri][import-data] sorgusunda alanları çıkaran, özellik oluşturan ve örnek verileri sağlayan gerekli sorguyu yapıştırın.
1. Karşıya yüklenen veri kümesiyle başlayan basit Azure Machine Learning deneme akışı.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Şirket içi SQL Server tam bir veritabanını Azure SQL veritabanı 'na kopyalamak için alternatif Yöntem
![Azure 'da yerel DB 'yi ayır ve SQL DB 'ye iliştirme][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ek Azure kaynakları: Azure sanal makinesi (SQL Server/IPython Not defteri sunucusu)
SQL Server veritabanının tamamını SQL Server VM çoğaltmak için bir veritabanını bir konumdan veya sunucudan diğerine kopyalamanız gerekir. Bu, veritabanının geçici olarak çevrimdışı gerçekleştirileceğidir. Bunu SQL Server Management Studio Nesne Gezgini veya eşdeğer Transact-SQL komutlarını kullanarak yapabilirsiniz.

1. Kaynak konumda veritabanını ayırın. Daha fazla bilgi için bkz. [veritabanını ayırma](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. Windows Gezgini veya Windows komut Istemi penceresinde, ayrılmış veritabanı dosyasını veya dosyalarını ve günlük dosyasını veya dosyalarını Azure 'daki SQL Server VM hedef konuma kopyalayın.
1. Kopyalanan dosyaları hedef SQL Server örneğine iliştirin. Daha fazla bilgi için bkz. [veritabanı iliştirme](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Ayırma ve Iliştirme kullanarak bir veritabanını taşıma (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Senaryo \#7: yerel dosyalardaki büyük veri, Azure HDInsight Hadoop kümelerinde hedef Hive veritabanı
![Yerel hedef Hive 'de büyük veri][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Ek Azure kaynakları: Azure HDInsight Hadoop kümesi ve Azure sanal makinesi (IPython Not defteri sunucusu)
1. IPython Not defteri sunucusu çalıştıran bir Azure sanal makinesi oluşturun.
1. Azure HDInsight Hadoop kümesi oluşturun.
1. Seçim Ön işleme ve verileri temizleme.
   
   a.  IPython not defterindeki verileri önceden işleme ve Temizleme, Azure 'daki verilere erişme
   
       blobs.
   
   b.  Gerekirse, verileri temizlenen, tablolu biçimde dönüştürün.
   
   c.  VM 'ler yerel dosyalara veri kaydetme (VM 'de IPython Not defteri çalışıyor, yerel sürücüler VM sürücülerine başvurur).
1. 2\. adımda seçilen Hadoop kümesinin varsayılan kapsayıcısına verileri yükleyin.
1. Azure HDInsight Hadoop kümesinde verileri Hive veritabanına yükleyin.
   
   a.  Hadoop kümesinin baş düğümünde oturum açın
   
   b.  Hadoop komut satırını açın.
   
   c.  Hadoop komut satırında komut `cd %hive_home%\bin` ile Hive kök dizinini girin.
   
   d.  Veritabanı ve tablolar oluşturmak ve BLOB depolamadan Hive tablolarına veri yüklemek için Hive sorgularını çalıştırın.
   
   > [!NOTE]
   > Veriler büyükse, kullanıcılar bölüm içeren Hive tablosunu oluşturabilir. Ardından kullanıcılar, Bölüm tarafından bölümlenmiş Hive tablosuna veri yüklemek için baş düğümdeki Hadoop komut satırında bir `for` döngüsü kullanabilir.
   > 
   > 
1. Hadoop komut satırında gerektiğinde verileri bulun ve özellik oluşturun. Özelliklerin veritabanı tablolarında gerçekleştirilmiş olması gerekmediğini unutmayın. Yalnızca bunları oluşturmak için gereken sorguyu aklınızda edin.
   
   a.  Hadoop kümesinin baş düğümünde oturum açın
   
   b.  Hadoop komut satırını açın.
   
   c.  Hadoop komut satırında komut `cd %hive_home%\bin` ile Hive kök dizinini girin.
   
   d.  Verileri araştırmak ve gerektiğinde özellikler oluşturmak için Hadoop kümesinin baş düğümündeki Hadoop komut satırında Hive sorguları ' nı çalıştırın.
1. Gerekirse ve/veya isteniyorsa, verileri Azure Machine Learning Studio sığacak şekilde örnekleyebilirsiniz.
1. [Azure Machine Learning Studio](https://studio.azureml.net/)oturum açın.
1. Verileri [Içeri aktarma][import-data] modülünü kullanarak doğrudan `Hive Queries` verileri okuyun. Doğrudan [Içeri aktarma veri][import-data] sorgusunda alanları çıkaran, özellik oluşturan ve örnek verileri sağlayan gerekli sorguyu yapıştırın.
1. Karşıya yüklenen veri kümesiyle başlayan basit Azure Machine Learning deneme akışı.

## <a name="decisiontree"></a>Senaryo seçimi için karar ağacı
---
Aşağıdaki diyagramda yukarıda açıklanan senaryolar ve bu konu başlığı altında listelenen senaryoların her birine götürür ve gelişmiş analiz Işlemi ve teknoloji seçimleri özetlenmektedir. Veri işleme, araştırma, özellik Mühendisliği ve örnekleme, kaynak, ara ve/veya hedef ortamlarda bir veya daha fazla yöntem/ortamda gerçekleşebilir ve gerektiğinde tekrarlayarak devam edebilir. Diyagram yalnızca bazı akışların bir gösterimi görevi görür ve kapsamlı bir numaralandırma sağlamaz.

![Örnek DS işlem izlenecek yol senaryoları][8]

### <a name="advanced-analytics-in-action-examples"></a>Eylem örneklerinde gelişmiş analiz
Genel veri kümelerini kullanarak gelişmiş analiz sürecini ve teknolojisini kullanan uçtan uca Azure Machine Learning izlenecek yollar için bkz.:

* [Ekip veri bilimi işlemi işlem içinde: SQL Server kullanılıyor](sql-walkthrough.md).
* [Team Data Science süreci: HDInsight Hadoop kümelerini kullanma](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
