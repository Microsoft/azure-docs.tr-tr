---
title: Konuşma SDK günlüğü-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'sında (C++, C#, Python, amaç-C, Java) günlüğe kaydetmenin nasıl etkinleştirileceği hakkında bilgi edinin.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 58f6c97ac819947f84735bc0bc4c125b43db58dc
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075794"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Konuşma SDK 'sında günlüğü etkinleştirme

Dosyaya günlük kaydı, konuşma SDK 'Sı için isteğe bağlı bir özelliktir. Geliştirme günlüğü sırasında, konuşma SDK 'sının temel bileşenlerinden ek bilgi ve Tanılamalar sağlar. Konuşma yapılandırma nesnesindeki `Speech_LogFilename` özellik, günlük dosyasının konumu ve adı olarak ayarlanarak etkinleştirilebilir. Bu yapılandırmadan bir tanıyıcı oluşturulduktan sonra günlüğe kaydetme işlemi Global olarak etkinleştirilir ve daha sonra devre dışı bırakılamaz. Çalışan bir günlüğe kaydetme oturumu sırasında bir günlük dosyasının adını değiştiremezsiniz.

> [!NOTE]
> JavaScript dışında, desteklenen tüm konuşma SDK 'Sı programlama dillerinde konuşma SDK sürümü 1.4.0 bu yana günlüğe kaydetme kullanılabilir.

## <a name="sample"></a>Örnek

Günlük dosyası adı bir yapılandırma nesnesi üzerinde belirtilir. `SpeechConfig` örnek olarak alma ve `config`adında bir örnek oluşturduğunuzu kabul etmek için:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Config nesnesinden bir tanıyıcı oluşturabilirsiniz. Bu, tüm tanıyıcılar için günlüğe kaydetmeyi etkinleştirir.

> [!NOTE]
> Yapılandırma nesnesinden bir `SpeechSynthesizer` oluşturursanız, günlüğe kaydetme etkinleştirilmeyecektir. Ancak günlüğe kaydetme etkinleştirilirse, `SpeechSynthesizer`tanılamayı da alacaksınız.

## <a name="create-a-log-file-on-different-platforms"></a>Farklı platformlarda günlük dosyası oluşturma

Windows veya Linux için, günlük dosyası kullanıcının yazma iznine sahip olduğu herhangi bir yolda olabilir. Diğer işletim sistemlerindeki dosya sistemi konumlarına yazma izinleri, varsayılan olarak sınırlı veya kısıtlanmış olabilir.

### <a name="universal-windows-platform-uwp"></a>Evrensel Windows Platformu (UWP)

UWP uygulamalarının, uygulama verileri konumlarından birine (yerel, dolaşım veya geçici) günlük dosyaları yerleştirmeleri gerekir. Yerel uygulama klasöründe bir günlük dosyası oluşturulabilir:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

UWP uygulamalarına yönelik dosya erişimi izni hakkında daha fazla bilgi [edinebilirsiniz.](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)

### <a name="android"></a>Android

Bir günlük dosyasını iç depolamaya, dış depolamaya veya önbellek dizinine kaydedebilirsiniz. Dahili depolamada veya önbellek dizininde oluşturulan dosyalar uygulamaya özeldir. Dış depolamada bir günlük dosyası oluşturmak tercih edilir.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Yukarıdaki kod, bir günlük dosyasını uygulamaya özgü bir dizinin kökündeki dış depolamaya kaydeder. Bir Kullanıcı Dosya Yöneticisi ile dosyaya erişebilir (genellikle `Android/data/ApplicationName/logfile.txt`). Uygulama kaldırıldığında dosya silinir.

Ayrıca, bildirim dosyasında `WRITE_EXTERNAL_STORAGE` izin istemeniz gerekir:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Android uygulamaları için veri ve dosya depolama hakkında daha fazla bilgi [edinebilirsiniz.](https://developer.android.com/guide/topics/data/data-storage.html)

#### <a name="ios"></a>iOS

Yalnızca uygulama korumalı alanının içindeki dizinlere erişilebilir. Dosyalar belgeler, kitaplık ve geçici dizinlerde oluşturulabilir. Belgeler dizinindeki dosyalar, bir kullanıcı tarafından kullanılabilir hale getirilebilir. Aşağıdaki kod parçacığı, uygulama belge dizininde bir günlük dosyası oluşturmayı gösterir:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Oluşturulan bir dosyaya erişmek için aşağıdaki özellikleri uygulamanın `Info.plist` özellik listesine ekleyin:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

İOS dosya sistemi hakkında daha fazla bilgi [edinebilirsiniz.](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da örneklerimizi keşfet](https://aka.ms/csspeech/samples)
