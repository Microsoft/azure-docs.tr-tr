---
title: Service Fabric kümesi yükseltmelerini yönetme
description: Service Fabric kümesi çalışma zamanının ne zaman ve nasıl güncelleştirileceğini yönetin
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 98c3300e5cc51c32d894397839879e25190d979b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731176"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Service Fabric kümesi yükseltmelerini yönetme

Azure Service Fabric kümesi, sahip olduğunuz bir kaynaktır, ancak kısmen Microsoft tarafından yönetilir. Microsoft 'un Azure Service Fabric kümenizi ne zaman ve nasıl güncelleştiğinizde aşağıda açıklanmaktadır.

Küme yükseltme kavramları ve işlemlerinde daha fazla arka plan için bkz. [Azure Service Fabric kümelerini yükseltme ve güncelleştirme](service-fabric-cluster-upgrade.md)

## <a name="set-upgrade-mode"></a>Yükseltme modunu ayarla

Kümenizi Microsoft tarafından yayımlandıklarında otomatik Service Fabric yükseltmelerini alacak şekilde ayarlayabilir veya kümeniz için yükseltme modunu ayarlayarak, şu anda Desteklenen sürümlerin bir listesinden el ile seçim yapabilirsiniz. Bu, Azure portal ' deki *Yapı yükseltme modu* denetimi veya `upgradeMode` küme dağıtım şablonunuzun ayarı aracılığıyla yapılabilir.

### <a name="azure-portal"></a>Azure portalı

Azure portal kullanarak, yeni bir Service Fabric kümesi oluştururken otomatik veya el ile yükseltme arasında seçim yapabilirsiniz.

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="' Gelişmiş ' seçeneklerinden Azure portal yeni bir küme oluştururken otomatik veya el ile yükseltme arasında seçim yapın":::

Ayrıca, var olan bir küme kaynağının **doku yükseltmeleri** bölümünde otomatik veya el ile yükseltme arasında geçiş yapabilirsiniz.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Azure portal küme kaynağınızın ' Fabric yükseltmeleri ' bölümünde otomatik veya el Ile yükseltmeler ' i seçin":::

### <a name="manual-upgrades-with-azure-portal"></a>Azure portal ile el ile yükseltmeler

El ile yükseltme seçeneğini belirlediğinizde, bir yükseltmeyi başlatmak için gereken tek şey, kullanılabilir sürümler açılan listesinden seçim yapmak ve sonra *kaydetmeniz* gerekir. Buradan küme yükseltmesi hemen açılır.

[Küme sistem durumu ilkeleri](#custom-policies-for-manual-upgrades) (düğüm durumunun bir birleşimi ve kümede çalışan tüm uygulamaların sistem durumu), yükseltme sırasında öğesine dağıtılır. Küme sistem durumu ilkeleri karşılanmazsa, yükseltme geri alınacaktır.

Geri alma işlemine neden olan sorunları düzelttikten sonra, daha önce yaptığınız adımları izleyerek yükseltmeyi yeniden başlatmanız gerekir.

### <a name="resource-manager-template"></a>Resource Manager şablonu

Kaynak Yöneticisi şablonu kullanarak küme yükseltme modunuzu değiştirmek için,  Microsoft. servicefabric/  `upgradeMode` *kümeler* kaynak tanımının özelliği için otomatik veya el ile ' yi belirtin. El ile yükseltmeler ' i seçerseniz, öğesini `clusterCodeVersion` Şu anda desteklenen bir [doku sürümüne](#query-for-supported-cluster-versions)de ayarlayın.

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="Ekran görüntüsünde, yapıyı yansıtmak için düz metin girintili olan bir şablon gösterilir. ' ClusterCodeVersion ' ve ' upgradeMode ' özellikleri vurgulanmıştır.":::

Şablonun başarıyla dağıtılması sırasında, küme yükseltme modundaki değişiklikler uygulanır. Kümeniz el ile modundaysa, küme yükseltmesi otomatik olarak başlatılır.

[Küme sistem durumu ilkeleri](#custom-policies-for-manual-upgrades) (düğüm durumunun bir birleşimi ve kümede çalışan tüm uygulamaların sistem durumu), yükseltme sırasında öğesine dağıtılır. Küme sistem durumu ilkeleri karşılanmazsa, yükseltme geri alınır.

Geri alma işlemine neden olan sorunları düzelttikten sonra, daha önce yaptığınız adımları izleyerek yükseltmeyi yeniden başlatmanız gerekir.

## <a name="wave-deployment-for-automatic-upgrades"></a>Otomatik yükseltmeler için dalga dağıtımı

Otomatik yükseltme modunda, kümenizi dalga dağıtımı için etkinleştirme seçeneğiniz vardır. Dalga dağıtımı sayesinde test, aşama ve üretim kümelerinizi, üretim kümeleriniz güncelleştirildikten önce gelecek Service Fabric sürümlerini doğrulamak üzere yerleşik ' Bake zamanı ' ile ayırarak sırayla yükseltmek için bir işlem hattı oluşturabilirsiniz.

### <a name="enable-wave-deployment"></a>Dalga dağıtımını etkinleştir

> [!NOTE]
> Dalga dağıtımı `2020-12-01-preview` Için *Microsoft. servicefabric/kümeler* kaynağınız için (veya üzeri) API sürümü gerekir.

Otomatik yükseltme için dalga dağıtımını etkinleştirmek için, ilk olarak kümenizin hangi dalgasının atanacağını saptayın:

* **Dalga 0** ( `Wave0` ): yeni bir Service Fabric derlemesi serbest bırakıldıktan sonra kümeler güncelleştirilir. Test/dev kümelerine yöneliktir.
* **Dalga 1** ( `Wave1` ): yeni bir derleme yayımlandıktan sonra kümeler bir hafta (yedi gün) güncelleştirilir. Üretim öncesi/hazırlama kümeleri için tasarlanmıştır.
* **Wave 2** ( `Wave2` ): yeni bir derleme yayımlandıktan sonra kümeler iki hafta (14 gün) güncelleştirilir. Üretim kümeleri için tasarlanmıştır.

Daha sonra, `upgradeWave` küme kaynak şablonunuza yukarıda listelenen dalga değerlerinden birini kullanarak bir özellik eklemeniz yeterlidir. Küme kaynak API sürümünüzün veya daha sonraki bir sürümü olduğundan emin olun `2020-12-01-preview` .

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

Güncelleştirilmiş şablonu dağıttıktan sonra, bir sonraki yükseltme döneminde ve sonrasında kümeniz belirtilen dalgaya kaydedilir.

Bir küme yükseltmesi başarısız olursa daha fazla yardım için bağlantılarla [e-posta bildirimlerine kaydolabilirsiniz](#register-for-notifications) .

### <a name="register-for-notifications"></a>Bildirimlere kaydolma

Bir küme yükseltmesi başarısız olduğunda bildirimlere kaydolabilirsiniz. Belirtilen e-posta adresinize, yükseltme hatası hakkında daha fazla ayrıntı ve daha fazla yardım için bağlantılar gönderilecek bir e-posta gönderilir.

> [!NOTE]
> Wave dağıtımında kayıt, yükseltme hatalarıyla ilgili bildirim almak için gerekli değildir.

Bildirimlere kaydolmak için, `notifications` küme kaynak şablonunuza bir bölüm ekleyin ve bildirim almak için bir veya daha fazla e-posta adresi (*alıcı*) belirleyin:

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

Güncelleştirilmiş şablonunuzu dağıttıktan sonra, yükseltme hatası bildirimleri için kaydolmuş olursunuz.

## <a name="custom-policies-for-manual-upgrades"></a>El ile yükseltmelere yönelik özel ilkeler

El ile küme yükseltmeleri için özel sistem durumu ilkeleri belirtebilirsiniz. Bu ilkeler, her yeni çalışma zamanı sürümünü seçtiğiniz her seferinde uygulanır ve bu, sistemi kümenizin yükseltmesini başlatma konusunda tetikler. İlkeleri geçersiz kılamazsınız, varsayılanlar kullanılır.

Özel durum ilkelerini belirtebilir veya **yükseltme ilkesi** için *özel* seçenek ' i seçerek Azure Portal içindeki küme kaynağınızın **doku yükseltmeleri** bölümünde geçerli ayarları gözden geçirebilirsiniz.

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="Yükseltme sırasında özel sistem durumu ilkeleri ayarlamak için Azure portal küme kaynağınızın ' yapı yükseltmeleri ' bölümünde ' özel ' yükseltme ilkesi seçeneğini belirleyin.":::

## <a name="query-for-supported-cluster-versions"></a>Desteklenen küme sürümlerini sorgula

[Azure REST API](/rest/api/azure/) 'yi, belirtilen konum ve aboneliğiniz için kullanılabilir olan tüm Service Fabric çalışma zamanı sürümlerini ([clusterversions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) listelemek üzere kullanabilirsiniz.

Desteklenen sürümler ve işletim sistemleriyle ilgili daha fazla ayrıntı için [Service Fabric sürümlerine](service-fabric-versions.md) de başvurabilirsiniz.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
    "name": "4.4.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "4.4.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Linux"
    }
  }
]
}
```

`supportExpiryUtc`Çıktı içinde, belirli bir yayının süresi dolduğunda veya süresi dolmuşsa rapor edilir. En son sürümlerde geçerli bir tarih yoktur, ancak yalnızca sona erme tarihi henüz ayarlanmamışsa, *9999-12-31T23:59:59.9999999* değerine sahip olur.


## <a name="next-steps"></a>Sonraki adımlar

* [Service Fabric yükseltmelerini yönetme](service-fabric-cluster-upgrade-version-azure.md)
* [Service Fabric kümesi ayarlarınızı](service-fabric-cluster-fabric-settings.md) özelleştirin
* [Kümenizi ve dışarı ölçeklendirin](service-fabric-cluster-scale-in-out.md)
* [Uygulama yükseltmeleri](service-fabric-application-upgrade.md) hakkında bilgi edinin


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
