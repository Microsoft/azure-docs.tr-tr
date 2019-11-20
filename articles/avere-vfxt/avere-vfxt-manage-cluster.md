---
title: Avere vFXT kümesini yönetme-Azure
description: Avere kümesini yönetme-düğüm ekleme veya kaldırma, vFXT kümesini yeniden başlatma, durdurma veya yok etme
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: rohogue
ms.openlocfilehash: bcdba7f14147714c5e29c13bfe9e20fa44a27ef9
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256195"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Avere vFXT kümesini yönetme

Kümeyi oluşturduktan sonra küme düğümleri eklemeniz veya kümeyi durdurup yeniden başlatmanız gerekebilir. Ve projeniz tamamlandığında kümeyi kalıcı olarak durdurmayı ve kaldırmayı bilmeniz gerekir. 

Küme yönetim görevine bağlı olarak, avere Denetim Masası 'nı, vfxt.py komut satırı kümesi oluşturma betiğini veya bunu yapmak için Azure portal kullanmanız gerekebilir. 

Bu tablo, her görev için hangi araçların kullanılabileceğini bir genel bakış sunar. 

| Eylem | Avere Denetim Masası | vfxt.py  | Azure portal |
| --- | --- | --- | --- |
| Küme düğümleri Ekle | eşleşen | Yes | eşleşen |
| Küme düğümlerini kaldır | Yes | eşleşen | eşleşen |
| Küme düğümünü durdur | Evet (hizmetleri veya yeniden başlatmayı da yeniden başlatabilir) | eşleşen | bir düğüm sanal makinesinin portaldan kapatılması düğüm hatası olarak yorumlanır |
| Durdurulmuş bir düğüm Başlat | eşleşen | eşleşen | Yes |
| Tek bir küme düğümünü yok etme | eşleşen | eşleşen | Yes |
| Kümeyi yeniden başlatın |  |  |  |
| Kümeyi güvenli bir şekilde kapatma veya durdurma | Yes | Yes | eşleşen |
| Kümeyi yok etme  | eşleşen | Yes | Evet, ancak veri bütünlüğü garanti edilmez |

Her araçla ilgili ayrıntılı yönergeler aşağıda verilmiştir.

## <a name="about-stopped-instances-in-azure"></a>Azure 'da Durdurulan örnekler hakkında

Herhangi bir Azure VM 'yi kapattığınızda veya durdurduğunuzda, işlem ücretleri durduruluyor, ancak yine de depolama alanı için ödeme yapmanız gerekir. Bir vFXT düğümünü veya vFXT kümesini kapatırsanız ve yeniden başlatmayı düşünmüyorsanız ilgili VM 'Leri silmek için Azure portal kullanmanız gerekir. 

Azure portal, *durdurulmuş* bir düğüm (yeniden başlatılabilir) Azure Portal **durdurulan** durumu gösterir; *silinen* düğüm, durumu **durduruldu (serbest bırakıldı)** durumunu gösterir ve artık işlem veya depolama ücretleri doğurur.

VM 'yi silmeden önce, kümeyi durdurmak veya kapatmak için avere Denetim Masası veya vfxt.py seçenekleri kullanılarak, değiştirilen tüm verilerin önbellekten arka uç depolamaya yazıldığından emin olun.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Avere denetim masası ile kümeyi yönetme 

Avere Denetim Masası bu görevler için kullanılabilir: 

* Tek tek düğümleri Durdur veya yeniden Başlat
* Kümeden düğüm kaldırma
* Tüm kümeyi Durdur veya yeniden Başlat

Avere denetim masası, veri bütünlüğünü önceliklendirir, bu nedenle, olası bir verileri bir bozucu işlemden önce arka uç depolamaya yazmaya çalışır. Bu, avere Portal 'dan daha güvenli bir seçenek sunar. 

Web tarayıcısından avere Denetim Masası 'Na erişin. Yardım gerekirse [, vFXT kümesine erişme](avere-vfxt-cluster-gui.md) bölümündeki yönergeleri izleyin.

### <a name="manage-nodes-with-avere-control-panel"></a>Avere denetim masası ile düğümleri yönetme

**FXT düğümleri** ayarlar sayfasında, tek tek düğümleri yönetmeye yönelik denetimler bulunur.

Bir düğümü kapatmak, yeniden başlatmak veya kaldırmak için, **FXT düğümleri** sayfasındaki listede bulunan düğümü bulun ve **Eylemler** sütununda ilgili düğmeye tıklayın.

> [!NOTE] 
> Etkin düğümlerin sayısı değiştiğinde, IP adresleri küme düğümleri arasında hareket edebilir.

Daha fazla bilgi için avere Cluster Settings kılavuzundaki [küme > FXT düğümlerini](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) okuyun.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Avere denetim masası ile kümeyi durdurma veya yeniden başlatma

**Sistem bakım** ayarları sayfasında, küme hizmetlerini yeniden başlatmak, kümeyi yeniden başlatmak veya kümeyi güvenle kapatmak için komutlar bulunur. Ayrıntılar için [yönetim > sistem bakımı](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) 'Nı (avere küme ayarları kılavuzunda) okuyun.

Bir küme kapatılırken, ilk olarak durum iletilerini **Pano** sekmesine nakleder. Birkaç dakika sonra, avere Denetim Masası oturumu yanıt vermeyi durdurur, bu da kümenin kapatıldığı anlamına gelir.

## <a name="manage-the-cluster-with-vfxtpy"></a>Vfxt.py ile kümeyi yönetme

vfxt.py, küme oluşturma ve yönetimi için bir komut satırı aracıdır. 

vfxt.py, küme denetleyicisi sanal makinesine önceden yüklenir. Başka bir sisteme yüklemek isterseniz, <https://github.com/Azure/AvereSDK> ' a bakın.

Vfxt.py betiği şu küme yönetim görevleri için kullanılabilir:

* Kümeye yeni düğümler ekleme
* Kümeyi durdurma veya başlatma  
* Kümeyi yok etme

Avere denetim masası gibi vfxt.py işlemler, küme veya düğümü kapatmadan veya yok etmeden önce değiştirilen verilerin arka uç depolamada kalıcı olarak depolandığından emin olmanızı dener. Bu, avere Portal 'dan daha güvenli bir seçenek sunar.

GitHub 'da tamamı vfxt.py kullanım kılavuzu mevcuttur: [vfxt.py Ile bulut kümesi Yönetimi](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Vfxt.py ile küme düğümleri ekleme

Küme düğümü eklemek için örnek bir komut betiği, küme denetleyicisine dahildir. Denetleyicide ``./add-nodes`` ' i bulun ve küme bilgileriniz ile özelleştirmek için bir düzenleyicide açın. 

Bu komutu kullanmak için kümenin çalışıyor olması gerekir. 

Aşağıdaki değerleri sağlayın: 

* Küme için kaynak grubu adı ve ayrıca, kümeyle aynı olmayan ağ ve depolama kaynakları için
* Küme konumu
* Küme ağı ve alt ağı 
* Küme düğümü erişim rolü (yerleşik rol [avere işlecini](../role-based-access-control/built-in-roles.md#avere-operator)kullanın)
* Küme yönetimi IP adresi ve yönetici parolası 
* Eklenecek düğüm sayısı (1, 2 veya 3)
* Düğüm örneği türü ve önbellek boyutu değerleri 

Prototip kullanmıyorsanız, yukarıda açıklanan bilgiler dahil olmak üzere aşağıdaki gibi bir komut oluşturmanız gerekir. 

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \ 
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Daha fazla bilgi için vfxt.py kullanım kılavuzunda [bir kümeye düğüm ekleme](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) makalesini okuyun.

### <a name="stop-a-cluster-with-vfxtpy"></a>Vfxt.py ile küme durdurma

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Vfxt.py ile durdurulmuş bir küme başlatma

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```    

Küme durdurulduğu için, küme düğümlerini belirtmek üzere örnek tanımlayıcıları geçirmeniz gerekir. Daha fazla bilgi için vfxt.py kullanım kılavuzunda [Değiştirilecek kümeyi belirtmeyi](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) okuyun.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Vfxt.py ile küme yok etme

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

@No__t-0 seçeneği, küme önbelleğinden değiştirilen verileri yazmak istemediğiniz durumlarda kullanılabilir.

Ek bilgi için [vfxt.py kullanım kılavuzunu](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) okuyun.  

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Azure portal küme VM 'lerini yönetme 

Azure portal küme VM 'lerini tek tek yok etmek için kullanılabilir, ancak küme önce düzgün kapatılmadığında veri bütünlüğü garanti edilmez. 

Azure portal, bu küme yönetim görevleri için kullanılabilir: 

* Durdurulmuş bir vFXT düğümü başlatma
* Tek bir vFXT düğümünü Durdur (küme bunu düğüm hatası olarak Yorumlar)
* Küme önbelleğindeki değiştirilen verilerin çekirdek filklerine yazıldığından *emin olmanız gerekmiyorsa* bir vFXT kümesini yok edin
* Güvenli bir şekilde kapatıldıktan sonra vFXT düğümlerini ve diğer küme kaynaklarını kalıcı olarak kaldır

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Azure portal vFXT örneklerini yeniden başlatın

Durdurulmuş bir düğümü yeniden başlatmanız gerekiyorsa Azure portal kullanmanız gerekir. Sol taraftaki menüden **sanal makineler** ' i seçin ve sonra genel bakış sayfasını açmak IÇIN listedeki VM adına tıklayın.

VM 'yi yeniden etkinleştirmek için genel bakış sayfasının en üstündeki **Başlat** düğmesine tıklayın.

![Durdurulmuş bir VM başlatma seçeneğini gösteren Azure portal ekran](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Küme düğümlerini silme

VFXT kümesinden bir düğümü silmek, ancak kümenin geri kalanını tutmak istiyorsanız, önce avere denetim masası ile [düğümü kümeden kaldırmanız](#manage-nodes-with-avere-control-panel) gerekir.

> [!CAUTION]
> Bir düğümü önce vFXT kümesinden kaldırmadan silerseniz, veriler kaybolabilir.

VFXT düğümü olarak kullanılan bir veya daha fazla örneği kalıcı olarak yok etmek için Azure portal kullanın.
Sol taraftaki menüden **sanal makineler** ' i seçin ve sonra genel bakış sayfasını açmak IÇIN listedeki VM adına tıklayın.

VM 'yi kalıcı olarak yok etmek için genel bakış sayfasının en üstündeki **Sil** düğmesine tıklayın.

Bu yöntemi, güvenli bir şekilde kapatıldıktan sonra küme düğümlerini kalıcı olarak kaldırmak için kullanabilirsiniz. 

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Azure portal kümeyi yok edin

> [!NOTE] 
> Önbellekte kalan tüm istemci değişikliklerinin arka uç depolamaya yazılmasını istiyorsanız, Azure portal düğüm örneklerini kaldırmadan önce vfxt.py `--destroy` seçeneğini kullanın veya avere Denetim Masası 'nı kullanarak kümeyi düzgün bir şekilde kapatın.

Düğüm örneklerini Azure portal silerek kalıcı olarak yok edebilirsiniz. Yukarıda açıklanan şekilde tek seferde silebilir veya **sanal makineler** sayfasını kullanarak küme VM 'lerinin tümünü bulabilir, bunları onay kutuları ile seçebilir ve tümünü tek bir eylemde kaldırmak için **Sil** düğmesine tıklayabilirsiniz.

![Portalda "Cluster" terimiyle filtrelenen ve dört işaretli ve vurgulanmış olan VM 'lerin listesi](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Azure portal ek küme kaynaklarını silme

Özellikle vFXT kümesi için ek kaynaklar oluşturduysanız, kümeyi aşağı doğru bir şekilde kaldırmak isteyebilirsiniz. İhtiyaç duyduğunuz verileri veya diğer projelerle paylaşılan herhangi bir öğeyi içeren öğeleri yok etme.

Küme düğümlerini silmenin yanı sıra, şu bileşenleri kaldırmayı göz önünde bulundurun: 

* Küme denetleyicisi VM 'si
* Küme düğümleriyle ilişkili veri diskleri
* Küme bileşenleriyle ilişkili ağ arabirimleri ve genel IP 'Ler
* Sanal ağlar
* Depolama hesapları (**yalnızca** önemli veriler içeriyorlarsa)
* Kullanılabilirlik kümesi 

![Bir test kümesi için oluşturulan kaynakları gösteren "tüm kaynaklar" listesi Azure portal](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Azure portal bir kümenin kaynak grubunu silin

Kümeyi barındırmak için özel olarak bir kaynak grubu oluşturduysanız, kaynak grubunu yok ederek küme için tüm ilgili kaynakları yok edebilirsiniz. 

> [!Caution] 
> Yalnızca grupta hiçbir değerin olmadığından eminseniz kaynak grubunu yok edin. Örneğin, kaynak grubu içindeki herhangi bir depolama kapsayıcılarından gerekli verileri taşıdığınızdan emin olun.  

Bir kaynak grubunu silmek için portalın sol menüsünde **kaynak grupları** ' na tıklayın ve ardından vFXT kümesi için oluşturduğunuz kaynağı bulmak için kaynak grupları listesini filtreleyin. Kaynak grubunu seçin ve panelin sağ tarafındaki üç noktaya tıklayın. **Kaynak grubunu sil**' i seçin. Portal, geri alınamaz olan silme işlemini onaylamanızı ister.  

!["Kaynak grubunu sil" eylemini gösteren kaynak grubu](media/avere-vfxt-delete-resource-group.png)
