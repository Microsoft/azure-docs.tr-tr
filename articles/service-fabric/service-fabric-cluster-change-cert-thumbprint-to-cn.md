---
title: Bir kümeyi sertifika ortak adını kullanacak şekilde güncelleştirme
description: Azure Service Fabric küme sertifikasını parmak izi tabanlı bildirimlerden ortak adlara nasıl dönüştüreceğiniz hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: daf8d55e156f30b1f9e9ec5c50d60714e6f17884
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308022"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Küme sertifikalarını parmak izi tabanlı bildirimlerden ortak adlara Dönüştür

Bir sertifikanın imzası (genellikle parmak izi olarak bilinir) benzersizdir. Parmak izi tarafından tanımlanan bir küme sertifikası, bir sertifikanın belirli bir örneğine başvurur. Bu özel durum, sertifika geçişi ve yönetimi genel, zor ve açık hale getirir. Her değişiklik, kümenin ve temel alınan bilgi işlem konaklarının yükseltmelerini düzenlemenizi gerektirir.

Bir Azure Service Fabric kümesinin sertifika bildirimlerini, sertifikanın konu ortak adına (CN) göre, parmak izi tabanlı bildirimlerden dönüştürmek, yönetimi önemli ölçüde basitleştirir. Özellikle, bir sertifikanın yerine bir küme yükseltmesi gerektirmez. Bu makalede, mevcut bir kümenin kapalı kalma süresi olmadan CN tabanlı bildirimlere nasıl dönüştürüleceği açıklanır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>Sertifika yetkilisi tarafından imzalanan sertifikalara gitme

Sertifikası parmak izi tarafından belirtilen bir kümenin güvenliği, başka bir imzayla aynı imzaya sahip bir sertifikayı taklit etmek imkansız veya hesaplama yapılamaz hale gelir. Bu durumda, sertifikanın bir düzeyi daha az önemlidir, bu nedenle otomatik olarak imzalanan sertifikalar yeterlidir.

Bunun aksine, sertifikaları, küme sahibinin kendi sertifika sağlayıcılarında bulunan örtük güvenle CN akışları tarafından bildirildiği kümenin güvenliği. Sağlayıcı, sertifikayı veren ortak anahtar altyapısı (PKI) hizmetidir. Güven, diğer faktörlere bağlı olarak, PKI 'nın sertifika uygulamalarında, işletimsel güvenliğin henüz diğer güvenilir taraflar tarafından denetlenmesi ve onaylanıp onaylanmadığını ve bu şekilde devam eder.

Ayrıca, bu, sertifikaları konuya göre doğrulamaya yönelik temel bir değer olduğundan, küme sahibinin, sertifikalarını hangi sertifika yetkilileri (CA 'Lar) gönderdiğinden ayrıntılı bir bilgisine sahip olması gerekir. Bu, otomatik olarak imzalanan sertifikaların bu amaçla uygun olmadığından de bunu gösterir. Herhangi biri belirli bir konuya sahip bir sertifika oluşturabilir.

CN tarafından tanımlanan bir sertifika genellikle şu durumlarda geçerli kabul edilir:

* Zinciri başarıyla oluşturulabilir.
* Konunun beklenen CN öğesi vardır.
* Veren (zincirdeki anında veya daha yüksek), doğrulamayı gerçekleştiren aracı tarafından güvenilirdir.

Service Fabric, sertifikaların CN tarafından iki şekilde bildirimini destekler:

* *Örtük* verenler ile, zincirin bir güven çıpası içinde bitmesi gerektiği anlamına gelir.
* Parmak iziyle belirtilen verenler ile, veren sabitleme olarak bilinir.

Daha fazla bilgi için bkz. [ortak ad tabanlı sertifika doğrulama bildirimleri](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

CN 'ye parmak izi tarafından belirtilen otomatik olarak imzalanan bir sertifika kullanarak bir kümeyi dönüştürmek için, hedef, CA imzalı sertifikanın önce parmak izi tarafından kümeye tanıtılmalıdır. Yalnızca, parmak izden CN 'ye dönüştürme yapılabilir.

Sınama amacıyla, otomatik olarak imzalanan *bir SERTIFIKA CN* tarafından, ancak veren kendi parmak izine sabitlenmişse, Güvenlik açısından bu eylem, parmak izine göre aynı sertifikayı bildirmek için neredeyse eşdeğerdir. Bu tür başarılı bir dönüştürme, CA imzalı bir sertifikayla, parmak izden CN 'ye başarılı bir dönüştürme garantisi vermez. Dönüştürmeyi, uygun CA imzalı bir sertifikayla test etmenizi öneririz. Bu test için ücretsiz seçenekler var.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Sertifikayı karşıya yükleyin ve ölçek kümesine yükleyin

Azure 'da, sertifika alma ve sağlama için önerilen mekanizma Azure Key Vault ve araç araçlarını içerir. Küme sertifikası bildirimiyle eşleşen bir sertifika, kümelerinizi oluşturan sanal makine ölçek kümelerinin her düğümüne sağlanmalıdır. Daha fazla bilgi için bkz. [sanal makine ölçek kümelerinde gizli](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#how-do-i-securely-ship-a-certificate-to-the-vm-)diziler.

Kümenin sertifika bildirimlerinde değişiklik yapmadan önce, kümenin her düğüm türünün sanal makinelerine hem geçerli hem de hedef küme sertifikalarının yüklenmesi önemlidir. Sertifika verme işleminden Service Fabric bir düğümü sağlamak için yapılan yolculuğun bir [sertifikanın yolculuğunda](cluster-security-certificate-management.md#the-journey-of-a-certificate)derinlemesine bir şekilde ele alındığı açıklanır.

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>Kümeyi en iyi başlangıç durumuna getirme

Parmak izi tabanlı bir sertifika bildirimini CN tabanlı etkilerle dönüştürme:

- Kümedeki her bir düğümün kimlik bilgilerini diğer düğümlere nasıl bulduğu ve sunduğunu gösterir.
- Her düğüm, güvenli bağlantı kurulduktan sonra karşılık gelen kimlik bilgilerini nasıl doğrular.

Devam etmeden önce [her iki yapılandırma için de sunuyu ve doğrulama kurallarını](cluster-security-certificates.md#certificate-configuration-rules) gözden geçirin. -To-CN dönüştürmesi gerçekleştirdiğinizde en önemli fark, yükseltilen ve henüz yükseltilmemiş düğümlerin (yani, farklı yükseltme etki alanlarına ait düğümlerin) yükseltme sırasında her zaman karşılıklı kimlik doğrulaması gerçekleştirebilmelidir. Bu davranışı elde etmenin önerilen yolu, bir başlangıç yükseltmesinde hedef veya hedef sertifikayı parmak izine göre bildirmektir. Ardından, sonraki bir adımda CN 'ye geçişi tamamlayabilirsiniz. Küme zaten önerilen bir başlangıç durumundaysa, bu bölümü atlayabilirsiniz.

Bir dönüştürme için birden çok geçerli başlangıç durumu var. Bu, kümenin, CN 'ye Yükseltmenin başlangıcında hedef sertifikayı (parmak izi ile bildirilmiştir) zaten kullanıyor olması. , `GoalCert` `OldCert1` Ve bu makaleye göz önünde bulunduruyoruz `OldCert2` .

#### <a name="valid-starting-states"></a>Geçerli başlangıç durumları

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, `GoalCert` bu değerden sonraki bir `NotBefore` tarihe sahip `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, `GoalCert` bu değerden sonraki bir `NotBefore` tarihe sahip `OldCert1`

> [!NOTE]
> Sürüm 7.2.445 (7,2 CU4) öncesinde, en uzun süresi dolan sertifikayı (en uzak ' NotAfter ' özelliğine sahip sertifika) Service Fabric, bu nedenle yukarıdaki başlangıç durumları 7,2 ' den önce, GoalCert 'ın daha sonraki bir tarihe sahip olmasını gerektirir `NotAfter``OldCert1`

Kümeniz daha önce açıklanan durumlardan birinde değilse, bu makalenin sonundaki bölümde bu durumu elde etmeye yönelik bilgiler bölümüne bakın.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>İstenen CN tabanlı sertifika doğrulama şemasını seçin

Daha önce açıklandığı gibi Service Fabric, bir örtük güven çıpası ile veya veren parmak izlerinin açıkça sabitlenmesi ile CN tarafından sertifika bildirimini destekler. Daha fazla bilgi için bkz. [ortak ad tabanlı sertifika doğrulama bildirimleri](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Farkları ve her iki mekanizmayı seçme etkilerine ilişkin iyi bir fikir sahibi olduğunuzdan emin olun. Sözdizimi, bu fark veya seçenek parametrenin değerine göre belirlenir `certificateIssuerThumbprintList` . Boş bir güvenilen kök CA 'ya (güven çıpası) bağlı olarak, bir parmak izi kümesi, küme sertifikalarının izin verilen doğrudan verenler kısıtlar.

   > [!NOTE]
   > `certificateIssuerThumbprint`Alanı, konu CN tarafından belirtilen sertifikaların beklenen doğrudan verenler belirtmenize olanak tanır. Kabul edilebilir değerler bir veya daha fazla virgülle ayrılmış SHA1 parmak izdir. Bu eylem, sertifika doğrulamasını güçlendirir.
   >
   > Herhangi bir veren belirtilmemişse veya liste boşsa, zinciri derleyebileceğinden kimlik doğrulaması için sertifika kabul edilecektir. Daha sonra sertifika, doğrulayıcı tarafından güvenilen bir kökte sona erer. Bir veya daha fazla veren parmak izi belirtilirse, zincirdeki ayıklanarak doğrudan veren 'in parmak izi, bu alanda belirtilen değerlerden biriyle eşleşiyorsa, sertifika kabul edilir. Sertifika, köke güvenilip güvenilmediği kabul edilir.
   >
   > Bir PKI, belirli bir konuyla sertifikaların imzalandığı farklı sertifika yetkilileri ( *verenler* olarak da bilinir) kullanabilir. Bu nedenle, söz konusu konu için beklenen tüm veren parmak izlerinin belirtilmesi önemlidir. Diğer bir deyişle, bir sertifikanın yenilenmesi, yenilenen sertifikayla aynı veren tarafından imzalanmamış olarak garanti edilmez.
   >
   > Verenin belirtilmesi en iyi yöntem olarak kabul edilir. Sertifikayı dışarıda bırakmak, güvenilen bir köke kadar olan sertifikalar için çalışmaya devam eder, ancak bu davranışın sınırlamaları vardır ve yakın gelecekte kullanıma hazır olabilir. Azure 'da dağıtılan ve özel bir PKI tarafından verilen x509 sertifikalarıyla güvenli hale getirilen ve konuya göre belirtilen kümeler Service Fabric (kümeden hizmete iletişim için) tarafından doğrulanmamış olabilir. Doğrulama PKI 'nın sertifika ilkesinin bulunabilir, kullanılabilir ve erişilebilir olmasını gerektirir.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>Kümenin Azure Resource Manager şablonunu güncelleştirin ve dağıtın

Service Fabric kümelerinizi Azure Resource Manager (ARM) şablonlarıyla yönetin. JSON yapıtları de kullanan alternatif, [Azure Kaynak Gezgini (Önizleme)](https://resources.azure.com). Şu anda Azure portal eşdeğer bir deneyim bulunmamaktadır.

Var olan bir kümeye karşılık gelen özgün şablon yoksa, Azure portal eşdeğer bir şablon elde edilebilir. Kümeyi içeren kaynak grubuna gidin ve soldaki **Otomasyon** menüsünden **şablonu dışarı aktar** ' ı seçin. Ardından istediğiniz kaynakları seçin. En azından, sanal makine ölçek kümesi ve küme kaynakları, sırasıyla verilmelidir. Oluşturulan şablon da indirilebilir. Bu şablon, tamamen dağıtılabilir bir şekilde değişiklik yapılmasını gerektirebilir. Şablon, tamamen özgün bir ile eşleşmeyebilir. Bu, küme kaynağının geçerli durumunun bir yansımasıyla aynıdır.

Gerekli değişiklikler aşağıdaki gibidir:

- Service Fabric düğüm uzantısının tanımı güncelleştiriliyor (sanal makine kaynağı altında). Küme birden çok düğüm türünü tanımlıyorsa, karşılık gelen her bir sanal makine ölçek kümesinin tanımını güncelleştirmeniz gerekir.
- Küme kaynak tanımı güncelleştiriliyor.

Ayrıntılı örnekler buraya eklenmiştir.

### <a name="update-the-virtual-machine-scale-set-resources"></a>Sanal makine ölçek kümesi kaynaklarını Güncelleştir
Kimden:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Hedef:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>Küme kaynağını güncelleştirme

**Microsoft. ServiceFabric/kümeler** kaynağında, **Commonnames** ayarıyla bir **certificatecommonnames** özelliği ekleyin ve **sertifika** özelliğini tamamen (tüm ayarları) kaldırın.

Kimden:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Hedef:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Daha fazla bilgi için bkz. [parmak izi yerine sertifika ortak adı kullanan Service Fabric kümesi dağıtma](./service-fabric-create-cluster-using-cert-cn.md).

## <a name="deploy-the-updated-template"></a>Güncelleştirilmiş şablonu dağıtma

Değişiklikleri yaptıktan sonra güncelleştirilmiş şablonu yeniden dağıtın.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Kümeyi CN tabanlı sertifika bildirimlerine dönüştürmek için geçerli bir başlangıç durumu elde edin

| Başlangıç durumu | Yükseltme 1 | Yükseltme 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` ve `GoalCert` sonraki bir `NotBefore` tarihe sahiptir `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` ve `OldCert1` sonraki bir `NotBefore` tarihe sahiptir `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, burada `OldCert1` şundan sonraki bir `NotBefore` Tarih vardır `GoalCert` | Sürümüne yükselt `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, burada `OldCert1` şundan sonraki bir `NotBefore` Tarih vardır `GoalCert` | Sürümüne yükselt `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | `OldCert1` `OldCert2` Durumuna ulaşmak için veya birini kaldırın`Thumbprint: OldCertx, ThumbprintSecondary: None` | Yeni başlangıç durumundan devam et |

> [!NOTE]
> Sürüm 7.2.445 (7,2 CU4) öncesindeki bir sürümdeki küme için, `NotBefore` `NotAfter` Yukarıdaki durumlarda ile değiştirin.

Bu yükseltmelerden herhangi birini gerçekleştirme hakkında yönergeler için bkz. [Azure Service Fabric kümesinde sertifikaları yönetme](service-fabric-cluster-security-update-certs-azure.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Küme güvenliği](service-fabric-cluster-security.md)hakkında bilgi edinin.
* [Ortak ad ile bir küme sertifikasını](service-fabric-cluster-rollover-cert-cn.md)nasıl alabileceğinizi öğrenin.
* [Touchless oto geçişi için bir kümeyi yapılandırmayı](cluster-security-certificate-management.md)öğrenin.

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
