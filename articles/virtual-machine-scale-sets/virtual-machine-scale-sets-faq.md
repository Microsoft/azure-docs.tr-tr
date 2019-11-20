---
title: Azure sanal makine ölçek kümeleri hakkında SSS | Microsoft Docs
description: Sanal makine ölçek kümeleri hakkında sık sorulan soruların yanıtlarını alın.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2019
ms.author: manayar
ms.custom: na
ms.openlocfilehash: 429e201ba1d15103ae130ee2fb767cd1b4fa909a
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779422"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Azure sanal makine ölçek kümeleri hakkında SSS

Azure'da sanal makine ölçek kümeleri hakkında sık sorulan soruların yanıtlarını alın.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Üst ölçek kümeleri için sık sorulan sorular

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Bir ölçek kümesinde kaç tane sanal makinem olabilir?

Bir ölçek kümesi, platform görüntülerini temel alan 0 ila 1.000 VM'ye veya özel görüntüleri temel alan 0-600 VM'ye sahip olabilir.

### <a name="are-data-disks-supported-within-scale-sets"></a>Ölçek kümelerinde veri diskleri destekleniyor mu?

Evet. Bir ölçek kümesi, kümedeki tüm sanal makineler için geçerli olan bağlı veri diski yapılandırmasını tanımlayabilir. Daha fazla bilgi için bkz. [Azure ölçek kümeleri ve bağlı veri diskleri](virtual-machine-scale-sets-attached-disks.md). Veri depolamayla ilgili diğer seçenekler şunlardır:

* Azure dosyaları (paylaşılan SMB sürücüleri)
* İşletim sistemi sürücüsü
* Geçici sürücü (yerel, Azure Depolama tarafından yedeklenmez)
* Azure veri hizmeti (örneğin Azure tabloları, Azure blobları)
* Dış veri hizmeti (örneğin, uzak veritabanı)

### <a name="which-azure-regions-support-scale-sets"></a>Hangi Azure bölgeleri ölçek kümelerini destekler?

Tüm bölgeler ölçek kümelerini destekler.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Özel bir görüntü kullanarak nasıl ölçek kümesi oluşturabilirim?

Oluşturma ve bir VM görüntüsü yakalayabilir ve ardından, Ölçek kümeniz için kaynak olarak kullanın. Özel bir VM görüntüsü oluşturma ve kullanma hakkında bir öğretici için kullandığınız [Azure CLI](tutorial-use-custom-image-cli.md) veya [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Ölçek kümemin kapasitesini 20’den 15’e düşürürsem hangi VM’ler kaldırılır?

Sanal makineler, ölçek kümesinden güncelleştirme etki alanları ve hata etki alanları arasında eşit olacak şekilde kaldırılır. En yüksek kimlik numarasına sahip VM’ler ilk önce kaldırılır.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Kapasiteyi 15’ten 18’e yükseltirsem ne olur?

Kapasiteyi 18’e artırırsanız 3 yeni VM oluşturulur. Her defasında VM örnek kimliği önceki en yüksek değerden artırılır (örneğin 20, 21, 22). VM’ler hata etki alanlarında ve güncelleştirme etki alanlarında dengelenir.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Bir ölçek kümesinde birden fazla uzantı kullanırken bir yürütme sırası uygulamayı zorunlu kılabilir miyim?

Evet, ölçek kümesi [uzantı sıralamasını](virtual-machine-scale-sets-extension-sequencing.md)kullanabilirsiniz.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Ölçek kümeleri Azure kullanılabilirlik kümeleri ile birlikte çalışır mı?

Kullanan bölgesel (Bölgesel olmayan) ölçek kümesi *yerleştirme grubuna*, örtülü bir kullanılabilirlik kümesi ile beş hata etki alanları ve beş güncelleştirme etki gibi davranır. 100'den fazla sanal makine ölçek kümeleri birden fazla yerleştirme grubuna'yayılır. Yerleştirme grupları hakkında daha fazla bilgi için bkz. [Büyük sanal makine ölçek kümeleri ile çalışma](virtual-machine-scale-sets-placement-groups.md). Bir sanal makine kullanılabilirlik kümesi, sanal makine ölçek kümesiyle aynı sanal ağda bulunabilir. Genellikle bir kullanılabilirlik kümesinde benzersiz yapılandırma gerektiren denetim düğümünü sanal makinelere, veri düğümlerini ise ölçek kümesine yerleştirmek, yaygın bir yapılandırmadır.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Ölçek kümeleri Azure kullanılabilirlik alanları çalışmak?

Evet! Daha fazla bilgi için [ölçek kümesi bölge doc](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Otomatik Ölçeklendirme

### <a name="what-are-best-practices-for-azure-autoscale"></a>Azure otomatik ölçeklendirme için en iyi uygulamalar nelerdir?

Otomatik ölçeklendirme için en iyi yöntemler için bkz. [sanal makineleri otomatik ölçeklendirme için en iyi yöntemler](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Ana bilgisayar tabanlı ölçümler kullanan otomatik ölçeklendirme ölçüm adları nerede bulabilirim?

Ana bilgisayar tabanlı ölçümler kullanan otomatik ölçeklendirme için ölçüm adları için bkz [Azure İzleyici ile desteklenen ölçümler](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Bir Azure Service Bus konu ve kuyruk uzunluğuna göre otomatik ölçeklendirme, örnekler var mı?

Evet. Bir Azure Service Bus konu ve kuyruk uzunluğuna göre otomatik ölçeklendirme örnekleri için bkz: [Azure İzleyici otomatik ölçeklendirme ortak ölçümleri](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Bir Service Bus kuyruğu için aşağıdaki JSON kullanın:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Bir depolama kuyruğu için aşağıdaki JSON kullanın:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Örnek değerler kaynak Tekdüzen Kaynak Tanımlayıcıları (URI'lar) ile değiştirin.


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Mıyım otomatik ölçeklendirme ana bilgisayar tabanlı ölçümler veya bir tanılama uzantısı'nı kullanarak?

Konak düzeyinde ölçümler veya konuk işletim sistemi tabanlı ölçümler kullanan bir sanal makine üzerinde bir otomatik ölçeklendirme ayarı oluşturabilirsiniz.

Desteklenen ölçümlerin listesi için bkz. [Azure İzleyici otomatik ölçeklendirme ortak ölçümleri](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics).

Sanal makine ölçek kümeleri için tam bir örnek için bkz: [sanal makine ölçek kümeleri için Resource Manager şablonlarını kullanarak gelişmiş otomatik ölçeklendirme Yapılandırması](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets).

Örnek konak düzeyinde CPU ölçüm ve bir ileti sayısı ölçüm kullanır.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Uyarı kuralları bir sanal makine ölçek kümesinde nasıl ayarlayabilirim?

PowerShell veya Azure CLI aracılığıyla sanal makine ölçek kümeleri için ölçümler üzerinde uyarılar oluşturabilirsiniz. Daha fazla bilgi için [Azure İzleyici PowerShell hızlı başlangıç örnekleri](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) ve [Azure İzleyici platformlar arası CLI hızlı başlangıç örnekleri](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

Sanal makine ölçek kümesinin Targetresourceıd şöyle görünür:

/Subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.COMPUTE/virtualMachineScaleSets/yourvmssname

Tüm VM performans sayacı için uyarı ayarlama için ölçüm olarak seçebilirsiniz. Daha fazla bilgi için [Resource Manager tabanlı bir Windows sanal makineler için konuk işletim sistemi ölçümleri](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) ve [Linux VM'ler için konuk işletim sistemi ölçümleri](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) içinde [Azure İzleyici otomatik ölçeklendirme ortak ölçümleri](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)makalesi.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>PowerShell kullanarak bir sanal makine ölçek otomatik ölçeklendirme nasıl ayarlayabilirim?

Otomatik ölçeklendirmeyi PowerShell kullanarak bir sanal makine ölçek ayarlamak için bkz: [bir sanal makine ölçek kümesini otomatik ölçeklendirme](tutorial-autoscale-powershell.md). Ayrıca otomatik ölçeklendirme ile yapılandırabileceğiniz [Azure CLI](tutorial-autoscale-cli.md) ve [Azure şablonları](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>(Durduruldu serbest bırakıldı) durdurduysanız, bir VM olan başlatılan bir otomatik ölçeklendirme işleminin bir parçası bu VM'ye?

Hayır. Otomatik ölçeklendirme kurallarını ölçek kümesinin bir parçası ek sanal makine örnekleri gerekiyorsa, yeni bir VM örneği oluşturulur. (Serbest bırakıldı) durdurulan sanal makine örnekleri bir otomatik ölçeklendirme olayının bir parçası başlatılmaz. Ancak, bu durduruldu (serbest bırakıldı) sanal makineler ölçekler örnekleri sayısının VM örneğine bazında herhangi bir VM örneğine silinebilir aynı şekilde kimliği bir otomatik ölçeklendirme olayının bir parçası silinebilir



## <a name="certificates"></a>Sertifikalar

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Nasıl bir sertifika VM güvenli bir şekilde sevk?

Güvenli bir şekilde sertifika VM dağıtmayı, bir müşterinin anahtar kasası Windows sertifika deposundan doğrudan bir müşteri sertifika yükleyebilirsiniz.

Aşağıdaki JSON kullanın:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

Kodu Windows ve Linux'ı destekler.

Daha fazla bilgi için [oluşturma veya güncelleştirme bir sanal makine ölçek kümesi](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Nasıl yaparım? Azure Service Fabric kümeleri için sağlanan otomatik olarak imzalanan sertifikaları kullanmak mı istiyorsunuz?
En son örnek için Azure kabuğu 'nda aşağıdaki Azure CLı ifadesini kullanın, Service dokuları CLı modülü örnek belgelerini okuyun ve STDOUT 'a yazdırılır:

```bash
az sf cluster create -h
```

Otomatik olarak imzalanan sertifikalar, bir sertifika yetkilisi tarafından sağlanmış olan dağıtılan güven için kullanılamaz ve kurumsal üretim çözümlerini barındırmak için tasarlanan tüm Service Fabric kümeleri için kullanılmamalıdır. Ek Service Fabric güvenlik kılavuzu için [Azure Service Fabric En Iyi güvenlik uygulamalarını](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) inceleyin ve [küme güvenliği senaryolarını Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Ben, Linux sanal makine ölçek kümesi bir Resource Manager şablonundan SSH kimlik doğrulaması için kullanılacak SSH anahtar çifti belirtebilir miyim?

Evet. REST API'yi **osProfile** standart VM REST API için benzer.

Dahil **osProfile** şablonunuzdaki:

```json
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```

Bu JSON bloğu [Bu Azure hızlı başlangıç şablonunda](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)kullanılır.

Daha fazla bilgi için [oluşturma veya güncelleştirme bir sanal makine ölçek kümesi](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Kullanım dışı sertifikaları nasıl kaldırabilirim?

Kullanım dışı sertifikalar kaldırmak için eski sertifikayı kasa sertifikalar listesinden kaldırın. Bilgisayarınızda listesinde kalmasını istediğiniz tüm sertifikaların bırakın. Bu sertifikayı tüm Vm'lerinizden kaldırmaz. Bu da sertifika sanal makine ölçek kümesinde oluşturulan yeni vm'lere eklemez.

Sertifikayı mevcut VM 'lerden kaldırmak için, sertifika deponuzdan sertifikaları el ile kaldırmak için özel bir betik uzantısı kullanın.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Nasıl ı var olan bir SSH ortak anahtarını sanal makine ölçek kümesi SSH katmana sağlama sırasında ekleme?

Sanal makinelerin yalnızca bir ortak SSH anahtarının ile sağlıyorsanız, ortak anahtarları Key Vault'ta put gerekmez. Ortak anahtarları, gizli değildir.

Bir Linux VM oluşturma sırasında SSH ortak anahtarlarını düz metin sağlayabilirsiniz:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
}
```

linuxConfiguration öğe adı | Gerekli | Tür | Açıklama
--- | --- | --- | ---
SSH | Hayır | Koleksiyon | Bir Linux işletim sistemi için SSH anahtar yapılandırmasını belirtir
yol | Evet | Dize | Burada SSH anahtarlarını veya sertifika klasöründe bulunmalıdır Linux dosya yolunu belirtir
anahtar verileri | Evet | Dize | Bir base64 kodlamalı SSH ortak anahtarını belirtir.

Bir örnek için bkz. [101 vm sshkey GitHub Hızlı Başlangıç şablonu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Ne zaman çalıştırabilir `Update-AzVmss` birden fazla sertifika aynı anahtar kasasından ekledikten sonra şu iletiyi görüyorum:

>Güncelleştirme-AzVmss: Liste gizli dizisi, izin verilmeyen/Subscriptions/\<My-Subscription-id >/resourceGroups/internal-RG-dev/Providers/Microsoft.KeyVault/Vaults/internal-keyvault-dev örneklerini içerir.

Bu var olan bir kaynak kasası için yeni bir kasa sertifika kullanmak yerine aynı kasaya yeniden eklemeyi denerseniz oluşabilir. `Add-AzVmssSecret` Komut düzgün çalışmaz ek gizli dizileri ekliyorsanız.

Daha fazla gizli dizileri aynı anahtar kasasından eklemek için $vmss.properties.osProfile.secrets[0].vaultCertificates listeyi güncelleştirin.

Beklenen Giriş yapısı için bkz: [oluşturma veya güncelleştirme bir sanal makine kümesi](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Gizli anahtar Kasası'nda sanal makine ölçek kümesi nesnesini bulabilirsiniz. Ardından, sertifika referans (URL'sini ve gizli dizi deposu adı) kasayla ilişkili listesine ekleyin.

> [!NOTE]
> Şu anda sanal makine ölçek kümesi API'si kullanarak sertifikaları Vm'lerden kaldırılamıyor.
>

Yeni sanal makineler eski sertifika yoktur. Ancak, sertifika sahip olan ve hangi zaten dağıtılmış olan sanal makineler eski sertifika gerekir.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Sertifikalar için sertifika gizli dizi deposu içinde olduğunda parola sağlamadan sanal makine ölçek gönderebilir?

Sabit kodlu parolaları komut gerekmez. Dağıtım betiği çalıştırmak için kullandığınız izinlerle parolaları dinamik olarak alabilir. Gizli dizi deposu anahtarından bir sertifika taşıyan bir betiğiniz varsa kasa, gizli dizi deposu `get certificate` komut ayrıca parola .pfx dosyasının çıkarır.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Bir sanal makine ölçek virtualMachineProfile.osProfile gizli dizileri özelliğini iş nasıl ayarlar? CertificateUrl özelliğini kullanarak bir sertifika için bir mutlak URI belirtmeniz gerektiğinde sourceVault değeri neden ihtiyacım var?

Windows Uzaktan Yönetim (WinRM) sertifika başvuru işletim sistemi profili gizli dizileri özelliğinde mevcut olması gerekir.

Kaynak kasası belirten amacı, bir kullanıcının Azure bulut hizmet modelinde var olan erişim denetimi listesi (ACL) İlkeleri zorunlu sağlamaktır. Kaynak kasası belirtilmezse, dağıtmak veya gizli bir anahtar kasasına erişmek için izinleri olmayan kullanıcılar bir işlem kaynak sağlayıcısı (CRP aracılığıyla) şunları yapabilecek. ACL'ler bile var olmayan kaynaklar için mevcut.

Bir yanlış kaynak kasa kimliği geçerli bir anahtar kasası URL'si ancak sağlarsanız, işlemi yoklama sırasında bir hata bildirilir.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Mevcut bir gizli dizileri ekleyebilirim, sanal makine ölçek ayarlayın, gizli dizileri mevcut Vm'lere veya yenilerini yalnızca içine eklenmiş olan?

Hatta önceden var olanları tüm Vm'leriniz için sertifikalar eklenir. Sanal makine ölçek kümenizi upgradePolicy özelliğini ayarlarsanız ayarlanır **el ile**, VM'de el ile güncelleştirme gerçekleştirdiğinizde VM'ye sertifika eklenir.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Linux Vm'leri için burada sertifikaları put?

Linux Vm'leri için sertifikaları dağıtma konusunda bilgi edinmek için [dağıtma sertifikaların Vm'lere müşteri tarafından yönetilen bir anahtar kasasından](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Yeni bir kasa sertifika için yeni bir sertifika nesnesi nasıl ekleyebilirim?

Kasa sertifika için var olan bir gizli dizi eklemek için aşağıdaki PowerShell örneğine bakın. Yalnızca bir gizli dizi nesnesini kullanın.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Bir sanal makine görüntüsünü yeniden sertifikaları ne olur?

Bir sanal makine görüntüsünü yeniden sertifika silinir. Siler tüm işletim sistemi disk görüntüsü yeniden oluşturuluyor.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Anahtar kasasından sertifika silme ne olur?

Anahtar kasasından gizli dizi silinir ve ardından çalıştırdığınız `stop deallocate` tüm Vm'leriniz için ve sonra yeniden başlatın, karşılaştığınız hata. Gizli dizileri anahtar kasasından almak CRP gerekiyor, ancak bu işlem gerçekleştirilemiyor çünkü hata oluşur. Bu senaryoda, sanal makine ölçek kümesi modelinden sertifikaları silebilirsiniz.

CRP bileşen müşteri gizli dizileri devam etmez. Çalıştırırsanız `stop deallocate` sanal makine ölçek kümesindeki tüm sanal makineler için önbellek silinir. Bu senaryoda, gizli dizileri anahtar kasasından alınır.

Azure Service fabric'te gizli (fabric tek kiracılı model), önbelleğe alınmış bir kopyasını olduğundan, ölçeği genişletme, bu sorunla karşılaşırsanız yok.

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Key Vault kullandığımda sertifika sürümü belirtmek neden olan?

Sertifika sürümü belirtmek için Key Vault gereksinimi amacı, hangi sertifika sanal makinelerindeki dağıtılır, kullanıcıya Temizle olmasını sağlamaktır.

Bir VM oluşturun ve sonra gizli anahtar Kasası'nda güncelleştirmeniz, yeni sertifikayı Vm'lerinizi indirilmez. Ancak buna başvuruda bulunma Vm'lerinizi görünür ve yeni sanal makineler yeni gizli anahtar alın. Bunu önlemek için bir gizli dizi sürümü başvurmak için gereklidir.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Takımım bize .cer ortak anahtarları dağıtılan birden fazla sertifika ile çalışır. Ne için sanal makine ölçek bu sertifikaları dağıtmak için önerilen yaklaşım ayarlanır?

.Cer dağıtmak için ortak anahtarları bir sanal makine ölçek kümesi, yalnızca .cer dosyalarını içeren bir .pfx dosyası oluşturabilirsiniz. Bunu yapmak için `X509ContentType = Pfx`. Örneğin, C# veya Powershell'inizi x509Certificate2 nesne olarak .cer dosyasını yükleyin ve ardından yöntemi çağırın.

Daha fazla bilgi için [X509Certificate.Export yöntemi (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Sertifikalar Nasıl yaparım? Base64 dizeleri olarak mı?

Bir base64 dizesi olarak bir sertifika geçirme benzetmek için bir Resource Manager şablonunda en son sürümü tutulan URL ayıklayabilirsiniz. Resource Manager şablonunuzda aşağıdaki JSON özelliği ekleyin:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>JSON anahtar kasası nesnelerindeki sertifikaları kaydırın gerekiyor mu?

Sanal makine ölçek kümeleri ve Vm'leri, sertifikaları JSON nesneleri alınmalıdır.

İçerik türü application/x-pkcs12 destekliyoruz.

Şu anda .cer dosyalarını desteklemiyoruz. .Cer dosyalarını kullanmak için bunları .pfx kapsayıcılarına dışarı aktarın.



## <a name="compliance-and-security"></a>Uyumluluk ve güvenlik

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Sanal makine ölçek kümeleri PCI ile uyumlu misiniz?

Sanal makine ölçek kümeleri, CRP’nin üzerinde ince bir API katmanıdır. Her iki bileşen de, Azure hizmet ağacındaki işlem platformunun birer parçasıdır.

Uyumluluk açısından bakıldığında, sanal makine ölçek kümeleri Azure işlem platformunun temel parçalarından birini oluşturur. CRP’nin kendisiyle takımı, araçları, süreçleri, dağıtım yöntemini, güvenlik denetimlerini, tam zamanında (JIT) derlemeyi, izlemeyi, uyarıları, vb. paylaşır. Sanal makine ölçek kümeleri Ödeme Kartı Sektörü (PCI) ile uyumludur, çünkü CRP geçerli PCI Veri Güvenliği Standardı (DSS) kanıtının bir parçasıdır.

Daha fazla bilgi için bkz. [Microsoft Güven Merkezi](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resourceshttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>Mu [kimliklerini Azure kaynakları için yönetilen](https://docs.microsoft.com/azure/active-directory/msi-overview) sanal makine ölçek kümeleri ile çalışabilir?

Evet. [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) ve [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi)için Azure hızlı başlangıç şablonlarında bazı örnek MSI şablonlarına bakabilirsiniz.


## <a name="extensions"></a>Uzantılar

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Bir sanal makine ölçek kümesi uzantısını nasıl silebilirim?

Bir sanal makine ölçek kümesi uzantısını silmek için aşağıdaki PowerShell örneği kullanın:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

ExtensionName değerini bulabilirsiniz `$vmss`.

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Azure Izleyici günlükleri ile tümleşen bir sanal makine ölçek kümesi şablonu örneği var mı?

Azure Izleyici günlükleri ile tümleşen bir sanal makine ölçek kümesi şablonu örneği için Azure [Service Fabric kümesi dağıtma ve Azure izleyici günlüklerini kullanarak izlemeyi etkinleştirme](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric)bölümündeki ikinci örneğe bakın.

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Nasıl bir uzantı tüm sanal makinelere my sanal makine ölçek kümesindeki ekleyebilirim?

Güncelleştirme ilkesi ayarlanırsa **otomatik**, tüm sanal makineler yeni uzantı özellikleri ile şablonunu yeniden dağıtmadan güncelleştirir.

Güncelleştirme ilkesi ayarlanırsa **el ile**önce uzantıyı güncelleştirin ve vm'lerinizde tüm örnekleri el ile güncelleştirin.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Mevcut bir sanal makine ölçek kümesi ile ilişkili uzantıları güncelleştirdiyseniz, etkilenen VM'lerin mevcut olan?

Sanal makine ölçek genişletme tanımında ayarlarsanız modeli güncelleştirilir ve upgradePolicy özelliği **otomatik**, Vm'leri güncelleştirir. UpgradePolicy özelliği ayarlanmışsa **el ile**, uzantılar, model eşleşmeyen olarak işaretlenir.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Mevcut bir makine Service-cenled veya yeniden görüntüsü olduğunda uzantılar yeniden çalıştırılır mi?

Var olan bir VM Service-cenled ise, yeniden önyükleme olarak görünür ve uzantılar yeniden çalıştırılmaz. Bir VM yeniden yansıma içeriyorsa, işlem işletim sistemi sürücüsünü kaynak görüntüyle değiştirme benzerdir. Uzantılar gibi en son modelden herhangi bir özelleştirme yeniden çalıştırılır.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Nasıl bir sanal makine ölçek kümesi bir Active Directory etki alanına katılacak mısınız?

Sanal makine ölçek kümesi için bir Active Directory (AD) etki alanına katılmak için bir uzantı tanımlayabilirsiniz.

Bir uzantıyı tanımlamak için JsonADDomainExtension özelliğini kullanın:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>My sanal makine ölçek kümesi uzantısını yeniden başlatma gerektiren bir sorun yüklemeye çalışıyor.

Sanal makine ölçek kümesi uzantısını yeniden başlatma gerektiren bir sorun yüklemeye çalışan Azure Otomasyonu Desired State Configuration (Automation DSC) uzantısı kullanabilirsiniz. İşletim sistemi Windows Server 2012 R2 ise, Azure Windows Management Framework (WMF) 5.0 Kurulum, yeniden başlatmalar çeker ve ardından yapılandırma ile devam eder.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Kötü amaçlı yazılımdan koruma üzerinde my sanal makine ölçek kümesinde nasıl kapatırım?

Kötü amaçlı yazılımdan koruma, sanal makine ölçek kümesi üzerinde etkinleştirmek için aşağıdaki PowerShell örneği kullanın:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Özel bir depolama hesabında barındırılan özel bir betik Nasıl yaparım? mi çalışıyor?

Bir özel depolama hesabında barındırılan özel bir betik yürütmek için korunan ayarları depolama hesabı anahtarı ve adı ile ayarlayın. Daha fazla bilgi için bkz. [Özel Betik uzantısı](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).

## <a name="passwords"></a>Parolalar

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Nasıl miyim my sanal makine ölçek kümesindeki VM'ler için parola sıfırlama?

Ölçek kümesinde sanal makineler için parolayı değiştirmek için başlıca iki yolu vardır.

- Sanal makine ölçek kümesi modelinden doğrudan değiştirin. API 2017-12-01 ve üzeri sürümlerde kullanılabilir.

    Doğrudan ölçek kümesi modelinde (örneğin bir Azure kaynak Gezgini, PowerShell veya CLI kullanarak) yönetici kimlik bilgilerini güncelleştirin. Ölçek kümesi sonra güncelleştirilmiş, tüm yeni Vm'lere yeni kimlik bilgilerine sahip. Bunlar başlatıldığında, var olan VM'ler yalnızca yeni kimlik bilgileri gerekir.

- VM erişimi uzantılarını kullanarak parolayı sıfırlayın.

    Aşağıdaki PowerShell örneğini kullanın:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Ağ

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Kümedeki tüm VM NIC için geçerli olacak şekilde, bir ölçek kümesi için bir ağ güvenlik grubu (NSG) atamak mümkün mü?

Evet. Bir ağ güvenlik grubunun doğrudan bir ölçek kümesi ağ profili Networkınterfaceconfigurations bölümüne başvurarak uygulanabilir. Örnek:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Aynı bölgede ve aynı abonelik içinde sanal makine ölçek kümeleri için bir VIP takası ne yapmalıyım?

İki sanal makine ölçek kümeleri ile Azure Load Balancer ön uçları sahip ve aynı abonelik ve aynı bölgede olmaları, her bir genel IP adreslerini serbest bırakın ve diğer atayın. Bkz [. VIP takas: Örneğin Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) için mavi yeşil dağıtım. Bu gecikme gelmez serbest/ayrılan ağ kaynakları gibi ancak düzeyi. Azure Application Gateway iki arka uç havuzları ve yönlendirme kuralı'nı kullanmayı daha hızlı bir seçenektir. Alternatif olarak, uygulamanızla barındırabilir [Azure uygulama Hizmeti'ni](https://azure.microsoft.com/services/app-service/) hazırlama ve üretim yuvası arasında hızlı geçiş yapmak için destek sağlar.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Statik özel IP adresi ayırma kullanmak için özel IP adresleri aralığı nasıl belirtebilirim?

IP adresleri, belirttiğiniz bir alt ağdan seçilir.

Sanal makine ölçek kümesi IP adresi ayırma yöntemi her zaman "dinamik" olmakla birlikte, bu IP adresleri değiştirebilirsiniz anlamına gelmez. Bu durumda, "dinamik" yalnızca IP adresi bir PUT İsteği belirtmeyin anlamına gelir. Alt ağ kullanarak statik belirtin.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Sanal makine ölçek kümesi için mevcut bir Azure sanal ağına nasıl dağıtabilirim?

Sanal makine ölçek kümesi için mevcut bir Azure sanal ağı dağıtmak için bkz. [bir sanal makine ölçek kümesi bir sanal ağınız Deploy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Ölçek kümeleri hızlandırılmış ağ ile kullanabilir miyim?

Evet. Hızlandırılmış ağ kullanmak için enableAcceleratedNetworking true olarak Networkınterfaceconfigurations ayarlarında kümenin ölçek ayarlayın. Örneğin:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "niconfig1",
            "properties": {
                "primary": true,
                "enableAcceleratedNetworking" : true,
                "ipConfigurations": [
                ]
            }
        }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Bir ölçek kümesi tarafından kullanılan DNS sunucularını nasıl yapılandırabilirim?

Sanal makine ölçek kümesini özel DNS yapılandırmasıyla oluşturmak için ölçek kümesinin Networkınterfaceconfigurations bölümüne bir dnsSettings JSON paketi ekleyin. Örnek:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Bir ölçek kümesindeki her VM için genel bir IP adresi atamak için nasıl yapılandırabilirim?

Her VM için genel bir IP adresi atayan bir sanal makine ölçek kümesi oluşturmak için Microsoft.Compute/virtualMachineScaleSets kaynağı API sürümünün 2017-03-30 olduğundan emin olun ve Ekle bir _publicıpaddressconfiguration_ JSON Paket ölçek ipConfigurations bölümüne ayarlayın. Örnek:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Bir ölçek kümesi birden fazla uygulama ağ geçitleri ile çalışmak için yapılandırabilirim?

Evet. Birden çok Application Gateway arka uç adres havuzu için kaynak kimliklerini, ölçek kümesi ağ profilinizin _ıpconfigurations_ bölümündeki _Applicationgatewaybackendadddresspoir_ listesine ekleyebilirsiniz.

## <a name="scale"></a>Ölçek

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>Hangi durumda miyim ikiden az VM içeren bir sanal makine ölçek kümesi oluşturursunuz?

İkiden az VM içeren bir sanal makine ölçek kümesi oluşturmak için bir neden bir sanal makine ölçek kümesinin elastik özelliklerini kullanmak olabilir. Örneğin, sıfır VM içeren ödeme VM çalıştırma maliyetleri olmadan altyapınızı tanımlamak için bir sanal makine ölçek kümesi dağıtabilirsiniz. Vm'leri dağıtmak hazır olduğunuzda, ardından "üretim örneği sayısına ayarlayın kapasitesini" sanal makine ölçek artırmak.

Başka bir nedenle ikiden az VM içeren bir sanal makine ölçek kümesi oluşturabilir, ayrık VM'ler içeren bir kullanılabilirlik kullanarak kullanılabilirlik az endişe olup olmadığınız ' dir. Sanal makine ölçek kümeleri, farklı protokole işlem birimlerle çalışmak için bir yol sağlar. Bu bütünlük, sanal makine ölçek kümeleri ile kullanılabilirlik kümeleri için önemli bir avantajıdır. Birçok durum bilgisiz iş yükü bireysel birimlere izlemez. İş yükü azalırsa bir işlem birimi için ölçeği azaltın ve iş yükü arttığında ise ardından çoğu için ölçeği artırma.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesindeki VM sayısını nasıl değiştirebilirim?

Sanal makine ölçek kümesi Azure portalında VM'lerin sayısını değiştirmek için sanal makine ölçek kümesi özellikler bölümü, "Ölçek" dikey penceresinde tıklayın ve kaydırıcı çubuğunu kullanın.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Belirli eşikleri dolduğunda özel uyarıları nasıl tanımlarım?

Belirtilen eşik için uyarıları nasıl işleneceğini bazı davranabilirsiniz. Örneğin, özelleştirilmiş Web kancaları tanımlayabilirsiniz. Aşağıdaki Web kancası örnek bir Resource Manager şablonundan verilmiştir:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Düzeltme eki uygulama ve işlemler

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Var olan bir kaynak grubunda ölçek kümesi oluşturabilir miyim?

Evet, var olan bir kaynak grubunda ölçek kümesi oluşturabilirsiniz.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Ölçek kümesini başka bir kaynak grubuna taşıyabilir miyim?

Evet, yeni bir abonelik veya kaynak grubu için kaynakları ölçek kümesi taşıyabilirsiniz.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Nasıl için sanal makine ölçek Kümemin yeni bir görüntüye güncelleştirebilirim? Düzeltme eki uygulama nasıl yönetebilirim?

Sanal makine ölçek kümenizi için yeni bir görüntü güncelleştirme ve düzeltme eki uygulama yönetmek için bkz. [bir sanal makine ölçek kümelerini yükseltme](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Bir VM görüntüsü değiştirmeden sıfırlamak için reimage işlemini yeniden kullanabilir miyim? (Diğer bir deyişle, bir VM yerine yeni bir görüntü fabrika ayarlarına sıfırlama istiyorum.)

Evet, bir VM görüntüsü değiştirmeden sıfırlama işlemi reimage kullanabilirsiniz. Sanal makine ölçek kümesi, ancak bir platform görüntüsü ile başvuran `version = latest`, çağırdığınızda, sanal Makinenizin daha sonraki bir işletim sistemi görüntüsünü güncelleştirebilirsiniz `reimage`.

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Ölçek Kümeleri Azure Izleyici günlükleriyle tümleştirilebilecek mi?

Evet, Azure Izleyici uzantısını ölçek kümesi VM 'lerine yükleyebilirsiniz. Azure CLI örnek aşağıda verilmiştir:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Azure portalında Log Analytics çalışma alanı gerekli Workspaceıd ve workspaceKey bulabilirsiniz. Genel bakış sayfasında, ayarları kutucuğa tıklayın. Üstteki bağlı kaynaklar sekmesine tıklayın.

> [!NOTE]
> Ölçek kümesi _Upgradepolicy_ , manuel olarak ayarlandıysa, uzantıyı üzerinde yükseltme çağırarak küme Içindeki tüm VM 'lere uygulamanız gerekir. CLI'daki bu olacaktır _az vmss update-instances_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Sorun giderme

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Önyükleme tanılamayı nasıl kapatırım?

Önyükleme tanılamayı etkinleştirmek için ilk olarak bir depolama hesabı oluşturun. Ardından, bu JSON bloğu, sanal makine ölçek kümesine yerleştirmek **virtualMachineProfile**ve sanal makine ölçek kümesini güncelleştirin:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Yeni bir VM oluşturulduğunda, sanal makinenin Instanceview özelliği ekran görüntüsü vb. ayrıntıları gösterir. Bir örneği aşağıda verilmiştir:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Sanal makine özellikleri

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Her VM için özellik bilgilerini birden çok çağrı yapmadan nasıl alabilirim? Örneğin, nasıl hata etki alanı her 100 VM için benim sanal makine ölçek kümesindeki elde?

Birden çok çağrı yapmadan her VM için özellik bilgilerini almak için çağırabilirsiniz `ListVMInstanceViews` REST API yaparak `GET` üzerinde aşağıdaki kaynak URI'si:

/Subscriptions/ < subscription_id > /resourceGroups/ < resource_group_name > /providers/Microsoft.Compute/virtualMachineScaleSets/ < scaleset_name > / virtualMachines? $expand = instanceView & $select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Farklı uzantı bağımsız değişkenler için bir sanal makine ölçek kümesi'ndeki farklı Vm'lere geçirebilirsiniz?

Hayır, bir sanal makine ölçek kümesi'ndeki farklı Vm'lere için farklı bir uzantı bağımsız değişkenler geçirilemez. Ancak, uzantıları gibi makine adı gibi çalıştırdıkları işletim VM benzersiz özelliklerine göre hareket eder. Uzantılar da sorgulayabilir örnek meta veri çubuğunda http://169.254.169.254 VM hakkında daha fazla bilgi için.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Neden benim sanal makine ölçek kümesi VM makine adları ve VM kimlikleri arasındaki boşlukları vardır? Örneğin: 0, 1, 3...

Sanal makine ölçek kümesi için sanal makine ölçek kümesi VM makine adları ve VM kimlikleri arasında boşluk olan **overprovision** özelliği varsayılan değerine ayarlanır **true**. Açıdan ayarlanırsa **true**, oluşturulan istenenden daha fazla VM. Ek VM'ler silinir. Bu durumda, artan dağıtım güvenilirlik elde ancak çoğaltamaz bitişik adlandırma ve bitişik ağ adresi çevirisi (NAT) kuralları.

Bu özelliği ayarlamak **false**. Küçük sanal makine ölçek kümeleri için bu dağıtım güvenilirliğini önemli ölçüde etkilemez.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Bir sanal makine ölçek kümesindeki sanal Makineyi silme ve VM serbest bırakılıyor arasındaki fark nedir? Hangisinin diğerine ne zaman kullanmalıyım?

Bir sanal makine ölçek kümesindeki sanal Makineyi silme ve VM serbest bırakılıyor arasındaki ana fark `deallocate` sanal sabit diskleri (VHD'ler) silmez. Çalışan ile ilişkili depolama ücreti `stop deallocate`. Aşağıdaki nedenlerden biri için ikisinden birini kullanabilirsiniz:

- İşlem maliyetler için ödeme durdurmak istediğiniz, ancak VM'lerin disk durumunu tutmak istediğiniz.
- Bir VM kümesi bir sanal makine ölçek kümesini ölçeklendirme daha hızlı başlamak istiyorsanız.
  - Bu senaryo ile ilgili olarak, kendi otomatik ölçeklendirme altyapısı ve daha hızlı uçtan uca ölçek istediğiniz oluşturmuş olabileceğiniz.
- Hata etki alanları veya güncelleştirme etki alanları arasında eşit olmayan şekilde dağıtılan bir sanal makine ölçek kümesi var. Bu, seçmeli olarak Vm'leri silindi veya bulunmadığından, Vm'leri açıdan sonra silinen olabilir. Çalışan `stop deallocate` ardından `start` sanal makinede hata etki alanları veya güncelleştirme etki alanları arasında eşit olacak şekilde ölçek Vm'leri dağıtır.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Bir sanal makine ölçek kümesi örneğinin anlık görüntüsünü almak Nasıl yaparım? mı?
Bir sanal makine ölçek kümesi örneğinden bir anlık görüntü oluşturun.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Anlık görüntüden yönetilen disk oluşturun.

```azurepowershell-interactive
$snapshotName = "myShapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
