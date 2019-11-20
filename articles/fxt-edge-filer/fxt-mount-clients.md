---
title: Microsoft Azure FXT Edge Filer kümesinde istemcileri bağlama
description: NFS istemci makinelerinin Azure FXT Edge Filer karma depolama önbelleğini nasıl bağlayabilirler
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: ac1263b352e7fdde57dfee6515a8b22400f22b06
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256033"
---
# <a name="tutorial-mount-the-cluster"></a>Öğretici: kümeyi bağlama

Bu öğreticide, NFS istemcilerini Azure FXT Edge Filer kümesine bağlama öğretilir. İstemciler, arka uç depolama eklediğinizde atadığınız sanal ad alanı yollarını bağlayabilir. 

Bu öğretici öğretir: 

> [!div class="checklist"]
> * İstemciye yönelik IP adresleri aralığı genelinde Yük Dengeleme istemcilerinin stratejileri
> * İstemciye yönelik bir IP adresinden ve ad alanı birleşimden bir bağlama yolu oluşturma
> * Bağlama komutunda kullanılacak bağımsız değişkenler

Bu öğreticinin tamamlanabilmesi yaklaşık 45 dakika sürer.

## <a name="steps-to-mount-the-cluster"></a>Kümeyi bağlama adımları

İstemci makinelerini Azure FXT Edge Filer kümenize bağlamak için aşağıdaki adımları izleyin.

1. İstemci trafiğinin Küme düğümleriniz arasında nasıl yük dengelenmesi gerektiğine karar verin. Ayrıntılar için, aşağıda [istemci yükünü](#balance-client-load)okuyun. 
1. Takılacak küme IP adresini ve birleşim yolunu belirler.
1. Bağlama için istemciye yönelik yolu saptayın.
1. [Bağlama komutunu](#use-recommended-mount-command-options)uygun bağımsız değişkenlerle verin.

## <a name="balance-client-load"></a>İstemci yükünü dengeleme

İstemci isteklerinin kümedeki tüm düğümler arasında dengelenmesi için, istemcileri istemciye yönelik tüm IP adreslerinin tam aralığına bağlamanız gerekir. Bu görevi otomatikleştirmek için birkaç yol vardır.

Küme için hepsini bir kez deneme DNS Yük Dengelemesi hakkında bilgi edinmek için [Azure FXT Edge Filer kümesi IÇIN DNS yapılandırma](fxt-configure-network.md#configure-dns-for-load-balancing)konusunu okuyun. Bu yöntemi kullanmak için, bu makalelerde açıklanamayan bir DNS sunucusu korumanız gerekir.

Küçük yüklemeler için daha basit bir yöntem, IP adreslerini istemci takma süresi içinde aralığa atamak için bir komut dosyası kullanmaktır. 

Diğer yük dengeleme yöntemleri büyük veya karmaşık sistemler için uygun olabilir. Microsoft temsilcinize başvurun veya yardım almak için bir [destek isteği](fxt-support-ticket.md) açın. (Azure Load Balancer Şu anda Azure FXT Edge Filsi ile *desteklenmiyor* .)

## <a name="create-the-mount-command"></a>Bağlama komutunu oluşturma 

@No__t-0 komutu, Azure FXT Edge Filer kümesindeki sanal sunucuyu (vServer) yerel dosya sistemindeki bir yola eşler. 

Biçim ``mount <FXT cluster path> <local path> {options}``

Bağlama komutunda üç öğe vardır: 

* küme yolu-aşağıda açıklanan IP adresi ve ad alanı birleşim yolu birleşimi
* Yerel yol-istemcideki yol 
* bağlama komutu seçenekleri-( [Önerilen bağlama komut seçeneklerini kullan](#use-recommended-mount-command-options)bölümünde listelenmiştir)

### <a name="create-the-cluster-path"></a>Küme yolunu oluşturma

Küme yolu, vServer *IP adresinin* bir birleşimidir ve bir *ad alanı birleşiminin*yoludur. Ad alanı birleşimi, [depolama sistemini](fxt-add-storage.md#create-a-junction)eklediğinizde tanımladığınız bir sanal yoldur.

Örneğin, ad alanı yolunuzda ``/fxt/files`` ' ı kullandıysanız, istemcileriniz *IPI*:/FXT/Files öğesini yerel bağlama noktasına bağlayabilir. 

![Ad alanı yolu alanındaki/avere/Files ile "yeni birleşim Ekle" iletişim kutusu](media/fxt-mount/fxt-junction-example.png)

IP adresi, vServer için tanımlanan istemciye yönelik IP adreslerinden biridir. İstemci ile karşılıklı IP aralığını küme denetim masasında iki yerde bulabilirsiniz:

* **Vservers** tablosu (Pano sekmesi)- 

  ![Grafik altındaki veri tablosunda seçili olan VServer sekmesi ile Denetim Masası 'nın Pano sekmesi ve IP adresi bölümü daire içinde](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Istemciye yönelik ağ** ayarları sayfası- 

  ![Belirli bir vServer için tablonun adres aralığı bölümünün etrafında bir daire olan sanal sunucu > Istemciye yönelik ağ yapılandırması sayfası > Ayarlar](media/fxt-mount/fxt-ip-addresses-settings.png)

Bağlama komutunun küme yolunu oluşturmak için IP adresini ve ad alanı yolunu birleştirin. 

Örnek istemci bağlama komutu: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Yerel yolu oluşturma

Bağlama komutunun yerel yolu size yöneliktir. Sanal ad alanının bir parçası olarak istediğiniz herhangi bir yol yapısını ayarlayabilirsiniz. İstemci iş akışınız için uygun bir ad alanı ve yerel yol tasarlayın. 

İstemciye yönelik ad alanı hakkında daha fazla bilgi için, küme yapılandırma kılavuzunun [ad alanına genel bakış](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)' ı okuyun.

Yollara ek olarak, her bir istemciyi bağlamak için aşağıda açıklanan [bağlama komutu seçeneklerini](#use-recommended-mount-command-options) dahil edin.

### <a name="use-recommended-mount-command-options"></a>Önerilen bağlama komut seçeneklerini kullanın

Sorunsuz bir istemci bağlama sağlamak için, bu ayarları ve bağımsız değişkenleri bağlama komutunuz geçirin: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Gerekli ayarlar | |
--- | --- 
``hard`` | Azure FXT Edge Filer kümesine yönelik hafif bağlar, uygulama hatalarıyla ve olası veri kaybı ile ilişkilendirilir. 
``proto=netid`` | Bu seçenek NFS ağ hatalarının uygun işlenmesini destekler.
``mountproto=netid`` | Bu seçenek, bağlama işlemleri için ağ hatalarının uygun işlenmesini destekler.
``retry=n`` | Geçici bağlama hatalarından kaçınmak için ``retry=30`` olarak ayarlayın. (Ön plan takmaları farklı bir değer önerilir.)

| Tercih edilen ayarlar  | |
--- | --- 
``nointr``            | İstemcileriniz, bu seçeneği destekleyen eski işletim sistemi çekirdekler (2008 Nisan 'tan önce) kullanıyorsa, bunu kullanın. "INTR" seçeneği varsayılandır.

## <a name="next-steps"></a>Sonraki adımlar

İstemcileri bağladıktan sonra, iş akışınızı test edebilir ve kümeniz ile çalışmaya başlayın.

Verileri yeni bir bulut çekirdeği filine taşımanız gerekiyorsa, paralel veri alma kullanarak önbellek yapısından yararlanın. Bazı stratejiler, [verileri bir vFXT kümesine taşıma](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest)bölümünde açıklanmaktadır. (Azure için avere vFXT, Azure FXT Edge filine benzer şekilde önbelleğe alma teknolojisini kullanan bulut tabanlı bir üründür.)

Tüm donanım sorunlarını gidermeniz gerekiyorsa, [Izleme Azure FXT Edge filigran donanım durumunu](fxt-monitor.md) okuyun. 
