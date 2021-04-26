---
title: Paylaşılan erişim imzaları (SAS) ile verilere sınırlı erişim verme
titleSuffix: Azure Storage
description: Blob, kuyruk, tablo ve dosya dahil olmak üzere Azure depolama kaynaklarına erişim sağlamak için paylaşılan erişim imzaları (SAS) kullanma hakkında bilgi edinin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/28/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 51e73222233602491b0c8ed3835d032610c68e0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722795"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme

Paylaşılan erişim imzası (SAS) Depolama hesabınızdaki kaynaklara güvenli temsilci erişimi sağlar. SAS ile, bir istemcinin verilerinize nasıl erişe, üzerinde ayrıntılı denetim sahibi olursunuz. Örnek:
 
- İstemcinin erişebileceği kaynaklar.

- Bu kaynaklara sahip oldukları izinler.

- SAS 'ın geçerli olduğu süre.

## <a name="types-of-shared-access-signatures"></a>Paylaşılan erişim imzası türleri

Azure depolama, üç tür paylaşılan erişim imzasını destekler:

- Kullanıcı temsili SAS

- Hizmet SAS

- Hesap SAS

### <a name="user-delegation-sas"></a>Kullanıcı temsili SAS

Kullanıcı temsili SAS, Azure Active Directory (Azure AD) kimlik bilgileriyle ve SAS için belirtilen izinlerle korunmaktadır. Kullanıcı temsili SAS yalnızca BLOB depolama için geçerlidir.

Kullanıcı temsili SAS hakkında daha fazla bilgi için bkz. [Kullanıcı temsilcileri oluşturma SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

### <a name="service-sas"></a>Hizmet SAS

Hizmet SAS, depolama hesabı anahtarıyla güvenli hale getirilir. Hizmet SAS, Azure depolama hizmetlerinden yalnızca birindeki bir kaynağa erişim temsilcisi seçer: BLOB depolama, kuyruk depolama, tablo depolama veya Azure dosyaları.

Hizmet SAS hakkında daha fazla bilgi için bkz. [HIZMET SAS oluşturma (REST API)](/rest/api/storageservices/create-service-sas).

### <a name="account-sas"></a>Hesap SAS

Bir hesap SAS, depolama hesabı anahtarıyla güvenli hale getirilir. Hesap SAS ise bir veya daha fazla depolama hizmetindeki kaynaklara erişim atar. Bir hizmet veya Kullanıcı temsili SAS aracılığıyla kullanılabilen tüm işlemler, hesap SAS 'si aracılığıyla da kullanılabilir. 

Ayrıca aşağıdakilere erişim temsilciliğini de sağlayabilirsiniz:

- Hizmet düzeyi işlemleri (örneğin, **hizmet özelliklerini al/ayarla** ve **hizmet istatistikleri al** işlemleri). 

- Hizmet SAS ile izin verilmeyen okuma, yazma ve silme işlemleri. 

Hesap SAS 'si hakkında daha fazla bilgi için [bir hesap SAS (REST API) oluşturun](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Microsoft, Azure AD kimlik bilgilerini, daha kolay tehlikeye giren hesap anahtarını kullanmak yerine en iyi güvenlik uygulaması olarak mümkün olduğunca kullanmanızı önerir. Uygulama tasarımınız blob depolamaya erişim için paylaşılan erişim imzaları gerektirdiğinde, üstün güvenlik için mümkün olduğunda bir Kullanıcı temsili SAS oluşturmak için Azure AD kimlik bilgilerini kullanın. Daha fazla bilgi için bkz. [Azure Active Directory kullanarak bloblara ve kuyruklara erişim yetkisi verme](storage-auth-aad.md).

Paylaşılan erişim imzası aşağıdaki iki formdan birini alabilir:

- Geçici **SAS**. Geçici SAS oluşturduğunuzda başlangıç saati, süre sonu zamanı ve izinler SAS URI 'sinde belirtilir. Her tür SAS bir geçici SAS olabilir.

- **Depolanan erişim Ilkesiyle HIZMET SAS**. Depolanan erişim ilkesi, bir blob kapsayıcısı, tablo, kuyruk veya dosya paylaşma olabilecek bir kaynak kapsayıcısında tanımlanır. Depolanan erişim ilkesi, bir veya daha fazla hizmet paylaşılan erişim imzalarının kısıtlamalarını yönetmek için kullanılabilir. Bir hizmet SAS 'sini depolanan bir erişim ilkesiyle ilişkilendirdiğinizde, SAS, &mdash; &mdash; depolama erişim ilkesi için tanımlanan başlangıç zamanı, süre sonu zamanı ve izinleri devralır.

> [!NOTE]
> Kullanıcı temsili SAS veya hesap SA 'ları bir geçici SAS olmalıdır. Depolanan erişim ilkeleri, Kullanıcı temsili SAS veya hesap SAS 'ları için desteklenmez.

## <a name="how-a-shared-access-signature-works"></a>Paylaşılan erişim imzasının nasıl çalıştığı

Paylaşılan erişim imzası, bir veya daha fazla depolama kaynağına işaret eden imzalı bir URI 'dir. URI, özel bir sorgu parametreleri kümesi içeren bir belirteç içerir. Belirteç, kaynaklara istemci tarafından nasıl erişildiğini gösterir. Sorgu parametrelerinden biri olan imza SAS parametrelerinden oluşturulur ve SAS oluşturmak için kullanılan anahtarla imzalanır. Bu imza, depolama kaynağına erişimi yetkilendirmek için Azure depolama tarafından kullanılır.

> [!NOTE]
> SAS belirteçlerinin oluşturulmasını denetlemek mümkün değildir. Bir SAS belirteci oluşturma ayrıcalıklarına sahip olan herhangi bir Kullanıcı, hesap anahtarını veya bir Azure RBAC rol ataması aracılığıyla bunu, depolama hesabının sahibi hakkında bilgi sahibi olmadan yapabilir. Kullanıcıların SAS belirteçleri oluşturmasına izin veren izinleri kısıtlamak konusunda dikkatli olun. Kullanıcıların, blob ve kuyruk iş yükleri için hesap anahtarıyla imzalanmış bir SAS oluşturmasını önlemek için, depolama hesabına paylaşılan anahtar erişimine izin vermeyebilirsiniz. Daha fazla bilgi için bkz. [paylaşılan anahtarla yetkilendirmeyi önleme](shared-key-authorization-prevent.md).

### <a name="sas-signature-and-authorization"></a>SAS imzası ve yetkilendirme

Bir SAS belirtecini Kullanıcı temsili anahtarıyla veya depolama hesabı anahtarı (paylaşılan anahtar) ile imzalayabilirsiniz. 

#### <a name="signing-a-sas-token-with-a-user-delegation-key"></a>Bir Kullanıcı temsili anahtarıyla SAS belirtecini imzalama

Bir SAS belirtecini, Azure Active Directory (Azure AD) kimlik bilgileri kullanılarak oluşturulan bir *Kullanıcı temsili anahtarı* kullanarak imzalayabilirsiniz. Kullanıcı temsili SAS, Kullanıcı temsili anahtarıyla imzalanır.

Anahtarı almak ve daha sonra SAS oluşturmak için, bir Azure AD güvenlik sorumlusu, eylemi içeren bir Azure rolü atanmalıdır `Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey` . Daha fazla bilgi için bkz. [Kullanıcı TEMSILI SAS (REST API) oluşturma](/rest/api/storageservices/create-user-delegation-sas).

#### <a name="signing-a-sas-token-with-an-account-key"></a>Bir SAS belirtecini hesap anahtarıyla imzalama

Depolama hesabı anahtarıyla bir hizmet SAS ve hesap SAS 'si imzalanır. Hesap anahtarı ile imzalanmış bir SAS oluşturmak için, bir uygulamanın hesap anahtarına erişimi olması gerekir.

Bir istek bir SAS belirteci içerdiğinde, bu istek, SAS belirtecinin nasıl imzalandığını temel alarak yetkilendirilir. SAS belirteci oluşturmak için kullandığınız erişim anahtarı veya kimlik bilgileri, SAS sahibi olan bir istemciye erişim sağlamak için Azure depolama tarafından da kullanılır.

Aşağıdaki tabloda her bir SAS belirteci türünün nasıl yetkilendirildiği özetlenmektedir.

| SAS türü | Yetkilendirme türü |
|-|-|
| Kullanıcı temsili SAS (yalnızca BLOB depolama) | Azure AD |
| Hizmet SAS | Paylaşılan Anahtar |
| Hesap SAS | Paylaşılan Anahtar |

Microsoft, üstün güvenlik için mümkün olduğunda Kullanıcı temsili SAS kullanılmasını önerir.

### <a name="sas-token"></a>SAS belirteci

SAS belirteci, örneğin Azure depolama istemci kitaplıklarından birini kullanarak istemci tarafında oluşturduğunuz bir dizedir. SAS belirteci, Azure depolama tarafından herhangi bir şekilde izlenmez. İstemci tarafında sınırsız sayıda SAS belirteci oluşturabilirsiniz. SAS oluşturduktan sonra, Depolama hesabınızdaki kaynaklara erişmesi gereken istemci uygulamalarına dağıtabilirsiniz.

İstemci uygulamaları, bir isteğin parçası olarak Azure depolama 'ya SAS URI 'SI sağlar. Daha sonra hizmet, geçerli olduğunu doğrulamak için SAS parametrelerini ve imzasını denetler. Hizmet imzanın geçerli olduğunu doğruladığında, istek yetkilendirilir. Aksi takdirde, istek 403 (yasak) hata koduyla reddedilir.

Kaynak URI ve SAS belirtecini gösteren bir Service SAS URI 'SI örneği aşağıda verilmiştir. SAS belirteci URI sorgu dizesini içerdiğinden, kaynak URI 'sinin ardından önce bir soru işareti gelmelidir, sonra SAS belirtecine göre:

![Bir hizmet SAS URI 'SI bileşenleri](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Paylaşılan erişim imzası ne zaman kullanılır?

Depolama hesabınızdaki kaynaklara güvenli erişim sağlamak için bir SAS kullanın, aksi takdirde bu kaynaklar için izinleri olmayan herhangi bir istemciye.

SAS 'ın yararlı olduğu yaygın bir senaryo, kullanıcıların depolama hesabınıza kendi verilerini okuacağı ve yazdıkları bir hizmettir. Depolama hesabının kullanıcı verilerini depoladığı bir senaryoda iki tipik tasarım deseni vardır:

1. İstemciler, kimlik doğrulamasını yapan ön uç ara sunucu hizmeti üzerinden verileri karşıya yükleyip indirirler. Bu ön uç proxy hizmeti, iş kurallarının doğrulanmasına izin verir. Ancak büyük miktarlarda veri veya yüksek hacimli işlemler için, talebe uyacak şekilde ölçeklenebilen bir hizmet oluşturmak pahalı veya zor olabilir.

   ![Senaryo diyagramı: ön uç proxy hizmeti](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

2. Basit bir hizmet gerektiğinde istemcinin kimliğini doğrular ve ardından bir SAS oluşturur. İstemci uygulaması SAS aldıktan sonra, depolama hesabı kaynaklarına doğrudan erişebilir. Erişim izinleri SAS tarafından ve SAS tarafından izin verilen aralığa göre tanımlanır. SAS tüm verileri ön uç ara sunucu hizmetiyle yönlendirme gereksinimini azaltır.

   ![Senaryo diyagramı: SAS sağlayıcı hizmeti](./media/storage-sas-overview/sas-storage-provider-service.png)

Birçok gerçek dünya hizmeti, bu iki yaklaşımdan oluşan karma bir karma kullanabilir. Örneğin, bazı veriler ön uç proxy 'si aracılığıyla işlenebilir ve doğrulanabilir. Diğer veriler, SAS kullanılarak kaydedilir ve/veya doğrudan okunurdur.

Ayrıca, belirli senaryolarda bir kopyalama işleminde kaynak nesnesine erişim yetkisi vermek için SAS gerekir:

- Bir blobu farklı bir depolama hesabında bulunan başka bir bloba kopyaladığınızda. 
  
  İsteğe bağlı olarak, hedef Blobun erişimi yetkilendirmek için bir SAS kullanabilirsiniz.

- Farklı bir depolama hesabında bulunan başka bir dosyaya bir dosya kopyaladığınızda. 

  İsteğe bağlı olarak, hedef dosyaya erişim yetkisi vermek için bir SAS kullanabilirsiniz.

- Bir blobu bir dosyaya veya bir blob dosyasına kopyaladığınızda. 

  Kaynak ve hedef nesneler aynı depolama hesabı içinde bulunsa bile SAS kullanmanız gerekir.

## <a name="best-practices-when-using-sas"></a>SAS kullanırken en iyi uygulamalar

Uygulamalarınızda paylaşılan erişim imzaları kullandığınızda, iki olası riski bilmeniz gerekir:

- SAS sızsa, onu alan herkes tarafından kullanılabilir ve bu da depolama hesabınızı tehlikeye atabilir.

- Bir istemci uygulaması için belirtilen bir SAS süresinin dolmasını ve uygulamanın hizmetinizden yeni bir SAS alamadıysa, uygulamanın işlevselliği hinalınmayabilir.

Paylaşılan erişim imzalarını kullanmaya yönelik aşağıdaki öneriler, bu riskleri azaltmaya yardımcı olabilir:

- **Her zaman** bir SAS oluşturmak veya DAĞıTMAK için HTTPS kullanın. Bir SAS HTTP üzerinden geçirilmezse ve ele geçirilirse, bir saldırgan orta noktadan adam saldırısı gerçekleştirerek SAS 'yi okuyabilir. Daha sonra, söz konusu SAS 'yi amaçlanan kullanıcının sahip olduğu gibi kullanabilirler. Bu, hassas verilere zarar verebilir veya kötü amaçlı kullanıcı tarafından verilerin bozulmasına izin verebilir.

- **Mümkün olduğunda bir Kullanıcı temsili SAS kullanın.** Bir Kullanıcı temsili SAS, bir hizmet SAS veya hesap SAS 'si için üstün güvenlik sağlar. Hesap anahtarınızı kodunuzla depolamanız gerekmiyorsa, bir Kullanıcı temsili SAS, Azure AD kimlik bilgileriyle korunmaktadır.

- **SAS için bir iptal planı vardır.** SAS tehlikede olursa yanıt vermeye hazır olduğunuzdan emin olun.

- **Hizmet SAS için depolanan bir erişim ilkesi tanımlayın.** Saklı erişim ilkeleri, depolama hesabı anahtarlarını yeniden oluşturmanız gerekmeden bir hizmet SAS için izinleri iptal etme seçeneği sunar. Bu süre sonu süresini ileride (veya sonsuz) olarak ayarlayın ve gelecekte bir süre içinde ilerlemek için düzenli olarak güncelleştirildiğinden emin olun.

- **Geçici SAS hizmeti SAS veya hesap SAS 'si üzerinde neredeyse vadeli süre sonu zamanlarını kullanın.** Bu şekilde, SAS tehlikede olsa bile, yalnızca kısa bir süre için geçerlidir. Bu uygulama, saklı erişim ilkesine başvurmamak için özellikle önemlidir. Yakın dönem süre sonu süreleri Ayrıca, kendisine karşıya yükleme için kullanılabilir süreyi sınırlayarak bir bloba yazılabilen veri miktarını da sınırlar.

- **Gerektiğinde istemcilerin SAS 'yi otomatik olarak yenilemesini sağlayabilirsiniz.** SAS sağlayan hizmet kullanılamıyorsa istemciler, yeniden denemeler için zaman tanımak üzere süre sonundan önce SAS 'yi yenilemelidir. Bu, bazı durumlarda gereksiz olabilir. Örneğin, SAS 'ın kısa bir süre boyunca kısa süreli işlemler için kullanılmasını isteyebilirsiniz. Bu işlemlerin, sona erme dönemi içinde tamamlanması beklenmektedir. Sonuç olarak, SAS 'nin yenilenmesini beklemeiyorduk. Ancak, SAS üzerinden düzenli olarak istek yapan bir istemciniz varsa, süre sonu olma olasılığı oynatma olur. 

- **SAS başlangıç zamanına dikkat edin.** Bir SAS için başlangıç saatini geçerli saate ayarlarsanız, ilk birkaç dakika boyunca zaman aralıklı olarak meydana gelebilir. Bunun nedeni farklı makinelerin geçerli süreleriyle (saat eğriltme olarak bilinir) kaynaklanmaktadır. Genel olarak, başlangıç saatini geçmişte en az 15 dakika olacak şekilde ayarlayın. Ya da, hiç ayarlama yapmayın, bu, tüm durumlarda hemen geçerli hale gelir. Aynı genellikle süre sonu zamanı için geçerlidir ve herhangi bir isteğe bağlı olarak, 15 dakikaya kadar saatin ölçeğini gözlemleyebilirsiniz. 2012-02-12 ' dan önceki REST sürümünü kullanan istemciler için, depolanan bir erişim ilkesine başvurmayan SAS için en uzun süre 1 saattir. 1 saatten daha uzun bir dönem belirten herhangi bir ilke başarısız olur.

- **SAS tarih saat biçimiyle dikkatli olun.** Bazı yardımcı programlar (AzCopy gibi) için, DateTime biçimlerinin ' +% Y-% d-% dT% H:%D:% SZ ' olması gerekir. Bu biçim, saniyeler içinde özel olarak dahildir. 

- **Erişilecek kaynağa özel olun.** En iyi güvenlik uygulaması, gerekli en düşük ayrıcalıklara sahip bir kullanıcı sağlamaktır. Bir kullanıcının yalnızca tek bir varlığa okuma erişimi olması gerekiyorsa, tüm varlıklara okuma/yazma/silme erişimi değil, onlara bu tek varlığa yönelik okuma erişimi izni verin. Bu, SAS 'nin bir saldırgan içinde daha az gücü olduğundan, SAS tehlikeye atılırsa hasar azaltır.

- **Hesabınızın bir SAS aracılığıyla da dahil olmak üzere herhangi bir kullanım için faturalandırılacağını anlayın.** Blob 'a yazma erişimi sağlarsanız, bir Kullanıcı bir 200 GB blobu karşıya yüklemeyi tercih edebilir. Bunlara yönelik okuma erişimi de aldıysanız, çıkış maliyetlerinde 2 TB 'a sahip olmak üzere 10 kez indirmeyi seçebilirler. Bu, kötü amaçlı kullanıcıların olası eylemlerini azaltmaya yardımcı olmak için sınırlı izinler sağlar. Bu tehdidi azaltmak için kısa süreli SAS kullanın (ancak bitiş saati üzerinde geçen saat çarpıklığı).

- **SAS kullanarak yazılan verileri doğrulayın.** Bir istemci uygulaması depolama hesabınıza veri yazdığında, bu verilerle ilgili sorunlar olabileceğini aklınızda bulundurun. Verileri doğrulamayı planlıyorsanız, veriler yazıldıktan sonra ve uygulamanız tarafından kullanılmadan önce Bu doğrulamayı gerçekleştirin. Bu yöntem ayrıca, ister bir kullanıcı tarafından, hem de bir veya sızdırılan SAS kullanan bir kullanıcı tarafından hesabınıza yazılan bozuk veya kötü amaçlı verilere karşı koruma sağlar.

- **SAS ne zaman kullanacağınızı öğrenin.** Bazen, depolama hesabınıza yönelik belirli bir işlemle ilişkili riskler, SAS kullanmanın avantajlarından yararlanır. Bu gibi işlemler için, iş kuralı doğrulama, kimlik doğrulama ve denetim gerçekleştirdikten sonra depolama hesabınıza yazan bir orta katman hizmeti oluşturun. Ayrıca, bazen erişimi başka yollarla yönetmek daha basittir. Örneğin, bir kapsayıcıdaki tüm Blobları herkese açık bir şekilde okunabilir hale getirmek istiyorsanız, her istemciye erişim için bir SAS sağlamak yerine kapsayıcıyı ortak hale getirebilirsiniz.

- **Uygulamanızı izlemek için Azure Izleyici ve Azure depolama günlüklerini kullanın.** SAS sağlayıcısı hizmetinizdeki bir kesinti nedeniyle yetkilendirme hatalarının oluşması olabilir. Ayrıca, depolanan bir erişim ilkesinin yanlışlıkla kaldırılmasından de oluşabilir. Bu yetkilendirme hatalarının türlerindeki herhangi bir ani artış gözlemlemek için Azure Izleyici ve depolama Analizi günlüğünü de kullanabilirsiniz. Daha fazla bilgi için bkz. Azure [izleyici 'de Azure depolama ölçümleri](../blobs/monitor-blob-storage.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) ve [günlüğe kaydetme Azure depolama Analizi](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Depolama hesabı için oluşturulmuş paylaşılan erişim imzaları sayısını izlemez ve hiçbir API bu ayrıntıyı sağlayamaz. Bir depolama hesabı için oluşturulan paylaşılan erişim imzalarının sayısını bilmeniz gerekirse, numarayı el ile izlemeniz gerekir.

## <a name="get-started-with-sas"></a>SAS ile çalışmaya başlama

Paylaşılan erişim imzalarını kullanmaya başlamak için, her SAS türü için aşağıdaki makalelere bakın.

### <a name="user-delegation-sas"></a>Kullanıcı temsili SAS

- [PowerShell ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturma](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Azure CLı ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturma](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [.NET ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturma](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Hizmet SAS

- [.NET ile bir kapsayıcı veya blob için hizmet SAS oluşturma](../blobs/sas-service-create.md)

### <a name="account-sas"></a>Hesap SAS

- [.NET ile hesap SAS oluşturma](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Paylaşılan erişim imzasıyla erişim temsilcisi (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Kullanıcı temsilciliğini oluşturma SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Hizmet SAS oluşturma (REST API)](/rest/api/storageservices/create-service-sas)
- [Hesap SAS (REST API) oluşturma](/rest/api/storageservices/create-account-sas)
