---
title: Şirket içi Hadoop kümesinden Azure Storage 'a veri geçirmek için Azure Data Factory kullanma
description: Şirket içi Hadoop kümesinden Azure Storage 'a veri geçirmek için Azure Data Factory nasıl kullanacağınızı öğrenin.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/30/2019
ms.openlocfilehash: b952be49bf5bc00b338aa04ed51e9dc451b5c4f9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675823"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Şirket içi Hadoop kümesinden Azure Storage 'a veri geçirmek için Azure Data Factory kullanma 

Azure Data Factory, verileri Şirket içinden Azure Blob depolama veya Azure Data Lake Storage 2. ölçeğinde bir performans, sağlam ve ekonomik bir mekanizma sağlar. 

Data Factory, şirket içi bir sunucudan Azure 'a veri geçirmek için iki temel yaklaşım sunar. Senaryonuza göre yaklaşımı seçebilirsiniz. 

- **Data Factory dıtcp modu** (önerilir): Data Factory içinde, dosyaları Azure Blob depolama ( [hazırlanan kopya](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)dahil) veya Azure Data Lake Store Gen2 olarak kopyalamak için [distcp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (dağıtılmış kopya) kullanabilirsiniz. En iyi kopyalama aktarım hızını elde etmek için, mevcut güçlü bir kümeden yararlanmak üzere DistCp ile tümleştirilmiş Data Factory kullanın. Ayrıca, esnek zamanlamanın avantajını ve Data Factory ' den Birleşik bir izleme deneyimini de elde edersiniz. Data Factory yapılandırmanıza bağlı olarak, kopyalama etkinliği otomatik olarak bir DistCp komutu oluşturur, verileri Hadoop kümenize gönderir ve kopyalama durumunu izler. Şirket içi Hadoop kümesinden Azure 'a veri geçirmek için Data Factory DistCp modu önerilir.
- **Data Factory yerel tümleştirme çalışma zamanı modu**: tek TCP, tüm senaryolarda bir seçenek değildir. Örneğin, bir Azure sanal ağları ortamında, DistCp Aracı, Azure depolama sanal ağ uç noktası ile Azure ExpressRoute özel eşlemesini desteklemez. Ayrıca, bazı durumlarda, var olan Hadoop kümenizi verileri geçirmek için bir altyapı olarak kullanmak istemezsiniz, böylece kümenize ağır yükleme yerleştirmezsiniz ve bu da mevcut ETL işlerinin performansını etkileyebilir. Bunun yerine, verileri Şirket içinden Azure 'a kopyalayan motor olarak Data Factory tümleştirme çalışma zamanının yerel özelliğini kullanabilirsiniz.

Bu makalede her iki yaklaşım hakkında aşağıdaki bilgiler sunulmaktadır:
> [!div class="checklist"]
> * Performans 
> * Esnekliği Kopyala
> * Ağ güvenliği
> * Üst düzey çözüm mimarisi 
> * En iyi uygulama uygulamaları  

## <a name="performance"></a>Performans

Data Factory DistCp modunda, üretilen işlem, ayrı TCP aracını bağımsız olarak kullanmanıza benzer. Data Factory, mevcut Hadoop kümenizin kapasitesini en üst düzeye çıkarır. Büyük küme içi veya küme içi kopyalama için DistCp kullanabilirsiniz. 

DistCp, dağıtım, hata işleme ve kurtarma ve raporlamayı yürürlüğe eklemek için MapReduce kullanır. Görev eşleme için giriş olarak bir dosya ve dizin listesi genişletir. Her görev, kaynak listesinde belirtilen bir dosya bölümünü kopyalar. Ortamınızdaki veri taşıma aktarım hızını en üst düzeye çıkarmak için ağ bant genişliğinizi, depolama ıOPS 'nizi ve bant genişliğinizi tam olarak kullanabilmek amacıyla işlem hatları oluşturmak üzere DistCp ile tümleştirilmiş Data Factory kullanabilirsiniz.  

Data Factory yerel tümleştirme çalışma zamanı modu, farklı düzeylerde paralellik de sağlar. Veri taşıma aktarım hızını en üst düzeye çıkarmak için ağ bant genişliğinizi, depolama ıOPS 'nizi ve bant genişliğini tam olarak kullanabilmek için paralellik kullanabilirsiniz:

- Tek bir kopyalama etkinliği, ölçeklenebilir işlem kaynaklarından yararlanabilir. Şirket içinde barındırılan bir tümleştirme çalışma zamanı ile makineyi el ile ölçeklendirebilir veya birden çok makineye ([en fazla dört düğüme](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) ölçeklendirebilirsiniz. Tek bir kopyalama etkinliği, dosya kümesini tüm düğümlerde bölümler. 
- Tek bir kopyalama etkinliği, birden çok iş parçacığı kullanarak veri deposundan okur ve yazar. 
- Data Factory denetim akışı, paralel olarak birden çok kopyalama etkinliği başlatabilir. Örneğin, [her döngü için](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)bir kullanabilirsiniz. 

Daha fazla bilgi için bkz. [kopyalama etkinliği performans Kılavuzu](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Dayanıklılık

Data Factory DistCp modunda, farklı esnekliği seviyeleri için farklı DistCp komut satırı parametreleri (örneğin, `-i`, sorunları Yoksay veya `-update`, kaynak dosya ve hedef dosya boyutu farklı olduğunda veri yaz) kullanabilirsiniz.

Data Factory yerel tümleştirme çalışma zamanı modunda, tek bir kopyalama etkinliği çalıştırmasında, Data Factory yerleşik bir yeniden deneme mekanizması vardır. Veri depolarında veya temel ağdaki belirli bir geçici başarısızlık düzeyini işleyebilir. 

Şirket içi bir sunucudan blob depolamaya ve şirket içi Data Lake Store Gen2 'e ikili kopyalama yaparken, Data Factory otomatik olarak büyük bir ölçüde işaret noktası gerçekleştirir. Bir kopyalama etkinliği başarısız olursa veya zaman aşımına uğrarsa, sonraki yeniden denemelerde (yeniden deneme sayısı > 1 olduğundan emin olun), kopya başlangıçta başlamak yerine son hata noktasından devam eder.

## <a name="network-security"></a>Ağ güvenliği 

Varsayılan olarak, Data Factory şirket içi sunucudan blob depolamaya veya HTTPS protokolü üzerinden şifrelenmiş bir bağlantı kullanarak Azure Data Lake Storage 2. veri aktarır. HTTPS, aktarım sırasında veri şifrelemesi sağlar ve gizlice dinleme ve ortadaki adam saldırıları önler. 

Alternatif olarak, verilerin genel İnternet üzerinden aktarılmasını istemiyorsanız, daha yüksek güvenlik için bir özel eşleme bağlantısı üzerinden ExpressRoute aracılığıyla veri aktarabilirsiniz. 

## <a name="solution-architecture"></a>Çözüm mimarisi

Bu görüntüde verilerin genel İnternet üzerinden geçirilmesi gösterilmektedir:

![Ortak ağ üzerinden veri taşımaya yönelik çözüm mimarisini gösteren diyagram](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- Bu mimaride, veriler genel İnternet üzerinden HTTPS kullanılarak güvenli bir şekilde aktarılır. 
- Ortak bir ağ ortamında Data Factory DistCp modunu kullanmanızı öneririz. En iyi kopyalama aktarım hızını elde etmek için, güçlü bir mevcut kümeden yararlanabilirsiniz. Ayrıca, Data Factory 'ten esnek zamanlama ve Birleşik izleme deneyimi avantajını de elde edersiniz.
- Bu mimaride, kendi kendine barındırılan tümleştirme çalışma zamanını, bir kurumsal güvenlik duvarının arkasındaki bir Windows makinesine Data Factory yükleyerek, Hadoop kümenize DistCp komutunu gönderebilirsiniz ve kopyalama durumunu izleyebilirsiniz. Makine, verileri taşıyacağınız bir altyapı olmadığından (yalnızca denetim amacı için), makinenin kapasitesi veri hareketinin verimini etkilemez.
- DistCp komutundan mevcut parametreler destekleniyor.

Bu görüntü, özel bir bağlantı üzerinden verilerin geçirilmesini gösterir: 

![Özel bir ağ üzerinden veri taşımaya yönelik çözüm mimarisini gösteren diyagram](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- Bu mimaride, veriler Azure ExpressRoute aracılığıyla özel bir eşleme bağlantısı üzerinden geçirilir. Veriler, genel İnternet üzerinden hiçbir şekilde gezmez.
- DistCp Aracı, Azure Storage sanal ağ uç noktası ile ExpressRoute özel eşlemesini desteklemez. Verileri geçirmek için tümleştirme çalışma zamanı aracılığıyla Data Factory yerel özelliğini kullanmanızı öneririz.
- Bu mimari için, Data Factory şirket içinde barındırılan tümleştirme çalışma zamanını Azure sanal ağınızdaki bir Windows sanal makinesine yüklemelisiniz. Ağınızı ve depolama ıOPS veya bant genişliğinizi tam olarak kullanabilmeniz için sanal makinelerinizi el ile ölçeklendirebilir veya birden çok VM 'ye ölçeklendirebilirsiniz.
- Her bir Azure VM için (Data Factory şirket içinde barındırılan tümleştirme çalışma zamanı yüklü) ile başlamak için önerilen yapılandırma, 32 vCPU ve 128 GB bellek ile Standard_D32s_v3. Veri geçişi sırasında VM 'nin CPU ve bellek kullanımını, daha iyi performans için sanal makineyi ölçeklendirmeniz gerekip gerekmediğini ve maliyeti azaltmak için VM 'nin ölçeğini azaltmayı seçebilirsiniz.
- Ayrıca, tek bir şirket içinde barındırılan tümleştirme çalışma zamanına sahip dört VM düğümünü ilişkilendirerek da ölçeği genişletebilirsiniz. Şirket içinde barındırılan tümleştirme çalışma zamanına karşı çalışan tek bir kopyalama işi otomatik olarak dosya kümesini bölümlendirir ve dosyaları paralel olarak kopyalamak için tüm VM düğümlerini kullanır. Yüksek kullanılabilirlik için, veri geçişi sırasında tek noktadan oluşan bir senaryoyu önlemek üzere iki VM düğümü ile başlamanız önerilir.
- Bu mimariyi kullandığınızda, ilk anlık görüntü veri geçişi ve Delta veri geçişi sizin için kullanılabilir.

## <a name="implementation-best-practices"></a>En iyi uygulama uygulamaları

Veri geçişinizi uygularken bu en iyi yöntemleri izlemeniz önerilir.

### <a name="authentication-and-credential-management"></a>Kimlik doğrulama ve kimlik bilgisi yönetimi 

- Bu durumda, bir [Windows (Kerberos) veya anonim](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties)olarak kimlik doğrulaması yapmak için kullanabilirsiniz. 
- Azure Blob depolamaya bağlanmak için birden çok kimlik doğrulama türü desteklenir.  [Azure kaynakları için yönetilen kimliklerin](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)kullanılması önemle önerilir. Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen Data Factory kimliğin üzerine inşa edilirken, Yönetilen kimlikler, bağlantılı hizmet tanımında kimlik bilgileri sağlamadan işlem hatlarını yapılandırmanıza olanak tanır. Alternatif olarak, bir [hizmet sorumlusu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [paylaşılan erişim imzası](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)veya [depolama hesabı anahtarı](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)kullanarak blob depolamada kimlik doğrulaması yapabilirsiniz. 
- Data Lake Storage 2. bağlantı için birden çok kimlik doğrulama türü de desteklenir.  [Azure kaynakları için Yönetilen kimlikler](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)kullanmanız önemle önerilir, ancak bir [hizmet sorumlusu](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) veya [depolama hesabı anahtarı](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)da kullanabilirsiniz. 
- Azure kaynakları için Yönetilen kimlikler kullanmadığınız durumlarda, Data Factory bağlı hizmetleri değiştirmeden anahtarları merkezi olarak yönetmeyi ve döndürmeyi kolaylaştırmak için [Azure Key Vault kimlik bilgilerinin depolanmasını](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) kesinlikle öneririz. Bu, [CI/CD için de en iyi uygulamadır](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>İlk anlık görüntü verilerini geçirme 

Data Factory DistCp modunda, DistCp komutunu göndermek ve ilk veri geçiş davranışını denetlemek için farklı parametreler kullanmak üzere bir kopyalama etkinliği oluşturabilirsiniz. 

Data Factory yerel tümleştirme çalışma zamanı modunda, özellikle 10 TB 'den fazla veri geçirdiğinizde veri bölümünün kullanılması önerilir. Verileri bölümlemek için, bu dosya adını Ardından, her bir Data Factory kopyalama işi aynı anda bir klasör bölümünü kopyalayabilir. Daha iyi üretilen iş için birden çok Data Factory kopyalama işini eşzamanlı olarak çalıştırabilirsiniz.

Ağ veya veri deposu geçici sorunları nedeniyle kopyalama işlerinin herhangi biri başarısız olursa, bu belirli bölümü Ise 'den yeniden yüklemek için başarısız kopyalama işini yeniden çalıştırabilirsiniz. Diğer bölümleri yükleyen diğer kopyalama işleri etkilenmez.

### <a name="delta-data-migration"></a>Delta verileri geçişi 

Data Factory DistCp modunda, Delta veri geçişi için, kaynak dosya ve hedef dosya boyutu farklı olduğunda veri yaz `-update`, DistCp komut satırı parametresini kullanabilirsiniz.

Data Factory yerel tümleştirme modunda, yeni veya değiştirilmiş dosyaları bir süre olarak belirlemek için en iyi yol, zaman bölümlenmiş bir adlandırma kuralı kullanmaktır. 1\. sırada verileriniz, dosya veya klasör adında saat dilimi bilgileriyle zaman dilimlendiğinde (örneğin, */yyyy/mm/dd/File.exe*), işlem hattı, artımlı olarak hangi dosya ve klasörlerin kopyalanacağını kolayca tanımlayabilir.

Alternatif olarak, LastModifiedDate ' deki veriniz zaman bölümlenmemiş değilse, Data Factory yeni veya değiştirilmiş dosyaları değerlerini kullanarak tanımlayabilir. Data Factory, tüm dosyaları da her bir küme değerinden büyük olan son değiştirme zaman damgasına sahip olan yeni ve güncelleştirilmiş dosyaları kopyalar. 

Bir çok sayıda dosyanız varsa, filtre koşuluna göre kaç dosyanın eşleşip eşleşmediğine bakılmaksızın ilk dosya taraması uzun zaman alabilir. Bu senaryoda ilk anlık görüntü geçişi için kullandığınız bölümü kullanarak verileri bölümlememenizi öneririz. Sonra, dosya tarama paralel olarak gerçekleşebilir.

### <a name="estimate-price"></a>Tahmin fiyatı 

Azure Blob Storage 'dan verileri bir sunucudan geçirmek için aşağıdaki ardışık düzeni göz önünde bulundurun: 

![Fiyatlandırma ardışık düzenini gösteren diyagram](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Aşağıdaki bilgileri alalım: 

- Toplam veri hacmi 1 PB 'dir.
- Data Factory yerel tümleştirme çalışma zamanı modunu kullanarak verileri geçirin.
- 1 PB, 1.000 bölüme ayrılmıştır ve her kopya bir bölüm taşıdır.
- Her kopyalama etkinliği, dört makine ile ilişkili olan ve 500 MBps aktarım hızına sahip olan kendiliğinden konak bir tümleştirme çalışma zamanı ile yapılandırılır.
- ForEach eşzamanlılık **4** olarak ayarlanır ve toplam üretilen Iş 2 GB 'dir.
- Toplamda, geçişin tamamlandığı 146 saat sürer.

Varsayımlarımıza göre tahmini fiyat aşağıda verilmiştir: 

![Fiyatlandırma hesaplamalarını gösteren tablo](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Bu bir kuramsal fiyatlandırma örneğidir. Gerçek fiyatlandırağınız, ortamınızdaki gerçek işleme göre değişir.
> Azure Windows VM 'nin (Şirket içinde barındırılan tümleştirme çalışma zamanı yüklü) fiyatı dahil değildir.

### <a name="additional-references"></a>Ek başvurular

- [Bağlantı ucu Bağlayıcısı](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob depolama Bağlayıcısı](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage 2. Bağlayıcısı](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Kopyalama etkinliği performans ayarlama Kılavuzu](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Şirket içinde barındırılan tümleştirme çalışma zamanı yüksek kullanılabilirlik ve ölçeklenebilirlik](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Veri taşıma güvenlik konuları](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Azure Key Vault kimlik bilgilerini depolama](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Bir dosyayı zaman bölümlenmiş bir dosya adına göre artımlı olarak Kopyala](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [LastModifiedDate göre yeni ve değiştirilmiş dosyaları Kopyala](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Data Factory fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory kullanarak birden çok kapsayıcıdan dosya kopyalama](solution-template-copy-files-multiple-containers.md)