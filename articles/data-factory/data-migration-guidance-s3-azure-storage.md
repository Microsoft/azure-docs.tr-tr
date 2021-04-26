---
title: Verileri Amazon S3'ten Azure Depolama'ya geçirme
description: Amazon S3 'ten Azure Storage 'a veri geçirmek için Azure Data Factory kullanın.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 2be8a9c7476bda6952ed1eaa15d29fe9c01b59a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371320"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Amazon S3 'ten Azure Storage 'a veri geçirmek için Azure Data Factory kullanma 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory, verileri Amazon S3 'ten Azure Blob depolama alanına veya Azure Data Lake Storage 2. geçirmek için bir performans, sağlam ve ekonomik bir mekanizma sağlar.  Bu makalede veri mühendisleri ve geliştiriciler için aşağıdaki bilgiler sağlanmaktadır: 

> [!div class="checklist"]
> * Performans 
> * Esnekliği Kopyala
> * Ağ güvenliği
> * Üst düzey çözüm mimarisi 
> * En iyi uygulama uygulamaları  

## <a name="performance"></a>Performans

ADF, farklı düzeylerde paralellik sağlayan sunucusuz bir mimari sunar ve bu sayede, ortamınız için veri taşıma aktarım hızını en üst düzeye çıkarmak amacıyla, geliştiricilerin ağ bant genişliğinizi ve depolama ıOPS ve bant genişliğini tamamen kullanmak üzere işlem hatları oluşturmalarına olanak tanır. 

Müşteriler, 2 GBps ve üzeri bir işlem hacmi sunan, Amazon S3 'den Azure Blob depolama alanına kadar yüzlerce milyonlarca dosyadan oluşan verileri petabaytlarca başarıyla geçirdiniz. 

![Diyagramda birçok dosya bölümü, ilişkili kopyalama eylemleri olan bir W S S3 deposunda, Azure Blob depolama A D L S Gen2 gösterilmektedir.](media/data-migration-guidance-s3-to-azure-storage/performance.png)

Yukarıdaki resimde, farklı paralellik seviyeleri aracılığıyla harika veri taşıma hızına nasıl ulaşabileceğiniz gösterilmektedir:
 
- Tek bir kopyalama etkinliği ölçeklenebilir işlem kaynaklarından yararlanabilir: Azure Integration Runtime kullanırken, her bir kopyalama etkinliği için sunucusuz bir şekilde [en fazla 256 DIUs](./copy-activity-performance.md#data-integration-units) belirtebilirsiniz; Şirket içinde barındırılan Integration Runtime kullanılırken, makineyi el ile ölçeklendirebilir veya birden fazla makineye ([4 düğüme kadar](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)) ölçeklendirebilirsiniz ve tek bir kopyalama etkinliği dosya kümesini tüm düğümlerde bölümleyebilir. 
- Tek bir kopyalama etkinliği, birden çok iş parçacığı kullanarak veri deposundan okur ve yazar. 
- ADF denetim akışı, örneğin [her döngü için](./control-flow-for-each-activity.md)kullanarak birden çok kopyalama etkinliğini paralel olarak başlatabilir. 

## <a name="resilience"></a>Esnekliği

Tek bir kopyalama etkinliği çalıştırmasında, ADF 'nin, veri depolarında veya temel ağdaki belirli bir geçici başarısızlık düzeyini işleyebilmesi için yerleşik yeniden deneme mekanizması vardır. 

S3 'den blob 'a ve S3 'ten ADLS 2. arasında ikili kopyalama yaparken, ADF otomatik olarak checkişaret gerçekleştirir.  Bir kopyalama etkinliği çalıştırması başarısız olduysa veya zaman aşımına uğramışsa, sonraki yeniden denemelerde, kopya baştan başlamak yerine son hata noktasından devam eder. 

## <a name="network-security"></a>Ağ güvenliği 

ADF, varsayılan olarak, Amazon S3 'den Azure Blob depolama alanına veya HTTPS protokolü üzerinden şifreli bağlantı kullanarak Azure Data Lake Storage 2. verileri aktarır.  HTTPS, aktarım sırasında veri şifrelemesi sağlar ve gizlice dinleme ve ortadaki adam saldırıları önler. 

Alternatif olarak, verilerin genel Internet üzerinden aktarılmasını istemiyorsanız, AWS Direct Connect ve Azure Express Route arasında özel bir eşleme bağlantısı üzerinden veri aktararak daha yüksek bir güvenlik elde edebilirsiniz.  Bu şekilde nasıl ulaşılabilecek aşağıda çözüm mimarisine bakın. 

## <a name="solution-architecture"></a>Çözüm mimarisi

Verileri genel Internet üzerinden geçirme:

![Diyagram, D F Azure ile Azure Storage arasında Azure Integration Runtime aracılığıyla bir W S S3 mağazasından Internet üzerinden geçişi gösterir. Çalışma zamanının Data Factory olan bir denetim kanalı vardır.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- Bu mimaride, veriler genel Internet üzerinden HTTPS kullanılarak güvenli bir şekilde aktarılır. 
- Hem kaynak Amazon S3 hem de hedef Azure Blob depolama veya Azure Data Lake Storage 2. tüm ağ IP adreslerinden gelen trafiğe izin verecek şekilde yapılandırılmıştır.  Belirli IP aralığına ağ erişimini nasıl kısıtlayabileceğini gösteren ikinci mimariye bakın. 
- Ortamınız için en iyi performansı elde edebilmeniz için ağınızı ve depolama bant genişliğinizi tam olarak kullanabilmeniz amacıyla, daha az bir şekilde, daha az sayıda horsegüç miktarını kolayca ölçeklendirebilirsiniz. 
- Bu mimari kullanılarak hem ilk anlık görüntü geçişi hem de Delta verileri geçişi elde edilebilir. 

Verileri özel bağlantı üzerinden geçir: 

![Diyagram, Azure sanal makinelerinde şirket içinde barındırılan tümleştirme çalışma zamanı aracılığıyla bir W S S3 deposundan özel bir eşleme bağlantısı üzerinden Azure depolama 'ya geçiş gösterir. Çalışma zamanının Data Factory olan bir denetim kanalı vardır.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- Bu mimaride veri geçişi, AWS doğrudan Connect ve Azure Express Route arasında özel bir eşleme bağlantısı üzerinden yapılır, bu da veriler hiçbir zaman ortak Internet üzerinden hiçbir zaman geçmez.  AWS VPC ve Azure sanal ağının kullanılması gerekir. 
- Bu mimariye ulaşmak için Azure sanal ağınızdaki bir Windows sanal makinesine ADF şirket içinde barındırılan tümleştirme çalışma zamanı 'nı yüklemeniz gerekir.  Ağınızı ve depolama ıOPS/bant genişliğinizi tamamen kullanabilmeniz için otomatik olarak barındırılan IR sanal makinelerinizi el ile ölçeklendirebilir veya birden çok VM 'ye (4 düğüme kadar) ölçeklendirebilirsiniz. 
- Verileri HTTPS üzerinden aktarmaya kabul edilebilir, ancak bu kaynak S3 'e ağ erişimini belirli bir IP aralığına kilitlemek istiyorsanız, AWS VPC 'yi kaldırıp özel bağlantıyı HTTPS ile değiştirerek bu mimarinin bir çeşidini benimseyebilirsiniz.  Filtreleme amacına yönelik statik, genel olarak yönlendirilebilir bir IP 'si kullanabilmeniz için Azure sanal ve şirket içinde barındırılan IR 'yi Azure VM 'de tutmak isteyeceksiniz. 
- Bu mimari kullanılarak hem ilk anlık görüntü veri geçişi hem de Delta verileri geçişi elde edilebilir. 

## <a name="implementation-best-practices"></a>En iyi uygulama uygulamaları 

### <a name="authentication-and-credential-management"></a>Kimlik doğrulama ve kimlik bilgisi yönetimi 

- Amazon S3 hesabının kimliğini doğrulamak için, [IAM hesabı için erişim anahtarını](./connector-amazon-simple-storage-service.md#linked-service-properties)kullanmanız gerekir. 
- Azure Blob depolamaya bağlanmak için birden çok kimlik doğrulama türü desteklenir.  [Azure kaynakları için yönetilen kimliklerin](./connector-azure-blob-storage.md#managed-identity) kullanımı kesinlikle önerilir: Azure AD 'de otomatik olarak YÖNETILEN bir ADF tanımının üzerine inşa edilen, bağlantılı hizmet tanımında kimlik bilgileri sağlamadan işlem hatlarını yapılandırmanıza olanak tanır.  Alternatif olarak, [hizmet sorumlusu](./connector-azure-blob-storage.md#service-principal-authentication), [paylaşılan erişim imzası](./connector-azure-blob-storage.md#shared-access-signature-authentication)veya [depolama hesabı anahtarı](./connector-azure-blob-storage.md#account-key-authentication)kullanarak Azure Blob depolama alanında kimlik doğrulaması yapabilirsiniz. 
- Azure Data Lake Storage 2. bağlanmak için birden çok kimlik doğrulama türü de desteklenir.  [Hizmet sorumlusu](./connector-azure-data-lake-storage.md#service-principal-authentication) veya [depolama hesabı anahtarı](./connector-azure-data-lake-storage.md#account-key-authentication) da kullanılabilir olsa da, [Azure kaynakları için yönetilen kimliklerin](./connector-azure-data-lake-storage.md#managed-identity) kullanılması önemle önerilir. 
- Azure kaynakları için Yönetilen kimlikler kullanmıyorsanız, ADF bağlı hizmetleri değiştirmeden anahtarları merkezi olarak yönetmeyi ve döndürmeyi kolaylaştırmak için [Azure Key Vault kimlik bilgilerinin depolanması](./store-credentials-in-key-vault.md) kesinlikle önerilir.  Bu Ayrıca, [CI/CD için en iyi uygulamalardan](./continuous-integration-deployment.md#best-practices-for-cicd)biridir. 

### <a name="initial-snapshot-data-migration"></a>İlk anlık görüntü verilerini geçirme 

Veri bölümü, özellikle 100 TB 'den fazla veri geçirirken önerilir.  Verileri bölümlemek için, Amazon S3 adlı klasörleri ve dosyaları ada göre filtrelemek için ' önek ' ayarından yararlanın ve sonra ADF kopyalama işi her seferinde bir bölüm kopyalayabilir.  Daha iyi üretilen iş için birden çok ADF kopyalama işini eşzamanlı olarak çalıştırabilirsiniz. 

Ağ veya veri deposu geçici sorunu nedeniyle kopyalama işlerinin herhangi biri başarısız olursa, ilgili bölümü AWS S3 ' dan yeniden yüklemek için başarısız kopyalama işini yeniden çalıştırabilirsiniz.  Diğer bölümleri yükleyen diğer tüm kopyalama işleri etkilenmeyecektir. 

### <a name="delta-data-migration"></a>Delta verileri geçişi 

AWS S3 ' deki yeni veya değiştirilmiş dosyaları tanımlamanın en iyi performansı, zaman bölümlenmiş adlandırma kuralını kullanmaktır. AWS S3 içindeki verileriniz, dosya veya klasör adındaki saat dilimi bilgileriyle bölümleniyorsa (örneğin,/yyyy/mm/dd/file.csv), işlem hatlarınız, artımlı olarak hangi dosya/klasörlerin kopyalanacağını kolayca tanımlayabilir. 

Alternatif olarak, AWS S3 içindeki verileriniz zaman bölümlenmiş değilse, ADF, LastModifiedDate göre yeni veya değiştirilmiş dosyaları tanımlayabilir.   Bu şekilde, ADF 'nin AWS S3 ' dan tüm dosyaları taraması ve yalnızca son değiştirilme zaman damgası belirli bir değerden daha büyük olan yeni ve güncelleştirilmiş dosyayı kopyalaması gerekir.  S3 içinde çok sayıda dosyanız varsa ilk dosya taramanın, filtre koşuluyla kaç dosyanın eşleşip eşleşmediğine bakılmaksızın uzun zaman sürediğine dikkat edin.  Bu durumda, dosya taramanın paralel olarak gerçekleşebilmesi için ilk anlık görüntü geçişi için aynı ' ön ek ' ayarını kullanarak önce verileri bölümleyerek yapmanız önerilir.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Azure VM 'de şirket içinde barındırılan tümleştirme çalışma zamanı gerektiren senaryolar için 

Verileri özel bağlantı üzerinden mi geçirirken yoksa Amazon S3 güvenlik duvarında belirli IP aralığına izin vermek istiyorsanız, Azure Windows VM 'ye şirket içinde barındırılan tümleştirme çalışma zamanı 'nı yüklemeniz gerekir. 

- Her bir Azure sanal makinesi için ile başlamak için önerilen yapılandırma, 32 vCPU ve 128 GB bellekle Standard_D32s_v3.  Veri geçişi sırasında IR VM 'nin izleme CPU ve bellek kullanımını, daha iyi performans için sanal makineyi daha fazla ölçeklendirmeniz gerektiğini ve maliyeti kazanmak için VM 'yi ölçeklendirmeniz gerektiğini görmek için kullanabilirsiniz. 
- Ayrıca, tek bir şirket içinde barındırılan IR ile 4 VM düğümünü ilişkilendirerek da ölçeği genişletebilirsiniz.  Şirket içinde barındırılan bir IR 'ye karşı çalışan tek bir kopyalama işi otomatik olarak dosya kümesini bölümleyip, dosyaları paralel olarak kopyalamak için tüm VM düğümlerinden faydalanır.  Yüksek kullanılabilirlik için, veri geçişi sırasında tek hata noktasını önlemek üzere 2 VM düğümü ile başlamanız önerilir. 

### <a name="rate-limiting"></a>Hız sınırlaması 

En iyi uygulama olarak, uygun bölüm boyutunu belirleyebilmeniz için temsili örnek veri kümesiyle bir performans POC 'si gerçekleştirin. 

Varsayılan DIU ayarıyla tek bir bölüm ve tek bir kopyalama etkinliği ile başlayın.  Ağınızın veya veri depolarının ıOPS/bant genişliği limitinin bant genişliği sınırına ulaşana kadar DIU ayarını kademeli olarak artırın veya tek bir kopyalama etkinliğinde izin verilen en fazla 256 DIU 'ya ulaştınız. 

Daha sonra, ortamınızın sınırlarına ulaşana kadar eşzamanlı kopyalama etkinliği sayısını kademeli olarak artırın. 

ADF kopyalama etkinliği tarafından bildirilen azaltma hatalarıyla karşılaşırsanız, ADF 'deki eşzamanlılık veya DIU ayarını azaltın ya da ağ ve veri depolarının bant genişliği/ıOPS sınırlarını artırmayı deneyin.  

### <a name="estimating-price"></a>Fiyat tahmini 

> [!NOTE]
> Bu bir kuramsal fiyatlandırma örneğidir.  Gerçek fiyatlandırağınız, ortamınızdaki gerçek işleme göre değişir.

S3 'ten Azure Blob Storage 'a veri geçirmek için oluşturulan aşağıdaki işlem hattını göz önünde bulundurun: 

![Diyagram, verileri geçirmek için bir işlem hattı gösterir ve bu arama için el ile tetikleyici akışı, saklı yordama kopyalama akışını içeren her bölüm için bir alt işlem hattına akar. Işlem hattının dışında, saklı yordam, arama yapılacak Azure SQL D B 'ye akar, bu da blob depolamaya akan, kopyalamak üzere bir W S S3 akışı olur.](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Şunları kabul edelim: 

- Toplam veri hacmi 2 PB 
- İlk çözüm mimarisini kullanarak HTTPS üzerinden veri geçirme 
- 2 PB 1 K bölüme bölünmüştür ve her kopya bir bölüm taşıyor 
- Her kopya DIU = 256 ile yapılandırılır ve 1 GBps aktarım hızına erişir 
- ForEach eşzamanlılık 2 olarak ayarlanır ve toplam verimlilik 2 GBps 'dir 
- Toplam olarak, geçişin tamamlanabilmesi için 292 saat sürer 

Yukarıdaki varsayımlar temelinde tahmini fiyat aşağıda verilmiştir: 

![Bir tablonun ekran görüntüsü tahmini bir fiyat gösterir.](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Ek başvurular 
- [Amazon basit depolama hizmeti Bağlayıcısı](./connector-amazon-simple-storage-service.md)
- [Azure Blob depolama Bağlayıcısı](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage 2. Nesil bağlayıcısı](./connector-azure-data-lake-storage.md)
- [Kopyalama etkinliği performans ayarlama Kılavuzu](./copy-activity-performance.md)
- [Şirket içinde barındırılan Integration Runtime oluşturma ve yapılandırma](./create-self-hosted-integration-runtime.md)
- [Şirket içinde barındırılan tümleştirme çalışma zamanı HA ve ölçeklenebilirliği](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Veri taşırken güvenlikle ilgili dikkat edilmesi gerekenler](./data-movement-security-considerations.md)
- [Kimlik bilgilerini Azure Key Vault’ta depolama](./store-credentials-in-key-vault.md)
- [Dosyayı, bölümlenmiş dosya adına göre artımlı olarak Kopyala](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [LastModifiedDate göre yeni ve değiştirilmiş dosyaları Kopyala](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [ADF fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Şablon

İşte, Amazon S3 ' den Azure Data Lake Storage 2. ' den yüz milyonlarca dosyadan oluşan verileri petabaytlarca geçirmek için ile başlayan [şablon](solution-template-migration-s3-azure.md) aşağıda verilmiştir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory ile birden çok kapsayıcıdan dosya kopyalama](solution-template-copy-files-multiple-containers.md)