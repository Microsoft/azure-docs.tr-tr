---
title: Android 'de konuşma SDK 'Sı ile codec ile sıkıştırılmış ses akışı
titleSuffix: Azure Cognitive Services
description: Android 'de konuşma SDK 'Sı ile Azure konuşma Hizmetleri 'ne sıkıştırılmış ses akışını nasıl sağlayacağınızı öğrenin.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 76a4b010dd54a9cc6224432adf615814520ef4fd
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109909"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>Nasıl yapılır: Android 'de konuşma SDK 'Sı ile codec ile sıkıştırılmış ses girişi kullanma

Konuşma SDK 'sının **sıkıştırılmış ses giriş akışı** API 'si, sıkıştırılmış ses ' i, pullstream veya PushStream kullanarak konuşma hizmetine akışa almanın bir yolunu sağlar.

> [!IMPORTANT]
> [ C++Linux 'ta, C#ve Java için (Ubuntu 16,04, Ubuntu 18,04, de, 9)](how-to-use-codec-compressed-audio-input-streams.md)akış sıkıştırılmış giriş sesi şu anda desteklenmektedir. Ayrıca iOS platformunda Android ve [Amaç-C ' d a](how-to-use-codec-compressed-audio-input-streams-ios.md) Java için de desteklenir.
> Konuşma SDK sürümü 1.7.0 veya üzeri gereklidir.

WAV/PCM için ana hat konuşma belgelerine bakın. WAV/PCM dışında, aşağıdaki codec sıkıştırılmış giriş biçimleri desteklenir:

- MP3
- OPUS/OGG
- FLAC
- WAV w kapsayıcısında
- WAV kapsayıcısında MULAW

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>Android 'de codec ile sıkıştırılmış ses girişi kullanma önkoşulları

Codec sıkıştırılmış ses, [GStreamer](https://gstreamer.freedesktop.org)kullanılarak uygulanır. Lisanslama nedenleriyle, GStreamer ikilileri SDK ile derlenmez. Android için önceden oluşturulmuş ikilileri kullanmanız gerekir. Önceden oluşturulmuş kitaplıkları indirmek için bkz. [Android Için yükleme geliştirme](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c).

`libgstreamer_android.so` gereklidir. GStreamer eklentilerinizin `libgstreamer_android.so`bağlı olduğundan emin olun.

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

Aşağıda bir örnek `Android.mk` ve `Application.mk` dosyası verilmiştir. GStreamer paylaşılan nesnesini oluşturmak için şu adımları izleyin: `libgstreamer_android.so`.

```make
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
LOCAL_LDLIBS := -llog
include $(BUILD_SHARED_LIBRARY)


ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
GSTREAMER_EXTRA_LIBS      := -liconv
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```make
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

Ubuntu 16,04 veya 18,04 ' de aşağıdaki komutu kullanarak `libgstreamer_android.so` oluşturabilirsiniz. Aşağıdaki komut satırları yalnızca [ANDROID NDK b16b](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip) Ile [GStreamer Android sürüm 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) için sınanmıştır.

```sh
# assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

Paylaşılan nesne (libgstreamer_android. so) oluşturulduktan sonra geliştirici, konuşma SDK 'Sı tarafından yüklenebilmeleri için paylaşılan nesneyi Android uygulamasına yerleştirmelidir.

## <a name="example-code-using-codec-compressed-audio-input"></a>Codec ile sıkıştırılmış ses girişini kullanan örnek kod

Konuşma hizmetlerine sıkıştırılmış bir ses biçiminde akış yapmak için `PullAudioInputStream` veya `PushAudioInputStream`oluşturun. Sonra akış sınıfınızın bir örneğinden bir `AudioConfig` oluşturun ve akışın sıkıştırma biçimini belirtin.

`myPullStream` adlı bir giriş akışı sınıfınız olduğunu ve OPUS/OGG 'yi kullandığınızı varsayalım. Kodunuz şöyle görünebilir:

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = AudioStreamFormat.getCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig = AudioConfig.fromStreamInput(myPullStream, audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get()

String text = result.getText();
```

## <a name="next-steps"></a>Sonraki adımlar

- [Konuşma deneme aboneliğinizi alın](https://azure.microsoft.com/try/cognitive-services/)
- [Java 'da konuşmayı tanımayı öğrenin](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
