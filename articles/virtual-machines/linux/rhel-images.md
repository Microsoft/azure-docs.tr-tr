---
title: Azure 'da görüntüleri Red Hat Enterprise Linux
description: Microsoft Azure Red Hat Enterprise Linux görüntüleri hakkında bilgi edinin
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 8/14/2019
ms.author: borisb
ms.openlocfilehash: eaabe9da20c22dd3e4d924887adcbc7081857e91
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035112"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Azure 'da görüntüleri Red Hat Enterprise Linux
Bu makalede, Azure Marketi 'ndeki kullanılabilir Red Hat Enterprise Linux (RHEL) görüntüleri, adlandırma ve bekletme ilkelerine göre birlikte açıklanmaktadır.

Tüm RHEL sürümleri için Red Hat destek ilkeleriyle ilgili bilgiler [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) sayfasında bulunabilir.

>[!Important]
> Şu anda Azure Marketi 'nde bulunan RHEL görüntüleri, kendi aboneliğinizi getir (KCG) veya Kullandıkça Öde (PAYG) lisanslama modellerini destekler. KCG ve PAYG 'ler arasında [Azure hibrit kullanım avantajı](../windows/hybrid-use-benefit-licensing.md) ve dinamik geçiş desteklenmez. Lisans modunun değiştirilmesi, sanal makinenin ilgili görüntüden yeniden dağıtılmasını gerektirir.

>[!Note]
> Azure Marketi galerisindeki RHEL görüntüleriyle ilgili herhangi bir sorun için lütfen Microsoft ile bir destek bileti gönderin.

## <a name="images-available-in-the-ui"></a>Kullanıcı arabiriminde bulunan görüntüler
Market 'te "Red hat" araması yaptığınızda veya Azure portal Kullanıcı arabiriminde bir kaynak oluşturduğunuzda, kullanılabilir RHEL görüntülerinin ve ilgili Red Hat ürünlerinin bir alt kümesini görürsünüz. Azure CLı/PowerShell/API kullanarak, kullanılabilir VM görüntülerinin tam kümesini her zaman elde edebilirsiniz.

Azure 'da kullanılabilir Red Hat görüntülerinin tam kümesini görmek için aşağıdaki komutu çalıştırın

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Adlandırma kuralı
Azure 'da VM görüntüleri Yayımcı, teklif, SKU ve sürüm tarafından düzenlenmiştir. Yayımcının birleşimi: teklif: SKU: sürüm, URN 'nin görüntüsüdür ve kullanılacak görüntüyü benzersiz olarak tanımlar.

Örneğin, `RedHat:RHEL:7-RAW:7.6.2018103108` 31 Ekim 2018 tarihinde oluşturulmuş RHEL 7,6 ham bölümlenmiş görüntüye başvurur.

Bir RHEL 7,6 VM oluşturma örneği aşağıda gösterilmiştir.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>"En son" bilinen ad
Azure REST API, belirli sürümü yerine sürüm için "en son" adının kullanılmasına izin verir. "En son" kullanımı, belirtilen Yayımcı, teklif ve SKU için kullanılabilir en son görüntüyü sağlar.

Örneğin `RedHat:RHEL:7-RAW:latest`, kullanılabilir en son RHEL 7 ailesi ham bölümlenmiş görüntüsünü ifade eder.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> Genel olarak, en son değerin [CompareTo yöntemi](https://msdn.microsoft.com/library/a5ts8tb6.aspx)kurallarına göre belirlenmesi için sürümlerin karşılaştırması.

### <a name="current-naming-convention"></a>Geçerli adlandırma kuralı
Şu anda yayınlanmış olan tüm RHEL görüntüleri, Kullandıkça Öde modelini kullanır ve [Azure 'Da Red Hat güncelleştirme altyapısına (rhuı)](https://aka.ms/rhui-update)bağlanır. Disk bölümleme şemasının (ham, LVM) sürüm yerine SKU 'da belirtildiği RHEL 7 ailesi görüntüleri için yeni bir adlandırma kuralı benimsenmiştir. RHEL görüntü sürümü 7 ham veya 7-LVM içermelidir. RHEL 6 aile adlandırması Şu anda değiştirilmedi.

Bu adlandırma kuralı 'nda 2 tür RHEL 7 görüntü SKU 'su bulunur: ikincil sürümü ve olmayan SKU 'ları listelerler. 7 ham veya 7-LVM SKU 'SU kullanmak istiyorsanız, sürümde dağıtmak istediğiniz RHEL ikincil sürümünü belirtebilirsiniz. "En son" sürümü seçerseniz, RHEL 'nin en son küçük sürümü hazırlanacaktır.

>[!NOTE]
> RHEL for SAP Image kümesinde RHEL sürümü düzeltilmeye devam eder. Bu nedenle, adlandırma kuralları SKU 'daki belirli bir sürümü içerir.

>[!NOTE]
> RHEL 6 görüntü kümesi yeni adlandırma kuralına taşınmadı.

## <a name="extended-update-support-eus"></a>Genişletilmiş güncelleştirme desteği (EUS)
2019 Nisan itibariyle, RHEL görüntüleri varsayılan olarak genişletilmiş güncelleştirme desteği (EUS) depolarına eklenmiş olarak kullanılabilir. RHEL EUS hakkında daha fazla ayrıntı, [Red Hat 'in belgelerinde](https://access.redhat.com/articles/rhel-eus)bulunabilir.

VM 'nizi Canus 'e nasıl TAŞıYACAĞıNıZ ve öğrendikleri son kullanım tarihleri hakkında daha fazla ayrıntı hakkında yönergeler [burada](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)bulunabilir.

>[!NOTE]
> RHEL ek özellikleri üzerinde EUS desteklenmez. Bu, genellikle RHEL ek özellikler kanalından kullanılabilen bir paketi yüklüyorsanız, duyurken bunu yapamazsınız. Red Hat ek özellikleri ürün yaşam döngüsü [burada](https://access.redhat.com/support/policy/updates/extras/)ayrıntılı olarak verilmiştir.

### <a name="for-customers-that-want-to-use-eus-images"></a>EUS görüntülerini kullanmak isteyen müşteriler için:
EUS depolarına eklenmiş olan görüntüleri kullanmak isteyen müşteriler, SKU 'da bir RHEL alt sürüm numarası içeren RHEL görüntüsünü kullanmalıdır. Bu görüntüler RAW bölümlenecek (yani LVM değil).

Örneğin, aşağıdaki 2 RHEL 7,4 görüntüsünü görebilirsiniz:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
Bu durumda `RedHat:RHEL:7.4:7.4.2019041718`, varsayılan olarak EUS depolarına eklenecektir `RedHat:RHEL:7-RAW:7.4.2018010506` ve varsayılan olarak EUS olmayan depolara eklenecektir.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>EUS görüntülerini kullanmak istemediğiniz müşteriler için:
Varsayılan olarak, EUS 'ye bağlı bir görüntü kullanmak istemiyorsanız, SKU 'da küçük bir sürüm numarası içermeyen bir görüntü kullanarak dağıtın.

#### <a name="rhel-images-with-eus"></a>EUS içeren RHEL görüntüleri
SKU 'da küçük bir sürüm içeren RHEL görüntüleri için aşağıdaki tablo uygulanacaktır.

>[!NOTE]
> Yazma sırasında, yalnızca RHEL 7,4 ve üzeri alt sürümlerde EUS desteği vardır. EUS artık RHEL < = 7,3 için desteklenmiyor.
>
> RHEL EUS kullanılabilirliği hakkında daha fazla ayrıntı için [burada](https://access.redhat.com/support/policy/updates/errata)bulunabilir.

İkincil sürüm |EUS resmi örneği              |EUS durumu                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat: RHEL: 7.4:7.4.2019041718 | 2019 Nisan ve üzeri yayımlanan görüntüler varsayılan olarak EUS olur|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | 2019 Haziran ve üzeri yayımlanan görüntüler varsayılan olarak EUS olacaktır |
RHEL 7,6      |RedHat: RHEL: 7.6:7.6.2019052206 | 2019 ve üzeri yayımlanan görüntüler varsayılan olarak EUS olacaktır  |
RHEL 8,0      |Yok                            | Red Hat 'te kullanılabilir EUS yok                               |


## <a name="list-of-rhel-images-available"></a>Kullanılabilir RHEL görüntüleri listesi
Aşağıdaki teklifler, şu anda genel kullanım için kullanılabilir:

Sunduğu| SKU | Bölümleme | Sağlama | Notlar
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-HAM    | RAW    | Linux Aracısı | RHEL 7. x resim ailesi. <br> Varsayılan olarak, EUS depolarına eklenmemiş.
|             | 7-LVM    | LVM    | Linux Aracısı | RHEL 7. x resim ailesi. <br> Varsayılan olarak, EUS depolarına eklenmemiş.
|             | 7-RAW-CI | HAM-Cı | Cloud-init  | RHEL 7. x resim ailesi. <br> Varsayılan olarak, EUS depolarına eklenmemiş.
|             | 6.7      | RAW    | Linux Aracısı |
|             | 6.8      | RAW    | Linux Aracısı |
|             | 6.9      | RAW    | Linux Aracısı |
|             | 6.10     | RAW    | Linux Aracısı |
|             | 7.2      | RAW    | Linux Aracısı |
|             | 7.3      | RAW    | Linux Aracısı |
|             | 7.4      | RAW    | Linux Aracısı | 2019 Nisan itibariyle varsayılan olarak, EUS depolarına eklenmiş.
|             | 7.5      | RAW    | Linux Aracısı | , Haziran 2019 itibariyle varsayılan olarak EUS depolarına eklendi.
|             | 7,6      | RAW    | Linux Aracısı | Varsayılan olarak 2019 Mayıs itibariyle EUS depolarında kullanıma açıldı.
|             | 7,7      | LVM    | Linux Aracısı | Varsayılan olarak, EUS depolarına eklendi.
RHEL-SAP      | 7.4      | LVM    | Linux Aracısı | SAP HANA ve Iş uygulamaları için RHEL 7,4
|             | 7.5      | LVM    | Linux Aracısı | SAP HANA ve Iş uygulamaları için RHEL 7,5
RHEL-SAP-HANA | 6.7      | RAW    | Linux Aracısı | SAP HANA için RHEL 6,7
|             | 7.2      | LVM    | Linux Aracısı | SAP HANA için RHEL 7,2
|             | 7.3      | LVM    | Linux Aracısı | SAP HANA için RHEL 7,3
RHEL-SAP-APPS | 6.8      | RAW    | Linux Aracısı | SAP Business Applications için RHEL 6,8
|             | 7.3      | LVM    | Linux Aracısı | SAP Business Applications için RHEL 7,3
RHEL-HA       | 7.4      | LVM    | Linux Aracısı | HA eklentisi ile RHEL 7,4
|             | 7.5      | LVM    | Linux Aracısı | HA eklentisi ile RHEL 7,5
|             | 7,6      | LVM    | Linux Aracısı | HA eklentisi ile RHEL 7,6
RHEL-SAP-HA   | 7.4      | LVM    | Linux Aracısı | HA eklentisi ile SAP için RHEL 7,4
|             | 7.5      | LVM    | Linux Aracısı | HA eklentisi ile SAP için RHEL 7,5
|             | 7,6      | LVM    | Linux Aracısı | HA eklentisi ile SAP için RHEL 7,6

### <a name="old-naming-convention"></a>Eski adlandırma kuralı
RHEL 7 resim ailesi ve RHEL 6 resim ailesi, yukarıda açıklanan adlandırma kuralı değişikliğine kadar SKU 'Larında belirli sürümleri kullanır.

Tam görüntü listesinde sayısal SKU 'Ları bulacaksınız. Yeni bir küçük yayın geldiğinde Microsoft ve Red Hat yeni sayısal SKU 'Lar oluşturur.

### <a name="other-available-offers-and-skus"></a>Diğer kullanılabilir teklifler ve SKU 'Lar
Kullanılabilir tekliflerin ve SKU 'ların tam listesi, yukarıdaki tabloda listelenenlerin ötesinde ek görüntüler içerebilir, örneğin `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Bu teklifler, belirli Market çözümlerinin desteğini sağlamak için kullanılabilir veya önizlemeler ve test amaçları için yayımlanabilir. Uyarı vermeden herhangi bir zamanda değiştirilebilir veya kaldırılabilirler. Bu kişilerin varlığı Microsoft veya Red Hat tarafından genel olarak belgelenmediği takdirde kullanmayın.

## <a name="publishing-policy"></a>Yayımlama ilkesi
Microsoft ve Red Hat güncelleştirme görüntüleri, belirli cele veya zaman zaman yapılandırma değişiklikleri/güncelleştirmeleri için gerekli olduğu gibi, yeni ikincil sürümler olarak yayımlanmıştır. Bir CVE düzeltmesinin yayını veya kullanılabilirliğini izleyen üç iş günü içinde, güncelleştirilmiş görüntüleri mümkün olan en kısa sürede sağlamak için çaba duyuyoruz.

Yalnızca belirli bir görüntü ailesindeki geçerli küçük sürümü güncelleştiririz. Daha yeni bir ikincil sürümün yayınlanmasıyla birlikte eski ikincil sürümü güncellemeyi durdurduk. Örneğin, RHEL 7,6 sürümü ile RHEL 7,5 görüntüleri artık güncelleştirilmeyecek.

>[!NOTE]
> RHEL Kullandıkça Öde görüntülerinde sağlanan etkin Azure VM 'Leri, Azure RHUı 'a bağlanır ve Red Hat tarafından yayınlanan ve Azure RHUı 'ye çoğaltılan (genellikle Red Hat 'in resmi sürümünden sonra 24 saatten kısa bir süre içinde) güncelleştirmeleri ve düzeltmeleri alabilir. . Bu VM 'Ler, güncelleştirmeleri almak için yeni yayımlanmış bir görüntü gerektirmez ve müşteriler güncelleştirmenin başlatılacağı zaman üzerinde tam denetime sahiptir.

## <a name="image-retention-policy"></a>Görüntü bekletme ilkesi
Geçerli ilkeniz, önceden yayımlanan tüm görüntüleri tutmak. Her türlü soruna neden olan bilinen görüntüleri kaldırma hakkını saklı tutarız. Örneğin, sonraki platform veya bileşen güncelleştirmeleri nedeniyle yanlış yapılandırmalara sahip görüntüler kaldırılabilir. Kaldırılabilir olan görüntüler, görüntü kaldırılmadan önce 30 güne kadar bildirim sağlamak için geçerli Market ilkesini izler.

## <a name="next-steps"></a>Sonraki adımlar
* Azure Red hat [güncelleştirme altyapısı hakkında](https://aka.ms/rhui-update)daha fazla bilgi edinin.
* Tüm RHEL sürümleri için Red Hat destek ilkeleriyle ilgili bilgiler [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) sayfasında bulunabilir.
