---
title: Como selecionar um dispositivo de entrada de áudio com o SDK de Discurso
titleSuffix: Azure Cognitive Services
description: Saiba selecionar dispositivos de entrada de áudio no SDK de Fala (C++, C#, Python, Objective-C, Java, JavaScript) obtendo os IDs dos dispositivos de áudio ligados a um sistema.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 9891cdb59c757035afd17339b052d5587ac99b0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74109969"
---
# <a name="how-to-select-an-audio-input-device-with-the-speech-sdk"></a>Como: Selecione um dispositivo de entrada de áudio com o SDK de Fala

A versão 1.3.0 do Speech SDK introduz uma API para selecionar a entrada de áudio. Este artigo descreve como obter as Identificações dos dispositivos de áudio ligados a um sistema. Estes podem então ser utilizados no SDK do `AudioConfig` Discurso configurando o dispositivo de áudio através do objeto:

```C++
audioConfig = AudioConfig.FromMicrophoneInput("<device id>");
```

```cs
audioConfig = AudioConfig.FromMicrophoneInput("<device id>");
```

```Python
audio_config = AudioConfig(device_name="<device id>");
```

```objc
audioConfig = AudioConfiguration.FromMicrophoneInput("<device id>");
```

```Java
audioConfig = AudioConfiguration.fromMicrophoneInput("<device id>");
```

```JavaScript
audioConfig = AudioConfiguration.fromMicrophoneInput("<device id>");
```

> [!Note]
> O uso do microfone não está disponível para o JavaScript em execução no Node.js

## <a name="audio-device-ids-on-windows-for-desktop-applications"></a>IDs de dispositivo sonoro no Windows para aplicações desktop

As [cadeias](/windows/desktop/CoreAudio/endpoint-id-strings) de ID de ponto [`IMMDevice`](/windows/desktop/api/mmdeviceapi/nn-mmdeviceapi-immdevice) final do dispositivo áudio podem ser recuperadas a partir do objeto no Windows para aplicações desktop.

A amostra de código que se segue ilustra como usá-la para enumerar dispositivos áudio em C++:

```cpp
#include <cstdio>
#include <mmdeviceapi.h>

#include <Functiondiscoverykeys_devpkey.h>

const CLSID CLSID_MMDeviceEnumerator = __uuidof(MMDeviceEnumerator);
const IID IID_IMMDeviceEnumerator = __uuidof(IMMDeviceEnumerator);

constexpr auto REFTIMES_PER_SEC = (10000000 * 25);
constexpr auto REFTIMES_PER_MILLISEC = 10000;

#define EXIT_ON_ERROR(hres)  \
              if (FAILED(hres)) { goto Exit; }
#define SAFE_RELEASE(punk)  \
              if ((punk) != NULL)  \
                { (punk)->Release(); (punk) = NULL; }

void ListEndpoints();

int main()
{
    CoInitializeEx(NULL, COINIT_MULTITHREADED);
    ListEndpoints();
}

//-----------------------------------------------------------
// This function enumerates all active (plugged in) audio
// rendering endpoint devices. It prints the friendly name
// and endpoint ID string of each endpoint device.
//-----------------------------------------------------------
void ListEndpoints()
{
    HRESULT hr = S_OK;
    IMMDeviceEnumerator *pEnumerator = NULL;
    IMMDeviceCollection *pCollection = NULL;
    IMMDevice *pEndpoint = NULL;
    IPropertyStore *pProps = NULL;
    LPWSTR pwszID = NULL;

    hr = CoCreateInstance(CLSID_MMDeviceEnumerator, NULL, CLSCTX_ALL, IID_IMMDeviceEnumerator, (void**)&pEnumerator);
    EXIT_ON_ERROR(hr);

    hr = pEnumerator->EnumAudioEndpoints(eCapture, DEVICE_STATE_ACTIVE, &pCollection);
    EXIT_ON_ERROR(hr);

    UINT  count;
    hr = pCollection->GetCount(&count);
    EXIT_ON_ERROR(hr);

    if (count == 0)
    {
        printf("No endpoints found.\n");
    }

    // Each iteration prints the name of an endpoint device.
    PROPVARIANT varName;
    for (ULONG i = 0; i < count; i++)
    {
        // Get pointer to endpoint number i.
        hr = pCollection->Item(i, &pEndpoint);
        EXIT_ON_ERROR(hr);

        // Get the endpoint ID string.
        hr = pEndpoint->GetId(&pwszID);
        EXIT_ON_ERROR(hr);

        hr = pEndpoint->OpenPropertyStore(
            STGM_READ, &pProps);
        EXIT_ON_ERROR(hr);

        // Initialize container for property value.
        PropVariantInit(&varName);

        // Get the endpoint's friendly-name property.
        hr = pProps->GetValue(PKEY_Device_FriendlyName, &varName);
        EXIT_ON_ERROR(hr);

        // Print endpoint friendly name and endpoint ID.
        printf("Endpoint %d: \"%S\" (%S)\n", i, varName.pwszVal, pwszID);
    }

Exit:
    CoTaskMemFree(pwszID);
    pwszID = NULL;
    PropVariantClear(&varName);
    SAFE_RELEASE(pEnumerator);
    SAFE_RELEASE(pCollection);
    SAFE_RELEASE(pEndpoint);
    SAFE_RELEASE(pProps);
}
```

Em C#, a biblioteca [NAudio](https://github.com/naudio/NAudio) pode ser usada para aceder à API CoreAudio e enumerar os dispositivos da seguinte forma:

```cs
using System;

using NAudio.CoreAudioApi;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            var enumerator = new MMDeviceEnumerator();
            foreach (var endpoint in
                     enumerator.EnumerateAudioEndPoints(DataFlow.Capture, DeviceState.Active))
            {
                Console.WriteLine("{0} ({1})", endpoint.FriendlyName, endpoint.ID);
            }
        }
    }
}
```

Um id de `{0.0.1.00000000}.{5f23ab69-6181-4f4a-81a4-45414013aac8}`dispositivo de amostra é .

## <a name="audio-device-ids-on-uwp"></a>IDs de dispositivo de áudio em UWP

Na Plataforma Universal windows (UWP), os dispositivos de `Id()` entrada de [`DeviceInformation`](/uwp/api/windows.devices.enumeration.deviceinformation) áudio podem ser obtidos utilizando a propriedade do objeto correspondente.

As seguintes amostras de código mostram como fazê-lo em C++ e C#:

```cpp
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Devices.Enumeration.h>

using namespace winrt::Windows::Devices::Enumeration;

void enumerateDeviceIds()
{
    auto promise = DeviceInformation::FindAllAsync(DeviceClass::AudioCapture);

    promise.Completed(
        [](winrt::Windows::Foundation::IAsyncOperation<DeviceInformationCollection> const& sender,
           winrt::Windows::Foundation::AsyncStatus /* asyncStatus */ ) {
        auto info = sender.GetResults();
        auto num_devices = info.Size();

        for (const auto &device : info)
        {
            std::wstringstream ss{};
            ss << "looking at device (of " << num_devices << "): " << device.Id().c_str() << "\n";
            OutputDebugString(ss.str().c_str());
        }
    });
}
```

```cs
using Windows.Devices.Enumeration;
using System.Linq;

namespace helloworld {
    private async void EnumerateDevices()
    {
        var devices = await DeviceInformation.FindAllAsync(DeviceClass.AudioCapture);

        foreach (var device in devices)
        {
            Console.WriteLine($"{device.Name}, {device.Id}\n");
        }
    }
}
```

Um id de `\\\\?\\SWD#MMDEVAPI#{0.0.1.00000000}.{5f23ab69-6181-4f4a-81a4-45414013aac8}#{2eef81be-33fa-4800-9670-1cd474972c3f}`dispositivo de amostra é .

## <a name="audio-device-ids-on-linux"></a>IDs de dispositivo de áudio em Linux

Os IDs do dispositivo são selecionados utilizando iDs padrão do dispositivo ALSA.

Os IDs das inputs anexas ao sistema estão `arecord -L`contidos na saída do comando .
Em alternativa, podem ser obtidos através da [biblioteca ALSA C](https://www.alsa-project.org/alsa-doc/alsa-lib/).

As identificações `hw:1,0` da `hw:CARD=CC,DEV=0`amostra são e.

## <a name="audio-device-ids-on-macos"></a>IDs de dispositivo de áudio no macOS

A função seguinte implementada no Objectivo-C cria uma lista dos nomes e iDs dos dispositivos de áudio ligados a um Mac.

A `deviceUID` cadeia é usada para identificar um dispositivo no SDK de fala para macOS.

```objc
#import <Foundation/Foundation.h>
#import <CoreAudio/CoreAudio.h>

CFArrayRef CreateInputDeviceArray()
{
    AudioObjectPropertyAddress propertyAddress = {
        kAudioHardwarePropertyDevices,
        kAudioObjectPropertyScopeGlobal,
        kAudioObjectPropertyElementMaster
    };

    UInt32 dataSize = 0;
    OSStatus status = AudioObjectGetPropertyDataSize(kAudioObjectSystemObject, &propertyAddress, 0, NULL, &dataSize);
    if (kAudioHardwareNoError != status) {
        fprintf(stderr, "AudioObjectGetPropertyDataSize (kAudioHardwarePropertyDevices) failed: %i\n", status);
        return NULL;
    }

    UInt32 deviceCount = (uint32)(dataSize / sizeof(AudioDeviceID));

    AudioDeviceID *audioDevices = (AudioDeviceID *)(malloc(dataSize));
    if (NULL == audioDevices) {
        fputs("Unable to allocate memory", stderr);
        return NULL;
    }

    status = AudioObjectGetPropertyData(kAudioObjectSystemObject, &propertyAddress, 0, NULL, &dataSize, audioDevices);
    if (kAudioHardwareNoError != status) {
        fprintf(stderr, "AudioObjectGetPropertyData (kAudioHardwarePropertyDevices) failed: %i\n", status);
        free(audioDevices);
        audioDevices = NULL;
        return NULL;
    }

    CFMutableArrayRef inputDeviceArray = CFArrayCreateMutable(kCFAllocatorDefault, deviceCount, &kCFTypeArrayCallBacks);
    if (NULL == inputDeviceArray) {
        fputs("CFArrayCreateMutable failed", stderr);
        free(audioDevices);
        audioDevices = NULL;
        return NULL;
    }

    // Iterate through all the devices and determine which are input-capable
    propertyAddress.mScope = kAudioDevicePropertyScopeInput;
    for (UInt32 i = 0; i < deviceCount; ++i) {
        // Query device UID
        CFStringRef deviceUID = NULL;
        dataSize = sizeof(deviceUID);
        propertyAddress.mSelector = kAudioDevicePropertyDeviceUID;
        status = AudioObjectGetPropertyData(audioDevices[i], &propertyAddress, 0, NULL, &dataSize, &deviceUID);
        if (kAudioHardwareNoError != status) {
            fprintf(stderr, "AudioObjectGetPropertyData (kAudioDevicePropertyDeviceUID) failed: %i\n", status);
            continue;
        }

        // Query device name
        CFStringRef deviceName = NULL;
        dataSize = sizeof(deviceName);
        propertyAddress.mSelector = kAudioDevicePropertyDeviceNameCFString;
        status = AudioObjectGetPropertyData(audioDevices[i], &propertyAddress, 0, NULL, &dataSize, &deviceName);
        if (kAudioHardwareNoError != status) {
            fprintf(stderr, "AudioObjectGetPropertyData (kAudioDevicePropertyDeviceNameCFString) failed: %i\n", status);
            continue;
        }

        // Determine if the device is an input device (it is an input device if it has input channels)
        dataSize = 0;
        propertyAddress.mSelector = kAudioDevicePropertyStreamConfiguration;
        status = AudioObjectGetPropertyDataSize(audioDevices[i], &propertyAddress, 0, NULL, &dataSize);
        if (kAudioHardwareNoError != status) {
            fprintf(stderr, "AudioObjectGetPropertyDataSize (kAudioDevicePropertyStreamConfiguration) failed: %i\n", status);
            continue;
        }

        AudioBufferList *bufferList = (AudioBufferList *)(malloc(dataSize));
        if (NULL == bufferList) {
            fputs("Unable to allocate memory", stderr);
            break;
        }

        status = AudioObjectGetPropertyData(audioDevices[i], &propertyAddress, 0, NULL, &dataSize, bufferList);
        if (kAudioHardwareNoError != status || 0 == bufferList->mNumberBuffers) {
            if (kAudioHardwareNoError != status)
                fprintf(stderr, "AudioObjectGetPropertyData (kAudioDevicePropertyStreamConfiguration) failed: %i\n", status);
            free(bufferList);
            bufferList = NULL;
            continue;
        }

        free(bufferList);
        bufferList = NULL;

        // Add a dictionary for this device to the array of input devices
        CFStringRef keys    []  = { CFSTR("deviceUID"),     CFSTR("deviceName")};
        CFStringRef values  []  = { deviceUID,              deviceName};

        CFDictionaryRef deviceDictionary = CFDictionaryCreate(kCFAllocatorDefault,
                                                              (const void **)(keys),
                                                              (const void **)(values),
                                                              2,
                                                              &kCFTypeDictionaryKeyCallBacks,
                                                              &kCFTypeDictionaryValueCallBacks);

        CFArrayAppendValue(inputDeviceArray, deviceDictionary);

        CFRelease(deviceDictionary);
        deviceDictionary = NULL;
    }

    free(audioDevices);
    audioDevices = NULL;

    // Return a non-mutable copy of the array
    CFArrayRef immutableInputDeviceArray = CFArrayCreateCopy(kCFAllocatorDefault, inputDeviceArray);
    CFRelease(inputDeviceArray);
    inputDeviceArray = NULL;

    return immutableInputDeviceArray;
}
```

Por exemplo, o UID para o `BuiltInMicrophoneDevice`microfone incorporado é .

## <a name="audio-device-ids-on-ios"></a>IDs de dispositivo de áudio no iOS

A seleção de dispositivos áudio com o SDK de Discurso não é suportada no iOS. No entanto, as aplicações que utilizam [`AVAudioSession`](https://developer.apple.com/documentation/avfoundation/avaudiosession?language=objc) o SDK podem influenciar o encaminhamento de áudio através do Quadro.

Por exemplo, a instrução

```objc
[[AVAudioSession sharedInstance] setCategory:AVAudioSessionCategoryRecord
    withOptions:AVAudioSessionCategoryOptionAllowBluetooth error:NULL];
```

ativa a utilização de um auricular Bluetooth para uma aplicação ativada pela fala.

## <a name="audio-device-ids-in-javascript"></a>IDs de dispositivo de áudio em JavaScript

No JavaScript, o método [MediaDevices.enumeradoDispositivos()](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/enumerateDevices) pode ser utilizado para enumerar os `fromMicrophone(...)`dispositivos de comunicação e encontrar um ID do dispositivo para passar para .

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as nossas amostras no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)
