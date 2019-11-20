---
title: Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme
description: Blob, kuyruk, tablo ve dosya dahil olmak üzere Azure depolama kaynaklarına erişim sağlamak için paylaşılan erişim imzaları (SAS) kullanma hakkında bilgi edinin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9623152bdea5cc56e6b9bcb7d9911a730fd7a4a4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72382005"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme

Paylaşılan erişim imzası (SAS), verilerinizin güvenliğine ödün vermeden Depolama hesabınızdaki kaynaklara güvenli yetki verilen erişim sağlar. SAS ile, bir istemcinin verilerinize nasıl erişe, üzerinde ayrıntılı denetim sahibi olursunuz. İstemcinin erişebileceği kaynakları, bu kaynaklarda sahip oldukları izinleri ve SAS 'ın ne kadar süreyle geçerli olduğunu (diğer parametreler arasında) denetleyebilirsiniz.

## <a name="types-of-shared-access-signatures"></a>Paylaşılan erişim imzası türleri

Azure depolama, üç tür paylaşılan erişim imzasını destekler:

- **Kullanıcı temsili SAS (Önizleme).** Kullanıcı temsili SAS, Azure Active Directory (Azure AD) kimlik bilgileriyle ve SAS için belirtilen izinlerle korunmaktadır. Kullanıcı temsili SAS yalnızca BLOB depolama için geçerlidir.

    Kullanıcı temsili SAS hakkında daha fazla bilgi için bkz. [Kullanıcı temsilcileri oluşturma SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- **Hizmet SAS.** Hizmet SAS, depolama hesabı anahtarıyla güvenli hale getirilir. Hizmet SAS, Azure depolama hizmetlerinden yalnızca birindeki bir kaynağa erişim temsilcisi seçer: BLOB depolama, kuyruk depolama, tablo depolama veya Azure dosyaları. 

    Hizmet SAS hakkında daha fazla bilgi için bkz. [HIZMET SAS oluşturma (REST API)](/rest/api/storageservices/create-service-sas).

- **Hesap SAS.** Bir hesap SAS, depolama hesabı anahtarıyla güvenli hale getirilir. Hesap SAS ise bir veya daha fazla depolama hizmetindeki kaynaklara erişim atar. Bir hizmet veya Kullanıcı temsili SAS aracılığıyla kullanılabilen tüm işlemler, hesap SAS 'si aracılığıyla da kullanılabilir. Ayrıca, hesap SAS ile hizmet **Özellikleri al/ayarla** ve **hizmet istatistikleri işlemlerini al** gibi hizmet düzeyinde uygulanan işlemlere erişim yetkisi verebilirsiniz. Bununla birlikte hizmet SAS ile izin verilmeyen blob kapsayıcılar, tablolar kuyruklar ve dosya paylaşımları üzerinde okuma, yazma ve silme işlemleri için yetkilendirme yapabilirsiniz. 

    Hesap SAS 'si hakkında daha fazla bilgi için [bir hesap SAS (REST API) oluşturun](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Microsoft, Azure AD kimlik bilgilerini, daha kolay tehlikeye giren hesap anahtarını kullanmak yerine en iyi güvenlik uygulaması olarak mümkün olduğunca kullanmanızı önerir. Uygulama tasarımınız blob depolamaya erişim için paylaşılan erişim imzaları gerektirdiğinde, üstün güvenlik için mümkün olduğunda bir Kullanıcı temsili SAS oluşturmak için Azure AD kimlik bilgilerini kullanın.

Paylaşılan erişim imzası, iki formdan birini alabilir:

- Geçici **SAS:** Bir geçici SAS oluşturduğunuzda, sa 'lar için başlangıç zamanı, süre sonu zamanı ve izin izinleri SAS URI 'sinde belirtilmiştir (veya başlangıç saati atlanırsa ima edilir). Her tür SAS bir geçici SAS olabilir.
- **Depolanan erişim Ilkesiyle HIZMET SAS:** Depolanan erişim ilkesi, bir blob kapsayıcısı, tablo, kuyruk veya dosya paylaşma olabilecek bir kaynak kapsayıcısında tanımlanır. Depolanan erişim ilkesi, bir veya daha fazla hizmet paylaşılan erişim imzalarının kısıtlamalarını yönetmek için kullanılabilir. Bir hizmet SAS 'sini depolanan bir erişim ilkesiyle ilişkilendirdiğinizde SAS, saklı erişim ilkesi için tanımlanan @ no__t-0başlangıç saati, süre sonu zamanı ve izinleri @ no__t-1olan kısıtlamaları devralır.

> [!NOTE]
> Kullanıcı temsili SAS veya hesap SA 'ları bir geçici SAS olmalıdır. Depolanan erişim ilkeleri, Kullanıcı temsili SAS veya hesap SAS 'ları için desteklenmez.

## <a name="how-a-shared-access-signature-works"></a>Paylaşılan erişim imzasının nasıl çalıştığı

Paylaşılan erişim imzası, bir veya daha fazla depolama kaynağını işaret eden ve özel bir sorgu parametreleri kümesi içeren bir belirteç içeren imzalı bir URI 'dir. Belirteç, kaynaklara istemci tarafından nasıl erişildiğini gösterir. Sorgu parametrelerinden biri olan imza SAS parametrelerinden oluşturulur ve SAS oluşturmak için kullanılan anahtarla imzalanır. Bu imza, depolama kaynağına erişimi yetkilendirmek için Azure depolama tarafından kullanılır.

### <a name="sas-signature"></a>SAS imzası

SAS 'yi iki şekilde imzalayabilirsiniz:

- Azure Active Directory (Azure AD) kimlik bilgileri kullanılarak oluşturulan bir *Kullanıcı temsili anahtarıyla* . Kullanıcı temsili SAS, Kullanıcı temsili anahtarıyla imzalanır.

    Kullanıcı temsilci anahtarını almak ve SAS oluşturmak için, Azure AD güvenlik sorumlusuna **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** eylemini içeren rol tabanlı erişim denetımı (RBAC) rolü atanmalıdır. Kullanıcı temsilciliğini almak için izinleri olan RBAC rolleri hakkında ayrıntılı bilgi için bkz. [Kullanıcı temsilcileri oluşturma SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- Depolama hesabı anahtarıyla. Depolama hesabı anahtarıyla bir hizmet SAS ve hesap SAS 'si imzalanır. Hesap anahtarı ile imzalanmış bir SAS oluşturmak için, bir uygulamanın hesap anahtarına erişimi olması gerekir.

### <a name="sas-token"></a>SAS belirteci

SAS belirteci, örneğin Azure depolama istemci kitaplıklarından birini kullanarak istemci tarafında oluşturduğunuz bir dizedir. SAS belirteci, Azure depolama tarafından herhangi bir şekilde izlenmez. İstemci tarafında sınırsız sayıda SAS belirteci oluşturabilirsiniz. SAS oluşturduktan sonra, Depolama hesabınızdaki kaynaklara erişmesi gereken istemci uygulamalarına dağıtabilirsiniz.

Bir istemci uygulaması, bir isteğin parçası olarak Azure depolama 'ya SAS URI 'SI sağlıyorsa, hizmet, isteği yetkilendirmek için geçerli olduğunu doğrulamak üzere SAS parametrelerini ve imzasını denetler. Hizmet imzanın geçerli olduğunu doğruladığında, istek yetkilendirilir. Aksi takdirde, istek 403 (yasak) hata koduyla reddedilir.

Kaynak URI ve SAS belirtecini gösteren bir Service SAS URI 'SI örneği aşağıda verilmiştir:

![Bir hizmet SAS URI 'SI bileşenleri](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Paylaşılan erişim imzası ne zaman kullanılır?

Depolama hesabınızdaki kaynaklara güvenli erişim sağlamak istediğinizde bir SAS kullanın, aksi takdirde bu kaynaklar için izinleri olmayan herhangi bir istemciye.

SAS 'ın yararlı olduğu yaygın bir senaryo, kullanıcıların depolama hesabınıza kendi verilerini okuacağı ve yazdıkları bir hizmettir. Bir depolama hesabının kullanıcı verilerini depoladığı bir senaryoda, tipik iki tasarım deseni vardır:

1. İstemciler, kimlik doğrulaması gerçekleştiren bir ön uç proxy hizmeti aracılığıyla verileri karşıya yükleyip indirir. Bu ön uç proxy hizmeti, iş kurallarının doğrulanmasına izin vermenin avantajlarından yararlanır, ancak büyük miktarlarda veri veya yüksek hacimli işlemler için, talebe uyacak şekilde ölçeklenebilen bir hizmet oluşturmak pahalı veya zor olabilir.

   ![Senaryo diyagramı: ön uç proxy hizmeti](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Hafif bir hizmet gerektiğinde istemcinin kimliğini doğrular ve ardından bir SAS oluşturur. İstemci uygulaması SAS aldıktan sonra, SAS tarafından tanımlanan izinlerle ve SAS tarafından izin verilen aralığa göre depolama hesabı kaynaklarına doğrudan erişebilirler. SAS, ön uç proxy hizmeti aracılığıyla tüm verileri yönlendirme gereksinimini azaltır.

   ![Senaryo diyagramı: SAS sağlayıcı hizmeti](./media/storage-sas-overview/sas-storage-provider-service.png)

Birçok gerçek dünya hizmeti, bu iki yaklaşımdan oluşan karma bir karma kullanabilir. Örneğin, bazı veriler ön uç proxy 'si aracılığıyla işlenebilir ve doğrulanabilir, diğer veriler kaydedilir ve/veya doğrudan SAS kullanılarak okunabilir.

Ayrıca, belirli senaryolarda bir kopyalama işleminde kaynak nesnesine erişim yetkisi vermek için SAS gerekir:

- Bir blobu farklı bir depolama hesabında bulunan başka bir bloba kopyaladığınızda, kaynak Blobun erişimi yetkilendirmek için bir SAS kullanmanız gerekir. İsteğe bağlı olarak, hedef Blobun erişimi yetkilendirmek için bir SAS kullanabilirsiniz.
- Farklı bir depolama hesabında bulunan başka bir dosyaya bir dosya kopyaladığınızda, kaynak dosyaya erişim yetkisi vermek için bir SAS kullanmanız gerekir. İsteğe bağlı olarak, hedef dosyaya erişim yetkisi vermek için bir SAS kullanabilirsiniz.
- Bir Blobun bir dosyaya veya bir dosyaya bir blob 'u kopyaladığınızda, kaynak ve hedef nesneler aynı depolama hesabı içinde bulunsa bile, kaynak nesneye erişim yetkisi vermek için SAS kullanmanız gerekir.

## <a name="best-practices-when-using-sas"></a>SAS kullanırken en iyi uygulamalar

Uygulamalarınızda paylaşılan erişim imzaları kullandığınızda, iki olası riski bilmeniz gerekir:

- SAS sızsa, onu alan herkes tarafından kullanılabilir ve bu da depolama hesabınızı tehlikeye atabilir.
- Bir istemci uygulaması için belirtilen bir SAS süresinin dolmasını ve uygulamanın hizmetinizden yeni bir SAS alamadıysa, uygulamanın işlevselliği hinalınmayabilir.

Paylaşılan erişim imzalarını kullanmaya yönelik aşağıdaki öneriler, bu riskleri azaltmaya yardımcı olabilir:

- **Her zaman** bir SAS oluşturmak veya DAĞıTMAK için HTTPS kullanın. Bir SAS HTTP üzerinden geçirilirse ve ele geçirilirse, bir saldırgan bir saldırgan, SAS 'yi okuyabilir ve bunu amaçlanan kullanıcının sahip olduğu gibi kullanabilir ve duyarlı verilerin güvenliğini sağlayabilir veya kötü amaçlı kullanıcı tarafından verilerin bozulmasına izin verebilir.
- **Mümkün olduğunda bir Kullanıcı temsili SAS kullanın.** Bir Kullanıcı temsili SAS, bir hizmet SAS veya hesap SAS 'si için üstün güvenlik sağlar. Hesap anahtarınızı kodunuzla depolamanız gerekmiyorsa, bir Kullanıcı temsili SAS, Azure AD kimlik bilgileriyle korunmaktadır.
- **SAS için bir iptal planı vardır.** SAS tehlikede olursa yanıt vermeye hazır olduğunuzdan emin olun.
- **Hizmet SAS için depolanan bir erişim ilkesi tanımlayın.** Saklı erişim ilkeleri, depolama hesabı anahtarlarını yeniden oluşturmanız gerekmeden bir hizmet SAS için izinleri iptal etme seçeneği sunar. Bu süre sonu süresini ileride (veya sonsuz) olarak ayarlayın ve gelecekte bir süre içinde ilerlemek için düzenli olarak güncelleştirildiğinden emin olun.
- **Geçici SAS hizmeti SAS veya hesap SAS 'si üzerinde neredeyse vadeli süre sonu zamanlarını kullanın.** Bu şekilde, SAS tehlikede olsa bile, yalnızca kısa bir süre için geçerlidir. Bu uygulama, saklı erişim ilkesine başvurmamak için özellikle önemlidir. Yakın dönem süre sonu süreleri Ayrıca, kendisine karşıya yükleme için kullanılabilir süreyi sınırlayarak bir bloba yazılabilen veri miktarını da sınırlar.
- **Gerektiğinde istemcilerin SAS 'yi otomatik olarak yenilemesini sağlayabilirsiniz.** SAS sağlayan hizmet kullanılamıyorsa istemciler, yeniden denemeler için zaman tanımak üzere süre sonundan önce SAS 'yi yenilemelidir. SAS 'nizin, sona erme döneminde tamamlanması beklenen kısa süreli, kısa süreli işlemler için kullanılması gerekiyorsa, SAS 'nin yenilenmesi beklenmediği için bu işlem gereksizdir. Ancak, SAS üzerinden düzenli olarak istek yapan istemciniz varsa, süre sonu olma olasılığı oynatma olur. Temel olarak, istemcinin yenilemeyi yeterince erken istediğini güvence altına almak için (daha önce belirtildiği gibi), istemcinin yenileme gereksinimini yeterince erken (daha önce belirtildiği gibi) dengelenmesi gerekir (başarılı yenilemeden önce SAS süresinin dolmasını önlemek için).
- **SAS başlangıç zamanına dikkat edin.** Bir SAS için başlangıç saatini **Şimdi**olarak ayarlarsanız, saat farkı (geçerli zaman farklı makinelere göre farklılık gösteren farklılıklar) nedeniyle, hatalardan bazıları ilk birkaç dakika boyunca zaman zaman gözlenebilir. Genel olarak, başlangıç saatini geçmişte en az 15 dakika olacak şekilde ayarlayın. Ya da, hiç ayarlama yapmayın, bu, tüm durumlarda hemen geçerli hale gelir. Aynı genellikle süre sonu zamanı için geçerlidir ve herhangi bir isteğe bağlı olarak, 15 dakikaya kadar saatin ölçeğini gözlemleyebilirsiniz. 2012-02-12 ' dan önceki bir REST sürümünü kullanan istemciler için, depolanan bir erişim ilkesine başvurmayan SAS için en uzun süre 1 saattir ve daha uzun vadeyi belirten herhangi bir ilke başarısız olur.
- **Erişilecek kaynağa özel olun.** En iyi güvenlik uygulaması, gerekli en düşük ayrıcalıklara sahip bir kullanıcı sağlamaktır. Bir kullanıcının yalnızca tek bir varlığa okuma erişimi olması gerekiyorsa, tüm varlıklara okuma/yazma/silme erişimi değil, onlara bu tek varlığa yönelik okuma erişimi izni verin. Bu, SAS 'nin bir saldırgan içinde daha az gücü olduğundan, SAS tehlikeye atılırsa hasar azaltır.
- **Hesabınızın bir SAS aracılığıyla da dahil olmak üzere herhangi bir kullanım için faturalandırılacağını anlayın.** Blob 'a yazma erişimi sağlarsanız, bir Kullanıcı bir 200 GB blobu karşıya yüklemeyi tercih edebilir. Bunlara yönelik okuma erişimi de aldıysanız, çıkış maliyetlerinde 2 TB 'a sahip olmak üzere 10 kez indirmeyi seçebilirler. Bu, kötü amaçlı kullanıcıların olası eylemlerini azaltmaya yardımcı olmak için sınırlı izinler sağlar. Bu tehdidi azaltmak için kısa süreli SAS kullanın (ancak bitiş saati üzerinde geçen saat çarpıklığı).
- **SAS kullanarak yazılan verileri doğrulayın.** Bir istemci uygulaması depolama hesabınıza veri yazdığında, bu verilerle ilgili sorunlar olabileceğini aklınızda bulundurun. Uygulamanız, kullanıma hazırlanmadan önce verilerin doğrulanmasını veya yetkilendirilmesini gerektiriyorsa, veriler yazıldıktan sonra ve uygulamanız tarafından kullanılmadan önce Bu doğrulamayı gerçekleştirmelisiniz. Bu yöntem ayrıca, ister bir kullanıcı tarafından, hem de bir veya sızdırılan SAS kullanan bir kullanıcı tarafından hesabınıza yazılan bozuk veya kötü amaçlı verilere karşı koruma sağlar.
- **SAS ne zaman kullanacağınızı öğrenin.** Bazen, depolama hesabınıza yönelik belirli bir işlemle ilişkili riskler, SAS kullanmanın avantajlarından yararlanır. Bu gibi işlemler için, iş kuralı doğrulama, kimlik doğrulama ve denetim gerçekleştirdikten sonra depolama hesabınıza yazan bir orta katman hizmeti oluşturun. Ayrıca, bazen erişimi başka yollarla yönetmek daha basittir. Örneğin, bir kapsayıcıdaki tüm Blobları herkese açık bir şekilde okunabilir hale getirmek istiyorsanız, her istemciye erişim için bir SAS sağlamak yerine kapsayıcıyı ortak hale getirebilirsiniz.
- **Uygulamanızı izlemek için Azure Izleyici ve Azure depolama günlüklerini kullanın.** SAS sağlayıcısı hizmetinizdeki bir kesinti veya depolanan bir erişim ilkesinin yanlışlıkla kaldırılması nedeniyle, yetkilendirme hatalarında ani değişiklikleri gözlemlemek için Azure Izleyici ve depolama Analizi günlüğünü kullanabilirsiniz. Daha fazla bilgi için bkz. Azure [izleyici 'de Azure depolama ölçümleri](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ve [günlüğe kaydetme Azure depolama Analizi](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>SAS ile çalışmaya başlama

Paylaşılan erişim imzalarını kullanmaya başlamak için, her SAS türü için aşağıdaki makalelere bakın.

### <a name="user-delegation-sas"></a>Kullanıcı temsili SAS

- [PowerShell ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturma (Önizleme)](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Azure CLı (Önizleme) ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturma](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [.NET ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturma (Önizleme)](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Hizmet SAS

- [.NET ile bir kapsayıcı veya blob için hizmet SAS oluşturma](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Hesap SAS

- [.NET ile hesap SAS oluşturma](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Paylaşılan erişim imzasıyla erişim temsilcisi (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Kullanıcı temsilciliğini oluşturma SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Hizmet SAS oluşturma (REST API)](/rest/api/storageservices/create-service-sas)
- [Hesap SAS (REST API) oluşturma](/rest/api/storageservices/create-account-sas)
