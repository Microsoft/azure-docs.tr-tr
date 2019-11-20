---
title: Azure 'da geliştiriciler için Başlarken Kılavuzu | Microsoft Docs
description: Bu konu, geliştirme ihtiyaçları için Microsoft Azure platformunu kullanmaya başlamak isteyen geliştiriciler için önemli bilgiler sağlar.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: 367027a48dd51dd88de63cb163e05e0e8503755d
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308575"
---
# <a name="get-started-guide-for-azure-developers"></a>Azure geliştiricileri için kullanmaya başlama kılavuzu

## <a name="what-is-azure"></a>Azure nedir?

Azure, mevcut uygulamalarınızı barındırabilecek, yeni uygulamaların geliştirilmesini kolaylaştıran ve hatta şirket içi uygulamaları geliştiren tam bir bulut platformudur. Azure, bulut bilgi işlemin verimlilik avantajlarından yararlanarak uygulamalarınızı geliştirmek, test etmek, dağıtmak ve yönetmek için ihtiyacınız olan bulut hizmetlerini tümleştirir.

Uygulamalarınızı Azure 'da barındırarak küçük bir başlangıç yapabilir ve uygulamanızı, müşteri talebi büyüdükçe kolayca ölçeklendirebilirsiniz. Azure Ayrıca, farklı bölgeler arasında yük devretme da dahil olmak üzere yüksek kullanılabilirliğe sahip uygulamalar için gereken güvenilirliği sunar. [Azure Portal](https://portal.azure.com) tüm Azure hizmetlerinizi kolayca yönetmenize olanak sağlar. Ayrıca, hizmet özel API 'Leri ve şablonları kullanarak hizmetlerinizi programlı bir şekilde yönetebilirsiniz.

**Bunu kimler okumalı**: Bu kılavuz, uygulama geliştiricileri için Azure platformuna giriş niteliğindedir. Azure 'da yeni uygulamalar oluşturmaya veya mevcut uygulamaları Azure 'a geçirmeye başlamak için gereken yönergeler ve yön sağlar.

## <a name="where-do-i-start"></a>Nereden başlamalıyım?

Azure 'un sunduğu tüm hizmetlerle, çözüm mimarinizi desteklemek için hangi hizmetlerin gerekli olduğunu anlamak için kullanılabilecek bir görev olabilir. Bu bölümde geliştiricilerin yaygın olarak kullandığı Azure hizmetleri vurgulanmaktadır. Tüm Azure hizmetlerinin listesi için bkz. [Azure belgeleri](../../index.md).

İlk olarak, Azure 'da uygulamanızı barındırmaya karar vermelisiniz. Tüm altyapınızı bir sanal makine (VM) olarak yönetmeniz gerekir. Azure 'un sağladığı platform yönetimi olanaklarını kullanabilir misiniz? Yalnızca kod yürütmeyi barındırmak için sunucusuz bir çerçeveye ihtiyacınız olabilir mi?

Uygulamanız için Azure 'un çeşitli seçeneklerini sağladığı bulut depolaması gerekir. Azure 'un kurumsal kimlik doğrulamasının avantajlarından yararlanabilirsiniz. Bulut tabanlı geliştirme ve izleme için de araçlar vardır ve birçok barındırma hizmeti DevOps tümleştirmesi sunar.

Şimdi, uygulamalarınız için araştırmanın önerdiğimiz belirli hizmetlerden bazılarını inceleyelim.

### <a name="application-hosting"></a>Uygulama barındırma

Azure, uygulamanızı çalıştırmak için çeşitli bulut tabanlı bilgi işlem teklifleri sağlar, böylece altyapı ayrıntıları konusunda endişelenmenize gerek kalmaz. Uygulamanızın kullanımı arttıkça kaynaklarınızın ölçeğini kolayca ölçeklendirebilir veya ölçekleyebilirsiniz.

Azure, uygulama geliştirme ve barındırma gereksinimlerinizi destekleyen hizmetler sunar. Azure, uygulamanızın barındırılması üzerinde tam denetim sağlamak için hizmet olarak altyapı (IaaS) sağlar. Azure 'un hizmet olarak platform (PaaS) teklifleri, uygulamalarınızı desteklemek için gereken tam olarak yönetilen Hizmetleri sağlar. Azure 'da, tüm yapmanız gereken, kodunuzun yazılması için de doğru sunucusuz barındırma vardır.

![Azure uygulama barındırma seçenekleri](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure uygulama hizmeti 

En hızlı yolun Web tabanlı projelerinizi yayımlamasını istediğinizde Azure App Service göz önünde bulundurun. App Service, Web uygulamalarınızı mobil istemcilerinizi destekleyecek şekilde genişletmeyi ve kolayca tüketilen REST API 'Leri yayımlamayı kolaylaştırır. Bu platform Sosyal sağlayıcılar, trafik tabanlı otomatik ölçeklendirme, üretimde test etme ve sürekli ve kapsayıcı tabanlı dağıtımlar kullanarak kimlik doğrulaması sağlar.

Web uygulamaları, mobil uygulama arka uçları ve API uygulamaları oluşturabilirsiniz.

Her üç uygulama türü App Service çalışma zamanını paylaştığından, bir Web sitesi barındırabilir, mobil istemcileri destekleyebilir ve API 'lerinizi Azure 'da, her türlü aynı proje veya çözümden bir şekilde kullanıma sunabilirsiniz. App Service hakkında daha fazla bilgi edinmek için bkz. [Azure Web Apps nedir?](../../app-service/overview.md).

App Service, DevOps göz önünde bulundurularak tasarlanmıştır. GitHub Web kancaları, Jenkins, Azure DevOps, TeamCity ve diğerleri dahil olmak üzere yayımlama ve sürekli tümleştirme dağıtımları için çeşitli araçları destekler.

[Çevrimiçi geçiş aracını](https://www.migratetoazure.net/)kullanarak, mevcut uygulamalarınızı App Service geçirebilirsiniz.

> **Ne zaman kullanılır**: Mevcut Web uygulamalarını Azure 'a geçirirken ve Web uygulamalarınız için tam olarak yönetilen bir barındırma platformuna ihtiyacınız olduğunda App Service kullanın. Ayrıca, mobil istemcileri destekletmelisiniz veya REST API 'Leri uygulamanızla birlikte kullanıma sunabileceğiniz App Service de kullanabilirsiniz.
> 
> **Kullanmaya**başlayın: App Service ilk [Web uygulamanızı](../../app-service/app-service-web-get-started-dotnet.md), [Mobil uygulamanızı](../../app-service-mobile/app-service-mobile-ios-get-started.md)veya [API uygulamanızı](../../app-service/app-service-web-tutorial-rest-api.md)oluşturmayı ve dağıtmayı kolaylaştırır.
> 
> **Şimdi deneyin**: App Service, bir Azure hesabına kaydolmadan platformu denemek için kısa süreli bir uygulama sağlamanıza olanak tanır. Platformu deneyin ve [Azure App Service uygulamanızı oluşturun](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure sanal makineleri

Hizmet olarak altyapı (IaaS) sağlayıcısı olarak Azure, uygulamanızı Windows veya Linux VM 'lerine dağıtmanıza veya geçirmenize olanak sağlar. Azure sanal ağ ile birlikte Azure sanal makineleri, Windows veya Linux VM 'lerinin Azure 'a dağıtımını destekler. VM 'Ler ile makinenin yapılandırması üzerinde toplam denetiminiz vardır. VM 'Leri kullanırken tüm sunucu yazılımı yükleme, yapılandırma, bakım ve işletim sistemi düzeltme ekleri sizin sorumluluğunuzdadır.

VM 'lerle sahip olduğunuz denetim düzeyi nedeniyle, Azure 'da PaaS modeline sığmayan çok çeşitli sunucu iş yüklerini çalıştırabilirsiniz. Bu iş yükleri, veritabanı sunucularını, Windows Server Active Directory ve Microsoft SharePoint 'i içerir. Daha fazla bilgi için bkz. [Linux](/azure/virtual-machines/linux/) veya [Windows](/azure/virtual-machines/windows/)için sanal makineler belgeleri.

> **Ne zaman kullanılır**: Uygulama altyapınız üzerinde tam denetim sağlamak veya şirket içi uygulama iş yüklerini değişiklik yapmadan Azure 'a geçirmek istediğinizde sanal makineleri kullanın.
> 
> **Kullanmaya**başlayın: Azure portal bir [LINUX VM](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) veya [Windows VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) 'si oluşturun.

#### <a name="azure-functions-serverless"></a>Azure Işlevleri (sunucusuz)

Kodunuzu çalıştırmak için tüm uygulamayı veya altyapıyı oluşturma ve yönetme konusunda endişelenmek yerine. Yalnızca kodunuzu yazmak ve olaylara veya bir zamanlamaya yanıt olarak çalıştırmak için ne olur?  [Azure işlevleri](../../azure-functions/functions-overview.md) , yalnızca ihtiyacınız olan kodu yazmanızı sağlayan "sunucusuz" tarzı bir tekliftir. Işlevlerle, kod yürütme HTTP istekleri, Web kancaları, bulut hizmeti olayları veya bir zamanlamaya göre tetiklenir. C\#, F\#, Node. js, Python veya PHP gibi tercih ettiğiniz geliştirme dilinizde kod oluşturabilirsiniz. Tüketim tabanlı faturalandırma sayesinde yalnızca kodunuzun yürütüldüğü süre için ödeme yaparsınız ve gerektiğinde Azure ölçeklendiriyor olursunuz.

> **Ne zaman kullanılır**: Diğer Azure hizmetleri tarafından tetiklenen kodunuz varsa, Web tabanlı olaylara veya bir zamanlamaya göre Azure Işlevleri 'ni kullanın. Ayrıca, tüm barındırılan bir projenin ek yüküne gerek duymadığınızda veya yalnızca kodunuzun çalıştığı zaman için ödeme yapmak istediğinizde Işlevleri de kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Işlevlerine genel bakış](../../azure-functions/functions-overview.md).
> 
> **Kullanmaya**başlayın: Portaldan [ilk işlevinizi oluşturmak](../../azure-functions/functions-create-first-azure-function.md) için işlevler hızlı başlangıç öğreticisini izleyin.
> 
> **Şimdi deneyin**: Azure Işlevleri, Azure hesabına kaydolmak zorunda kalmadan kodunuzu çalıştırmanızı sağlar. Şimdi deneyin ve [Ilk Azure işlevinizi oluşturun](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric, ölçeklenebilir ve güvenilir mikro hizmetler oluşturmayı, paketlemeyi, dağıtmayı ve yönetmeyi kolaylaştıran bir dağıtılmış sistemler platformudur. Ayrıca, dağıtılmış uygulamaları sağlama, dağıtma, izleme, yükseltme/düzeltme eki uygulama ve silme için kapsamlı uygulama yönetimi özellikleri sağlar. Paylaşılan bir makine havuzunda çalışan uygulamalar, gerektiğinde yüzlerce veya binlerce makineye kadar küçük ve ölçeklenebilir olabilir.

Service Fabric, .NET için açık Web arabirimi (OWıN) ve ASP.NET Core ile WebAPI 'yi destekler. Hem .NET Core hem de Java 'da Linux üzerinde hizmet oluşturmaya yönelik SDK 'lar sağlar. Service Fabric hakkında daha fazla bilgi edinmek için [Service Fabric belgelerine](https://docs.microsoft.com/azure/service-fabric/)bakın.

> **Ne zaman kullanılır:** Service Fabric, bir uygulama oluştururken veya bir mikro hizmet mimarisini kullanmak için mevcut bir uygulamayı yeniden yazarken iyi bir seçenektir. Üzerinde daha fazla denetime ihtiyacınız olduğunda veya temel alınan altyapıya doğrudan erişim için Service Fabric kullanın.
> 
> **Başlarken:** [Ilk Azure Service Fabric uygulamanızı oluşturun](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Azure hizmetleriyle uygulamalarınızı geliştirin

Azure, uygulama barındırmanın yanı sıra, hem bulutta hem de şirket içinde uygulamalarınızın işlevlerini, geliştirilmesini ve bakımını geliştirebilecek hizmet teklifleri sağlar.

#### <a name="hosted-storage-and-data-access"></a>Barındırılan depolama ve veri erişimi

Çoğu uygulama verileri depolamalıdır, böylece uygulamanızı Azure 'da barındırmaya nasıl karar vertiğinize bakılmaksızın aşağıdaki depolama ve veri hizmetlerinden birini veya birkaçını göz önünde bulundurun.

- **Azure Cosmos DB**: Genel olarak dağıtılmış, çok modelli bir veritabanı hizmeti, kapsamlı bir SLA ile herhangi bir sayıda coğrafi bölgede işleme ve depolamayı esnek etmenizi sağlar. 
  > **Ne zaman kullanılır:** Uygulamanız, MongoDB veritabanları da dahil olmak üzere belge, tablo veya grafik veritabanlarına ihtiyaç duyduğunda, çok iyi tanımlanmış tutarlılık modelleriyle. 
  > 
  > **Kullanmaya**başlayın: [Azure Cosmos DB bir Web uygulaması oluşturun](../../cosmos-db/create-sql-api-dotnet.md). MongoDB geliştiriciyseniz, bkz. [Azure Cosmos DB MongoDB Web uygulaması oluşturma](../../cosmos-db/create-mongodb-dotnet.md).

- **Azure depolama**: Bloblar, kuyruklar, dosyalar ve diğer ilişkisel olmayan veri türleri için dayanıklı, yüksek oranda kullanılabilir depolama sağlar. Depolama, sanal makineler için depolama temeli sağlar.

  > **Ne zaman kullanılır**: Uygulamanız, anahtar-değer çiftleri (tablolar), Bloblar, dosya paylaşımları veya iletiler (kuyruklar) gibi ilişkisel olmayan verileri depoladığında.
  > 
  > **Kullanmaya**başlayın: Şu türlerden birini seçin: [Bloblar](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [Tablolar](../../cosmos-db/table-storage-how-to-use-dotnet.md), [Kuyruklar](../../storage/queues/storage-dotnet-how-to-use-queues.md)veya [dosyalar](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL veritabanı**: Bulutta ilişkisel tablo verilerini depolamak için Microsoft SQL Server altyapısının Azure tabanlı bir sürümü. SQL veritabanı, kesinti süresi olmadan öngörülebilir performans, ölçeklenebilirlik, iş sürekliliği ve veri koruması sağlar.

  > **Ne zaman kullanılır**: Uygulamanız, bilgi tutarlılığı, işlem desteği ve TSQL sorguları desteği ile veri depolama alanı gerektirdiğinde.
  > 
  > **Kullanmaya**başlayın: [Azure Portal kullanarak dakikalar içinde BIR SQL veritabanı oluşturun](../../sql-database/sql-database-get-started.md).


Mevcut şirket içi verileri Azure 'a taşımak için [Azure Data Factory](../../data-factory/introduction.md) kullanabilirsiniz. Verileri buluta taşımaya hazırsanız, BizTalk Services [Karma Bağlantılar](../../biztalk-services/integration-hybrid-connection-overview.md) App Service barındırılan uygulamanızı şirket içi kaynaklara bağlamanıza olanak tanır. Ayrıca, şirket içi uygulamalarınızdan Azure veri ve depolama hizmetleri 'ne de bağlanabilirsiniz.

#### <a name="docker-support"></a>Docker desteği

Bir işletim sistemi sanallaştırma biçimindeki Docker kapsayıcıları, uygulamaları daha verimli ve öngörülebilir bir şekilde dağıtmanıza olanak tanır. Kapsayıcılı bir uygulama, geliştirme ve test sistemlerinizle aynı şekilde üretimde çalışmaktadır. Standart Docker araçlarını kullanarak kapsayıcıları yönetebilirsiniz. Azure 'da kapsayıcı tabanlı uygulamalar dağıtmak ve yönetmek için mevcut becerilerinizi ve popüler açık kaynaklı araçları kullanabilirsiniz.

Azure, uygulamalarınızda kapsayıcıları kullanmanın birkaç yolunu sağlar.

- **Azure Docker VM Uzantısı**: Bir Docker ana bilgisayarı görevi görecek şekilde sanal makinmenizi Docker araçlarıyla yapılandırmanıza olanak tanır.

  > **Ne zaman kullanılır**: Bir VM 'de uygulamalarınız için tutarlı kapsayıcı dağıtımları oluşturmak istediğinizde veya [Docker Compose](https://docs.docker.com/compose/overview/)kullanmak istediğinizde.
  > 
  > **Kullanmaya**başlayın: [DOCKER VM uzantısını kullanarak Azure 'da bir Docker ortamı oluşturun](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Azure Container Service**: Kapsayıcılı uygulamaları çalıştırmak için önceden yapılandırılmış bir sanal makine kümesi oluşturmanıza, yapılandırmanıza ve yönetmenize olanak sağlar. Kapsayıcı hizmeti hakkında daha fazla bilgi edinmek için bkz. [Azure Container Service giriş](../../container-service/container-service-intro.md).

  > **Ne zaman kullanılır**: Ek zamanlama ve yönetim araçları sağlayan veya bir Docker Sısınma kümesi dağıttığınız durumlarda üretime hazırlamış, ölçeklenebilir ortamlar oluşturmanız gerektiğinde.
  > 
  > **Kullanmaya**başlayın: [Bir kapsayıcı hizmeti kümesi dağıtın](../../container-service/dcos-swarm/container-service-deployment.md).

- **Docker makinesi**: Docker-Machine komutlarını kullanarak sanal konaklara bir Docker altyapısı yüklemenize ve yönetmenize olanak sağlar.

  >**Ne zaman kullanılır**: Tek bir Docker Konağı oluşturarak hızlı bir şekilde uygulama prototipi oluşturmanız gerektiğinde.

- **App Service Için özel Docker görüntüsü**: Linux üzerinde bir Web uygulaması dağıtırken bir kapsayıcı kayıt defterinden veya bir müşteri kapsayıcısından Docker Kapsayıcıları kullanmanıza olanak sağlar.

  > **Ne zaman kullanılır**: Linux üzerinde bir Web uygulamasını Docker görüntüsüne dağıtmaya çalışırken.
  > 
  > **Kullanmaya**başlayın: [Linux üzerinde App Service için özel bir Docker görüntüsü kullanın](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

Yalnızca uygulamalarınızı kimin kullandığını ve ayrıca Kaynaklarınıza yetkisiz erişimi engellemek için çok önemlidir. Azure, uygulama istemcilerinizin kimliğini doğrulamak için çeşitli yollar sağlar.

- **Azure Active Directory (Azure AD)** : Microsoft Multitenant, bulut tabanlı kimlik ve erişim yönetimi hizmeti. Azure AD ile tümleştirerek uygulamalarınıza çoklu oturum açma (SSO) ekleyebilirsiniz. Azure AD Graph API doğrudan veya Microsoft Graph API 'sini kullanarak dizin özelliklerine erişebilirsiniz. OAuth 2.0 yetkilendirme çerçevesi için Azure AD desteğiyle ve yerel HTTP/REST uç noktaları ve çok platformlu Azure AD kimlik doğrulama kitaplıklarını kullanarak Open ID Connect ile tümleştirilebilir.

  > **Ne zaman kullanılır**: Bir SSO deneyimi sağlamak istediğinizde, Graph tabanlı verilerle çalışın veya etki alanı tabanlı kullanıcıların kimliğini doğrulayın.
  > 
  > **Kullanmaya**başlayın: Daha fazla bilgi için, [Azure Active Directory Geliştirici Kılavuzu](../../active-directory/develop/v1-overview.md)' na bakın.

- **App Service kimlik doğrulaması**: Uygulamanızı barındırmak için App Service ' ı seçtiğinizde Facebook, Google, Microsoft ve Twitter gibi sosyal kimlik sağlayıcılarının yanı sıra Azure AD için de yerleşik kimlik doğrulama desteği alırsınız.

  > **Ne zaman kullanılır**: Azure AD, sosyal kimlik sağlayıcıları veya her ikisini de kullanarak App Service bir uygulamada kimlik doğrulamasını etkinleştirmek istediğinizde.
  > 
  > **Kullanmaya**başlayın: App Service kimlik doğrulaması hakkında daha fazla bilgi için, bkz. [Azure App Service kimlik doğrulama ve yetkilendirme](../../app-service/overview-authentication-authorization.md).

Azure 'da en iyi güvenlik uygulamaları hakkında daha fazla bilgi edinmek için bkz. [Azure Güvenlik en iyi uygulamaları ve desenleri](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>İzleme

Uygulamanız Azure 'da çalışır ve çalışır durumda olduğunda, performansı izleyebilir, sorunları izleyebilir ve müşterilerin uygulamanızı nasıl kullandığını görebilirsiniz. Azure çeşitli izleme seçenekleri sağlar.

-   **Visual Studio Application Insights**: Canlı Web uygulamalarınızı izlemek üzere Visual Studio ile tümleşen bir Azure barındırılan Genişletilebilir analiz hizmeti. Azure 'da barındırılıp barındırılmadığını ve uygulamalarınızın performansını ve kullanılabilirliğini sürekli olarak geliştirmek için ihtiyacınız olan verileri sağlar.

    >**Kullanmaya**başlayın: [Application Insights öğreticisini](../../azure-monitor/app/app-insights-overview.md)izleyin.

-   **Azure izleyici**: Azure altyapınız ve kaynaklarınız tarafından oluşturulan ölçümleri ve günlükleri görselleştirmenize, sorgulayabilir, yönlendirmenize, arşivlemenize ve bunlarla işlem yapmanıza yardımcı olan bir hizmettir. İzleyici, Azure portal gördüğünüz veri görünümlerini sağlar ve Azure kaynaklarını izlemek için tek bir kaynaktır.
 
    >**Kullanmaya**başlayın: [Azure izleyici 'yi kullanmaya](../../monitoring-and-diagnostics/monitoring-get-started.md)başlayın.

### <a name="devops-integration"></a>DevOps tümleştirmesi

Azure 'un, sürekli tümleştirme ile VM 'Ler sağlayıp sağlamasının veya Web uygulamalarınızı yayımlamasından bağımsız olarak, Azure popüler DevOps araçlarının çoğuyla tümleştirilir. Jenkins, GitHub, Pupevcil hayvan, Chef, TeamCity, Anerişilebilir, Azure DevOps ve diğerleri gibi araçlara yönelik destek sayesinde, zaten sahip olduğunuz araçlarla çalışabilir ve var olan deneyiminizi en üst düzeye çıkarabilirsiniz.

> **Şimdi deneyin:** [Çeşitli DevOps tümleştirmelerini deneyin](https://azure.microsoft.com/try/devops/).
> 
> **Kullanmaya**başlayın: App Service bir uygulamayla ilgili DevOps seçeneklerini görmek için bkz. [Azure App Service Için sürekli dağıtım](../../app-service/deploy-continuous-deployment.md).


## <a name="azure-regions"></a>Azure bölgeleri

Azure, dünyanın dört bir yanındaki birçok bölgede genel olarak kullanılabilen küresel bir bulut platformudur. Azure 'da bir hizmet, uygulama veya VM sağladığınızda, uygulamanızın çalıştığı veya verilerinizin depolandığı belirli bir veri merkezini temsil eden bir bölge seçmeniz istenir. Bu bölgeler, [Azure bölgeleri](https://azure.microsoft.com/regions/) sayfasında yayınlanan belirli konumlara karşılık gelir.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Uygulamanız ve verileriniz için en iyi bölgeyi seçin

Azure kullanmanın avantajlarından biri, uygulamalarınızı dünyanın dört bir yanındaki çeşitli veri merkezlerinde dağıtmanıza olanak sağlar. Seçtiğiniz bölge, uygulamanızın performansını etkileyebilir. Örneğin, ağ isteklerindeki gecikmeyi azaltmak için müşterilerinizin çoğuna yakın bir bölge seçmek daha iyidir. Ayrıca, belirli ülkelerde/bölgelerde uygulamanızı dağıtmaya yönelik yasal gereksinimleri karşılamak için bölgenizi seçmeniz de gerekebilir. Uygulama verilerini, uygulamanızı barındıran veri merkezinde mümkün olduğunca yakın bir şekilde depolamak için her zaman en iyi uygulamadır.

### <a name="multi-region-apps"></a>Çok bölgeli uygulamalar

Büyük olasılıkla, doğal bir olağanüstü durum veya Internet arızası gibi bir olay nedeniyle tüm veri merkezinin çevrimdışı olması olanaksız olur. En yüksek kullanılabilirlik sağlamak için önemli iş uygulamalarını birden fazla veri merkezinde barındırmak en iyi uygulamadır. Aynı zamanda birden çok bölge kullanılması, genel kullanıcılar için gecikme süresini azaltabilir ve uygulamalar güncelleştirilirken esneklik için ek fırsatlar sağlayabilir.

Sanal makine ve uygulama hizmetleri gibi bazı hizmetler, yüksek kullanılabilirliğe sahip kurumsal uygulamaları desteklemek için bölgeler arasında yük devretmeyle çoklu bölge desteğini etkinleştirmek üzere [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) kullanır. Bir örnek için bkz [. Azure başvuru mimarisi: Bir Web uygulamasını birden çok bölgede](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)çalıştırın.

>**Ne zaman kullanılır**: Yük devretme ve çoğaltmadan faydalanabilir kurumsal ve yüksek kullanılabilirliğe sahip uygulamalarınız olduğunda.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Uygulamalarımı ve projelerimi yönetmek Nasıl yaparım??

Azure, Azure kaynaklarınızı, uygulamalarınızı ve projelerinizi hem programlı olarak hem de [Azure Portal](https://portal.azure.com/)oluşturmanız ve yönetmeniz için zengin bir deneyim kümesi sağlar.

### <a name="command-line-interfaces-and-powershell"></a>Komut satırı arabirimleri ve PowerShell

Azure, Bash, Terminal, komut istemi veya tercih ettiğiniz komut satırı aracını kullanarak uygulamalarınızı ve hizmetlerinizi komut satırından yönetmenin iki yolunu sağlar. Genellikle, sanal makineleri, sanal ağları, Web uygulamalarını ve diğer hizmetleri oluşturma ve yapılandırma gibi Azure portal, komut satırından aynı görevleri gerçekleştirebilirsiniz.

-   [Azure komut satırı arabirimi (CLI)](../../xplat-cli-install.md): Azure aboneliğine bağlanmanızı ve komut satırından Azure kaynaklarına yönelik çeşitli görevleri programlamanızı sağlar.

-   [Azure PowerShell](../../powershell-install-configure.md): , Windows PowerShell kullanarak Azure kaynaklarını yönetmenizi sağlayan cmdlet 'ler içeren bir modül kümesi sağlar.

### <a name="azure-portal"></a>Azure portal

Azure portal, Azure kaynakları ve hizmetleri oluşturmak, yönetmek ve kaldırmak için kullanabileceğiniz web tabanlı bir uygulamadır. Azure portal konumunda <https://portal.azure.com>bulunur. Bu, özelleştirilebilir bir Pano, Azure kaynaklarını yönetmeye yönelik araçlar ve abonelik ayarlarına ve faturalandırma bilgilerine erişim içerir. Daha fazla bilgi için [Azure Portal genel bakış](../../azure-portal-overview.md)bölümüne bakın.

### <a name="rest-apis"></a>REST API'leri

Azure, Azure portal Kullanıcı arabirimini destekleyen bir REST API kümesi üzerine kurulmuştur. Bu REST API 'lerinin çoğu, Azure kaynaklarınızı ve uygulamalarınızı herhangi bir Internet etkin cihazdan programlı bir şekilde sağlamanıza ve yönetmenize olanak sağlamak için de desteklenir. REST API belgelerinin tamamı için bkz. [Azure Rest SDK başvurusu](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>API'ler

REST API 'Lerine ek olarak, birçok Azure hizmeti Ayrıca, aşağıdaki geliştirme platformları için SDK 'lar dahil olmak üzere platforma özgü Azure SDK 'larını kullanarak uygulamalarınızdan kaynakları programlı bir şekilde yönetmenizi sağlar:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

[Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) ve [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) gibi hizmetler, Web ve mobil istemci uygulamalarından hizmetlere erişmenizi sağlayan istemci tarafı SDK 'ları sağlar.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Uygulamanızı Azure 'da çalıştırmak, hepsi aynı yaşam döngüsünü izleyen ve mantıksal birim olarak düşünülebilir birden çok Azure hizmeti ile çalışmayı içerir. Örneğin, bir Web uygulaması Web Apps, SQL veritabanı, depolama, Redsıs için Azure önbelleği ve Azure Content Delivery Network hizmetleri kullanabilir. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) , uygulamanızdaki kaynaklarla bir grup olarak çalışmanıza olanak sağlar. Tüm kaynakları tek ve eşgüdümlü bir işlemle dağıtabilir, güncelleştirebilir veya silebilirsiniz.

Azure Resource Manager, ilgili kaynakları mantıksal olarak gruplandırmanıza ve yönetmeye ek olarak, ilgili kaynakların dağıtımını ve yapılandırmasını özelleştirmenize olanak sağlayan dağıtım özelliklerini içerir. Örneğin, Kaynak Yöneticisi kullanarak birden çok sanal makineden, yük dengeleyiciden ve Azure SQL veritabanından tek bir birim olarak oluşan bir uygulama dağıtabilir ve yapılandırabilirsiniz.

Bu dağıtımları, JSON biçimli bir belge olan Azure Resource Manager şablonu kullanarak geliştirirsiniz. Şablonlar, bir dağıtım tanımlamanıza ve komut dosyaları yerine bildirim temelli şablonlar kullanarak uygulamalarınızı yönetmenize olanak sağlar. Şablonlarınız, test, hazırlama ve üretim gibi farklı ortamlarda çalışabilir. Örneğin, şablonları kullanarak, depodaki kodu bir Azure hizmetleri kümesine dağıtan bir GitHub deposuna tek bir tıklama ile bir düğme ekleyebilirsiniz.

> **Ne zaman kullanılır**: Uygulamanız için, REST API 'Leri, Azure CLı ve Azure PowerShell kullanarak programlı bir şekilde yönetebileceğiniz şablon tabanlı bir dağıtım istediğinizde Kaynak Yöneticisi şablonları kullanın.
> 
> **Kullanmaya**başlayın: Şablonları kullanmaya başlamak için bkz. [Azure Resource Manager şablonları yazma](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Hesapları, abonelikleri ve Faturalandırmayı anlama

Geliştiriciler olarak, koda doğru bir şekilde başlamak ve uygulamalarımızın çalışmasını sağlamak için mümkün olduğunca hızlı bir şekilde çalışmaya başlamak istiyoruz. Azure 'da mümkün olduğunca kolay çalışmaya başlamasını kesinlikle öneririz. Azure, çok daha kolay hale getirmenize yardımcı olmak için [ücretsiz bir deneme](https://azure.microsoft.com/free/)sunar. Ayrıca, bazı hizmetlerde bir "ücretsiz olarak dene" işlevselliğine sahip olan, hatta bir hesap oluşturmanızı gerektirmeyen [Azure App Service](https://tryappservice.azure.com/)gibi işlevler de vardır. Kodlanması ve uygulamanızı Azure 'a dağıtmaya kadar eğlenceye kadar, Azure 'un Kullanıcı hesapları, abonelikler ve Faturalandırma açısından nasıl çalıştığını anlamak için biraz zaman yapmanız da önemlidir.

### <a name="what-is-an-azure-account"></a>Azure hesabı nedir?

Bir Azure aboneliği oluşturabilmek veya bunlarla çalışabilmek için bir Azure hesabınızın olması gerekir. Azure hesabı, Azure AD 'de veya Azure AD tarafından güvenilen bir iş veya okul organizasyonu gibi bir dizinde bulunan bir kimliktir. Böyle bir kuruluşa ait değilseniz, Azure AD tarafından güvenilen Microsoft hesabınızı kullanarak her zaman bir abonelik oluşturabilirsiniz. Şirket içi Windows Server Active Directory Azure AD ile tümleştirme hakkında daha fazla bilgi için bkz. Şirket [içi kimliklerinizi Azure Active Directory Ile tümleştirme](../../active-directory/hybrid/whatis-hybrid-identity.md).

Her Azure aboneliği bir Azure AD örneğiyle güven ilişkisine sahiptir. Bu; Azure aboneliğinin kullanıcılar, hizmetler ve cihazlar için kimlik doğrulaması yapmak üzere bu dizine güvendiği anlamına gelir. Birden çok abonelik aynı dizine güvenebilir ancak bir abonelik yalnızca bir dizine güvenir. Daha fazla bilgi edinmek için bkz. [Azure aboneliklerinin Azure Active Directory ile ilişkilendirilmesi](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Ayrıca, *Kullanıcılar*olarak da bilinen tek Azure hesap kimliklerini tanımlamaya ek olarak, Azure AD 'de *grupları* da tanımlayabilirsiniz. Kullanıcı gruplarının oluşturulması, rol tabanlı erişim denetimi (RBAC) kullanarak bir abonelikteki kaynaklara erişimi yönetmenin iyi bir yoludur. Grup oluşturma hakkında bilgi edinmek için bkz. [Azure Active Directory Preview 'da Grup oluşturma](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Ayrıca, [PowerShell kullanarak](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md)gruplar oluşturabilir ve yönetebilirsiniz.

### <a name="manage-your-subscriptions"></a>Aboneliklerinizi yönetin

Abonelik, Azure hesabına bağlı Azure hizmetlerinin mantıksal gruplandırmasıdır. Tek bir Azure hesabı, birden çok abonelik içerebilir. Azure hizmetleri için faturalandırma, abonelik başına esasına göre yapılır. Türe göre kullanılabilir abonelik tekliflerinin bir listesi için, [Microsoft Azure teklif ayrıntıları](https://azure.microsoft.com/support/legal/offer-details/)' na bakın. Azure aboneliklerinde, abonelik üzerinde tam denetim sahibi olan ve abonelik içindeki tüm hizmetler üzerinde denetim sahibi olan bir hizmet yöneticisi olan bir hesap yöneticisi vardır. Klasik abonelik yöneticileri hakkında daha fazla bilgi için bkz. [Azure abonelik yöneticileri ekleme veya değiştirme](../../billing/billing-add-change-azure-subscription-administrator.md). Yöneticilere ek olarak, bireysel hesaplara [rol tabanlı erişim denetimi (RBAC)](../../role-based-access-control/overview.md)kullanılarak Azure kaynakları için ayrıntılı denetim verilebilir.

#### <a name="resource-groups"></a>Kaynak grupları

Yeni Azure hizmetleri sağladığınızda, bunu belirli bir abonelikte yapabilirsiniz. Kaynaklar olarak da bilinen ayrı Azure Hizmetleri, bir kaynak grubu bağlamında oluşturulur. Kaynak grupları uygulamanızın kaynaklarını dağıtmayı ve yönetmeyi kolaylaştırır. Kaynak grubu, bir birim olarak birlikte çalışmak istediğiniz uygulamanızın tüm kaynaklarını içermelidir. Kaynakları kaynak grupları arasında ve hatta farklı aboneliklere taşıyabilirsiniz. Kaynakları taşıma hakkında bilgi edinmek için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../../resource-group-move-resources.md).

Azure Kaynak Gezgini, aboneliğinizde önceden oluşturduğunuz kaynakları görselleştirmeye yönelik harika bir araçtır. Daha fazla bilgi için bkz. [kaynakları görüntülemek ve değiştirmek için Azure Kaynak Gezgini kullanma](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Kaynaklara erişim izni verme

Azure kaynaklarına erişime izin verildiğinde, kullanıcılara belirli bir görevi gerçekleştirmek için gereken en az ayrıcalığı sağlamak her zaman en iyi uygulamadır.

- **Rol tabanlı erişim denetimi (RBAC)** : Azure 'da, belirli bir kapsamdaki Kullanıcı hesaplarına (sorumlular) erişim izni verebilirsiniz: abonelik, kaynak grubu veya ayrı kaynaklar. RBAC, bir kaynak grubuna bir kaynak kümesi dağıtmanıza ve belirli bir kullanıcı veya gruba izinler atamanıza olanak tanır. Ayrıca, erişimi yalnızca hedef kaynak grubuna ait kaynaklarla sınırlandırmanıza da olanak tanır. Ayrıca, sanal makine veya sanal ağ gibi tek bir kaynağa erişim izni verebilirsiniz. Erişim vermek için Kullanıcı, Grup veya hizmet sorumlusuna bir rol atarsınız. Önceden tanımlanmış birçok rol vardır ve kendi özel rollerinizi de tanımlayabilirsiniz. Daha fazla bilgi edinmek için bkz. [rol tabanlı erişim denetimi (RBAC) nedir?](../../role-based-access-control/overview.md).

  > **Ne zaman kullanılır**: Kullanıcılar ve gruplar için ayrıntılı erişim yönetimine ihtiyacınız olduğunda veya bir kullanıcıya abonelik sahibi yapmanız gerektiğinde.
  > 
  > **Kullanmaya**başlayın: Daha fazla bilgi için bkz. [RBAC kullanarak erişimi yönetme ve Azure Portal](../../role-based-access-control/role-assignments-portal.md).

- **Hizmet sorumlusu nesneleri**: Kullanıcı sorumlularına ve gruplarına erişim sağlamanın yanı sıra, hizmet sorumlusu için de aynı erişimi verebilirsiniz.

  > **Ne zaman kullanılır**: Azure kaynaklarını program aracılığıyla yönetirken veya uygulamalar için erişim verirken. Daha fazla bilgi için bkz. [Active Directory uygulama ve hizmet sorumlusu oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Tags

Azure Resource Manager, tek tek kaynaklara özel Etiketler atamanıza olanak tanır. Anahtar-değer çiftleri olan Etiketler faturalandırma veya izleme için kaynakları düzenlemeniz gerektiğinde yararlı olabilir. Etiketler, kaynakları birden çok kaynak grubunda izlemek için bir yol sağlar. Portala, Azure Resource Manager şablonunda veya program aracılığıyla REST API, Azure CLı veya PowerShell kullanarak etiketler atayabilirsiniz. Her kaynağa birden çok etiket atayabilirsiniz. Daha fazla bilgi edinmek için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../../resource-group-using-tags.md).

### <a name="billing"></a>Faturalandırma

Şirket içi bilgi işlemden bulutta barındırılan hizmetlere geçiş yaparken, hizmet kullanımını ve ilgili maliyetleri izlemek ve tahmin etmek önemli kaygılardır. Aylık olarak hangi yeni kaynakların çalışacağını tahmin etmek önemlidir. Ayrıca, geçerli harcama temelinde faturalandırmanın belirli bir aya nasıl göründüğünü de proje için de oluşturmanız gerekir.

#### <a name="get-resource-usage-data"></a>Kaynak kullanım verilerini al

Azure, Azure aboneliklerine yönelik kaynak tüketimine ve meta veri bilgilerine erişim veren bir Faturalandırma REST API 'si sağlar. Bu Faturalama API'leri, Azure maliyetlerini daha iyi tahmin etme ve yönetme olanağı sunar. Harcamayı saatlik artışlarla izleyip analiz edebilir, harcama uyarıları oluşturabilir ve geçerli kullanım eğilimlerini temelinde gelecekte faturalandırmayı tahmin edebilirsiniz.

>**Kullanmaya**başlayın: Faturalama API'leri kullanımı hakkında daha fazla bilgi edinmek için bkz. [Azure Faturalandırma kullanımı ve RateCard API 'lerine genel bakış](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Gelecekteki maliyetleri tahmin etme

Maliyetleri önceden tahmin etmek zor olsa da, Azure 'un dağıtılan kaynakların maliyetini tahmin ettiğinizde kullanabileceğiniz bir [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/) vardır. Ayrıca, geçerli tüketimine göre gelecekteki maliyetleri tahmin etmek için portalda faturalandırma dikey penceresini ve Faturalandırma REST API 'Lerini de kullanabilirsiniz.

>**Kullanmaya**başlayın: Bkz. [Azure Faturalandırma kullanımı ve RateCard API 'lerine genel bakış](../../billing-usage-rate-card-overview.md).
