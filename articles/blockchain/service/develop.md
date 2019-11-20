---
title: Azure blok zinciri hizmeti geliştirmeye genel bakış
description: Azure blok zinciri hizmeti 'nde çözüm geliştirmeye giriş.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/11/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a9444847fb75bdf01cabba98057605afbe03c9fc
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577438"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure blok zinciri hizmeti geliştirmeye genel bakış

Azure blok zinciri hizmeti sayesinde, varlık izleme, dijital belirteç, bağlılık ve geri alma, tedarik zinciri finansal ve provenance gibi kurumsal senaryoları etkinleştirmek için konsorsiyum blok zinciri ağları oluşturabilirsiniz. Bu makale, Azure blok zinciri hizmeti geliştirme ve kurumsal için blok zinciri uygulamaya yönelik önemli konulara giriş niteliğindedir.

## <a name="client-connection-to-azure-blockchain-service"></a>Azure blok zinciri hizmetine istemci bağlantısı

Blok zinciri ağları için tam düğümler, hafif düğümler ve uzak istemciler dahil farklı türlerde istemciler vardır. Azure blok zinciri hizmeti düğümleri içeren bir blok zinciri ağı oluşturur. Blok zinciri geliştirme için Azure blok zinciri hizmeti ağ geçidinizin olarak farklı istemcileri kullanabilirsiniz. Azure blok zinciri hizmeti, temel kimlik doğrulaması veya erişim anahtarını bir geliştirme uç noktası olarak sunar. Aşağıda, Bağlan ' a kullanabileceğiniz popüler istemciler verilmiştir.

### <a name="metamask"></a>MetaMask

MetaMask, tarayıcı tabanlı bir cüzdan (uzak istemci), RPC istemcisi ve temel sözleşme Gezgini. Diğer tarayıcı waldinden farklı olarak, MetaMask bir Web3 örneğini tarayıcı JavaScript bağlamına çıkartır ve bu, çeşitli Ethereum blok zincirlerine (*mainnet*, *Ropsten testnet*, *kovan testnet*, Yerel RPC düğümü) bağlanan bir RPC istemcisi olarak davranır vb.). Azure blok zinciri hizmetine bağlanmak ve yeniden karışımı kullanarak blok zinciri geliştirmeye başlamak için özel RPC 'yi kolayca ayarlayabilirsiniz.

### <a name="geth"></a>Geth

Geth, go 'da uygulanan tam Ethereum düğümünü çalıştırmaya yönelik komut satırı arabirimidir. Tam düğüm çalıştırmanız gerekmez, ancak Azure blok zinciri hizmeti ile etkileşim kurmak için JavaScript API 'sini ortaya çıkaran bir JavaScript çalışma zamanı ortamı sağlayan etkileşimli konsolunu da başlatabilirsiniz.

## <a name="development-framework-configuration"></a>Geliştirme çerçevesi yapılandırması

Gelişmiş kurumsal blok zinciri çözümleri geliştirmek için bir geliştirme çerçevesi, farklı blok zinciri ağlarına bağlanmak, akıllı sözleşme yaşam döngüsünü yönetmek, testi otomatikleştirmek, betiklerle akıllı anlaşma dağıtmak ve etkileşimli bir konsolu test etmek için gereklidir.

Truffle, Ethereum blockzincirleri üzerinde merkezi olmayan uygulamalar yazmak, derlemek, dağıtmak ve test etmek için popüler bir blok zinciri geliştirme çerçevesidir. Ayrıca, Truffle ' yı akıllı sözleşme geliştirmeyi ve geleneksel web geliştirmeyi sorunsuz bir şekilde tümleştirme girişiminde bulunan bir çerçeve olarak düşünebilirsiniz.

En küçük proje bile en az iki blok zinciri düğümüyle etkileşime girer: bir geliştirici makinesinde, diğeri ise geliştiricinin uygulamasını dağıttığı ağı temsil eder. Örneğin, ana genel Ethereum ağı veya Azure blok zinciri hizmeti. Truffle, her ağ için derleme ve dağıtım yapıtlarını yönetmeye yönelik bir sistem sağlar ve bu nedenle nihai uygulama dağıtımını basitleştirir. Daha fazla bilgi için bkz. [hızlı başlangıç: Azure blok zinciri hizmeti ağına bağlanmak Için Truffle kullanma](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Ethereum çekirdeği özel işlemi

Çekirdek, işlem artı sözleşme gizliliği ve yeni konsensus mekanizmalarına sahip bir Ethereum tabanlı dağıtılmış bir genel muhasebe protokolüdür. Go-Ethereum üzerinde önemli geliştirmeler şunları içerir:

* Gizlilik-çekirdek, genel ve özel durum ayrımı aracılığıyla özel işlemleri ve özel sözleşmeleri destekler ve özel verilerin ağ katılımcılarına yönlendirilme için eşler arası şifreli ileti değişimlerini kullanır.
* Alternatif konsensus mekanizmaları-geçici bir ağda iş açısından veya yarışma kanıtlama için gerek olmadan. Çekirdek, Raft ve iBFT gibi Consortium zincirleri için tasarlanan birden çok konsensus mekanizması sunar.  Azure blok zinciri hizmetleri, iBFT konsensus mekanizmasını kullanır.

* Yalnızca bilinen tarafların ağa katılabilmesini sağlamak için, akıllı sözleşmeleri kullanarak eş eşleme-düğüm ve eş izinleri
* Daha yüksek performans-çekirdek, genel olarak daha yüksek performans sunar

Özel işlem örneği için bkz. [öğretici: Azure blok zinciri hizmetini kullanarak Işlem gönderme](send-transaction.md) .

## <a name="block-explorers"></a>Blok gezginleri

Blok araştırıcılar, bireysel blok içeriği, işlem adresi verileri ve geçmişi görüntüleyen çevrimiçi blok zinciri tarayıcılardır. Temel blok bilgileri Azure blok zinciri hizmeti 'nde Azure Izleyici aracılığıyla kullanılabilir, ancak geliştirme sırasında daha fazla ayrıntı bilgisine ihtiyacınız varsa, araştırıcılar yararlı olabilir.  Kullanabileceğiniz popüler açık kaynaklı blok gezginleri vardır. Aşağıda, Azure blok zinciri hizmeti ile çalışan blok gezginlerinin listesi verilmiştir:

* Web3 Labs 'den [Epırus Azure blok zinciri hizmet Gezgini](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview)
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS ölçümü

Blok zinciri daha fazla kurumsal senaryolarda kullanıldığından, performans sorunlarını önlemek ve sistem verimsizlikleri önlemek için saniye başına işlem (TPS) hızı önemlidir. Yüksek işlem ücretleri, merkezi olmayan bir blok zinciri dahilinde devam etmek zor olabilir. Doğru bir TPS ölçümü, sunucu iş parçacığı, işlem kuyruğu boyutu, ağ gecikmesi ve güvenlik gibi farklı faktörlerden etkilenebilir. Geliştirme sırasında TPS hızını ölçmenize gerek duyuyorsanız, popüler bir açık kaynak aracı, bir [chaincepdir](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Sonraki adımlar

[Hızlı başlangıç: Azure blok zinciri hizmeti ağına bağlanmak için Truffle kullanın](connect-truffle.md)
