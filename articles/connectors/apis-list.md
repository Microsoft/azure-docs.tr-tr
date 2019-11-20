---
title: Azure Logic Apps için Bağlayıcılar
description: Yerleşik, yönetilen, şirket içi, tümleştirme hesabı ve kurumsal bağlayıcılar dahil olmak üzere Azure Logic Apps bağlayıcılarıyla iş akışlarını otomatikleştirin
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 3d828e6e365864c2c23e1d9f178a556ff3c07515
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430189"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure Logic Apps için Bağlayıcılar

Bağlayıcılar; Azure Logic Apps’ten diğer uygulamalar, hizmetler, sistemler, protokoller ve platformlar arasındaki olaylara, verilere ve eylemlere hızlı erişilmesini sağlar. Mantıksal uygulamalarınızdaki bağlayıcıları kullanarak, oluşturduğunuz ve zaten sahip olduğunuz verilerle görevler gerçekleştirmek için bulut ve şirket içi uygulamalarınızın yeteneklerini genişletebilirsiniz.

Logic Apps [yüzlerce bağlayıcı](https://docs.microsoft.com/connectors)sunurken, bu makalede binlerce uygulama tarafından başarıyla kullanılan popüler ve daha yaygın kullanılan bağlayıcılar ve veri ve bilgi işleme için milyonlarca yürütme açıklanmaktadır. Bağlayıcıların tam listesini ve Tetikleyiciler, Eylemler ve sınırlar gibi her bağlayıcının başvuru bilgilerini bulmak için [Bağlayıcılar genel bakış](https://docs.microsoft.com/connectors)altındaki bağlayıcı başvuru sayfalarını gözden geçirin. Ayrıca [Tetikleyiciler ve eylemler](#triggers-actions), [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md)ve [Logic Apps fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/logic-apps/)hakkında daha fazla bilgi edinin. 

> [!NOTE]
> Bağlayıcısı olmayan bir hizmet veya API ile tümleştirme için, hizmeti doğrudan HTTP gibi bir protokol üzerinden çağırabilir veya [özel bir bağlayıcı](#custom)oluşturabilirsiniz.

Bağlayıcılar, yerleşik Tetikleyiciler ve eylemler olarak ya da yönetilen bağlayıcılar olarak kullanılabilir:

<a name="built-in"></a>

* [**Yerleşik**](#built-ins): Bu yerleşik Tetikleyiciler ve eylemler, Azure Logic Apps için "yerel" ve özel zamanlamalarda çalışan, diğer uç noktalarla iletişim kurma, isteklere alma ve yanıt verme ve Azure Işlevleri, Azure API Apps (Web Uygulamalar), kendi API 'lerinizi Azure API Management ile ve yayımlanan iç içe mantık uygulamalarıyla birlikte yönetilen ve yayımlanmış bir API 'niz olabilir. Mantıksal uygulamanızın iş akışını düzenlemenize ve denetlemenize ve ayrıca verilerle çalışmanıza yardımcı olan yerleşik eylemleri de kullanabilirsiniz.

  > [!NOTE]
  > Bir [tümleştirme hizmeti ortamı (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) içindeki Logic Apps, bir Azure sanal ağındaki kaynaklara doğrudan erişebilir.
  > ISE kullandığınızda, **temel** etiketini görüntüleyen yerleşik Tetikleyiciler ve Eylemler, Logic Apps ile aynı Ise içinde çalışır. Logic Apps, yerleşik Tetikleyiciler ve ıSE 'de çalışan yerleşik Eylemler, tüketim tabanlı fiyatlandırma planından farklı bir fiyatlandırma planı kullanır.
  >
  > Sesleri oluşturma hakkında daha fazla bilgi için bkz. [Azure Logic Apps Azure sanal ağlarına bağlanma](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). 
  > Fiyatlandırma hakkında daha fazla bilgi için bkz. [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md).

<a name="managed-connectors"></a>

* **Yönetilen bağlayıcılar**: Microsoft tarafından dağıtılan ve yönetilen bu bağlayıcılar, Office 365, Azure Blob depolama, SQL Server, Dynamics, Salesforce, SharePoint gibi bulut hizmetlerine, şirket içi sistemlere veya her ikisine de erişmek için Tetikleyiciler ve eylemler sağlar ve daha fazlası. Bazı bağlayıcılar özellikle işletmeden işletmeye (B2B) iletişim senaryolarını destekler ve mantıksal uygulamanıza bağlı bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) gerektirir. Belirli bağlayıcılar kullanılmadan önce, Azure Logic Apps tarafından yönetilen bağlantıları oluşturmanız gerekebilir. 

  Örneğin, Microsoft BizTalk Server kullanıyorsanız, mantıksal uygulamalarınız [BizTalk Server şirket içi bağlayıcıyı](#on-premises-connectors)kullanarak BizTalk Server bağlanabilir ve sizinle iletişim kurabilir. 
  Daha sonra [tümleştirme hesabı bağlayıcıları](#integration-account-connectors)' nı kullanarak mantıksal uygulamalarınızda BizTalk benzeri işlemleri genişletebilir veya gerçekleştirebilirsiniz.

  Bağlayıcılar, standart ya da kurumsal olarak sınıflandırılır. 
  [Kurumsal bağlayıcılar](#enterprise-connectors) , ek bir MALIYET için SAP, IBM MQ ve IBM 3270 gibi kurumsal sistemlere erişim sağlar. Bağlayıcının standart veya kurumsal olduğunu anlamak için, [Bağlayıcılar genel bakış](https://docs.microsoft.com/connectors)altındaki her bağlayıcının başvuru sayfasındaki Teknik ayrıntılara bakın. 

  Ayrıca, bazı bağlayıcılar birden çok kategori içerebilse de, bu kategorileri kullanarak bağlayıcılar tanımlayabilirsiniz. 
  Örneğin, SAP bir kurumsal bağlayıcı ve şirket içi bağlayıcıdır:

  |   |   |
  |---|---|
  | [**Yönetilen API bağlayıcıları**](#managed-api-connectors) | Azure Blob depolama, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online ve çok daha fazlası gibi hizmetleri kullanan mantıksal uygulamalar oluşturun. |
  | [**Şirket içi bağlayıcılar**](#on-premises-connectors) | Şirket [içi veri ağ geçidini][gateway-doc]yükleyip ayarladıktan sonra, bu bağlayıcılar mantıksal uygulamalarınızın SQL Server, SharePoint Server, Oracle DB, dosya paylaşımları ve diğerleri gibi şirket içi sistemlere erişmesine yardımcı olur. |
  | [**Tümleştirme hesabı bağlayıcıları**](#integration-account-connectors) | Bir tümleştirme hesabı oluştururken ve ödeme yaptığınızda, bu bağlayıcılar XML 'yi dönüştürür ve doğrular, düz dosyaları kodlayıp kodunu çözer ve AS2, EDIOLGU ve x12 protokolleriyle işletmeden işletmeye (B2B) iletileri işler. |
  |||

  > [!NOTE]
  > Bir [tümleştirme hizmeti ortamı (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) içindeki Logic Apps, bir Azure sanal ağındaki kaynaklara doğrudan erişebilir. ISE 'yi kullandığınızda, **Ise** etiketini görüntüleyen standart ve kurumsal bağlayıcılar, Logic Apps ile aynı çalışma alanında çalışır. ISE etiketini görüntülememe ve genel Logic Apps hizmetinde çalışan bağlayıcılar.
  >
  > Bir Azure sanal ağına bağlı şirket içi sistemler için, mantıksal uygulamalarınızın **Ise** etıketı, http eylemi veya [özel bağlayıcı](#custom)içeren bir bağlayıcıyı kullanarak bu sistemlere doğrudan erişebilmesi için o ağa o ağa ekleyin. ISE 'de çalışan Logic Apps ve bağlayıcılar, tüketim tabanlı fiyatlandırma planından farklı bir fiyatlandırma planı kullanır. 
  >
  > Sesleri oluşturma hakkında daha fazla bilgi için bkz. [Azure Logic Apps Azure sanal ağlarına bağlanma](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).
  > Fiyatlandırma hakkında daha fazla bilgi için bkz. [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md).

  Bağlayıcıların tam listesi ve bir Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan eylemler ve tetikleyiciler gibi her bağlayıcının başvuru bilgileri ve herhangi bir sınırlama için, [Bağlayıcılar genel bakış](/connectors/)altındaki tam listeyi bulabilirsiniz. Fiyatlandırma bilgileri için bkz. [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md)ve [Logic Apps fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/logic-apps/). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Yerleşik

Logic Apps, zamanlama tabanlı iş akışları oluşturabilmek, mantıksal uygulamalarınızın diğer uygulama ve hizmetlerle iletişim kurmasına, Logic Apps aracılığıyla iş akışını denetlemesine ve verileri yönetmesine veya yönetmenize yardımcı olmak için yerleşik Tetikleyiciler ve eylemler sağlar.

|   |   |   |   | 
|---|---|---|---| 
| [![API Icon @ no__t-2**zamanlaması**][recurrence-doc] | -Mantıksal uygulamanızı, **yineleme** tetikleyicisiyle temel ve karmaşık tekrarlarla değişen belirli bir zamanlamaya göre çalıştırın. <p>- **Gecikme** eylemini kullanarak mantıksal uygulamanızı belirtilen süre için duraklatın. <p>-Mantıksal uygulamanızı, belirtilen tarih ve saate kadar **ertele** tamamlanana kadar duraklatın. | [![API Icon @ no__t-2**http**][http-doc] | Http, http + Swagger ve HTTP + Web kancasının her iki tetikleyici ve eylemi ile HTTP üzerinden herhangi bir uç nokta ile iletişim kurun. | 
| [![API Icon @ no__t-2**isteği**][http-request-doc] | -Mantıksal uygulamanızı diğer uygulamalardan veya hizmetlerden çağrılabilir, Event Grid Kaynak olaylarında tetikleyin veya **istek** tetikleyicisiyle Azure Güvenlik Merkezi uyarılarına yönelik yanıtlara karşı tetikleyin. <p>- **Yanıt** eylemiyle bir uygulamaya veya hizmete yanıt gönderin. | [![API Icon @ no__t-2**Batch**][batch-doc] | - **Batch iletileri** tetikleyicisiyle toplu olarak ileti işleme. <p>- **Batch 'e Ileti gönder** eylemine sahip mevcut toplu iş tetikleyicilerine sahip Logic Apps 'i çağırın. | 
| [![API Icon @ no__t-2**Azure işlevleri**][azure-functions-doc] | Mantıksal uygulamalarınızdan özel kod parçacıkları (C# veya Node. js) çalıştıran Azure işlevlerini çağırın. | [![API Icon @ no__t-2**Azure API Management**][azure-api-management-doc] | Azure API Management ile yönettiğiniz ve yayımladığınız kendi API 'leriniz tarafından tanımlanan Tetikleyicileri ve eylemleri çağırın. | 
| [![API Icon @ no__t-2**Azure Uygulama Hizmetleri**][azure-app-services-doc] | Azure App Service barındırılan Azure API Apps veya Web Apps çağırın. Bu uygulamalar tarafından tanımlanan Tetikleyiciler ve Eylemler, Swagger dahil edildiğinde diğer birinci sınıf Tetikleyicileri ve eylemleri gibi görünür. | [![API Icon @ no__t-2**Azure @ no__t-4Logic Apps**][nested-logic-app-doc] | Istek tetikleyicisi ile başlayan diğer mantıksal uygulamaları çağırın. | 
||||| 

### <a name="control-workflow"></a>Denetim iş akışı

Logic Apps mantıksal uygulamanızın iş akışındaki eylemleri yapılandırmak ve denetlemek için yerleşik eylemler sağlar:

|   |   |   |   | 
|---|---|---|---| 
| [![Yerleşik simgesi @ no__t-2**koşulu**][condition-doc] | Koşulu değerlendirin ve koşulun doğru veya yanlış olduğunu temel alarak farklı eylemleri çalıştırın. | [![Yerleşik simgesi**her biri için** @ no__t-2][for-each-doc] | Bir dizideki her öğe için aynı eylemleri gerçekleştirin. | 
| [![Yerleşik simgesi @ no__t-2**kapsam**][scope-doc] | Kapsamdaki eylemlerin çalışmasını bitirdikten sonra kendi durumlarını içeren, eylemleri *kapsamlara*gruplandırın. | [![Yerleşik simgesi @ no__t-2**Switch**][switch-doc] | Eylemleri, varsayılan durum dışında benzersiz değerler atanmış *durumlarına*göre gruplandırın. Yalnızca atanmış değeri bir ifade, nesne veya belirteçten sonuçla eşleşen bu durumu çalıştırın. Hiçbir eşleşme yoksa, varsayılan durumu çalıştırın. | 
| [![Yerleşik simgesi @ no__t-2**Terminate**][terminate-doc] | Etkin bir şekilde çalışan mantıksal uygulama iş akışını durdurun. | [![Yerleşik simgesi @ no__t-2**until**][until-doc] | Belirtilen koşul doğru olana veya bir durum değiştirilene kadar eylemleri tekrarlayın. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Verileri yönetme veya işleme

Logic Apps, veri çıktıları ve biçimleri ile çalışmaya yönelik yerleşik eylemler sağlar:  

|   |   | 
|---|---| 
| [![Yerleşik simgesi @ no__t-2**veri işlemleri**][data-operations-doc] | Verilerle işlemleri gerçekleştirin: <p>- **oluşturma**: çeşitli türlerde birden çok girişe ait tek bir çıktı oluşturun. <br>- **CSV tablosu oluştur**: JSON nesneleriyle bir diziden virgülle ayrılmış değer (CSV) tablosu oluşturun. <br>- **HTML tablosu oluştur**: JSON nesneleriyle BIR diziden HTML tablosu oluşturma. <br>- **Filter Array**: ölçütlerinizi karşılayan başka bir dizideki öğelerden bir dizi oluşturun. <br>- **birleşimi**: dizideki tüm öğelerden bir dize oluşturun ve bu öğeleri belirtilen sınırlayıcıyla ayırın. <br>- **JSON ayrıştırma**: iş akışınızda bu özellikleri kullanabilmeniz için, özelliklerden ve bunların değerlerinden Kullanıcı dostu belirteçler oluşturun. <br>- **seçim**: başka bir dizideki öğeleri veya değerleri dönüştürerek ve bu öğeleri belirtilen ÖZELLIKLERLE eşleyerek JSON nesneleriyle bir dizi oluşturun. | 
| ![Yerleşik simgesi @ no__t-1<br/>**Tarih saat** | Zaman damgalarına sahip işlemleri gerçekleştirin: <p>- **saate ekleme**: belirtilen birim sayısını zaman damgasına ekleyin. <br>- **saat dilimini çevir**: kaynak saat diliminden hedef saat dilimine bir zaman damgasını dönüştürür. <br>- **geçerli zaman**: geçerli zaman damgasını bir dize olarak döndürür. <br>- **gelecek zamandan kazanın**: geçerli zaman damgasını ve belirtilen zaman birimlerini döndürün. <br>- **Geçmiş zaman al**: geçerli zaman damgasını belirtilen zaman birimleri eksi döndürün. <br>-  '**dan çıkarma**: zaman damgasından birkaç zaman birimi çıkarma. |
| [![Yerleşik simgesi @ no__t-2**değişkenleri**][variables-doc] | Değişkenlerle işlemleri gerçekleştirin: <p>- **dizi değişkenine ekleme**: bir değişken tarafından depolanan bir dizide son öğe olarak bir değer ekleyin. <br>- **dize değişkenine ekleme**: bir değişken tarafından depolanan bir dizedeki son karakter olarak bir değer ekleyin. <br>- **azaltma değişkeni**: bir değişkeni sabit bir değerle küçültün. <br>- **artış değişkeni**: bir değişkeni sabit bir değerle artırın. <br>- **değişkenini Başlat**: değişken oluşturun ve veri türünü ve başlangıç değerini bildirin. <br>- **kümesi değişkeni**: varolan bir değişkene farklı bir değer atayın. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Yönetilen API bağlayıcıları

Logic Apps, bu hizmet veya sistemlerle görevleri, işlemleri ve iş akışlarını otomatikleştirmek için bu popüler standart bağlayıcıları sağlar.

|   |   |   |   | 
|---|---|---|---| 
| [![API Icon @ no__t-2**Azure Service Bus**][azure-service-bus-doc] | Logic Apps’te en yaygın kullanılan bağlayıcı ile zaman uyumsuz iletileri, oturumları ve konu aboneliklerini yönetin. | [![API Icon @ no__t-2**SQL Server**][sql-server-doc] | Kayıtları yönetmek, saklı yordamları çalıştırmak veya sorgular gerçekleştirmek için, şirket içi SQL Server veya buluttaki bir Azure SQL veritabanı 'na bağlanın. | 
| [![API Icon @ no__t-2**Office 365 @ no__t-4Outlook**][office-365-outlook-doc] | E-postalar, görevler, takvim olayları ve toplantılar, kişiler, istekler ve daha fazlasını oluşturup yönetebilmeniz için Office 365 e-posta hesabınıza bağlanın. | [![API Icon @ no__t-2**Azure Blob @ no__t-4Storage**][azure-blob-storage-doc] | Blob içeriğini oluşturup yönetebilmeniz için depolama hesabınıza bağlanın. | 
| [![API Icon @ no__t-2**SFTP**][sftp-doc] | Dosya ve klasörlerinizle çalışabilmeniz için İnternet’ten erişebileceğiniz SFTP sunucularına bağlanın. | [![API Icon @ no__t-2**SharePoint @ no__t-4Online**][sharepoint-online-doc] | Dosyaları, ekleri, klasörleri ve daha fazlasını yönetebilmeniz için SharePoint Online 'a bağlanın. | 
| [![API Icon @ no__t-2**Dynamics 365 @ no__t-4CRM online**][dynamics-365-doc] | Kayıtlar, öğeler ve daha fazlasını oluşturup yönetebilmeniz için Dynamics 365 hesabınıza bağlanın. | [![API Icon @ no__t-2**FTP**][ftp-doc] | Dosya ve klasörlerle çalışabilmeniz için internet 'ten erişebileceğiniz FTP sunucularına bağlanın. | 
| [![API Icon @ no__t-2**Salesforce**][salesforce-doc] | Kayıtlar, işler, nesneler ve daha fazlası gibi öğeleri oluşturup yönetebilmeniz için Salesforce hesabınıza bağlanın. | [![API Icon @ no__t-2**Twitter**][twitter-doc] | Twitter, takicileri, zaman çizelgenizi ve daha fazlasını yönetebilmeniz için Twitter hesabınıza bağlanın. Tasarruf etlerinizi SQL, Excel veya SharePoint 'e kaydedin. | 
| [![API Icon @ no__t-2**Azure Event Hubs**][azure-event-hubs-doc] | Olayları bir olay hub 'ı aracılığıyla tüketin ve yayımlayın. Örneğin, Event Hubs ile mantıksal uygulamanızdan çıkış alın ve ardından bu çıktıyı gerçek zamanlı bir analiz sağlayıcısına gönderin. | [![API Icon @ no__t-2**Azure Event**</br>**kılavuz**][azure-event-grid-doc] | Örneğin, Azure kaynakları veya üçüncü taraf kaynakları değiştiğinde Event Grid tarafından yayımlanan olayları izleyin. | 
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Şirket içi bağlayıcılar 

Şirket içi sistemlerdeki verilere ve kaynaklara erişmek için Logic Apps sağladığı yaygın olarak kullanılan bazı standart bağlayıcılar aşağıda verilmiştir. Şirket içi bir sisteme bağlantı oluşturabilmeniz için önce şirket [içi veri ağ geçidini indirmeniz, yüklemeniz ve ayarlamanız][gateway-doc]gerekir. Bu ağ geçidi, gerekli ağ altyapısını ayarlamak zorunda kalmadan güvenli bir iletişim kanalı sağlar. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![APı simgesi @ no__t-1<br/>**BIZ**</br> **Sunucu** | [![API Icon @ no__t-2**FILE @ no__t-4 System**][file-system-doc] | [![API Icon @ no__t-2**IBM DB2**][ibm-db2-doc] | [![API Icon @ no__t-2**ıbm**</br> **Informix**][ibm-informix-doc] | ![APı simgesi @ no__t-1<br/>**MySQL** | 
| [![API Icon @ no__t-2**Oracle DB**][oracle-db-doc] | ![APı simgesi @ no__t-1<br/>**PostgreSQL** | [![API Icon @ no__t-2**SharePoint @ no__t-4 Server**][sharepoint-server-doc] | [![API Icon @ no__t-2**SQL @ no__t-4 Server**][sql-server-doc] | ![APı simgesi @ no__t-1<br/>**Teradata** | 
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Tümleştirme hesabı bağlayıcıları

Logic Apps, Azure 'da Enterprise Integration Pack (EıP) aracılığıyla kullanılabilen bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)oluştururken ve ödeyerek, Logic Apps ile işletmeler arası (B2B) çözümleri oluşturmaya yönelik standart bağlayıcılar sağlar. Bu hesapla, ticaret ortakları, anlaşmalar, Haritalar, şemalar, sertifikalar vb. gibi B2B yapıtları oluşturabilir ve bunları kaydedebilirsiniz. Bu yapıtları kullanmak için, mantıksal uygulamalarınızı tümleştirme hesabınızla ilişkilendirin. Şu anda BizTalk Server kullanıyorsanız, bu bağlayıcılar zaten tanıdık görünebilir.

|   |   |   |   | 
|---|---|---|---| 
| [![API Icon @ no__t-2**AS2 @ no__t-4 kod çözme**][as2-doc] | [![API Icon @ no__t-2**AS2 @ no__t-4 Encoding**][as2-doc] | [![API Icon @ no__t-2**Ediolgu @ no__t-4 kod çözme**][edifact-decode-doc] | [![API Icon @ no__t-2**Ediolgu @ no__t-4 Encoding**][edifact-encode-doc] | 
| [![API Icon @ no__t-2**düz dosya @ no__t-4 kod çözme**][flat-file-decode-doc] | [![API Icon @ no__t-2**düz dosya @ no__t-4 Encoding**][flat-file-encode-doc] | [![API Icon @ no__t-2**Integration @ no__t-4account**][integration-account-doc] | [![API Icon @ no__t-2**sıvı**</br>**dönüşümler**][json-liquid-transform-doc] | 
| [![API Icon @ no__t-2**x12 @ no__t-4 kod çözme**][x12-decode-doc] | [![API Icon @ no__t-2**x12 @ no__t-4 Encoding**][x12-encode-doc] | [![API Icon @ no__t-2**XML**</br>**dönüşümler**][xml-transform-doc] | [![API Icon @ no__t-2**XML <br/>validation**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Kurumsal bağlayıcılar

Logic Apps, SAP ve IBM MQ gibi kurumsal sistemlere erişmek için bu kurumsal bağlayıcıları sağlar:

|   |   |   | 
|---|---|---| 
| [![API Icon @ no__t-2**ıbm 3270**][ibm-3270-doc] | [![API Icon @ no__t-2**IBM MQ**][ibm-mq-doc] | [![API Icon @ no__t-2**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Tetikleyiciler ve eylemler-daha fazla bilgi

Bağlayıcılar, *Tetikleyiciler*, *Eylemler*veya her ikisini de sağlayabilir. *Tetikleyici* , genellikle tetikleyiciyi tetikleyen ve mantıksal uygulamanızı çalıştırmaya başladığı olayı belirten herhangi bir mantıksal uygulamadaki ilk adımdır. Örneğin, FTP Bağlayıcısı, "bir dosya eklendiğinde veya değiştirildiğinde" mantıksal uygulamanızı başlatan bir tetikleyicisine sahiptir. Bazı Tetikleyiciler, belirtilen olay veya verileri düzenli olarak denetler ve ardından belirtilen olay veya verileri algılarsa tetiklenir. Diğer Tetikleyiciler bekler, ancak belirli bir olay gerçekleştiğinde veya yeni veri kullanılabilir olduğunda anında harekete geçecektir. Tetikleyiciler ayrıca mantıksal uygulamanıza gerekli verileri de geçirir. Mantıksal uygulamanız bu verileri iş akışı boyunca okuyabilir ve kullanabilir.
Örneğin, Twitter Bağlayıcısı, "yeni bir tweet gönderildiğinde" bir tetikleyicisine sahiptir. Bu, Tweet içeriğini mantıksal uygulamanızın iş akışına geçirir. 

Bir tetikleyici etkinleştirildikten sonra, Azure Logic Apps mantıksal uygulamanızın bir örneğini oluşturur ve mantıksal uygulamanızın iş akışındaki *eylemleri* çalıştırmaya başlar. Eylemler, tetikleyiciyi izleyen ve mantıksal uygulamanızın iş akışında görevleri gerçekleştiren adımlardır. Örneğin, bir SQL veritabanından müşteri verilerini alan ve daha sonraki eylemlerdeki verileri işleyen bir mantıksal uygulama oluşturabilirsiniz. 

Azure Logic Apps sağladığı genel tetikleyici türleri şunlardır:

* *Yinelenme tetikleyicisi*: Bu tetikleyici belirtilen bir zamanlamaya göre çalışır ve belirli bir hizmet ya da sistemle sıkı bir şekilde ilişkilendirilmez.

* *Yoklama tetikleyicisi*: Bu tetikleyici belirtilen zamanlamaya göre belirli bir hizmeti veya sistemi düzenli olarak yoklar, yeni verileri denetler veya belirli bir olayın olup olmadığını denetler. Yeni veriler varsa veya belirli bir olay meydana gelirse, tetikleyici mantıksal uygulamanızın yeni bir örneğini oluşturur ve çalıştırır. Bu, artık giriş olarak geçirilen verileri kullanabilir.

* *Gönderim tetikleyicisi*: Bu tetikleyici, yeni verileri bekler ve dinler veya bir olayın gerçekleşmesini ister. Yeni veriler kullanılabilir olduğunda veya olay gerçekleştiğinde, tetikleyici mantıksal uygulamanızın yeni bir örneğini oluşturur ve çalıştırır. Bu, artık giriş olarak geçirilen verileri kullanabilir.

<a name="custom"></a>

## <a name="connector-configuration"></a>Bağlayıcı yapılandırması

Her bağlayıcının Tetikleyicileri ve eylemleri, yapılandırmanız için kendi özelliklerini sağlar. Birçok bağlayıcı, mantıksal uygulamanızda bir tetikleyici veya eylem kullanabilmeniz için öncelikle hedef hizmet veya sisteme bir *bağlantı* oluşturmanızı ve kimlik doğrulama kimlik bilgilerini ya da diğer yapılandırma ayrıntılarını sağlamanızı gerektirir. Örneğin, verilere erişim veya sizin adınıza gönderi için Twitter hesabına bir bağlantı yetkilendirmelisiniz. 

Azure Active Directory (Azure AD) OAuth kullanan bağlayıcılar için bağlantı oluşturmak, erişim belirtecinizin [şifreli](../security/fundamentals/encryption-overview.md) ve güvenli bir şekilde bir Azure gizli deposunda depolandığı Office 365, Salesforce veya GitHub gibi hizmette oturum açma anlamına gelir. FTP ve SQL gibi diğer bağlayıcılar, sunucu adresi, Kullanıcı adı ve parola gibi yapılandırma ayrıntılarına sahip bir bağlantı gerektirir. Bu bağlantı yapılandırma ayrıntıları da şifrelenir ve güvenli bir şekilde depolanır. [Azure 'da şifreleme](../security/fundamentals/encryption-overview.md)hakkında daha fazla bilgi edinin.

Bağlantılar, hizmet veya sistem izin verdiği sürece hedef hizmete veya sisteme erişebilir. Office 365 ve Dynamics gibi Azure AD OAuth bağlantıları kullanan hizmetler için Azure Logic Apps erişim belirteçlerini süresiz olarak yeniler. Diğer hizmetlerde, Azure Logic Apps yenileme olmadan ne kadar süreyle bir belirteç kullanabileceği hakkında sınırlar bulunabilir. Genellikle, bazı eylemler parolanızı değiştirme gibi tüm erişim belirteçlerini geçersiz kılar.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Özel API 'Ler ve bağlayıcılar

Özel kod çalıştıran veya bağlayıcı olarak kullanılamayan API 'Leri çağırmak için, [özel API Apps oluşturarak](../logic-apps/logic-apps-create-api-app.md)Logic Apps platformunu genişletebilirsiniz. Ayrıca, bu API 'Leri Azure aboneliğinizdeki herhangi bir mantıksal uygulama için kullanılabilir hale getirmek *üzere bekleyen veya* SOAP tabanlı API 'ler için [özel bağlayıcılar oluşturabilirsiniz](../logic-apps/custom-connector-overview.md) .
Özel API Apps veya bağlayıcıları herkesin Azure 'da kullanması için genel hale getirmek amacıyla, [Microsoft sertifikası için bağlayıcılar gönderebilirsiniz](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Bir [tümleştirme hizmeti ortamı (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) içindeki Logic Apps, bir Azure sanal ağındaki kaynaklara doğrudan erişebilir.
> Şirket içi veri ağ geçidini gerektiren özel bağlayıcılarınız varsa ve bu bağlayıcıları bir ıSE dışında oluşturduysanız, bir ıSE içindeki Logic Apps de bu bağlayıcıları kullanabilir.
>
> Bir ıSE içinde oluşturulan özel bağlayıcılar şirket içi veri ağ geçidi ile çalışmaz. Ancak bu bağlayıcılar, ıSE 'yi barındıran bir Azure sanal ağına bağlı şirket içi veri kaynaklarına doğrudan erişebilir. Bu nedenle, bir ıSE içindeki Logic Apps, bu kaynaklarla iletişim kurarken veri ağ geçidine ihtiyaç duymamasından kaynaklanıyor olabilir.
>
> Sesleri oluşturma hakkında daha fazla bilgi için bkz. [Azure Logic Apps Azure sanal ağlarına bağlanma](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Bağlayıcıların tam listesini](https://docs.microsoft.com/connectors) bulun
* [İlk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Mantıksal uygulamalar için özel bağlayıcılar oluşturma](https://docs.microsoft.com/connectors/custom-connectors/)
* [Mantıksal uygulamalar için özel API’ler oluşturma](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Şirket içi veri ağ geçidiyle mantıksal uygulamalardan şirket içi veri kaynaklarına bağlanın"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Mantıksal uygulamaları Azure İşlevleri ile tümleştirin"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "API 'lerinizi yönetmek ve yayımlamak için bir Azure API Management hizmet örneği oluşturma"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Mantıksal uygulamaları App Service API Apps ile tümleştirin"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Service Bus Kuyruklarından ve Konu Başlıklarından iletiler gönderin ve Service Bus Kuyrukları ile Aboneliklerinden iletiler alın"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Gruplardaki iletileri veya toplu işlem olarak işleme"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Koşulu değerlendirin ve koşulun doğru veya yanlış olduğunu temel alarak farklı eylemleri çalıştırın"
[delay-doc]: ./connectors-native-delay.md "Gecikmeli eylemleri gerçekleştirin"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Bir dizideki her öğe için aynı eylemleri gerçekleştirin"
[http-doc]: ./connectors-native-http.md "HTTP bağlayıcısı ile HTTP aramaları yapın"
[http-request-doc]: ./connectors-native-reqres.md "HTTP istekleri ve mantıksal uygulama yanıtları için eylemler ekleyin"
[http-response-doc]: ./connectors-native-reqres.md "HTTP istekleri ve mantıksal uygulama yanıtları için eylemler ekleyin"
[http-swagger-doc]: ./connectors-native-http-swagger.md "HTTP + Swagger bağlayıcısı ile HTTP çağrıları yapın"
[http-webook-doc]: ./connectors-native-webhook.md "Mantıksal uygulamalarınıza HTTP Web kancası eylemleri ve Tetikleyicileri ekleme"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Mantıksal uygulamaları iç içe geçmiş iş akışları ile tümleştirin"
[query-doc]: ./connectors-native-query.md "Sorgu eylemi ile dizileri seçip filtreleyin"
[recurrence-doc]:  ./connectors-native-recurrence.md "Mantıksal uygulamalar için yinelenen eylemleri tetikleyin"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Grup tamamlandığında   ' yi çalıştırmayı tamamladıktan sonra kendi durumlarını almak için eylemleri gruplar halinde düzenleyin"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Eylemleri, benzersiz değerler atanmış durumlarda düzenleyin. Yalnızca değeri bir ifade, nesne veya belirteçten sonuçla eşleşen bir durum çalıştırın. Hiçbir eşleşme yoksa, varsayılan durumu çalıştırın"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Mantıksal uygulamanız için etkin olarak çalışan bir iş akışını durdurma veya iptal etme"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Belirtilen koşul doğru olana veya bir durum değiştirilene kadar eylemleri tekrarlayın"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Dizileri filtreleme veya CSV ve HTML tabloları oluşturma gibi veri işlemleri gerçekleştirin"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Başlatma, ayarlama, artırma, azaltma ve dize veya dizi değişkenine ekleme gibi değişkenlerle işlemleri gerçekleştirin"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Blob kapsayıcınızdaki dosyaları Azure blob depolama bağlayıcısı ile yönetin"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Örneğin, Azure kaynakları veya üçüncü taraf kaynakları değiştiğinde Event Grid tarafından yayımlanan olayları izleme"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Azure Event Hubs 'e bağlanın. Logic Apps ve Event Hubs arasında olay alma ve gönderme"
[box-doc]: ./connectors-create-api-box.md "Kutuya Bağlan. Karşıya yükleyin, alın, silin, dosyalarınızı listeleyin ve daha fazlasını yapın"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Dropbox 'a bağlanın. Karşıya yükleyin, alın, silin, dosyalarınızı listeleyin ve daha fazlasını yapın"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "CRM Online verileriyle çalışabilmek için Dynamics CRM Online’a bağlanın"
[facebook-doc]: ./connectors-create-api-facebook.md "Facebook 'a bağlanın. Bir zaman çizelgesinde gönderi yapın, sayfa akışı alın ve daha fazlasını yapın"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Şirket içi dosya sistemine bağlanın"
[ftp-doc]: ./connectors-create-api-ftp.md "Dosyaları karşıya yükleme, alma, silme ve diğer FTP görevleri için bir FTP / FTPS sunucusuna bağlanın"
[github-doc]: ./connectors-create-api-github.md "GitHub’a bağlanın ve sorunları izleyin"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Google Takvime bağlanır ve takvimi yönetebilir."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Verilerinizle çalışabilmek için GoogleDrive’a bağlanın"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Sayfalarınızı değiştirebilmeniz için Google sayfalarına bağlanın"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Görevlerinizi yönetebilmek için Google Görevler’e bağlanın"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "IBM ana bilgisayarları üzerinde 3270 uygulamalarına bağlanma"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Bulutta veya şirket içinde IBM DB2 'ye bağlanın. Bir satırı güncelleştirin, tablo alın ve daha fazlasını yapın"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Bulutta veya şirket içinde Informix 'e bağlanın. Bir satırı okuyun, tabloları listeleyin ve daha fazlasını yapın"
[ibm-mq-doc]: ./connectors-create-api-mq.md "İleti göndermek ve almak için IBM MQ şirket içi veya Azure 'a bağlanma"
[instagram-doc]: ./connectors-create-api-instagram.md "Instagram 'a bağlanın. Olayları tetikleme veya üzerinde işlem yapın"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "MailChimp hesabınıza bağlanın. Postaları yönetin ve otomatikleştirin"
[mandrill-doc]: ./connectors-create-api-mandrill.md "İletişim için Mandrill’e bağlanın"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Microsoft Translator 'A bağlanın. Metin çevirme, dilleri algılama ve daha fazla" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Office 365 hesabınıza bağlanın. E-posta gönderin ve alın, takviminizi ve kişilerinizi yönetin ve daha fazlasını yapın"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Office 365 için video bilgileri, video listeleri ile kanallar ve kayıttan yürütme URL'lerini alın"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Kişisel Microsoft OneDrive 'ınızdan bağlantı kurmak. Karşıya yükleme, silme, dosyaları listeleme ve daha fazlası"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "İş Microsoft OneDrive 'a bağlanın. Dosyalarınızı karşıya yükleyin, silin, listeleyin ve daha fazlasını yapın"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Bir Oracle veritabanına bağlanarak satır ekleme, silme ve daha fazlası"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Outlook posta kutunuza bağlanın. E-postanızı, takvimlerinizi, kişilerinizi ve daha fazlasını yönetin"
[project-online-doc]: ./connectors-create-api-projectonline.md "Microsoft Project Online 'a bağlanın. Projelerinizi, görevlerinizi, kaynaklarınızı ve daha fazlasını yönetin"
[rss-doc]: ./connectors-create-api-rss.md "Akış öğeleri yayımlayıp alın, RSS akışında yeni bir öğe yayımlandığında işlemleri tetikleyin."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Salesforce hesabınıza bağlanın. Hesapları, müşteri adaylarını, fırsatları ve daha fazlasını yönetin"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Şirket içi SAP sistemine bağlanın"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "SendGrid 'e bağlanın. E-posta gönderme ve alıcı listelerini yönetme"
[sftp-doc]: ./connectors-create-api-sftp.md "SFTP hesabınıza bağlanın. Dosyaları karşıya yükleyin, alın, silin ve daha fazlasını yapın"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "SharePoint şirket içi sunucusuna bağlanın. Belgeleri, liste öğelerini ve daha fazlasını yönetin"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "SharePoint Online 'a bağlanın. Belgeleri, liste öğelerini ve daha fazlasını yönetin"
[slack-doc]: ./connectors-create-api-slack.md "Slack’e bağlanın ve Slack kanallarında iletiler yayınlayın"
[smtp-doc]: ./connectors-create-api-smtp.md "Bir SMTP sunucusuna bağlanın ve ekleri olan e-posta gönderin"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "İletişim için SparkPost’a bağlanın"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Azure SQL veritabanı 'na veya SQL Server bağlanın. Bir SQL veritabanı tablosunda girdiler oluşturun, güncelleştirin, alın ve silin."
[trello-doc]: ./connectors-create-api-trello.md "Trello 'a bağlanın. Projelerinizi yönetin ve herkesle her şeyi düzenleyin"
[twilio-doc]: ./connectors-create-api-twilio.md "Twilio 'e bağlanın. İleti gönderin ve alın, kullanılabilir numaraları alın, gelen telefon numaralarını yönetin ve daha fazlasını yapın"
[twitter-doc]: ./connectors-create-api-twitter.md "Twitter 'a bağlanın. Zaman çizelgelerini alın, daha fazla nakil yapın ve daha fazlasını yapın"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Wunderlist 'e bağlanın. Görevlerinizi ve Yapılacaklar listelerinizi yönetin, yaşamınızı eşitlenmiş halde tutun ve daha fazlasını yapın"
[yammer-doc]: ./connectors-create-api-yammer.md "Yammer 'a bağlanın. İleti gönderin, yeni iletiler alın ve daha fazlasını yapın"
[youtube-doc]: ./connectors-create-api-youtube.md "YouTube 'a bağlanın. Videolarınızı ve kanallarınızı yönetin"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Enterprise integration AS2 hakkında bilgi edinin."
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Enterprise integration EDIFACT kod çözme hakkında bilgi edinin"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Enterprise integration EDIFACT kodlama hakkında bilgi edinin"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Enterprise integration düz dosyası hakkında bilgi edinin."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Enterprise integration düz dosyası hakkında bilgi edinin."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Tümleştirme hesabınızda şemalar, haritalar, iş ortakları ve daha fazlasını arayın"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Liquid ile JSON dönüştürmeleri hakkında bilgi edinin"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Enterprise integration X12 hakkında bilgi edinin."
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Enterprise integration X12 kod çözme hakkında bilgi edinin"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Enterprise integration X12 kodlama hakkında bilgi edinin"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Enterprise integration dönüştürmeleri hakkında bilgi edinin."
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Enterprise integration XML doğrulaması hakkında bilgi edinin."

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[wunderlist-icon]: ./media/apis-list/wunderlist.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
