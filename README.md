[README-NetworkPulse.md](https://github.com/user-attachments/files/31123886/README-NetworkPulse.md)
# Network Pulse

Минималистичное Android-приложение для ежесекундного мониторинга:

- ICMP ping до `8.8.8.8`, задержка и потери пакетов;
- уровень сотового сигнала в dBm через `TelephonyManager`;
- тип активного подключения: Wi-Fi, 5G, 4G, 3G или 2G/GSM;
- непрерывные замеры в Foreground Service при погашенном экране;
- два графика с масштабированием, прокруткой и локальной историей.

## Требования

- Android 8.0 (API 26) или новее;
- телефон или планшет с сотовым модемом для графика dBm;
- Android Studio с Android SDK Platform 35;
- JDK 17 (в актуальном Android Studio уже встроен).

## Сборка в Android Studio

1. Распакуйте архив исходников.
2. Запустите Android Studio и выберите **Open**.
3. Откройте папку `NetworkPulse` и дождитесь синхронизации Gradle.
4. Если Android Studio предложит установить Android SDK Platform 35 или Build Tools, согласитесь.
5. Выберите **Build → Build Bundle(s) / APK(s) → Build APK(s)**.
6. APK появится в `app/build/outputs/apk/debug/app-debug.apk`.

Debug APK автоматически подписан и подходит для самостоятельной установки. Для публикации в магазине создайте собственный release-ключ через **Build → Generate Signed Bundle / APK**.

## Сборка из командной строки

Windows PowerShell:

```powershell
$env:JAVA_HOME = "C:\Program Files\Android\Android Studio\jbr"
$env:ANDROID_SDK_ROOT = "$env:LOCALAPPDATA\Android\Sdk"
.\gradlew.bat assembleDebug
```

macOS/Linux:

```bash
export JAVA_HOME="/path/to/jdk-17"
export ANDROID_SDK_ROOT="$HOME/Android/Sdk"
./gradlew assembleDebug
```

Для установки через USB с включённой отладкой:

```powershell
adb install -r .\app\build\outputs\apk\debug\app-debug.apk
```

Либо скопируйте APK на телефон, откройте его файловым менеджером и разрешите установку из этого источника.

## Использование

1. Нажмите **СТАРТ**.
2. Разрешите точную геопозицию и чтение состояния телефона — Android защищает этими разрешениями сведения сотового радиомодуля.
3. На Android 13+ разрешите уведомления, чтобы постоянный индикатор сервиса был виден в шторке.
4. Для просмотра истории проведите пальцем по графику. Для масштаба используйте щипок. Двойное касание возвращает последние 60 секунд.
5. Нажмите **СТОП** в приложении или в уведомлении.

На экране доступно до 6 часов точек, база автоматически очищает данные старше 24 часов. При активном мониторинге приложение удерживает только частичную блокировку процессора: экран и графический процессор остаются выключенными. Это нужно для честного секундного интервала при блокировке экрана, но длительная сессия закономерно расходует больше батареи.

Если оператор или сеть блокирует ICMP до `8.8.8.8`, приложение покажет потерю пакета. Адрес можно изменить в `MonitorContract.PING_TARGET`.

## Структура

- `MainActivity.kt` — экран, разрешения и привязка данных к графикам;
- `MonitorService.kt` — Foreground Service, ICMP ping и Telephony callback;
- `RealtimeChartView.kt` — нативная отрисовка и жесты;
- `MeasurementStore.kt` — локальная SQLite-история;
- `AndroidManifest.xml` — разрешения и объявление special-use Foreground Service.
