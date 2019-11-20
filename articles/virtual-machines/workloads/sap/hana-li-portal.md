---
title: Azure HANA büyük örnekler denetim Azure portal Microsoft Docs
description: Portal aracılığıyla Azure HANA büyük örneklerini nasıl tanımlayabileceğinizi ve bunlarla etkileşime girebileceğinizi açıklar
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099810"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Azure portalı üzerinden Azure HANA Büyük Örnekler denetimi
Bu belge, [Hana büyük örneklerinin](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) [Azure Portal](https://portal.azure.com) nasıl sunulduğunu ve sizin Için dağıtılan Hana büyük örnek birimleri ile Azure Portal aracılığıyla hangi etkinliklerin yapılabileceğini anlatmaktadır. Azure portal 'daki HANA büyük örneklerin görünürlüğü, şu anda genel önizlemede olan HANA büyük örnekleri için bir Azure Kaynak sağlayıcısı aracılığıyla sunulmaktadır

## <a name="register-hana-large-instance-resource-provider"></a>HANA büyük örnek kaynak sağlayıcısını Kaydet
Genellikle HANA büyük örnek dağıtımları için kullandığınız Azure aboneliğiniz, HANA büyük örnek kaynak sağlayıcısı için kaydedilir. Ancak, HANA büyük örnek birimleri dağıttığınızı göremiyorsanız, kaynak sağlayıcısını Azure aboneliğinize kaydetmeniz gerekir. HANA büyük örnek kaynak sağlayıcısını kaydetmenin iki yolu vardır

### <a name="register-through-cli-interface"></a>CLı arabirimine kaydol
Azure CLı arabirimi aracılığıyla HANA büyük örnek dağıtımı için kullanılan Azure aboneliğinizde oturum açmanız gerekir. Aşağıdaki komutla HANA büyük örnek sağlayıcısını kaydedebilirsiniz (yeniden):
    
    az provider register --namespace Microsoft.HanaOnAzure

Daha fazla bilgi için bkz. [Azure kaynak sağlayıcıları ve türleri](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Azure portal kaydetme
Azure portal aracılığıyla HANA büyük örnek kaynak sağlayıcısını kaydedebilirsiniz (yeniden). Aboneliğinizi Azure portal ' de listeetmeniz ve HANA büyük örnek birimlerinizi dağıtmak için kullanılan aboneliğe çift tıklamanız gerekir. Aboneliğinizin Genel Bakış sayfasında, aşağıda gösterildiği gibi "kaynak sağlayıcıları" öğesini seçin ve arama penceresine "HANA" yazın. 

![Azure portal aracılığıyla HLI RP 'yi kaydetme](./media/hana-li-portal/portal-register-hli-rp.png)

Gösterilen ekran görüntüsünde, kaynak sağlayıcı zaten kaydettirildi. Kaynak sağlayıcısı henüz kayıtlı değilse, "yeniden Kaydet" veya "kaydolun" düğmesine basın.

Daha fazla bilgi için bkz. [Azure kaynak sağlayıcıları ve türleri](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Azure portal HANA büyük örnek birimlerinin görüntülenmesi
Bir HANA büyük örnek dağıtım isteği gönderilirken, HANA büyük örneklerine da bağlanmakta olduğunuz Azure aboneliğini belirtmeniz istenir. HANA büyük örnek birimlerinde çalıştırılan SAP uygulama katmanını dağıtmak için kullandığınız aboneliğin aynısını kullanmak önerilir.
İlk HANA büyük örnekleriniz dağıtıldıktan sonra, HANA büyük örnekleriniz için dağıtım isteğinde gönderdiğiniz Azure aboneliğinde yeni bir [Azure Kaynak grubu](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) oluşturulur.  Yeni kaynak grubu, belirli bir abonelikte dağıttığınız tüm HANA büyük örnek birimlerinizi listelecektir.

Yeni Azure kaynak grubunu bulmak için, Azure portal sol gezinti bölmesinde gezinerek aboneliğinizdeki kaynak grubunu listeleyin

![Azure portal gezinti bölmesi](./media/hana-li-portal/portal-resource-group.png)

Kaynak grupları listesinde, listede, HANA büyük örneklerinin dağıtılmasından önce kullandığınız abonelikte filtre uygulamanız gerekebilir

![Azure portal kaynak gruplarını filtrele](./media/hana-li-portal/portal-filtering-subscription.png)

Doğru aboneliğe filtreledikten sonra, hala kaynak grupları için uzun bir listeniz olabilir. "Xxx" in **-T050**gibi üç basamak olduğu **-TxXx** sonrası bir tane bulun. 

Kaynak grubunu bulduğunuz için ayrıntılarını listeleyin. Aldığınız liste şöyle görünebilir:

![Azure portal içindeki HLI listesi](./media/hana-li-portal/portal-hli-units-list.png)

Listelenen tüm birimler, aboneliğinizde dağıtılan tek bir HANA büyük örnek birimini temsil eder. Bu durumda, aboneliğinizde dağıtılan sekiz farklı HANA büyük örnek birimine göz atabilirsiniz.

Aynı Azure aboneliği altında birkaç HANA büyük örnek kiracılar dağıttıysanız, birden çok Azure Kaynak grubu bulacaksınız 


## <a name="look-at-attributes-of-single-hli-unit"></a>Tek bir HLı birimin özniteliklerine bakın
HANA büyük örnek birimleri listesinde, tek bir birime tıklayıp tek bir HANA büyük örnek biriminin ayrıntılarına ulaşabilirsiniz. 

Genel bakış ekranında, ' daha fazla göster 'e tıkladıktan sonra birimin bir sunumunu alıyorsunuz, şöyle görünür:

![Bir HLI biriminin genel bakışını göster](./media/hana-li-portal/portal-show-overview.png)

Gösterilen farklı özniteliklere bakarak, bu öznitelikler Azure VM özniteliklerinden çok farklı şekilde görünür. Sol taraftaki başlıkta, kaynak grubu, Azure bölgesi, abonelik adı ve KIMLIğIN yanı sıra eklediğiniz bazı Etiketler gösterilir. Varsayılan olarak, HANA büyük örnek birimlerinin atanmış etiketi yoktur. Üstbilginin sağ tarafında, birimin adı dağıtım tamamlandığında atanan olarak listelenir. İşletim sisteminin yanı sıra IP adresi de gösterilir. VM 'Lerde olduğu gibi, CPU iş parçacığı sayısı ve bellek sayısıyla birlikte HANA büyük örnek birim türü de gösterilir. Farklı HANA büyük örnek birimleri hakkında daha fazla ayrıntı burada gösterilmektedir:

- [HLI için kullanılabilen SKU’lar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA (büyük örnekler) depolama mimarisi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Sağ alt taraftaki ek veriler, HANA büyük örnek damgasının düzeltmesidir. Olası değerler şunlardır:

- Düzeltme 3
- Düzeltme 4

Düzeltme 4 ' te, Azure VM 'Leri ve düzeltme 4 damgaları veya satırları 'nda dağıtılan HANA büyük örnek birimleri arasındaki ağ gecikme süresi ile birlikte sunulan en son mimari büyük örnek geliştirmedir.
Dağıtılan her bir HANA büyük örnek birimi için otomatik olarak oluşturulan Azure yakınlık yerleşimi grubunun adına genel bakış alanının sağ alt köşesinde bulunan başka bir çok önemli bilgi bulunur. SAP uygulama katmanını barındıran Azure VM 'Leri dağıtıldığında bu yakınlık yerleşimi grubuna başvurulması gerekir. HANA büyük örnek birimiyle ilişkili [Azure yakınlık yerleşimi grubunu](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) kullanarak, Azure VM 'Lerinin Hana büyük örnek birimine yakın bir yerde dağıtıldığından emin olun. Aynı Azure veri merkezinde barındırılan HANA büyük örnek birimlerinin SAP uygulama katmanını bulmak için yakınlık yerleşimi gruplarının nasıl kullanılabileceği, [SAP uygulamalarıyla en iyi ağ gecikmesi Için Azure yakınlık yerleştirme gruplarında açıklanacaktır ](sap-proximity-placement-scenarios.md).

Üstbilginin sağ sütunundaki ek bir alan, HANA büyük örnek biriminin güç durumu hakkında bilgi verir.

> [!NOTE]
> Güç durumu, donanım biriminin açık veya kapalı olup olmadığını açıklar. Çalışır duruma sahip olan işletim sistemi hakkında bilgi vermez. Bir HANA büyük örnek birimini yeniden başlattığınızda, birim durumunun başlangıç durumuna geçmeye **başlamak**için değiştiği küçük bir zaman yaşarsınız. **Başlatılmış** durumunda işletim sistemi başlatıldığı veya işletim sisteminin tamamen başlatılmış olduğu anlamına gelir. Sonuç olarak, birim yeniden başlatıldıktan sonra durum **başlatılır**başlatılmaz hemen sonra birimde oturum açmanız beklenmemelidir.
> 

' Daha fazla ' düğmesine basarsanız ek bilgiler gösterilir. Bir ek bilgi, HANA büyük örnek damgasının, ' de dağıtılan birimin düzeltmesini görüntülüyor. HANA büyük örnek damgalarının farklı düzeltmeleri için [Azure 'da SAP HANA nedir (büyük örnekler)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) makalesine bakın

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Tek bir HANA büyük örnek biriminin etkinliklerini denetleme 
HANA büyük örnek birimlerine genel bir bakış vermekten daha fazla, belirli bir birimin etkinliklerini denetleyebilirsiniz. Etkinlik günlüğü şöyle görünebilir:

![Azure portal gezinti bölmesi](./media/hana-li-portal/portal-activity-list.png)

Kaydedilen ana etkinliklerden biri bir birimin yeniden başlatılmalardır. Listelenen veriler, etkinliğin durumunu, etkinliğin tetiklendiği zaman damgasını, etkinliğin tetiklendiği abonelik KIMLIĞINI ve etkinliği tetikleyen Azure kullanıcısını içerir. 

Kaydedilen başka bir etkinlik, Azure meta verilerinde bulunan birimde değişir. Yeniden başlatmanın başlatılmasının yanı sıra, **yazma Hanaınstances**etkinliğini görebilirsiniz. Bu tür bir etkinlik, HANA büyük örnek biriminde hiçbir değişiklik gerçekleştirmez, ancak Azure 'daki birimin meta verilerinde yapılan değişiklikleri belgeyor. Listelenen örnekte bir etiketi ekledik ve sildik (sonraki bölüme bakın).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Bir HANA büyük örnek birimine Azure etiketi ekleme ve silme
Başka bir olasılık da HANA büyük örnek birimine bir [etiket](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) eklemektir. Etiketlerin atanma yöntemi, sanal makinelere etiket atamaya farklı değildir. VM 'Lerde olduğu gibi, Etiketler Azure meta verilerinde mevcuttur ve HANA büyük örnekleri için VM 'Lerle aynı kısıtlamalara sahiptir.

Etiketlerin silinmesi VM 'lerle aynı şekilde çalışmaktadır. Her iki etkinlik de bir etiketi uygulamak ve silmek, belirli bir HANA büyük örnek biriminin etkinlik günlüğünde listelenir.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>HANA büyük örnek biriminin özelliklerini denetleme
Bölüm **özellikleri** , örnekleri size geldiğinde alacağınız önemli bilgileri içerir. Bu, destek durumlarında gerekebilecek veya depolama anlık görüntü yapılandırmasını ayarlarken ihtiyaç duyduğunuz tüm bilgileri alacağınız bir bölümdür. Bu bölümde, örneğinizin çevresindeki verilerin bir koleksiyonu, örneğin Azure ve depolama arka ucu arasındaki bağlantı. Bölümün en üstü şöyle görünür:


![Azure portal içindeki HLI özelliklerinin üst bölümü](./media/hana-li-portal/portal-properties-top.png)

İlk birkaç veri öğesi, genel bakış ekranında zaten gördünüz. Ancak verilerin önemli bir kısmı, ilk dağıtılan birimler kullanıma alındığı için aldığınız ExpressRoute bağlantı hattı KIMLIĞIDIR. Bazı destek durumlarında, bu verileri isteyip istemediğiniz sorulur. Ekran görüntüsünün en altında önemli bir veri girişi gösterilmektedir. Görüntülenen veriler, depolama alanınızı HANA büyük örnek yığınında kiracınızda yalıtarak NFS depolama kafasının IP adresidir . Bu IP adresi, [depolama anlık görüntü yedeklemeleri için yapılandırma dosyasını](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)düzenlediğinizde de gereklidir. 

Özellik bölmesinde aşağı kaydırdığınızda, HANA büyük örnek biriminiz için benzersiz bir kaynak KIMLIĞI veya dağıtıma atanan abonelik KIMLIĞI gibi ek veriler alırsınız.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Azure portal aracılığıyla bir HANA büyük örnek birimini yeniden başlatın
Linux işletim sisteminin yeniden başlatılmasını başlatırken, IŞLETIM sistemi yeniden başlatmayı başarıyla bitiremediği çeşitli durumlar vardı. Yeniden başlatmaya zorlamak için, Microsoft işlemlerinin HANA büyük örnek biriminin güç yeniden başlatılmasını gerçekleştirmesini sağlamak üzere bir hizmet isteği açmanız gerekir. Bir HANA büyük örnek biriminin güç yeniden başlatılmasının işlevselliği artık Azure portal tümleşiktir. HANA büyük örnek biriminin genel bakış bölümünde olduğu gibi, veri bölümünün üstünde yeniden başlatma düğmesini görürsünüz

![Azure portal #1 adımı yeniden başlatın](./media/hana-li-portal/portal-restart-first-step.png)

Yeniden Başlat düğmesine basarken birimi gerçekten yeniden başlatmak isteyip istemediğiniz sorulur. "Evet" düğmesine basarak onaylamanız durumunda birim yeniden başlatılır.

> [!NOTE]
> Yeniden başlatma işleminde, birim durumunun başlangıç durumuna geçmeye **başlamak**için değiştiği küçük bir zaman yaşarsınız. **Başlatılmış** durumunda işletim sistemi başlatıldığı veya işletim sisteminin tamamen başlatılmış olduğu anlamına gelir. Sonuç olarak, birim yeniden başlatıldıktan sonra durum **başlatılır**başlatılmaz hemen sonra birimde oturum açmanız beklenmemelidir.

> [!IMPORTANT]
> HANA büyük örnek biriminizdeki bellek miktarına bağlı olarak, donanımın ve işletim sisteminin yeniden başlatılması ve yeniden başlatılması bir saate kadar sürebilir


## <a name="open-a-support-request-for-hana-large-instances"></a>HANA büyük örnekleri için bir destek isteği açın
HANA büyük örnek birimlerinin Azure portal, özel olarak yalnızca bir HANA büyük örnek birimi için destek istekleri de oluşturabilirsiniz. **Yeni destek isteği** bağlantısını takip ettiğiniz için 

![hizmet isteği adımını başlatma #1 Azure portal](./media/hana-li-portal/portal-initiate-support-request.png)

Bir sonraki ekranda listelenen SAP HANA Büyük Örnekleri hizmetini almak için aşağıda gösterildiği gibi ' tüm hizmetler ' i seçmeniz gerekebilir

![Azure portal tüm hizmetleri seçin](./media/hana-li-portal/portal-create-service-request.png)

Hizmetler listesinde, hizmet **SAP HANA büyük örnek**' i bulabilirsiniz. Bu hizmeti seçerken, belirli sorun türlerini gösterildiği gibi seçebilirsiniz:


![Azure portal sorun sınıfını seçin](./media/hana-li-portal/portal-select-problem-class.png)

Farklı sorun türlerinin her birinde, sorununuzun daha fazla niteleyebileceği bir sorun alt türü seçmeniz önerilir. Alt türü seçtikten sonra, şimdi konuyu adı verebilirsiniz. Seçim süreciyle işiniz bittiğinde, oluşturma işleminin bir sonraki adımına geçebilirsiniz. **Çözümler** bölümünde, Hana büyük örnekleri etrafında, sorununuzun çözümüne yönelik bir işaretçi veren bir belge gösterilir. Önerilen belgelerde sorun için bir çözüm bulamıyorsanız bir sonraki adıma geçebilirsiniz. Bir sonraki adımda, sorunun VM 'Ler mi yoksa HANA büyük örnek birimleri ile mi olduğunu sorulur. Bu bilgiler, Destek isteğini doğru uzmanlarına yönlendirmenize yardımcı olur. 

![Azure portal destek çalışmasının ayrıntıları](./media/hana-li-portal/portal-support-request-details.png)

Soruları cevaplayın ve ek ayrıntılar sağladıkça, Destek isteğini gözden geçirmek ve göndermek için bir sonraki adıma geçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'da SAP HANA (büyük örnekler) izleme](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [HANA tarafından izleme ve sorun giderme](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

