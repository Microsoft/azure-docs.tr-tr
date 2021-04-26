---
title: 'Öğretici: VM ağı yönlendirme sorununu tanılama-Azure portal'
titleSuffix: Azure Network Watcher
description: Bu öğreticide, Azure Ağ İzleyicisi’nin IP sonraki atlama özelliği kullanılarak sanal makine ağ yönlendirme sorununu tanılama hakkında bilgi edineceksiniz.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: d5cd9c5d7f4a14a9a93e6984a8ef7367e5cbafc4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055102"
---
# <a name="tutorial-diagnose-a-virtual-machine-network-routing-problem-using-the-azure-portal"></a>Öğretici: Azure portalını kullanarak bir sanal ağ yönlendirme sorununu tanılama

Bir sanal makine (VM) dağıttığınızda, Azure bu sanal makine için birkaç varsayılan yol oluşturur. Azure’un varsayılan yollarını geçersiz kılmak için özel yollar oluşturabilirsiniz. Bazı durumlarda özel bir yol, bir sanal makinenin diğer kaynaklarla iletişim kuramamasına neden olabilir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * VM oluşturma
> * Ağ İzleyicisinin sonraki atlama özelliğini kullanarak bir URL ile iletişimi test etme
> * Bir IP adresi ile iletişimi test etme
> * Bir yönlendirme sorununu tanılayın ve nasıl giderebileceğinizi öğrenin

Tercih ederseniz, [Azure CLI](diagnose-vm-network-routing-problem-cli.md) veya [Azure PowerShell](diagnose-vm-network-routing-problem-powershell.md) kullanarak bir sanal makine ağ yönlendirme sorununu tanılayabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="log-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-vm"></a>VM oluşturma

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. **İşlem**'i seçin ve sonra **Windows Server 2016 Datacenter** veya **Ubuntu Server 17.10 VM** seçeneğini belirleyin.
3. Aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Tamam**’ı seçin:

    |Ayar|Değer|
    |---|---|
    |Ad|myVm|
    |Kullanıcı adı| Seçtiğiniz bir kullanıcı adını girin.|
    |Parola| Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.|
    |Abonelik| Aboneliğinizi seçin.|
    |Kaynak grubu| **Yeni oluştur**’u seçin ve **myResourceGroup** değerini girin.|
    |Konum| **Doğu ABD** seçin|

4. Sanal makine için bir boyut seçin ve **Seç** seçeneğini belirleyin.
5. **Ayarlar** bölümünde tüm varsayılanları kabul edin ve **Tamam**’ı seçin.
6. **Özet**’in **Oluştur** bölümünde **Oluştur**’u seçerek sanal makine dağıtımını başlatın. Sanal makinenin dağıtılması birkaç dakika sürer. Kalan adımlara devam etmeden önce sanal makinenin dağıtımı tamamlamasını bekleyin.

## <a name="test-network-communication"></a>Ağ iletişimini test etme

Ağ İzleyicisi ile ağ iletişimini test etmek için önce en az bir Azure bölgesinde bir ağ izleyicisini etkinleştirmeniz ve sonra iletişimi test etmek için Ağ İzleyicisinin sonraki atlama özelliğini kullanmanız gerekir.

### <a name="enable-network-watcher"></a>Ağ izleyicisini etkinleştirme

En az bir bölgede etkinleştirilmiş bir ağ izleyiciniz zaten varsa [Sonraki atlamayı kullanma](#use-next-hop) bölümüne geçin.

1. Portalda **Tüm hizmetler**’i seçin. **Filtre kutusu**’na *Ağ İzleyicisi* yazın. Sonuçlarda **Ağ İzleyicisi** göründüğünde seçin.
2. **Bölgeler**’i seçip genişletin ve sonra aşağıdaki resimde gösterildiği gibi **Doğu ABD**’nin sağındaki **...** öğesini seçin:

    ![Ağ İzleyicisini etkinleştirme](./media/diagnose-vm-network-traffic-filtering-problem/enable-network-watcher.png)

3. **Ağ İzleyicisini etkinleştirme**’yi seçin.

### <a name="use-next-hop"></a>Sonraki atlamayı kullanma

Azure, varsayılan hedeflerin yollarını otomatik olarak oluşturur. Varsayılan yolları geçersiz kılmak için özel yollar oluşturabilirsiniz. Bazı durumlarda, özel yollar iletişimin başarısız olmasına neden olabilir. Azure’un trafiği yönlendirmek için kullandığı yolu belirlemek üzere Ağ İzleyicisi’nin sonraki atlama özelliğini kullanın.

1. Azure portalında, **Ağ İzleyicisi** altındaki **Sonraki atlama**’yı seçin.
2. Aboneliğinizi seçin, aşağıdaki değerleri girin veya seçin ve sonra aşağıdaki resimde gösterildiği gibi **Sonraki atlama**’yı seçin:

    |Ayar                  |Değer                                                   |
    |---------                |---------                                               |
    | Kaynak grubu          | myResourceGroup öğesini seçin                                 |
    | Sanal makine         | MyVm öğesini seçin                                            |
    | Ağ arabirimi       | myvm - Ağ arabiriminizin adı farklı olabilir.   |
    | Kaynak IP adresi       | 10.0.0.4                                               |
    | Hedef IP adresi  | 13.107.21.200-<www.bing.com> adreslerinden biri. |

    ![Sonraki atlama](./media/diagnose-vm-network-routing-problem/next-hop.png)

    Birkaç saniye sonra sonuç, sonraki atlama türünün **İnternet** ve **Yol tablosu kimliği**’nin **Sistem Yolu** olduğu bilgisini verir. Bu sonuç, hedefe ait geçerli bir sistem yolu olduğunu bildirir.

3. **Hedef IP adresi**’ni *172.31.0.100* olarak değiştirin ve tekrar **Sonraki atlama**’yı seçin. Döndürülen sonuç, **Hiçbiri**’nin **Sonraki atlama türü** ve **Yol tablosu kimliği**’nin aynı zamanda **Sistem Yolu** olduğunu bildirir. Bu sonuç, hedefin geçerli bir sistem yolu olmasına rağmen trafiği hedefe yönlendiren bir sonraki atlama olmadığını size bildirir.

## <a name="view-details-of-a-route"></a>Bir yolun ayrıntılarını görüntüleme

1. Yönlendirmeyi daha ayrıntılı analiz etmek için ağ arabiriminin etkili yollarını gözden geçirin. Portalın üst kısmındaki arama kutusuna *myvm* (veya işaretlediğiniz ağ arabiriminin adı her neyse) yazın. Arama sonuçlarında **myvm** görüntülendiğinde seçin.
2. Aşağıdaki resimde gösterildiği gibi **DESTEK VE SORUN GİDERME** altında **Etkili yollar**’ı seçin:

    ![Etkili yollar](./media/diagnose-vm-network-routing-problem/effective-routes.png)

    Testi [Sonraki atlamayı kullan](#use-next-hop) bölümündeki 13.107.21.200 adresini kullanarak çalıştırdığınızda, bu adresi içeren başka bir yol olmadığından trafiği adrese yönlendirmek için adres ön eki 0.0.0.0/0 olan yol kullanılmıştır. Varsayılan olarak, başka bir yolun adres ön ekinde belirtilmeyen tüm adresler İnternet'e yönlendirilir.

    Ancak, testi 172.31.0.100 adresini kullanarak çalıştırdığınızda sonuç, sonraki atlama türü olmadığını size bildirir. Önceki resimde görebileceğiniz gibi, 172.16.0.0/12 adresini içeren 172.31.0.100 ön ekinin varsayılan bir yolu olmamasına rağmen **SONRAKİ ATLAMA TÜRÜ****Hiçbiri**’dir. Azure, 172.16.0.0/12 için varsayılan bir yol oluşturur ancak bir neden olmadıkça sonraki atlama türünü belirtmez. Örneğin, sanal ağın adres alanına 172.16.0.0/12 adres aralığını eklediyseniz, Azure yol için **SONRAKİ ATLAMA TÜRÜ**’nü **Sanal ağ** olarak değiştirir. Daha sonra bir denetimde **SONRAKİ ATLAMA TÜRÜ****Sanal ağ** olarak gösterilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu ve içerdiği tüm kaynakları silin:

1. Portalın üst kısmındaki **Ara** kutusuna *myResourceGroup* değerini girin. Arama sonuçlarında **myResourceGroup** seçeneğini gördüğünüzde bunu seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **KAYNAK GRUBU ADINI YAZIN:** için *myResourceGroup* girin ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir VM oluşturup VM’nin ağ yönlendirme durumunu tanıladınız. Azure’un birkaç varsayılan yol oluşturduğunu öğrendiniz ve iki farklı hedefin yolunu test ettiniz. [Azure'da yönlendirme](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ve [özel yollar oluşturma](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route) hakkında daha fazla bilgi edinin.

Ağ İzleyicisinin [bağlantı sorun giderme](network-watcher-connectivity-portal.md) özelliğini kullanarak, giden VM bağlantıları için ayrıca gecikme süresini, VM ile bir uç nokta arasında izin verilen ve reddedilen ağ trafiğini ve bir uç nokta için kullanılan yolu belirleyebilirsiniz. Bir VM ile IP adresi veya URL gibi bir uç nokta arasındaki iletişimi, zaman içinde Ağ İzleyicisi bağlantı izleme özelliğini kullanarak nasıl izleyebileceğinizi öğrenin.

> [!div class="nextstepaction"]
> [Ağ bağlantısı izleme](connection-monitor.md)