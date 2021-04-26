---
title: Azure Kubernetes Service (AKS) düğüm havuzları (Önizleme) için düğüm yapılandırmasını özelleştirme
description: Azure Kubernetes Service (AKS) küme düğümlerinde ve düğüm havuzlarında yapılandırmayı özelleştirmeyi öğrenin.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c2173118b58ca92d69286fb36014872c19058bd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779984"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Azure Kubernetes Service (AKS) düğüm havuzları (Önizleme) için düğüm yapılandırmasını özelleştirme

Düğüm yapılandırmanızı özelleştirmek, işletim sistemi (OS) ayarlarınızı veya kubelet parametrelerini iş yüklerinin gereksinimleriyle eşleşecek şekilde yapılandırmanıza veya ayarlamanıza olanak sağlar. Bir AKS kümesi oluşturduğunuzda veya kümenize bir düğüm havuzu eklediğinizde, yaygın olarak kullanılan bir işletim sistemi ve kubelet ayarları alt kümesini özelleştirebilirsiniz. Ayarları bu alt kümenin ötesinde yapılandırmak için, [düğümleriniz için AKS desteğini kaybetmeden gerekli yapılandırmalarınızı özelleştirmek üzere bir Daemon kümesi kullanın](support-policies.md#shared-responsibility).

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>`CustomNodeConfigPreview`Önizleme özelliğini kaydetme

Kubelet parametrelerini veya işletim sistemi ayarlarını özelleştirebileceği bir AKS kümesi veya düğüm havuzu oluşturmak için, `CustomNodeConfigPreview` aboneliğinizde Özellik bayrağını etkinleştirmeniz gerekir.

`CustomNodeConfigPreview`Aşağıdaki örnekte gösterildiği gibi, [az Feature Register][az-feature-register] komutunu kullanarak özellik bayrağını kaydedin:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Durumun *kayıtlı* gösterilmesi birkaç dakika sürer. [Az Feature List][az-feature-list] komutunu kullanarak kayıt durumunu doğrulayın:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Hazırlandığınızda, [az Provider Register][az-provider-register] komutunu kullanarak *Microsoft. Containerservice* kaynak sağlayıcısı kaydını yenileyin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>aks-preview CLI uzantısını yükleme

Kubelet parametrelerini veya işletim sistemi ayarlarını özelleştirebilecek bir AKS kümesi veya düğüm havuzu oluşturmak için en son *aks-önizleme* Azure CLI uzantısının olması gerekir. [Az Extension Add][az-extension-add] komutunu kullanarak *aks-Preview* Azure CLI uzantısını yükler. Veya [az Extension Update][az-extension-update] komutunu kullanarak kullanılabilir tüm güncelleştirmeleri yükler.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Özel düğüm yapılandırması kullan

### <a name="kubelet-custom-configuration"></a>Kubelet özel yapılandırması

Desteklenen Kubelet parametreleri ve kabul edilen değerler aşağıda listelenmiştir.

| Parametre | İzin verilen değerler/Aralık | Varsayılan | Description |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | hiçbiri, statik | yok | Statik ilke, tamsayı CPU 'SU olan [garantili](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) düğüm kapsayıcılarındaki kapsayıcılara, düğümdeki özel CPU 'lara erişim isteklerine izin verir. |
| `cpuCfsQuota` | doğru, yanlış | true |  CPU sınırlarını belirten kapsayıcılar için CPU CFS kota zorlamayı etkinleştirin/devre dışı bırakın. | 
| `cpuCfsQuotaPeriod` | Milisaniye cinsinden Aralık (MS) | `100ms` | CPU CFS kota dönemi değerini ayarlar. | 
| `imageGcHighThreshold` | 0-100 | 85 | Görüntü çöp toplamanın her zaman çalıştırılacağı disk kullanımının yüzdesi. Çöp **toplamayı tetikleyecek en** az disk kullanımı. Görüntü atık toplamayı devre dışı bırakmak için 100 olarak ayarlayın. | 
| `imageGcLowThreshold` | 0-100, şundan yüksek değil `imageGcHighThreshold` | 80 | Görüntü çöp toplamanın hiçbir şekilde çalıştırılmayacağı disk kullanımının yüzdesi. Çöp toplamayı **tetikleyesağlayan** minimum disk kullanımı. |
| `topologyManagerPolicy` | hiçbiri, en iyi çaba, sınırlı, tek NUMA düğümü | yok | NUMA düğümü hizalamasını iyileştirin, daha fazla bilgi için [buraya](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/)bakın. Yalnızca Kubernetes v 1.18 +. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Yok | Güvenli olmayan Sysctls veya güvenli olmayan sysctl desenlerinin izin verilen listesi. | 

### <a name="linux-os-custom-configuration"></a>Linux işletim sistemi özel yapılandırması

Desteklenen işletim sistemi ayarları ve kabul edilen değerler aşağıda listelenmiştir.

#### <a name="file-handle-limits"></a>Dosya tanıtıcı sınırları

Çok sayıda trafiğe hizmet verirken, hizmet vermekte olduğunuz trafiğin çok sayıda yerel dosyadan geldiği yaygındır. Daha fazlasını işleyebilmeniz için aşağıdaki çekirdek ayarlarını ve yerleşik sınırları ince ayar, bazı sistem belleğinin maliyetinden daha fazla bilgi alabilirsiniz.

| Ayar | İzin verilen değerler/Aralık | Varsayılan | Description |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192-12000500 | 709620 | Linux çekirdeğinin ayırabilecek en fazla dosya tanıtıcısı sayısı, bu değeri artırarak izin verilen en fazla açık dosya sayısını artırabilirsiniz. |
| `fs.inotify.max_user_watches` | 781250-2097152 | 1048576 | Sistem tarafından izin verilen en fazla dosya sayısı. Her bir *izleme* , 32 bit çekirdekte yaklaşık 90 bayt ve 64-bit çekirdekte yaklaşık 160 bayttır. | 
| `fs.aio-max-nr` | 65536-6553500 | 65536 | Aio-NR, geçerli sistem genelinde zaman uyumsuz GÇ isteklerinin sayısını gösterir. aio-Max-NR, aio-NR ' ın büyüyebileceği maksimum değeri değiştirmenize izin verir. |
| `fs.nr_open` | 8192-20000500 | 1048576 | Bir işlemin ayırabilecek en fazla dosya işleyici sayısı. |


#### <a name="socket-and-network-tuning"></a>Yuva ve ağ ayarlama

Çok büyük sayıda eşzamanlı oturumu işlemesi beklenen aracı düğümleri için, düğüm havuzu başına ince ayar kullanabileceğiniz aşağıdaki TCP ve ağ seçeneklerinin alt kümesini kullanabilirsiniz. 

| Ayar | İzin verilen değerler/Aralık | Varsayılan | Description |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096-3240000 | 16384 | Belirli bir dinleme yuvası için sıraya alınabilen en fazla bağlantı isteği sayısı. [Listen (2)](http://man7.org/linux/man-pages/man2/listen.2.html) işlevine geçilen kapsam parametresinin değeri için üst sınır. Kapsam bağımsız değişkeni öğesinden büyükse `somaxconn` , bu sınıra sessizce kesilir.
| `net.core.netdev_max_backlog` | 1000-3240000 | 1000 | Arabirim paketleri çekirdekten daha hızlı aldığında, GIRIŞ tarafında sıraya alınan en fazla paket sayısı. |
| `net.core.rmem_max` | 212992-134217728 | 212992 | Bayt cinsinden en fazla alma yuvası arabelleği boyutu. |
| `net.core.wmem_max` | 212992-134217728 | 212992 | Bayt cinsinden en büyük gönderme yuvası arabelleği boyutu. | 
| `net.core.optmem_max` | 20480-4194304 | 20480 | Yuva başına izin verilen en fazla ek arabellek boyutu (seçenek bellek arabelleği). Yuva seçeneği belleği, yuvanın kullanımıyla ilgili ek yapıları depolamak için birkaç durumda kullanılır. | 
| `net.ipv4.tcp_max_syn_backlog` | 128-3240000 | 16384 | Bağlantı istemcisinden hala bildirim almamış olan sıraya alınan bağlantı isteği sayısı üst sınırı. Bu sayı aşılırsa, çekirdek istekleri bırakmaya başlayacaktır. |
| `net.ipv4.tcp_max_tw_buckets` | 8000-1440000 | 32768 | `timewait`Sistem tarafından aynı anda tutulan yuvaların sayısı. Bu sayı aşılırsa, zaman bekleme yuvası hemen yok edilir ve uyarı yazdırılır. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | Yalnız bırakılmış (herhangi bir uygulama tarafından başvurulmayan) bağlantının süresi, yerel uçta durdurulmadan önce FIN_WAIT_2 durumunda kalır. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Etkinleştirildiğinde TCP iletileri kaç sıklıkta gönderir `keepalive` `keepalive` . |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | TCP 'nin kaç araştırma `keepalive` gönderdiği, bağlantının kopuk olduğuna karar vermez. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Yoklamaların gönderilme sıklığı. Bu sayı `tcp_keepalive_probes` , yoklama başladıktan sonra yanıt vermeyen bir bağlantıyı sonlandırmaya yönelik süreyi oluşturur. | 
| `net.ipv4.tcp_tw_reuse` | 0 veya 1 | 0 | `TIME-WAIT`Protokol görüş açısından güvenli hale geldiğinde yeni bağlantılar için yuvaları yeniden kullanılmasına izin verin. | 
| `net.ipv4.ip_local_port_range` | İlk: 1024-60999 ve son: 32768-65000] | İlk: 32768 ve son: 60999 | TCP ve UDP trafiği tarafından yerel bağlantı noktasını seçmek için kullanılan yerel bağlantı noktası aralığı. İki sayıdan oluşan: ilk sayı, aracı düğümündeki TCP ve UDP trafiği için izin verilen ilk yerel bağlantı noktasıdır, ikincisi ise son yerel bağlantı noktasıdır. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128-80000 | 4096 | ARP önbelleğinde olabilecek en az girdi sayısı. Girdi sayısı bu ayarın altındaysa çöp toplama tetiklenemez. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512-90000 | 8192 | ARP önbelleğinde olabilecek en fazla sayıda giriş. Bu ayar, ARP atık toplama, bu en yüksek düzeyde ulaştıktan sonra yaklaşık 5 saniye tetiklendiği için en önemli öneme sahiptir. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024-100000 | 16384 | ARP önbelleğindeki en yüksek girdi sayısı. |
| `net.netfilter.nf_conntrack_max` | 131072-589824 | 131072 | `nf_conntrack` , Linux içinde NAT için bağlantı girişlerini izleyen bir modüldür. `nf_conntrack`Modül, TCP protokolünün *kurulan bağlantı* kaydını kaydetmek için bir karma tablo kullanır. `nf_conntrack_max` , karma tablodaki en fazla düğüm sayısı, diğer bir deyişle, modül tarafından desteklenen en fazla bağlantı sayısı `nf_conntrack` veya bağlantı izleme tablosu boyutu. | 
| `net.netfilter.nf_conntrack_buckets` | 65536-147456 | 65536 | `nf_conntrack` , Linux içinde NAT için bağlantı girişlerini izleyen bir modüldür. `nf_conntrack`Modül, TCP protokolünün *kurulan bağlantı* kaydını kaydetmek için bir karma tablo kullanır. `nf_conntrack_buckets` , Karma tablosunun boyutudur. | 

#### <a name="worker-limits"></a>Çalışan sınırları

Dosya tanımlayıcısı sınırları gibi, bir işlemin oluşturabileceğiniz çalışan veya iş parçacıklarının sayısı hem çekirdek ayarı hem de Kullanıcı limitleriyle sınırlıdır. AKS üzerinde kullanıcı sınırı sınırsızdır. 

| Ayar | İzin verilen değerler/Aralık | Varsayılan | Description |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | Süreçler çalışan iş parçacıklarını açabilir. Oluşturulabilen tüm iş parçacıklarının en fazla sayısı çekirdek ayarıyla ayarlanır `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Sanal bellek

Aşağıdaki ayarlar, Linux çekirdeğinin sanal bellek (VM) alt sisteminin ve kirli verilerin diskteki çalışmasını ayarlamak için kullanılabilir `writeout` .

| Ayar | İzin verilen değerler/Aralık | Varsayılan | Description |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530-262144 | 65530 | Bu dosya, bir işlemin sahip olabileceği maksimum bellek eşleme alanı sayısını içerir. Bellek eşleme alanı, `malloc` doğrudan `mmap` ,, `mprotect` ve `madvise` , ve ayrıca paylaşılan kitaplıkları yüklerken çağırmanın yan etkisi olarak kullanılır. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Bu yüzde değeri, dizinin ve ıncode nesnelerinin önbelleğe alınması için kullanılan belleği geri kazanmak için çekirdeğin kullanımını denetler. |
| `vm.swappiness` | 0 - 100 | 60 | Bu denetim, çekirdeğin bellek sayfalarını nasıl takacağınızı tanımlamak için kullanılır. Daha yüksek değerler kararlılığı arttıracaktır, daha düşük değerler değiştirme miktarını azaltır. 0 değeri, çekirdekte ve dosya yedekli sayfaların miktarı bir bölgedeki yüksek su işaretinden küçük olana kadar, çekirdeğe değiştirmeyi başlatmamasını sağlar. | 
| `swapFileSizeMB` | 1 MB- [geçici diskin](../virtual-machines/managed-disks-overview.md#temporary-disk) boyutu (/dev/sdb) | Yok | SwapFileSizeMB, bu düğüm havuzundan aracı düğümlerinde bir takas dosyası boyutunun MB olarak oluşturulmasını belirtir. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Saydam kubpages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) , işlemcinin bellek eşleme donanımınızın daha verimli bir şekilde kullanılmasını sağlayarak performansı artırmak için tasarlanan bir Linux çekirdek özelliğidir. Etkinleştirildiğinde, çekirdek, mümkün olduğunda ayırmayı dener `hugepages` ve herhangi bir Linux işlemi, `mmap` bölge 2 MB 'lik bir şekilde HIZALANMıŞSA 2 MB sayfa alır. Bazı durumlarda `hugepages` , sistem genelinde etkin olduğunda, uygulamalar daha fazla bellek kaynağı ayırmayı sonlandırabilir. Bir uygulama `mmap` büyük bir bölgedir ancak yalnızca 1 bayta dokunabilir, bu durumda herhangi bir nedenden dolayı 4k sayfası yerine 2 MB 'lik bir sayfa ayrılabilir. Bu senaryo, `hugepages` sistem genelinde veya yalnızca bölgeler içinde olacak şekilde devre dışı bırakmak mümkün değildir `MADV_HUGEPAGE madvise` . | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Bu değer, daha fazla kullanılabilir hale getirmek için çekirdeğin bellek sıkıştırmasını açık bir şekilde kullanmasını sağlayıp yapmayacağını denetler `hugepages` . | 

> [!IMPORTANT]
> Arama ve okunabilirlik kolaylığı için, işletim sistemi ayarları bu belgede adlarıyla görüntülenir, ancak yapılandırma JSON dosyasına veya [camelCase aktifleştirme kuralını](/dotnet/standard/design-guidelines/capitalization-conventions)kullanarak aks API 'sine eklenmelidir.

`kubeletconfig.json`Aşağıdaki içeriğe sahip bir dosya oluşturun:

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
`linuxosconfig.json`Aşağıdaki içeriğe sahip bir dosya oluşturun:

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

Önceki adımda oluşturulan JSON dosyalarını kullanarak kubelet ve işletim sistemi yapılandırmasını belirten yeni bir küme oluşturun. 

> [!NOTE]
> Bir küme oluşturduğunuzda kubelet yapılandırması, işletim sistemi yapılandırması veya her ikisini de belirtebilirsiniz. Bir küme oluştururken bir yapılandırma belirtirseniz, yalnızca ilk düğüm havuzundaki düğümler bu yapılandırmayı uygulamış olur. JSON dosyasında yapılandırılmamış tüm ayarlar varsayılan değeri korur.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Oluşturduğunuz JSON dosyasını kullanarak Kubelet parametrelerini belirten yeni bir düğüm havuzu ekleyin.

> [!NOTE]
> Var olan bir kümeye düğüm havuzu eklediğinizde kubelet yapılandırması, işletim sistemi yapılandırması veya her ikisini de belirtebilirsiniz. Düğüm havuzu eklerken bir yapılandırma belirtirseniz, yalnızca yeni düğüm havuzundaki düğümler bu yapılandırmayı uygulamış olur. JSON dosyasında yapılandırılmamış tüm ayarlar varsayılan değeri korur.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Sonraki adımlar

- [AKS kümenizi nasıl yapılandıracağınızı](cluster-configuration.md)öğrenin.
- Kümenizdeki [düğüm görüntülerini nasıl yükselteceğinizi](node-image-upgrade.md) öğrenin.
- Kümenizi, Kubernetes 'in en son sürümüne nasıl yükselteceğinizi öğrenmek için bkz. [Azure Kubernetes hizmeti (AKS) kümesini yükseltme](upgrade-cluster.md) .
- Bazı yaygın AKS sorularına cevap bulmak için [aks hakkında sık sorulan soruların](faq.md) listesine bakın.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why