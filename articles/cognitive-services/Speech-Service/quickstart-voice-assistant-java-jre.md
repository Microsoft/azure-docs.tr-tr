---
title: 'Hızlı başlangıç: Java için özel ses Yardımcısı (Windows, Linux)-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, bir Java konsol uygulamasında bilişsel hizmetler konuşma SDK 'sını kullanmayı öğreneceksiniz. Doğrudan hat konuşma kanalını kullanmak ve bir ses Yardımcısı deneyimini etkinleştirmek üzere yapılandırılmış daha önce oluşturulmuş bir bot Framework bot 'a istemci uygulamanızı nasıl bağlayabileceğinizi öğreneceksiniz.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: bidishac
ms.openlocfilehash: 54a5be769ace97ffa9a4f5f38a9227d9565abfd1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111700"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-java-preview"></a>Hızlı başlangıç: konuşma SDK 'Sı ile bir ses Yardımcısı oluşturma, Java (Önizleme)

Hızlı başlangıç, [konuşmadan metne](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre), [metinden konuşmaya](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre)ve [konuşma çevirisi](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre)için de kullanılabilir.

Bu makalede, Azure bilişsel [Hizmetler konuşma SDK 'sını](speech-sdk.md)kullanarak bir Java konsol uygulaması oluşturursunuz. Uygulama, doğrudan hat konuşma kanalını kullanmak için yapılandırılmış daha önce yazılmış bir bot 'a bağlanır, bir sesli istek gönderir ve bir sesli yanıt etkinliği (yapılandırıldıysa) döndürür. Uygulama, konuşma SDK Maven paketiyle oluşturulmuştur ve Windows, Ubuntu Linux veya macOS 'ta Java IDE tutulma. 64 bit Java 8 çalışma zamanı ortamında (JRE) çalışır.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç şunları gerektirir:

- İşletim sistemi: Windows (64-bit), Ubuntu Linux 16.04/18.04 (64-bit) veya macOS 10,13 veya üzeri.
- [Tutulma Java IDE](https://www.eclipse.org/downloads/).
- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) veya [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
- Konuşma Hizmetleri için bir Azure abonelik anahtarı. [Ücretsiz olarak bir tane alın](get-started.md) veya [Azure Portal](https://portal.azure.com)oluşturun.
- Bot Framework sürüm 4,2 veya üzeri kullanılarak oluşturulan önceden yapılandırılmış bir bot. Bir ses girişi almak için bot 'ın yeni doğrudan hat konuşma kanalına abone olması gerekir.

  > [!NOTE]
  > Lütfen [sesli Yardımcılar için desteklenen bölgeler listesine](regions.md#voice-assistants) başvurun ve kaynaklarınızın bu bölgelerden birinde dağıtıldığından emin olun.

Ubuntu 16.04/18.04 çalıştırıyorsanız, çakışan küreler başlamadan önce bu bağımlılıkların yüklü olduğundan emin olun:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Windows (64-bit) çalıştırıyorsanız, platformunuz için Microsoft Visual C++ yeniden dağıtılabilir ' i yüklediğinizden emin olun:

- [Visual Studio 2017 C++ Için Microsoft Visual yeniden dağıtılabilir 'i indirin](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>İsteğe bağlı: hızlı başlangıç

Bu hızlı başlangıçta, bir basit istemci uygulamasının konuşma özellikli bot 'a nasıl bağlanacağını açıklayan adım adım açıklanmaktadır. Hemen başlamak istiyorsanız, bu hızlı başlangıçta kullanılan tam, kullanıma hazır kaynak kodu `quickstart` klasörü altındaki [konuşma SDK örneklerinde](https://aka.ms/csspeech/samples) bulunur.

## <a name="create-and-configure-project"></a>Proje oluşturma ve yapılandırma

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Ayrıca, günlüğe kaydetmeyi etkinleştirmek için _Pod. xml_ dosyasını aşağıdaki bağımlılığı içerecek şekilde güncelleştirin:

```xml
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-simple</artifactId>
     <version>1.7.5</version>
 </dependency>
```

## <a name="add-sample-code"></a>Örnek kodu ekleyin

1. Java projenize yeni bir boş sınıf eklemek için **Dosya** > **Yeni** > **Sınıf** seçeneklerini belirleyin.

1. **Yeni Java sınıfı** penceresinde, **paket** alanına _Speechsdk. QuickStart_ yazın ve **ad** alanına _Main_ yazın.

   ![Yeni Java Sınıfı penceresinin ekran görüntüsü](media/sdk/qs-java-jre-06-create-main-java.png)

1. Yeni oluşturulan `Main` sınıfını açın ve `Main.java` dosyasının içeriğini aşağıdaki başlangıç kodu ile değiştirin:

   ```java
   package speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
   import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
   import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;

   import javax.sound.sampled.AudioFormat;
   import javax.sound.sampled.AudioSystem;
   import javax.sound.sampled.DataLine;
   import javax.sound.sampled.SourceDataLine;
   import java.io.InputStream;

   public class Main {
       final Logger log = LoggerFactory.getLogger(Main.class);

       public static void main(String[] args) {
           // New code will go here
       }

       private void playAudioStream(PullAudioOutputStream audio) {
           ActivityAudioStream stream = new ActivityAudioStream(audio);
           final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
           final AudioFormat format = new AudioFormat(
                   AudioFormat.Encoding.PCM_SIGNED,
                   audioFormat.getSamplesPerSecond(),
                   audioFormat.getBitsPerSample(),
                   audioFormat.getChannels(),
                   audioFormat.getFrameSize(),
                   audioFormat.getSamplesPerSecond(),
                   false);
           try {
               int bufferSize = format.getFrameSize();
               final byte[] data = new byte[bufferSize];

               SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
               SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
               line.open(format);

               if (line != null) {
                   line.start();
                   int nBytesRead = 0;
                   while (nBytesRead != -1) {
                       nBytesRead = stream.read(data);
                       if (nBytesRead != -1) {
                           line.write(data, 0, nBytesRead);
                       }
                   }
                   line.drain();
                   line.stop();
                   line.close();
               }
               stream.close();

           } catch (Exception e) {
               e.printStackTrace();
           }
       }

   }
   ```

1. `main` yönteminde, önce `DialogServiceConfig` yapılandırıp `DialogServiceConnector` örnek oluşturmak için kullanırsınız. Bu örnek, bot 'unuzla etkileşim kurmak için doğrudan hat konuşma kanalına bağlanır. Ses girişi kaynağını belirtmek için bir `AudioConfig` örneği de kullanılır. Bu örnekte, varsayılan mikrofon `AudioConfig.fromDefaultMicrophoneInput()`ile kullanılır.

   - `YourSubscriptionKey` dize anahtarını, [Bu Web sitesinden](get-started.md)alabileceğiniz abonelik anahtarınızla değiştirin.
   - Dize `YourServiceRegion`, aboneliğinizle ilişkili [bölge](regions.md) ile değiştirin.

   > [!NOTE]
   > Lütfen [sesli Yardımcılar için desteklenen bölgeler listesine](regions.md#voice-assistants) başvurun ve kaynaklarınızın bu bölgelerden birinde dağıtıldığından emin olun.

   ```java
   final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
   final String region = "YourServiceRegion"; // Your speech subscription service region
   final BotFrameworkConfig botConfig = BotFrameworkConfig.fromSubscription(subscriptionKey, region);

   // Configure audio input from a microphone.
   final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

   // Create a DialogServiceConnector instance.
   final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
   ```

1. Bağlayıcı `DialogServiceConnector`, bot etkinliklerini, konuşma tanıma sonuçlarını ve diğer bilgileri iletmek için çeşitli olaylara bağımlıdır. Daha sonra bu olay dinleyicilerini ekleyin.

   ```java
   // Recognizing will provide the intermediate recognized text while an audio stream is being processed.
   connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // Recognized will provide the final recognized text once audio capture is completed.
   connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // SessionStarted will notify when audio begins flowing to the service for a turn.
   connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
       log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
   });

   // SessionStopped will notify when a turn is complete and it's safe to begin listening again.
   connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
       log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
   });

   // Canceled will be signaled when a turn is aborted or experiences an error condition.
   connector.canceled.addEventListener((o, canceledEventArgs) -> {
       log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
       connector.disconnectAsync();
   });

   // ActivityReceived is the main way your bot will communicate with the client and uses Bot Framework activities.
   connector.activityReceived.addEventListener((o, activityEventArgs) -> {
       final String act = activityEventArgs.getActivity().serialize();
           log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
           if (activityEventArgs.hasAudio()) {
               playAudioStream(activityEventArgs.getAudio());
           }
       });
   ```

1. `connectAsync()` yöntemini çağırarak konuşmayı doğrudan konuşmaya `DialogServiceConnector` bağlayın. Botunuzu test etmek için mikrofonunuzdan ses girişi göndermek için `listenOnceAsync` yöntemini çağırabilirsiniz. Ayrıca, bir özel etkinliği serileştirilmiş bir dize olarak göndermek için `sendActivityAsync` yöntemini de kullanabilirsiniz. Bu özel etkinlikler, bot 'unuzun konuşmada kullandığı ek verileri sağlayabilir.

   ```java
   connector.connectAsync();
   // Start listening.
   System.out.println("Say something ...");
   connector.listenOnceAsync();

   // connector.sendActivityAsync(...)
   ```

1. `Main` dosyadaki değişiklikleri kaydedin.

1. Yanıt oynatmayı desteklemek için, getAudio () API 'sinden, işleme kolaylığı için bir Java InputStream 'e döndürülen Puldefdiooutputstream nesnesini dönüştüren ek bir sınıf ekleyin. Bu `ActivityAudioStream`, doğrudan hat konuşma kanalından gelen ses yanıtını işleyen özel bir sınıftır. Kayıttan yürütmeyi işlemek için gereken ses biçimi bilgilerini getirmek için erişimcileri sağlar. Bunun için **dosya** > **Yeni** > **sınıfı**' nı seçin.

1. **Yeni Java sınıfı** penceresinde, **paket** alanına _speechsdk. QuickStart_ ve **ad** alanına _activityaudiostream_ yazın.

1. Yeni oluşturulan `ActivityAudioStream` sınıfını açın ve aşağıdaki kodla değiştirin:

   ```java
   package com.speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

   import java.io.IOException;
   import java.io.InputStream;

    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second (16 kHz).
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format (16 bits).
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream,
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample, and the # channels.
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer, this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream.
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

   ```

1. `ActivityAudioStream` dosyadaki değişiklikleri kaydedin.

## <a name="build-and-run-the-app"></a>Uygulamayı derleme ve çalıştırma

F11 ' i seçin veya **hata ayıklama** > **Çalıştır** ' ı seçin.
Konsol "bir şeyi söyleyin" iletisini görüntüler.
Bu noktada, bot 'ın anlayabilmesi için Ingilizce bir tümcecik veya cümle konuşun. Konuşmanızı, bot 'unuza göre tanınıp işlendiği doğrudan hat konuşma kanalı aracılığıyla sizin bot 'a iletilir. Yanıt bir etkinlik olarak döndürülür. Bot 'niz bir yanıt olarak konuşma döndürürse, ses `AudioPlayer` sınıfı kullanılarak geri yürütülür.

![Başarılı tanıma sonrası konsol çıktısının ekran görüntüsü](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Sonraki adımlar

Ses dosyasından konuşmayı okuma gibi ek örnekler GitHub ' da bulunabilir.

> [!div class="nextstepaction"]
> [Temel bot oluşturma ve dağıtma](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Ayrıca bkz.

- [Sesli yardımcılar hakkında](voice-assistants.md)
- [Ücretsiz bir konuşma Hizmetleri abonelik anahtarı alın](get-started.md)
- [Özel anahtar sözcükler](speech-devices-sdk-create-kws.md)
- [Doğrudan hat konuşmayı bot 'a bağlama](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [GitHub 'da Java örneklerini keşfet](https://aka.ms/csspeech/samples)
