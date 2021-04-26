---
title: Azure NetApp Files için kaynak sınırları | Microsoft Docs
description: Azure NetApp Files kaynaklarına yönelik limitleri ve kaynak sınırı artışını nasıl isteyeceğini açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: f023bfa2b3941f7d667f4be34a8ee8dc1ed9a9c3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750203"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files için kaynak sınırları

Azure NetApp Files için kaynak sınırlarını anlamak, birimlerinizi yönetmenize yardımcı olur.

## <a name="resource-limits"></a>Kaynak sınırları

Aşağıdaki tabloda Azure NetApp Files için kaynak sınırları açıklanmaktadır:

|  Kaynak  |  Varsayılan limit  |  Destek isteği aracılığıyla ayarlanabilir  |
|----------------|---------------------|--------------------------------------|
|  Abonelik başına Azure bölgesi başına NetApp hesabı sayısı  |  10    |  Yes   |
|  NetApp hesabı başına kapasite havuzlarının sayısı   |    25     |   Yes   |
|  Abonelik başına birim sayısı   |    500     |   Yes   |
|  Kapasite havuzu başına birim sayısı     |    500   |    Yes     |
|  Birim başına anlık görüntü sayısı       |    255     |    No        |
|  Azure sanal ağı başına Azure NetApp Files (Microsoft. NetApp/birimler) için temsilci seçilen alt ağ sayısı    |   1   |    No    |
|  Bir VNet 'te kullanılan IP sayısı (anında eşlenen sanal ağlar dahil) Azure NetApp Files   |    1000   |    No   |
|  Tek bir kapasite havuzunun en küçük boyutu   |  4 TiB     |    No  |
|  Tek bir kapasite havuzunun en büyük boyutu    |  500 TiB   |   No   |
|  Tek bir birimin en küçük boyutu    |    100 GiB    |    No    |
|  Tek bir birimin en büyük boyutu     |    100 TiB    |    No    |
|  Tek bir dosyanın en büyük boyutu     |    16 TiB    |    No    |    
|  Tek bir dizindeki dizin meta verilerinin en büyük boyutu      |    320 MB    |    No    |    
|  Birim başına en fazla dosya sayısı ([maxfiles](#maxfiles))     |    100.000.000    |    Yes    |    
|  Birim başına en fazla dışarı aktarma ilkesi kuralı sayısı     |    5  |    No    | 
|  El ile QoS birimi için atanan en düşük aktarım hızı     |    1 MIB/sn   |    No    |    
|  El ile QoS birimi için atanan en yüksek aktarım hızı     |    4.500 MIB/sn    |    No    |    
|  Çapraz bölge çoğaltma verileri koruma birimlerinin sayısı (hedef birimler)     |    5    |    Yes    |     

Dizin meta verileri (320 MB) için en büyük boyut sınırına yaklaşıp yaklaşmadığını görmek için, [bir dizinin sınır boyutuna yaklaşıp yaklaşmadığını belirleme nasıl yaparım?](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size)bakın.   

Daha fazla bilgi için bkz. [Kapasite Yönetimi SSS](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="maxfiles-limits"></a>Maxfiles limitleri <a name="maxfiles"></a> 

Azure NetApp Files birimlerde *maxfiles* adlı bir sınır vardır. Maxfiles sınırı, bir birimin içerebileceği dosya sayısıdır. Linux dosya sistemleri, sınır olarak ifade eder *.* Bir Azure NetApp Files birimi için maxfiles limiti, birimin boyutuna (Kota) göre dizinlenir. Bir birim için maxfiles sınırı, sağlanan birim boyutu başına 20.000.000 dosya hızında artar veya azalır. 

Hizmet, bir birimin sağlanan boyutuna bağlı olarak maxfiles sınırını dinamik olarak ayarlar. Örneğin, başlangıçta 1 TiB boyutuyla yapılandırılan bir birimin maxfiles sınırı 20.000.000 olur. Birimin boyutuyla ilgili sonraki değişiklikler, aşağıdaki kurallara göre maxfiles sınırının otomatik olarak yeniden okundu olarak oluşmasına neden olur: 

|    Birim boyutu (Kota)     |  Maxfiles limitini otomatik olarak ayarlama    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20.000.000     |
|    > 1 TiB, ancak <= 2 TiB    |    40.000.000     |
|    > 2 TiB, ancak <= 3 TiB    |    60.000.000     |
|    > 3 TiB ancak <= 4 TiB    |    80.000.000     |
|    > 4 TiB                 |    100.000.000    |

Bir birim için en az 4 TiB kota ayırdıysanız, maxfiles (ınomdes) limitini 100.000.000 ' den fazla artırmak için bir [destek isteği](#limit_increase) başlatabilirsiniz. Artırmış olduğunuz her 100.000.000 dosya için (veya bir kesri), karşılık gelen birim kotasını 4 TiB ile artırmanız gerekir.  Örneğin, maxfiles limitini 100.000.000 dosyadan 200.000.000 dosya (veya aralarında herhangi bir sayı) olarak artırırsanız, birim kotasını 4 TiB 'den 8 TiB 'ye artırmanız gerekir.

Birim kotanızın en az 20 TiB olması durumunda maxfiles limitini 500.000.000 olarak artırabilirsiniz. <!-- ANF-11854 --> 

## <a name="request-limit-increase"></a>İstek sınırı artışı <a name="limit_increase"></a> 

Yukarıdaki tablodan ayarlanabilir sınırları artırmak için bir Azure destek isteği oluşturabilirsiniz. 

Azure portal gezinti düzleminden: 

1. **Yardım ve destek**' e tıklayın.
2. **+ Yeni destek isteği**' ne tıklayın.
3. Temel bilgiler sekmesinde, aşağıdaki bilgileri sağlayın: 
    1. Sorun türü: **hizmet ve abonelik sınırlarını (kotalar)** seçin.
    2. Abonelikler: kotanın artması gereken kaynak için aboneliği seçin.
    3. Kota türü: **Depolama: Azure NetApp Files sınırlarını** seçin.
    4. **İleri: çözümler**' e tıklayın.
4. Ayrıntılar sekmesinde:
    1. Açıklama kutusunda, ilgili kaynak türü için aşağıdaki bilgileri sağlayın:

        |  Kaynak  |    Üst kaynaklar      |    İstenen yeni sınırlar     |    Kota artışının nedeni       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Hesap |  *Abonelik kimliği*   |  *İstenen yeni en büyük **Hesap** numarası*    |  *İstekte hangi senaryo veya kullanım örneği istendi?*  |
        |  Havuz    |  *Abonelik KIMLIĞI, NetApp hesap URI 'SI*  |  *İstenen yeni en büyük **Havuz** numarası*   |  *İstekte hangi senaryo veya kullanım örneği istendi?*  |
        |  Birim  |  *Abonelik KIMLIĞI, NetApp hesap URI 'SI, kapasite havuzu URI 'SI*   |  *İstenen yeni en yüksek **birim** numarası*     |  *İstekte hangi senaryo veya kullanım örneği istendi?*  |
        |  Maxfiles  |  *Abonelik KIMLIĞI, NetApp hesap URI 'SI, kapasite havuzu URI 'SI, birim URI 'SI*   |  *İstenen yeni en yüksek **maxfiles** numarası*     |  *İstekte hangi senaryo veya kullanım örneği istendi?*  |    
        |  Bölgeler arası çoğaltma veri koruma birimleri  |  *Abonelik KIMLIĞI, hedef NetApp hesap URI 'si, hedef kapasite havuzu URI 'SI, kaynak NetApp hesap URI 'si, kaynak kapasitesi havuzu URI 'SI, kaynak birim URI 'si*   |  * Yeni çok sayıda **çapraz bölge çoğaltma verileri koruma birimi (hedef birim)** için istenen     |  _What senaryo veya kullanım örneği istekte istendi mi? *  |    

    2. Uygun destek yöntemini belirtin ve sözleşme bilgilerinizi sağlayın.

    3. Ileri ' ye tıklayın, isteği oluşturmak için **+ Oluştur** ' a tıklayın. 


## <a name="next-steps"></a>Sonraki adımlar  

- [Azure NetApp Files’ın depolama hiyerarşisini anlama](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files için maliyet modeli](azure-netapp-files-cost-model.md)
