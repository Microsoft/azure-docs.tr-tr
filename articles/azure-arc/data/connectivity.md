---
title: Bağlantı modları ve gereksinimler
description: Ortamınızdaki Azure 'a yönelik Azure Arc etkin veri Hizmetleri bağlantı seçeneklerini açıklar
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d148509af45b93dce8dbd99b9afc674276b149b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493981"
---
# <a name="connectivity-modes-and-requirements"></a>Bağlantı modları ve gereksinimler

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>Bağlantı modları

Azure Arc etkin veri Hizmetleri ortamınızdan Azure 'a bağlantı derecesi için birden çok seçenek vardır. Gereksinimleriniz iş ilkesi, kamu mevzuata veya Azure ile ağ bağlantısının kullanılabilirliğine göre farklılık gösterdiğinden, aşağıdaki bağlantı modlarında seçim yapabilirsiniz.

Azure Arc etkin veri Hizmetleri, Azure 'a iki farklı *bağlantı modunda* bağlanma seçeneği sunar: 

- Doğrudan bağlı 
- Dolaylı olarak bağlı

Bağlantı modu, Azure 'a ne kadar veri gönderileceğini ve kullanıcıların Arc veri denetleyicisiyle nasıl etkileşime gireceğini seçme esnekliği sağlar. Seçilen bağlantı moduna bağlı olarak, Azure Arc etkin veri Hizmetleri 'nin bazı işlevleri kullanılamayabilir veya bulunmayabilir.

Daha da, Azure Arc etkin veri Hizmetleri doğrudan Azure 'a bağlıysa, kullanıcılar [Azure Resource Manager API 'leri](/rest/api/resources/), Azure CLI ve Azure Arc veri hizmetlerini çalıştırmak için Azure Portal kullanabilir. Doğrudan bağlantılı moddaki deneyim, Azure portal sağlama/kaldırma, ölçeklendirme, yapılandırma vb. ile diğer Azure hizmetini nasıl kullanacağınız konusunda çok benzer.  Azure Arc etkin veri Hizmetleri dolaylı olarak Azure 'a bağlandıysa Azure portal salt okunurdur. Dağıtılan SQL yönetilen örneklerinin ve Postgres hiper ölçek örneklerinin envanterini ve bunlarla ilgili ayrıntıları görebilirsiniz, ancak Azure portal üzerinde işlem yapmanız gerekmez.  Dolaylı olarak bağlı modda tüm eylemlerin, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] kubectl gibi Azure Data Studio, veya Kubernetes yerel araçları kullanılarak yerel olarak alınması gerekir.

Ayrıca, Azure Active Directory ve Azure Role-Based Access Control, bu işlevselliği sağlamak üzere sürekli olarak ve Azure 'a doğrudan bağlantı için bir bağımlılık olduğundan doğrudan bağlı modda kullanılabilir.

Azure 'a bağlı bazı hizmetler yalnızca Azure Defender güvenlik hizmetleri, kapsayıcı öngörüleri ve BLOB depolamaya Azure Backup gibi doğrudan ulaşılabilecekleri durumlarda kullanılabilir.

||**Dolaylı olarak bağlı**|**Doğrudan bağlı**|**Hiç bağlanmadı**|
|---|---|---|---|
|**Açıklama**|Dolaylı olarak bağlı modu, yönetim hizmetlerinin çoğunu ortamınızda yerel olarak Azure 'a doğrudan bağlantı olmadan sunar.  _Yalnızca_ envanter ve Faturalama amacıyla Azure 'a en az miktarda veri gönderilmesi gerekir. Bir dosyaya aktarılmışsa ve ayda en az bir kez Azure 'a yüklenir.  Azure 'a doğrudan veya sürekli bağlantı gerekmez.  Azure bağlantısı gerektiren bazı özellikler ve hizmetler kullanılabilir olmayacaktır.|Doğrudan bağlı modu, Azure ile doğrudan bir bağlantı kurulamazsa tüm kullanılabilir hizmetleri sunar. Bağlantılar her zaman _ortamınızdan Azure 'a başlatılır ve_ https/443 gibi standart bağlantı noktalarını ve protokolleri kullanır.|Herhangi bir şekilde Azure 'a veya Azure 'dan veri gönderilemez.|
|**Geçerli kullanılabilirlik**| Önizlemede kullanılabilir.|Önizlemede kullanılabilir.|Şu anda desteklenmiyor.|
|**Tipik kullanım örnekleri**|Şirket içi veri merkezleri iş veya mevzuata uygunluk ilkeleri veya dış saldırılardan ya da veri taşmalarına yönelik bir sorun nedeniyle veri merkezinin veri bölgesi içinde veya dışında bağlantı kurulmasına izin vermez.  Tipik örnekler: finans kurumları, Sağlık Hizmetleri, kamu. <br/><br/>Sınır sitesinin genellikle Internet bağlantısı olmayan sınır sitesi konumları.  Tipik örnekler: yağ/gaz veya askeri alan uygulamaları.  <br/><br/>Uzun süreli kesintilerle aralıklı bağlantı içeren uç site konumları.  Tipik örnekler: Stadiums, CRUISE. | Ortak bulutlar kullanan kuruluşlar.  Tipik örnekler: Azure, AWS veya Google bulutu.<br/><br/>Internet bağlantısının genellikle bulunduğu ve izin verilen uç site konumları.  Tipik örnekler: perakende mağaza, üretim.<br/><br/>Şirket veri merkezleri, veri merkezin veri bölgelerine/verilerine Internet 'e bağlantı için daha fazla izin veren ilkeler sağlar.  Tipik örnekler: düzenlenmiş olmayan işletmeler, küçük/orta ölçekli işletmeler|Gerçek anlamda "AIR-gapped" ortamları, herhangi bir koşulda veri ortamına ulaşmaz veya veri ortamından gidebilirler. Tipik örnekler: popüler gizli kamu olanakları.|
|**Azure 'a veri gönderme**|Faturalama ve envanter verilerinin Azure 'a nasıl gönderilebir konusunda üç seçenek vardır:<br><br> 1) veriler, hem güvenli veri bölgesi hem de Azure bağlantısı olan otomatik bir işlem tarafından veri bölgesinden dışarı aktarılabilir.<br><br>2) veriler veri bölgesi içindeki otomatik bir işlem tarafından veri bölgesinden dışarı aktarıldığından, otomatik olarak daha az güvenli bir bölgeye kopyalanır ve daha az güvenli bölgedeki otomatik bir işlem verileri Azure 'a yükler.<br><br>3) veriler güvenli bölge içindeki bir kullanıcı tarafından el ile dışarı aktarılmışsa, güvenli bölgenin el ile kullanıma sunulmasıdır ve Azure 'a el ile yüklenir. <br><br>İlk iki seçenek, sık çalıştırılmak üzere zamanlanabilen otomatik bir sürekli işlemdir. bu sayede, verilerin Azure 'a yalnızca Azure 'a yönelik bağlantı ile aktarılması konusunda en az gecikme vardır.|Veriler otomatik olarak ve sürekli olarak Azure 'a gönderilir.|Veriler hiçbir şekilde Azure 'a gönderilmez.|

## <a name="feature-availability-by-connectivity-mode"></a>Bağlantı moduna göre özellik kullanılabilirliği

|**Özellik**|**Dolaylı olarak bağlı**|**Doğrudan bağlı**|
|---|---|---|
|**Otomatik yüksek kullanılabilirlik**|Desteklenir|Desteklenir|
|**Self Servis sağlama**|Desteklenir<br/>Oluşturma, Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] veya Kubernetes yerel araçları (Held, kubectl, OC, vb.) aracılığıyla veya Azure Arc etkinleştirilmiş Kubernetes Gilar sağlaması kullanılarak yapılabilir.|Desteklenir<br/>Dolaylı olarak bağlı mod oluşturma seçeneklerine ek olarak, Azure portal, Azure Resource Manager API 'Leri, Azure CLı veya ARM şablonları aracılığıyla da oluşturabilirsiniz. **Doğrudan bağlantı modunun bekleyen kullanılabilirliği**
|**Elastik ölçeklenebilirlik**|Desteklenir|Desteklenir<br/>**Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**Faturalandırma**|Desteklenir<br/>Faturalama verileri düzenli aralıklarla dışarı aktarılabilir ve Azure 'a gönderilir.|Desteklenir<br/>Faturalama verileri otomatik olarak ve sürekli olarak Azure 'a gönderilir ve neredeyse gerçek zamanlı olarak yansıtılır. **Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**Envanter yönetimi**|Desteklenir<br/>Envanter verileri düzenli aralıklarla dışarı aktarılabilir ve Azure 'a gönderilir.<br/><br/>Azure Data Studio, Azure Data CLı gibi istemci araçlarını veya `kubectl` yerel olarak envanteri görüntülemek ve yönetmek için kullanın.|Desteklenir<br/>Envanter verileri otomatik olarak ve sürekli olarak Azure 'a gönderilir ve neredeyse gerçek zamanlı olarak yansıtılır. Bu nedenle, stoku doğrudan Azure portal yönetebilirsiniz. **Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**Otomatik yükseltmeler ve düzeltme eki uygulama**|Desteklenir<br/>Veri denetleyicisinin Microsoft Container Registry (MCR) için doğrudan erişimi olmalıdır veya kapsayıcı görüntülerinin MCR 'den çekilmesi ve veri denetleyicisinin erişimi olan yerel, özel bir kapsayıcı kayıt defterine itilmesi gerekir.|Desteklenir<br/>**Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**Otomatik yedekleme ve geri yükleme**|Desteklenir<br/>Otomatik yerel yedekleme ve geri yükleme.|Desteklenir<br/>Otomatik yerel yedekleme ve geri yükleme özelliklerine ek olarak, uzun süreli, site dışı saklama için Azure Backup yedeklemeleri _isteğe bağlı_ olarak gönderebilirsiniz. **Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**İzleme**|Desteklenir<br/>Grafana ve kibana panoları kullanarak yerel izleme.|Desteklenir<br/>Yerel izleme panolarına ek olarak, birden çok sitenin ölçeğini tek bir yerde izlemek için _isteğe bağlı olarak_ Azure izleyici 'ye izleme verileri ve Günlükler gönderebilirsiniz. **Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**Kimlik Doğrulaması**|Veri denetleyicisi ve Pano kimlik doğrulaması için yerel kullanıcı adını/parolayı kullanın. SQL ve Postgres oturum açmaları veya veritabanı örneklerine bağlantı için Active Directory kullanın.  Kubernetes API 'sine kimlik doğrulaması için K8s kimlik doğrulama sağlayıcılarını kullanın.|Dolaylı olarak bağlı mod için kimlik doğrulama yöntemlerinin yanı sıra, _isteğe bağlı_ olarak Azure Active Directory de kullanabilirsiniz. **Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**Rol tabanlı erişim denetimi (RBAC)**|Kubernetes API üzerinde Kubernetes RBAC kullanın. Veritabanı örnekleri için SQL ve Postgres RBAC kullanın.|İsteğe bağlı olarak Azure Active Directory ve Azure RBAC ile tümleştirilebilir. **Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**Azure Defender**|Desteklenmez|Geleceğe göre planlanmış|

## <a name="connectivity-requirements"></a>Bağlantı gereksinimleri

**Bazı işlevlerin Azure bağlantısı olması gerekir.**

**Azure ile olan tüm iletişimler her zaman ortamınızdan başlatılır.** Bu, Azure portal bir kullanıcı tarafından başlatılan işlemler için de geçerlidir.  Bu durumda, Azure 'da kuyruğa alınan etkin bir görev vardır.  Ortamınızdaki bir aracı, kuyruktaki görevlerin hangileri olduğunu görmek için Azure ile iletişimi başlatır, görevleri çalıştırır ve durumu/tamamlamayı geri bildirir ve Azure 'a başarısız olur.

|**Veri türü**|**Yön**|**Gerekli/İsteğe Bağlı**|**Ek maliyetler**|**Mod gerekli**|**Notlar**|
|---|---|---|---|---|---|
|**Kapsayıcı görüntüleri**|Microsoft Container Registry-> müşteri|Gerekli|No|Dolaylı veya doğrudan|Kapsayıcı görüntüleri, yazılım dağıtma yöntemidir.  Internet üzerinden Microsoft Container Registry (MCR) ile bağlanabilecek bir ortamda, kapsayıcı görüntüleri doğrudan MCR 'den çeklenebilir.  Dağıtım ortamının doğrudan bağlantısı olmaması durumunda, görüntüleri MCR konumundan çekerek dağıtım ortamındaki özel bir kapsayıcı kayıt defterine gönderebilirsiniz.  Oluşturma sırasında, oluşturma işlemini MCR yerine özel kapsayıcı kayıt defterinden çekmek üzere yapılandırabilirsiniz. Bu, otomatik güncelleştirmeler için de geçerlidir.|
|**Kaynak envanteri**|Müşteri ortamı-Azure >|Gerekli|No|Dolaylı veya doğrudan|Veri denetleyicileri, veritabanı örnekleri (PostgreSQL ve SQL) envanterinin faturalandırılması amacıyla Azure 'da ve aynı zamanda Azure Arc veri Hizmetleri ile birden fazla ortamınız olduğunda yararlı olan tek bir yerde tüm veri denetleyicileri ve veritabanı örneklerinin envanterini oluşturma amacıyla Azure 'da tutulur.  Örneklerin sağlanması, kullanıma sunulmasının, ölçeklendirilmesi ve ölçeği azaltıldığı için, envanterin ölçeği Azure 'da güncelleştirilir.|
|**Faturalandırma telemetri verileri**|Müşteri ortamı-Azure >|Gerekli|No|Dolaylı veya doğrudan|Faturalama amacıyla veritabanı örneklerinin kullanımının Azure 'a gönderilmesi gerekir.  Önizleme döneminde Azure Arc etkin veri Hizmetleri için maliyet yoktur.|
|**Verileri ve günlükleri izleme**|Müşteri ortamı-Azure >|İsteğe Bağlı|Veri hacmine bağlı olabilir (bkz. [Azure izleyici fiyatlandırması](https://azure.microsoft.com/en-us/pricing/details/monitor/))|Dolaylı veya doğrudan|Birden çok ortamda verileri tek bir yerde toplamak ve ayrıca Azure Machine Learning, vb. ' deki verileri kullanarak uyarılar gibi Azure Izleyici hizmetlerini kullanmak için, yerel olarak toplanan izleme verilerini ve günlükleri Azure Izleyici 'ye göndermek isteyebilirsiniz.|
|**Azure rol tabanlı Access Control (Azure RBAC)**|Müşteri ortamı-> Azure > müşteri ortamı|İsteğe Bağlı|No|Yalnızca doğrudan|Azure RBAC kullanmak istiyorsanız, her zaman Azure ile bağlantı kurulması gerekir.  Azure RBAC kullanmak istemiyorsanız, yerel Kubernetes RBAC kullanılabilir.  **Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**Azure Active Directory (AD)**|Müşteri ortamı-> Azure > müşteri ortamı|İsteğe Bağlı|Belki de zaten Azure AD için ödeme yapabilirsiniz|Yalnızca doğrudan|Kimlik doğrulaması için Azure AD 'yi kullanmak istiyorsanız, her zaman Azure ile bağlantı kurulması gerekir. Kimlik doğrulaması için Azure AD 'yi kullanmak istemiyorsanız, Active Directory üzerinden ABD Active Directory Federasyon Hizmetleri (AD FS) (ADFS) kullanabilirsiniz. **Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**Yedekleme ve geri yükleme**|Müşteri ortamı-> müşteri ortamı|Gerekli|No|Doğrudan veya dolaylı|Yedekleme ve geri yükleme hizmeti yerel depolama sınıflarına işaret etmek üzere yapılandırılabilir. |
|**Azure Backup-uzun süreli saklama**| Müşteri ortamı-Azure > | İsteğe Bağlı| Azure depolama için Evet | Yalnızca doğrudan |Yedeklemeleri uzun süreli, site dışı bekletmede Azure Backup için yerel olarak alınmış yedeklemeler göndermek ve geri yükleme için bunları yerel ortama geri getirmek isteyebilirsiniz. **Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**Azure Defender güvenlik hizmetleri**|Müşteri ortamı-> Azure > müşteri ortamı|İsteğe Bağlı|Yes|Yalnızca doğrudan|**Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|
|**Azure portal sağlama ve yapılandırma değişiklikleri**|Müşteri ortamı-> Azure > müşteri ortamı|İsteğe Bağlı|No|Yalnızca doğrudan|Sağlama ve yapılandırma değişiklikleri, Azure Data Studio veya kullanılarak yerel olarak yapılabilir [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .  Doğrudan bağlı modda, Azure portal de yapılandırma değişiklikleri sağlayabileceksiniz. **Doğrudan bağlantı modunun bekleyen kullanılabilirliği**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>Internet adresleri, bağlantı noktaları, şifreleme ve proxy sunucusu desteğiyle ilgili ayrıntılar

Şu anda önizleme aşamasında yalnızca dolaylı olarak bağlı mod desteklenir. Bu modda, Internet 'te kullanılabilir hizmetler için yalnızca üç bağlantı gerekir. Bu bağlantılar aşağıdakileri içerir:

- [Microsoft Container Registry (MCR)](#microsoft-container-registry-mcr)
- [Azure Resource Manager API'leri](#azure-resource-manager-apis)
- [Azure İzleyici API 'Leri](#azure-monitor-apis)

Azure ve Microsoft Container Registry tüm HTTPS bağlantıları, resmi olarak imzalanan ve doğrulanabilen sertifikalar kullanılarak SSL/TLS kullanılarak şifrelenir.

Aşağıdaki bölümler bu bağlantılarla ilgili ayrıntıları sağlar. 

### <a name="microsoft-container-registry-mcr"></a>Microsoft Container Registry (MCR)

Microsoft Container Registry, Azure Arc etkin veri Hizmetleri kapsayıcı görüntülerini barındırır.  Bu görüntüleri MCR 'den çekebilir ve özel bir kapsayıcı kayıt defterine gönderebilir ve veri denetleyicisi dağıtım işlemini yapılandırarak kapsayıcı görüntülerini bu özel kapsayıcı kayıt defterinden çekebilirsiniz.

#### <a name="connection-source"></a>Bağlantı kaynağı

Kubernetes, kapsayıcı görüntülerini çeken her bir Kubernetes düğümünün her biri için Kubernetes kubelet.

#### <a name="connection-target"></a>Bağlantı hedefi

`mcr.microsoft.com`

#### <a name="protocol"></a>Protokol

HTTPS

#### <a name="port"></a>Bağlantı noktası

443

#### <a name="can-use-proxy"></a>Proxy kullanabilir

Yes

#### <a name="authentication"></a>Kimlik Doğrulaması

Yok

### <a name="azure-resource-manager-apis"></a>Azure Resource Manager API'leri
Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ve Azure CLI, bazı özellikler Için Azure 'a veya Azure 'dan veri göndermek ve almak üzere Azure Resource Manager API 'lerine bağlanır.

#### <a name="connection-source"></a>Bağlantı kaynağı

[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]Azure 'a bağlanan Azure Data Studio, veya Azure CLI çalıştıran bir bilgisayar.

#### <a name="connection-target"></a>Bağlantı hedefi

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>Protokol

HTTPS

#### <a name="port"></a>Bağlantı noktası

443

#### <a name="can-use-proxy"></a>Proxy kullanabilir

Yes

#### <a name="authentication"></a>Kimlik Doğrulaması 

Azure Active Directory

### <a name="azure-monitor-apis"></a>Azure İzleyici API 'Leri

Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ve Azure CLI, bazı özellikler Için Azure 'a veya Azure 'dan veri göndermek ve almak üzere Azure Resource Manager API 'lerine bağlanır.

#### <a name="connection-source"></a>Bağlantı kaynağı

[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]Azure izleyici 'de izleme ölçümlerini veya günlüklerini karşıya yükleyen Azure CLI çalıştıran bir bilgisayar.

#### <a name="connection-target"></a>Bağlantı hedefi

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>Protokol

HTTPS

#### <a name="port"></a>Bağlantı noktası

443

#### <a name="can-use-proxy"></a>Proxy kullanabilir

Yes

#### <a name="authentication"></a>Kimlik Doğrulaması 

Azure Active Directory

> [!NOTE]
> Şimdilik, Grafana ve kibana panolarına ve öğesinden veri denetleyicisi API 'sine yönelik tüm HTTPS/443 bağlantıları, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] otomatik olarak imzalanan sertifikalar KULLANıLARAK SSL şifrelenir.  Gelecekte bu SSL bağlantılarının şifrelenmesi için kendi sertifikalarınızı sağlamanıza olanak tanıyan bir özellik kullanıma sunulacaktır.

Azure Data Studio ve [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] KUBERNETES API sunucusuna bağlantı, oluşturduğunuz Kubernetes kimlik doğrulama ve şifrelemesini kullanır.  Azure Data Studio ve ' ı kullanan her kullanıcının, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Azure Arc etkin veri hizmetleriyle ilgili birçok eylemi gerçekleştirmek Için Kubernetes API 'sine kimliği doğrulanmış bir bağlantısı olması gerekir.

