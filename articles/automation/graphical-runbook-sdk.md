---
title: Azure Otomasyonu Grpahical runbook SDK genel bakış
description: Bu makalede, Azure Otomasyonu grafik Runbook SDK kullanmayı açıklar
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e4229079a1fa50295eef85b42f91bbc1b4a21fc3
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478600"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Azure Otomasyonu grafik runbook SDK'sını kullanma

[Grafik runbook'ları](automation-graphical-authoring-intro.md) runbook'ları temel Windows PowerShell veya PowerShell iş akışı kodu karmaşıklığını yönetmenize yardımcı olur. Microsoft Azure Otomasyonu grafik Yazma SDK, geliştiricilere oluşturma ve Azure Otomasyonu ile kullanmak için grafik runbook'ları düzenleme olanak tanır. Aşağıdaki kod parçacıkları kodunuzdan bir grafik runbook oluşturmaya yönelik temel akışı gösterir.

## <a name="pre-requisites"></a>Ön koşullar

Başlamak için içeri aktarma `Microsoft.Azure.Automation.GraphicalRunbook.Model` projenize paket.

## <a name="create-a-runbook-object-instance"></a>Bir runbook nesne örneği oluşturun

Başvuru `Orchestrator.GraphRunbook.Model` derleme ve bir örneğini oluşturmak `Orchestrator.GraphRunbook.Model.GraphRunbook` sınıfı:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Runbook parametreleri ekleme

Örneği `Orchestrator.GraphRunbook.Model.Parameter` nesnelerini ve bunların runbook'a ekleyin:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Etkinlikleri ve bağlantılar ekleme

Etkinlikleri uygun tür örneği oluşturun ve bunları runbook'a ekleyin:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Etkinlikler, aşağıdaki sınıfları tarafından uygulanır `Orchestrator.GraphRunbook.Model` ad alanı:

|Sınıf  |Etkinlik  |
|---------|---------|
|CommandActivity     | (Cmdlet'i, işlevi, vb.) bir PowerShell komutu çağırır.        |
|InvokeRunbookActivity     | Başka bir runbook'u satır içi çağırır.        |
|JunctionActivity     | Tamamlamak tüm gelen dalları bekler.        |
|WorkflowScriptActivity     | Runbook bağlamında (bağlı olarak runbook türü), PowerShell veya PowerShell iş akışı kod bloğunu yürütür. Bu güçlü bir araçtır, ancak bu aşırı kullanmayın: UI metni; bu komut dosyası bloğu gösterir yürütme altyapısı, sağlanan blok bir siyah kutu olarak değerlendirir ve içeriğini temel sözdizimi denetimi hariç analiz etmek için hiçbir geçme girişiminde bulunulacaktır. Yalnızca tek bir PowerShell komutu çağırmak gerekiyorsa CommandActivity tercih eder.        |

> [!NOTE]
> Kendi etkinliklerinizi sağlanan sınıflarından değil: Azure Otomasyonu runbook'ları özel etkinlik türleriyle kullanmanız mümkün olmayacaktır.

CommandActivity ve InvokeRunbookActivity parametreleri değil doğrudan değerleri değeri tanımlayıcıları sağlanmalıdır. Değer tanımlayıcıları gerçek parametre değerlerini nasıl üretildiğini belirtir. Aşağıdaki değeri tanımlayıcıları şu anda sağlanır:


|Tanımlayıcı  |Tanım  |
|---------|---------|
|ConstantValueDescriptor     | Sabit kodlanmış bir sabit değer ifade eder.        |
|RunbookParameterValueDescriptor     | Ada göre bir runbook parametresine başvuruyor.        |
|ActivityOutputValueDescriptor     | İzin verme "başka bir etkinliği tarafından üretilen verilere abone olmak" bir etkinlik çıkışı, Yukarı Akış etkinlik ifade eder.        |
|AutomationVariableValueDescriptor     | Bir Otomasyon değişken varlığı için ada göre gösterir.         |
|AutomationCredentialValueDescriptor     | Bir Otomasyon sertifikası varlığı için ada göre gösterir.        |
|AutomationConnectionValueDescriptor     | Bir Otomasyon bağlantı varlığı için ada göre gösterir.        |
|PowerShellExpressionValueDescriptor     | Etkinlik çağırmadan önce değerlendirilecek serbest biçimli bir PowerShell ifadesi belirtir.  <br/>Bu güçlü bir araçtır, ancak bu aşırı kullanmayın: kullanıcı Arabirimi Bu ifade; metin olarak gösterilir yürütme altyapısı, sağlanan blok bir siyah kutu olarak değerlendirir ve içeriğini temel sözdizimi denetimi hariç analiz etmek için hiçbir geçme girişiminde bulunulacaktır. Mümkün olduğunda, daha özel değer tanımlayıcıları tercih eder.      |

> [!NOTE]
> Kendi değeri tanımlayıcıları sağlanan sınıflarından değil: Azure Otomasyonu runbook'ları ile özel değer tanımlayıcısı türleri kullanmanız mümkün olmayacaktır.

Etkinlikleri bağlanma bağlantılar oluşturmak ve bunları runbook'a ekleyin:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Runbook bir dosyaya kaydedin.

Kullanım `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` bir runbook bir dize için seri hale getirmek için:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Bu dize bir dosyaya kaydedilebilir **.graphrunbook** uzantı ve bu dosya, Azure Automation'a alınabilir.
Seri hale getirilmiş biçimi gelecekteki sürümlerinde değişebilir `Orchestrator.GraphRunbook.Model.dll`. Geriye dönük uyumluluk etmeyeceğiz: eski bir sürümü ile seri hale getirilmiş herhangi bir runbook `Orchestrator.GraphRunbook.Model.dll` herhangi sürüme seri durumdan çıkarılabiliyorsa. İleriye dönük uyumluluğu garanti edilmez: daha yeni bir sürümü ile seri hale getirilmiş bir runbook'un eski sürümleriyle durumdan çıkarılamaz durumda olmayabilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Otomasyonu grafik runbook'ları hakkında daha fazla bilgi için bkz: [giriş grafik yazma](automation-graphical-authoring-intro.md)

