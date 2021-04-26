---
title: 'Senaryo: sanal ağlar ve dallar için özel yalıtım'
titleSuffix: Azure Virtual WAN
description: Yönlendirme senaryoları-seçili sanal ağların ve dalların birbirlerine ulaşabilmesi önlenir
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: wellee
ms.openlocfilehash: e8e5a5a1b9325f40fdd51133155a0daffaa55a7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99408501"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>Senaryo: sanal ağlar ve dallar için özel yalıtım

Sanal WAN sanal hub 'ı yönlendirme ile çalışırken, kullanılabilecek oldukça az sayıda senaryo vardır. Her iki sanal ağ (VNet) ve dal için özel bir yalıtım senaryosunda, amaç, belirli bir VNET kümesinin başka bir sanal ağ kümesine ulaşmasını engellemektir. Benzer şekilde, dalların (VPN/ER/Kullanıcı VPN) yalnızca belirli sanal ağ kümelerine erişmesine izin verilir.

Ayrıca, Azure Güvenlik duvarının VNET 'e dalı ve VNET **trafiğinin VNET 'e**  dalını incelemesi gereken ek gereksinim da tanıtılmaktadır.  

Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Tasarım

Kaç tane yol tablosunun gerekli olacağını anlamak için bir bağlantı matrisi oluşturabilirsiniz. Bu senaryo için, her hücrenin bir kaynağın (satır) bir hedefle (sütun) iletişim kurup kuramayacağını temsil ettiği aşağıdaki gibi görünür:

| Kaynak | Hedef:| *Mavi VNET 'ler* | *Kırmızı VNET 'ler* | *Kırmızı dallar*| *Mavi dallar*| 
|---|---|---|---|---|---|
| **Mavi VNET 'ler** |   &#8594;|   Direct     |           |   |  AzFW|
| **Kırmızı VNET 'ler**  |   &#8594;|              |   Direct  |  AzFW  | 
| **Kırmızı dallar**   |   &#8594;|   |   AzFW  |  Direct | Direct
| **Mavi dallar**| &#8594;| AzFW  |   |Direct   | Direct

Önceki tablodaki hücrelerin her biri, bir sanal WAN bağlantısının (akışın "Kimden" tarafı, satır başlıkları) bir hedefle (akışın "to" tarafı, italik olan sütun üst bilgileri) iletişim kuracağını açıklar. **Doğrudan** , **azfw** , trafiğin hedefe Iletilmeden önce Azure Güvenlik Duvarı tarafından incelenediğini Ifade ederken doğrudan sanal WAN aracılığıyla trafik akışlarını ifade etmek anlamına gelir. Boş bir giriş, akışın kurulumda engellediği anlamına gelir.

Bu durumda, sanal ağlar için iki yol tablosu, yolda Azure Güvenlik Duvarı olmadan VNet yalıtımının amacını elde etmek için gereklidir. Bu rota tablolarını **RT_BLUE** ve **RT_RED** çağıracağız.

Ayrıca, dallar her zaman  **varsayılan** yol tablosuyla ilişkilendirilmelidir. Dallardan gelen ve giden trafiğin Azure Güvenlik Duvarı tarafından denetlendiğinden emin olmak için **varsayılan** olarak statik yollar, **RT_RED** ve Azure Güvenlik Duvarı 'na trafik işaret eden yönlendirme tablolarına **RT_BLUE** ve Istenen trafiğe izin vermek için ağ kuralları ayarlayacağız. Ayrıca, dalların **RT_BLUE** ve RT_RED **yayılmadığından** da emin veriyoruz .

Sonuç olarak, bu son tasarımdır:

* Mavi sanal ağlar:
  * İlişkili yol tablosu: **RT_BLUE**
  * Yol tablolarına yayma: **RT_BLUE**
* Kırmızı sanal ağlar:
  * İlişkili yol tablosu: **RT_RED**
  * Yol tablolarına yayma: **RT_RED** 
* Dallar
  * İlişkili yol tablosu: **varsayılan**
  * Yol tablolarına yayma: **varsayılan**
* Statik yollar:
    * **Varsayılan yol tablosu**: sonraki atlama Azure Güvenlik Duvarı Ile sanal ağ adres alanları
    * **RT_RED**: sonraki atlama Azure Güvenlik Duvarı ile 0.0.0.0/0
    * **RT_BLUE**: sonraki atlama Azure Güvenlik Duvarı ile 0.0.0.0/0
* Güvenlik Duvarı ağ kuralları:
    * **Kural** **kaynağı ön ekine** Izin ver: mavi dal adresi önekleri **hedef ön eki**: mavi VNET ön ekleri 
    * **Kural**  **kaynağı ön ekine** Izin ver: Red şube adresi önekleri **hedef ön eki**: Red VNET ön ekleri

> [!NOTE]
> Tüm dalların varsayılan yol tablosuyla ilişkilendirilmesi ve aynı yönlendirme tabloları kümesine yayılması gerektiğinden, tüm dallar aynı bağlantı profiline sahip olur. Diğer bir deyişle, sanal ağlar için kırmızı/mavi kavramı dallara uygulanamaz. Bununla birlikte, dallar için özel yönlendirme elde etmek üzere dallardaki trafiği Azure Güvenlik Duvarı 'na iletebiliriz.

> [!NOTE]
> Azure Güvenlik Duvarı varsayılan olarak bir sıfır güvenli modeldeki trafiği engeller. İncelenen paketle eşleşen bir açık **Izin verme** kuralı yoksa, Azure Güvenlik Duvarı paketi düşürülecektir.

Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).



## <a name="workflow"></a><a name="architecture"></a>İş akışı

**Şekil 1**' de mavi ve kırmızı VNET 'Ler ve mavi ya da kırmızı sanal ağlara erişebilen dallar vardır.

* Mavi bağlantılı VNET 'ler birbirine ulaşabilir ve tüm mavi dallar (VPN/ER/P2S) bağlantılarına ulaşabilir. Diyagramda, mavi dal siteden siteye VPN sitesidir.
* Kırmızı bağlantılı VNET 'ler birbirine ulaşabilir ve tüm kırmızı dallar (VPN/ER/P2S) bağlantılarına ulaşabilir. Diyagramda, kırmızı dal, Noktadan siteye VPN kullanıcılarınızla aynıdır.

Yönlendirmeyi ayarlarken aşağıdaki adımları göz önünde bulundurun.

1. Bu sanal ağlara giden trafiği özelleştirmek için Azure portal, **RT_BLUE** ve **RT_RED** iki özel yol tablosu oluşturun.
2. **RT_BLUE** yol tablosu Için, mavi sanal ağların diğer tüm mavi sanal ağların adres öneklerini öğrendiğinden emin olmak için aşağıdaki ayarları uygulayın.:
   * **İlişkilendirme**: tüm mavi VNET 'leri seçin.
   * **Yayma**: tüm mavi VNET 'leri seçin.
3. Kırmızı VNET 'ler için **RT_RED** yol tablosu için aynı adımları yineleyin.
4. Sanal WAN 'da Azure Güvenlik Duvarı sağlayın. Sanal WAN hub 'ında Azure Güvenlik Duvarı hakkında daha fazla bilgi için bkz. [sanal WAN hub 'Da Azure Güvenlik Duvarı 'Nı yapılandırma](howto-firewall.md).
5. Sanal hub 'ın **varsayılan** yol tablosuna, VNET adres alanlarını (Blue ve Red) hedefleyen tüm trafiği Azure Güvenlik Duvarı 'na yönlendiren bir statik yol ekleyin. Bu adım, Dallarınızın tüm paketlerinin İnceleme için Azure Güvenlik Duvarı 'na gönderilmesini sağlar.
    * Örnek: **hedef ön eki**: 10.0.0.0/24 **sonraki atlama**: Azure Güvenlik Duvarı
    >[!NOTE]
    > Bu adım, "güvenli özel trafik" seçeneği belirlenerek güvenlik duvarı Yöneticisi aracılığıyla da yapılabilir. Bu, sanal ağlar ve dallar için geçerli olan tüm RFC1918 özel IP adresleri için bir yol ekler. RFC1918 ile uyumlu olmayan herhangi bir dala veya sanal ağa el ile eklemeniz gerekir. 

6. **RT_RED** için bir statik yol ekleyin ve tüm trafiği Azure Güvenlik Duvarı 'na yönlendirerek **RT_BLUE** . Bu adım, sanal ağların dala doğrudan erişememesini sağlar. Bu sanal ağlar varsayılan yol tablosuyla ilişkili olmadığından, bu adım güvenlik duvarı Yöneticisi aracılığıyla yapılamaz.
    * Örnek: **hedef ön eki**: 0.0.0.0/0 **sonraki atlama**: Azure Güvenlik Duvarı

    > [!NOTE]
    > Yönlendirme, en uzun önek eşleşmesi (LPM) kullanılarak gerçekleştirilir. Sonuç olarak, 0.0.0.0/0 statik yolları **BLUE_RT** ve **RED_RT** bulunan tam ön ekler üzerinde **tercih edilmez.** Sonuç olarak, VNET içi trafik Azure Güvenlik Duvarı tarafından denetlenmeyecektir.

Bu, aşağıdaki şekilde görüldüğü gibi yönlendirme yapılandırması değişikliklerine neden olur.

**Şekil 1** 
 [ ![ Şekil 1 ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png)](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

* Sanal WAN hakkında daha fazla bilgi için bkz. [SSS](virtual-wan-faq.md).
* Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).
