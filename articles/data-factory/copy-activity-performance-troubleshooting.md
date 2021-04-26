---
title: Kopyalama etkinliği performansını sorun giderme
description: Azure Data Factory 'da etkinlik performansını kopyalama sorunlarını giderme hakkında bilgi edinin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/07/2021
ms.openlocfilehash: ce7c97abfb879e9298edac5f38540bbc026274da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584432"
---
# <a name="troubleshoot-copy-activity-performance"></a>Kopyalama etkinliği performansını sorun giderme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede Azure Data Factory ' de kopyalama etkinliği performans sorununu giderme konusu özetlenmektedir. 

Bir kopyalama etkinliği çalıştırdıktan sonra, [kopyalama etkinliği izleme](copy-activity-monitoring.md) görünümünde sonuçları ve performans istatistiklerini toplayabilirsiniz. Bir örnek verilmiştir.

![Kopyalama etkinliği çalıştırma ayrıntılarını izle](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Performans ayarı ipuçları

Bazı senaryolarda, Data Factory bir kopyalama etkinliği çalıştırdığınızda Yukarıdaki örnekte gösterildiği gibi en üst kısımdaki **"performans ayarlama ipuçları"** nı görürsünüz. İpuçları, bu belirli bir kopya çalıştırması için ADF tarafından tanımlanan darboğazyı, kopyalama aktarım hızını nasıl arttıracaklarını gösteren önerilerden daha fazla bilgi sağlar. Yeniden konumlandırılan değişikliği yapmayı deneyin, sonra kopyayı yeniden çalıştırın.

Bir başvuru olarak şu anda performans ayarlama ipuçları aşağıdaki durumlar için öneriler sağlar:

| Kategori              | Performans ayarı ipuçları                                      |
| --------------------- | ------------------------------------------------------------ |
| Veri deposuna özgü   | **Azure SYNAPSE Analytics**'e veri yükleme: kullanılmıyorsa PolyBase veya Copy deyimlerini kullanmayı önerin. |
| &nbsp;                | Verileri **Azure SQL veritabanı**'ndan kopyalama: DTU yüksek kullanım altındaysa, daha yüksek katmana yükseltmeyi önerin. |
| &nbsp;                | Veri kopyalama/hedef **Azure Cosmos DB**: ru yüksek kullanım altındayken, daha büyük ru 'ya yükseltmeyi önerin. |
|                       | **SAP tablosundan** veri kopyalama: büyük miktarda veriyi kopyalarken, paralel yüklemeyi etkinleştirmek ve en fazla bölüm numarasını ARTıRMAK için SAP bağlayıcısının bölüm seçeneğinden yararlanın. |
| &nbsp;                | **Amazon Redshift**'tan veri almak: KULLANıLMıYORSA, kaldırma kullanmayı önerin. |
| Veri depolama alanı azaltma | Kopyalama sırasında veri deposu tarafından bir dizi okuma/yazma işlemi kısıtlanıyor ise, veri deposu için izin verilen istek hızını denetlemeyi ve artırmayı önerin ya da eşzamanlı iş yükünü azaltın. |
| Tümleştirme çalışma zamanı  | Şirket içinde barındırılan bir **Integration Runtime (IR)** kullanıyorsanız ve kopyalama ETKINLIĞI, IR 'nin yürütülmesi için kullanılabilir kaynağa sahip olana kadar kuyrukta uzun süre bekliyorsa, IR 'nin ölçeğini genişletme/büyütme önerin. |
| &nbsp;                | En uygun olmayan bir bölgede bulunan bir **Azure Integration Runtime** kullanırsanız, yavaş okuma/yazma ile sonuçlanır, başka bir bölgede bir IR kullanmak için yapılandırmayı önerin. |
| Hataya dayanıklılık       | Hata toleransını yapılandırır ve uyumsuz satırları atlayarak performansı düşürür, kaynak ve havuz verilerinin uyumlu olmasını önerin. |
| Hazırlanmış kopya           | Hazırlanan kopya yapılandırıldıysa ancak kaynak havuzu çiftinde yararlı değilse, kaldırmayı önerin. |
| Sürdür                | Kopyalama etkinliği son hata noktasından devam ettirildiğinde, ancak özgün çalıştırmasından sonra DIU ayarını değiştirmeniz durumunda, yeni DIU ayarının etkili olmadığına göz atın. |

## <a name="understand-copy-activity-execution-details"></a>Kopyalama etkinliği yürütme ayrıntılarını anlama

Kopyalama etkinliği izleme görünümünün en altındaki yürütme ayrıntıları ve süreleri, kopyalama etkinliğinizin (Bu makalenin başındaki örnek), özellikle de kopyalama performansının giderilmesi için yararlı olan anahtar aşamalarını açıklar. Kopya çalışmalarınızın performans sorunu en uzun süreye sahip bir süredir. Her bir aşamanın tanımında aşağıdaki tabloya başvurun ve [Azure IR kopyalama etkinliğinin nasıl giderileceğini](#troubleshoot-copy-activity-on-azure-ir) ve bu tür bilgiyle şirket IÇINDE [barındırılan IR 'de kopyalama etkinliğinin nasıl giderileceğini](#troubleshoot-copy-activity-on-self-hosted-ir) öğrenin.

| Aşama           | Description                                                  |
| --------------- | ------------------------------------------------------------ |
| Kuyruk           | Kopyalama etkinliği tümleştirme çalışma zamanı üzerinde çalışmaya başlanana kadar geçen süre. |
| Kopyalama öncesi betiği | IR ve kopyalama etkinliğinden itibaren kopyalama etkinliği arasındaki geçen süre, havuz veri deposunda kopyalama öncesi betiği yürütmeyi tamamlıyor. Veritabanı havuzları için kopyalama öncesi betiği yapılandırdığınızda, örneğin Azure SQL veritabanı 'na veri yazarken yeni verileri kopyalamaya başlamadan önce bu uygulamayı temizleyebilirsiniz. |
| Aktarma        | Önceki adımın sonu ile IR 'nin tüm verileri kaynaktan havuza aktarma arasındaki geçen süre. <br/>Aktarım çalıştırmasının altındaki alt adımlara paralel olarak not alın ve bazı işlemler, örnek olarak ayrıştırma/oluşturma.<br><br/>- **İlk bayta kalan süre:** Önceki adımın sonu ile IR 'nin kaynak veri deposundan ilk baytı aldığı zaman arasında geçen süre. Dosya tabanlı olmayan kaynaklar için geçerlidir.<br>- **Listeleme kaynağı:** Kaynak dosyalarını veya veri bölümlerini listelemek için harcanan sürenin miktarı. İkincisi, veritabanı kaynakları için bölüm seçeneklerini yapılandırdığınızda, ör. Oracle/SAP HANA/Teradata/Netezza/vb gibi veritabanlarından veri kopyalama yaparken geçerlidir.<br/>-**Kaynaktan okunuyor:** Kaynak veri deposundan verileri almak için harcanan sürenin miktarı.<br/>- **Havuza yazma:** Havuz veri deposuna veri yazmak için harcanan sürenin miktarı. Not Bazı bağlayıcılarda Azure Bilişsel Arama, Azure Veri Gezgini, Azure Tablo depolama, Oracle, SQL Server, Common Data Service, Dynamics 365, Dynamics CRM, Salesforce/Salesforce hizmeti bulutu dahil bu ölçüm yoktur. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Azure IR kopyalama etkinliğinin sorunlarını giderme

Senaryolarınız için performans testi planlamak ve yürütmek için [performans ayarlama adımlarını](copy-activity-performance.md#performance-tuning-steps) izleyin. 

Kopyalama etkinliği performansı beklentilerinizi karşılamıyorsa, Azure Integration Runtime üzerinde çalışan tek kopyalama etkinliğinin sorunlarını gidermek için, kopyalama izleme görünümünde gösterilen [performans ayarlama ipuçları](#performance-tuning-tips) ' nı görürseniz, öneriyi uygulayın ve yeniden deneyin. Aksi takdirde, [kopyalama etkinliği yürütme ayrıntılarını anlayın](#understand-copy-activity-execution-details), **en uzun** süreye sahip olan aşamayı denetleyin ve kopyalama performansını artırmak için aşağıdaki kılavuzu uygulayın:

- **"Kopyalama öncesi betiği" uzun süre yaşadı:** havuz veritabanında çalışan kopyalama öncesi betiğin tamamlanmasının uzun sürmesi anlamına gelir. Performansı geliştirmek için belirtilen kopyalama öncesi betik mantığını ayarlayın. Betiği geliştirmeye yönelik daha fazla yardıma ihtiyacınız varsa veritabanı ekibinize başvurun.

- **"Aktarım-ilk bayta kalan süre" uzun çalışma süresi yaşadı**: kaynak sorgunuzun herhangi bir veri döndürmesi uzun sürme anlamına gelir. Sorguyu veya sunucuyu denetleyin ve iyileştirin. Daha fazla yardıma ihtiyacınız varsa veri Mağazası ekibinize başvurun.

- **"Aktarım listeleme kaynağı" uzun çalışma süresi yaşadı**: kaynak dosyalarının listelenmesi veya kaynak veritabanının veri bölümlerinin yavaş olması anlamına gelir.
  - Dosya tabanlı kaynaktan veri kopyalarken, klasör yolu veya dosya adı (veya) üzerinde **joker karakter filtresi** kullanırsanız `wildcardFolderPath` `wildcardFileName` veya **dosya son değiştirme zamanı filtresi** ( `modifiedDatetimeStart` veya `modifiedDatetimeEnd` ) kullanıyorsanız, bu filtre kopyalama etkinliğinin belirtilen klasörün altındaki tüm dosyaları istemci tarafına listelemesine neden olur ve sonra filtreyi uygular. Bu tür dosya numaralandırması, özellikle de yalnızca küçük bir dosya kümesi filtre kuralını karşıladığında performans sorunlarına neden olabilir.

    - [Dosyaları, tarih saat bölümlenmiş dosya yoluna veya adına göre kopyalayıp kopyalayamayacağını](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)denetleyin. Bu şekilde, kaynak tarafında liste üzerinde yük getirmez.

    - Bunun yerine veri deposunun yerel filtresini kullanıp kullananınızın, özellikle Amazon S3/Azure Blob/Azure dosya depolaması ve "**listafter/listBefore** ADLS 1." için özel olarak "**önek**" gibi. Bu filtreler veri deposu sunucu tarafı filtreleridir ve çok daha iyi performansa sahip olur.

    - Tek büyük veri kümesini birkaç küçük veri kümesine bölmek ve bu kopyalama işlerinin verilerin her bir tackles bölümünü aynı anda çalıştırmasına izin vermek için göz önünde bulundurun. Bunu arama/GetMetadata + ForEach + Copy ile yapabilirsiniz. [Birden çok kapsayıcıdan dosya kopyalama](solution-template-copy-files-multiple-containers.md) veya [Amazon S3 'ten verileri](solution-template-migration-s3-azure.md) genel örnek olarak ADLS 2. çözüm şablonlarına geçirme bölümüne bakın.

  - ADF 'nin kaynakta azaltma hatası olduğunu veya veri deponuzda yüksek kullanım durumunda olup olmadığını denetleyin. Bu durumda, veri deposundaki iş yüklerinizi azaltın veya azaltma sınırını veya kullanılabilir kaynağı artırmak için veri deposu yöneticinizle iletişim kurmayı deneyin.

  - Azure IR, kaynak veri deposu bölgenize aynı veya yakın bir şekilde kullanın.

- **"Kaynaktan okuma aktarım" uzun çalışma süresi yaşadı**: 

  - Varsa bağlayıcıya özgü verileri yükleme en iyi uygulamasını benimseyin. Örneğin, [Amazon Redshift](connector-amazon-redshift.md)'tan veri kopyalarken, Redshift Unload kullanacak şekilde yapılandırın.

  - ADF 'nin kaynakta herhangi bir azaltma hatası olup olmadığını ve veri deponuzda yüksek kullanım altında olup olmadığını denetleyin. Bu durumda, veri deposundaki iş yüklerinizi azaltın veya azaltma sınırını veya kullanılabilir kaynağı artırmak için veri deposu yöneticinizle iletişim kurmayı deneyin.

  - Kopyalama kaynak ve havuz örüntüsünün olup olmadığını denetleyin: 

    - Kopyalama düzeniniz 4 ' ten fazla veri tümleştirme birimini (DIUs) destekliyorsa, Ayrıntılar [bölümünde bu bölüme](copy-activity-performance-features.md#data-integration-units) bakın, genellikle daha iyi performans almak için daha fazla performans sağlayabilirsiniz. 

    - Aksi takdirde, tek büyük veri kümesini birkaç küçük veri kümesine bölmeyi düşünün ve bu kopyalama işlerinin verilerin her bir tackles bölümünü aynı anda çalıştırmasına izin verin. Bunu arama/GetMetadata + ForEach + Copy ile yapabilirsiniz. Bkz. [birden çok kapsayıcıdan dosya kopyalama](solution-template-copy-files-multiple-containers.md), [Amazon S3 ' dan ADLS 2.](solution-template-migration-s3-azure.md)veya [toplu kopyalama ile genel örnek olarak bir denetim tablosu](solution-template-bulk-copy-with-control-table.md) çözüm şablonlarıyla veri geçirme.

  - Azure IR, kaynak veri deposu bölgenize aynı veya yakın bir şekilde kullanın.

- **"Havuza aktarım yazma" uzun çalışma süresi yaşadı**:

  - Varsa bağlayıcıya özgü verileri yükleme en iyi uygulamasını benimseyin. Örneğin, verileri [Azure SYNAPSE Analytics](connector-azure-sql-data-warehouse.md)'e kopyalarken PolyBase veya Copy deyimlerini kullanın. 

  - ADF 'nin havuzda herhangi bir azaltma hatası olduğunu veya veri deponuzda yüksek kullanım altında olup olmadığını denetleyin. Bu durumda, veri deposundaki iş yüklerinizi azaltın veya azaltma sınırını veya kullanılabilir kaynağı artırmak için veri deposu yöneticinizle iletişim kurmayı deneyin.

  - Kopyalama kaynak ve havuz örüntüsünün olup olmadığını denetleyin: 

    - Kopyalama düzeniniz 4 ' ten fazla veri tümleştirme birimini (DIUs) destekliyorsa, Ayrıntılar [bölümünde bu bölüme](copy-activity-performance-features.md#data-integration-units) bakın, genellikle daha iyi performans almak için daha fazla performans sağlayabilirsiniz. 

    - Aksi takdirde, [paralel kopyaları](copy-activity-performance-features.md)kademeli olarak ayarlayabilir, çok fazla sayıda paralel kopya bile performansa zarar verebilir.

  - Azure IR, havuz veri deposu bölgenize aynı veya yakın bir şekilde kullanın.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Şirket içinde barındırılan IR 'de kopyalama etkinliğinin sorunlarını giderme

Senaryolarınız için performans testi planlamak ve yürütmek için [performans ayarlama adımlarını](copy-activity-performance.md#performance-tuning-steps) izleyin. 

Kopyalama performansı beklentilerinizi karşılamıyorsa, Azure Integration Runtime üzerinde çalışan tek kopyalama etkinliğinin sorunlarını gidermek için, kopyalama izleme görünümünde gösterilen [performans ayarlama ipuçları](#performance-tuning-tips) ' nı görürseniz, öneriyi uygulayın ve yeniden deneyin. Aksi takdirde, [kopyalama etkinliği yürütme ayrıntılarını anlayın](#understand-copy-activity-execution-details), **en uzun** süreye sahip olan aşamayı denetleyin ve kopyalama performansını artırmak için aşağıdaki kılavuzu uygulayın:

- **"Kuyruk" uzun süre yaşadı:** bu, şirket IÇINDE barındırılan IR 'nin yürütülmesi için kaynağa sahip olana kadar kopyalama etkinliğinin sırada beklediği anlamına gelir. IR kapasitesini ve kullanımını denetleyin ve iş yükünüze göre [ölçeği artırma veya genişletme](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

- **"Aktarım-ilk bayta kalan süre" uzun çalışma süresi yaşadı**: kaynak sorgunuzun herhangi bir veri döndürmesi uzun sürme anlamına gelir. Sorguyu veya sunucuyu denetleyin ve iyileştirin. Daha fazla yardıma ihtiyacınız varsa veri Mağazası ekibinize başvurun.

- **"Aktarım listeleme kaynağı" uzun çalışma süresi yaşadı**: kaynak dosyalarının listelenmesi veya kaynak veritabanının veri bölümlerinin yavaş olması anlamına gelir.

  - Şirket içinde barındırılan IR makinesinin, kaynak veri deposuna bağlanmak için düşük gecikme süresine sahip olup olmadığını denetleyin. Kaynağınız Azure 'da ise, şirket içinde barındırılan IR makinesinden Azure bölgesine gecikme süresini denetlemek için [Bu aracı](http://www.azurespeed.com/Azure/Latency) kullanabilirsiniz.

  - Dosya tabanlı kaynaktan veri kopyalarken, klasör yolu veya dosya adı (veya) üzerinde **joker karakter filtresi** kullanırsanız `wildcardFolderPath` `wildcardFileName` veya **dosya son değiştirme zamanı filtresi** ( `modifiedDatetimeStart` veya `modifiedDatetimeEnd` ) kullanıyorsanız, bu filtre kopyalama etkinliğinin belirtilen klasörün altındaki tüm dosyaları istemci tarafına listelemesine neden olur ve sonra filtreyi uygular. Bu tür dosya numaralandırması, özellikle de yalnızca küçük bir dosya kümesi filtre kuralını karşıladığında performans sorunlarına neden olabilir.

    - [Dosyaları, tarih saat bölümlenmiş dosya yoluna veya adına göre kopyalayıp kopyalayamayacağını](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)denetleyin. Bu şekilde, kaynak tarafında liste üzerinde yük getirmez.

    - Bunun yerine veri deposunun yerel filtresini kullanıp kullananınızın, özellikle Amazon S3/Azure Blob/Azure dosya depolaması ve "**listafter/listBefore** ADLS 1." için özel olarak "**önek**" gibi. Bu filtreler veri deposu sunucu tarafı filtreleridir ve çok daha iyi performansa sahip olur.

    - Tek büyük veri kümesini birkaç küçük veri kümesine bölmek ve bu kopyalama işlerinin verilerin her bir tackles bölümünü aynı anda çalıştırmasına izin vermek için göz önünde bulundurun. Bunu arama/GetMetadata + ForEach + Copy ile yapabilirsiniz. [Birden çok kapsayıcıdan dosya kopyalama](solution-template-copy-files-multiple-containers.md) veya [Amazon S3 'ten verileri](solution-template-migration-s3-azure.md) genel örnek olarak ADLS 2. çözüm şablonlarına geçirme bölümüne bakın.

  - ADF 'nin kaynakta azaltma hatası olduğunu veya veri deponuzda yüksek kullanım durumunda olup olmadığını denetleyin. Bu durumda, veri deposundaki iş yüklerinizi azaltın veya azaltma sınırını veya kullanılabilir kaynağı artırmak için veri deposu yöneticinizle iletişim kurmayı deneyin.

- **"Kaynaktan okuma aktarım" uzun çalışma süresi yaşadı**: 

  - Şirket içinde barındırılan IR makinesinin, kaynak veri deposuna bağlanmak için düşük gecikme süresine sahip olup olmadığını denetleyin. Kaynağınız Azure 'da ise, şirket içinde barındırılan IR makinesinden Azure bölgelerine gecikme süresini denetlemek için [Bu aracı](http://www.azurespeed.com/Azure/Latency) kullanabilirsiniz.

  - Şirket içinde barındırılan IR makinesinde verileri okumak ve verileri verimli bir şekilde aktarmak için yeterli gelen bant genişliğine sahip olup olmadığını denetleyin. Kaynak veri depolduğunuz Azure 'da olması, indirme hızını denetlemek için [Bu aracı](https://www.azurespeed.com/Azure/Download) kullanabilirsiniz.

  - Data Factory-> genel bakış sayfanıza Azure portal > şirket içinde barındırılan IR 'nin CPU ve bellek kullanımı eğilimini kontrol edin. CPU kullanımı yüksek veya kullanılabilir bellek düşükse, [ölçeği artırma/genişletme](create-self-hosted-integration-runtime.md#high-availability-and-scalability) için göz önünde bulundurun.

  - Varsa bağlayıcıya özgü verileri yükleme en iyi uygulamasını benimseyin. Örnek:

    - [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP tablosu](connector-sap-table.md#sap-table-as-source)ve [SAP açık hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)'dan verileri kopyalarken, verileri paralel olarak kopyalamak için veri bölümü seçeneklerini etkinleştirin.

    - Bir [sunucudan verileri](connector-hdfs.md)kopyalarken, distcp kullanacak şekilde yapılandırın.

    - [Amazon Redshift](connector-amazon-redshift.md)'tan veri kopyalarken, Redshift Unload kullanacak şekilde yapılandırın.

  - ADF 'nin kaynakta herhangi bir azaltma hatası olup olmadığını ve veri deponuzda yüksek kullanım altında olup olmadığını denetleyin. Bu durumda, veri deposundaki iş yüklerinizi azaltın veya azaltma sınırını veya kullanılabilir kaynağı artırmak için veri deposu yöneticinizle iletişim kurmayı deneyin.

  - Kopyalama kaynak ve havuz örüntüsünün olup olmadığını denetleyin: 

    - Bölüm seçeneği etkinleştirilmiş veri depolarından verileri kopyalarsanız, [paralel kopyaları](copy-activity-performance-features.md)kademeli olarak ayarlamayı düşünün. çok fazla paralel kopya, performansı da etkileyebilir.

    - Aksi takdirde, tek büyük veri kümesini birkaç küçük veri kümesine bölmeyi düşünün ve bu kopyalama işlerinin verilerin her bir tackles bölümünü aynı anda çalıştırmasına izin verin. Bunu arama/GetMetadata + ForEach + Copy ile yapabilirsiniz. Bkz. [birden çok kapsayıcıdan dosya kopyalama](solution-template-copy-files-multiple-containers.md), [Amazon S3 ' dan ADLS 2.](solution-template-migration-s3-azure.md)veya [toplu kopyalama ile genel örnek olarak bir denetim tablosu](solution-template-bulk-copy-with-control-table.md) çözüm şablonlarıyla veri geçirme.

- **"Havuza aktarım yazma" uzun çalışma süresi yaşadı**:

  - Varsa bağlayıcıya özgü verileri yükleme en iyi uygulamasını benimseyin. Örneğin, verileri [Azure SYNAPSE Analytics](connector-azure-sql-data-warehouse.md)'e kopyalarken PolyBase veya Copy deyimlerini kullanın. 

  - Şirket içinde barındırılan IR makinesinin, havuz veri deposuna bağlanma sırasında düşük gecikme süresine sahip olup olmadığını denetleyin. Havuzunuzu Azure 'da kullanıyorsanız, [Bu aracı](http://www.azurespeed.com/Azure/Latency) kullanarak şirket IÇINDE barındırılan IR makinesinden Azure bölgesine gecikme süresini denetleyebilir.

  - Şirket içinde barındırılan IR makinesinin verileri etkili bir şekilde aktarımına ve yazmaya yetecek kadar giden bant genişliğine sahip olup olmadığını denetleyin. Havuz veri depoluizin Azure 'da ise, yükleme hızını denetlemek için [Bu aracı](https://www.azurespeed.com/Azure/UploadLargeFile) kullanabilirsiniz.

  - Data Factory-> genel bakış sayfanıza Azure portal > şirket içinde barındırılan IR 'nin CPU ve bellek kullanımı eğilimi olup olmadığını denetleyin. CPU kullanımı yüksek veya kullanılabilir bellek düşükse, [ölçeği artırma/genişletme](create-self-hosted-integration-runtime.md#high-availability-and-scalability) için göz önünde bulundurun.

  - ADF 'nin havuzda herhangi bir azaltma hatası olduğunu veya veri deponuzda yüksek kullanım altında olup olmadığını denetleyin. Bu durumda, veri deposundaki iş yüklerinizi azaltın veya azaltma sınırını veya kullanılabilir kaynağı artırmak için veri deposu yöneticinizle iletişim kurmayı deneyin.

  - [Paralel kopyaları](copy-activity-performance-features.md)kademeli olarak ayarlamayı göz önünde bulundurun. çok fazla paralel kopya, hatta performansın zarar görmediğini unutmayın.


## <a name="connector-and-ir-performance"></a>Bağlayıcı ve IR performansı 

Bu bölüm, belirli bağlayıcı türü veya tümleştirme çalışma zamanı için bazı performans sorun giderme kılavuzlarını araştırır.

### <a name="activity-execution-time-varies-using-azure-ir-vs-azure-vnet-ir"></a>Etkinlik yürütme süresi, Azure IR ile Azure VNet IR kullanımını değiştirir

Etkinlik yürütme süresi, veri kümesinin farklı Integration Runtime temel aldığı zaman değişir.

- **Belirtiler**: veri kümesindeki bağlantılı hizmet açılan listesini değiştirmek, aynı işlem hattı etkinliklerini gerçekleştirir, ancak büyük ölçüde farklı çalışma sürelerine sahip olur. Veri kümesi, yönetilen sanal ağ Integration Runtime temel alıyorsa, varsayılan Integration Runtime baz alınarak, bu sürenin ortalaması daha fazla zaman alır.  

- **Neden**: işlem hattı çalışmalarının ayrıntılarını kontrol etmek için, normal bir işlem Azure IR üzerinde çalışırken, yavaş Işlem hattının yönetilen VNET (sanal ağ) IR üzerinde çalıştığını görebilirsiniz. Tasarıma göre, yönetilen VNet IR, Veri Fabrikası başına bir işlem düğümü ayırmadığımızda Azure IR sıra süresini daha uzun sürer. bu nedenle, her kopyalama etkinliğinin başlaması için bir sıcak değer vardır ve öncelikle Azure IR yerine VNet JOIN üzerinde gerçekleşir. 



    
### <a name="low-performance-when-loading-data-into-azure-sql-database"></a>Azure SQL veritabanı 'na veri yüklenirken düşük performans

- **Belirtiler**: Azure SQL veritabanı 'na veri kopyalama yavaş olur.

- **Neden**: sorunun kök nedeni çoğunlukla Azure SQL veritabanı 'nın performans sorunlarına karşı tetiklenir. Olası bazı nedenler şunlardır:

    - Azure SQL veritabanı katmanı yeterince yüksek değil.

    - Azure SQL veritabanı DTU kullanımı %100 ' e yakın. [Performansı izleyebilir](../azure-sql/database/monitor-tune-overview.md) ve Azure SQL veritabanı katmanını yükseltmeyi göz önünde bulundurun.

    - Dizinler düzgün ayarlanmadı. Veri yüklemeden önce tüm dizinleri kaldırın ve yükleme tamamlandıktan sonra yeniden oluşturun.

    - WriteBatchSize, şema satır boyutuna sığacak kadar büyük değil. Sorunun özelliğini genişletmenize çalışın.

    - Toplu iç içe değil, saklı yordam kullanılıyor, bu da daha kötü performansa sahip olması beklenir. 


### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>Büyük Excel dosyası ayrıştırılırken zaman aşımı veya yavaş performans

- **Belirtiler**:

    - Excel veri kümesi oluşturduğunuzda ve şemayı bağlantı/depolama alanından içeri aktardığınızda, verileri Önizle, Listele veya Yenile, Excel dosyası boyutta büyükse zaman aşımı hatası ile karşılaşabilirsiniz.

    - Büyük Excel dosyasından (>= 100 MB) verileri başka veri deposuna kopyalamak için kopyalama etkinliğini kullandığınızda, yavaş performans veya OOM sorunu yaşayabilirsiniz.

- **Neden**: 

    - Excel veri kümesindeki şemayı içeri aktarma, verileri önizleme ve çalışma sayfalarını listeleme gibi işlemler için zaman aşımı 100 s ve statiktir. Büyük Excel dosyası için, bu işlemler zaman aşımı değeri içinde bitmeyebilir.

    - ADF kopyalama etkinliği tüm Excel dosyasını belleğe okur ve verileri okumak için belirtilen çalışma sayfasını ve hücreleri bulur. Bu davranış, temel alınan ADF tarafından kullanılan SDK 'nın kullanımından kaynaklanır.

- **Çözüm**: 

    - Şemayı içeri aktarmak için, özgün dosyanın bir alt kümesi olan daha küçük bir örnek dosya oluşturabilir ve "şemayı bağlantı/depodan içeri aktar" yerine "örnek dosyadan şemayı içeri aktar" seçeneğini belirleyebilirsiniz.

    - Çalışma sayfası listesinde, çalışma sayfası açılan menüsünde "Düzenle" düğmesine tıklayabilir ve bunun yerine sayfa adını/dizinini girebilirsiniz.

    - Büyük Excel dosyasını (>100 MB) başka bir depoya kopyalamak için, spor akışı okuma ve daha iyi bir şekilde gerçekleştirdiğiniz veri akışı Excel kaynağını kullanabilirsiniz.
    
## <a name="other-references"></a>Diğer başvurular

Desteklenen bazı veri depoları için performans izleme ve ayarlama başvuruları aşağıda verilmiştir:

* Azure Blob depolama: BLOB depolama için [ölçeklenebilirlik ve performans hedefleri](../storage/blobs/scalability-targets.md) , [BLOB depolaması için performans ve ölçeklenebilirlik denetim listesi](../storage/blobs/storage-performance-checklist.md).
* Azure Tablo Depolama: tablo depolaması için tablo depolama ve performans [ve ölçeklenebilirlik denetim listesi](../storage/tables/storage-performance-checklist.md) [için ölçeklenebilirlik ve performans hedefleri](../storage/tables/scalability-targets.md) .
* Azure SQL veritabanı: [performansı izleyebilir](../azure-sql/database/monitor-tune-overview.md) ve veritabanı işlem BIRIMI (DTU) yüzdesini kontrol edebilirsiniz.
* Azure SYNAPSE Analytics: özelliği veri ambarı birimlerinde (DWU) ölçülür. Bkz. [Azure SYNAPSE Analytics 'te işlem gücünü yönetme (genel bakış)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Azure Cosmos DB performans düzeyleri](../cosmos-db/performance-levels.md).
* SQL Server: [performansı izleme ve ayarlama](/sql/relational-databases/performance/monitor-and-tune-for-performance).
* Şirket içi dosya sunucusu: [dosya sunucuları Için performans ayarlama](/previous-versions//dn567661(v=vs.85)).

## <a name="next-steps"></a>Sonraki adımlar
Diğer kopyalama etkinliği makalelerine bakın:

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Kopyalama etkinliği performans ve ölçeklenebilirlik kılavuzu](copy-activity-performance.md)
- [Etkinlik performansını en iyi duruma getirme özelliklerini Kopyala](copy-activity-performance-features.md)
- [Data Lake veya veri ambarınızdan verileri Azure 'a geçirmek için Azure Data Factory kullanın](data-migration-guidance-overview.md)
- [Verileri Amazon S3'ten Azure Depolama'ya geçirme](data-migration-guidance-s3-azure-storage.md)
