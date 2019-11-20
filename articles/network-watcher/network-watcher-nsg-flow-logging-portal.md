---
title: Sanal makineye gelen ve sanal makineden giden ağ trafiği akışını günlüğe kaydetme - öğretici - Azure portalı | Microsoft Docs
description: Ağ İzleyicisi’nin NSG akış günlükleri özelliğini kullanarak bir sanal makineye gelen ve sanal makineden giden ağ trafiği akışını günlüğe kaydetme hakkında bilgi edinin.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 3e5490a4d74f10532764029f7a83788e3e39b592
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636209"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak bir sanal makineye gelen ve giden ağ trafiğini günlüğe kaydet

Ağ güvenlik grubu (NSG), bir sanal makineye gelen trafiği ve sanal makineden giden trafiği filtrelemenize olanak sağlar. Ağ İzleyicisinin NSG akış günlüğü özelliği ile NSG aracılığıyla akan trafiği günlüğe kaydedebilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Ağ güvenlik grubu ile sanal makine oluşturma
> * Ağ İzleyicisini etkinleştirme ve Microsoft.Insights sağlayıcısını kaydetme
> * Ağ İzleyicisinin NSG akış günlüğü özelliğini kullanarak NSG için bir trafik akışı günlüğünü etkinleştirme
> * Günlüğe kaydedilen verileri indirme
> * Günlüğe kaydedilen verileri görüntüleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-vm"></a>VM oluşturma

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. **İşlem**' ı seçin ve ardından **Windows Server 2016 Datacenter** veya **Ubuntu Server**sürümünü seçin.
3. Aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Tamam**’ı seçin:

    |Ayar|Value|
    |---|---|
    |Name|myVm|
    |Kullanıcı adı| Seçtiğiniz bir kullanıcı adını girin.|
    |istemcisiyle yönetilen bir cihaz için)| Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.|
    |Subscription| Aboneliğinizi seçin.|
    |Resource group| **Yeni oluştur**’u seçin ve **myResourceGroup** değerini girin.|
    |Location| **Doğu ABD**’yi seçin|

4. Sanal makine için bir boyut seçin ve **Seç** seçeneğini belirleyin.
5. **Ayarlar** bölümünde tüm varsayılanları kabul edin ve **Tamam**’ı seçin.
6. **Özet**’in **Oluştur** bölümünde **Oluştur**’u seçerek sanal makine dağıtımını başlatın. Sanal makinenin dağıtılması birkaç dakika sürer. Kalan adımlara devam etmeden önce sanal makinenin dağıtımı tamamlamasını bekleyin.

Sanal makinenin oluşturulması birkaç dakika sürer. Sanal makinenin oluşturulması tamamlanmadan kalan adımlara devam etmeyin. Portal sanal makineyi oluştururken, **myVm-nsg** adıyla bir ağ güvenlik grubu da oluşturur ve bunu sanal makine için ağ arabirimiyle ilişkilendirir.

## <a name="enable-network-watcher"></a>Ağ İzleyicisini etkinleştirme

Doğu ABD bölgesinde etkinleştirilmiş bir ağ izleyiciniz zaten varsa [Insights sağlayıcısını kaydetme](#register-insights-provider) bölümüne atlayın.

1. Portalda **Tüm hizmetler**’i seçin. **Filtre kutusu**’na *Ağ İzleyicisi* yazın. **Ağ İzleyicisi**, sonuçlarda görüntülendiğinde onu seçin.
2. **Bölgeler**’i seçip genişletin ve sonra aşağıdaki resimde gösterildiği gibi **Doğu ABD**’nin sağındaki **...** öğesini seçin:

    ![Ağ İzleyicisini etkinleştirme](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. **Ağ izleyicisini etkinleştirme**’yi seçin.

## <a name="register-insights-provider"></a>Insights sağlayıcısını kaydetme

NSG akış günlüğü kaydı için **Microsoft.Insights** sağlayıcısı gerekir. Sağlayıcıyı kaydetmek için aşağıdaki adımları tamamlayın:

1. Portalın sol üst köşesinde **Tüm hizmetler**’i seçin. Filtre kutusuna *Abonelikler* yazın. **Abonelikler**, arama sonuçlarında görüntülendiğinde onu seçin.
2. Abonelikler listesinden sağlayıcısını etkinleştirmek istediğiniz aboneliği seçin.
3. **AYARLAR** bölümünden **Kaynak sağlayıcıları**’nı seçin.
4. **microsoft.insights** sağlayıcısı için **DURUM** değerinin, aşağıdaki resimde gösterildiği gibi **Kayıtlı** olduğunu onaylayın. Durum **Kaydedilmedi** ise sağlayıcının sağındaki **Kaydet**’i seçin.

    ![Sağlayıcıyı kaydetme](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>NSG akış günlüğünü etkinleştirme

1. NSG akış günlüğü verileri bir Azure Depolama hesabına yazılır. Azure Depolama hesabı oluşturmak için, portalın sol üst köşesinden **+ Kaynak oluştur**’u seçin.
2. **Depolama**’yı ve sonra **Depolama hesabı - blob, dosya, tablo, kuyruk** öğesini seçin.
3. Aşağıdaki bilgileri girin veya seçin, kalan varsayılan değerleri kabul edin ve sonra **Oluştur**’u seçin.

    | Ayar        | Value                                                        |
    | ---            | ---   |
    | Name           | 3-24 karakter uzunluğundadır. Yalnızca küçük harfler ve rakamlar içerebilir ve tüm Azure Depolama hesapları arasında benzersiz olmalıdır.                                                               |
    | Location       | **Doğu ABD**’yi seçin                                           |
    | Resource group | **Var olanı kullan**’ı seçin ve sonra **myResourceGroup** seçeneğini belirleyin |

    Depolama hesabının oluşturulması yaklaşık bir dakika sürebilir. Depolama hesabı oluşturulmadan kalan adımlara devam etmeyin. Yenisini oluşturmak yerine mevcut bir depolama hesabını kullanırsanız, depolama hesabı için **AYARLAR** bölümünde **Güvenlik duvarları ve sanal ağlar** için **Tüm ağlar** (varsayılan) seçeneği belirlenmiş bir depolama hesabı seçtiğinizden emin olun. Her durumda, depolama hesabının NSG ile aynı bölgede olması gerekir. 
    
    > [!NOTE]
    > Microsoft. Insight ve Microsoft. Network sağlayıcıları şu anda [Azure depolama için güvenilen Microsoft Hizmetleri](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)olarak desteklenirken, NSG akış günlükleri yine de tamamen eklendi. NSG akış günlüğünü etkinleştirmek için, yukarıda belirtilen **tüm ağların** seçili olması gerekir.
    
4. Portalın sol üst köşesinde **Tüm hizmetler**’i seçin. **Filtre** kutusuna *Ağ İzleyicisi* yazın. **Ağ İzleyicisi**, arama sonuçlarında görüntülendiğinde onu seçin.
5. **GÜNLÜKLER** bölümünde, aşağıdaki resimde gösterildiği gibi **NSG akış günlükleri**’ni seçin:

    ![NSG](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. NSG listesinden **myVm-nsg** adlı NSG’yi seçin.
7. **Akış günlükleri ayarları** bölümünde **Açık** seçeneğini belirleyin.
8. Akış günlüğü sürümünü seçin. Sürüm 2, akış oturumu istatistikleri içerir (bayt ve paket)

   ![Akış günlükleri sürümünü seçin](./media/network-watcher-nsg-flow-logging-portal/select-flow-log-version.png)

9. 3\. adımda oluşturduğunuz depolama hesabını seçin.
   > [!NOTE]
   > NSG akış günlükleri şu durumlarda depolama hesaplarıyla çalışmaz:
   > * Depolama hesaplarında güvenlik duvarı etkinleştirilmiştir.
   > * Depolama hesaplarında [hiyerarşik ad alanı](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) etkindir.
1. Portalın sol üst köşesinde **Tüm hizmetler**’i seçin. **Filtre** kutusuna *Ağ İzleyicisi* yazın. **Ağ İzleyicisi**, arama sonuçlarında görüntülendiğinde seçin.
10. **Bekletme (gün)** değerini 5 olarak ayarlayın ve **Kaydet**’i seçin.
    > [!IMPORTANT]
    > Şu anda ağ Izleyicisi için ağ [güvenlik grubu (NSG) akış günlüklerinin](network-watcher-nsg-flow-logging-overview.md) , bekletme ilkesi ayarlarına göre blob depolamadan otomatik olarak silinmediği bir sorun vardır. Sıfır olmayan bir bekletme ilkeniz varsa, herhangi bir ücret ödemeden kaçınmak için saklama süresini aşan depolama bloblarını düzenli aralıklarla silmenizi öneririz. NSG akış günlüğü depolama blogunu silme hakkında daha fazla bilgi için bkz. [NSG akış günlüğü depolama Bloblarını silme](network-watcher-delete-nsg-flow-log-blobs.md).

## <a name="download-flow-log"></a>Akış günlüğünü indirme

1. Ağ İzleyicisinden, portalda **GÜNLÜKLER** bölümündeki **NSG akış günlükleri**’ni seçin.
2. Aşağıdaki resimde gösterildiği gibi **Yapılandırılan depolama hesaplarından akış günlüklerini indirebilirsiniz** seçeneğini belirleyin:

   ![Akış günlüklerini indirme](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. [NSG akış günlüğünü etkinleştirme](#enable-nsg-flow-log) bölümünün 2. adımında yapılandırdığınız depolama hesabını seçin.
4. **BLOB hizmeti**altında Bloblar' ı seçin ve ardından **Öngörüler-logs-networksecuritygroupflowevent** kapsayıcısını seçin.
5. Kapsayıcıda, aşağıdaki resimde gösterildiği gibi, bir PT1H. JSON dosyasına ulaşana kadar klasör hiyerarşisine gidin. Günlük dosyaları şu adlandırma kuralını izleyen bir klasör hiyerarşisine yazılır: https://{storageAccountName}. blob. Core. Windows. net/Insights-logs-networksecuritygroupflowevent/RESOURCEID =/Subscriptions/{SubscriptionID}/ RESOURCEGROUPS/{resourceGroupName}/SAĞLAYıCıLAR/MICROSOFT. NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y = {Year}/m = {month}/d = {Day}/h = {saat}/m = 00/macAddress = {macAddress}/Pt1h.exe

   ![Akış günlüğü](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

6. PT1H.json dosyasının sağındaki **...** öğesini seçin ve **İndir** seçeneğini belirleyin.

## <a name="view-flow-log"></a>Akış günlüğünü görüntüleme

Aşağıdaki json, verileri günlüğe kaydedilen her akış için PT1H.json dosyasında göreceklerinize bir örnektir:

### <a name="version-1-flow-log-event"></a>Sürüm 1 akış günlüğü olayı
```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [
            {
                "rule": "UserRule_default-allow-rdp",
                "flows": [
                    {
                        "mac": "000D3A170C69",
                        "flowTuples": [
                            "1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"
                        ]
                    }
                ]
            }
        ]
    }
}
```
### <a name="version-2-flow-log-event"></a>Sürüm 2 akış günlüğü olayı
```json
{
    "time": "2018-11-13T12:00:35.3899262Z",
    "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 2,
        "flows": [
            {
                "rule": "DefaultRule_DenyAllInBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                            "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                            "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                        ]
                    }
                ]
            },
            {
                "rule": "DefaultRule_AllowInternetOutBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                            "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                            "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                            "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                        ]
                    }
                ]
            }
        ]
    }
}
```

Önceki çıktıda yer alan **mac** değeri, sanal makine oluşturulurken oluşturulan ağ arabiriminin MAC adresidir. **flowTuples** için virgülle ayrılmış bilgiler aşağıdaki gibidir:

| Örnek veriler | Verilerin temsil ettiği   | Açıklama                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1542110377   | Zaman damgası             | Akışın oluştuğu zamanın UNIX EPOCH biçiminde zaman damgası. Önceki örnekte tarih, 1 Mayıs 2018, 14:59:05 GMT olarak dönüştürülür.                                                                                    |
| 10.0.0.4  | Kaynak IP adresi      | Akışın geldiği kaynak IP adresi. 10.0.0.4, [Sanal makine oluşturma](#create-a-vm) bölümünde oluşturduğunuz sanal makinenin özel IP adresidir.
| 13.67.143.118     | Hedef IP adresi | Akışın hedeflendiği hedef IP adresi.                                                                                  |
| 44931        | Kaynak bağlantı noktası            | Akışın geldiği kaynak bağlantı noktası.                                           |
| 443         | Hedef bağlantı noktası       | Akışın hedeflendiği hedef bağlantı noktası. Trafiğin bağlantı noktası 443 ' e gönderildiği için, günlük dosyasındaki **UserRule_default-Allow-RDP**adlı kural akışı işlenir.                                                |
| T            | Protocol               | Akış protokolünün TCP (T) mi yoksa UDP (U) mi olduğu.                                  |
| O            | Direction              | Trafiğin gelen (I) mi yoksa giden (O) mi olduğu.                                     |
| A            | Action                 | Trafiğe izin mi verildiği (A) yoksa trafiğin ret mi edildiği (D).  
| C            | Yalnızca akış durumu **sürüm 2** | Akışın durumunu yakalar. Olası durumlar **B**: Bir akış oluşturulduğunda başlar. İstatistikler sağlanmamış. **C**: Devam eden bir akış için devam edin. İstatistikler 5 dakikalık aralıklarla sağlanır. **E**: Bir akış sonlandırıldığında bitiş. İstatistikler sağlanır. |
| 30 | Kaynak gönderilen paketler **yalnızca hedef sürüm 2** ' ye | Son güncelleştirmeden bu yana kaynaktan hedefe gönderilen TCP veya UDP paketlerinin toplam sayısı. |
| 16978 | Gönderilen bayt-kaynak yalnızca hedef **sürüm 2** | Son güncelleştirmeden bu yana kaynaktan hedefe gönderilen TCP veya UDP paket baytlarının toplam sayısıdır. Paket baytları paket üst bilgisini ve yükünü içerir. |
| 24 | Gönderilen paketler-hedef yalnızca kaynak **sürüm 2** ' ye | Son güncelleştirmeden bu yana hedefin kaynağına Gönderilen TCP veya UDP paketlerinin toplam sayısı. |
| 14008| Gönderilen bayt-hedef yalnızca kaynak **sürüm 2** ' ye | Son güncelleştirmeden bu yana, hedefin kaynağına Gönderilen TCP ve UDP paket baytlarının toplam sayısıdır. Paket baytları paket üst bilgisini ve yükünü içerir.|

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, NSG için NSG akış günlüğünün nasıl etkinleştirildiğini öğrendiniz. Ayrıca bir dosyada günlüğe kaydedilen verilerin nasıl indirileceğini ve görüntüleneceğini de öğrendiniz. Json dosyasındaki işlenmemiş verilerin yorumlanması güç olabilir. Verileri görselleştirmek için, Ağ İzleyicisi [trafik analizini](traffic-analytics.md), Microsoft [PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) ve diğer araçları kullanabilirsiniz.
