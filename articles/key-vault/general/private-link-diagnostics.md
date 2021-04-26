---
title: Azure Key Vault'ta özel bağlantıları yapılandırma sorunlarını tanılama
description: Key Vault ve ayrıntılı ortak özel bağlantı sorunlarını yapılandırma ile çözümleyin
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 03abe4e4e098d46060e33ba114872905e54a443f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96317086"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Azure Key Vault'ta özel bağlantıları yapılandırma sorunlarını tanılama

## <a name="introduction"></a>Giriş

Bu makale, kullanıcıların Key Vault ve özel bağlantılar özelliği ile ilgili sorunları tanılamasına ve düzeltmenize yardımcı olur. Bu kılavuz, ilk kez çalışan özel bağlantıları alma veya bazı değişiklikler nedeniyle özel bağlantıların çalışmayı durdurduğu bir durumu düzeltmek için yapılandırma yönünden yardımcı olur.

Bu özelliğe yeni bir özelliktir, bkz. [Azure özel bağlantısı ile Key Vault tümleştirme](private-link-service.md).

### <a name="problems-covered-by-this-article"></a>Bu makalede ele alınan sorunlar

- DNS sorgularınız, özel bağlantılar özelliğini kullanmayı bekleeceğiniz özel bir IP adresi yerine Anahtar Kasası için genel bir IP adresi döndürür.
- Özel bağlantı kullanan belirli bir istemci tarafından yapılan tüm istekler, zaman aşımları veya ağ hatalarıyla başarısız olur ve sorun aralıklı değildir.
- Anahtar Kasası özel bir IP adresine sahiptir, ancak istekler hala `403` iç hata koduyla yanıt almaya devam eder `ForbiddenByFirewall` .
- Özel bağlantılar kullanıyorsunuz, ancak anahtar kasanız hala genel Internet 'ten gelen istekleri kabul ediyor.
- Anahtar kasasında iki özel uç nokta vardır. Bir tane kullanan istekler sorunsuz çalışıyor, ancak diğerini kullanan istekler başarısız oluyor.
- Özel bağlantılar kullanan başka bir aboneliğiniz, Anahtar Kasası veya sanal ağınız vardır. Yeni bir benzer dağıtım yapmak istiyorsunuz, ancak burada çalışan özel bağlantıları alamazsınız.

### <a name="problems-not-covered-by-this-article"></a>Bu makalede kapsanmayan sorunlar

- Aralıklı bir bağlantı sorunu var. Belirli bir istemcide, çalışan bazı istekler ve bazıları çalışmıyor görürsünüz. *Aralıklı sorunlar genellikle özel bağlantı yapılandırmasındaki bir sorundan kaynaklanmamalıdır; Bunlar ağ veya istemci aşırı yüklemesi için bir imzalardır.*
- BYOK (Kendi Anahtarını Getir), CMK (müşteri tarafından yönetilen anahtarlar) veya Anahtar Kasası 'nda depolanan gizli dizileri erişimi destekleyen bir Azure ürünü kullanıyorsunuz. Anahtar Kasası ayarlarında güvenlik duvarını etkinleştirdiğinizde, bu ürün anahtar kasanıza erişemez. *Ürüne özgü belgelere bakın. Güvenlik Duvarı etkin olan anahtar kasaları için açık bir şekilde destek belirttiğinizden emin olun. Gerekirse, söz konusu ürün için desteğe başvurun.*

### <a name="how-to-read-this-article"></a>Bu makaleyi okuma

Özel bağlantılarla ilgili yeni bir bağlantı kullanıyorsanız veya karmaşık bir dağıtımı değerlendirdiğiniz takdirde, makalenin tamamını okumanız önerilir. Aksi takdirde, karşılaştığınız sorunu daha anlamlı hale getiren bölümü seçebilirsiniz.

Haydi başlayalım!

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. istemci bağlantısının sahip olduğunu doğrulayın

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>İstemcinizin sanal ağda çalıştığını onaylayın

Bu kılavuz, uygulama kodundan kaynaklanan anahtar kasasındaki bağlantıları düzeltgetirmenize yardımcı olmaya yöneliktir. Azure sanal makineler, Azure Service Fabric kümeleri, Azure App Service, Azure Kubernetes hizmeti (AKS) ve benzeri diğer uygulamalarda yürütülen uygulamalar ve betikler aşağıda verilmiştir. Bu kılavuz, tarayıcının anahtar kasanıza doğrudan eriştiği Azure portal web tabanlı kullanıcı arabiriminde gerçekleştirilen erişimler için de geçerlidir.

Özel bağlantıların tanımına göre, uygulama, betik veya portalın, [Özel uç nokta kaynağının](../../private-link/private-endpoint-overview.md) dağıtıldığı sanal ağa bağlı makine, küme veya ortamda çalışıyor olması gerekir.

Uygulama, komut dosyası veya Portal rastgele Internet 'e bağlı bir ağda çalışıyorsa, bu kılavuz geçerli DEĞILDIR ve olası özel bağlantılar kullanılamaz. Bu sınırlama, kullanıcı tarayıcısı yerine isteğe bağlı olarak sunulan uzak bir Azure makinesinde çalıştırıldıklarından, Azure Cloud Shell yürütülen komutlara da uygulanabilir.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>Yönetilen bir çözüm kullanıyorsanız, belirli belgelere bakın

Bu kılavuz, Microsoft tarafından yönetilen, anahtar kasasının müşteri sanal ağından bağımsız olarak bulunan bir Azure ürünü tarafından erişildiği çözümler için geçerli DEĞILDIR. Bu senaryolara örnek olarak Azure Storage veya bekleyen şifreleme için yapılandırılmış Azure SQL, Azure Event hub, müşteri tarafından sunulan anahtarlarla verileri şifrelemek Azure Data Factory, anahtar kasasındaki gizli dizileri almak Azure Pipelines ve diğer benzer senaryolardır. Bu durumlarda, *ürünün güvenlik duvarı etkinken anahtar kasalarını destekleyip desteklemediğini kontrol etmeniz gerekir*. Bu destek genellikle Key Vault güvenlik duvarının [güvenilir hizmetler](overview-vnet-service-endpoints.md#trusted-services) özelliği ile gerçekleştirilir. Ancak, birçok ürün, güvenilen Hizmetler listesine çeşitli nedenlerle dahil değildir. Bu durumda, ürüne özgü desteğe ulaşın.

Az sayıda Azure ürünü, *VNET ekleme* kavramını destekler. Basit koşullarda, ürün müşteri sanal ağına bir ağ aygıtı ekler ve bu da istekleri sanal ağa dağıtılmışsa gibi göndermesini sağlar. Bir önemli örneği [Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Bu gibi ürünler, özel bağlantıları kullanarak anahtar kasasında istek yapabilir ve bu sorun giderme kılavuzu size yardımcı olabilir.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. bağlantının onaylandığını ve başarılı olduğunu doğrulayın

Aşağıdaki adımlar özel uç nokta bağlantısının onaylandığını ve başarılı olduğunu doğrular:

1. Azure portal açın ve Anahtar Kasası kaynağınızı açın.
2. Sol menüde **ağ**' ı seçin.
3. **Özel uç nokta bağlantıları** sekmesine tıklayın. Bu, tüm özel uç nokta bağlantılarını ve ilgili durumlarını gösterir. Bağlantı yoksa veya sanal ağınız için bağlantı eksikse, yeni bir özel uç nokta oluşturmanız gerekir. Bu daha sonra ele alınacaktır.
4. Hala **Özel uç nokta bağlantılarında**, tanıladığınız bir tane bulun ve "bağlantı durumu" nın **onaylandığını** ve "sağlama durumu" nın **başarılı** olduğunu onaylayın.
    - Bağlantı "bekliyor" durumundaysa, bunu yalnızca onaylamanız mümkün olabilir.
    - Bağlantı "reddedildi", "başarısız", "hata", "bağlantısı kesildi" veya başka bir durum için geçerli değilse, yeni bir özel uç nokta kaynağı oluşturmanız gerekir.

İşlerin temiz tutulması için verimsiz bağlantıları silmek iyi bir fikirdir.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. Anahtar Kasası güvenlik duvarının düzgün yapılandırıldığını onaylayın

>[!IMPORTANT]
> Güvenlik Duvarı ayarlarının değiştirilmesi, hala özel bağlantıları kullanmayan meşru istemcilerden erişimi kaldırabilir. Güvenlik Duvarı yapılandırmasındaki her bir değişikliğin etkilerini anladığınızdan emin olun.

Önemli bir kavram, özel bağlantılar özelliğinin yalnızca veri alımını engellemek için kapalı bir sanal ağda anahtar kasanıza erişim *izni verdiği* bir kavramdır. Var olan erişimi *kaldırmaz* . Genel Internet 'ten gelen erişimleri etkin bir şekilde engellemek için, Anahtar Kasası güvenlik duvarını açıkça etkinleştirmeniz gerekir:

1. Azure portal açın ve Anahtar Kasası kaynağınızı açın.
2. Sol menüde **ağ**' ı seçin.
3. Üst düzey **güvenlik duvarları ve sanal ağlar** sekmesinin seçili olduğundan emin olun.
4. **Özel uç nokta ve seçili ağların** seçeneğinin seçili olduğundan emin olun. **Tüm ağları** Seç ' i seçerseniz, dış istemcilerin neden hala anahtar kasasına erişebileceğini açıklar.

Aşağıdaki deyimler güvenlik duvarı ayarları için de geçerlidir:

- Özel bağlantılar özelliği, Anahtar Kasası güvenlik duvarı ayarlarında "sanal ağ" belirtilmesini gerektirmez. Anahtar Kasası güvenlik duvarı ayarlarında sanal ağ belirtilmese bile, anahtar kasasının özel IP adresini kullanan tüm istekler (sonraki bölüme bakın) çalışmalıdır.
- Özel bağlantılar özelliği, Anahtar Kasası güvenlik duvarı ayarlarında herhangi bir IP adresi belirtilmesini gerektirmez. Yine, güvenlik duvarı ayarlarında hiçbir IP adresi belirtilmediği halde, anahtar kasasının özel IP adresini kullanan tüm istekler çalışmalıdır.

Özel bağlantılar kullanıyorsanız, Anahtar Kasası güvenlik duvarında sanal ağ veya IP adresi belirtmenin tek yapmanız şu şekilde yapılır:

- Bazı istemcilerin özel bağlantıları kullanıldığı bir karma sisteminiz vardır, bazıları bazı hizmet uç noktaları, bazı genel IP adreslerini kullanır.
- Özel bağlantılara geçiş yapıyorsanız. Bu durumda, tüm istemcilerin özel bağlantıları kullandığını onaylamak için, Anahtar Kasası güvenlik duvarı ayarlarından sanal ağları ve IP adreslerini kaldırmanız gerekir.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. anahtar kasasının özel bir IP adresi olduğundan emin olun

### <a name="difference-between-private-and-public-ip-addresses"></a>Özel ve genel IP adresleri arasındaki fark

Özel bir IP adresi, her zaman aşağıdaki biçimlerden birine sahiptir:

- 10. x. x. x: örnekler: `10.1.2.3` , `10.56.34.12` .
- 172.16. x. x-172.32. x. x: örnekler: `172.20.1.1` , `172.31.67.89` .
- 192.168. x. x: örnekler: `192.168.0.1` , `192.168.100.7`

Belirli IP adresleri ve aralıklar ayrılmıştır:

- 224. x. x. x: çok noktaya yayın
- 255.255.255.255: yayın
- 127. x. x. x: geri döngü
- 169.254. x. x: bağlantı-yerel
- 168.63.129.16: iç DNS

Diğer tüm IP adresleri herkese açıktır.

Portala gözatarken veya IP adresini gösteren bir komut çalıştırdığınızda, IP adresinin özel, genel veya ayrılmış olduğunu belirleyediğinizden emin olun. Özel bağlantıların çalışması için, Anahtar Kasası ana bilgisayar adı, sanal ağ adres alanına ait olan özel bir IP adresine çözümlenmelidir.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>Sanal ağda Anahtar Kasası özel IP adresini bulma

Ana bilgisayar adı çözümlemesini tanılamanıza ve özel bağlantılar etkin olan anahtar kasasının tam özel IP adresini bilmeniz gerekir. Bu adresi bulmak için şu yordamı izleyin:

1. Azure portal açın ve Anahtar Kasası kaynağınızı açın.
2. Sol menüde **ağ**' ı seçin.
3. **Özel uç nokta bağlantıları** sekmesine tıklayın. Bu, tüm özel uç nokta bağlantılarını ve ilgili durumlarını gösterir.
4. Tanıladığınız bir tane bulun ve "bağlantı durumu" nın **onaylandığını** ve sağlama durumunun **başarılı** olduğunu doğrulayın. Bunu görmüyorsanız, bu belgenin önceki bölümlerine geri dönün.
5. Doğru öğeyi bulduğunuzda, **Özel uç nokta** sütunundaki bağlantıya tıklayın. Bu, Özel uç nokta kaynağını açar.
6. Genel Bakış sayfasında **özel DNS ayarları** adlı bir bölüm gösterilebilir. Anahtar Kasası ana bilgisayar adı ile eşleşen yalnızca bir giriş olduğunu doğrulayın. Bu giriş, Anahtar Kasası özel IP adresini gösterir.
7. Ayrıca, **ağ arabirimindeki** bağlantıyı tıklatabilir ve özel IP adresinin önceki adımda görüntülenen aynı olduğunu doğrulayabilirsiniz. Ağ arabirimi, anahtar kasasını temsil eden bir sanal aygıttır.

IP adresi, VM 'Lerin ve *aynı sanal ağda çalışan* diğer cihazların anahtar kasasına bağlanmak için kullanacağı adrestir. IP adresini unutmayın veya tarayıcı sekmesini açık tutun ve başka araştırmalar yaparken dokunmayın.

>[!NOTE]
> Anahtar kasasında birden çok özel uç nokta varsa, birden çok özel IP adresi vardır. Bu yalnızca, her biri kendi özel uç noktası (Özel uç nokta tek bir sanal ağa ait) ile aynı anahtar kasasına erişen birden fazla sanal ağınız varsa yararlıdır. Doğru sanal ağ için sorunu tanılamanıza ve Yukarıdaki yordamda doğru özel uç nokta bağlantısını seçtiğinizden emin olun. Ayrıca, aynı sanal ağdaki aynı Key Vault için birden çok özel uç **nokta oluşturmayın.** Bu gerekli değildir ve karışıklık kaynağıdır.

## <a name="5-validate-the-dns-resolution"></a>5. DNS çözümlemesini doğrulama

DNS çözümlemesi, Anahtar Kasası ana bilgisayar adını (örnek:) `fabrikam.vault.azure.net` BIR IP adresine (örnek:) çevirme işlemidir `10.1.2.3` . Aşağıdaki alt bölümlerde her bir senaryoda DNS çözümünün beklenen sonuçları gösterilmektedir.

### <a name="key-vaults-without-private-link"></a>Özel bağlantı olmadan anahtar kasaları

Bu bölüm, öğrenme amaçlarına yöneliktir. Anahtar kasasının onaylanan durumda özel uç nokta bağlantısı olmadığında, ana bilgisayar adının çözümlenmesi şuna benzer bir sonuç verir:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Adın bir genel IP adresine çözümlendiğini ve `privatelink` diğer ad olmadığını görebilirsiniz. Diğer ad daha sonra açıklanmaz, artık bu konuda endişelenmeyin.

Yukarıdaki sonuç, makinenin sanal ağa bağlı olmasından bağımsız olarak veya Internet bağlantısı olan rastgele bir makine olması durumunda beklenmektedir. Bu durum, anahtar kasasının onaylanan durumda özel uç nokta bağlantısı olmadığından ve bu nedenle özel bağlantıları desteklemek için anahtar kasasının gerekli olmaması nedeniyle oluşur.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>Rastgele Internet makinesinden özel bağlantı çözümleyen Anahtar Kasası

Anahtar kasasında onaylanan durumda bir veya daha fazla özel uç nokta bağlantısı olduğunda ve ana bilgisayar adını Internet 'e bağlı rastgele bir makineden çözümlediğinizde (Özel uç noktanın bulunduğu sanal ağa bağlı *olmayan* bir makine), şunu bulabilirsiniz:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Önceki senaryodan gelen önemli fark, değeri olan yeni bir diğer ad olur `{vaultname}.privatelink.vaultcore.azure.net` . Bu, Anahtar Kasası veri düzlemi 'nin özel bağlantılardan gelen istekleri kabul etmeye hazır olduğu anlamına gelir.

Sanal ağ *dışındaki* makinelerden (az önce kullandığınız gibi) gerçekleştirilen isteklerin özel bağlantıları kullanacağı anlamına gelmez. Ana bilgisayar adının hala genel bir IP adresine çözümlendiğini göreceksiniz. Yalnızca *sanal ağa bağlı* makineler özel bağlantıları kullanabilir. Bu konuda daha fazla bilgi alınacaktır.

`privatelink`Diğer adı görmüyorsanız, anahtar kasasının durumunda sıfır özel uç nokta bağlantısı olduğu anlamına gelir `Approved` . Yeniden denemeden önce [Bu bölüme](#2-confirm-that-the-connection-is-approved-and-succeeded) geri dönün.

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Sanal ağdan özel bağlantı çözümlemesi olan Anahtar Kasası

Anahtar kasasında onaylanan durumda bir veya daha fazla özel uç nokta bağlantısı varsa ve ana bilgisayar adını özel uç noktanın oluşturulduğu sanal ağa bağlı bir makineden çözümlediğinizde, beklenen yanıt şu olur:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  10.1.2.3
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3
```

İki önemli farkı vardır. İlk olarak, ad özel bir IP adresine çözümlenir. Bu, bu makalenin [karşılık gelen bölümünde](#find-the-key-vault-private-ip-address-in-the-virtual-network) BULDUĞUMUZ IP adresi olmalıdır. İkincisi, bundan sonra başka bir diğer ad yoktur `privatelink` . Bunun nedeni, sanal ağ DNS sunucularının diğer ad zincirini *kesmesini* ve özel IP adresini doğrudan adından döndürmesidir `fabrikam.privatelink.vaultcore.azure.net` . Bu giriş aslında `A` özel DNS bölgesindeki bir kayıttır. Bu konuda daha fazla bilgi alınacaktır.

>[!NOTE]
> Yukarıdaki sonuç yalnızca özel uç noktanın oluşturulduğu sanal ağa bağlı bir sanal makinede gerçekleşir. Sanal ağda özel uç noktayı içeren bir sanal makıne yoksa, bir tane dağıtın ve bu ağa uzaktan bağlanın, ardından `nslookup` Yukarıdaki komutu (Windows) veya `host` komutunu (Linux) yürütün.

Bu komutları özel uç noktanın oluşturulduğu sanal ağa bağlı bir sanal makinede çalıştırırsanız ve Anahtar Kasası özel IP adresini **göstermiyorsa** , sonraki bölüm sorunu gidermeye yardımcı olabilir.

## <a name="6-validate-the-private-dns-zone"></a>6. Özel DNS bölgeyi doğrulama

DNS çözümlemesi, önceki bölümde açıklandığı gibi çalışmıyorsa, Özel DNS bölgenize bir sorun olabilir ve bu bölüm yardımcı olabilir. DNS çözümlemesi doğru Anahtar Kasası özel IP adresini gösteriyorsa, Özel DNS bölgeniz muhtemelen doğru olur. Bu bölümün tamamını atlayabilirsiniz.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Gerekli Özel DNS bölgesi kaynağının var olduğunu onaylayın

Azure aboneliğinizin bu tam adı taşıyan bir [özel DNS bölge](../../dns/private-dns-privatednszone.md) kaynağı olmalıdır:

`privatelink.vaultcore.azure.net`

Portalda abonelik sayfasına giderek ve Sol menüdeki "kaynaklar" seçeneğini belirleyerek bu kaynağın varolup olmadığını kontrol edebilirsiniz. Kaynak adı olmalıdır `privatelink.vaultcore.azure.net` ve kaynak türü **özel DNS bölge** olmalıdır.

Normalde bu kaynak, ortak bir yordam kullanarak özel bir uç nokta oluşturduğunuzda otomatik olarak oluşturulur. Ancak bu kaynağın otomatik olarak oluşturulabileceği durumlar vardır ve bunu el ile yapmanız gerekir. Bu kaynak yanlışlıkla da silinmiş olabilir.

Bu kaynağınız yoksa aboneliğinizde yeni bir Özel DNS bölge kaynağı oluşturun. Adın `privatelink.vaultcore.azure.net` , boşluk veya ek noktalar olmadan tam olarak olması gerektiğini unutmayın. Yanlış adı belirtirseniz, bu makalede açıklanan ad çözümlemesi çalışmaz. Bu kaynağı oluşturma hakkında daha fazla bilgi için, bkz. [Azure Portal kullanarak Azure özel DNS bölgesi oluşturma](../../dns/private-dns-getstarted-portal.md). Bu sayfayı izlerseniz, bu noktada zaten bir tane olması gerektiğinden, sanal ağ oluşturma işlemini atlayabilirsiniz. Ayrıca, sanal makinelerle doğrulama yordamlarını atlayabilirsiniz.

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Özel DNS bölgesinin sanal ağa bağlı olduğunu onaylayın

Özel DNS bölgenin olması yeterli değildir. Ayrıca, Özel uç noktasını içeren sanal ağa bağlanmalıdır. Özel DNS bölgesi doğru sanal ağa bağlı değilse, bu sanal ağdan gelen herhangi bir DNS çözümlemesi Özel DNS bölgesini yoksayar.

Özel DNS bölgesi kaynağını açın ve Sol menüdeki **sanal ağ bağlantıları** seçeneğine tıklayın. Bu, her biri aboneliğinizin bir Sanal Ağa gelen adına sahip bağlantıların bir listesini gösterir. Özel uç nokta kaynağını içeren sanal ağın burada listelenmesi gerekir. Bu yoksa, ekleyin. Ayrıntılı adımlar için bkz. [sanal ağı bağlama](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network). "Otomatik kaydolmayı etkinleştir" işaretini kaldırın-Bu özellik özel bağlantılarla ilgili değildir.

Özel DNS bölgesi sanal ağa bağlandıktan sonra, sanal ağdan kaynaklanan DNS istekleri Özel DNS bölgesindeki adları arar. Bu, Özel uç noktanın oluşturulduğu sanal ağa bağlı sanal makinelerde gerçekleştirilen doğru adres çözümlemesi için gereklidir.

>[!NOTE]
> Bağlantıyı az önce kaydettiyseniz, Portal işlem tamamlandıktan sonra bile bu işlemin etkili olması biraz zaman alabilir. Ayrıca, DNS çözümlemesini test etmek için kullandığınız VM 'yi yeniden başlatmanız gerekebilir.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>Özel DNS bölgesinin doğru bir kaydı içerdiğini onaylayın

Portalı kullanarak Özel DNS bölgeyi adıyla açın `privatelink.vaultcore.azure.net` . Genel Bakış sayfası tüm kayıtları gösterir. Varsayılan olarak, ad ve türe sahip bir kayıt olacaktır `@` , bu `SOA` da yetki başlangıcı anlamına gelir. Bunu dokunmayın.

Anahtar Kasası adı çözümlemenin çalışması için, `A` sonek veya noktalar içermeyen basit kasa adına sahip bir kayıt olmalıdır. Örneğin, ana bilgisayar adı ise, `fabrikam.vault.azure.net` `A` `fabrikam` herhangi bir sonek veya nokta olmadan adında bir kayıt olmalıdır.

Ayrıca, `A` kaydın değeri (IP adresi) [anahtar KASASı özel IP adresi](#find-the-key-vault-private-ip-address-in-the-virtual-network)olmalıdır. `A`Kaydı bulursanız Ancak yanlış IP adresini içeriyorsa, yanlış IP adresini kaldırmalı ve yeni bir tane eklemeniz gerekir. Kaydın tamamını kaldırmalı `A` ve yeni bir tane eklemeniz önerilir.

>[!NOTE]
> Bir kaydı kaldırdığınızda veya değiştirdiğinizde `A` , TTL (yaşam süresi) değeri henüz kullanım süresinin dolamadığı için makine ESKI IP adresine çözümlenmeye devam edebilir. Her zaman 60 saniyeden (bir dakika) daha küçük bir TTL değeri belirtmeniz ve 600 saniyeden (10 dakika) daha büyük olmaması önerilir. Çok büyük bir değer belirtirseniz, istemcileriniz kesintileri kurtarmak çok uzun sürebilir.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>Birden fazla sanal ağ için DNS çözümlemesi

Birden çok sanal ağ varsa ve her birinin aynı anahtar kasasına başvuran kendi özel uç noktası kaynağı varsa, Anahtar Kasası ana bilgisayar adının ağa bağlı olarak farklı bir özel IP adresine çözümlenmesi gerekir. Bu, her biri farklı bir sanal ağa bağlanan ve kayıttaki farklı bir IP adresi kullanan birden çok Özel DNS bölgesinin da gerekli olduğu anlamına gelir `A` .

Daha Gelişmiş senaryolarda, sanal ağlarda eşleme etkinleştirilmiş olabilir. Bu durumda, yalnızca bir sanal ağın özel uç nokta kaynağına ihtiyacı vardır, ancak her ikisinin de Özel DNS bölge kaynağıyla bağlantılı olması gerekebilir. Bu senaryo bu belge tarafından doğrudan ele alınmıyor.

### <a name="understand-that-you-have-control-over-dns-resolution"></a>DNS çözümlemesi üzerinde denetiminiz olduğunu anlayın

[Önceki bölümde](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine)açıklandığı gibi, özel bağlantıları olan bir Anahtar Kasası `{vaultname}.privatelink.vaultcore.azure.net` *ortak* kaydında diğer ada sahiptir. Sanal ağ tarafından kullanılan DNS sunucusu ortak kaydı kullanır, ancak *özel* bir kayıt için her bir diğer adı denetler ve bir tane bulunursa, genel kayıtta tanımlanan diğer adları durdurur.

Bu mantık, sanal ağın adı olan bir Özel DNS bölgesine bağlı olması `privatelink.vaultcore.azure.net` ve Anahtar Kasası için genel DNS kaydının diğer ada sahip olması `fabrikam.privatelink.vaultcore.azure.net` (Anahtar Kasası ana bilgisayar adının özel DNS bölge adıyla tam olarak eşleştiğini unutmayın), DNS sorgusunun `A` `fabrikam` *özel DNS bölgesinde* adı olan bir kaydı arayacağı anlamına gelir. `A`Kayıt bulunursa, IP adresı DNS sorgusunda döndürülür ve genel DNS kaydında başka arama yapılmaz.

Gördüğünüz gibi, ad çözümlemesi denetiminizin altında yer alabilir. Bu tasarımın rationales 'leri şunlardır:

- Özel DNS sunucuları ve şirket içi ağlarla tümleştirme içeren karmaşık bir senaryonuz olabilir. Bu durumda, adların IP adreslerine nasıl çevrildiği kontrol etmeniz gerekir.
- Özel bağlantılar olmadan bir anahtar kasasına erişmeniz gerekebilir. Bu durumda, ana bilgisayar adının sanal ağdan çözümlenmesi ortak IP adresini döndürmelidir ve bu durum özel bağlantılar olmadan anahtar kasalarının `privatelink` ad kaydında diğer ada sahip olmadığından meydana gelir.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. Anahtar Kasası isteklerinin özel bağlantı kullanmasını doğrulama

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>`/healthstatus`Anahtar kasasının uç noktasını sorgulama

Anahtar kasanız `/healthstatus` , Tanılama için kullanılabilen uç nokta sağlar. Yanıt üst bilgileri, Anahtar Kasası hizmeti tarafından görülen kaynak IP adresini içerir. Bu uç noktayı aşağıdaki komutla çağırabilirsiniz (**Anahtar Kasası ana bilgisayar adını kullanmayı unutmayın**):

Windows (PowerShell):

```powershell
PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers
```

```output
Key                           Value
---                           -----
Pragma                        no-cache
x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
x-ms-keyvault-service-version 1.2.27.0
x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security     max-age=31536000;includeSubDomains
Content-Length                4
Cache-Control                 no-cache
Content-Type                  application/json; charset=utf-8
```

Linux veya aşağıdakiler dahil olmak üzere Windows 10 ' un son sürümü `curl` :

```console
joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
```

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
x-ms-keyvault-service-version: 1.2.27.0
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security: max-age=31536000;includeSubDomains
Content-Length: 4
```

Aşağıdakine benzer bir çıktı almıyorsanız veya bir ağ hatası alırsanız, belirttiğiniz ana bilgisayar adı aracılığıyla anahtar kasanıza erişilemediği anlamına gelir ( `fabrikam.vault.azure.net` örnekte). Ana bilgisayar adı doğru IP adresine çözümlenmiyor ya da aktarım katmanında bir bağlantı sorunu var. Bu durum yönlendirme sorunları, paket bırakmaları ve diğer nedenlerden kaynaklanabilir. Daha fazla araştırma yapmanız gerekir.

Yanıt üst bilgisi içermelidir `x-ms-keyvault-network-info` :

```console
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
```

`addr`Başlıktaki alanı, `x-ms-keyvault-network-info` ISTEĞIN kaynağının IP adresini gösterir. Bu IP adresi aşağıdakilerden biri olabilir:

- İsteği yapan makinenin özel IP adresi. Bu, isteğin özel bağlantıları veya hizmet uç noktalarını kullandığını gösterir. Bu, özel bağlantılar için beklenen çıktıdır.
- İstekte bulunan makineden *değil* , başka BIR özel IP adresi. Bu, bazı özel yönlendirmenin etkin olduğunu gösterir. Yine de isteğin özel bağlantıları veya hizmet uç noktalarını kullandığını gösterir.
- Bazı genel IP adresleri. Bu, isteğin bir ağ geçidi (NAT) cihazı aracılığıyla Internete yönlendirildiğini gösterir. Bu, isteğin özel bağlantıları kullanmadığını ve bazı sorunların düzeltilmesi gerektiğini gösterir. Bunun yaygın nedenleri 1) özel uç nokta onaylı ve başarılı durumunda değil; 2) ana bilgisayar adı anahtar kasasının özel IP adresine çözümlenmiyor. Bu makalede her iki durumda da sorun giderme eylemleri bulunur.

>[!NOTE]
> `/healthstatus`Çalışma isteği, ancak `x-ms-keyvault-network-info` üst bilgi eksikse, uç nokta muhtemelen Anahtar Kasası tarafından sunulmuyor demektir. Yukarıdaki komutlar HTTPS sertifikasını da doğruladığından, eksik üst bilgi izinsiz bir işaret olabilir.

### <a name="query-the-key-vault-ip-address-directly"></a>Anahtar Kasası IP adresini doğrudan sorgulama

>[!IMPORTANT]
> HTTPS sertifika doğrulaması olmadan anahtar kasasına erişilmesi tehlikelidir ve yalnızca öğrenme amacıyla kullanılabilir. Üretim kodu, bu istemci tarafı doğrulaması olmadan anahtar kasasına HIÇBIR şekilde erişmelidir. Yalnızca sorunları tanısanız bile, Anahtar Kasası istekleriniz için genellikle HTTPS sertifika doğrulamasını devre dışı bıraktığınızda, ortaya çıkarmayacak olan değişikliklere tabi olabilirsiniz.

Yeni bir PowerShell sürümü yüklediyseniz, `-SkipCertificateCheck` HTTPS sertifika denetimlerini atlamak için kullanabilirsiniz, ardından [Anahtar Kasası IP adresini](#find-the-key-vault-private-ip-address-in-the-virtual-network) doğrudan hedefleyebilirsiniz:

```powershell
PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers
```

Kullanıyorsanız `curl` , bağımsız değişkenle aynı şekilde yapabilirsiniz `-k` :

```console
joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus
```

Yanıtların önceki bölümden aynı olması gerekir, yani `x-ms-keyvault-network-info` aynı değere sahip üstbilgiyi içermesi gerekir. `/healthstatus`Anahtar Kasası ana bilgisayar adını veya IP adresini kullanıyorsanız, uç nokta dikkate almaz.

`x-ms-keyvault-network-info`Anahtar Kasası ana bilgisayar adını kullanarak istek için bir değer döndürmeyi ve IP adresini kullanarak istek için başka bir değer döndürdüğünü görürseniz her istek farklı bir uç noktayı hedefler. Bir `addr` önceki bölümdeki alanın açıklamasına başvurarak `x-ms-keyvault-network-info` , hangi durumun yanlış olduğunu ve düzeltilmesi gerektiğini belirleyin.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. diğer değişiklikler ve özelleştirmeler olumsuz hataya neden oluyor

Aşağıdaki öğeler, ayrıntılı olmayan araştırma eylemlerdir. Bunlara ek sorunları nerede arayacaklarını söyleyecektir, ancak bu senaryolardaki sorunları gidermek için Gelişmiş ağ bilgisine sahip olmanız gerekir.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Sanal ağda özel DNS sunucuları tanılama

Portalda, sanal ağ kaynağını açın. Sol taraftaki menüde, **DNS sunucuları**' nı açın. "Özel" i kullanıyorsanız, DNS çözümlemesi bu belgede açıklanmayabilir. DNS sunucularınızın Anahtar Kasası ana bilgisayar adını nasıl çözdüğünü tanılamanız gerekir.

Varsayılan Azure tarafından sağlanmış DNS sunucularını kullanıyorsanız, bu tüm belgeyi uygulanabilir olur.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>Sanal makinede, geçersiz kılan Konakları veya özel DNS sunucularını tanılama

Birçok işletim sistemi, genellikle dosyayı değiştirerek ana bilgisayar başına açık bir sabit IP adresi ayarlamaya olanak tanır `hosts` . Ayrıca DNS sunucularının üzerine yazma izni verebilir. Bu senaryolardan birini kullanıyorsanız sisteme özgü tanılama seçenekleri ile devam edin.

### <a name="promiscuous-proxies-fiddler-etc"></a>Karışık proxy 'ler (Fiddler, vb.)

Açıkça belirtilmediği sürece, bu makaledeki tanılama seçenekleri yalnızca ortamda bir karma proxy yoksa çalışır. Bu proxy 'ler genellikle Tanılanmakta olan makineye (Fiddler en yaygın örnek olarak) yüklenirken, Gelişmiş yöneticiler kök sertifika yetkililerinin (CA 'Lar) üzerine yazabilir ve ağda birden fazla makineye hizmeti sunan ağ geçidi cihazlarında bir karma proxy yükleyemez. Bu proxy 'ler, güvenlik ve güvenilirliği önemli ölçüde etkileyebilir. Microsoft bu tür ürünleri kullanan konfigürasyonları desteklemez.

### <a name="other-things-that-may-affect-connectivity"></a>Bağlantıyı etkileyebilecek diğer şeyler

Bu, gelişmiş veya karmaşık senaryolarda bulunan öğelerin ayrıntılı olmayan bir listesidir:

- Güvenlik Duvarı ayarları, sanal ağa bağlı Azure Güvenlik Duvarı veya sanal ağda veya makinede dağıtım yapılan özel bir güvenlik duvarı çözümüdür.
- Hangi DNS sunucularının kullanıldığını ve trafiğin nasıl yönlendirildiğini etkileyebilecek ağ eşleme.
- DNS sorgularından gelen trafik dahil olmak üzere trafiğin nasıl yönlendirildiğini etkileyebilecek özel ağ geçidi (NAT) çözümleri.