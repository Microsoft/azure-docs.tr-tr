---
title: Öğretici-Azure portal kullanarak ağlarla iletişim sorununu tanılama
titleSuffix: Azure Network Watcher
description: Bu öğreticide, ağ izleyicisinin VPN Tanılama özelliğini kullanarak bir Azure sanal ağ geçidi aracılığıyla şirket içi veya diğer sanal ağa bağlı bir Azure sanal ağı arasında bir iletişim sorunu tanılamayı öğrenin.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: e9122a323556bebd4ed08ab99f2183313d04eaca
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066033"
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>Öğretici: Azure portalını kullanarak ağlar arasında bir iletişim sorununu tanılama

Sanal ağ geçidi, bir Azure sanal ağını şirket içine ya da diğer sanal ağa bağlar. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Ağ İzleyicisi'nin VPN tanılama özelliği ile bir sanal ağ geçidi sorununu tanılama
> * Bir ağ geçidi bağlantısı sorununu tanılama
> * Ağ geçidi ile ilgili bir sorunu çözme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

VPN tanılamayı kullanmak için çalışır durumda bir VPN ağ geçidinizin olması gerekir. Tanılamak için mevcut bir VPN ağ geçidiniz yoksa, bir [PowerShell betiği](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) kullanarak bir tane dağıtabilirsiniz. PowerShell betiğini şuradan çalıştırabilirsiniz:
- **Yerel bir PowerShell yüklemesi**: betik Azure PowerShell `Az` modülünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse bkz. [Azure PowerShell yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.
- **Azure Cloud Shell**: [Azure Cloud Shell](https://shell.azure.com/powershell)’de PowerShell’in en son sürümü yüklü ve yapılandırılmış durumdadır ve Azure’da oturum açmanızı sağlar.

Betiğin bir VPN ağ geçidi oluşturması yaklaşık bir saat sürer. Kalan adımlarda, tanılamakta olduğunuz ağ geçidinin bu betikle dağıtılan ağ geçidi olduğu varsayılır. Bunun yerine kendi mevcut ağ geçidinizi tanılarsanız sonuçlarınız farklılık gösterir.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="enable-network-watcher"></a>Ağ İzleyicisini etkinleştirme

Doğu ABD bölgesinde etkinleştirilmiş bir ağ izleyicisi zaten varsa [Ağ geçidi tanılama](#diagnose-a-gateway) bölümüne atlayın.

1. Portalda **Tüm hizmetler**’i seçin. **Filtre kutusu**’na *Ağ İzleyicisi* yazın. Sonuçlarda **Ağ İzleyicisi** göründüğünde seçin.
2. **Bölgeler**’i seçip genişletin ve sonra aşağıdaki resimde gösterildiği gibi **Doğu ABD**’nin sağındaki **...** öğesini seçin:

    ![Ağ İzleyicisini etkinleştirme](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. **Ağ İzleyicisini etkinleştirme**’yi seçin.

## <a name="diagnose-a-gateway"></a>Ağ geçidi tanılama

1. Portalın sol tarafından **Tüm hizmetler**’i seçin.
2. **Filtre** kutusuna *ağ izleyicisi* yazmaya başlayın. **Ağ İzleyicisi**, arama sonuçlarında görüntülendiğinde seçin.
3. **AĞ TANILAMA ARAÇLARI** altında **VPN Tanılama**’yı seçin.
4. **Depolama hesabı**’nı ve ardından tanılama bilgilerini yazmak istediğiniz depolama hesabını seçin.
5. **Depolama hesapları** listesinden kullanmak istediğiniz depolama hesabını seçin. Mevcut bir depolama hesabınız yoksa **+ Depolama hesabı**’nı seçin, gerekli bilgileri girin veya seçin ve ardından **Oluştur**’u seçerek bir depolama hesabı oluşturun. [Önkoşullar](#prerequisites) bölümündeki betiği kullanarak bir VPN ağ geçidi oluşturduysanız, depolama hesabını ağ geçidi ile aynı kaynak grubunda (*TestRG1*) oluşturmak isteyebilirsiniz.
6. **Kapsayıcılar** listesinden kullanmak istediğiniz kapsayıcıyı ve sonra **Seç**’i seçin. Kapsayıcınız yoksa **+ Kapsayıcı** öğesini seçin, kapsayıcı için bir ad girin, ardından **Tamam**’ı seçin.
7. Bir ağ geçidi seçin ve ardından **Sorun gidermeyi başlat** seçeneğini belirleyin. Aşağıdaki resimde gösterildiği gibi test, **Vnet1GW** adlı bir ağ geçidine karşı gerçekleştirilir:

    ![VPN tanılama](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. Test çalışırken, önceki resimde **Başlatılmadı** ifadesinin gösterildiği **SORUN GİDERME DURUMU** sütununda **Çalışıyor** ifadesi görünür. Testin çalışması birkaç dakika sürebilir.
9. Tamamlanmış bir testin durumunu görüntüleyin. Aşağıdaki resimde, tamamlanmış bir tanılama testinin durum sonuçları gösterilmiştir:

    ![Ekran görüntüsü, bir tanılama testinin durum sonuçlarını, bu örnekte Özet ve ayrıntı dahil sağlıksız olduğunu gösterir.](./media/diagnose-communication-problem-between-networks/status.png)

    **SORUN GİDERME DURUMU**’nun **Sağlıksız** olduğunu ve ayrıca **Durum** sekmesinde sorunla ilgili bir **Özet** ve **Ayrıntı** seçeneğinin bulunduğunu görebilirsiniz.
10. **Eylem** sekmesini seçtiğinizde VPN tanılama, ek bilgiler sağlar. Aşağıdaki resimde gösterilen örnekte, VPN tanılama işlemi her bir bağlantının durumunu denetlemeniz gerektiğini bildirir:

    ![Ekran görüntüsü, size ek bilgi sağlayan eylem sekmesini gösterir.](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>Ağ geçidi bağlantısı tanılama

Ağ geçidi bir ağ geçidi bağlantısı üzerinden diğer ağlara bağlanır. Bir sanal ağ ile bağlı ağ arasında başarılı bir iletişim kurulabilmesi için hem ağ geçidinin hem de ağ geçidi bağlantılarının sağlıklı olması gerekir.

1. [Ağ geçidi tanılama](#diagnose-a-gateway) bölümündeki 7. adımı, bu kez bir bağlantı seçerek tekrar tamamlayın. Aşağıdaki örnekte, **VNet1toSite1** adlı bir bağlantı test edilmiştir:

    ![Ekran görüntüsü seçili bir bağlantı için nasıl sorun gidermeye başlayakullanacağınızı gösterir.](./media/diagnose-communication-problem-between-networks/connection.png)

    Test birkaç dakika boyunca çalışır.
2. Bağlantı testi tamamlandıktan sonra **Durum** ve **Eylem** sekmelerinde aşağıdaki resimlerde gösterilen sonuçlara benzer sonuçlar alırsınız:

    ![Bağlantı durumu](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![Bağlantı eylemi](./media/diagnose-communication-problem-between-networks/connection-action.png)

    VPN tanılama, **Durum** sekmesinde neyin yanlış olduğu konusunda sizi bilgilendirir ve **Eylem** sekmesinde soruna neden olabilecek durumlarla ilgili birkaç öneride bulunur.

    Test ettiğiniz ağ geçidi [Önkoşullar](#prerequisites) bölümündeki [betik](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ile dağıtılan ağ geçidi ise, **Durum** sekmesindeki sorun ve **Eylemler** sekmesindeki ilk iki öğe, sorunun tam olarak ne olduğunu açıklar. Betik, şirket içi VPN ağ geçidi cihazı için 23.99.221.164 şeklinde bir yer tutucu IP adresi yapılandırır.

    Sorunu çözmek için şirket içi VPN ağ geçidinizin [düzgün yapılandırıldığından](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) emin olmanız ve yerel ağ geçidi için betik tarafından yapılandırılan IP adresini şirket içi VPN ağ geçidinizin gerçek genel adresi ile değiştirmeniz gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

VPN ağ geçidini [önkoşullar](#prerequisites) bölümündeki betiği kullanarak yalnızca bu öğretici tamamlamak amacıyla oluşturduysanız, kaynak grubunu ve içerdiği tüm kaynakları silin:

1. Portalın üst kısmındaki **Arama** kutusuna *TestRG1* yazın. Arama sonuçlarında **TestREG1**’i gördüğünüzde seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **KAYNAK GRUBU ADINI YAZIN:** için *TestRG1* girin ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, sanal ağ geçidi ile bir sorunu tanılama hakkında bilgi edindiniz. Anormallikleri gözden geçirebilmek için bir VM ile ağ iletişimini günlüğe kaydetmek isteyebilirsiniz. Nasıl yapılacağını öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Bir VM’ye gelen ve VM’den giden ağ trafiğini günlüğe kaydetme](network-watcher-nsg-flow-logging-portal.md)
