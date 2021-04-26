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
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 32715ad1a01366d7d56e6fa8129151b15c315e1d
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504184"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Konuşma SDK 'sında günlüğü etkinleştirme

Dosyaya günlük kaydı, konuşma SDK 'Sı için isteğe bağlı bir özelliktir. Geliştirme günlüğü sırasında, konuşma SDK 'sının temel bileşenlerinden ek bilgi ve Tanılamalar sağlar. `Speech_LogFilename`Bir konuşma yapılandırma nesnesi üzerindeki özelliği günlük dosyasının konumu ve adı olarak ayarlanarak etkinleştirilebilir. Günlüğe kaydetme, konuşma SDK 'sının yerel kitaplığındaki statik bir sınıf tarafından işlenir. Herhangi bir konuşma SDK 'Sı tanıyıcı veya birleştirici örneği için günlük kaydını etkinleştirebilirsiniz. Aynı işlemdeki tüm örnekler günlük girdilerini aynı günlük dosyasına yazar.

> [!NOTE]
> JavaScript dışında, desteklenen tüm konuşma SDK 'Sı programlama dillerinde konuşma SDK sürümü 1.4.0 bu yana günlüğe kaydetme kullanılabilir.

## <a name="sample"></a>Örnek

Günlük dosyası adı bir yapılandırma nesnesi üzerinde belirtilir. `SpeechConfig`Örnek olarak alma ve adında bir örnek oluşturduğunuzu kabul eden `config` :

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
> Yapılandırma nesnesinden bir oluşturursanız `SpeechSynthesizer` , günlük kaydını etkinleştirmez. Ancak günlüğe kaydetme etkinse, ' dan tanılama da alacaksınız `SpeechSynthesizer` .

## <a name="create-a-log-file-on-different-platforms"></a>Farklı platformlarda günlük dosyası oluşturma

Windows veya Linux için, günlük dosyası kullanıcının yazma iznine sahip olduğu herhangi bir yolda olabilir. Diğer işletim sistemlerindeki dosya sistemi konumlarına yazma izinleri, varsayılan olarak sınırlı veya kısıtlanmış olabilir.

### <a name="universal-windows-platform-uwp"></a>Evrensel Windows Platformu (UWP)

UWP uygulamalarının, uygulama verileri konumlarından birine (yerel, dolaşım veya geçici) günlük dosyaları yerleştirmeleri gerekir. Yerel uygulama klasöründe bir günlük dosyası oluşturulabilir:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

UWP uygulamalarına yönelik dosya erişimi izni hakkında daha fazla bilgi [edinebilirsiniz.](/windows/uwp/files/file-access-permissions)

### <a name="android"></a>Android

Bir günlük dosyasını iç depolamaya, dış depolamaya veya önbellek dizinine kaydedebilirsiniz. Dahili depolamada veya önbellek dizininde oluşturulan dosyalar uygulamaya özeldir. Dış depolamada bir günlük dosyası oluşturmak tercih edilir.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Yukarıdaki kod, bir günlük dosyasını uygulamaya özgü bir dizinin kökündeki dış depolamaya kaydeder. Bir Kullanıcı Dosya Yöneticisi ile dosyaya erişebilir (genellikle içinde `Android/data/ApplicationName/logfile.txt` ). Uygulama kaldırıldığında dosya silinir.

`WRITE_EXTERNAL_STORAGE`Bildirim dosyasında da izin istemeniz gerekir:

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

Oluşturulan bir dosyaya erişmek için, uygulamanın özellik listesine aşağıdaki özellikleri ekleyin `Info.plist` :

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