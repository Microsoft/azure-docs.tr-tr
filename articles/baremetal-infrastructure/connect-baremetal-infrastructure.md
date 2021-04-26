---
title: Azure 'da BareMetal altyapı örnekleri bağlama
description: Azure portal veya Azure CLı 'de BareMetal örnekleri belirlemeyi ve bunlarla etkileşime geçme hakkında bilgi edinin.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: e67ede85608f2a33dcc179005f0090ca2ce6a640
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871660"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Azure 'da BareMetal altyapı örnekleri bağlama

Bu makalede, [Azure Portal](https://portal.azure.com/) [baremetal örnekleri](concepts-baremetal-infrastructure-overview.md)nasıl görüntüleyeceği gösterilmektedir. Bu makalede ayrıca, dağıtılan BareMetal altyapı örnekleriniz ile Azure portal neler yapabileceğinizi gösterilmektedir. 
 
## <a name="register-the-resource-provider"></a>Kaynak sağlayıcısını kaydetme
BareMetal örnekleri için bir Azure Kaynak sağlayıcısı, Azure portal örneklerin görünürlüğünü sağlar. Varsayılan olarak, BareMetal örnek dağıtımları için kullandığınız Azure aboneliği *BareMetalInfrastructure* kaynak sağlayıcısını kaydeder. Dağıtılan BareMetal örneklerinizi görmüyorsanız, kaynak sağlayıcısını aboneliğinize kaydetmeniz gerekir. 

Azure portal veya Azure CLı kullanarak BareMetal örnek kaynak sağlayıcısını kaydedebilirsiniz.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Aboneliğinizi Azure portal listeetmeniz ve sonra BareMetal örneklerinizi dağıtmak için kullanılan aboneliğe çift tıklamaları gerekir.
 
1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure portalı menüsünde **Tüm hizmetler**’i seçin.

1. **Tüm hizmetler** kutusunda, **abonelik** girin ve ardından **abonelikler**' i seçin.

1. Abonelik listesinden aboneliği seçin.

1. **Kaynak sağlayıcıları** ' nı seçin ve aramaya **BareMetalInfrastructure** girin. Görüntünün gösterdiği gibi, kaynak sağlayıcının **kayıtlı** olması gerekir.
 
>[!NOTE]
>Kaynak sağlayıcı kayıtlı değilse, **Kaydet**' i seçin.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Kayıtlı BareMetal örneklerinin gösterildiği ekran görüntüsü.":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı kullanmaya başlamak için:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLı aracılığıyla BareMetal örnek dağıtımı için kullandığınız Azure aboneliğinde oturum açın. `BareMetalInfrastructure` [Az Provider Register](/cli/azure/provider#az_provider_register) komutuyla kaynak sağlayıcısını kaydedin:

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

Kullanılabilir tüm sağlayıcıları görmek için [az Provider List](/cli/azure/provider#az_provider_list) komutunu kullanabilirsiniz.

---

Kaynak sağlayıcıları hakkında daha fazla bilgi için bkz. [Azure kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md).  

## <a name="baremetal-instances-in-the-azure-portal"></a>Azure portal BareMetal örnekleri
 
Bir BareMetal örnek dağıtım isteği gönderdiğinizde, BareMetal örneklerine bağlanmakta olduğunuz Azure aboneliğini belirtirsiniz. BareMetal örneklerine karşı çalıştırılan uygulama katmanını dağıtmak için kullandığınız aboneliğin aynısını kullanın.
 
BareMetal örneklerinizin dağıtımı sırasında, dağıtım isteğinde kullandığınız Azure aboneliğinde yeni bir [Azure Kaynak grubu](../azure-resource-manager/management/manage-resources-portal.md) oluşturulur. Bu yeni kaynak grubunda, bu abonelikte dağıttığınız tüm BareMetal örnekleri listelenir.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. BareMetal aboneliğinde, Azure portal **kaynak grupları**' nı seçin.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Kaynak gruplarının listesini gösteren ekran görüntüsü.":::

1. Listede yeni kaynak grubunu bulun.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Filtrelenmiş kaynak grupları listesinde BareMetal örneğini gösteren ekran görüntüsü." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >BareMetal örneğini dağıtmak için kullandığınız abonelikte filtre uygulayabilirsiniz. Uygun aboneliğe filtre uyguladıktan sonra, uzun kaynak grupları listeniz olabilir. - **TxXx** sonrası- **T250** gibi üç basamaklı bir çözüm arayın.

1. Ayrıntılarını görüntülemek için yeni kaynak grubunu seçin. Resimde dağıtılan bir BareMetal örnek gösterilmektedir.
   
   >[!NOTE]
   >Aynı Azure aboneliği altında birkaç Karemetal örnek kiracılar dağıttıysanız, birden çok Azure Kaynak grubu görürsünüz.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Tüm BareMetal örneklerinizi görmek için, kaynak grubunuz için [az baremetalinstance List](/cli/azure/baremetalinstance#az_baremetalinstance_list) komutunu çalıştırın:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> `--output`Parametresi, tüm komutlarda kullanılabilen genel bir parametredir. **Tablo** değeri, çıktıyı kolay bir biçimde sunar. Daha fazla bilgi için bkz. [Azure CLI komutları Için çıkış biçimleri](/cli/azure/format-output-azure-cli).

---

## <a name="view-the-attributes-of-a-single-instance"></a>Tek bir örneğin özniteliklerini görüntüleme

Tek bir örneğin ayrıntılarını görüntüleyebilirsiniz.

### <a name="portal"></a>[Portal](#tab/azure-portal)

BareMetal örnekleri listesinde, görüntülemek istediğiniz tek örneği seçin.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Tek bir örneğin BareMetal örnek özniteliklerini gösteren ekran görüntüsü." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
Görüntüdeki öznitelikler Azure sanal makine (VM) özniteliklerinden çok farklı değildir. Sol tarafta, kaynak grubu, Azure bölgesi ve abonelik adı ile KIMLIĞINI görürsünüz. Etiketler atadıysanız, bunları burada da görürsünüz. Varsayılan olarak, BareMetal örneklerine Etiketler atanmaz.
 
Sağ tarafta, CPU iş parçacığı ve bellek sayısını gösteren BareMetal örneği, işletim sistemi (OS), IP adresi ve SKU 'nun adını görürsünüz. Ayrıca güç durumu ve donanım sürümünü (BareMetal örnek damgasının düzeltmesi) görürsünüz. Güç durumu, donanım biriminin açık veya kapalı olduğunu gösterir. Ancak işletim sistemi ayrıntıları, çalışıp çalışmadığını göstermez.
 
Olası donanım düzeltmeleri şunlardır:

* Düzeltme 3 (Rev 3)

* Düzeltme 4 (Rev 4)
 
* Düzeltme 4,2 (Rev 4,2)
 
>[!NOTE]
>Rev 4,2, var olan Rev 4 mimarisini kullanan en son yeniden markalı BareMetal altyapısıdır. Rev 4, Azure sanal makinesi (VM) konaklarına daha yakın bir yakınlık sağlar. Azure VM 'Ler ve SAP HANA örnekleri arasındaki ağ gecikmesi açısından önemli iyileştirmeler yapılmıştır. BareMetal örneklerinizi Azure portal aracılığıyla erişebilir ve yönetebilirsiniz. Daha fazla bilgi için bkz. [Azure 'Da Baremetal altyapısı](concepts-baremetal-infrastructure-overview.md).

 
Ayrıca, sağ tarafta, dağıtılan her BareMetal örnek için otomatik olarak oluşturulan [Azure yakınlık yerleşimi grubunun](../virtual-machines/co-location.md) adını bulacaksınız. Uygulama katmanını barındıran Azure VM 'lerini dağıtırken yakınlık yerleşimi grubuna başvurun. BareMetal örneğiyle ilişkili yakınlık yerleşimi grubunu kullandığınızda Azure VM 'lerinin BareMetal örneğine yakın bir şekilde dağıtılmasını sağlayabilirsiniz.
 
>[!TIP]
>Uygulama katmanını düzeltme 4. x ile aynı Azure veri merkezinde bulmak için bkz. [en iyi ağ gecikmesi Için Azure yakınlık yerleşimi grupları](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bir BareMetal örneğinin ayrıntılarını görmek için [az baremetalinstance Show](/cli/azure/baremetalinstance#az_baremetalinstance_show) komutunu çalıştırın:

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Örnek adından emin değilseniz, `az baremetalinstance list` yukarıda açıklanan komutu çalıştırın.

---
 
## <a name="check-activities-of-a-single-instance"></a>Tek bir örneğin etkinliklerini denetleme
 
Tek bir BareMetal örneğinin etkinliklerini kontrol edebilirsiniz. Kaydedilen ana etkinliklerden biri, örneğin yeniden başlatılır. Listelenen veriler etkinliğin durumunu, tetiklenen etkinlik zaman damgasını, abonelik KIMLIĞINI ve etkinliği tetikleyen Azure kullanıcısını içerir.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="BareMetal örnek etkinliklerini gösteren ekran görüntüsü." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Azure 'daki örnek meta verilerinde yapılan değişiklikler, etkinlik günlüğüne de kaydedilir. Yeniden başlatmanın başlatıldığı yanında, **Write BareMetallnstances** etkinliğini görebilirsiniz. Bu etkinlik BareMetal örneği üzerinde hiçbir değişiklik yapmaz, ancak Azure 'daki birimin meta verilerinde yapılan değişiklikleri belgeler.
 
Kaydedilen başka bir etkinlik, bir örneğe etiket eklediğinizde veya bir [etiketi](../azure-resource-manager/management/tag-resources.md) sildiğinizde.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Azure etiketini bir örneğe ekleme ve silme

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Bir BareMetal örneğine Azure etiketleri ekleyebilir veya bunları silebilirsiniz. Etiketler, VM 'lere etiket atarken olduğu gibi atanır. VM 'Lerde olduğu gibi, Etiketler Azure meta verilerinde bulunur. Etiketler, sanal makinelerin bulunduğu BareMetal örnekleri için aynı kısıtlamalara sahiptir.
 
Etiketlerin silinmesi VM 'Lerle aynı şekilde de aynı şekilde çalışacaktır. Bir etiketi uygulamak ve silmek BareMetal örneğinin etkinlik günlüğünde listelenir.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal örneklerine etiket atama, sanal makineler için etiket atama ile aynı şekilde çalışacaktır. VM 'Lerde olduğu gibi, Etiketler Azure meta verilerinde bulunur. Etiketler, sanal makinelerin bulunduğu BareMetal örnekleri için aynı kısıtlamalara sahiptir.

Bir BareMetal örneğine etiket eklemek için [az baremetalinstance Update](/cli/azure/baremetalinstance#az_baremetalinstance_update) komutunu çalıştırın:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Bir etiketi kaldırmak için aynı komutu kullanın:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Bir örneğin özelliklerini denetleme
 
Örnekleri aldığınızda, örneklerle ilgili toplanan verileri görüntülemek için Özellikler bölümüne gidebilirsiniz. Toplanan veriler Azure bağlantısı, depolama arka ucu, ExpressRoute bağlantı hattı KIMLIĞI, benzersiz kaynak KIMLIĞI ve abonelik KIMLIĞINI içerir. Bu bilgileri destek isteklerinde veya depolama anlık görüntü yapılandırmasını ayarlarken kullanacaksınız.
 
Göreceğiniz diğer önemli bilgi, depolama NFS IP adresidir. BareMetal örnek yığınında depolama alanınızı, **kiracınıza** ayırır. [Depolama anlık görüntüsü yedeklemeleri için yapılandırma dosyasını](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)DÜZENLERKEN bu IP adresini kullanacaksınız.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="BareMetal örnek özelliği ayarlarını gösteren ekran görüntüsü." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Azure portal aracılığıyla bir BareMetal örneğini yeniden başlatma

İşletim sisteminin, BareMetal örneğinin güç yeniden başlatılmasını gerektiren bir yeniden başlatmayı tamamlayamayabileceği çeşitli durumlar vardır.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Örneğin Azure portal doğrudan yeniden başlatmasını yapabilirsiniz:
 
Yeniden **Başlat ' ı** ve ardından **Evet** ' i seçerek yeniden başlatmayı onaylayın.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="BareMetal örneğinin nasıl yeniden başlatılmasını gösteren ekran görüntüsü.":::
 
Bir BareMetal örneğini yeniden başlattığınızda bir gecikme yaşarsınız. Bu gecikme sırasında, güç durumu **Başlangıç** **durumundan başlangıç durumuna taşınıyor, bu** da işletim sisteminin tamamen başlatıldığı anlamına gelir. Sonuç olarak, yeniden başlatmadan sonra yalnızca durum ' a geçiş yapıldıktan sonra birimde oturum **açabilirsiniz.**

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bir BareMetal örneğini yeniden başlatmak için [az baremetalinstance restart](/cli/azure/baremetalinstance#az_baremetalinstance_restart) komutunu kullanın:

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>BareMetal örneğinizin bellek miktarına bağlı olarak, bir yeniden başlatma ve donanımın ve işletim sisteminin yeniden başlatılması bir saate kadar sürebilir.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>BareMetal örnekleri için bir destek isteği açın
 
Özel olarak BareMetal örnekleri için destek istekleri gönderebilirsiniz.
1. Azure portal, **Yardım + Destek** altında, **[Yeni bir destek isteği](https://rc.portal.azure.com/#create/Microsoft.Support)** oluşturun ve bilet için aşağıdaki bilgileri sağlayın:
 
   - **Sorun türü:** Bir sorun türü seçin.
 
   - **Abonelik:** Aboneliğinizi seçin.
 
   - **Hizmet:** BareMetal altyapısı
 
   - **Kaynak:** Örneğin adını belirtin.
 
   - **Özet:** İsteğiniz için bir Özet sağlayın.
 
   - **Sorun türü:** Bir sorun türü seçin.
 
   - **Sorun alt türü:** Sorun için bir alt tür seçin.

1. Sorununuza yönelik bir çözüm bulmak için **çözümler** sekmesini seçin. Bir çözüm bulamıyorsanız bir sonraki adıma gidin.

1. **Ayrıntılar** sekmesini seçin ve sorunun VM 'ler mi yoksa BareMetal örneklerle mi olduğunu seçin. Bu bilgiler, destek talebinin doğru uzmanlarına yönlendirilmesine yardımcı olur.

1. Sorunun başladığını belirtin ve örnek bölgesini seçin.

1. İstek hakkında daha fazla ayrıntı sağlayın ve gerekirse bir dosyayı karşıya yükleyin.

1. İsteği göndermek için **gözden geçir + oluştur** ' u seçin.
 
Bir destek temsilcisinin isteğinizi onaylamasını beş adede kadar iş günü sürer.

## <a name="next-steps"></a>Sonraki adımlar

İş yükleri hakkında daha fazla bilgi edinin:

- [Azure 'da SAP HANA nedir (büyük örnekler)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
