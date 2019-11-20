---
title: Azure Güvenlik Merkezi 'nde bulut Yerel bilgi işlem için tehdit algılama | Microsoft Docs
description: Bu makalede, Azure Güvenlik Merkezi 'nde bulunan bulut Yerel işlem uyarıları sunulmaktadır.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: memildin
ms.openlocfilehash: 6b6acb0ae1452795fe02906779b920e4b41f9a55
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748399"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde bulut Yerel bilgi işlem için tehdit algılama

Yerel bir çözüm olarak, Azure Güvenlik Merkezi 'nin birden çok hedef arasında saldırı yöntemi tanımlaması için iç günlüklere benzersiz görünürlüğü vardır. Bu makalede, aşağıdaki Azure hizmetleri için kullanılabilen uyarılar sunulmaktadır:

* [Azure App Service](#app-services)
* [Azure kapsayıcıları](#azure-containers) 

> [!NOTE]
> Bu hizmetler şu anda Azure Kamu ve bağımsız bulut bölgelerinde kullanılabilir değildir.

## Azure App Service<a name="app-services"></a>

Güvenlik Merkezi, App Service üzerinde çalışan uygulamaları hedefleyen saldırıları belirlemek için bulutun ölçeğini kullanır. Saldırganlar, zayıf yanları bulmak ve yararlanmak için Web uygulamalarını araştırma. Belirli ortamlara yönlendirilmeden önce, Azure 'da çalışan uygulamalara yönelik istekler, incelendikleri ve günlüğe kaydedildiği çeşitli ağ geçitleri aracılığıyla yapılır. Bu veriler daha sonra kötüye kullanım ve saldırganlar tanımlamak ve daha sonra kullanılacak yeni desenler öğrenmek için kullanılır.

Güvenlik Merkezi, Azure 'un bulut sağlayıcısı olarak sahip olduğu görünürlüğü kullanarak, birden çok hedef üzerinde saldırı yöntemini tanımlamak üzere iç günlükleri App Service analiz eder. Örneğin, metodolojide, yaygın tarama ve dağıtılmış saldırılar bulunur. Bu tür bir saldırı genellikle IP 'nin küçük bir alt kümesinden gelir ve birden fazla konaktaki benzer uç noktalara gezinme düzenlerini gösterir. Saldırılar, güvenlik açığı bulunan bir sayfa veya eklenti arıyor ve tek bir konağın açısından belirlenemiyor.

Güvenlik Merkezi 'nin Ayrıca temel alınan sanal makinelere ve VM 'lere erişimi vardır. Bellek ile birlikte, altyapı, müşteri makineleriyle ilgili olarak, kısa bir bir saldırıya karşı çok daha fazla bilgi almak için bu hikayeye işaret edebilir. Bu nedenle, güvenlik merkezi bir Web uygulaması kullanıldıktan sonra dağıtılsa bile, devam eden saldırıları tespit edebilir.

> [!div class="mx-tableFixed"]

|Uyarı|Açıklama|
|---|---|
|**Şüpheli WordPress teması çağırma algılandı**|App Service etkinlik günlüğü, App Service kaynağınızın olası bir kod ekleme etkinliğini gösterir.<br/> Bu şüpheli etkinlik, kodun sunucu tarafında yürütülmesini desteklemek için bir WordPress temasını ve ardından doğrudan bir Web isteği ile, istenen tema dosyasını çağırma etkinliğine benzer. Bu tür bir etkinlik WordPress üzerinden bir saldırı kampanyasının parçası olabilir.|
|**Anormal IP adresinden Web sayfasına bağlantı algılandı**|App Service etkinlik günlüğü, daha önce hiçbir şekilde bağlanmayan bir kaynak adresinden hassas bir Web sayfasına bağlantı gösterir. Bu bağlantı, birisinin web uygulaması yönetim sayfalarınıza bir deneme yanılma saldırısı girişiminde bulunduğunu gösterebilir. Ayrıca, yeni bir IP adresi kullanan yasal bir kullanıcının sonucu da olabilir.|
|**Tehdit zekasından Azure App Service FTP arayüzüne bağlı bir IP bulundu**|App Service FTP günlükleri analizi, tehdit bilgileri akışında bulunan bir kaynak adresten bağlantı algıladı. Bu bağlantı sırasında, bir Kullanıcı listelenen sayfalara erişti.|
|**Web parmak baskısı algılandı**|App Service etkinlik günlüğü, App Service kaynağınızın olası bir Web parmak izi etkinliğini gösterir. <br/>Bu şüpheli etkinlik, görünmeyen Elephant adlı bir araçla ilişkilendirilir. Araç parmak izi Web sunucularını yazdırır ve yüklü uygulamaları ve bunların sürümlerini algılamaya çalışır. Saldırganlar genellikle bu aracı, Web uygulamalarının güvenlik açıklarını bulmasını yoklayıp kullanır.|
|**Olası savunmasız Web sayfasına şüpheli erişim algılandı**|App Service etkinlik günlüğü, gizli olarak görünen bir Web sayfasına erişildiğini belirtir. <br/>Bu şüpheli etkinlik, erişim deseninin bir Web tarayıcısı ile aynı olduğu bir kaynak adresinden kaynaklıır. Bu tür bir etkinlik genellikle, bir saldırganın hassas veya güvenlik açığı bulunan Web sayfalarına erişim kazanmaya çalışacak şekilde ağınızı taraymasıyla ilişkilendirilir.|
|**Karşıya yükleme klasöründeki PHP dosyası**|App Service etkinlik günlüğü, karşıya yükleme klasöründe bulunan şüpheli bir PHP sayfasına erişildiğini belirtir. <br/>Bu klasör türü genellikle PHP dosyalarını içermez. Bu tür bir dosyanın varlığı, rastgele dosya karşıya yükleme güvenlik açıklarından faydalanması anlamına gelebilir.|
|**Windows App Service Linux komutlarını çalıştırma girişimi**|App Service işlemlerin çözümlenmesi bir Windows App Service üzerinde Linux komutu çalıştırma girişimi algıladı. Bu eylem Web uygulaması tarafından çalıştırılıyor. Bu davranış genellikle ortak bir Web uygulamasındaki bir güvenlik açığından yararlanan kampanyalar sırasında görülür.|
|**Şüpheli PHP yürütmesi algılandı**|Makine günlükleri şüpheli bir PHP işleminin çalıştığını gösterir. Eylem, PHP işlemini kullanarak komut satırından işletim sistemi komutlarını veya PHP kodunu çalıştırma girişiminde bulunur. Bu davranış meşru olsa da Web uygulamalarında bu davranış, Web kabuklarını Web sitelerine bulaştıkları gibi kötü amaçlı etkinlikleri gösteriyor olabilir.|
|**Geçici klasörden işlem yürütme**|App Service işlemler analizi, uygulamanın geçici klasöründen bir işlemin yürütülmesini algıladı. Bu davranış meşru olsa da, Web uygulamalarında bu davranış kötü amaçlı etkinlikleri gösterebilir.|
|**Yüksek ayrıcalık komutu çalıştırma denemesi algılandı**|App Service işlemlerin çözümlenmesi, yüksek ayrıcalık gerektiren bir komutu çalıştırma girişimi algıladı. Komut, Web uygulaması bağlamında çalıştırıldı. Bu davranış meşru olsa da, Web uygulamalarında bu davranış kötü amaçlı etkinlikleri gösterebilir.|

## Azure kapsayıcıları<a name="azure-containers"></a>

Güvenlik Merkezi, Kapsayıcılı ortamlarınız için gerçek zamanlı tehdit algılama sağlar ve şüpheli etkinlikler için uyarı oluşturur. Bu bilgileri kullanarak güvenlik sorunlarını hızlı bir şekilde çözebilir ve kapsayıcılarınızın güvenlik düzeyini artırabilirsiniz.

Farklı düzeylerde tehditler tespit ediyoruz: 

* **Ana bilgisayar düzeyi** -Güvenlik Merkezi 'nin Aracısı (Standart katmanda mevcuttur. Ayrıntılar için [fiyatlandırma](security-center-pricing.md) ) Linux 'u şüpheli etkinlikler için izler. Aracı, düğüm veya üzerinde çalışan bir kapsayıcıdan kaynaklanan şüpheli etkinlikler için uyarıları tetikler. Web kabuğu algılama ve bilinen şüpheli IP adresleriyle bağlantı gibi etkinliklere örnek olarak verilebilir.

    Kapsayıcılı ortamınızın güvenliğine daha ayrıntılı bir bakış için, aracı kapsayıcıya özgü Analizi izler. Ayrıcalıklı kapsayıcı oluşturma, API sunucularına şüpheli erişim ve bir Docker kapsayıcısı içinde çalışan Secure Shell (SSH) sunucuları gibi olaylar için uyarı tetikleyecektir.

    >[!NOTE]
    > Aracılarınıza aracıları yüklememeyi seçerseniz, tehdit algılama avantajları ve uyarıların yalnızca bir alt kümesini alacaksınız. Hala kötü amaçlı sunucularla ağ analizi ve iletişimlerle ilgili uyarılar alacaksınız.

* **Aks küme düzeyi**Için Kubernetes denetim günlükleri analizini temel alan tehdit algılama izlemesi vardır. **Aracısız** izlemeyi etkinleştirmek Için, **fiyatlandırma & ayarları** sayfasından Kubernetes seçeneğini aboneliğinize ekleyin (bkz. [fiyatlandırma](security-center-pricing.md)). Bu düzeyde uyarı oluşturmak için, güvenlik merkezi AKS tarafından yönetilen Hizmetleri AKS tarafından alınan günlükleri kullanarak izler. Bu düzeydeki olay örnekleri, sunulan Kubernetes panoları, yüksek ayrıcalıklı rollerin oluşturulmasını ve hassas takmaları oluşturmayı içerir.

    >[!NOTE]
    > Güvenlik Merkezi, Azure Kubernetes hizmet eylemleri ve abonelik ayarlarında Kubernetes seçeneği etkinleştirildikten sonra gerçekleşen dağıtımlar için algılama uyarıları oluşturur. 

Ayrıca, güvenlik araştırmacıları küresel takımımız tehdidi sürekli olarak izler. Bulundukları gibi kapsayıcıya özgü uyarıları ve güvenlik açıklarını ekler.


### <a name="aks-cluster-level-alerts"></a>AKS küme düzeyi uyarıları

> [!div class="mx-tableFixed"]

|Uyarı|Açıklama|
|---|---|
|**Küme Yöneticisi rolüne ÖNIZLEME-rol bağlama algılandı**|Kubernetes denetim günlüğü analizi, Küme Yöneticisi rolüne yeni bir bağlama algıladı ve bu da yönetici ayrıcalıklarına neden oldu. Yönetici ayrıcalıklarını gereksiz şekilde sağlamak, kümede ayrıcalık yükseltme sorunları oluşmasına neden olabilirler.|
|**ÖNIZLEME-açığa çıkarılan Kubernetes panosu algılandı**|Kubernetes denetim günlüğü analizi, Kubernetes panosunun bir LoadBalancer hizmeti tarafından açığa çıkmasını algıladı. Sunulan panolar küme yönetimine kimliği doğrulanmamış erişime izin verir ve güvenlik tehdidi oluşturabilir.|
|**ÖNIZLEME-yeni yüksek ayrıcalıklar rolü algılandı**|Kubernetes denetim günlüğü analizi, yüksek ayrıcalıklara sahip yeni bir rol algıladı. Yüksek ayrıcalıklara sahip bir role bağlama, kümede Kullanıcı/Grup yükseltilmiş ayrıcalıklar sağlar. Gereksiz ayrıcalıklar sağlanması, kümede ayrıcalık yükseltme sorunları oluşmasına neden olabilirler.|
|**ÖNIZLEME-kuin sistemi ad alanında yeni kapsayıcı algılandı**|Kubernetes denetim günlüğü analizi, bu ad alanında normalde çalıştırılan kapsayıcılar arasında olmayan kuin-System ad alanında yeni bir kapsayıcı algıladı. Kuto-System ad alanları kullanıcı kaynaklarını içermemelidir. Saldırganlar, kötü amaçlı bileşenleri gizlemek için bu ad alanını kullanabilir.|
|**ÖNIZLEME-dijital para birimi araştırma kapsayıcısı algılandı**|Kubernetes denetim günlüğü analizi, dijital para birimi araştırma aracıyla ilişkilendirilmiş bir görüntü içeren bir kapsayıcı algıladı.|
|**ÖNIZLEME-ayrıcalıklı kapsayıcı algılandı**|Kubernetes denetim günlüğü analizi yeni bir ayrıcalıklı kapsayıcı algıladı. Ayrıcalıklı bir kapsayıcı, düğümün kaynaklarına erişebilir ve kapsayıcılar arasındaki yalıtımı keser. Tehlikeye atılırsa bir saldırgan, düğüme erişim kazanmak için ayrıcalıklı kapsayıcıyı kullanabilir.|
|**ÖNIZLEME-hassas birim bağlaması ile kapsayıcı algılandı**|Kubernetes denetim günlüğü analizi, hassas birim bağlama ile yeni bir kapsayıcı algıladı. Algılanan birim, düğümden kapsayıcıya gizli bir dosya veya klasör bağlayan bir hostPath türüdür. Kapsayıcının güvenliği tehlikeye girerse, saldırgan bu takma birimi kullanarak düğüme erişim elde edebilir.|



### <a name="host-level-alerts"></a>Konak düzeyi uyarıları

> [!div class="mx-tableFixed"]

|Uyarı|Açıklama|
|---|---|
|**Ayrıcalıklı kapsayıcı algılandı**|Makine günlükleri, ayrıcalıklı bir Docker kapsayıcısının çalıştığını gösterir. Ayrıcalıklı bir kapsayıcı konağın kaynaklarına tam erişime sahiptir. Tehlikeye atılırsa, bir saldırgan konak makinesine erişim kazanmak için ayrıcalıklı kapsayıcıyı kullanabilir.|
|**Kapsayıcıda ayrıcalıklı komut çalıştırma**|Makine günlükleri, bir Docker kapsayıcısında ayrıcalıklı bir komutun çalıştırıldığını gösterir. Ayrıcalıklı bir komutun konak makinesinde genişletilmiş ayrıcalıkları vardır.|
|**Sunulan Docker Daemon algılandı**|Makine günlükleri, Docker Daemon 'nizin (dockerd) bir TCP yuvası sunduğunu gösterir. Varsayılan olarak, Docker yapılandırması bir TCP yuvası etkinleştirildiğinde şifreleme veya kimlik doğrulama kullanmaz. İlgili bağlantı noktasına erişimi olan herkes, Docker Daemon 'a tam erişim alabilir.|
|**SSH sunucusu bir kapsayıcı içinde çalışıyor**|Makine günlükleri, bir SSH sunucusunun bir Docker kapsayıcısı içinde çalıştığını gösterir. Bu davranış bilerek mümkün olsa da, genellikle bir kapsayıcının yanlış yapılandırılmış veya ihlal edilen olduğunu gösterir.|
|**Bir Miner görüntüsü olan kapsayıcı algılandı**|Makine günlükleri, dijital para birimi madenciliği ile ilişkili bir görüntüyü çalıştıran bir Docker kapsayıcısının yürütülmesini gösterir. Bu davranış muhtemelen kaynaklarınızın nerede olduğunu gösterebilir.|
|**Kubernetes API 'sine şüpheli istek**|Makine günlükleri, Kubernetes API 'sine şüpheli bir istek yapıldığını gösterir. İstek bir Kubernetes düğümünden gönderilmiş, muhtemelen düğümde çalışan kapsayıcılardan biri. Bu davranış kasıtlı olarak kullanılabilse de, düğümün güvenliği aşılmış bir kapsayıcı çalıştırdığı anlamına gelebilir.|
|**Kubernetes panosuna şüpheli istek**|Makine günlükleri, Kubernetes panosuna şüpheli bir istek yapıldığını gösterir. İstek bir Kubernetes düğümünden gönderilmiş, muhtemelen düğümde çalışan kapsayıcılardan biri. Bu davranış kasıtlı olarak kullanılabilse de, düğümün güvenliği aşılmış bir kapsayıcı çalıştırdığı anlamına gelebilir.|