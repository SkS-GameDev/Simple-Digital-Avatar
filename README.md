# Разработка цифрового аватара с ИИ

Цифровой аватар — это 3D-модель, которая похожа на реального человека.
Это не статичная картинка, а имитация реальной жизни.

Некоторые способы создания цифровых аватаров:

* 3D-моделирование и компьютерная графика. Внешность аватара, его мимику и движения прорисовывают дизайнеры и программисты.
* Нейросеть и костюмы, которые захватывают движения и мимику реального актёра. Поверх движений человека накладывается компьютерная графика, которая меняет его внешность. Получается реалистичная 3D-модель с разнообразными эмоциями.

Цифровые аватары могут использоваться практически в любой сфере. Они могут быть консультантами в банках или интернет-магазинах, моделями на виртуальных показах, лекторами образовательных курсов и амбассадорами брендов.

Здесь мы рассмотрим разработку цифрового аватара с использованием ИИ.
Демонстрация работы цифрового аватара: [ссылка](resources/digital-avatar.mov).

## Проектирование цифрового аватара

Проектирование простого цифрового аватара включает в себя следующие этапы:

* 3D-модель - внешность аватара.
* Lip sync - синхронизация движений губ аватара с произносимыми словами.
* Синтез речи - генерация речи аватара.
* Распознавание речи - способность понимать человеческую речь.
* Интеграция с ИИ - генерация ответов на вопросы и управление аватаром.
* Обучение - возможность обучения аватара новым знаниям.

## Разработка цифрового аватара

### Начало работы

Создайте новый проект в Unity. В примере будет демонстрироваться разработка на Unity 6000.

### 3D-модель

3D-модель создаётся в специальных программах для моделирования. Дизайнеры и программисты работают над внешностью аватара, его мимикой и движениями.

Рекомендуется взять готовую 3D-модель с уже настроеными костями, весами и анимациями.

В проекте будет использоваться 3D-модель [Unity-Chan](https://unity-chan.com/download/index.php).

После добавления своё 3D-модели в проект, добавьте её на сцену.

### Lip sync

Lip sync — это синхронизация движений губ аватара с произносимыми словами. Для этого используются специальные программы, которые распознают речь и передают данные в программу для анимации.

Для реализации lip sync рекомендуется использовать библиотеку [uLipSync](https://github.com/hecomi/uLipSync).

![Lip sync](https://raw.githubusercontent.com/wiki/hecomi/uLipSync/Unity-Chan.gif)

#### Установка uLipSync

Добавьте uLipSync в проект:

* Загрузите последнюю версию .unitypackage со [страницы выпуска](https://github.com/hecomi/uLipSync/releases). Также же добавятся примеры использования.
* Добавьте <https://github.com/hecomi/uLipSync.git#upm> в диспетчер пакетов. Без примеров использования.

#### Настройка uLipSync

* После размещения модели аватара добавьте AudioSource компонент к любому игровому объекту, где будет воспроизводиться звук.
* Добавьте uLipSync компонент к тому же GameObject. Выберите uLipSync-Profile из списка и назначьте его слоту для профиля компонента.
* Добавьте uLipSyncBlendShape к корню Unity-chan's SkinnedMeshRenderer. Выберите целевую форму сочетания, MTH_DEF и перейдите в Blend Shapes > Phoneme table - BlendShape и добавьте 7 элементов: A, I, U, E, O, N и -, нажав кнопку + ("-" для шума). Затем выберите форму сочетания, соответствующую каждой фонеме, как показано на следующем рисунке.
* Наконец, чтобы соединить их, в uLipSync компоненте перейдите в Parameters > On Lip Sync Updated (LipSyncInfo) и нажмите +, чтобы добавить событие, затем перетащите игровой объект (или компонент) с uLipSyncBlendShape компонентом, где написано None (Object). Найдите uLipSyncBlendShape в раскрывающемся списке и выберите OnLipSyncUpdate в нем.

Теперь, когда вы запускаете игру, Юнити-тян будет шевелить губами, когда она говорит.

### Распознавание речи

Распознавание речи — это способность понимать человеческую речь. Для этого используются библиотеки, которые преобразуют аудио данные в текст.

В Unity можно использовать библиотеку [whisper.unity](https://github.com/Macoron/whisper.unity/tree/master?tab=readme-ov-file).

#### Установка whisper.unity

* Можно склонировать репозиторий и открыть его как обычный Unity проект. Он поставляется с примерами и крошечными весами многоязычных моделей. <https://github.com/Macoron/whisper.unity>.
* В качестве альтернативы вы можете добавить этот репозиторий в свой проект в виде пакета Unity. Добавьте его по этому URL-адресу git в ваш менеджер пакетов Unity: <https://github.com/Macoron/whisper.unity.git?path=/Packages/com.whisper.unity>.

#### Скачивание модельных весов

Вы можете скачать модельные веса [отсюда](https://huggingface.co/ggerganov/whisper.cpp). Просто положите их в свою StreamingAssets папку.

#### Настройка whisper.unity

* Создайте новый пустой GameObject и добавьте к нему WhisperManager.
* Укажите путь к модельным весам в WhisperManager относительно StreamingAssets папки.
* Поменяйте настройки языка на `ru`.

WhisperManager готов к использованию.

#### Считывание аудио с микрофона

Для считывания аудио с микрофона можно использовать библиотеку [Microphone](https://docs.unity3d.com/ScriptReference/Microphone.html).

```csharp
public interface IAudioRecorder
{
    void StartRecording();
    void StopRecording();
    float[] ReadAudioData();

    void SetMicrophoneIndex(int index);
}
```

Был создан интерфейс `IAudioRecorder`, который содержит методы для работы с микрофоном.
Интерфейс позволит выполнять следующие действия:

* Начать запись аудио. Метод `StartRecording`.
* Остановить запись аудио. Метод `StopRecording`.
* Прочитать аудио данные. Метод `ReadAudioData`.
* Установить индекс микрофона. Метод `SetMicrophoneIndex`.

Реализация интерфейса `IAudioRecorder`, добавим базовые пераметры и методы для изменения микрофона.

```csharp
using UnityEngine;

public class AudioRecorder : IAudioRecorder
{
    private int _microphoneIndex;
    private AudioClip _microphoneClip;
    private int _position1, _position2;

    public void SetMicrophoneIndex(int index)
    {
        _microphoneIndex = index;
    }
}
```

Добавим методы для начала и остановки записи аудио.

При начале записи аудио мы уничтожаем предыдущий аудио клип, если он существует, и начинаем запись нового аудио.

```csharp
public void StartRecording()
{
    if (_microphoneClip != null)
    {
        Object.Destroy(_microphoneClip);
    }

    _microphoneClip = Microphone.Start(Microphone.devices[_microphoneIndex], true, 3500, 16000);
    _position1 = 0;
}
```

При остановке записи аудио мы останавливаем запись и избавляемся от аудио клипа.

```csharp
public void StopRecording()
{
    if (_microphoneClip == null) return;
    
    Microphone.End(Microphone.devices[_microphoneIndex]);

    _microphoneClip = null;

    _position1 = 0;
    _position2 = 0;
}
```

Добавим метод для чтения аудио данных.

Метод `ReadAudioData` считывает аудио данные из аудио клипа и возвращает их. Сначала мы проверяем, что аудио клип существует и запись ведётся. Затем мы считываем позицию аудио клипа и проверяем, что позиция увеличилась. Если позиция увеличилась, то считываем аудио данные из аудио клипа и обновляем позицию.

```csharp
public float[] ReadAudioData()
{
    if (_microphoneClip == null || !Microphone.IsRecording(Microphone.devices[_microphoneIndex])) return null;

    _position2 = Microphone.GetPosition(Microphone.devices[_microphoneIndex]);

    if (_position2 <= _position1) return null;

    var audioData = new float[_position2 - _position1];
    _microphoneClip.GetData(audioData, _position1);

    _position1 = _position2;

    return audioData;
}
```

#### Создание сервис локатора

Сервис локатор — это паттерн проектирования, который позволяет получить доступ к сервисам в любом месте приложения.

Создадим сервис локатор для доступа к `IAudioRecorder` и к другим сервисам в будущем.

```csharp
using System;
using System.Collections.Generic;

public class ServiceLocator
{
    public static ServiceLocator Instance => _instance ??= new ServiceLocator();
    private static ServiceLocator _instance;

    private readonly Dictionary<Type, object> _services = new();

    public void Register<T>(object service) => _services[typeof(T)] = service;

    public T Get<T>() => (T)_services[typeof(T)];

    public void Clear() => _services.Clear();

    public void Unregister<T>() => _services.Remove(typeof(T));

    public bool Contains<T>() => _services.ContainsKey(typeof(T));
}
```

Реализован примитивный сервис локатор, который позволяет регистрировать, получать и удалять сервисы.

#### Создание инсталлера зависимостей

Инсталлер зависимостей — это класс, который является точкой входа для регистрации всех зависимостей.

```csharp
using UnityEngine;

public class DependencyInstaller : MonoBehaviour
{
    private void Awake()
    {
        var serviceLocator = ServiceLocator.Instance;

        var audioRecorder = new AudioRecorder();
        serviceLocator.Register<IAudioRecorder>(audioRecorder);
    }
}
```

Добавьте инсталлер зависимостей на сцену. В дальнейшем в инсталлере будут регистрироваться все зависимости.

#### Создание пользовательского интерфейса с кнопкой управления микрофоном

Создайте новый Canvas и добавьте к нему кнопку. При нажатии на кнопку микрофон будет включаться и выключаться.

Напишите скрипт для кнопки:

```csharp
using System;
using UnityEngine;
using UnityEngine.UI;

[RequireComponent(typeof(Button))]
public class MicrophoneButton : MonoBehaviour
{
    public event Action OnMicrophoneToggle;

    private Button _button;

    private void Awake()
    {
        _button = GetComponent<Button>();
    }

    private void OnEnable()
    {
        _button.onClick.AddListener(ToggleMicrophone);
    }

    private void OnDisable()
    {
        _button.onClick.RemoveListener(ToggleMicrophone);
    }

    private void ToggleMicrophone() =>
        OnMicrophoneToggle?.Invoke();
}
```

Добавьте скрипт к кнопке и подпишитесь на событие `OnMicrophoneToggle`.

```csharp
using UnityEngine;

[RequireComponent(typeof(MicrophoneButton))]
public class MicrophoneButtonHandler : MonoBehaviour
{
    private void Awake()
    {
        var microphoneButton = GetComponent<MicrophoneButton>();
        microphoneButton.OnMicrophoneToggle += OnMicrophoneToggle;
    }

    private void OnMicrophoneToggle()
    {
        var serviceLocator = ServiceLocator.Instance;
        var audioRecorder = serviceLocator.Get<IAudioRecorder>();

        if (Microphone.IsRecording(Microphone.devices[0]))
        {
            audioRecorder.StopRecording();
        }
        else
        {
            audioRecorder.StartRecording();
        }
    }
}
```

Был создан обработчик кнопки микрофона, который при нажатии на кнопку включает или выключает микрофон. При включении микрофона начинается запись аудио, а при выключении микрофона запись останавливается.

#### Сервис для распознавания речи

Напишем интерфейс для сервиса распознавания речи который будет возвращать текст из аудио данных.

```csharp
public interface ISpeechRecognizer
{
    Task<string> GetTextAsync(float[] samples);
}
```

Создадим сервис для распознавания речи с использованием WhisperManager.

```csharp
using System.Threading.Tasks;
using Whisper;

public class SpeechRecognizer : ISpeechRecognizer
{
    private readonly WhisperManager _whisperManager = ServiceLocator.Instance.Get<WhisperManager>();

    public async Task<string> GetTextAsync(float[] samples)
    {
        var whisperResult = await _whisperManager.GetTextAsync(samples, 16000, 1);
        return whisperResult.Result;
    }
}
```

Обновим инсталлер зависимостей для регистрации нового сервиса и добавим WhisperManager.

```csharp
using UnityEngine;
using Whisper;

public class DependencyInstaller : MonoBehaviour
{
    [SerializeField] private WhisperManager whisperManager;
    
    private void Awake()
    {
        var serviceLocator = ServiceLocator.Instance;

        serviceLocator.Register<WhisperManager>(whisperManager);
        
        var audioRecorder = new AudioRecorder();
        serviceLocator.Register<IAudioRecorder>(audioRecorder);
        
        var speechRecognizer = new SpeechRecognizer();
        serviceLocator.Register<ISpeechRecognizer>(speechRecognizer);
    }
}
```

#### Создание пользовательского интерфейса для отображения текста

Создайте новый TextMeshPro и добавьте к нему скрипт для отображения текста.

```csharp
using TMPro;
using UnityEngine;

[RequireComponent(typeof(TMP_Text))]
public class TextDisplay : MonoBehaviour
{
    private TMP_Text _text;

    private void Awake()
    {
        _text = GetComponent<TMP_Text>();
    }

    public void SetText(string text) => _text.text = text;
}
```

Добавьте скрипт к TextMeshPro.

#### Начнём реализацию основной логики

Пора приступить к создадию скрипта для Цифрового Аватара.

Но перед этим, обновим наш сервис для записи аудио, добавив в него событие которое при оконачании записи аудио будет передавать все аудио данные.

```csharp
public interface IAudioRecorder
{
    event Action<float[]> OnAudioDataReceived;

    // Весь остальной код
}

В интерфейсе `IAudioRecorder` добавлено событие `OnAudioDataReceived`, которое передаёт массив аудио данных.

Обновим реализацию интерфейса `IAudioRecorder`, добавив вызов события при окончании записи аудио.

```csharp
public class AudioRecorder : IAudioRecorder
{
    public event Action<float[]> OnAudioDataReceived;

    // Весь остальной код

    public void StopRecording()
    {
        if (_microphoneClip == null) return;

        OnAudioDataReceived?.Invoke(GetAudioData());

        Microphone.End(Microphone.devices[_microphoneIndex]);

        _microphoneClip = null;

        _position1 = 0;
        _position2 = 0;
    }

    // Весь остальной код

    private float[] GetAudioData()
    {
        _position2 = Microphone.GetPosition(Microphone.devices[_microphoneIndex]);

        if (_position2 <= 0) return null;

        var audioData = new float[_position2];
        _microphoneClip.GetData(audioData, 0);
        return audioData;
    }
}
```

Мы добавили событие `OnAudioDataReceived` и вызвали его при окончании записи аудио. Добавили метод `GetAudioData` для получения аудио данных.

Теперь создадим скрипт для Цифрового Аватара.

```csharp
using System.Threading.Tasks;
using UnityEngine;

public class DigitalAvatar : MonoBehaviour
{
    [SerializeField] private TextDisplay textDisplay;

    private IAudioRecorder _audioRecorder;
    private ISpeechRecognizer _speechRecognizer;

    private void Start()
    {
        var serviceLocator = ServiceLocator.Instance;
        _audioRecorder = serviceLocator.Get<IAudioRecorder>();
        _speechRecognizer = serviceLocator.Get<ISpeechRecognizer>();

        _audioRecorder.OnAudioDataReceived += OnAudioDataReceived;
    }

    private async void OnAudioDataReceived(float[] audioData)
    {
        var text = await RecognizeSpeechAsync(audioData);
        textDisplay.SetText(text);
    }

    private async Task<string> RecognizeSpeechAsync(float[] audioData)
    {
        if (audioData == null) return string.Empty;

        return await _speechRecognizer.GetTextAsync(audioData);
    }
}
```

Скрипт для Цифрового Аватара подписывается на событие `OnAudioDataReceived` и при получении аудио данных передаёт их на распознавание речи. Распознанный текст отображается на экране.

Добавьте скрипт на сцену.

### Генерации текста с помощью ИИ

```plaintext
Генерация текста с помощью искусственного интеллекта (ИИ) — это процесс создания текста на основе алгоритмов машинного обучения с помощью нейросетей.

Принцип работы: нейросеть поглощает огромное количество данных, анализирует их и учится на их основе создавать что-то новое. Когда пользователь даёт запрос, нейросеть начинает анализировать эту информацию, выбирает подходящие фразы и создаёт текст, основанный на всём, что она «узнала».
```

Чаще всего генерация текста с помощью ИИ происходит на серверах больших компаний, таких как Google, Microsoft, OpenAI и других. Они предоставляют API для работы с нейросетями, которые могут генерировать текст.

Но языковые модели могут быть использованы и локально. Для этого существуют библиотеки позволяющие использовать предобученные модели для генерации текста.

#### Unity LLM

LLM для Unity обеспечивает плавную интеграцию больших языковых моделей (LLM) в движок Unity engine.
Это позволяет создавать интеллектуальных персонажей, с которыми ваши игроки могут взаимодействовать для получения захватывающего опыта.

LLM для Unity построен на базе библиотеки llama.cpp

##### Краткая сводка об библиотеке

* 💻 Кроссплатформенный! Windows, Linux, macOS и Android
* 🏠 Работает локально без доступа в Интернет. Никакие данные никогда не покидают игру!
* ⚡ Невероятно быстрый вывод на CPU и GPU (Nvidia, AMD, Apple Metal)
* 🤗 Поддерживает все основные модели LLM
* 🔧 Простота настройки, вызов с помощью одной строки кода
* 💰 Бесплатно использовать как в личных, так и в коммерческих целях

🧪 Протестировано на Unity: 2021 LTS, 2022 LTS, 2023

#### Установка Unity LLM

* Установить и импортировать актив из Unity Asset Store - <https://assetstore.unity.com/packages/tools/ai-ml-integration/llm-for-unity-273604> (Примеры использования прилогаются)
* Импортировать с помощью Git - <https://github.com/undreamai/LLMUnity.git> (Без примеров использования)

#### Использовать Unity LLM

Сначала вы настроите LLM для своей игры:

* Создайте пустой GameObject.
* В инспекторе добавьте компонент LLM.
  * Загрузите одну из моделей по умолчанию с помощью Download Model кнопки (~GBs).
  * Или загрузите свою собственную модель .gguf с помощью Load model кнопки.

Затем вы можете настроить каждого из своих персонажей следующим образом:

* Создайте пустой игровой объект для персонажа.
* В инспекторе добавьте компонент LLMCharacter.
* Определите роль вашего искусственного интеллекта в Prompt. Вы можете определить имя искусственного интеллекта (AI Name) и игрока (Player Name).
* (Необязательно) Выберите LLM, созданный выше, в поле LLM, если у вас есть более одного игрового объекта LLM.

Обновим промт у нашего персонажа на русский язык для более понятного взаимодействия.

```plaintext
Беседа между любопытным человеком и помощником искусственного интеллекта. Помощник дает полезные, подробные и вежливые ответы на вопросы человека.
```

Вы также можете настроить LLM и настройки персонажа в соответствии с вашими предпочтениями.

Пример использования:

```csharp
using LLMUnity;

public class GameLogic : MonoBehaviour 
{
    [SerializeField] private LLMCharacter llmCharacter;

    private void Start()
    {
        // ваша игровая логика
        string message = "Привет, бот!";
        _ = llmCharacter.Chat(message, HandleReply);
    }

    private void HandleReply(string reply)
    {
        // сделайте что-нибудь с ответом модели
        Debug.Log(reply);
    }
}
```

Вот и все ✨!

Мы же с вами сначала попробуем использовать стандартную модель, а после возьмём её и до тренируем её на основе своего датасета.

#### Написание сервиса для использовать Unity LLM

Создадим интерфейс для сервиса, который будет использовать Unity LLM. Сервис будет генерировать текст на основе входного сообщения.

В сервисе будет:

* параметр `IsGenerating`, который будет показывать, идёт ли генерация текста.
* метод `StartGeneration`, который будет запускать генерацию текста на основе входного сообщения. Метод возвращает текст.
* метод `StopGeneration`, который останавливает генерацию текста.
* событие `OnMessageReceived`, которое передаёт генерируемый текст.
* событие `OnMessageReceivedCompleted`, которое вызывается при завершении генерации текста.

```csharp
public interface IConversationGeneration
{
    bool IsGenerating { get; }

    event Action<string> OnMessageReceived;
    event Action OnMessageReceivedCompleted;

    Task<string> StartGeneration(string message);
    void StopGeneration();
}
```

Создадим сервис для генерации текста на основе Unity LLM.

Реализуем интерфейс, добавив соответствующие события и параметры. Так же сам класс будет содержать в себе LLMCharacter.

```csharp
public class ConversationGeneration : IConversationGeneration
{
    public event Action<string> OnMessageReceived;
    public event Action OnMessageReceivedCompleted;

    public bool IsGenerating { get; private set; }

    private readonly LLMCharacter _llmCharacter = ServiceLocator.Instance.Get<LLMCharacter>();
}
```

Добавим методы для генерации текста на основе входного сообщения.

* Метод `StartGeneration` запускает генерацию текста на основе входного сообщения.
* Метод возвращает текст. Метод `StopGeneration` останавливает генерацию текста.

```csharp
public async Task<string> StartGeneration(string message)
{
    if (IsGenerating) return null;

    IsGenerating = true;

    return await _llmCharacter.Chat(message, MessageReceived, MessageReceivedComplete);
}

public void StopGeneration()
{
    _llmCharacter.CancelRequests();

    IsGenerating = false;
}
```

Добавим методы для обработки событий.

* Метод `MessageReceived` вызывается при получении сообщения.
* Метод `MessageReceivedComplete` вызывается при завершении генерации текста.

```csharp
private void MessageReceived(string message)
{
    OnMessageReceived?.Invoke(message);
}

private void MessageReceivedComplete()
{
    IsGenerating = false;
    OnMessageReceivedCompleted?.Invoke();
}
```

Добавьте сервис в инсталлер зависимостей.

```csharp
using LLMUnity;
using UnityEngine;
using Whisper;

public class DependencyInstaller : MonoBehaviour
{
    [SerializeField] private WhisperManager whisperManager;
    [SerializeField] private LLMCharacter llmCharacter;
    
    private void Awake()
    {
        var serviceLocator = ServiceLocator.Instance;

        serviceLocator.Register<WhisperManager>(whisperManager);
        serviceLocator.Register<LLMCharacter>(llmCharacter);
        
        var audioRecorder = new AudioRecorder();
        serviceLocator.Register<IAudioRecorder>(audioRecorder);
        
        var speechRecognizer = new SpeechRecognizer();
        serviceLocator.Register<ISpeechRecognizer>(speechRecognizer);
        
        var conversationGeneration = new ConversationGeneration();
        serviceLocator.Register<IConversationGeneration>(conversationGeneration);
    }
}
```

#### Обновим логику Аватара что бы он мог генерировать текст

Обновим скрипт для Цифрового Аватара, добавив в него сервис для генерации текста.

* Добавим сервис для генерации текста в Цифровой Аватар.
* Подпишемся на событие `OnMessageReceived` и отобразим полученный текст.
* Обновим метод `OnAudioDataReceived`, чтобы он передавал текст в модель.

```csharp
public class DigitalAvatar : MonoBehaviour
{
    // Старый код

    private IConversationGeneration _conversationGeneration;

    private void Start()
    {
        var serviceLocator = ServiceLocator.Instance;
        _audioRecorder = serviceLocator.Get<IAudioRecorder>();
        _speechRecognizer = serviceLocator.Get<ISpeechRecognizer>();
        _conversationGeneration = serviceLocator.Get<IConversationGeneration>();

        _audioRecorder.OnAudioDataReceived += OnAudioDataReceived;
        _conversationGeneration.OnMessageReceived += OnMessageReceived;
    }

    private async void OnAudioDataReceived(float[] audioData)
    {
        var text = await RecognizeSpeechAsync(audioData);

        await _conversationGeneration.StartGeneration(text);
    }

    // Старый код

    private void OnMessageReceived(string message)
    {
        textDisplay.SetText(message);
    }
}
```

### Синтез речи

Синтез речи — это генерация речи. Для этого используются специальные программы, которые преобразуют текст в речь.

Будет использоваться библиотека [piper](https://github.com/rhasspy/piper).

piper - Быстрая локальная нейронная система преобразования текста в речь.

#### Установка нужных библиотек для синтеза речи

* [Скачайте espeak-ng.dll](https://github.com/espeak-ng/espeak-ng/releases) и поместите его в папку Plugins/Windows.
  * eSpeak NG - это синтезатор речи с открытым исходным кодом, который поддерживает более ста языков и акцентов.
* [Скачайте piper_phonemize.dll](https://github.com/rhasspy/piper-phonemize/releases) и поместите его в папку Plugins/Windows.
  * piper phonemize - Библиотека C++ для преобразования текста в фонемы для Piper.
* [Скачайте espeak-ng-data](https://github.com/RTU-TVP/espeak-ng-data) и поместите его в папку StreamingAssets.
* Загрузите .onnx файл модели, например ru_RU-irina-medium.onnx и поместите его в папку Resources.
* Установите пакет Sentis в Unity. Пакет Sentis доступен в диспетчере пакетов Unity.
  * Sentis — это библиотека нейронных сетей для Unity. Вы можете использовать Sentis для импорта обученных моделей нейронных сетей в Unity, а затем запускать их в режиме реального времени.

Вы можете найти [больше моделей piper здесь](https://huggingface.co/rhasspy/piper-voices). К каждой модели прилагается карточка модели, описывающая набор обучающих данных и лицензию.

Вам также нужно будет правильно настроить "голос" (код языка) и частоту дискретизации для модели. Это можно найти в json, расположенном рядом с моделью.

#### Написание сервиса для синтеза речи

После установки всех необходимых библиотек, создадим сервис для синтеза речи.

Создадим интерфейс для сервиса, который будет использовать piper для синтеза речи. Сервис будет принимать текст и возвращать аудио данные.

```csharp
public interface ISpeechSynthesis
{
    Task<AudioClip> TextToSpeech(string text);
}
```

Теперь приступим к реализации сервиса.

В конструкторе сервиса мы инициализируем piper и загружаем модель. Передаем модель, используемый язык и частоту дискретизации.

После чего в конструкторе инитиализируем переменные, загрузим модель и создадим экземпляр Worker.

```plaintext
Worker — это механизм логического вывода. Вы создаёте рабочий процесс, чтобы разбить модель на выполнимые задачи, запустить задачи на графическом или центральном процессоре и получить результат.
```

Добавим метод Dispose для освобождения ресурсов.

```csharp
public class SpeechSynthesisWithPiper : ISpeechSynthesis, IDisposable
{
    private const BackendType BACKEND = BackendType.GPUCompute;
    private readonly string _voice;
    private readonly int _sampleRate;

    private readonly Worker _worker;

    public SpeechSynthesisWithPiper(ModelAsset model, string language = "ru", int sampleRate = 22050)
    {
        _voice = language;
        _sampleRate = sampleRate;

        var espeakPath = Path.Combine(Application.streamingAssetsPath, "espeak-ng-data");
        PiperWrapper.InitPiper(espeakPath);

        var runtimeModel = ModelLoader.Load(model);

        _worker = new Worker(runtimeModel, BACKEND);
    }

    public void Dispose()
    {
        PiperWrapper.FreePiper();
        _worker?.Dispose();
    }
```

Создадим метод который в дальнейдем будет преобразовывать числовой формат в аудио формат.

```csharp
private AudioClip CreateAudioClip(List<float> audioBuffer)
{
    var audioClip = AudioClip.Create("piper_tts", audioBuffer.Count, 1, _sampleRate, false);
    audioClip.SetData(audioBuffer.ToArray(), 0);
    return audioClip;
}
```

Добавим метод для синтеза речи.

```csharp
public async Task<AudioClip> TextToSpeech(string text)
{
    var phonemes = PiperWrapper.ProcessText(text, _voice);

    var inputLengthsShape = new TensorShape(1);
    var scalesShape = new TensorShape(3);
    using var scalesTensor = new Tensor<float>(scalesShape, new[] { 0.667f, 1f, 0.8f });

    var audioBuffer = new List<float>();
    foreach (var sentence in phonemes.Sentences)
    {
        var inputPhonemes = sentence.PhonemesIds;
        var inputShape = new TensorShape(1, inputPhonemes.Length);
        using var inputTensor = new Tensor<int>(inputShape, inputPhonemes);
        using var inputLengthsTensor = new Tensor<int>(inputLengthsShape, new[] { inputPhonemes.Length });

        _worker.SetInput("input", inputTensor);
        _worker.SetInput("input_lengths", inputLengthsTensor);
        _worker.SetInput("scales", scalesTensor);

        _worker.Schedule();

        using var outputTensor = _worker.PeekOutput() as Tensor<float>;
        await outputTensor.ReadbackAndCloneAsync();

        var output = outputTensor.AsReadOnlySpan().ToArray();
        audioBuffer.AddRange(output);
    }

    return CreateAudioClip(audioBuffer);
}
```

Добавим созданный сервис в инсталлер зависимостей.

```csharp
using LLMUnity;
using UnityEngine;
using Whisper;

public class DependencyInstaller : MonoBehaviour
{
    [SerializeField] private WhisperManager whisperManager;
    [SerializeField] private LLMCharacter llmCharacter;
    [SerializeField] private ModelAsset model;
    [SerializeField] private string language;
    [SerializeField] private int sampleRate;

    private void Awake()
    {
        var serviceLocator = ServiceLocator.Instance;

        serviceLocator.Register<WhisperManager>(whisperManager);
        serviceLocator.Register<LLMCharacter>(llmCharacter);

        var audioRecorder = new AudioRecorder();
        serviceLocator.Register<IAudioRecorder>(audioRecorder);

        var speechRecognizer = new SpeechRecognizer();
        serviceLocator.Register<ISpeechRecognizer>(speechRecognizer);

        var conversationGeneration = new ConversationGeneration();
        serviceLocator.Register<IConversationGeneration>(conversationGeneration);

        var speechSynthesis = new SpeechSynthesisWithPiper(model, language, sampleRate);
        serviceLocator.Register<ISpeechSynthesis>(speechSynthesis);
    }
}
```

#### Обновим логику Аватара что бы он мог проигрывать аудио

Добавим ссылку на источни вывода звука

```csharp
[SerializeField] private AudioSource audioSource;
```

Добавим новыe сервисы.

```csharp
private IAudioRecorder _audioRecorder;
private ISpeechRecognizer _speechRecognizer;
private IConversationGeneration _conversationGeneration;
private ISpeechSynthesis _speechSynthesis;

private void Start()
{
    var serviceLocator = ServiceLocator.Instance;
    _audioRecorder = serviceLocator.Get<IAudioRecorder>();
    _speechRecognizer = serviceLocator.Get<ISpeechRecognizer>();
    _conversationGeneration = serviceLocator.Get<IConversationGeneration>();
    _speechSynthesis = serviceLocator.Get<ISpeechSynthesis>();

    _audioRecorder.OnAudioDataReceived += OnAudioDataReceived;
    _conversationGeneration.OnMessageReceived += OnMessageReceived;
}
```

Обновим логику получения текста. Сделаем так что бы мы его получали с помощью сервиса генерации текста, а после передавали его на синтез речи.

```csharp
private string _generatedText;

private void OnMessageReceived(string message)
{
    _generatedText = message;
}

private async void OnMessageReceivedCompleted()
{
    Debug.Log($"Получено сообщение: {_generatedText}");

    microphoneButton.SetIsInteractable(true);

    var audioClip = await _speechSynthesis.TextToSpeech(_generatedText);
    audioSource.PlayOneShot(audioClip);
}
```

#### Обновим пользовательский интерфейс

Удалим TextDisplay:

* удалим скрипт TextDisplay.
* удалим объект в иерархии.
* удилим все ссылки на TextDisplay.

Добавим отключение и включение интерактивности кнопки микрофона.

Добавим метод для отключения и включения интерактивности кнопки микрофона.

```csharp
public void SetIsInteractable(bool isInteractable)
{
    _button.interactable = isInteractable;
}
```

Обновим логику Аватара.

Добавим подписку на событие окончания проигрывания аудио.

```csharp
private void Start()
{
    // Старый код
    _playingSounds.OnSoundFinished += OnSoundFinished;
}
```

Добавим ссылку на кнопку микрофона.

```csahrp
[SerializeField] private MicrophoneButton microphoneButton;
```

Обновим метод получения аудио от микрофона.

```csharp
private async void OnAudioDataReceived(float[] audioData)
{
    microphoneButton.SetIsInteractable(false);

    var text = await RecognizeSpeechAsync(audioData);

    await _conversationGeneration.StartGeneration(text);
}
```

Реализуем метод для обработки события окончания проигрывания аудио.

```csharp
private void OnSoundFinished()
{
    microphoneButton.SetIsInteractable(true);
}
```

#### Настройка сцены

К компоненту DigitalAvatar добавьте ссылку на кнопку микрофона.

В DependencyInstaller добавьте ссылки на все необходимые компоненты и присвойте значения параметрам.

#### Запуск приложения

Попробуйте запустить приложение и проверьте, что все работает корректно.

Поздравляю! Вы создали Цифрового Аватара, который может взаимодействовать с пользователем, распознавать речь, генерировать текст и произносить его.

## Авторы

### Автор и разработчик

* xAFK-BOTx, Россия

## Технологии

* **Движок:** Unity 6000.0.29f1
* **Язык программирования:** C#
* **Система контроля версий:** Git
* **Библиотеки:**
  * Whisper (Piper) - библиотека для распознавания и синтеза речи
  * LLMUnity - библиотека для работы с языковыми моделями в Unity типа .gguf
  * Sentis - библиотека нейронных сетей для Unity
  * eSpeak NG - синтезатор речи с открытым исходным кодом
  * piper phonemize - библиотека C++ для преобразования текста в фонемы для Piper