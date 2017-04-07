
# Genel Bakış
## [DocumentDB nedir?](documentdb-introduction.md)
## [DocumentDB: MongoDB için API nedir?](documentdb-protocol-mongodb.md)
## [Temel kavramlar](documentdb-resources.md)
## [Genel dağıtım](documentdb-distribute-data-globally.md)
## [Güvenlik](documentdb-nosql-database-security.md)
## [NoSQL TCO analizi](https://aka.ms/documentdb-tco-paper)
## Senaryolar
### [Genel kullanım örnekleri](documentdb-use-cases.md)
### [DocumentDB ile iletişim](documentdb-social-media-apps.md)

# Kullanmaya Başlama
## İlk uygulamanızı yazma
### [.NET konsol uygulaması](documentdb-get-started.md)
### [.NET Core konsol uygulaması](documentdb-dotnetcore-get-started.md)
### [Java konsol uygulaması](documentdb-java-get-started.md)
### [Node.js konsol uygulaması](documentdb-nodejs-get-started.md)
### [MongoDB API’si için Node.js konsol uygulaması](documentdb-mongodb-samples.md)
### [C++ konsol uygulaması](documentdb-cpp-get-started.md)
## Web uygulaması oluşturma
### [.NET web uygulaması](documentdb-dotnet-application.md)
### [MongoDB API’si için .NET web uygulaması](documentdb-mongodb-application.md)
### [Xamarin uygulaması](documentdb-mobile-apps-with-xamarin.md)
### [Node.js web uygulaması](documentdb-nodejs-application.md)
### [Java web uygulaması](documentdb-java-application.md)
### [Python Flask web uygulaması](documentdb-python-application.md)
## [Yerel Olarak Geliştirme](documentdb-nosql-local-emulator.md)
### [Öykünücü Sertifikalarını Dışarı Aktarma](documentdb-nosql-local-emulator-export-ssl-certificates.md)
## [SSS](documentdb-faq.md)

# Nasıl yapılır

## Planlama
### [Bölümleme ve ölçeklendirme](documentdb-partition-data.md)
### [Tutarlılık](documentdb-consistency-levels.md)
### [NoSQL ile SQL karşılaştırması](documentdb-nosql-vs-sql.md)
### [Düşük maliyetli okuma ve yazmalar](documentdb-key-value-store-cost.md)

## Yönet
### [İstek birimleri](documentdb-request-units.md)
### [Verilerinizi içeri aktarma](documentdb-import-data.md)
### [MongoDB için API’ye içeri aktarma](documentdb-mongodb-migrate.md)
### [MongoDB hesabınıza bağlanma](documentdb-connect-mongodb-account.md)
### [MongoChef kullanma](documentdb-mongodb-mongochef.md)
### [Robomongo kullanma](documentdb-mongodb-robomongo.md)
### [Veri modeli oluşturma](documentdb-modeling-data.md)
### [Verilerin süresini otomatik olarak sonlandırma](documentdb-time-to-live.md)
### [Yedekleme ve geri yükleme](documentdb-online-backup-and-restore.md)
### [Bölgesel yük devretme](documentdb-regional-failovers.md)
### Otomasyon
#### [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
#### [Azure CLI 1.0: Hesap oluşturma](documentdb-automation-resource-manager-cli-nodejs.md)
#### [Azure CLI 1.0: Bölge ekleme veya kaldırma](documentdb-automation-region-management.md)
#### [Azure PowerShell](documentdb-manage-account-with-powershell.md)
### Güvenlik
#### [Veri erişiminin güvenliğini sağlama](documentdb-secure-access-to-data.md)
#### [Güvenlik duvarı desteği](documentdb-firewall-support.md)
### [S1, S2, S3’ü devre dışı bırakma](documentdb-performance-levels.md)

## Geliştirme
### [SQL sorgusu](documentdb-sql-query.md)
### [Saklı yordamlar, tetikleyiciler ve UDF'ler](documentdb-programming.md)
### [Dizinlerinizi özelleştirme](documentdb-indexing-policies.md)
### [Birden çok bölgede geliştirme](documentdb-developing-with-multiple-regions.md)
### [Değişiklik akışı ile değişiklikleri izleme](documentdb-change-feed.md)
### [Jeo-uzamsal veriler kullanma](documentdb-geospatial.md)
### [Performansı test etme](documentdb-performance-testing.md)
### [Performans ipuçları](documentdb-performance-tips.md)
### En iyi uygulamalar
#### [Çok yöneticili kurulum](documentdb-multi-region-writers.md)
#### [DateTimes](documentdb-working-with-dates.md)

## Portalı kullanma
### [Veritabanı hesabı oluşturma](documentdb-create-account.md)
### [Koleksiyon oluşturma](documentdb-create-collection.md)
### [Aktarım hızı ayarlama](documentdb-set-throughput.md)
### [Genel çoğaltma ekleme](documentdb-portal-global-replication.md)
### [Belge ekleme ve düzenleme](documentdb-view-json-document-explorer.md)
### [Belgeleri sorgulama](documentdb-query-collections-query-explorer.md)
### [Bir hesabı yönetme](documentdb-manage-account.md)
### [Bir hesabı izleme](documentdb-monitor-accounts.md)
### [Betikleri yönetme](documentdb-view-scripts.md)
### [Sorun giderme ipuçları](documentdb-portal-troubleshooting.md)


## Tümleştirme
### [Spark’a bağlanma](documentdb-spark-connector.md)
### [Azure Uygulama Hizmeti ile web sitesi dağıtma](documentdb-create-documentdb-website.md)
### [Logic Apps ile uygulama günlüğü kaydı](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
### [Azure İşlevlerine Bağlama](../azure-functions/functions-bindings-documentdb.md)
### [Hadoop ile verileri çözümleme](documentdb-run-hadoop-with-hdinsight.md)
### [Azure Search ile Tümleştirme](../search/search-howto-index-documentdb.md)
### [Azure Data Factory ile veri taşıma](../data-factory/data-factory-azure-documentdb-connector.md)
### [Azure Akış Analizi ile verileri gerçek zamanlı olarak çözümleme](../stream-analytics/stream-analytics-define-outputs.md#documentdb)
### [Logic Apps kullanarak HL7 FHIR kaydını değiştirme](documentdb-change-feed-hl7-fhir-logic-apps.md)
### [Gerçek zamanlı sensör verisi işleme](../hdinsight/hdinsight-storm-iot-eventhub-documentdb.md)
### [Power BI ile verilerinizi görselleştirme](documentdb-powerbi-visualize.md)
### [Veri görselleştirme için ODBC sürücüsünden yararlanma](documentdb-nosql-odbc-driver.md)


# Başvuru
## [Java](documentdb-sdk-java.md)
## [.NET](documentdb-sdk-dotnet.md)
## [.NET Core](documentdb-sdk-dotnet-core.md)
## [Node.js](documentdb-sdk-node.md)
## [Python](documentdb-sdk-python.md)
## [REST](/rest/api/documentdb/)
## [REST Kaynak Sağlayıcısı](/rest/api/documentdbresourceprovider/)

# İlgili
## [.NET örnekleri](documentdb-dotnet-samples.md)
## [Node.js örnekleri](documentdb-nodejs-samples.md)
## [Python örnekleri](documentdb-python-samples.md)
## [SQL Söz Dizimi](https://msdn.microsoft.com/library/azure/dn782250.aspx)
## [SQL dil bilgisi kopya kağıdı](documentdb-sql-query-cheat-sheet.md)

# Kaynaklar
## [Fiyatlandırma](https://azure.microsoft.com/pricing/details/documentdb/)
## [MSDN forumu](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb)
## [Videolar](https://azure.microsoft.com/documentation/videos/index/?services=documentdb)
## [Hizmet güncelleştirmeleri](https://azure.microsoft.com/updates/?product=documentdb)
## [Topluluk portalı](documentdb-community.md)
## [Sorgu Oyun Alanı](https://www.documentdb.com/sql/demo)
## [Şemadan bağımsız dizinleme sayfası](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)
## [Beyzbol örneği ile açıklanan veri tutarlılığı](http://research.microsoft.com/apps/pubs/default.aspx?id=206913)
## [Kitap: Using Microsoft Azure DocumentDB in a Node.js Application (Node.js Uygulamasında Microsoft Azure DocumentDB Kullanma)](https://go.microsoft.com/fwlink/?LinkId=828428&clcid=0x409)
## [Öğrenme yolu](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
