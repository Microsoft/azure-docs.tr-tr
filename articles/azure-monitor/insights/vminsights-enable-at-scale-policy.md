---
title: Azure Ilkesini kullanarak VM'ler için Azure İzleyici etkinleştirme | Microsoft Docs
description: Bu makalede, Azure Ilkesi kullanarak birden çok Azure sanal makinesi veya sanal makine ölçek kümesi için VM'ler için Azure İzleyici nasıl etkinleştirileceği açıklanır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 4a89eb36c9aa7369d6145304b572b4245cef3483
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109352"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Azure Ilkesini kullanarak VM'ler için Azure İzleyici (Önizleme) etkinleştirme

Bu makalede, Azure Ilkesi kullanılarak Azure sanal makineler veya sanal makine ölçek kümeleri için VM'ler için Azure İzleyici (Önizleme) nasıl etkinleştirileceği açıklanmaktadır. Bu işlemin sonunda, Log Analytics ve bağımlılık aracılarını ve uyumlu olmayan tanımlanmış sanal makineleri etkinleştirmeyi başarıyla yapılandırmış olursunuz.

Tüm Azure sanal makinelerinizin veya sanal makine ölçek kümelerinin VM'ler için Azure İzleyici bulmasını, yönetmesini ve etkinleştirmesini sağlamak için Azure Ilkesi veya Azure PowerShell kullanabilirsiniz. Azure Ilkesi, yeni sağlanan VM 'lerin tutarlı uyumluluk ve otomatik olarak etkinleştirilmesini sağlamak üzere aboneliklerinizi etkili bir şekilde yönetmek üzere ilke tanımlarını yönetebilmeniz için önerdiğimiz yöntemdir. Bu ilke tanımları:

* Log Analytics aracısını ve bağımlılık aracısını dağıtın.
* Uyumluluk sonuçları hakkında rapor oluşturun.
* Uyumsuz VM 'Ler için düzeltin.

Bu görevleri Azure PowerShell veya Azure Resource Manager şablonuyla gerçekleştirmeye ilgileniyorsanız, bkz. [Azure PowerShell veya Azure Resource Manager şablonları kullanarak VM'ler için Azure izleyici (Önizleme) etkinleştirme](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Ilke kapsamını yönetme özelliğine genel bakış

Başlangıçta, VM'ler için Azure İzleyici için ilke tanımlarını yönetmeye ve dağıtmaya yönelik Azure Ilkesiyle ilgili deneyim, yalnızca Azure Ilkesinden yapılır. Ilke kapsamını Yönet özelliği, daha önce bahsedilen ilke tanımlarını içeren **etkinleştirme VM'ler için Azure izleyici** girişim ölçeğini daha kolay ve kolay bir şekilde bulmayı, yönetmeyi ve etkinleştirmeyi kolaylaştırır. Bu yeni özelliğe, VM'ler için Azure İzleyici ' deki **Başlarken** sekmesinden erişebilirsiniz. **VM'ler için Azure izleyici ilkesi** kapsamı sayfasını açmak Için **ilke kapsamını Yönet** ' i seçin.

![VM 'lerden Azure Izleyici Başlarken sekmesi](./media/vminsights-enable-at-scale-policy/get-started-page.png)

Buradan, yönetim gruplarınız ve abonelikleriniz genelinde girişim için kapsamı denetleyebilir ve yönetebilirsiniz. Yönetim gruplarının ve aboneliklerinin her birinde kaç tane VM olduğunu ve bunların uyumluluk durumunu anlayabilirsiniz.

![VM'ler için Azure İzleyici Ilke sayfasını yönet](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Bu bilgiler, bir merkezi konumdan VM'ler için Azure İzleyici için idare senaryonuzu planlayıp yürütgetirmenize yardımcı olmak için yararlıdır. Azure Ilkesi, bir bir ilke veya girişim bir kapsama atandığında, bu yeni sayfayla birlikte ilke veya girişim atanmamış ve yerinde atayabileceğiniz bir uyumluluk görünümü sağlar. Atama, görüntüleme ve düzenleme gibi tüm eylemler doğrudan Azure Ilkesine yeniden yönlendirme. **VM'ler için Azure izleyici Ilkesi kapsamı** sayfası, yalnızca girişim **etkinleştirme VM'ler için Azure izleyici**için genişletilmiş ve tümleşik bir deneyimdir.

Bu sayfadan, Log Analytics çalışma alanınızı VM'ler için Azure İzleyici için de yapılandırabilirsiniz, bu:

- Hizmet Eşlemesi çözümünü kurar.
- Performans grafikleri, çalışma kitapları ve özel günlük sorgularınız ve uyarılarınız tarafından kullanılan işletim sistemi performans sayaçlarını sunar.

![VM'ler için Azure İzleyici çalışma alanını Yapılandır](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Bu seçenek, herhangi bir ilke eylemleriyle ilgili değildir. VM'ler için Azure İzleyici etkinleştirmek için gereken [önkoşulları](vminsights-enable-overview.md) yerine getirmek için kolay bir yol sağlamak için kullanılabilir.  

### <a name="what-information-is-available-on-this-page"></a>Bu sayfada hangi bilgiler kullanılabilir?

Aşağıdaki tablo, ilke kapsamı sayfasında sunulan bilgilerin dökümünü ve nasıl yorumlanacağını sağlar.

| İşlev | Açıklama | 
|----------|-------------| 
| **Kapsam** | Yönetim grubu hiyerarşisinde ayrıntıya gidebilme olanağı sunan veya bu erişimi devralınmış olan yönetim grubu ve abonelikler.|
| **Rol** | Kapsam, okuyucu, sahip veya katkıda bulunan olabilir. Bazı durumlarda, abonelik erişimi olduğunu ancak ait olduğu yönetim grubuna değil, aboneliğe erişiminizin olabileceğini göstermek için boş görünebilir. Diğer sütunlardaki bilgiler, rolünüze bağlı olarak değişir. Rol, hangi verileri görebileceğinize ve ilke veya girişim atama, bunları düzenlemeyle veya uyumluluğu görüntüleme açısından gerçekleştirebileceğiniz eylemler konusunda bir anahtardır. |
| **Toplam VM sayısı** | Bu kapsamdaki sanal makine sayısı. Bir yönetim grubu için, abonelikler veya alt yönetim grubu altında iç içe geçmiş sanal makinelerin toplamıdır. |
| **Atama kapsamı** | İlke veya girişim kapsamındaki VM 'lerin yüzdesi. |
| **Atama durumu** | İlke veya girişim atamalarınızın durumu hakkında bilgi. |
| **Uyumlu VM 'Ler** | İlke veya girişim kapsamında uyumlu olan sanal makinelerin sayısı. Girişim **etkinleştirme VM'ler için Azure izleyici**için, bu hem Log Analytics Aracısı hem de bağımlılık aracısına sahip olan sanal makinelerin sayısıdır. Bazı durumlarda, hiçbir atama, VM yok veya yeterli izin olmadığından boş görünebilir. Bilgi, **uyumluluk durumu**altında sunulmaktadır. |
| **Uyumluluk** | Genel uyumluluk numarası, tüm ayrı kaynakların toplamına ayrılan ayrı kaynakların toplamıdır. |
| **Uyumluluk Durumu** | İlkenize veya girişim atamalarınızın uyumluluk durumuyla ilgili bilgiler.|

İlkeyi veya girişimi atadığınızda, atamada seçilen kapsam listelenen kapsam veya bunun bir alt kümesi olabilir. Örneğin, bir yönetim grubu (kapsam kapsamı) değil, abonelik (ilke kapsamı) için bir atama oluşturmuş olabilirsiniz. Bu durumda, **atama kapsamının** değeri, ilke veya girişim kapsamındaki VM 'leri, kapsam kapsamındaki VM 'ler tarafından ayrılmış olarak gösterir. Başka bir durumda, bazı VM 'Leri, kaynak gruplarını veya ilke kapsamından bir aboneliği dışınınız olabilir. Değer boşsa, ilke veya girişim yok ya da izniniz yok anlamına gelir. Bilgi, **atama durumu**altında sağlanır.

## <a name="enable-by-using-azure-policy"></a>Azure İlkesi'ni kullanarak etkinleştirme

Azure İzleyici VM'ler için kiracınızda Azure İlkesi kullanarak etkinleştirmek için:

- Girişimi bir kapsama atayın: yönetim grubu, abonelik veya kaynak grubu.
- Uyumluluk sonuçlarını gözden geçirin ve düzeltin.

Azure ilkesi atama hakkında daha fazla bilgi için bkz. [Azure ilkesine genel bakış](../../governance/policy/overview.md#policy-assignment) ve gözden geçirme [yönetim gruplarına genel bakış](../../governance/management-groups/overview.md) devam etmeden önce.

### <a name="policies-for-azure-vms"></a>Azure VM 'Ler için ilkeler

Bir Azure VM 'nin ilke tanımları aşağıdaki tabloda listelenmiştir.

|Ad |Açıklama |Tür |
|-----|------------|-----|
|\[Önizleme\]: VM'ler için Azure İzleyici'yi etkinleştir |Belirtilen kapsamdaki (Yönetim grubu, abonelik veya kaynak grubu) sanal makineler için Azure Izleyicisini etkinleştirin. Log Analytics çalışma alanı, bir parametre olarak alır. |Girişim |
|\[Preview\]: denetim bağımlılık Aracısı dağıtımı – VM görüntüsü (OS) listelenmemiş |VM görüntüsü (OS) listede tanımlanmamışsa ve aracı yüklü değilse, VM 'Leri uyumsuz olarak raporlar. |İlke |
|\[Preview\]: denetim Log Analytics aracı dağıtımı – VM görüntüsü (OS) listelenmemiş |VM görüntüsü (OS) listede tanımlanmamışsa ve aracı yüklü değilse, VM 'Leri uyumsuz olarak raporlar. |İlke |
|\[Preview\]: Linux VM 'Ler için bağımlılık aracısını dağıtma |VM görüntüsü (OS) listede tanımlanmışsa ve aracı yüklü değilse, Linux VM 'Ler için bağımlılık aracısını dağıtın. |İlke |
|\[Preview\]: Windows VM 'Leri için bağımlılık aracısını dağıtma |VM görüntüsü (OS) listede tanımlanmışsa ve aracı yüklü değilse, Windows VM 'Ler için bağımlılık Aracısı 'nı dağıtın. |İlke |
|\[önizleme\]: Linux sanal makineleri için Log Analytics aracısı dağıtma |VM görüntüsü (OS) listede tanımlanmışsa ve aracı yüklü değilse, Linux VM 'Leri için Log Analytics Aracısı dağıtın. |İlke |
|\[Preview\]: Windows VM 'Leri için Log Analytics aracısı dağıtma |VM görüntüsü (OS) listede tanımlanmışsa ve aracı yüklü değilse, Windows VM 'Leri için Log Analytics Aracısı dağıtın. |İlke |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Azure sanal makine ölçek kümeleri için ilkeler

Bir Azure sanal makine ölçek kümesinin ilke tanımları aşağıdaki tabloda listelenmiştir.

|Ad |Açıklama |Tür |
|-----|------------|-----|
|\[Preview\]: sanal makine ölçek kümeleri için Azure Izleyicisini etkinleştirme |Belirtilen kapsamdaki (Yönetim grubu, abonelik veya kaynak grubu) sanal makine ölçek kümeleri için Azure Izleyicisini etkinleştirin. Log Analytics çalışma alanı, bir parametre olarak alır. Note: ölçek ayarlama yükseltme ilkeniz el Ile olarak ayarlandıysa, uzantıyı üzerinde yükseltme çağırarak küme içindeki tüm VM 'lere uygulayın. CLı 'da bu `az vmss update-instances`. |Girişim |
|\[Preview\]: sanal makine ölçek kümelerinde denetim bağımlılığı Aracısı dağıtımı-VM görüntüsü (OS) listelenmemiş |VM görüntüsü (OS) listede tanımlanmamışsa ve aracı yüklü değilse, sanal makine ölçek kümesini uyumsuz olarak bildirir. |İlke |
|\[Preview\]: sanal makine ölçek kümelerinde denetim Log Analytics Aracısı dağıtımı-VM görüntüsü (OS) listelenmemiş |VM görüntüsü (OS) listede tanımlanmamışsa ve aracı yüklü değilse, sanal makine ölçek kümesini uyumsuz olarak bildirir. |İlke |
|\[Preview\]: Linux sanal makine ölçek kümeleri için bağımlılık aracısı dağıtma |VM görüntüsü (OS) listede tanımlanmışsa ve aracı yüklü değilse, Linux sanal makine ölçek kümeleri için bağımlılık aracısını dağıtın. |İlke |
|\[Preview\]: Windows sanal makine ölçek kümeleri için bağımlılık aracısı dağıtma |VM görüntüsü (OS) listede tanımlanmışsa ve aracı yüklü değilse, Windows sanal makine ölçek kümeleri için bağımlılık aracısını dağıtın. |İlke |
|\[Preview\]: Linux sanal makine ölçek kümeleri için Log Analytics Aracısı dağıtın |VM görüntüsü (OS) listede tanımlanmışsa ve aracı yüklü değilse, Linux sanal makine ölçek kümeleri için Log Analytics Aracısı dağıtın. |İlke |
|\[Preview\]: Windows sanal makine ölçek kümeleri için Log Analytics aracısı dağıtma |VM görüntüsü (OS) listede tanımlanmışsa ve aracı yüklü değilse, Windows sanal makine ölçek kümeleri için Log Analytics Aracısı dağıtın. |İlke |

Tek başına ilke (girişimle yer almaz) aşağıda açıklanmıştır:

|Ad |Açıklama |Tür |
|-----|------------|-----|
|\[Preview\]: VM için denetim Log Analytics çalışma alanı – rapor uyumsuzluğu |İlke veya girişim atamasında belirtilen Log Analytics çalışma alanına oturum açtıklarında VM 'Leri uyumsuz olarak raporla. |İlke |

### <a name="assign-the-azure-monitor-initiative"></a>Azure İzleyici girişim Ata

**VM'ler için Azure izleyici Ilkesi kapsamı** sayfasından ilke atamasını oluşturmak için aşağıdaki adımları izleyin. Bu adımları tamamlamak nasıl anlamak için bkz: [Azure portalından bir ilke ataması oluşturma](../../governance/policy/assign-policy-portal.md).

İlkeyi veya girişimi atadığınızda, atamadaki seçili kapsam burada listelenen kapsam veya bunun bir alt kümesi olabilir. Örneğin, yönetim grubu (kapsam kapsamı) değil, abonelik (ilke kapsamı) için bir atama oluşturmuş olabilirsiniz. Bu durumda, kapsam yüzdesi, ilke veya girişim kapsamındaki VM 'Leri kapsam kapsamındaki VM 'Ler tarafından ayrılmış olarak gösterir. Başka bir durumda, bazı VM 'Leri veya kaynak gruplarını veya ilke kapsamından bir aboneliği dışınınız olabilir. Boşsa, ilke veya girişim yok ya da izniniz yok anlamına gelir. Bilgi, **atama durumu**altında sağlanır.

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Azure portalında **İzleyici**. 

3. **Öngörüler** bölümünde **sanal makineler (Önizleme)** öğesini seçin.
 
4. **Kullanmaya** başlayın sekmesini seçin. Sayfasında, **Ilke kapsamını Yönet**' i seçin.

5. Bir yönetim grubu veya tablodan bir abonelik seçin. Üç nokta (...) simgesini seçerek **kapsam** seçin. Örnekte, bir kapsam, ilke atamasını zorlama için bir sanal makine gruplandırması ile sınırlandırır.

6. **Azure ilke ataması** sayfasında, girişim **Etkinleştir VM'ler için Azure izleyici**önceden doldurulur. 
    **Atama adı** kutusu, girişim adı ile otomatik olarak doldurulur, ancak bunu değiştirebilirsiniz. İsteğe bağlı bir açıklama da ekleyebilirsiniz. **Atanan** kutusu, oturum açan kim temel alınarak otomatik olarak doldurulur. Bu değer isteğe bağlıdır.

7. (İsteğe bağlı) Kapsamdan bir veya daha fazla kaynakları kaldırmak için işaretleyin **dışlamaları**.

8. İçinde **Log Analytics çalışma alanı** bir çalışma alanı açılır listesinde desteklenen bir bölge için seçin.

   > [!NOTE]
   > Çalışma alanı atama kapsamı dışındadır, verme *Log Analytics katkıda bulunan* izinleri ilke atama sorumlusu kimliği Bunu yapmazsanız, erişim izni vermek Için `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` gibi bir dağıtım hatası görebilirsiniz, [yönetilen kimliği el ile yapılandırmayı](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)gözden geçirin.
   > 
   >  Atanmakta olan girişim, *Deployifnotexists* efektli bir ilke Içerdiğinden, **yönetilen kimlik** onay kutusu seçilidir.
    
9. İçinde **yönetme kimlik konumu** aşağı açılan listesinde, uygun bölgeyi seçin.

10. **Ata**'yı seçin.

Atamayı oluşturduktan sonra, **VM'ler için Azure izleyici Ilke kapsamı** sayfası, değişiklikleri yansıtacak şekilde **atama kapsamını**, **atama durumunu**, **uyumlu VM 'leri**ve **uyumluluk durumunu** güncelleştirir. 

Aşağıdaki matris, girişim için olası her uyumluluk durumunu eşler.  

| Uyumluluk durumu | Açıklama | 
|------------------|-------------|
| **Uyumluluk** | Kapsamdaki tüm sanal makinelerin kendisine dağıtılan Log Analytics ve bağımlılık aracıları vardır.|
| **Uyumlu değil** | Kapsamdaki tüm VM 'Lerin bunlara dağıtılan Log Analytics ve bağımlılık aracıları yoktur ve düzeltme gerekebilir.|
| **Başlatılmadı** | Yeni bir atama eklendi. |
| **İne** | Yönetim grubu için yeterli ayrıcalıklara sahip değilsiniz. <sup>1</sup> | 
| **Boş** | Hiçbir ilke atanmadı. | 

<sup>1</sup> yönetim grubuna erişiminiz yoksa, bir sahibe erişim sağlamasını isteyin. Ya da, uyumluluğu görüntüleyin ve atamaları alt yönetim grupları ya da abonelikler aracılığıyla yönetin. 

Aşağıdaki tablo girişim için olası her atama durumunu eşler.

| Atama durumu | Açıklama | 
|------------------|-------------|
| **Başarılı** | Kapsamdaki tüm sanal makinelerin kendisine dağıtılan Log Analytics ve bağımlılık aracıları vardır.|
| **Uyarı** | Abonelik bir yönetim grubu altında değil.|
| **Başlatılmadı** | Yeni bir atama eklendi. |
| **İne** | Yönetim grubu için yeterli ayrıcalıklara sahip değilsiniz. <sup>1</sup> | 
| **Boş** | VM yok veya bir ilke atanmamış. | 
| **Eylem** | Bir ilke atayın veya bir atamayı düzenleyin. | 

<sup>1</sup> yönetim grubuna erişiminiz yoksa, bir sahibe erişim sağlamasını isteyin. Ya da, uyumluluğu görüntüleyin ve atamaları alt yönetim grupları ya da abonelikler aracılığıyla yönetin.

## <a name="review-and-remediate-the-compliance-results"></a>Gözden geçirin ve uyumluluk sonuçlarını Düzelt

Aşağıdaki örnek bir Azure VM içindir, ancak aynı zamanda sanal makine ölçek kümeleri için de geçerlidir. Uyumluluk sonuçlarının nasıl inceleyeceğinizi öğrenmek için bkz. [uyumsuzluk sonuçlarını belirleme](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). **VM'ler için Azure izleyici Ilkesi kapsamı** sayfasında, tablodan bir yönetim grubu veya bir abonelik seçin. Üç nokta (...) simgesini seçerek **uyumluluğu görüntüle** ' yi seçin.   

![Azure Vm'leri için Uyumluluk İlkesi](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

Girişim ile birlikte gelen ilkelerin sonuçlarına bağlı olarak, VM 'Ler aşağıdaki senaryolarda uyumsuz olarak bildirilir:

* Log Analytics Aracısı veya bağımlılık Aracısı dağıtılmadı.  
    Bu senaryo, var olan Vm'leri bir kapsamla tipik bir durumdur. Bunu azaltmak için, uyumsuz bir ilkede [Düzeltme görevleri oluşturarak](../../governance/policy/how-to/remediate-resources.md) gerekli aracıları dağıtın.  
    - \[Preview\]: Linux VM 'Ler için bağımlılık aracısını dağıtma
    - \[Preview\]: Windows VM 'Leri için bağımlılık aracısını dağıtma
    - \[önizleme\]: Linux sanal makineleri için Log Analytics aracısı dağıtma
    - \[Preview\]: Windows VM 'Leri için Log Analytics aracısı dağıtma

* VM görüntüsü (OS), ilke tanımında tanımlı değil.  
    Dağıtım ilkesi ölçütlerini iyi bilinen bir Azure VM görüntülerinden dağıtılan Vm'leri içerir. VM işletim sistemi desteklenip desteklenmediğini görmek için belgelere bakın. Desteklenmiyorsa, güncelleştirme ve dağıtım ilkesi yinelenen veya görüntü uyumlu hale getirmek için değiştirebilirsiniz.  
    - \[Preview\]: denetim bağımlılık Aracısı dağıtımı – VM görüntüsü (OS) listelenmemiş
    - \[Preview\]: denetim Log Analytics aracı dağıtımı – VM görüntüsü (OS) listelenmemiş

* Sanal makineleri belirtilen Log Analytics çalışma alanına oturum değildir.  
    Bazı VM'ler girişim kapsamda bir ilke atamasında belirtilen başka bir Log Analytics çalışma açtığı, mümkündür. Bu ilke, hangi VM 'Lerin uyumsuz bir çalışma alanına rapor olduğunu belirleyen bir araçtır.  
    - \[Preview\]: VM için denetim Log Analytics çalışma alanı – rapor uyumsuzluğu

## <a name="edit-an-initiative-assignment"></a>Girişim atamasını düzenleme

Bir yönetim grubuna veya aboneliğine bir girişim atadıktan sonra, aşağıdaki özellikleri değiştirmek için onu düzenleyebilirsiniz:

- Atama adı
- Açıklama
- Atayan
- Log Analytics çalışma alanı
- Özel durumlar

## <a name="next-steps"></a>Sonraki adımlar

Artık sanal makineleriniz için izleme etkin olduğuna göre, bu bilgiler VM'ler için Azure İzleyici analiz için kullanılabilir. 

- Bulunan Uygulama bağımlılıklarını görüntülemek için bkz: [Vm'leri harita görünümü Azure İzleyici](vminsights-maps.md). 

- VM performanlarınızın performans sorunlarını ve genel kullanımını belirlemek için bkz. [Azure VM performansını görüntüleme](vminsights-performance.md). 
