---
title: Azure blok zinciri çalışma ekranı önizleme mimarisi
description: Azure blok zinciri çalışma ekranı önizleme mimarisine ve bileşenlerine genel bakış.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 4613d441fd0d363654073d4832de19139a7781e7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579734"
---
# <a name="azure-blockchain-workbench-preview-architecture"></a>Azure blok zinciri çalışma ekranı önizleme mimarisi

Azure blok zinciri çalışma ekranı önizlemesi, çeşitli Azure bileşenlerini kullanarak bir çözüm sunarak blok zinciri uygulama geliştirmeyi basitleştirir. Blok zinciri çalışma ekranı, Azure Marketi 'ndeki bir çözüm şablonu kullanılarak dağıtılabilir. Şablon, dağıtım için blok zinciri yığını, istemci uygulaması türü ve IoT tümleştirmesi desteği dahil olmak üzere modülleri ve bileşenleri seçmenizi sağlar. Bir kez dağıtıldıktan sonra, blok zinciri çalışma ekranı bir Web uygulamasına, iOS uygulamasına ve Android uygulamasına erişim sağlar.

![Blok zinciri çalışma ekranı mimarisi](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Kimlik ve kimlik doğrulama

Blok zinciri çalışma ekranı kullanarak, bir konsorsiyum, Azure Active Directory (Azure AD) kullanarak kurumsal kimliklerini federasyona edebilir. Çalışma ekranı, Azure AD 'de depolanan kurumsal kimliklere sahip şirket içi kimlikler için yeni kullanıcı hesapları oluşturur. Kimlik eşleme, istemci API 'Lerinde ve uygulamalarında kimliği doğrulanmış oturum açmayı kolaylaştırır ve kuruluşların kimlik doğrulama ilkelerini kullanır. Çalışma ekranı ayrıca kurumsal kimlikleri belirli bir akıllı sözleşmede belirli rollerle ilişkilendirebilme olanağı sağlar. Ayrıca, çalışma zamanı bu rollerin gerçekleştirebileceği eylemleri ve ne zaman yapılacağını belirlemek için de bir mekanizma sağlar.

Blockzincirle çalışma ekranı dağıtıldıktan sonra, kullanıcılar, istemci uygulamaları, REST tabanlı istemci API 'SI veya mesajlaşma API 'SI aracılığıyla blok zinciri çalışma ekranı ile etkileşime geçebilir. Her durumda, Azure Active Directory (Azure AD) ya da cihaza özgü kimlik bilgileri aracılığıyla etkileşimlerin doğrulanması gerekir.

Kullanıcılar, e-posta adresinde katılımcılara bir e-posta daveti göndererek kimliklerini bir konsorsiyum Azure AD 'ye devredebilir. Oturum açarken, bu kullanıcıların kimliği, parola ve ilkeleri kullanılarak doğrulanır. Örneğin, kuruluşunun iki öğeli kimlik doğrulaması.

Azure AD, blok zinciri çalışma ekranına erişimi olan tüm kullanıcıları yönetmek için kullanılır. Akıllı bir sözleşmeye bağlanan her cihaz, Azure AD ile de ilişkilendirilir.

Ayrıca, Azure AD, kullanıcıları özel bir yönetici grubuna atamak için de kullanılır. Yönetici grubuyla ilişkili kullanıcılara, sözleşmeleri dağıtma ve bir kullanıcıya bir sözleşmeye erişim izni verme dahil olmak üzere blok zinciri çalışma ekranı içindeki haklar ve eylemlere erişim izni verilir. Bu grubun dışındaki kullanıcıların, yönetici eylemlerine erişimi yoktur.

## <a name="client-applications"></a>İstemci uygulamaları

Çalışma ekranı, Web ve mobil için otomatik olarak oluşturulan istemci uygulamaları (iOS, Android) sağlar, bu da blok zinciri uygulamalarını doğrulamak, test etmek ve görüntülemek için kullanılabilir. Uygulama arabirimi akıllı anlaşma meta verileri temelinde dinamik olarak oluşturulur ve herhangi bir kullanım örneğine uyum sağlayabilir. İstemci uygulamaları, blok zinciri çalışma ekranı tarafından oluşturulan tam blok zinciri uygulamalarına kullanıcıya yönelik bir ön uç sağlar. İstemci uygulamaları Azure Active Directory (Azure AD) aracılığıyla kullanıcıların kimliğini doğrular ve ardından akıllı sözleşmenin iş bağlamına uyarlanmış bir kullanıcı deneyimi sunar. Kullanıcı deneyimi yetkili kişiler tarafından yeni akıllı anlaşma örnekleri oluşturulmasına olanak tanır ve ardından akıllı sözleşmenin gösterdiği iş işleminde uygun noktalarda belirli işlem türlerini yürütme özelliğini sunar.

Web uygulamasında yetkili kullanıcılar Yönetici Konsolu erişebilir. Konsol, Azure AD 'de Yönetici grubundaki kullanıcılar tarafından kullanılabilir ve aşağıdaki işlevlere erişim sağlar:

* Popüler senaryolar için Microsoft tarafından sunulan akıllı sözleşmeleri dağıtın. Örneğin, bir varlık aktarım senaryosu.
* Kendi akıllı sözleşmelerini karşıya yükleyin ve dağıtın.
* Belirli bir rol bağlamında akıllı sözleşmeye Kullanıcı erişimi atayın.

Daha fazla bilgi için bkz. [GitHub 'Da Azure blok zinciri çalışma ekranı örnek istemci uygulamaları](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile).

## <a name="gateway-service-api"></a>Ağ geçidi hizmeti API 'SI

Blok zinciri çalışma ekranı, REST tabanlı bir ağ geçidi hizmeti API 'sini içerir. Bir blok zincirine yazarken, API bir olay aracısına ileti oluşturur ve gönderir. API tarafından veriler istendiğinde sorgular, zincir dışı SQL veritabanına gönderilir. SQL veritabanı, desteklenen akıllı sözleşmeler için bağlam ve yapılandırma bilgilerini sağlayan bir zincir veri ve meta veri çoğaltmasını içerir. Sorgular, yük dışı çoğaltmadaki gerekli verileri, sözleşmenin meta verileri tarafından bilgilendirilmiş bir biçimde döndürür.

Geliştiriciler, blok zinciri çalışma ekranı istemci uygulamalarına bağlı olmadan blok zinciri çözümlerini derlemek veya bütünleştirmek için ağ geçidi hizmeti API 'sine erişebilirler.

> [!NOTE]
> API 'ye kimliği doğrulanmış erişimi etkinleştirmek için, iki istemci uygulaması Azure Active Directory kaydedilir. Azure Active Directory her uygulama türü (yerel ve Web) için farklı uygulama kayıtları gerektirir. 

## <a name="message-broker-for-incoming-messages"></a>Gelen iletiler için ileti Aracısı

Doğrudan blok zinciri çalışma ekranına ileti göndermek isteyen geliştiriciler, doğrudan Service Bus ileti gönderebilir. Örneğin, iletiler API 'SI sistemden sisteme tümleştirme veya IoT cihazları için kullanılabilir.

## <a name="message-broker-for-downstream-consumers"></a>Aşağı akış müşterileri için ileti Aracısı

Uygulamanın yaşam döngüsü boyunca olaylar oluşur. Olaylar, ağ geçidi API 'SI veya genel muhasebe tarafından tetiklenebilir. Olay bildirimleri olay temelinde aşağı akış kodu başlatabilir.

Blok zinciri çalışma ekranı otomatik olarak iki tür olay tüketicileri dağıtır. Zincir dışı SQL Mağazası 'nı doldurmak için bir tüketici blok zinciri olayları tarafından tetiklenir. Diğer tüketici, belgelerin karşıya yüklenmesi ve depolanması ile ilgili API tarafından oluşturulan olaylar için meta verileri yakalar.

## <a name="message-consumers"></a>İleti tüketicileri

 İleti tüketicileri Service Bus iletileri alır. İleti tüketicileri için temel olay modeli ek hizmet ve sistem uzantılarına izin verir. Örneğin, CosmosDB 'yi doldurmak veya Azure Akış Analizi 'ni kullanarak iletileri değerlendirmek için destek ekleyebilirsiniz. Aşağıdaki bölümlerde, blok zinciri çalışma ekranına eklenen ileti tüketicileri açıklanır.

### <a name="distributed-ledger-consumer"></a>Dağıtılmış muhasebe tüketicisi

Dağıtılmış muhasebe teknolojisi (DLT) iletileri, blok zincirine yazılacak işlemler için meta verileri içerir. Tüketici, iletileri alır ve verileri bir işlem Oluşturucu, imzalayan ve yönlendiriciye iter.

### <a name="database-consumer"></a>Veritabanı tüketicisi

Veritabanı tüketicisi Service Bus iletileri alır ve verileri SQL veritabanı gibi eklenmiş bir veritabanına gönderir.

### <a name="storage-consumer"></a>Depolama tüketicisi

Depolama TÜKETİCİSİNDE Service Bus iletileri alır ve verileri ekli bir depolamaya gönderir. Örneğin, Azure depolama 'da karma belgeleri depolama.

## <a name="transaction-builder-and-signer"></a>İşlem Oluşturucu ve imzalayan

Gelen ileti aracısıdır bir iletinin blok zincirine yazılması gerekiyorsa, bu, DLT tüketicisi tarafından işlenir. DLT tüketicisi, istenen bir işlemin yürütülmesi için meta verileri içeren iletiyi alan ve sonra *işlem Oluşturucu ve imzalayanın*bilgilerini gönderen bir hizmettir. *İşlem Oluşturucu ve imzalayan* , verileri ve istenen blok zinciri hedefini temel alan bir blok zinciri işlemini ayrıştırır. Bağlandıktan sonra işlem imzalanır. Özel anahtarlar Azure Key Vault depolanır.

 Blok zinciri çalışma ekranı Key Vault uygun özel anahtarı alır ve işlemi Key Vault dışında imzalar. İmzalandıktan sonra işlem, işlem yönlendiricilerine ve defterlere gönderilir.

## <a name="transaction-routers-and-ledgers"></a>İşlem yönlendiricileri ve bağış cihazları

İşlem yönlendiricileri ve defterler, imzalı işlemleri alır ve bunları uygun blok zincirine yönlendirir. Şu anda blok zinciri çalışma ekranı, hedef blok zinciri olarak Ethereum 'u destekliyor.

## <a name="dlt-watcher"></a>DLT İzleyicisi

Dağıtılmış bir muhasebe teknolojisi (DLT) İzleyicisi blok zinciri çalışma ekranına eklenen blok zincirlerinde gerçekleşen olayları izler.
Olaylar, bireyler ve sistemlerle ilgili bilgileri yansıtır. Örneğin, yeni sözleşme örnekleri oluşturma, işlemlerin yürütülmesi ve durum değişiklikleri. Olaylar yakalanarak giden ileti aracısına gönderilir, bu nedenle aşağı akış tüketicileri tarafından tüketilebilir.

Örneğin, SQL tüketicisi olayları izler, bunları kullanır ve SQL veritabanını dahil edilen değerlerle doldurur. Kopyalama, zincir dışı bir depoda bir zincir veri çoğaltmasının yeniden kullanılmasını mümkün bir şekilde sunar.

## <a name="azure-sql-database"></a>Azure SQL veritabanı

Blok zinciri çalışma ekranına eklenen Azure SQL veritabanı, sözleşme tanımlarını, yapılandırma meta verilerini ve blok zincirinde depolanan verilerin SQL erişimli bir çoğaltmasını depolar. Bu veriler, veritabanına doğrudan erişerek kolayca sorgulanabilir, görselleştirilir veya analiz edilebilir. Geliştiriciler ve diğer kullanıcılar veritabanını raporlama, analiz veya diğer veri merkezli Tümleştirmeler için kullanabilir. Örneğin, kullanıcılar Power BI kullanarak işlem verilerini görselleştirebilir.

Bu kapalı depolama alanı, kurumsal kuruluşların bir blok zinciri defteri yerine SQL 'de verileri sorgulamasına olanak sağlar. Ayrıca, blok zinciri teknolojisi yığınlarından bağımsız bir standart şemayı standartlaştırarak zincir dışı depolama, raporların ve diğer yapıların projeler, senaryolar ve kuruluşlar genelinde yeniden kullanılmasını mümkün hale getirir.

## <a name="azure-storage"></a>Azure Storage

Azure depolama, sözleşmelerle ilişkili sözleşmeleri ve meta verileri depolamak için kullanılır.

Satın alma siparişlerinden ve konşimentoda, Haberler ve tıbbi imagery 'de kullanılan görüntülere, polis gövde kameralar ve önemli hareket resimleri gibi bir Continuum 'tan kaynaklanan videolar için, belgeler birçok blok zinciri merkezli senaryoda bir rol oynar. Belgeler blok zincirine doğrudan yerleştirmek için uygun değildir.

Blok zinciri çalışma ekranı, blok zinciri iş mantığı ile belge veya diğer medya içeriği ekleme yeteneğini destekler. Belge veya medya içeriğinin karması blok zincirinde saklanır ve gerçek belge veya medya içeriği Azure Storage 'da depolanır. İlişkili işlem bilgileri gelen ileti aracısına dağıtılır, bu paket çalışır, imzalanır ve blok zincirine yönlendirilir. Bu işlem, giden ileti Aracısı aracılığıyla paylaşılan olayları tetikler. SQL DB bu bilgileri kullanır ve daha sonra sorgulamak üzere VERITABANıNA gönderir. Aşağı akış sistemleri, uygun şekilde hareket etmek için bu olayları da kullanabilir.

## <a name="monitoring"></a>İzleme

Çalışma ekranı Application Insights ve Azure Izleyici kullanarak uygulama günlüğü sağlar. Application Insights, blok zinciri çalışma ekranındaki tüm günlüğe kaydedilen bilgileri depolamak için kullanılır ve hatalar, uyarılar ve başarılı işlemler içerir. Application Insights, geliştiriciler tarafından blok zinciri çalışma ekranı ile ilgili sorunları ayıklamak için kullanılabilir. 

Azure Izleyici, blok zinciri ağının durumu hakkında bilgi sağlar. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench’i dağıtma](../../blockchain-workbench/blockchain-workbench-deploy.md)