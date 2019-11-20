---
title: Eğitim verilerini içeri aktarma
titleSuffix: ML Studio (classic) - Azure
description: Verilerinizi çeşitli veri kaynaklarından Azure Machine Learning Studio (klasik) içine aktarma. Hangi veri türlerinin ve veri biçimlerinin desteklendiğini öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: b59d965d991124f2bf8d0abc10c4afbe7a95292a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837565"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Eğitim verilerinizi çeşitli veri kaynaklarından Azure Machine Learning Studio (klasik) içine aktarın

Bir tahmine dayalı analiz çözümü geliştirmek ve eğitebilmek için Machine Learning Studio (klasik) içinde kendi verilerinizi kullanmak için şu verileri kullanabilirsiniz: 

* **Yerel dosya** -çalışma alanınızda bir veri kümesi modülü oluşturmak için sabit sürücünüzden yerel verileri zamandan önce yükleyin
* **Çevrimiçi veri kaynakları** -denemenizin çalıştığı sırada birkaç çevrimiçi kaynaktan birindeki verilere erişmek Için [veri alma][import-data] modülünü kullanın
* **Machine Learning Studio (klasik) deneme** -klasik Machine Learning Studio bir veri kümesi olarak kaydedilmiş verileri kullanın
* Şirket [**içi SQL Server veritabanı**](use-data-from-an-on-premises-sql-server.md) -verileri el ile kopyalamak zorunda kalmadan şirket içi SQL Server veritabanından veri kullanma

> [!NOTE]
> Machine Learning Studio klasik sürümünde eğitim verileri için kullanabileceğiniz bir dizi örnek veri kümesi bulunur. Bunlar hakkında daha fazla bilgi için bkz. [Azure Machine Learning Studio (klasik) örnek veri kümelerini kullanma](use-sample-datasets.md).

## <a name="prepare-data"></a>Verileri hazırlama

Machine Learning Studio klasik sürümü, bir veritabanından ayrılmış veya yapılandırılmış veriler gibi dikdörtgen veya tablo verileriyle çalışmak üzere tasarlanmıştır, ancak bazı durumlarda dikdörtgen olmayan veriler kullanılabilir.

Verilerinizin klasik sürümüne aktarmadan önce verilerinizin görece temiz olması en iyisidir. Örneğin, tırnak işaretleri olmayan dizeler gibi sorunları ele almak isteyeceksiniz.

Ancak, .net Studio 'nun klasik sürümünde verilerinizi içeri aktardıktan sonra denemenizin bazı verileri işlemesini sağlayan modüller mevcuttur. Kullanacağınız makine öğrenimi algoritmalarına bağlı olarak, eksik değerler ve seyrek veriler gibi veri yapısal sorunlarını nasıl işleyeceğinize ve bu konuda yardımcı olabilecek modüller vardır. Bu işlevleri gerçekleştiren modüller için modül paleti ' nin **veri dönüştürme** bölümüne bakın.

Denemenizin herhangi bir noktasında, çıkış bağlantı noktasına tıklayarak bir modül tarafından üretilen verileri görüntüleyebilir veya indirebilirsiniz. Modüle bağlı olarak, farklı indirme seçenekleri olabilir veya Web tarayıcınızdaki verileri klasik Studio sürümünde görselleştirmeniz mümkün olabilir.

## <a name="supported-data-formats-and-data-types"></a>Desteklenen veri biçimleri ve veri türleri

Verileri içeri aktarmak için kullandığınız mekanizmaya ve nereden geldiğini bağlı olarak denemenize bir dizi veri türü aktarabilirsiniz:

* Düz metin (. txt)
* Üst bilgiyle (. csv) veya (. NH. csv) olmayan virgülle ayrılmış değerler (CSV)
* Üst bilgi (. TSV) veya (. NH. TSV) içermeyen sekmeyle ayrılmış değerler (TSV)
* Excel dosyası
* Azure tablosu
* Hive tablosu
* SQL veritabanı tablosu
* OData değerleri
* SVMLight verileri (. svmlight) (biçim bilgileri için [svmlight tanımına](http://svmlight.joachims.org/) bakın)
* Öznitelik Ilişki dosyası biçimi (ARFF) verileri (. arff) (biçim bilgileri için [arff tanımına](https://weka.wikispaces.com/ARFF) bakın)
* ZIP dosyası (. zip)
* R nesnesi veya çalışma alanı dosyası (. RDATA

Verileri de içeren ARFF gibi bir biçimde içeri aktarırsanız, Studio 'nun klasik sürümü bu meta verileri her bir sütunun başlığını ve veri türünü tanımlamak için kullanır.

Bu meta verileri içermeyen TSV veya CSV biçimi gibi verileri içeri aktarırsanız, klasik .NET sürümü verileri örnekleyerek her bir sütunun veri türünü alır. Veride de sütun başlıkları yoksa, Studio 'nun klasik sürümü varsayılan adlar sağlar.

[Meta verileri Düzenle][edit-metadata] modülünü kullanarak sütunlar için başlıkları ve veri türlerini açıkça belirtebilir veya değiştirebilirsiniz.

Aşağıdaki veri türleri, Studio 'nun klasik sürümü tarafından tanınır:

* Dize
* Tamsayı
* Çift
* Boole
* DateTime
* TimeSpan

Studio, modüller arasında veri geçirmek için ***veri tablosu*** adlı bir iç veri türü kullanır. Veri [kümesine Dönüştür][convert-to-dataset] modülünü kullanarak verilerinizi veri tablosu biçimine açıkça dönüştürebilirsiniz.

Veri tablosu dışındaki biçimleri kabul eden herhangi bir modül, verileri bir sonraki modüle geçirmeden önce sessizce veri tablosuna dönüştürür.

Gerekirse, diğer dönüştürme modüllerini kullanarak veri tablosu biçimini CSV, TSV, ARFF veya SVMLight biçimine dönüştürebilirsiniz.
Bu işlevleri gerçekleştiren modüller için modül paleti ' nin **veri biçimi dönüştürmeleri** bölümüne bakın.

## <a name="data-capacities"></a>Veri kapasiteleri

Klasik Machine Learning Studio sürümündeki modüller, yaygın kullanım örnekleri için 10 GB 'lık yoğun sayısal verileri destekler. Bir modülün birden fazla giriş aldığı durumlarda 10 GB değeri tüm giriş boyutlarının toplamıdır. Hive veya Azure SQL veritabanı sorgularını kullanarak daha büyük veri kümeleri örnekleyebilirsiniz ya da verileri içeri aktarmadan önce, öğrenimini, ön işleme sayısını kullanarak kullanabilirsiniz.  

Aşağıdaki veri türleri, özellik normalleştirme sırasında daha büyük veri kümelerine genişleyebilir ve boyutu 10 GB’den az olacak şekilde sınırlıdır:

* Seyrek
* Kategorik
* Dizeler
* İkili veriler

Aşağıdaki modüller, boyutu 10 GB'den az veri kümeleriyle sınırlıdır:

* Öneren modüller
* Synthetic Minority Oversampling Technique (SMOTE) modülü
* Betik modülleri: R, Python, SQL
* Katılma veya Özellik Karma gibi çıkış veri boyutunun giriş veri boyutundan büyük olabileceği modüller
* Yineleme sayısının çok büyük olduğu durumlarda Çapraz doğrulama, Model Ayarlama Hiperparametreleri, Sıralı Regresyon ve Tek veya Tüm Çoklu Sınıflar

Birkaç GB 'den daha büyük olan veri kümelerinde, verileri Azure Storage veya Azure SQL veritabanı 'na yükleyin ya da doğrudan yerel bir dosyadan yüklemek yerine Azure HDInsight kullanın.

Görüntü verileri hakkında, [görüntüleri Içeri aktarma](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) modül başvurusunda bilgi edinebilirsiniz.

## <a name="import-from-a-local-file"></a>Yerel bir dosyadan içeri aktar

Studio 'nun klasik sürümünde eğitim verileri olarak kullanmak üzere sabit sürücünüzden bir veri dosyası yükleyebilirsiniz. Bir veri dosyasını içeri aktardığınızda, çalışma alanınızda denemeleri içinde kullanıma yönelik bir veri kümesi modülü oluşturursunuz.

Yerel bir sabit sürücüden verileri içeri aktarmak için aşağıdakileri yapın:

1. Studio (klasik) penceresinin alt kısmındaki **+ Yeni** seçeneğine tıklayın.
2. **Veri kümesi** ve **yerel dosya**' yı seçin.
3. **Yeni bir veri kümesini karşıya yükle** iletişim kutusunda, karşıya yüklemek istediğiniz dosyaya gidin.
4. Bir ad girin, veri türünü belirtin ve isteğe bağlı olarak bir açıklama girin. Bir açıklama önerilir; gelecekte verileri kullanırken hatırlamak istediğiniz veriler hakkında herhangi bir özelliği kaydetmenize olanak tanır.
5. Bu onay kutusu, **mevcut bir veri kümesinin yeni sürümüdür** ve mevcut bir veri kümesini yeni verilerle güncelleştirmenize olanak tanır. Bunu yapmak için, bu onay kutusuna tıklayın ve ardından mevcut bir veri kümesinin adını girin.

![Yeni bir veri kümesini karşıya yükle](./media/import-data/upload-dataset-from-local-file.png)

Karşıya yükleme süresi verilerinizin boyutuna ve hizmetle olan bağlantınızın hızına bağlıdır. Dosyanın uzun sürdüğüne sahipseniz, beklerken Studio 'nun klasik sürümünün içinde başka şeyler yapabilirsiniz. Ancak, verilerin karşıya yüklenmesi tamamlanmadan önce tarayıcının kapatılması, yükleme işleminin başarısız olmasına neden olur.

Verileriniz karşıya yüklendikten sonra, veri kümesi modülünde depolanır ve çalışma alanınızdaki her türlü deneme için kullanılabilir.

Bir deneme düzenlenirken, modül paletindeki **kayıtlı veri** kümeleri listesinin altındaki **veri kümelerinde** karşıya yüklediğiniz veri kümelerini bulabilirsiniz. Daha fazla analiz ve makine öğrenimi için veri kümesini kullanmak istediğinizde veri kümesini deneme tuvaline sürükleyip bırakabilirsiniz.

## <a name="import-from-online-data-sources"></a>Çevrimiçi veri kaynaklarından içeri aktar

[Veri alma][import-data] modülünü kullanarak, denemenizin çalışması sırasında çeşitli çevrimiçi veri kaynaklarından verileri içeri aktarabiliyor.

> [!NOTE]
> Bu makalede, [verileri Içeri aktarma][import-data] modülü hakkında genel bilgiler sağlanmaktadır. Erişebileceğiniz veri türleri hakkında daha ayrıntılı bilgi için, bkz. [veri alma][import-data] modülü için modül başvurusu konusu.

[Veri alma][import-data] modülünü kullanarak, denemenizin çalıştığı sırada çeşitli çevrimiçi veri kaynaklarından birindeki verilere erişebilirsiniz:

* HTTP kullanan bir Web URL 'SI
* HiveQL kullanarak Hadoop
* Azure Blob depolama
* Azure tablosu
* Azure VM 'de Azure SQL veritabanı veya SQL Server
* Şirket içi SQL Server veritabanı
* Bir veri akışı sağlayıcısı, OData Şu anda
* Azure Cosmos DB

Denemeniz çalışırken bu eğitim verilerine erişildiği için, bu deneme sürümünde yalnızca kullanılabilir. Karşılaştırma ile, bir veri kümesi modülünde depolanan veriler, çalışma alanınızdaki her türlü deneme tarafından kullanılabilir.

Studio (klasik) denemenizin çevrimiçi veri kaynaklarına erişmek için [veri Içeri aktarma][import-data] modülünü denemenize ekleyin. Ardından, veri kaynağını seçmek ve yapılandırmak için adım adım kılavuzlu yönergeler için **Özellikler** altında, **Verileri İçeri Aktarma Sihirbazı** ' nı seçin. Alternatif olarak, **Özellikler** altında **veri kaynağını** el ile seçebilir ve verilere erişmek için gereken parametreleri sağlayabilirsiniz.

Desteklenen çevrimiçi veri kaynakları aşağıdaki tabloda listelenir. Bu tablo ayrıca desteklenen dosya biçimlerini ve verilere erişmek için kullanılan parametreleri özetler.

> [!IMPORTANT]
> Şu anda, verileri [Içeri aktarma][import-data] ve [dışarı aktarma][export-data] modülleri yalnızca klasik dağıtım modeli kullanılarak oluşturulan Azure Storage 'dan verileri okuyabilir ve yazabilir. Diğer bir deyişle, sık erişimli depolama erişim katmanı veya seyrek erişimli depolama erişim katmanı sunan yeni Azure Blob depolama hesabı türü henüz desteklenmiyor.
>
> Genellikle, bu hizmet seçeneği sunulmadan önce oluşturmuş olabileceğiniz tüm Azure depolama hesapları etkilenmemelidir.
> Yeni bir hesap oluşturmanız gerekiyorsa, dağıtım modeli için **Klasik** ' i seçin veya Resource Manager ' ı kullanın ve **Hesap türü**Için **BLOB depolama** yerine **genel amaçlı** ' i seçin.
>
> Daha fazla bilgi için bkz. [Azure Blob depolama: sık ve seyrek erişimli depolama katmanları](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Desteklenen çevrimiçi veri kaynakları
Azure Machine Learning Studio **veri alma** modülünün klasik sürümü aşağıdaki veri kaynaklarını destekler:

| Veri kaynağı | Açıklama | Parametreler |
| --- | --- | --- |
| HTTP aracılığıyla Web URL 'SI |HTTP kullanan herhangi bir Web URL 'sinden, virgülle ayrılmış değerler (CSV), sekmeyle ayrılmış değerler (TSV), öznitelik ilişkisi dosya biçimi (ARFF) ve destek vektör makineleri (SVM-Light) biçimleri içindeki verileri okur |<b>URL</b>: herhangi bir uzantıya sahip site URL 'si ve dosya adı da dahil olmak üzere dosyanın tam adını belirtir. <br/><br/><b>Veri biçimi</b>: desteklenen veri biçimlerinden birini BELIRTIR: CSV, TSV, arff veya SVM-Light. Verilerin bir başlık satırı varsa, sütun adlarını atamak için kullanılır. |
| Hadoop/bir |Hadoop 'daki dağıtılmış depolama alanından verileri okur. SQL benzeri bir sorgu dili olan HiveQL kullanarak istediğiniz verileri belirtirsiniz. HiveQL Ayrıca verileri toplamak ve verileri klasik .NET sürümüne eklemeden önce veri filtrelemeyi gerçekleştirmek için de kullanılabilir. |<b>Hive veritabanı sorgusu</b>: verileri oluşturmak Için kullanılan Hive sorgusunu belirtir.<br/><br/><b>Hcatalog sunucusu URI 'si</b> : kümenizin adı *&gt;. azurehdinsight.net olan&lt;* biçimini kullanarak Kümenizin adı belirtildi.<br/><br/><b>Hadoop Kullanıcı hesabı adı</b>: kümeyi sağlamak Için kullanılan Hadoop Kullanıcı hesabının adını belirtir.<br/><br/><b>Hadoop Kullanıcı hesabı parolası</b> : küme sağlanırken kullanılan kimlik bilgilerini belirtir. Daha fazla bilgi için bkz. [HDInsight 'Ta Hadoop kümeleri oluşturma](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Çıkış verilerinin konumu</b>: verilerin bir Hadoop Dağıtılmış dosya sistemi 'nde mi yoksa Azure 'da mı depolandığını belirtir. <br/><ul>Çıktı verilerini bir olarak depoladığınızda,,,,, bir sunucu URI 'sini belirtin. (HDInsight kümesi adını HTTPS://ön eki olmadan kullandığınızdan emin olun). <br/><br/>Çıktı verilerinizi Azure 'da depolarsanız, Azure depolama hesabı adı, depolama erişim anahtarı ve depolama kapsayıcısı adı ' nı belirtmeniz gerekir.</ul> |
| SQL veritabanı |Bir Azure SQL veritabanında veya Azure sanal makinesinde çalışan bir SQL Server veritabanında depolanan verileri okur. |<b>Veritabanı sunucusu adı</b>: veritabanının çalıştığı sunucunun adını belirtir.<br/><ul>Azure SQL veritabanı durumunda oluşturulan sunucu adını girin. Genellikle form *&lt;generated_identifier&gt;. Database.Windows.net.* <br/><br/>Azure sanal makinesinde barındırılan bir SQL Server olması durumunda *TCP:&lt;sanal makıne DNS adı&gt;, 1433* yazın</ul><br/><b>Veritabanı adı </b>: sunucudaki veritabanının adını belirtir. <br/><br/><b>Sunucu Kullanıcı hesabı adı</b>: veritabanı için erişim izinlerine sahip olan bir hesabın kullanıcı adını belirtir. <br/><br/><b>Sunucu Kullanıcı hesabı parolası</b>: Kullanıcı hesabının parolasını belirtir.<br/><br/><b>Veritabanı sorgusu</b>: okumak istediğiniz verileri açıklayan bir SQL açıklaması girin. |
| Şirket içi SQL veritabanı |Şirket içi SQL veritabanında depolanan verileri okur. |<b>Veri ağ geçidi</b>: SQL Server veritabanınıza erişebileceği bir bilgisayarda yüklü veri yönetimi ağ geçidinin adını belirtir. Ağ geçidini ayarlama hakkında daha fazla bilgi için bkz. Şirket [ıçı SQL Server 'dan verileri kullanarak Azure Machine Learning Studio klasik sürümü ile gelişmiş analiz gerçekleştirme](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Veritabanı sunucusu adı</b>: veritabanının çalıştığı sunucunun adını belirtir.<br/><br/><b>Veritabanı adı </b>: sunucudaki veritabanının adını belirtir. <br/><br/><b>Sunucu Kullanıcı hesabı adı</b>: veritabanı için erişim izinlerine sahip olan bir hesabın kullanıcı adını belirtir. <br/><br/><b>Kullanıcı adı ve parola</b>: veritabanı kimlik bilgilerinizi girmek Için <b>değer girin</b> ' e tıklayın. Şirket içi SQL Server nasıl yapılandırıldığına bağlı olarak Windows tümleşik kimlik doğrulaması veya SQL Server kimlik doğrulaması kullanabilirsiniz.<br/><br/><b>Veritabanı sorgusu</b>: okumak istediğiniz verileri açıklayan bir SQL açıklaması girin. |
| Azure Tablosu |Azure depolama 'daki tablo hizmetinden verileri okur.<br/><br/>Büyük miktarlarda veriyi seyrek olarak okuduğunuzda Azure Tablo hizmetini kullanın. Esnek, ilişkisel olmayan (NoSQL), yüksek düzeyde ölçeklenebilir, pahalı ve yüksek oranda kullanılabilir depolama çözümü sağlar. |**Içeri aktarma verileri** , genel bilgilere erişip eriştiğinize veya oturum açma kimlik bilgileri gerektiren bir özel depolama hesabına bağlı olarak değişir. Bu, her biri kendi parametre kümesine sahip olan "PublicOrSAS" veya "Account" değerine sahip olabilen <b>kimlik doğrulama türü</b> tarafından belirlenir. <br/><br/><b>Ortak veya paylaşılan erişim imzası (SAS) URI 'si</b>: parametreler şunlardır:<br/><br/><ul><b>Tablo URI 'si</b>: tablo için genel veya SAS URL 'sini belirtir.<br/><br/><b>Özellik adları için taranacak satırları belirtir</b>: değerler, belirtilen sayıda satırı taramak Için <i>TOPN</i> , tablodaki tüm satırları almak için <i>scanall</i> ' tır. <br/><br/>Veriler homojen ve öngörülebilir ise, *TOPN* ' ı seçmeniz ve N için bir sayı girmeniz önerilir. Büyük tablolar için bu, daha hızlı okuma süreleriyle sonuçlanabilir.<br/><br/>Veriler, tablonun derinliğine ve konumuna göre değişiklik gösteren özellikler kümesiyle yapılandırılmış ise, tüm satırları taramak için *Scanall* seçeneğini seçin. Bu, elde edilen özellik ve meta veri dönüştürmesinin bütünlüğünü sağlar.<br/><br/></ul><b>Özel depolama hesabı</b>: parametreler şunlardır: <br/><br/><ul><b>Hesap adı</b>: okunacak tabloyu içeren hesabın adını belirtir.<br/><br/><b>Hesap anahtarı</b>: hesapla ilişkili depolama anahtarını belirtir.<br/><br/><b>Tablo adı</b> : okunacak verileri içeren tablonun adını belirtir.<br/><br/><b>Özellik adları için taranacak satırlar</b>: değerler, tablodaki tüm satırları almak Için <i>TOPN</i> , belirtilen sayıda satırı tarar veya <i>scanall</i> ' tır.<br/><br/>Veriler homojen ve öngörülebilir ise, *TOPN* 'yi seçmenizi ve N için bir sayı girmenizi öneririz. Büyük tablolar için bu, daha hızlı okuma süreleriyle sonuçlanabilir.<br/><br/>Veriler, tablonun derinliğine ve konumuna göre değişiklik gösteren özellikler kümesiyle yapılandırılmış ise, tüm satırları taramak için *Scanall* seçeneğini seçin. Bu, elde edilen özellik ve meta veri dönüştürmesinin bütünlüğünü sağlar.<br/><br/> |
| Azure Blob Depolama |Azure depolama 'da bulunan, görüntü, yapılandırılmamış metin veya ikili veri gibi BLOB hizmetinde depolanan verileri okur.<br/><br/>Blob hizmetini kullanarak verileri herkese açık bir şekilde sunabilir veya uygulama verilerini özel olarak depolayabilirsiniz. HTTP veya HTTPS bağlantılarını kullanarak verilerinize dilediğiniz yerden erişebilirsiniz. |**Veri alma** modülündeki seçenekler, ortak bilgilere erişip eriştiğinize veya oturum açma kimlik bilgileri gerektiren bir özel depolama hesabına bağlı olarak değişir. Bu, "PublicOrSAS" veya "Account" değerinden birine sahip olabilen <b>kimlik doğrulama türü</b> tarafından belirlenir.<br/><br/><b>Ortak veya paylaşılan erişim imzası (SAS) URI 'si</b>: parametreler şunlardır:<br/><br/><ul><b>URI</b>: Depolama Blobu için genel veya SAS URL 'sini belirtir.<br/><br/><b>Dosya biçimi</b>: blob hizmetindeki verilerin biçimini belirtir. Desteklenen biçimler CSV, TSV ve ARFF.<br/><br/></ul><b>Özel depolama hesabı</b>: parametreler şunlardır: <br/><br/><ul><b>Hesap adı</b>: okumak istediğiniz blobu içeren hesabın adını belirtir.<br/><br/><b>Hesap anahtarı</b>: hesapla ilişkili depolama anahtarını belirtir.<br/><br/><b>Kapsayıcı, dizin veya blob yolu</b> : okunacak verileri içeren Blobun adını belirtir.<br/><br/><b>BLOB dosyası biçimi</b>: blob hizmetindeki verilerin biçimini belirtir. Desteklenen veri biçimleri CSV, TSV, ARFF, CSV, belirtilen kodlamalı ve Excel. <br/><br/><ul>Biçim CSV veya TSV ise, dosyanın bir başlık satırı içerip içermediğini belirtdiğinizden emin olun.<br/><br/>Excel çalışma kitaplarından veri okumak için Excel seçeneğini kullanabilirsiniz. <i>Excel veri biçimi</i> seçeneğinde, verilerin bir Excel çalışma sayfası aralığında veya bir Excel tablosunda olup olmadığını belirtin. <i>Excel sayfası veya katıştırılmış tablo </i>seçeneğinde, okumak istediğiniz sayfanın veya tablonun adını belirtin.</ul><br/> |
| Veri akışı sağlayıcısı |Desteklenen bir akış sağlayıcısından verileri okur. Şu anda yalnızca açık veri Protokolü (OData) biçimi destekleniyor. |<b>Veri içeriği türü</b>: OData biçimini belirtir.<br/><br/><b>Kaynak URL 'si</b>: veri akışı için tam URL 'yi belirtir. <br/>Örneğin, aşağıdaki URL Northwind örnek veritabanından okur: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Başka bir deneyden içeri aktar

Bir deneyden bir ara sonuç almak ve bunu başka bir deneyin parçası olarak kullanmak isteyeceğiniz durumlar olacaktır. Bunu yapmak için, modülü bir veri kümesi olarak kaydedersiniz:

1. Veri kümesi olarak kaydetmek istediğiniz modülün çıktısına tıklayın.
2. **Veri kümesi olarak kaydet**' e tıklayın.
3. İstendiğinde, veri kümesini kolayca tanımlamanızı sağlayacak bir ad ve açıklama girin.
4. **Tamam** onay işaretine tıklayın.

Kaydetme tamamlandığında, veri kümesi, çalışma alanınızdaki herhangi bir deneyde kullanılabilir olacaktır. Bunu, modül paletindeki **kayıtlı veri kümeleri** listesinde bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Veri Içeri aktarma ve veri dışa aktarma modülleri kullanan Azure Machine Learning Studio Web hizmetlerini dağıtma](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
