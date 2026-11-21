# YAAP для OnePlus 12R (aston) — сборка через GitHub Actions

Автоматическая сборка прошивки **YAAP (Yet Another AOSP Project), Android 17**
для OnePlus 12R (кодовое имя `aston`, модели CPH2585/CPH2609/CPH2611,
Snapdragon 8 Gen 2 / Adreno 740, arm64).

> Возможность сборки на этом репозитории: локально AOSP на arm64 не собирается,
> поэтому всё происходит на x86_64 раннере GitHub Actions (`ubuntu-24.04-8core`).

## Состав

| Файл | Назначение |
|---|---|
| `.github/workflows/build.yml` | Workflow сборки |
| `roomservice.xml` | Локальный манифест: деревья устройства/ядра/vendor для aston |
| `patches/yaap_aston.mk` | Продуктовый конфиг YAAP (вместо `lineage_aston.mk`) |

## Источники деревьев

YAAP не имеет официальной поддержки OnePlus 12R, поэтому используются активно
поддерживаемые деревья Android 17:

- **Device/kernel**: crDroid `17.0` (`android_device_oneplus_aston`,
  `android_kernel_oneplus_sm8550[-modules|-devicetrees]`)
- **Vendor (влобы)**: TheMuppets `lineage-24.0` + crDroid GitLab
  (`vendor/oneplus/ir`, `vendor/oneplus/dolby`)
- **Qualcomm HAL**: LineageOS `lineage-24.0` / halogenOS `XOS-17.0`

## Как запустить

1. Настройте **larger runners** для репозитория:
   *Settings → Actions → General → Larger runners → Allow larger runners*
   (раннер `ubuntu-24.04-8core` оплачивается с коэффициентом ×8 минут).
2. Откройте вкладку **Actions → Build YAAP for OnePlus 12R (aston)**.
3. Нажмите **Run workflow** (собирается всегда ветка `seventeen` = Android 17;
   другие ветки не используются, т.к. деревья в `roomservice.xml` — 17.0).
4. После завершения скачайте артефакты из ветки **Artifacts**:
   - `yaap-aston-run-*` — flashable zip;
   - `yaap-aston-images-*` — `recovery.img`, `boot.img`, `init_boot.img`,
     `dtbo.img`, `vbmeta.img`, `vendor_boot.img` (для первичной установки).

Первый запуск: `repo sync` (~1–2 ч) + сборка (~2–3 ч).

## Известные ограничения

- Сборка «с нуля»: crDroid-деревья рассчитаны на их собственную конфигурацию,
  поэтому первый билд может требовать итераций (недостающие модули/зависимости).
  Ошибки видны в логах workflow; чаще всего правка сводится к добавлению
  репозитория в `roomservice.xml`.
- Прошивка требует **разблокированного загрузчика** — это стирает все данные и
  лишает гарантии. Выполняйте на свой риск.
