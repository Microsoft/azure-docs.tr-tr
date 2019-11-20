---
title: Ethereum yetki kanıtı-Azure
description: Çok siteli bir konsorsiyumum ağını dağıtmak ve yapılandırmak için Ethereum yetki kanıtlama Konsorsiyumu çözümünü kullanın
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 04/08/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: 01b9f7f74077737ea95a56bbe81f440db425bf0c
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698464"
---
# <a name="ethereum-proof-of-authority-consortium"></a>Ethereum yetki kanıtlama Konsorsiyumu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Genel Bakış
[Bu çözüm](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) , en az Azure ve Ethereum bilgisine sahip çok üyeli bir konsorsiyum yetkilisi olan çok kullanıcılı bir ağ kanıtı ağı dağıtmayı, yapılandırmayı ve yönetimini kolaylaştırmak için tasarlanmıştır.

Azure portal aracılığıyla Kullanıcı girişleri ve tek tıklamayla dağıtım ile her üye, dünya genelinde Microsoft Azure Işlem, ağ ve depolama hizmetleri kullanarak bir ağ ayak izi sağlayabilir. Her üyenin ağ izi, bir uygulamanın veya kullanıcının Ethereum işlemlerini göndermek için etkileşime girebileceği yük dengeli bir doğrulayıcı düğümleri kümesinden oluşur.

## <a name="concepts"></a>Kavramlar

### <a name="terminology"></a>Terminoloji

-   **Consensus** -blok doğrulaması ve oluşturma aracılığıyla dağıtılmış ağ genelinde verileri eşitleme işlemidir.

-   **Konsorsiyum üyesi** -blok zinciri ağı üzerinde konsensus 'a katılan bir varlık.

-   **Admin** -belirli bir Konsorsiyumu üyesine katılımı yönetmek için kullanılan bir Ethereum hesabı.

-   **Validator** -yönetici adına konsensus 'a katılan bir Ethereum hesabıyla ilişkili bir makine.

### <a name="proof-of-authority"></a>Yetki kanıtı

Blok zinciri topluluğuyla yeni olan kullanıcılar için, bu çözümün yayını Azure 'da kolay ve yapılandırılabilir bir şekilde teknoloji hakkında bilgi edinmek için harika bir fırsattır. İş kanıtı, ağı kendi kendine düzenleyen ve dengeli bir katılımaya izin veren hesaplama maliyetlerinden yararlanan bir Sybil direncidir. Bu, şifreli para birimi rekabeti ağ üzerindeki güvenliği yükseltir anonim, açık blok zinciri ağlarında harika bir şekilde çalışmaktadır. Ancak, özel/konsorsiyum ağlarında temeldeki en önemli bir değer yoktur. Alternatif bir protokol ve yetki kanıtı, tüm yarışmaya katılanların bilinen ve saygın olduğu izin verilen ağlarda daha uygundur. Araştırma gereksinimi olmadan, Byzantine hata toleransını sürdürirken yetki kanıtı daha etkilidir.

### <a name="consortium-governance"></a>Consortium idare

Yetki kanıtı, ağın sağlıklı kalmasını sağlamak için izin verilen bir ağ yetkilileri listesine bağlı olduğundan, bu izin listesinde değişiklik yapmak için bir yetkili mekanizma sağlanması önemlidir. Her dağıtım, izin verilen bu listenin zinciri için bir dizi akıllı sözleşme ve Portal ile birlikte sunulur. Önerilen bir değişiklik, konsorsiyum üyelerinden oluşan bir büyük oyuna ulaştığında, değişiklik yapılır. Bu, yeni yarışma katılımcılarının eklenmesine veya riskli bir ağı teşvik eden bir saydam şekilde kaldırılmasına olanak sağlar.

### <a name="admin-account"></a>Yönetici hesabı

Yetkili olmayan düğümlerin dağıtımı sırasında yönetici Ethereum adresi istenir. Bu Ethereum hesabını oluşturmak ve güvenli hale getirmek için birkaç farklı mekanizma kullanabilirsiniz. Bu adres ağ üzerinde bir yetkili olarak eklendikten sonra, bu hesabı idare 'e katılmak için kullanabilirsiniz. Bu yönetici hesabı, bu dağıtımın bir parçası olarak oluşturulan Doğrulayıcı düğümlerine konsensus katılımı sağlamak için de kullanılacaktır. Yalnızca ortak Ethereum adresi kullanıldığından, her yöneticinin kendi özel anahtarlarını istenen güvenlik modeliyle takip eden bir şekilde güvenli hale getirme esnekliği vardır.

### <a name="validator-node"></a>Doğrulayıcı düğümü

Yetki kanıtlama protokolünde, doğrulayıcı düğümleri geleneksel Miner düğümlerinin yerini alır. Her doğrulayıcının, bir akıllı anlaşma izin listesine eklenen benzersiz bir Ethereum kimliği vardır. Doğrulayıcı bu listede olduktan sonra blok oluşturma işlemine katılabilir. Bu işlem hakkında daha fazla bilgi edinmek için bkz. [yetkili gidiş konsensus](https://wiki.parity.io/Aura)hakkında eşlik eden belgeler. Her konsorsiyum üyesi, coğrafi yedeklilik için beş bölge genelinde iki veya daha fazla Doğrulayıcı düğümü sağlayabilir. Doğrulayıcı düğümleri, temel alınan dağıtılmış defter durumunda konsensus 'a gelmesi için diğer Doğrulayıcı düğümleriyle iletişim kurar.
Ağa yönelik dengeli bir katılım sağlamak için, her bir konsorsiyumun, ağdaki ilk Üyeden daha fazla Doğrulayıcıları kullanmasını yasaktır (ilk üye üç Doğrulayıcıları dağıttığında, her üye yalnızca üç Doğrulayıcıları olabilir).

### <a name="identity-store"></a>Kimlik deposu

Her üye aynı anda birden çok doğrulayıcı düğümüne sahip olacak ve her düğümde izin verilen bir kimlik olması gerektiğinden, doğrulayıcılar ağ üzerinde güvenli bir şekilde benzersiz bir etkin kimlik elde edebilir. Bunun daha kolay olması için, oluşturulan Ethereum kimliklerini güvenli bir şekilde tutan her üyenin aboneliğine dağıtılan bir kimlik deposu geliştirdik. Dağıtım sonrasında, düzenleme kapsayıcısı her Doğrulayıcı için bir Ethereum özel anahtarı oluşturur ve bunu Azure Key Vault depolar. Eşlik düğümü başlamadan önce, kimliğin başka bir düğüm tarafından çekilmediğinden emin olmak için kullanılmayan bir kimlik üzerinde bir kira edinir. Kimlik, istemciye, blok oluşturmaya başlama yetkisi veren istemciye sağlanır. Barındırma VM 'si bir kesinti yaşıyorsa, kimlik kirası serbest bırakılır ve bu, bir değişiklik düğümünün gelecekte kimliğini sürdürmesini sağlar.

### <a name="bootnode-registrar"></a>Bootnode Kaydedicisi

Bağlantı kolaylığı sağlamak için, her üye [VERI API uç noktasında](#data-api)bir bağlantı bilgileri kümesi barındırır. Bu veriler, katılan üye için eşleme düğümleri olarak sağlanmış olan bootnodes listesini içerir. Bu veri API 'sinin bir parçası olarak, bu bootnode listesini güncel tutduk

### <a name="bring-your-own-operator"></a>Kendi operatörüzü getir

Genellikle bir konsorsiyum üyesi ağ İdaresi 'ne katılmak, ancak altyapısını çalıştırmak ve bakımını yapmak istemeyecektir. Geleneksel sistemlerden farklı olarak ağ genelinde tek bir operatör olması, blok zinciri sistemlerinin merkezi olmayan modeline karşı çalışır. Her bir konsorsiyum üyesi, bir ağı işletmek için merkezi bir aracı çalıştırmak yerine, altyapı yönetimini kendi tercih eden işleçle temsil edebilir. Bu, her üyenin kendi altyapısını veya temsilci işlemini farklı bir iş ortağıyla çalıştırmayı seçebildiği bir karma modele izin verir. Temsilci olan işlem iş akışı aşağıdaki gibi çalışmaktadır:

1.  **Konsorsiyum üyesi** bir Ethereum adresi oluşturuyor (özel anahtarı barındırır)

2.  **Konsorsiyum üyesi** , genel Ethereum adresini **işlece** sağlar

3.  **İşleç** Azure Resource Manager çözümümüzü kullanarak POA Doğrulayıcı düğümlerini dağıtır ve yapılandırır

4.  **İşleç** , **Consortium üyesine** RPC ve yönetim uç noktasını sağlar

5.  **Konsorsiyum üyesi** , doğrulayıcı düğümlerini kabul eden bir isteği imzalamak için özel anahtarını kullanır ve kendi adına katılmak Için bu **işleci** dağıtıldı

### <a name="azure-monitor"></a>Azure İzleyici

Bu çözüm, düğüm ve ağ istatistiklerini izlemek için Azure Izleyici ile de birlikte gelir. Uygulama geliştiricileri için, bu, blok oluşturma istatistiklerini izlemek üzere temeldeki blok zincirine yönelik görünürlük sağlar. Ağ işletmenleri, altyapı istatistikleri ve sorgulanabilir Günlükler aracılığıyla ağ kesintilerini hızla algılamak ve engellemek için Azure Izleyici 'yi kullanabilir. Daha fazla bilgi için bkz. [hizmet izleme](#service-monitoring).

### <a name="deployment-architecture"></a>Dağıtım mimarisi

#### <a name="description"></a>Açıklama

Bu çözüm, tek veya çok bölgeli bir çok üyeli bir Ethereum Konsorsiyumu ağını dağıtabilir. Varsayılan olarak, RPC ve eşleme uç noktalarına, abonelikler ve bulutlar arasında basitleştirilmiş bağlantı sağlamak için genel IP üzerinden erişilebilir. Uygulama düzeyinde erişim denetimleri için [eşlik 'nın permissioning sözleşmelerini](https://wiki.parity.io/Permissioning) kullanmanızı öneririz. Ayrıca, sanal ağlar arası bağlantı için VNet ağ geçitlerinden yararlanan VPN 'lerin arkasında dağıtılan ağları da destekliyoruz. Bu dağıtımlar daha karmaşıktır, bu nedenle önce genel IP modeliyle başlamanız önerilir.

#### <a name="consortium-member-overview"></a>Consortium üyesine genel bakış

Her bir konsorsiyum üye dağıtımı şunları içerir:

-   PoA Doğrulayıcıları çalıştırmaya yönelik sanal makineler

-   RPC, eşleme ve Idare DApp isteklerini dağıtmaya yönelik Azure Load Balancer

-   Doğrulayıcı kimliklerini güvenli hale getirmek için Azure Key Vault

-   Kalıcı ağ bilgilerini barındırmak ve kiralamayı koordine etmek için Azure depolama

-   Günlük ve performans istatistikleri toplamak için Azure Izleyici

-   Özel VNET 'lerde VPN bağlantılarına izin vermek için sanal ağ geçidi (isteğe bağlı)

![dağıtım mimarisi](./media/ethereum-poa-deployment/deployment-architecture.png)

Güvenilirlik ve modülerlilik için Docker kapsayıcılarından faydalanır. Her dağıtımın parçası olarak sürümlü görüntüleri barındırmak ve barındırmak için Azure Container Registry kullanırız. Kapsayıcı görüntüleri aşağıdakilerden oluşur:

-   Orchestrator

    -   Dağıtım sırasında bir kez çalışır

    -   Kimlikler ve idare sözleşmeleri oluşturur

    -   Kimlikleri kimlik deposunda depolar

-   Eşlik Istemcisi

    -   Kimlik deposundan kimlik kiralamaları

    -   Eşleri bulur ve eşler arasında bağlanır

-   Etden stats Aracısı

    -   RPC ve Azure Izleyici 'ye gönderim aracılığıyla yerel günlükleri ve istatistikleri toplar

-   İdare DApp

    -   Idare sözleşmeleri ile etkileşim için Web arabirimi

## <a name="how-to-guides"></a>Nasıl yapılır kılavuzları
### <a name="governance-dapp"></a>İdare DApp

Yetki kanıtlama kalbi, yönetim açısından idare edilir. İdare DApp, önceden dağıtılan [akıllı sözleşmeleri](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) ve ağdaki yetkilileri yönetmek için kullanılan bir Web uygulamasını bir kümesidir.
Yetkililer, yönetici kimliklerine ve doğrulayıcı düğümlerine bölünmüştür.
Yöneticiler, konsensus katılımını bir doğrulayıcı düğümleri kümesine devretmek için güce sahiptir. Yöneticiler ayrıca diğer yöneticileri ağ içine veya dışına da oylayabilir.

![idare dapp](./media/ethereum-poa-deployment/governance-dapp.png)

-   Merkezi **olmayan idare-** Ağ yetkililerinde yapılan değişiklikler, select yöneticileri tarafından zincir üzerinde oylama aracılığıyla yönetilir.

-   **Doğrulayıcı temsili-** Yetkililer, her bir bir dağıtımda ayarlanan Doğrulayıcı düğümlerini yönetebilir.

-   **Denetlenebilir değişiklik geçmişi-** Her değişiklik, saydam ve seslebilirlik sağlayan blok zincirinde kaydedilir.

#### <a name="getting-started-with-governance"></a>İdare ile çalışmaya başlama
Idare DApp aracılığıyla herhangi bir türde işlem gerçekleştirmek için bir Ethereum cüzdan kullanmanız gerekir.  En kolay yaklaşım, [MetaMask](https://metamask.io)gibi bir tarayıcı içi cüzdan kullanmaktır. Bununla birlikte, bunlar ağa dağıtılmış akıllı sözleşmelerde, Idare sözleşmenize yönelik etkileşimlerinizi de otomatikleştirebiliriz.

MetaMask yükledikten sonra, tarayıcıdaki Idare DApp 'ye gidin.  URL 'YI dağıtım onay e-postasında veya dağıtım çıkışında Azure portal aracılığıyla bulabilirsiniz.  Tarayıcı içi bir cüzdan yüklü değilse herhangi bir eylem gerçekleştiremezsiniz; Ancak yine de yönetici durumunu okuyabilirsiniz.  

#### <a name="becoming-an-admin"></a>Yönetici olma
Ağa dağıtılan ilk üyesiyse, otomatik olarak yönetici olacak ve eşlik düğümleriniz doğrulayıcılar olarak listelenecektir.  Ağa katılırsanız, büyük bir yönetici olarak (% 50 ' dan fazla) erişmeniz gerekir var olan yönetici kümesi.  Yönetici olmaya devam ederseniz, düğümleriniz blok zincirini eşitler ve doğrular; Ancak, bu kişiler blok oluşturma işlemine katılmaz. Bir yönetici olmak üzere oylama işlemini başlatmak için __aday ' a__ tıklayın ve Ethereum adresinizi ve diğer adınızı girin.

![Belirle](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Larınızdan
__Adaylar__ sekmesinin seçilmesi, geçerli aday Yöneticiler kümesini gösterir.  Bir aday, geçerli yöneticiler tarafından bir çoğunluk oyuna ulaştığında aday bir yöneticiye yükseltilir.  Bir aday üzerinde oy vermek için satırı seçin ve üst kısımdaki "oyın" düğmesine tıklayın.  Oyunuzu bir oyda değiştirirseniz, adayı seçebilir ve "rescind oyı" düğmesine tıklayabilirsiniz.

![Larınızdan](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Yöneticiler
__Yöneticiler__ sekmesi geçerli yönetici kümesini gösterir ve size bir oy verme özelliği sağlar.  Yönetici% 50 ' den fazla destek kaybettikten sonra ağ üzerinde yönetici olarak kaldırılacaktır.  Bu yöneticinin sahip olduğu tüm Doğrulayıcı düğümleri, Doğrulayıcı durumunu kaybeder ve ağ üzerinde işlem düğümleri olur.  Herhangi bir sayıda nedenden dolayı yönetici kaldırılabilir. Ancak, bir ilkeyi önceden kabul etmek için konsorsiyumun bir üyesi vardır.

![Yöneticiler](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Metninin
Sol menüdeki __doğrulayıcılar__ sekmesini seçtiğinizde, bu örnek için dağıtılan geçerli eşlik düğümleri ve bunların geçerli durumu (düğüm türü) görüntülenir.  Bu görünüm geçerli dağıtılmış Consortium üyesini temsil ettiğinden, her Consortium üyesinin bu listede farklı bir doğrulayıcılar kümesi olur.  Bu yeni bir dağıtılmış örneğidir ve henüz Doğrulayıcıları eklemediyseniz, ' doğrulayıcılar Ekle ' seçeneği gösterilir.  Bunun belirlenmesi, bölgesel olarak dengeli bir eşlik düğümleri kümesini otomatik olarak seçer ve bunları Doğrulayıcı kümesine atar.  İzin verilen kapasiteden daha fazla düğüm dağıttıysanız, kalan düğümler ağ üzerinde işlem düğümleri olur.

Her doğrulayıcının adresi, Azure 'daki [kimlik deposu](#identity-store) aracılığıyla otomatik olarak atanır.  Bir düğüm kapanıyorsa, kendi kimliğini bir yere alır ve dağıtımınızdaki başka bir düğümün yerini almak için bu bir düğüme izin verir.  Bu, konsensus katılımınızı yüksek oranda kullanılabilir olmasını sağlar.

![Metninin](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Konsorsiyum adı
Herhangi bir yönetici, sayfanın üstünde görünen konsorsiyumun adını güncelleştirebilir.  Konsorsiyumun adını güncelleştirmek için sol üstteki dişli simgesini seçin.

#### <a name="account-menu"></a>Hesap menüsü
Sağ üst köşede, Ethereum hesabınızın diğer adı ve identicon.  Yönetici değilseniz, diğer adınızı güncelleştirebilirsiniz.

![Hesap](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Ethereum yetki kanıtı dağıtma

Çok taraflı dağıtım akışına bir örnek aşağıda verilmiştir:

1.  Üç üye, her biri MetaMask kullanarak bir Ethereum hesabı oluşturur

2.  *A üyesi bir* Ethereum POA dağıtır ve bu kişilerin Ethereum ortak adresini sağlar

3.  *A üyesi* , *B üyesine* ve *C üyesine* yönelik Consortium URL 'sini sağlar

4.  *Üye B* ve *üye C* dağıtımı, Ethereum POA, Ethereum ortak adresi ve *ÜYESI A*'nın Konsorsiyumu URL 'si

5.  *B üyesi B* 'de yönetici olarak *bir oy üyesi*

6.  Üye *A* ve *üye B* üye *C* 'yi yönetici olarak Oylar

Bu işlem, birkaç sanal makine ve yönetilen disk dağıtımı destekleyebilen bir Azure aboneliği gerektirir. Gerekirse, başlamak için [ücretsiz bir Azure hesabı oluşturun](https://azure.microsoft.com/free/) .

Abonelik güvenli hale getirildikten sonra Azure portal ' a gidin. ' + ', Market (' tümünü görüntüle ') öğesini seçin ve Ethereum PoA Konsorsiyumu arayın.

Aşağıdaki bölümde, ağdaki ilk üyenin parmak izini yapılandırma işleminde size yol gösterilir. Dağıtım akışı beş adıma bölünür: Temel bilgiler, dağıtım bölgeleri, ağ boyutu ve performans, Ethereum ayarları, Azure Izleyici.

#### <a name="basics"></a>Temel

**Temel bilgiler**altında, abonelik, kaynak grubu ve temel sanal makine özellikleri gibi herhangi bir dağıtım için standart parametrelerin değerlerini belirtin.

Her parametrenin ayrıntılı açıklaması aşağıdadır:

Parametre adı|Açıklama|İzin verilen değerler|Varsayılan değerler
---|---|---|---
Yeni bir ağ oluşturun veya var olan bir ağa katılarak emin misiniz?|Yeni bir ağ oluşturun veya önceden var olan bir konsorsiyum ağı ekleyin|Yeni bir JOIN oluştur var|Yeni Oluştur
E-posta adresi (Isteğe bağlı)|Dağıtımınız hakkında bilgi ile dağıtım tamamlandığında bir e-posta bildirimi alacaksınız.|Geçerli e-posta adresi|NA
VM Kullanıcı adı|Dağıtılan her VM 'nin Yönetici Kullanıcı adı (yalnızca alfasayısal karakterler)|1-64 karakter|NA
Kimlik doğrulaması türü|Sanal makinede kimlik doğrulama yöntemi.|Parola veya SSH ortak anahtarı|istemcisiyle yönetilen bir cihaz için)
Parola (kimlik doğrulama türü = parola)|Dağıtılan sanal makinelerin her biri için yönetici hesabının parolası.  Parolanın aşağıdakilerden 3 ' ü içermesi gerekir: 1 büyük harf karakter, 1 küçük harf, 1 sayı ve 1 özel karakter. Tüm VM 'Ler başlangıçta aynı parolaya sahip olsa da, sağlama sonrasında parolayı değiştirebilirsiniz.|12-72 karakter|NA
SSH anahtarı (kimlik doğrulama türü = ortak anahtar)|Uzaktan oturum açma için kullanılan güvenli kabuk anahtarı.||NA
Subscription|Consortium ağının dağıtılacağı abonelik||NA
Kaynak Grubu|Consortium ağının dağıtılacağı kaynak grubu.||NA
Location|Kaynak grubu için Azure bölgesi.||NA

Örnek dağıtım aşağıda gösterilmiştir: ![temel dikey pencere](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>Dağıtım bölgeleri

Ardından, dağıtım bölgeleri ' nin altında, konsorsiyumun sayısını ve verilen bölge sayısına göre Azure bölgelerinin seçimini yapmak için bölge sayısı girdilerini belirtin. Kullanıcı en fazla 5 bölgede dağıtım yapabilir. Temel bilgiler bölümünden kaynak grubu konumuyla eşleşecek ilk bölgeyi seçmeyi öneririz. Geliştirme veya test ağları için üye başına tek bir bölge önerilir. Üretim için, yüksek kullanılabilirlik için iki veya daha fazla bölgede dağıtım yapmanızı öneririz.

Her parametrenin ayrıntılı açıklaması aşağıdadır:

  Parametre adı|Açıklama|İzin verilen değerler|Varsayılan değerler
  ---|---|---|---
  Bölge sayısı|Konsorsiyum ağını dağıtmak için bölge sayısı|1, 2, 3, 4, 5|1\.
  İlk bölge|Consortium ağını dağıtmaya yönelik ilk bölge|Tüm izin verilen Azure bölgeleri|NA
  İkinci bölge|Consortium ağını dağıtmanın ikinci bölgesi (yalnızca bölge sayısı 2 olarak seçildiğinde görünür)|Tüm izin verilen Azure bölgeleri|NA
  Üçüncü bölge|Konsorsiyum ağını dağıtmaya yönelik üçüncü bölge (yalnızca bölge sayısı 3 olarak seçildiğinde görünür)|Tüm izin verilen Azure bölgeleri|NA
  Dördüncü bölge|Konsorsiyum ağını dağıtmaya yönelik dördüncü bölge (yalnızca bölge sayısı 4 olarak seçildiğinde görünür)|Tüm izin verilen Azure bölgeleri|NA
  Beşinci bölge|Konsorsiyum ağını dağıtmaya yönelik beşinci bölge (yalnızca bölge sayısı 5 olarak seçildiğinde görünür)|Tüm izin verilen Azure bölgeleri|NA

Örnek dağıtım aşağıda gösterilmiştir: ![dağıtım bölgeleri](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Ağ boyutu ve performansı

Ardından, ' ağ boyutu ve performans ' altında, bir konsorsiyumun, doğrulayıcı düğümlerinin sayısı ve boyutu gibi bir ağ boyutu için giriş belirtin.
Doğrulayıcı düğüm depolama boyutu, blok zincirinin olası boyutunu dikte eder. Bu, dağıtımdan sonra değiştirilebilir.

Her parametrenin ayrıntılı açıklaması aşağıdadır:

  Parametre adı|Açıklama|İzin verilen değerler|Varsayılan değerler
  ---|---|---|---
  Yük dengeli Doğrulayıcı düğümlerinin sayısı|Ağın bir parçası olarak sağlanacak Doğrulayıcı düğümlerinin sayısı|2-15|2
  Doğrulayıcı düğümü depolama performansı|Dağıtılan Doğrulayıcı düğümlerinin her birini yedekleyen yönetilen disk türü.|Standart SSD veya Premium|Standart SSD
  Doğrulayıcı düğümü sanal makine boyutu|Doğrulayıcı düğümleri için kullanılan sanal makine boyutu.|Standart A, standart D, standart D-v2, standart F serisi, standart DS ve standart FS|Standart D1 v2

[Depolama fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/managed-disks/)

[Sanal makine fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

Sanal makine ve depolama katmanı, ağ performansını etkileyecektir.  İstenen maliyet verimliliği temelinde aşağıdaki SKU 'Ların kullanılması önerilir:

  Sanal makine SKU 'SU|Depolama katmanı|Fiyat|Aktarım hızı|Gecikme süresi
  ---|---|---|---|---
  F1|Standart SSD|düşük|düşük|geniş
  D2_v3|Standart SSD|orta|orta|orta
  F16s|Premium SSD|geniş|geniş|düşük

Örnek dağıtım aşağıda gösterilmiştir: ![ağ boyutu ve performansı](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ethereum ayarları

Ardından, Ethereum ayarları altında, Ethernet KIMLIĞI ve Ethereum hesabı parolası ya da genesu bloğu gibi Ethereum ile ilgili yapılandırma ayarlarını belirtin.

Her parametrenin ayrıntılı açıklaması aşağıdadır:

  Parametre adı|Açıklama|İzin verilen değerler|Varsayılan değerler
  ---|---|---|---
Konsorsiyum üye KIMLIĞI|Konsorsiyumdan kaçınmak için IP adresi alanlarını yapılandırmak için kullanılan Consortium ağına katılan her üyeyle ilişkili KIMLIK. Özel ağ söz konusu olduğunda, üye KIMLIĞI aynı ağdaki farklı kuruluşlar arasında benzersiz olmalıdır.  Aynı kuruluş birden çok bölgeye dağıttığında bile benzersiz bir üye KIMLIĞI gereklidir. Bu parametrenin değerini, çakışma olmadığından emin olmak için diğer birleştirme üyeleriyle paylaşmanız gerekecektir.|0-255|NA
Ağ kimliği|Dağıtmakta olan konsorsiyumum ağının ağ KIMLIĞI.  Her bir Ethereum ağının kendi ağ KIMLIĞI vardır ve bu, genel ağın KIMLIĞI olan 1 ' dir.|5-999.999.999|10101010
Yönetici Ethereum adresi|PoA İdaresi içine katılmak için kullanılan Ethereum hesap adresi.  Ethereum adresini oluşturmak için MetaMask kullanmanızı öneririz.|0x ile başlayan 42 alfasayısal karakter|NA
Gelişmiş Seçenekler|Ethereum ayarları için Gelişmiş Seçenekler|Etkinleştir veya devre dışı bırak|Devre Dışı Bırak
Genel IP (Gelişmiş Seçenekler = etkinleştir)|Ağı VNet ağ geçidinin arkasında dağıtır ve eşleme erişimini kaldırır. Bu seçenek işaretliyse, bağlantının uyumlu olması için tüm üyelerin VNet Gateway kullanması gerekir.|Genel IP özel VNet|Genel IP
Blok gaz sınırı (Gelişmiş Seçenekler = etkinleştir)|Ağın başlangıç blok gaz sınırı|Herhangi bir sayısal|50000000
Yeniden mühürlemek süresi (sn)|Ağ üzerinde işlem olmadığında boş blokların oluşturulma sıklığı. Daha yüksek bir sıklık daha hızlı ve daha fazla depolama maliyetine sahip olacaktır.|Herhangi bir sayısal|15
İşlem Izin Sözleşmesi (Gelişmiş Seçenekler = etkinleştir)|Işlem Izinleri sözleşmesi için bayt kodu. Akıllı sözleşme dağıtımını ve yürütmeyi, izin verilen Ethereum hesapları listesine kısıtlar.|Sözleşme bytecode 'u|NA

Örnek dağıtım aşağıda gösterilmiştir: ![Ethereum ayarları](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>İzleme

Izleme dikey penceresi ağınız için bir Azure Izleyici günlükleri kaynağı yapılandırmanıza olanak tanır. İzleme Aracısı, ağ durumu veya hata ayıklama sorunlarını hızla denetleyebilme olanağı sunarak, ağınıza ait yararlı ölçümleri ve günlükleri toplayıp sunacaktır.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Parametre adı|Açıklama|İzin verilen değerler|Varsayılan değerler
  ---|---|---|---
İzleme|Izlemeyi etkinleştirme seçeneği|Etkinleştir veya devre dışı bırak|Etkinleştir
Mevcut Azure Izleyici günlüklerine bağlanma|Yeni bir Azure Izleyici günlükleri örneği oluşturun veya var olan bir örneğe katın|Yeni oluştur veya var olanı Birleştir|Yeni oluştur
İzleyici konumu (mevcut Azure Izleyici günlüklerine Bağlan = yeni oluştur)|Yeni Azure Izleyici günlükleri örneğinin dağıtılacağı bölge|Tüm Azure Izleyici günlük bölgeleri|NA
Mevcut Log Analytics çalışma alanı KIMLIĞI (mevcut Azure Izleyici günlüklerine Bağlan = var olanı Birleştir)|Mevcut Azure Izleyici günlükleri örneğinin çalışma alanı KIMLIĞI||NA
Mevcut Log Analytics birincil anahtarı (mevcut Azure Izleyici günlüklerine bağlanma = var olanı Birleştir)|Mevcut Azure Izleyici günlükleri örneğine bağlanmak için kullanılan birincil anahtar||NA


Örnek dağıtım aşağıda gösterilmiştir: ![Azure izleyici](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Özet

Temel dağıtım öncesi doğrulamayı çalıştırmak için ve belirtilen girişleri gözden geçirmek için Özet dikey penceresine tıklayın. Dağıtılmadan önce şablonu ve parametreleri indirebilirsiniz.

Yasal ve gizlilik koşullarını gözden geçirin ve dağıtım için ' satın al ' seçeneğine tıklayın. Dağıtım VNet ağ geçitleri içeriyorsa, dağıtım 50 45 dakika sürer.

#### <a name="post-deployment"></a>Dağıtım sonrası

##### <a name="deployment-output"></a>Dağıtım çıkışı

Dağıtım tamamlandıktan sonra, onay e-postası veya Azure portal aracılığıyla gerekli parametrelere erişebilirsiniz. Bu parametrelerde şunları bulacaksınız:

-   Ethereum RPC uç noktası

-   İdare panosu URL 'SI

-   Azure Izleyici URL 'SI

-   Veri URL 'SI

-   VNet ağ geçidi kaynak KIMLIĞI (isteğe bağlı)

##### <a name="confirmation-email"></a>Onay e-postası

Bir e-posta adresi ([temel bilgiler bölümü](#basics)) sağlarsanız, dağıtım çıkış bilgilerine sahip e-posta adresine bir e-posta gönderilir.

![Dağıtım e-postası](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portal

Dağıtım başarıyla tamamlandıktan sonra ve tüm kaynaklar sağlandıktan sonra, kaynak grubunuzda çıkış parametrelerini görüntüleyebilirsiniz.

1.  Portalda kaynak grubunuzu bulma

2.  *Dağıtımlar* 'a git

3.  Kaynak grubunuz ile aynı adda en iyi dağıtımı seçin

4.  *Çıkışları* Seç

### <a name="growing-the-consortium"></a>Konsorsiyumun büyümesi

Konsorsiyumu genişletmek için önce fiziksel ağı bağlamanız gerekir.
Bu ilk adım, genel IP tabanlı dağıtımı kullanarak sorunsuz bir şekilde gerçekleştirilir. VPN arkasında dağıtım yapıyorsanız, yeni üye dağıtımının bir parçası olarak ağ bağlantısını yapmak için [VNET ağ geçidini bağlama](#connecting-vnet-gateways) bölümüne bakın.  Dağıtımınız tamamlandıktan sonra, bir ağ yöneticisi olmak için [Idare DApp](#governance-dapp) 'yi kullanın.

#### <a name="new-member-deployment"></a>Yeni üye dağıtımı

1.  Aşağıdaki bilgileri katılım üyesiyle paylaşabilirsiniz. Bu bilgiler, dağıtım sonrası e-postanız veya Portal dağıtım çıkışında bulunabilir.

    -  Konsorsiyum verileri URL 'Si

    -  Dağıttığınız düğümlerin sayısı

    -  VNet ağ geçidi kaynak KIMLIĞI (VPN kullanılıyorsa)

2.  Dağıtım üyesi, ağ varlığını, aşağıdakileri aklınızda tutarak [dağıtmada aynı çözümü](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) kullanmalıdır:

    -  *Varolanı katılmayı* seçin

    -  Dengeli gösterimi sağlamak için ağdaki diğer üyelerin geri kalanı ile aynı sayıda Doğrulayıcı düğümünü seçin

    -  Önceki adımda sağlanmış olan Ethereum adresini kullanın

    -  *Ethereum ayarları* sekmesinde belirtilen *konsorsiyum veri URL 'sini* geçirin

    -  Ağın geri kalanı VPN 'in arkasındaysa Gelişmiş bölüm altında *özel VNET* ' i seçin.

#### <a name="connecting-vnet-gateways"></a>VNet ağ geçitlerini bağlama

Varsayılan genel IP ayarlarını kullanarak dağıttıysanız, bu adımı yoksayabilirsiniz. Özel ağ söz konusu olduğunda, farklı Üyeler VNet Gateway bağlantıları aracılığıyla bağlanır. Bir üyenin ağa katılabilmesi ve işlem trafiğini görmeniz için, mevcut bir üyenin VPN ağ geçidinde son bir yapılandırmayı bağlantıyı kabul etmesi gerekir. Bu, katılan üyenin Ethereum düğümlerinin bağlantı kurulana kadar çalıştırılmayacağı anlamına gelir. Tek bir hata noktası olasılığını azaltmak için konsorsiyumde yedekli ağ bağlantıları (ağ) oluşturmanız önerilir.

Yeni üye dağıtıldıktan sonra, mevcut üye yeni üyeye VNet ağ geçidi bağlantısı ayarlayarak iki yönlü bağlantıyı tamamlamalıdır. Bunu başarmak için, mevcut üyeye şunlar gerekir:

1.  Bağlanan üyenin VNet Gateway RESOURCEID (bkz. dağıtım çıktısı)

2.  Paylaşılan bağlantı anahtarı

Mevcut üyenin bağlantıyı tamamlaması için aşağıdaki PowerShell betiğini çalıştırması gerekir. Portalın sağ üst gezinti çubuğunda bulunan Azure Cloud Shell kullanmanızı öneririz.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>Hizmet izleme

Dağıtım e-postasında bağlantıyı izleyerek veya dağıtım \[çıkışı OMS\_portalı\_URL 'sindeki\]parametreyi bularak Azure izleyici portalınızı bulabilirsiniz.

Portal önce üst düzey ağ istatistiklerini ve düğüme genel bakış görüntülenir.

![izleme kategorileri](./media/ethereum-poa-deployment/monitor-categories.png)

**Düğüm genel bakış** seçimine, düğüm başına altyapı istatistiklerini görüntülemek için sizi bir portala yönlendirirsiniz.

![düğüm istatistikleri](./media/ethereum-poa-deployment/node-stats.png)

**Ağ istatistiklerini** seçmek sizi Ethereum ağ istatistiklerini görüntülemenize yönlendirir.

![Ağ İstatistikleri](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Örnek kusto sorguları

Bu panoların arkasında bir sorgulanabilir ham Günlükler kümesi bulunur. Bu ham günlükleri panoları özelleştirmek, sorunları araştırmak veya eşik uyarısı ayarlamak için kullanabilirsiniz. Aşağıda, günlük araması aracında çalıştıracağımız örnek bir sorgu kümesi bulacaksınız:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Birden fazla Doğrulayıcı tarafından raporlanan blokları listeler. Zincir çatallarını bulmaya yardımcı olması yararlı olur.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>5 dakikalık demetlerden ortalama olarak belirtilen bir doğrulayıcı düğümü için Ortalama eşdüzey sayı alın.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>SSH erişimi

Güvenlik nedenleriyle, SSH bağlantı noktası erişimi varsayılan olarak bir ağ grubu güvenlik kuralı tarafından reddedilir. POA ağındaki sanal makine örneklerine erişmek için bu kuralı izin ver olarak \"değiştirmeniz gerekir\"

1.  Azure portal ' den dağıtılan kaynak grubunun genel bakış bölümünden başlayın.

    ![SSH genel bakış](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Erişmek istediğiniz VM 'nin bölgesi için ağ güvenlik grubunu seçin

    ![SSH NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Allow-SSH\"kuralınıseçin \"

    ![SSH-izin ver](./media/ethereum-poa-deployment/ssh-allow.png)

4.  \"Eylemi\" izin ver olarak değiştir

    ![SSH etkinleştirme izin ver](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  \"Kaydet\" ' e tıklayın (değişikliklerin uygulanması birkaç dakika sürebilir)

Artık, belirtilen Yönetici Kullanıcı adı ve parola/SSH anahtarı ile SSH aracılığıyla Doğrulayıcı düğümlerine yönelik sanal makinelere uzaktan bağlanabilirsiniz.
İlk Doğrulayıcı düğümüne erişmek için çalıştırılacak SSH komutu, şablon dağıtım çıkış parametresinde, ' SSH\_to\_First\_VL\_node\_REGION1 ' olarak listelenir (örnek dağıtım için: SSH-p 4000 poaadmin\@leader4vb.eastus.cloudapp.Azure.com). Ek işlem düğümlerine ulaşmak için, bağlantı noktası numarasını bir kez artırın (örneğin, ilk işlem düğümü 4000 numaralı bağlantı noktasıdır).

Birden fazla bölgeye dağıttıysanız, yukarıdaki komutu bu bölgedeki yük dengeleyicinin DNS adı veya IP adresi ile değiştirin. Diğer bölgelerin DNS adını veya IP adresini bulmak için, \*adlandırma kuralı \* \* \* \*-lbpı-reg\#olan kaynağı bulun ve DNS adını ve IP adresi özelliklerini görüntüleyin.

### <a name="azure-traffic-manager-load-balancing"></a>Azure Traffic Manager yük dengelemesi

Azure Traffic Manager, gelen trafiği farklı bölgelerde birden fazla dağıtımda yönlendirerek, kapalı kalma süresini azaltabilir ve ağ yanıt hızını artırır. Yerleşik sistem durumu denetimleri ve otomatik yeniden yönlendirme, RPC uç noktalarının ve Idare DApp 'nin yüksek oranda kullanılabilir olmasını sağlamaya yardımcı olur. Bu özellik, birden fazla bölgeye dağıttığınız ve üretime hazırsanız faydalıdır.

Traffic Manager'ı aşağıdaki amaçlarla kullanabilirsiniz:

-   Otomatik yük devretme ile bir ağ kullanılabilirliğini geliştirme.

-   Son kullanıcıları en düşük ağ gecikmesi ile Azure konumuna yönlendirerek ağlarınızın yanıt verme hızını artırın.

Bir Traffic Manager profili oluşturmaya karar verirseniz, ağınıza erişmek için profilin DNS adını kullanabilirsiniz. Ağa diğer konsorsiyum üyeleri eklendikten sonra, dağıtılan doğrulayıcıların yükünü dengelemek için de Traffic Manager kullanılabilir.

#### <a name="creating-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma

Azure Portal kaynak \" Oluşturdüğmesinetıkladıktan\"sonra\" Traffic Manager profili arayın ve seçin.\"

![Azure Traffic Manager 'ı arama](./media/ethereum-poa-deployment/traffic-manager-search.png)

Profile benzersiz bir ad verin ve PoA dağıtımı sırasında oluşturulan kaynak grubunu seçin. Dağıtmak için "Oluştur" düğmesine tıklayın.

![Traffic Manager oluşturma](./media/ethereum-poa-deployment/traffic-manager-create.png)

Dağıtıldıktan sonra kaynak grubundaki örneği seçin. Traffic Manager 'a erişim için DNS adı genel bakış sekmesinde bulunabilir

![Traffic Manager DNS 'yi bulma](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Uç noktalar sekmesini seçin ve Ekle düğmesine tıklayın. Uç noktaya benzersiz bir ad verin. Hedef kaynak türünü genel IP adresi olarak değiştirin. Ardından, ilk bölgenin\'yük dengeleyicinin genel IP adresini seçin.

![Yönlendirme trafiği Yöneticisi](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Dağıtılan ağdaki her bölge için tekrarlayın. Uç noktalar \"etkin\" duruma getirildikten sonra otomatik olarak yüklenir ve trafik yöneticisinin DNS adına bölge dengelemesi yapılır. Artık bu DNS adını belgenin diğer \[adımlarında konsorsiyum\_veri\_URL 'si\] parametresi yerine kullanabilirsiniz.

### <a name="data-api"></a>Veri API 'SI

Her bir konsorsiyum üyesi başkalarının ağa bağlanması için gerekli bilgileri barındırır. Mevcut üye, üyenin dağıtımından önce [CONSORTIUM_DATA_URL] sağlar. Dağıtım sonrasında, bir katılım üyesi JSON arabiriminden aşağıdaki uç noktada bilgi alacaktır:

`<CONSORTIUM_DATA_URL>/networkinfo`

Yanıt, üyeleri (Genessıs bloğu, doğrulayıcı kümesi sözleşmesi ABı, bootnodes) ve mevcut üye için yararlı olan bilgileri (Doğrulayıcı adresleri) birleştirmek için yararlı bilgiler içerir. Bu standartlaştırma 'yi bulut sağlayıcıları genelinde konsorsiyunuzu uzatmak için kullanmanızı öneririz. Bu API aşağıdaki yapıyla JSON biçimli bir yanıt döndürür:
```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```
## <a name="tutorials"></a>Öğreticiler

### <a name="programmatically-interacting-with-a-smart-contract"></a>Programlı bir şekilde akıllı sözleşmeyle etkileşim kurma

> [!WARNING]
> Ethereum özel anahtarınızı hiçbir şekilde ağ üzerinden göndermeyin! Her bir işlemin önce yerel olarak imzalandığından ve imzalı işlemin ağ üzerinden gönderildiğinden emin olun.

Aşağıdaki örnekte, ethereumjs *-Wallet* ' ı kullanarak yerel olarak Imzalanacak bir ethereumjs *-TX* ve ham IŞLEMI Ethereum RPC uç noktasına göndermek için *Web3* .

Bu örnek için bu basit Merhaba-Dünya Akıllı sözleşmesini kullanacağız:

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text;
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

Bu örnekte, sözleşmenin zaten dağıtıldığı varsayılır. Bir sözleşmeyi programlı bir şekilde dağıtmak için *Solc* ve *Web3* kullanabilirsiniz. Önce aşağıdaki düğüm modüllerini yüklemeniz gerekir:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Bu nodeJS betiği şunları yapar:

-   Ham işlem oluşturun: postMsg

-   Oluşturulan özel anahtarı kullanarak işlemi imzalayın

-   İmzalı işlemi Ethereum ağına gönder

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

// TODO Replace with your RPC endpoint
var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));

// Get the current nonce of the account
web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00',
     gasLimit: '0x2FAF080',
     to: address,
     value: '0x00',
     data: data
   }
   var tx = new ethereumjs(rawTx);

   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="deploy-smart-contract-with-truffle"></a>Truffle ile akıllı sözleşme dağıtma

-   Gerekli kitaplıkları yükler

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   Truffle. js ' de, MetaMask hesabınızın kilidini açmak için aşağıdaki kodu ekleyin ve anımsatıcı tümceciğini (MetaMask/ayarlar/açığa çıkar çekirdek sözcüklerini) girerek PoA düğümünü giriş noktası olarak yapılandırın

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var rpc_endpoint = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    poa: {
      provider: new HDWalletProvider(mnemonic, rpc_endpoint),
      network_id: 3,
      gasPrice : 0
    }
  }
};

```

-   PoA ağına dağıtın

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Truffle ile akıllı sözleşmede hata ayıklama

Truffle akıllı sözleşmede hata ayıklama için kullanılabilen bir yerel geliştirme ağı içerir. Öğreticiyi tam olarak [buradan](https://truffleframework.com/tutorials/debugging-a-smart-contract)edinebilirsiniz.

### <a name="webassembly-wasm-support"></a>WebAssembly (ıSSTREAM) desteği

WebAssembly desteği yeni dağıtılan PoA ağlarında zaten etkin. Web derlemesine (Rust, C, C++) transpiles olan herhangi bir dilde akıllı anlaşma geliştirmesi sağlar. Daha fazla bilgi için aşağıdaki bağlantılara bakın

-   WebAssembly 'A eşlik genel bakışı-<https://wiki.parity.io/WebAssembly-Home>

-   Eşlik üzerinden teknik eğitim-<https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>Başvuru

### <a name="faq"></a>SSS

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Ağda gönderdiğim\'çok sayıda işlem olduğunu fark ediyorum. Bunlar nereden geliyor?

[KIşIsel API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)'nin kilidini açmak güvenli değildir. Botlar, kilitlenmemiş Ethereum hesaplarını dinler ve fonları boşaltmaya çalışır. Bot, bu hesapların gerçek zamanlı olarak olduğunu varsayar ve bakiyedeki ilk olarak bu hesapları yapmaya çalışır. Ağda kişisel API 'yi etkinleştirmeyin. Bunun yerine, MetaMask gibi bir cüzdan kullanarak el ile veya program aracılığıyla [akıllı bir sözleşmeyle etkileşim kuran](#programmatically-interacting-with-a-smart-contract)bölümde özetlendiği gibi, işlemleri önceden imzalayın.

#### <a name="how-to-ssh-onto-a-vm"></a>VM üzerinde SSH kullanma

SSH bağlantı noktası güvenlik nedenleriyle gösterilmez. [SSH bağlantı noktasını etkinleştirmek için bu kılavuzu](#ssh-access)izleyin.

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Nasıl yaparım? bir denetim üyesi veya işlem düğümleri ayarlamak mı istiyorsunuz?

İşlem düğümleri, ağla ilişkilendirilen ancak Consensus 'a katılmayan bir dizi eşlik istemcisi. Bu düğümler, Ethereum işlemlerini göndermek ve akıllı sözleşmenin durumunu okumak için hala kullanılabilir.
Bu, ağ üzerindeki yetkili olmayan konsorsiyum üyelerine seslebilirlik sağlamaya yönelik bir mekanizma olarak da geçerlidir. Bunu başarmak için, konsorsiyumun büyümesinin 2. adımını izlemeniz yeterlidir.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Neden MetaMask işlemleri uzun sürüyor?

İşlemlerin doğru sırada alındığından emin olmak için, her bir Ethereum işlemi bir artırma nonce ile gelir. Farklı bir ağda MetaMask içinde bir hesap kullandıysanız, nonce değerini sıfırlamanız gerekir. Ayarlar simgesine (3-çubuklar), ayarlar, hesabı Sıfırla ' ya tıklayın. İşlem geçmişi silinecek ve artık işlemi yeniden gönderebilirsiniz.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>MetaMask 'te gaz ücreti belirtmem gerekir mi?

Ether, yetkili bir konsorsiyumun bir amacını vermez. Bu nedenle, MetaMask içinde işlem gönderilirken gaz ücreti belirtmeye gerek yoktur.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Azure OMS 'yi sağlama hatası nedeniyle dağıtımım başarısız olursa ne yapmam gerekir?

İzleme isteğe bağlı bir özelliktir. Azure izleyici kaynağını başarıyla sağlayamamasından dolayı dağıtımınızın başarısız olduğu bazı nadir durumlarda Azure Izleyici olmadan yeniden dağıtabilirsiniz.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Özel ağ dağıtımlarıyla uyumlu genel IP dağıtımları mı var?

Hayır, eşleme iki yönlü iletişim gerektirir, böylece tüm ağ genel veya özel olmalıdır.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Yetki kanıtlama için beklenen işlem performansı nedir?

İşlem aktarım hızı, işlem türlerine ve ağ topolojisine bağlı olarak yüksek ölçüde bağımlıdır.  Basit işlemler kullanarak, birden çok bölgede dağıtılan bir ağ ile saniye başına ortalama 400 işlem işaretledi.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Nasıl yaparım? akıllı sözleşme olaylarına abone misiniz?

Ethereum yetki kanıtı artık Web-Sockets 'i desteklemektedir.  Web yuvası URL 'sini ve bağlantı noktasını bulmak için dağıtım e-postanızı veya dağıtım çıktılarınızı denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Ethereum yetkili bir Konsorsiyumu](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) çözümünü kullanarak başlayın.
