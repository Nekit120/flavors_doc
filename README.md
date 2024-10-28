# Настройка Flavors в Flutter

Flavors в Flutter позволяют создавать различные версии приложения для разных целей, таких как разработка, тестирование и релиз. Это удобно для управления настройками, API ключами и параметрами сборки для разных окружений. В нашем проекте сейчас три флейвора: 1. dev, 2. stage и 3. prod.

## Что такое Flavors?

Flavors (флейворы) — это возможность создания разных конфигураций одного приложения, которые могут использоваться для различных сред разработки. Каждый флейвор может иметь свои уникальные настройки, API ключи и параметры сборки, что облегчает тестирование и развертывание приложения.

## Настройка Flavors

В данном проекте настроены следующие флейворы:
1. **dev** — окружение для разработки.
2. **stage** — окружение для тестирования.
3. **prod** — окружение для релиза (в данный момент требует доработки).

### Конфигурация для Android

1. Откройте файл `android/app/build.gradle` и добавьте следующие настройки:

    ```groovy
    flavorDimensions "default"

    productFlavors {
        dev {   
            dimension "default" 
            applicationIdSuffix ".dev"
            resValue "string", "app_name", "Dev your app" 
        } 
        stage { 
            dimension "default"
            applicationIdSuffix ".stage" 
            resValue "string", "app_name", "Stage your app" 
        }
        prod { 
            dimension "default" 
            resValue "string", "app_name", "Your app" 
        }
    }
    ```

2. В каталоге `android/app/src` создайте папки `dev`, `stage`, `prod` (если они отсутствуют) для добавления конфигурационных файлов, специфичных для соответствующих окружений.

### Конфигурация для iOS

1. Откройте проект в Xcode, перейдя в `ios/Runner.xcodeproj`.
2. Перейдите к `Runner > Targets > Runner` и добавьте новую схему для `dev`.
3. В разделе Build Settings для `dev` укажите уникальный Bundle Identifier, например, `dss.workspace.dev`.

## Запуск приложения с флейворами

Вы можете запускать ваше приложение с различными флейворами несколькими способами:

- **Через консоль:** Используйте команду для запуска приложения с нужным флейвором. Например, для `dev`:
  
    ```bash
    flutter run --flavor dev -t lib/main.dart
    ```

- **Через конфигурацию запуска в VS Code:**
    1. Откройте панель Run and Debug (Ctrl + Shift + D).
    2. Нажмите на ссылку `create a launch.json file`.
    3. Выберите Flutter.
    4. Добавьте конфигурацию для вашего флейвора:

    ```json
    {
        "name": "Flutter: Dev",
        "request": "launch",
        "type": "dart",
        "flutterMode": "debug",
        "program": "lib/main_dev.dart",
        "args": ["--flavor", "dev"]
    }
    ```

- **Через ручную настройку конфигураций запуска в Android Studio:**
    1. Перейдите в меню `Run > Edit Configurations`.
    2. Нажмите на кнопку `+` и выберите Flutter.
    3. Укажите название конфигурации, выберите файл `main.dart` и добавьте аргумент `--flavor dev`.

## Использование переменных окружения

Переменные окружения позволяют вам хранить конфиденциальные данные, такие как API ключи и другие настройки, отдельно от кода. В Flutter можно использовать `--dart-define` для передачи этих переменных во время сборки. Однако, если у вас есть множество переменных, более удобно загружать их из файла.

### 1. Создание файлов конфигураций

Создайте файлы `.env` для каждого флейвора в вашем проекте. Например:
- `env/dev.env`
- `env/stage.env`
- `env/prod.env`

В каждом из этих файлов укажите ваши переменные окружения. Пример содержимого для файла `prod.env`:

```env
BASE_URL=https://stage.api-dss.prox2.dex-it.ru
API_KEY=your_prod_api_key
APP_NAME=DSS Workspace
```
## 2. Настройка конфигураций в launch.json

Теперь, когда у вас есть файлы с переменными окружения, вы можете настроить `launch.json`, чтобы передавать их в приложение при запуске. Пример конфигурации для флейвора `prod`:

```json
{
    "name": "Flutter: Prod",
    "request": "launch",
    "type": "dart",
    "program": "lib/main_prod.dart",
    "args": [
        "--flavor", "prod",
        "--dart-define-from-file=.env/prod.env"
    ]
}
```
Таким образом, при запуске конфигурации **Flutter: Prod** будут загружены переменные из файла `prod.env`.

## 3. Использование переменных в коде

После того как вы настроили и запустили приложение с помощью переменных окружения, вы можете получить доступ к этим переменным в вашем коде с помощью `const String.fromEnvironment`:

```dart
const String apiUrl = String.fromEnvironment('API_URL', defaultValue: 'https://api.default');
const String apiKey = String.fromEnvironment('API_KEY', defaultValue: 'default_key');
```
