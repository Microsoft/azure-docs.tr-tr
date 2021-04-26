---
title: ARM şablonu sık sorulan sorular
description: Azure Resource Manager şablonları (ARM şablonları) hakkında sık sorulan sorular (SSS).
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e64253aa79cfaeb2655bb091d038dc7c98b198bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419414"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>ARM şablonları hakkında sık sorulan sorular

Bu makale Azure Resource Manager şablonları (ARM şablonları) hakkında sık sorulan soruları yanıtlar.

## <a name="getting-started"></a>Kullanmaya başlama

* **ARM şablonları nedir ve neden kullanmalıyım?**

  ARM şablonları, Azure 'a dağıtmak istediğiniz şeyi tanımladığınız JSON dosyalarıdır. Şablonlar, Azure için bir kod olarak altyapı çözümü uygulamanıza yardımcı olur. Kuruluşunuz gerekli altyapıyı farklı ortamlara sürekli ve güvenilir bir şekilde dağıtabilir.

  ARM şablonlarının Azure altyapınızı yönetmenize nasıl yardımcı olduğu hakkında daha fazla bilgi edinmek için bkz. [ARM şablonları nelerdir?](overview.md)

* **Nasıl yaparım? şablonlarla çalışmaya başlama**

  ARM şablonlarını yazmayı basitleştirmek için doğru araçlara ihtiyacınız vardır. [Visual Studio Code](https://code.visualstudio.com/) ve [Azure Resource Manager araçları uzantısını](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)yüklemenizi öneririz. Bu araçlara hızlı bir giriş için bkz. [hızlı başlangıç: VISUAL STUDIO Code ARM şablonları oluşturma](quickstart-create-templates-use-visual-studio-code.md).

  ARM şablonları oluşturma hakkında daha fazla bilgi edinmek için, [ARM şablonlarında Başlangıç Öğreticisi serisini](template-tutorial-create-first-template.md)başlatın. Bu öğreticiler, bir ARM şablonu oluşturma sürecinde adım adım sizi ele alır. Şablonun farklı bölümleri ve bunların birlikte nasıl çalıştıkları hakkında bilgi edinirsiniz. Bu içerik [Microsoft Learn modülü](/learn/modules/authoring-arm-templates/)olarak da kullanılabilir.

* **Azure 'a dağıtmak için ARM şablonlarını mı yoksa Terkform mı kullanmalıyım?**

  En iyi şekilde istediğiniz seçeneği kullanın. Her iki hizmet de Azure 'a yönelik dağıtımları otomatikleştirmede size yardımcı olur.

  ARM şablonlarını diğer kod olarak altyapı hizmetleri üzerinde kullanmanın avantajları olduğuna inandık. Bu avantajlar hakkında daha fazla bilgi edinmek için bkz. [ARM şablonlarını neden seçmeliyim?](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>Derleme 2020

* **Sununuzu Microsoft Build 2020 ' de kaçırdım. Sunu görüntülenmek üzere kullanılabilir mi?**

  Evet, lütfen [dilediğiniz zaman izleyin](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions).

* **Derlemede duyurulacak yeni özellikler hakkında nereden daha fazla bilgi edinebilirim?**

  Çalıştık özellikler hakkında genel bilgi edinmek için [Azure danışmanları dağıtımlarımız Yammer grubumuza](https://aka.ms/ARMMeet)katın.

  Yeni şablon dili hakkında bilgi edinmek için, [bildirimlere kaydolun](https://aka.ms/armLangUpdates).

  Şablon özellikleri hakkında bilgi edinmek için bkz. [Azure Resource Manager şablonu özellikleri (Önizleme)](template-specs.md).

## <a name="creating-and-testing-templates"></a>Şablonları oluşturma ve test etme

* **ARM şablonları için en iyi yöntemler hakkında nereden bilgi edinebilirim?**

  Şablonlarınızı nasıl uygulayacağınızı gösteren öneriler için bkz. [ARM şablonu en iyi uygulamaları](template-best-practices.md). Şablon oluşturduktan sonra [ARM test araç setini](https://github.com/azure/arm-ttk)çalıştırın. Şablonunuzun Önerilen uygulamalarla eşleşip eşleşmediğini denetler.

* **Aygıtımı Portal üzerinden ayarladım. Şablonu var olan bir kaynak grubundan almanın bir yolu var mı?**

  Evet, şablonu bir kaynak grubundan [dışarı aktarabilirsiniz](export-template-portal.md) . İçe aktarılmış şablon, şablonlar hakkında bilgi edinmek için iyi bir başlangıç noktasıdır, ancak büyük olasılıkla bunu bir üretim ortamında kullanmadan önce gözden geçirmek isteyeceksiniz.

  Şablonu dışarı aktarırken, şablona eklemek istediğiniz kaynakları seçebilirsiniz.

* **ARM şablonunda bir kaynak grubu oluşturabilir ve kaynakları buna dağıtabilir miyim?**

  Evet, şablonu Azure aboneliğiniz düzeyinde dağıtırken bir şablonda kaynak grubu oluşturabilirsiniz. Kaynak grubu oluşturma ve kaynak dağıtma hakkında bir örnek için bkz. [kaynak grubu ve kaynaklar](deploy-to-subscription.md#resource-groups).

* **ARM şablonunda bir abonelik oluşturabilir miyim?**

  Evet, daha fazla bilgi için bkz. [Azure aboneliklerini en son API 'Lerle program aracılığıyla oluşturma](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md).

* **Uygulamamı dağıtımdan önce nasıl test edebilirim?**

  [ARM test araç takımını](https://github.com/azure/arm-ttk) ve bunları dağıtmadan önce şablonlarınıza ilişkin [durum işlemini](template-deploy-what-if.md) çalıştırmayı öneririz. Test araç seti, şablonunuzun en iyi yöntemleri kullanıp kullanmadığını denetler. Şablonu nasıl uyguladığınızı iyileştirebilecek değişiklikleri tanımladıklarında uyarılar sağlar.

  Ne yapılır işlemi şablonunuzun ortamınızda yaptığı değişiklikleri gösterir. Dağıtılmadan önce istenmeden değişiklikler görebilirsiniz. Ayrıca, ön kontrol doğrulaması sırasında algılayabilecekleri hataları da döndürür. Örneğin, şablonunuz bir sözdizimsel hata içeriyorsa, bu hatayı döndürür. Ayrıca, dağıtılan kaynakların son durumu hakkında belirleyebildiği hataları da döndürür. Örneğin, şablonunuz zaten kullanımda olan bir ada sahip bir depolama hesabı dağıttığında, bu hatayı döndürür.

* **Her kaynak türü için kullanılabilen özelliklerle ilgili bilgileri nereden bulabilirim?**

  VS Code, kaynak özellikleriyle çalışmak için IntelliSense sağlar. Özellikler ve açıklamalar için [şablon başvurusunu](/azure/templates/) da görüntüleyebilirsiniz.

* **Kaynak türünün birden çok örneğini oluşturmem gerekiyor. Şablonum içinde bir yineleyici mi oluşturulsun Nasıl yaparım??**

  Birden fazla örnek belirtmek için Copy öğesini kullanın. [Kaynakları](copy-resources.md), [özellikleri](copy-properties.md), [değişkenleri](copy-variables.md)ve [çıkışları](copy-outputs.md)kopyalamak için kullanabilirsiniz.

## <a name="template-language"></a>Şablon dili

* **Yeni bir şablon dili üzerinde çalıştığınızı duydum. Bunun hakkında daha fazla bilgi edinebilirim?**

  Yeni dil hakkında bilgi edinmek için bkz. [Bıcep nedir? nedir?](bicep-overview.md).

* **YAML 'de şablon oluşturmayı desteklemek için bir plan var mı?**

  Şu anda YAML 'yi desteklemeye yönelik bir plan yoktur. Yeni şablon dilinin, YAML veya JSON 'dan daha kolay bir çözüm sunacak olduğunu düşünüyoruz.

* **Yeni şablon dili yayımlandıktan sonra yine de JSON 'daki şablonlar yazabilir miyim?**

  Evet, JSON şablonlarını kullanmaya devam edebilirsiniz.

* **JSON şablonlarımı yeni şablon diline dönüştürecek bir araç sunacak misiniz?**

  Evet. Bkz. [ARM ŞABLONLARıNı JSON Ile Bıcep arasında dönüştürme](bicep-decompile.md).

## <a name="template-specs"></a>Şablon Belirtimleri

* **Şablon özelliklerinin önizleme sürümüne nasıl başlamalıyım?**

  PowerShell veya Azure CLı 'nin en son sürümünü yükler. Azure PowerShell için, [5.0.0 veya üzeri sürümünü](/powershell/azure/install-az-ps)kullanın. Azure CLı için [sürüm 2.14.2 veya üstünü](/cli/azure/install-azure-cli)kullanın.

* **Şablon özellikleri ve Azure şemaları ile ilgili nasıl yapılır?**

  Azure şemaları, kaynağı bir kaynakla değiştirerek uygulamasındaki şablon özelliklerini kullanır `blueprint definition` `template spec` . Şema tanımını bir şablon belirtimine dönüştürmek için bir geçiş yolu sağlıyoruz, ancak şema tanımı API 'Leri yine de desteklenecektir. Kaynakta değişiklik yok `blueprint assignment` . Planlar, Azure 'da yönetilen bir ortam oluşturmak için Kullanıcı deneyimine sahip olmaya devam edecektir.

* **Şablon özellikleri bağlı şablonlar ile değiştirilsin mi?**

  Hayır, ancak şablon özellikleri, bağlantılı şablonlarla iyi çalışacak şekilde tasarlanmıştır. Ana şablonu dağıtabilmeniz için bağlantılı şablonu genel olarak erişilebilen bir uç noktaya taşımanız gerekmez. Bunun yerine, şablon belirtimini oluştururken üst şablonu ve yapıtları birlikte paketleyin.

* **Şablon Özellikleri, abonelikler arasında paylaşılabilir mi?**

  Evet, Kullanıcı şablon belirtimine okuma erişimi olduğu sürece abonelikler arasında kullanılabilirler. Şablon Özellikleri kiracılar arasında kullanılamaz.

## <a name="scripts-in-templates"></a>Şablonlardaki betikler

* **Şablonda mümkün olmayan görevleri yapmak için şablonumu bir komut dosyası ekleyebilir miyim?**

  Evet, [dağıtım betikleri](deployment-script-template.md)kullanın. Şablonlarınıza Azure PowerShell veya Azure CLı betikleri dahil edebilirsiniz. Özellik önizleme aşamasındadır.

* **Özel Betik uzantıları ve istenen durum yapılandırması (DSC) kullanmaya devam edebilir miyim?**

  Bu seçenekler hala kullanılabilir ve değiştirilmez. Dağıtım betikleri, VM konuğılile ilgili olmayan eylemleri gerçekleştirecek şekilde tasarlanmıştır. Bir VM 'de konak işletim sisteminde bir betik çalıştırmanız gerekiyorsa, Özel Betik uzantısı ve/veya DSC daha iyi bir seçenek olacaktır. Ancak, dağıtım betiklerinin zaman aşımı süresini ayarlama gibi avantajları vardır.

* **Dağıtım betikleri Azure Kamu 'da destekleniyor mu?**

  Evet, US Gov Arizona ve US Gov Virginia dağıtım betikleri kullanabilirsiniz.

## <a name="preview-changes-before-deployment"></a>Dağıtımdan önce değişiklikleri Önizle

* **Bir şablon dağıtılmadan önce gerçekleştirilecek değişiklikleri önizleyebilir miyim?**

  Evet, [durum özelliğini](template-deploy-what-if.md)kullanın. Ortamınızın geçerli durumunu değerlendirir ve dağıtımdan sonra var olacak durumla karşılaştırır. Şablonda beklenmedik sonuçlara sahip olmadığından emin olmak için özetlenen değişiklikleri inceleyebilirsiniz.

* **Hem artımlı hem de tam modlarla ne olduğunu kullanabilir miyim?**

  Evet, her iki [dağıtım modu](deployment-modes.md) desteklenir. Artımlı mod kullanmanın bir örneği için bkz. [Run a-if işlemi](template-deploy-what-if.md#run-what-if-operation). Tüm modu kullanma hakkında bir örnek için bkz. [silmeyi onaylayın](template-deploy-what-if.md#confirm-deletion).

* **Bağlantılı şablonlarla ne tür işler çalışıyor?**

  Evet, ne yapılır-ana şablonun ve bağlı şablonlarının durumunu değerlendirir.

* **Bir Azure ardışık düzeninde ne olduğunu kullanabilir miyim?**

  Evet, işlem hattının devam etmesi gerektiğini doğrulamak için ne yapılacağını kullanabilirsiniz.

* **Ne olursa kullanın, Şablonum içinde olmayan özelliklerde değişiklik görüyorum. Bu "gürültü" beklensin mi?**

  Paraziti azaltmak için çalışıyoruz. GitHub deponuzdaki sorunları buraya göndererek iyileştirmemize yardımcı olabilirsiniz: https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>Şablon görselleştiricisi

* **ARM şablonumu ve kaynaklarını görselleştirmem için bir yol var mı?**

  ARM şablonunuzu görselleştirmenin harika bir işini yapan, [topluluk tarafından katkıda bulunulan vs Code uzantısı](https://aka.ms/ARMVisualizer) sunuyoruz. Dağıttığınız kaynakları ve bunlar arasındaki ilişkileri gösterir.

* **Şablon görselleştiricisi VS Code dışında kullanılabilir mi?**

  Şablon görselleştiricisi portalda önizlenmekte. Daha fazla bilgi için, [derlemeden bu kısa oturumu](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions)izleyin.

## <a name="deployment-limits"></a>Dağıtım limitleri

* **Tek bir dağıtım işleminde kaç tane kaynak grubu dağıtabilirim?**

  Geçmişte, bu sınır beş kaynak gruplarıdır. Kısa süre önce 800 kaynak grubu artmıştır. Daha fazla bilgi için bkz. [abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](deploy-to-subscription.md).

* **Dağıtım geçmişinde 800 dağıtımlarla sınırlı olmamak üzere bir hata aldım. Ne yapmam gerekir?**

  Bir kaynak grubu için dağıtım geçmişinin nasıl tutuluyorsa değiştiriyorsunuz. Geçmişte bu hatadan kaçınmak için bu geçmişten dağıtımları el ile silmeniz gerekiyordu. 2020 Haziran 'dan başlayarak, sınıra yaklaşmaya yönelik olarak, geçmişten dağıtımları otomatik olarak sileceğiz. Daha fazla bilgi için bkz. [dağıtım geçmişinden otomatik silme işlemleri](deployment-history-deletions.md).

  Bir dağıtımı geçmişten silmek, dağıtılan kaynakları etkilemez.

## <a name="templates-and-devops"></a>Şablonlar ve DevOps

* **ARM şablonlarını Azure Pipelines ile tümleştirebilir miyim?**

  Evet. Şablon ve işlem hatlarını kullanma hakkında bir açıklama için bkz. [öğretici: ARM şablonlarının Azure Pipelines Ile sürekli tümleştirilmesi](deployment-tutorial-pipeline.md) ve [Azure Pipelines ARM şablonlarını tümleştirme](add-template-to-azure-pipelines.md).

* **Şablon dağıtmak için GitHub eylemlerini kullanabilir miyim?**

  Evet, bkz. [GitHub eylemlerini kullanarak ARM şablonları dağıtma](deploy-github-actions.md).

## <a name="next-steps"></a>Sonraki adımlar

ARM şablonlarına giriş için bkz. [ARM şablonları nedir?](overview.md).
