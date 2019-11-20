---
title: Elastik veritabanı araçlarını kullanmaya başlama
description: Kullanımı kolay bir örnek uygulama dahil olmak üzere Azure SQL veritabanı 'nın elastik veritabanı araçları özelliğinin temel açıklaması.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: b5c323bf3d5ff55c2c30961003e8c456cbe39c82
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823652"
---
# <a name="get-started-with-elastic-database-tools"></a>Elastik veritabanı araçlarını kullanmaya başlama

Bu belgede, örnek bir uygulama çalıştırmanıza yardımcı olarak [elastik veritabanı istemci kitaplığı](sql-database-elastic-database-client-library.md) için geliştirici deneyimi sunulmaktadır. Örnek uygulama, basit bir parçalı uygulama oluşturur ve Azure SQL veritabanı 'nın elastik veritabanı araçları özelliğinin temel yeteneklerini araştırır. Parça [eşleme yönetimi](sql-database-elastic-scale-shard-map-management.md), [verilere bağımlı yönlendirme](sql-database-elastic-scale-data-dependent-routing.md)ve [çok parçalı sorgulama](sql-database-elastic-scale-multishard-querying.md)için kullanım örneklerine odaklanır. İstemci kitaplığı, .NET ve Java için de kullanılabilir. 

## <a name="elastic-database-tools-for-java"></a>Java için elastik veritabanı araçları

### <a name="prerequisites"></a>Ön koşullar

* Java geliştirici seti (JDK), sürüm 1,8 veya üzeri
* [Maven](https://maven.apache.org/download.cgi)
* Azure 'da veya yerel bir SQL Server örneğindeki bir SQL veritabanı sunucusu

### <a name="download-and-run-the-sample-app"></a>Örnek uygulamayı indirme ve çalıştırma

JAR dosyalarını derlemek ve örnek projeyi kullanmaya başlamak için aşağıdakileri yapın: 
1. İstemci kitaplığını içeren [GitHub deposunu](https://github.com/Microsoft/elastic-db-tools-for-java) , örnek uygulamayla birlikte kopyalayın. 

2. _./Sample/src/Main/Resources/Resource.Properties_ dosyasını düzenleyerek şunları ayarlayın:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Örnek projeyi oluşturmak için _./Sample_ dizininde aşağıdaki komutu çalıştırın:

    ```
    mvn install
    ```
    
4. Örnek projeyi, _./Sample_ dizininde başlatmak için aşağıdaki komutu çalıştırın: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. İstemci kitaplığı özellikleri hakkında daha fazla bilgi edinmek için çeşitli seçeneklerle denemeler yapın. Örnek uygulama uygulaması hakkında bilgi edinmek için kodu keşfetmeye çekinmeyin.

    ![İlerleme-Java][5]
    
Tebrikler! Azure SQL veritabanı 'nda elastik veritabanı araçlarını kullanarak ilk parçalı uygulamanızı başarıyla oluşturup çalıştırın. SQL veritabanınıza bağlanmak için Visual Studio 'Yu veya SQL Server Management Studio kullanın ve örneğin oluşturulan parçalara hızlıca göz atın. Yeni örnek parça veritabanları ve örneğin oluşturduğu bir parça eşleme Yöneticisi veritabanı fark edeceksiniz. 

İstemci kitaplığını kendi Maven projenize eklemek için aşağıdaki bağımlılığı Pod dosyanıza ekleyin:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>.NET için elastik veritabanı araçları

### <a name="prerequisites"></a>Ön koşullar

* Visual Studio 2012 veya sonraki sürümleri C#. [Visual Studio Indirmelerinde](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)ücretsiz bir sürüm indirin.
* NuGet 2,7 veya üzeri. En son sürümü almak için bkz. [NuGet 'ı yükleme](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Örnek uygulamayı indirme ve çalıştırma

Kitaplığı yüklemek için [Microsoft. Azure. SQLDatabase. Elaol Scale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)adresine gidin. Kitaplığı, aşağıdaki bölümde açıklanan örnek uygulamayla birlikte yüklenir.

Örneği indirmek ve çalıştırmak için aşağıdaki adımları izleyin: 

1. [Azure SQL Için elastik veritabanı araçları-MSDN 'Den Başlarken örneği](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) ' ni indirin. Örneği, seçtiğiniz bir konuma ayıklayın.

2. Bir proje oluşturmak için, *C#* dizinden *Elakıscalestarterkit. sln* çözümünü açın.

3. Örnek projenin çözümünde *app. config* dosyasını açın. Ardından, Azure SQL veritabanı sunucunuzun adını ve oturum açma bilgilerinizi (Kullanıcı adı ve parola) eklemek için dosyadaki yönergeleri izleyin.

4. Uygulamayı derleyin ve çalıştırın. İstendiğinde, çözümün NuGet paketlerini geri yüklemek için Visual Studio 'Yu etkinleştirin. Bu eylem, en son elastik veritabanı istemci kitaplığı sürümünü NuGet 'den indirir.

5. İstemci kitaplığı özellikleri hakkında daha fazla bilgi edinmek için çeşitli seçeneklerle denemeler yapın. Uygulamanın konsol çıkışında aldığı ve arka planda kodu keşfetmeye yönelik adımlara göz atın.
   
    ![Lemesine][4]

Tebrikler! SQL veritabanı 'nda elastik veritabanı araçlarını kullanarak ilk parçalı uygulamanızı başarıyla oluşturup çalıştırın. SQL veritabanınıza bağlanmak için Visual Studio 'Yu veya SQL Server Management Studio kullanın ve örneğin oluşturulan parçalara hızlıca göz atın. Yeni örnek parça veritabanları ve örneğin oluşturduğu bir parça eşleme Yöneticisi veritabanı fark edeceksiniz.

> [!IMPORTANT]
> Azure ve SQL veritabanı güncelleştirmeleriyle eşitlenmiş olarak kalmak için Management Studio her zaman en son sürümünü kullanmanızı öneririz. [SQL Server Management Studio’yu güncelleyin](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Kod örneğinin temel parçaları

* **Parçaları ve parça haritalarını yönetme**: kod, *ShardManagementUtils.cs* dosyasındaki parçalar, aralıklar ve eşlemelerle çalışmayı gösterir. Daha fazla bilgi için bkz. parça [eşleme yöneticisiyle veritabanlarını genişletme](https://go.microsoft.com/?linkid=9862595).  

* **Verilere bağımlı yönlendirme**: işlemlerin sağ parçaya yönlendirilmesi *DataDependentRoutingSample.cs* dosyasında gösterilmiştir. Daha fazla bilgi için bkz. [verilere bağımlı yönlendirme](https://go.microsoft.com/?linkid=9862596). 

* **Birden çok parça üzerinde sorgulama**: parçalar arasında sorgulama *MultiShardQuerySample.cs* dosyasında gösterilmiştir. Daha fazla bilgi için bkz. [çok parçalı sorgulama](https://go.microsoft.com/?linkid=9862597).

* **Boş parçalar ekleniyor**: yeni boş parçaların eklenmesi, *CreateShardSample.cs* dosyasındaki kod tarafından gerçekleştirilir. Daha fazla bilgi için bkz. parça [eşleme yöneticisiyle veritabanlarını genişletme](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Diğer elastik ölçeklendirme işlemleri

* **Var olan bir parçayı bölme: parçalara**ayırma özelliği bölünmüş birleştirme aracı tarafından sağlanır. Daha fazla bilgi için bkz. [ölçekli bulut veritabanları arasında veri taşıma](sql-database-elastic-scale-overview-split-and-merge.md).

* **Mevcut parçaları birleştirme: parça**birleştirmesi, bölünmüş birleştirme aracı kullanılarak da gerçekleştirilir. Daha fazla bilgi için bkz. [ölçekli bulut veritabanları arasında veri taşıma](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Maliyet

Elastik veritabanı araçları kitaplığı ücretsizdir. Elastik veritabanı araçları 'nı kullandığınızda, Azure kullanımınızın maliyetinin ötesinde ek ücret ödemeniz gerekmez. 

Örneğin, örnek uygulama yeni veritabanları oluşturur. Bu özelliğin maliyeti, seçtiğiniz SQL veritabanı sürümüne ve uygulamanızın Azure kullanımına bağlıdır.

Fiyatlandırma bilgileri için bkz. [SQL Veritabanı Fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Sonraki adımlar

Elastik veritabanı araçları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* Kod örnekleri: 
  * Elastik veritabanı araçları ([.net](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Azure SQL Entity Framework tümleştirme için elastik veritabanı araçları](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Betik merkezinde parça esnekliği](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [elastik ölçek duyurusu](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Channel 9: [elastik ölçeğe genel bakış videosu](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Tartışma Forumu: [Azure SQL veritabanı Forumu](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Performansı ölçmek için: parça [eşleme Yöneticisi Için performans sayaçları](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

