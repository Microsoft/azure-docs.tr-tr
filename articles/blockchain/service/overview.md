---
title: Azure blok zinciri hizmetine genel bakış
description: Azure blok zinciri hizmeti 'ne genel bakış
ms.date: 03/15/2021
ms.topic: overview
ms.reviewer: patricka
ms.openlocfilehash: 035e2c1df5912f291ad241ade25361f9cc098b22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573634"
---
# <a name="what-is-azure-blockchain-service"></a>Azure Blok Zinciri Hizmeti nedir?

Azure blok zinciri hizmeti, kullanıcılara blok zinciri ağlarını Azure 'da büyüyerek büyütme ve çalıştırma yeteneği sağlayan, tam olarak yönetilen bir muhasebe hizmetidir. Hem altyapı yönetimi hem de blok zinciri ağ yönetimi için Birleşik denetim sağlayarak Azure blok zinciri hizmeti şunları sağlar:

* Basit ağ dağıtımı ve işlemleri
* Yerleşik konsorsiyum yönetimi
* Tanıdık geliştirme araçlarıyla akıllı sözleşmeler geliştirin

Azure blok zinciri hizmeti birden çok defter protokolünü destekleyecek şekilde tasarlanmıştır. Şu anda, [Istanbul Byzantine hata toleransı (iBFT)](https://docs.goquorum.consensys.net/en/stable/Concepts/Consensus/IBFT/) konsensus mekanizmasını kullanarak Ethereum [çekirdek](https://www.goquorum.com/) defterine yönelik destek sağlar.

Bu özellikler neredeyse hiç yönetim gerektirmez ve tümüyle ek ücret ödemeden sağlanır. Sanal makineleri ve altyapıyı yönetmek için zaman ve kaynak ayırmak yerine, uygulama geliştirme ve iş mantığına odaklanırsınız. Ayrıca, yeni beceriler öğrenmek zorunda kalmadan çözümlerinizi teslim etmek için seçtiğiniz açık kaynak araçları ve platformuyla uygulamanızı geliştirmeye devam edebilirsiniz.

## <a name="network-deployment-and-operations"></a>Ağ dağıtımı ve işlemler

Azure blok zinciri hizmeti 'nin dağıtımı, Azure blok zinciri uzantısı kullanılarak Azure portal, Azure CLı veya Visual Studio Code aracılığıyla yapılır. Dağıtım basitleştirilerek, hem işlem hem de Doğrulayıcı düğümlerinin sağlanması, güvenlik yalıtımı için Azure sanal ağları ve hizmet tarafından yönetilen depolamanın sağlanması dahildir.  Ayrıca, yeni bir blok zinciri üyesi dağıttığınızda, kullanıcılar bir konsorsiyumun de oluşturulmasını veya katılıyor.  Consortiums, farklı Azure aboneliklerindeki birden fazla tarafın paylaşılan bir blok zincirinde birbirleriyle güvenli bir şekilde iletişim kurabilmelerini sağlar.  Bu basitleştirilmiş dağıtım, blok zinciri ağ dağıtımını gün ve dakika olarak azaltır.

### <a name="performance-and-service-tiers"></a>Performans ve hizmet katmanları

Azure blok zinciri hizmeti iki hizmet katmanı sunar: *temel* ve *Standart*. Her katman, hafif geliştirme ve test iş yüklerini desteklemek için farklı performans ve yetenekler sunarak çok büyük ölçekli üretim blok zinciri dağıtımlarını destekler. Geliştirme, test ve kavram kanıtı için *temel* katmanı kullanın. Üretim sınıfı dağıtımları için *Standart* katmanı kullanın. Her iki katmanda de en az bir işlem düğümü ve bir doğrulayıcı düğümü (temel) veya iki Doğrulayıcı düğüm (Standart) bulunur. 

![Fiyatlandırma katmanları](./media/overview/pricing-tiers.png)

İki Doğrulayıcı düğümü sunmaya ek olarak, *Standart* katman her işlem ve doğrulayıcı düğümü Için Iki *sanal çekirdek* sağlar, ancak *temel* katman 1 sanal çekirdek yapılandırma sağlar.  İşlem ve doğrulayıcı düğümleri için 2 sanal çekirdek sunarak, 1 sanal çekirdek çekirdek defterine ayrılabilir, çünkü kalan 1 sanal çekirdek, altyapı ile ilgili diğer hizmetler için kullanılabilir ancak üretim blok zinciri iş yükleri için en iyi performansı sağlar. Fiyatlandırma ayrıntıları hakkında daha fazla bilgi için bkz. [Azure blok zinciri hizmeti fiyatlandırması](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Güvenlik ve bakım

İlk blok zinciri üyesini sağlamaktan sonra, üyeye ek işlem düğümleri ekleyebilirsiniz.  Varsayılan olarak, işlem düğümleri güvenlik duvarı kuralları aracılığıyla güvenli hale getirilir ve erişim için yapılandırma gerektirir.  Ayrıca, tüm işlem düğümleri TLS aracılığıyla hareket halindeki verileri şifreler.  Güvenlik duvarı kuralları, temel kimlik doğrulaması, erişim anahtarları ve Azure Active Directory tümleştirme dahil olmak üzere işlem düğümü erişiminin güvenliğini sağlamak için birden çok seçenek mevcuttur. Daha fazla bilgi için bkz. [işlem düğümlerini yapılandırma](configure-transaction-nodes.md) ve [Azure Active Directory erişimi yapılandırma](configure-aad.md).

Yönetilen bir hizmet olarak, Azure blok zinciri hizmeti, blok zinciri üyesinin düğümlerine en son ana bilgisayar işletim sistemi ve Muhasebe Yazılım yığın güncelleştirmeleriyle birlikte düzeltme ve geleneksel IaaS blok zinciri düğümleri için gereken DevOps 'un çoğunu ortadan kaldıran sağlar.  Düzeltme eki uygulama ve güncelleştirme hakkında daha fazla bilgi için bkz. [desteklenen Azure blok zinciri hizmeti muhasebe sürümleri](ledger-versions.md).

### <a name="monitoring-and-logging"></a>İzleme ve günlüğe kaydetme

Ayrıca, Azure blok zinciri hizmeti, düğümlerin CPU, bellek ve depolama kullanımı hakkında Öngörüler sağlayan Azure Izleyici hizmeti aracılığıyla zengin ölçümler sağlar.  Azure Izleyici Ayrıca, blok zinciri ağ etkinliğine yönelik işlemler ve bloklar, işlem sırası derinliği ve etkin bağlantılar gibi yararlı öngörüler sağlar.  Ölçümler, blok zinciri uygulamanız için önemli olan öngörülere görünümler sunacak şekilde özelleştirilebilir.  Ayrıca, eşikler, kullanıcıların bir e-posta veya kısa mesaj gönderme, bir Logic App, Azure Işlevi çalıştırma veya özel tanımlanmış bir Web kancasına gönderme gibi eylemleri tetiklemelerine olanak sağlayan uyarılar aracılığıyla tanımlanabilir.

![Ekran yakalama, blokları, işlemleri, bekleyen işlemleri ve işlenmiş isteklerin değerlerini içeren izlemeyi gösterir.](./media/overview/metrics.png)

Kullanıcılar, Azure Log Analytics aracılığıyla çekirdek Muhasebeyle ilgili günlükleri veya işlem düğümlerine bağlantı girişimi gibi diğer önemli bilgileri görüntüleyebilir.

## <a name="built-in-consortium-management"></a>Yerleşik konsorsiyum yönetimi

İlk blok zinciri üyesini dağıttığınızda, yeni bir konsorsiya katılırsanız ya da oluşturursunuz.  Konsorsiyum, çok taraflı bir işlemle Transact blok zinciri üyeleri arasındaki idare ve bağlantıyı yönetmek için kullanılan mantıksal bir gruptur.  Azure blok zinciri hizmeti, konsorsiyumun hangi eylemlerin yapabileceğini belirleyen önceden tanımlı akıllı sözleşmeler aracılığıyla yerleşik idare denetimleri sağlar.  Bu idare denetimleri, konsorsiyumun Yöneticisi tarafından gerektiği şekilde özelleştirilebilir. Yeni bir konsorsiyum oluşturduğunuzda, blockzincirinizin üyesi konsorsiyumun varsayılan yöneticisidir ve diğer taraflara konsorsiyume katılması için davet etme olanağı sağlar.  Yalnızca daha önce davet edildiyseniz bir konsorsiya katabilirsiniz.  Bir konsorsiya katılırken, blok zinciri üyesi, konsorsiyumun Yöneticisi tarafından yerine yerleştirilen idare denetimlerine tabidir.

![Konsorsiyum yönetimi](./media/overview/consortium.png)

Bir konsorsiyumde üye ekleme ve kaldırma gibi konsorsiyum yönetim eylemlerine PowerShell ve bir REST API erişebilirsiniz. Solidity tabanlı akıllı sözleşmeleri değiştirmek ve göndermek yerine ortak arabirimleri kullanarak bir konsorsiyunuzu programlı bir şekilde yönetebilirsiniz. Daha fazla bilgi için bkz. [konsorsiyum yönetimi](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Bildiğiniz geliştirme araçlarını kullanarak geliştirme

Açık kaynaklı çekirdek Ethereum defterine bağlı olarak, mevcut Ethereum uygulamalarında yaptığınız şekilde Azure blok zinciri hizmeti için uygulama geliştirebilirsiniz. Önde gelen sektör ortaklarıyla çalışan Azure blok zinciri geliştirme seti Visual Studio Code uzantısı, geliştiricilerin akıllı sözleşmeleri oluşturmak için Truffle paketi gibi tanıdık araçlardan yararlanmasını sağlar. Azure blok zinciri Visual Studio Code uzantısını kullanarak, akıllı sözleşmelerinizi tek bir IDE 'den derleyip dağıtabilmeniz için mevcut bir konsorsiyum oluşturabilir veya bağlanabilirsiniz. Daha fazla bilgi için, VS Code marketi ve [Azure blok zinciri Geliştirme Seti Kullanıcı kılavuzunda](https://aka.ms/vscodebcextensionwiki) [Azure blok zinciri geliştirme seti](https://aka.ms/vscodebcextension) ' ne bakın.

## <a name="publish-blockchain-data"></a>Blok zinciri verilerini yayımlama

Azure blok zinciri hizmeti için blok zinciri Veri Yöneticisi, Azure hizmetleri ile güvenilir ve ölçeklenebilir blok zinciri ile tümleştirme sağlayan Azure Event Grid konulara Azure blok zinciri hizmeti işlem verilerini yakalar, dönüştürür ve sunar. Zincir dışı uygulamaları ve veri depolarını bütünleştirmek için Blockzincirini Veri Yöneticisi kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure blok zinciri hizmeti Için blok zinciri veri Yöneticisi](data-manager.md).

## <a name="support-and-feedback"></a>Destek ve geri bildirim

Azure blok zinciri haberleri için Azure blok zinciri [blogu](https://azure.microsoft.com/blog/topics/blockchain/) ' nı ziyaret ederek, blok zinciri hizmeti tekliflerini ve Azure blok zinciri Mühendisliği ekibinin bilgilerini güncel tutun.

Ürün geri bildirimi sağlamak veya yeni özellikler istemek için, [blok zinciri Için Azure geri bildirim Forumu](https://aka.ms/blockchainuservoice)aracılığıyla bir fikir gönderin veya oylayın.

### <a name="community-support"></a>Topluluk desteği

Microsoft mühendisleri ve Azure blok zinciri topluluk uzmanlarıyla birlikte katılın.

* [Microsoft Q&Azure blok zinciri hizmeti için soru sayfası](/answers/topics/azure-blockchain-service.html)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-service)

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için bir hızlı başlangıç yapın veya bu kaynaklardan daha fazla ayrıntı bulun.
* [Azure Portal kullanarak bir blok zinciri üyesi oluşturun](create-member.md) veya [Azure CLI kullanarak bir blok zinciri üyesi oluşturun](create-member-cli.md)
* Microsoft Learn yolunu izleyerek [blok zinciri geliştirmeye başlayın](/learn/paths/ethereum-blockchain-development)
* [Başlangıç serisini blok zincirine](https://channel9.msdn.com/Series/Beginners-Series-to-Blockchain) izleyin
* Maliyet karşılaştırmaları ve hesaplayıcıları için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/blockchain-service)
* [Azure blok zinciri geliştirme setini](https://github.com/Azure-Samples/blockchain-devkit) kullanarak ilk uygulamanızı oluşturma
* Azure blok zinciri VSCode uzantısı [Kullanıcı Kılavuzu](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)
