---
title: Azure Data Box kablolama seçenekleri | Microsoft Docs
description: Azure Data Box cihazınız için kullanabileceğiniz farklı kablolama seçenekleri açıklanmaktadır.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 10/24/2018
ms.author: alkohli
ms.openlocfilehash: 661cec672aa02f62b479b7f5831adfdaff9e6b21
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316565"
---
# <a name="cabling-options-for-your-azure-data-box"></a>Azure Data Box cihazınız için kablolama seçenekleri

Bu makalede Azure Data Box cihazınızı veri aktarımı için kablolama seçenekleri anlatılmaktadır.

## <a name="transfer-via-mgmt-port"></a>MGMT bağlantı noktası aracılığıyla aktarım

Bu seçenek, Data Box cihazınız için minimum yapılandırma düzeyidir. Hem yönetim hem de veri için yalnızca MGMT bağlantı noktasını yapılandırabilirsiniz.

Başlamadan önce aşağıdakilere sahip olduğunuzdan emin olun:

- MGMT bağlantı noktası için bir RJ45 Ethernet kablosu.
- [Desteklenen işletim sistemini](data-box-system-requirements.md#supported-operating-systems-for-clients) çalıştıran bir veri kaynağı.

Cihazınızın kablolarını bağlamak için aşağıdaki adımları gerçekleştirin.

1. Bir RJ45 kablosu kullanarak MGMT bağlantı noktasından verilerin bulunduğu sunucuya bağlantı kurun.

    ![Tek bağlantı noktası kurulumu](media/data-box-cable-options/cabling-mgmt-only.png)

2. Sunucuda şu ayarları yapın:

    - **IP adresi**: 192.168.100.5
    - **Alt ağ**: 255.255.255.0

3. Cihazın yerel Web Kullanıcı arabirimine şu adresten erişin: 192.168.100.10. Azure portaldan aldığınız kilit açma parolasını kullanarak Data Box'ta oturum açın ve cihazın kilidini açın.


## <a name="transfer-via-data-port-with-static-ips"></a>Statik IP adresleriyle DATA bağlantı noktası aracılığıyla aktarım

Yönetim için MGMT bağlantı noktasını, veri için de veri bağlantı noktalarından birini kullanarak Data Box cihazınız için iki bağlantı noktası yapılandırabilirsiniz. DATA 1, DATA 2 veya DATA 3 veri bağlantı noktasını kullanabilirsiniz.

Tek bir veri bağlantı noktası yapılandırıyorsanız bunun mutlaka DATA 1 veya DATA 2 gibi 10 GbE destekli bir bağlantı noktası olmasını öneririz. 1 GbE bağlantı noktası, veri aktarımı için gereken süreyi önemli ölçüde artıracaktır.

Başlamadan önce aşağıdakilere sahip olduğunuzdan emin olun:

- MGMT bağlantı noktası için bir RJ45 Ethernet kablosu.
- Bağlamak istediğiniz her bir 10 GbE destekli veri bağlantı noktası için bir 10 GbE SFP+ Twinax bakır kablo.
- [Desteklenen işletim sistemini](data-box-system-requirements.md#supported-operating-systems-for-clients) çalıştıran bir veya daha fazla veri kaynağı.

### <a name="option-1---initial-setup-via-server"></a>1\. Seçenek: Başlangıç kurulumunu sunucu üzerinden gerçekleştirme

Cihazınızın kablolarını bağlamak için aşağıdaki adımları gerçekleştirin.

1. Yapılandırma için bir RJ45 Ethernet kablosu kullanarak sunucudan MGMT bağlantı noktasına bağlantı kurun.
2. DATA 3 için bir RJ45 veya DATA 1 ya da DATA 2 için SFP+ kabloları kullanarak veri kaynağı olarak kullanılan sunucuya bağlantı yapın. Yüksek performans için 10 GbE destekli DATA 1 veya DATA 2 bağlantı noktalarını kullanmanızı öneririz.
3. Sunucuda şu ayarları yapın:

   - **IP adresi**: 192.168.100.5
   - **Alt ağ**: 255.255.255.0

     ![İki bağlantı noktası kurulumu](media/data-box-cable-options/cabling-2-port-setup.png)

3. Cihazın yerel Web Kullanıcı arabirimine şu adresten erişin: 192.168.100.10. Azure portaldan aldığınız kilit açma parolasını kullanarak Data Box'ta oturum açın ve cihazın kilidini açın.
4. Yapılandırdığınız veri bağlantı noktalarına statik IP atayın.

### <a name="option-2---initial-setup-via-separate-computer"></a>2\. Seçenek: Başlangıç kurulumunu ayrı bir bilgisayar üzerinden gerçekleştirme

Cihazınızın kablolarını bağlamak için aşağıdaki adımları gerçekleştirin.

1. Yapılandırma için ayrı bilgisayardan, doğrudan MGMT bağlantı noktasına bir RJ45 Ethernet kablosu kullanın.
2. DATA 3 için bir RJ45 veya DATA 1 ya da DATA 2 için SFP+ kabloları kullanarak sunucuya bağlantı yapın. Yüksek performans için 10 GbE destekli DATA 1 veya DATA 2 bağlantı noktalarını kullanmanızı öneririz. Veri bağlantı noktalarını verilerin bulunduğu sunucuya 10 GbE anahtar üzerinden bağlayın.
3. Kullanmakta olduğunuz dizüstü bilgisayardaki Ethernet bağdaştırıcısını şu ayarlarla yapılandırın:

   - **IP adresi**: 192.168.100.5
   - **Alt ağ**: 255.255.255.0

     ![Anahtarla iki bağlantı noktası kurulumu](media/data-box-cable-options/cabling-with-static-ip.png)

3. Cihazın yerel Web Kullanıcı arabirimine şu adresten erişin: 192.168.100.10. Azure portaldan aldığınız kilit açma parolasını kullanarak Data Box'ta oturum açın ve cihazın kilidini açın.
4. DHCP sunucusu tarafından atanan IP adreslerini belirleyin.

## <a name="transfer-via-data-port-with-static-ips-using-a-switch"></a>Anahtar kullanarak statik IP adresleriyle DATA bağlantı noktası aracılığıyla aktarım 

1 GbE ve 10 GbE ağlarda birden fazla veri kaynağı bulunuyorsa bu yapılandırmayı kullanabilirsiniz.

Başlamadan önce aşağıdakilere sahip olduğunuzdan emin olun:

- MGMT bağlantı noktası için bir RJ45 Ethernet kablosu.
- Bağlamak istediğiniz her bir 10 GbE destekli veri bağlantı noktası için bir 10 GbE SFP+ Twinax bakır kablo.
- [Desteklenen işletim sistemini](data-box-system-requirements.md#supported-operating-systems-for-clients) çalıştıran bir veya daha fazla veri kaynağı. Bu veri kaynakları 1 GbE veya 10 GbE gibi farklı ağlarda bulunabilir.

Cihazınızın kablolarını bağlamak için aşağıdaki adımları gerçekleştirin.

1. Yapılandırma için bir RJ45 Ethernet kablosu kullanarak sunucudan MGMT bağlantı noktasına bağlantı kurun.
2. DATA 3 için bir RJ45 veya DATA 1 ya da DATA 2 için SFP+ kabloları kullanarak sunucuya bağlantı yapın. Yüksek performans için 10 GbE destekli DATA 1 veya DATA 2 bağlantı noktalarını kullanmanızı öneririz.
3. Kullanmakta olduğunuz dizüstü bilgisayardaki Ethernet bağdaştırıcısını şu ayarlarla yapılandırın:

   - **IP adresi**: 192.168.100.5
   - **Alt ağ**: 255.255.255.0

     ![Anahtarla iki bağlantı noktası kurulumu](media/data-box-cable-options/cabling-with-switch-static-ip.png)

3. Cihazın yerel Web Kullanıcı arabirimine şu adresten erişin: 192.168.100.10. Azure portaldan aldığınız kilit açma parolasını kullanarak Data Box'ta oturum açın ve cihazın kilidini açın.
4. Yapılandırdığınız veri bağlantı noktalarına statik IP atayın.


## <a name="transfer-via-data-port-in-a-dhcp-environment"></a>DHCP ortamında DATA bağlantı noktası aracılığıyla aktarım

Cihazınız bir DHCP ortamında bulunacaksa bu yapılandırmayı kullanın.

Başlamadan önce aşağıdakilere sahip olduğunuzdan emin olun:

- DATA 1 bağlantı noktasına bağlanmak istiyorsanız bir RJ45 kablosu.
- Bağlamak istediğiniz her bir 10 GbE destekli veri bağlantı noktası için bir 10 GbE SFP+ Twinax bakır kablo.
- [Desteklenen işletim sistemini](data-box-system-requirements.md#supported-operating-systems-for-clients) çalıştıran bir veya daha fazla veri kaynağı. Bu veri kaynakları 1 GbE veya 10 GbE gibi farklı ağlarda bulunabilir.

Cihazınızın kablolarını bağlamak için aşağıdaki adımları gerçekleştirin.

1. Sunucuya bağlanmak için anahtar üzerinden (DHCP sunucusuna erişim sağlanabilen) bir RJ45 veya SFP+ kablosu kullanın.

    ![Anahtarla iki bağlantı noktası kurulumu](media/data-box-cable-options/cabling-dhcp-data-only.png)
2. IP adresini belirlemek için DHCP sunucusunu veya DNS sunucusunu kullanın.
3. Aynı ağ üzerinde bulunan başka bir sunucudan DHCP sunucusu tarafından atanmış olan IP adresini kullanarak cihazın yerel web kullanıcı arabirimine erişim sağlayın. Azure portaldan aldığınız kilit açma parolasını kullanarak Data Box'ta oturum açın ve cihazın kilidini açın.

## <a name="next-steps"></a>Sonraki adımlar

- Cihazın kablo bağlantılarını yaptıktan sonra [Verilerinizi Azure Data Box'a kopyalama](data-box-deploy-copy-data.md) bölümüne gidin.
