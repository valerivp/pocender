# POCENDER --- формализованное описание пользовательского интерфейса

**Источник:** `POCENDER STANDALONE CNC CONTOL SYSTEM MANUAL`, V1.1, 1
Aug 2023.\
**Область:** программная часть интерфейса; аппаратная часть исключена.

## 0. Правила описания

### 0.1. Геометрия сенсорного интерфейса

Базовая геометрическая модель:

``` yaml
screen:
  width: 100%
  rows: 10
```

-   `x` и `w` --- целые проценты ширины экрана.
-   `y` и `h` --- целые номера строк.
-   Дробные `y` и `h` запрещены.
-   Если элемент визуально занимает дробную высоту, используется
    ближайшая целая строка.
-   Базовый экран интерфейса имеет 10 вертикальных строк.
-   На большинстве рабочих экранов строка `9` занята нижней навигацией.
-   На специализированных полноэкранных таблицах строка `9` может быть
    строкой команд.

Геометрия является реконструкцией по изображениям руководства; если
точное значение не следует из изображения, используется округлённое
значение.

### 0.2. Формат элемента

``` yaml
- id: unique_id
  type: Button
  text: "..."
  rect: {x: 0, y: 0, w: 10, h: 1}
  state: ...
  action: ...
  manual_comment: "Комментарий/описание из руководства"
  source: "p. NN"
```

`manual_comment` --- отдельное свойство элемента. Если руководство не
описывает элемент, свойство имеет значение `null`.

### 0.3. Типы элементов

``` yaml
Button
Toggle
Input
Display
ClickableDisplay
Select
Slider
CoordinateTable
ProgramEditor
Keyboard
IconMenu
EditTable
OperationPanel
Navigation
Status
```

### 0.4. Общие состояния

``` yaml
normal
pressed
active
disabled
locked
alarm
paused
running
```

------------------------------------------------------------------------

# 1. Общие компоненты

## 1.1. Нижняя навигация

Присутствует на большинстве основных сенсорных экранов.

``` yaml
BOTTOM_NAV:
  y: 9
  h: 1
  items:
    - {text: "MANUAL",  x: 0,  w: 20, action: manual}
    - {text: "CODING",  x: 20, w: 20, action: coding}
    - {text: "POCENDER",x: 40, w: 20, action: null}
    - {text: "SETTING", x: 60, w: 20, action: setting}
    - {text: "RUN",     x: 80, w: 20, action: run}
```

`POCENDER` --- центральный брендовый элемент, а не обычная команда
навигации.

## 1.2. Координатный дисплей

Общий компонент для JOG/Probe/Offsets и некоторых экранов выполнения.

``` yaml
COORDINATE_DISPLAY:
  type: CoordinateTable
  axes: [X, Y, Z, A, B, C]
  columns: [MPOS, WPOS]
  interactive: true
```

Свойства: - `MPOS` --- машинные координаты G53. - `WPOS` --- рабочие
координаты G54--G59. - Клик по координатной области может отменять
активный шаг/позиционирование и используется как разблокировка некоторых
операций. - При срабатывании limit/home цвет WPOS меняется на зелёный.

------------------------------------------------------------------------

# 2. JOG

**Источник:** страницы 14--16.

``` yaml
SCREEN:
  id: jog
  title: JOG
  rows: 10
  template: coordinate
```

## 2.1. Геометрия

``` yaml
coordinate_area:
  x: 0
  y: 0
  w: 48
  h: 7

axis_controls:
  x: 48
  y: 1
  w: 40
  h: 6

mode_panel:
  x: 88
  y: 0
  w: 12
  h: 8

status:
  x: 0
  y: 8
  w: 100
  h: 1

bottom_nav:
  y: 9
  h: 1
```

## 2.2. Элементы

``` yaml
- id: jog.rtcp
  type: Toggle
  text: "RTCP"
  rect: {x: 48, y: 0, w: 12, h: 1}
  action: toggle_rtcp
  manual_comment: "Включает MPG RTCP для текущего инструмента. Выход — повторным нажатием, кликом по координатам или idle/status area."

- id: jog.mpos
  type: Display
  text: "MPOS"
  rect: {x: 5, y: 0, w: 20, h: 1}
  manual_comment: "Машинная система координат G53. Автоматически очищается после поиска нуля; вручную не устанавливается."

- id: jog.wpos
  type: Display
  text: "WPOS"
  rect: {x: 25, y: 0, w: 20, h: 1}
  manual_comment: "Рабочие координаты G54–G59. Могут быть обнулены, установлены и возвращены к нулю."

- id: jog.axis_value.X
  type: ClickableDisplay
  text: "X"
  rect: {x: 0, y: 1, w: 48, h: 1}
  action: edit_axis_coordinate(X)
  manual_comment: "Нажатие открывает ввод координаты выбранной оси; буква оси удаляется с клавиатуры. После OK обновляется координата G54–G59."

- id: jog.axis_value.Y
  type: ClickableDisplay
  text: "Y"
  rect: {x: 0, y: 2, w: 48, h: 1}
  action: edit_axis_coordinate(Y)
  manual_comment: "Аналогично X."

- id: jog.axis_value.Z
  type: ClickableDisplay
  text: "Z"
  rect: {x: 0, y: 3, w: 48, h: 1}
  action: edit_axis_coordinate(Z)
  manual_comment: "Аналогично X."

- id: jog.axis_value.A
  type: ClickableDisplay
  text: "A"
  rect: {x: 0, y: 4, w: 48, h: 1}
  action: edit_axis_coordinate(A)
  manual_comment: "Аналогично X."

- id: jog.axis_value.B
  type: ClickableDisplay
  text: "B"
  rect: {x: 0, y: 5, w: 48, h: 1}
  action: edit_axis_coordinate(B)
  manual_comment: "Аналогично X."

- id: jog.axis_value.C
  type: ClickableDisplay
  text: "C"
  rect: {x: 0, y: 6, w: 48, h: 1}
  action: edit_axis_coordinate(C)
  manual_comment: "Аналогично X."

- id: jog.set0
  type: Button
  text: "Set0"
  rect: {x: 48, y: 1, w: 10, h: 1}
  action: zero_work_coordinate
  manual_comment: "Сбрасывает текущую рабочую координату G54–G59 выбранной оси в ноль."

- id: jog.zero
  type: Button
  text: "Return0"
  rect: {x: 58, y: 1, w: 10, h: 1}
  action: return_work_zero
  manual_comment: "Быстро возвращает текущую ось в нулевую точку рабочей системы координат."

- id: jog.axis_positive
  type: Button
  text: "X+/Y+/..."
  rect: {x: 68, y: 1, w: 10, h: 6}
  action: jog_positive
  manual_comment: "Короткое нажатие выполняет один шаг; удержание обеспечивает непрерывное движение в положительном направлении."

- id: jog.axis_negative
  type: Button
  text: "X-/Y-/..."
  rect: {x: 78, y: 1, w: 10, h: 6}
  action: jog_negative
  manual_comment: "Короткое нажатие выполняет один шаг; удержание обеспечивает непрерывное движение в отрицательном направлении."

- id: jog.probe
  type: Button
  text: "Probe"
  rect: {x: 88, y: 0, w: 12, h: 1}
  action: open(probe)
  manual_comment: "Открывает страницу Probe. Текст становится зелёным при активном сигнале пробника."

- id: jog.offsets
  type: Button
  text: "Offsets"
  rect: {x: 88, y: 1, w: 12, h: 1}
  action: open(offsets)
  manual_comment: "Открывает страницу Offsets."

- id: jog.jogging
  type: Toggle
  text: "Jogging"
  rect: {x: 88, y: 2, w: 12, h: 1}
  action: cycle([Jogging, Step Motion, Positioning])
  manual_comment: "Переключает режим Jogging / Step Motion / Positioning."

- id: jog.g54
  type: Button
  text: "G54"
  rect: {x: 88, y: 3, w: 12, h: 1}
  action: cycle_wcs(G54..G59)
  manual_comment: "Показывает текущую рабочую систему координат и переключает G54–G59."

- id: jog.pos
  type: Input
  text: "POS"
  rect: {x: 88, y: 4, w: 12, h: 2}
  action: edit
  manual_comment: "В Step Motion задаёт расстояние шага; в Positioning задаёт координатную позицию."

- id: jog.spd
  type: Input
  text: "SPD"
  rect: {x: 88, y: 6, w: 12, h: 2}
  action: edit
  manual_comment: "Задаёт скорость Step Motion/Positioning."

- id: jog.gears
  type: Toggle
  text: "Gears"
  rect: {x: 88, y: 7, w: 12, h: 1}
  action: cycle([0.01, 0.1, 1, 10, 100])
  manual_comment: "Переключает уровни скорости Jog: 0.01, 0.1, 1, 10, 100."

- id: jog.feed
  type: Display
  text: "F"
  rect: {x: 0, y: 8, w: 18, h: 1}
  manual_comment: "Текущая скорость подачи."

- id: jog.feed_override
  type: Display
  text: "Ovrd"
  rect: {x: 18, y: 8, w: 12, h: 1}
  manual_comment: "Текущее значение коррекции подачи."

- id: jog.spindle
  type: Display
  text: "S"
  rect: {x: 30, y: 8, w: 18, h: 1}
  manual_comment: "Текущая скорость шпинделя."

- id: jog.spindle_override
  type: Display
  text: "Ovrd"
  rect: {x: 48, y: 8, w: 12, h: 1}
  manual_comment: "Текущее значение коррекции скорости шпинделя."

- id: jog.tool
  type: Button
  text: "Tool#"
  rect: {x: 68, y: 8, w: 12, h: 1}
  action: tool_change
  manual_comment: "Запускает смену инструмента на номер, введённый в поле справа; текущий номер можно изменить."

---

# 3. PROBE

**Источник:** страницы 17–20.

```yaml
SCREEN:
  id: probe
  rows: 10
  template: coordinate_plus_operation
```

Основные зоны:

``` yaml
coordinate_area: {x: 0, y: 0, w: 48, h: 7}
operation_panel: {x: 48, y: 0, w: 52, h: 8}
status: {x: 0, y: 8, w: 100, h: 1}
bottom_nav: {x: 0, y: 9, w: 100, h: 1}
```

``` yaml
- id: probe.goto_position
  type: Input
  text: "X0Y0Z0"
  action: goto_input
  manual_comment: "Ввод желаемой позиции; после ввода выполняется быстрое перемещение к указанной координате."

- id: probe.goto
  type: Button
  text: "GOTO"
  action: goto
  manual_comment: "Быстро перемещает оси к координате из поля слева."

- id: probe.goto_error
  type: ClickableDisplay
  text: "error"
  action: show_error
  manual_comment: "Показывает ошибку; клик открывает сообщение. Например, ошибка 9 означает преждевременное срабатывание probe."

- id: probe.probe
  type: Button
  text: "Probe"
  action: move_tool_setter_xy
  manual_comment: "Быстро перемещает к машинным координатам X/Y Tool Setter из Settings."

- id: probe.probe_result
  type: Display
  text: "Probe result"
  manual_comment: "Показывает машинные координаты G53 после Tool Setting."

- id: probe.probe_plus
  type: Button
  text: "Probe+"
  action: probe_positive_xy
  manual_comment: "После выбора X/Y задаёт текущие X/Y машинные координаты по Probe+."

- id: probe.probe_minus
  type: Button
  text: "Probe-"
  action: probe_negative_xy
  manual_comment: "После выбора X/Y задаёт текущие X/Y машинные координаты по Probe-."

- id: probe.speed
  type: Input
  text: "Probe SPD"
  action: edit
  manual_comment: "Скорость probing."

- id: probe.travel
  type: Input
  text: "Probe TRA"
  action: edit
  manual_comment: "Максимальная дистанция probing; если пробник не найден в пределах диапазона, выдаётся ошибка."

- id: probe.plate_thk
  type: Input
  text: "Plate THK"
  action: edit
  manual_comment: "Толщина пластины/tool setter. Используется при автоматическом сбросе координат."

- id: probe.x_probe
  type: Button
  text: "X Probe"
  action: auto_probe_x_positive
  state_after_click: locked
  manual_comment: "Проба в положительном направлении; после успешного Tool Setting рабочая координата автоматически сбрасывается в ноль. Во время операции экран блокируется."

- id: probe.y_probe
  type: Button
  text: "Y Probe"
  action: auto_probe_y_positive
  state_after_click: locked
  manual_comment: "Проба в положительном направлении; экран блокируется."

- id: probe.z_probe
  type: Button
  text: "Z Probe"
  action: auto_probe_z
  state_after_click: locked
  manual_comment: "Автоматический Z probing; экран блокируется."

- id: probe.x_minus
  type: Button
  text: "X- Probe"
  action: auto_probe_x_negative
  state_after_click: locked
  manual_comment: "Проба в отрицательном направлении; после успешного Tool Setting рабочая координата автоматически сбрасывается в ноль."

- id: probe.y_minus
  type: Button
  text: "Y- Probe"
  action: auto_probe_y_negative
  state_after_click: locked
  manual_comment: "Проба в отрицательном направлении; экран блокируется."

- id: probe.find_x_mid
  type: Button
  text: "Find X Midpoint"
  action: find_x_midpoint
  manual_comment: "После положительной и отрицательной X-пробы перемещает X в середину между точками."

- id: probe.find_y_mid
  type: Button
  text: "Find Y Midpoint"
  action: find_y_midpoint
  manual_comment: "После положительной и отрицательной Y-пробы перемещает Y в середину между точками."

- id: probe.x_reset
  type: Button
  text: "X Reset"
  action: zero_x
  manual_comment: "Сбрасывает текущую рабочую координату X в ноль."

- id: probe.y_reset
  type: Button
  text: "Y Reset"
  action: zero_y
  manual_comment: "Сбрасывает текущую рабочую координату Y в ноль."

- id: probe.tool_setter_pos
  type: Button
  text: "Tool Setter Pos"
  action: move_tool_setter
  manual_comment: "Перемещает к MPos, заданной Tool Setter X/Y в Settings."

- id: probe.first_tlm
  type: Button
  text: "First TLM"
  action: first_tool_length_measure
  manual_comment: "После перемещения к Tool Setter автоматически измеряет и записывает длину первого инструмента."

- id: probe.auto_tlo
  type: Button
  text: "Auto TLO"
  action: auto_tool_length_offset
  manual_comment: "После предыдущей обработки перемещает к Tool Setter и выполняет автоматическую компенсацию длины Z для текущей системы координат."

---

# 4. OFFSETS

**Источник:** страницы 21–23.

```yaml
SCREEN:
  id: offsets
  rows: 10
  template: coordinate_selection
```

``` yaml
coordinate_selector:
  type: Display
  rect: {x: 0, y: 0, w: 55, h: 7}
  values: [G54,G55,G56,G57,G58,G59,G28,G92,Probe,MPos,WPos]
  manual_comment: "Показывает соответствующие координаты; Next Page переключает набор."

right_controls:
  x: 55
  y: 0
  w: 45
  h: 8
```

``` yaml
- id: offsets.zxy_home
  type: Button
  text: "ZXY Home"
  action: home([Z,X,Y])
  state_after_click: locked
  manual_comment: "Последовательный Home: Z, затем X, затем Y. Требует включённого Home. Экран блокируется; End отменяет/разблокирует."

- id: offsets.axis_home
  type: Button
  text: "[X/Y/Z/A/B/C] Home"
  action: home(axis)
  state_after_click: locked
  manual_comment: "Одноосевой Home. Требует включённого Home; экран блокируется до завершения, End отменяет."

- id: offsets.g28_set
  type: Button
  text: "G28.1 / G30.1"
  action: store_machine_return_position
  manual_comment: "Записывает текущую MPos/G53 как позицию возврата G28/G30."

- id: offsets.g28_return
  type: Button
  text: "G28 / G30"
  action: return_machine_position
  manual_comment: "Быстро перемещает к позиции, заданной G28.1/G30.1."

- id: offsets.wcs
  type: Button
  text: "G54..G59"
  action: select_wcs
  manual_comment: "Переключает текущую рабочую систему координат."

- id: offsets.next
  type: Button
  text: "Next Page"
  action: cycle_coordinate_display
  manual_comment: "Переключает отображение между G54–G59, G28, G92, Probe, MPos и WPos."

- id: offsets.spindle
  type: Toggle
  text: "Spindle Off / Spindle On"
  action: spindle_toggle
  manual_comment: "Показывает состояние шпинделя. Включение запускает шпиндель на 1000 RPM."

- id: offsets.spindle_speed
  type: ClickableDisplay
  text: "1000"
  action: set_spindle_speed
  manual_comment: "Клик задаёт скорость шпинделя online."

- id: offsets.unlock
  type: Button
  text: "Unlock"
  action: clear_alarm
  manual_comment: "Разблокирует и возобновляет нормальную работу после Alarm."

- id: offsets.reset
  type: Button
  text: "Reset"
  action: zero_all_work_coordinates
  manual_comment: "Устанавливает текущую рабочую позицию всех осей в ноль."

- id: offsets.zero_return
  type: Button
  text: "Zero Return"
  action: return_work_zero_all
  manual_comment: "Перемещает все оси к нулевой позиции рабочей системы координат."

- id: offsets.coolant
  type: Toggle
  text: "CLNT Off / CLNT On"
  action: coolant_toggle
  manual_comment: "Показывает состояние coolant и переключает его."

- id: offsets.machine_parameters
  type: Display
  text: "machine parameters"
  manual_comment: "Показывает текущие машинные параметры."

---

# 5. MDI

**Источник:** страницы 24–27.

```yaml
SCREEN:
  id: mdi
  rows: 10
  template: editor
```

``` yaml
header:
  y: 0
  h: 1

program_area:
  x: 0
  y: 1
  w: 62
  h: 5

status_panel:
  x: 62
  y: 0
  w: 38
  h: 5

keyboard:
  x: 0
  y: 6
  w: 72
  h: 3

execution_controls:
  x: 72
  y: 6
  w: 28
  h: 3

bottom_nav:
  y: 9
  h: 1
```

``` yaml
- id: mdi.status
  type: Status
  text: "Status"
  values: [SGL Step, SGL BLK, Cycling, FXD Count Cycling, Idle]
  manual_comment: "Показывает текущий режим выполнения."

- id: mdi.count
  type: Display
  text: "Count"
  manual_comment: "Счётчик циклов."

- id: mdi.error
  type: Display
  text: "Error"
  manual_comment: "Номер ошибки."

- id: mdi.line
  type: ClickableDisplay
  text: "Line"
  manual_comment: "Текущий номер строки программы; клик показывает общее число символов."

- id: mdi.total_cycles
  type: Input
  text: "Total Cycles"
  manual_comment: "Количество повторений для FXD Count Cycling."

- id: mdi.file_name
  type: Input
  text: "File Name"
  manual_comment: "Имя файла вводится через клавиатуру; китайские символы не поддерживаются."

- id: mdi.preview
  type: Input
  text: "program input"
  manual_comment: "Введённое содержимое вставляется перед выбранной строкой."

- id: mdi.ent
  type: Button
  text: "ENT"
  action: insert_gcode
  manual_comment: "Enter: добавляет введённый G-code в окно программы."

- id: mdi.clr
  type: Button
  text: "CLR"
  action: context_dependent_clear
  manual_comment: "Двойной клик удаляет выбранную строку; одиночный при наличии preview очищает preview; если строка не выбрана и preview пуст, очищает всю программу."

- id: mdi.select
  type: Button
  text: "Select"
  action: select_sd_file
  manual_comment: "Переходит в SD Run для выбора файла; после выбора имя файла меняется на выбранное."

- id: mdi.read
  type: Button
  text: "Read"
  action: read_file_first_200_lines
  manual_comment: "Читает первые 200 строк текущего файла. При отсутствии SD/файла выдаёт error42."

- id: mdi.save
  type: Button
  text: "Save"
  action: save_file
  manual_comment: "Сохраняет программу на SD. SGL BLK/Cycle/FXD Count требуют сохранённый файл."

- id: mdi.feed_plus
  type: Button
  text: "F+"
  action: feed_override(+10%)
  manual_comment: "Увеличивает подачу на 10%."

- id: mdi.feed_minus
  type: Button
  text: "F-"
  action: feed_override(-10%)
  manual_comment: "Уменьшает подачу на 10%."

- id: mdi.spindle_plus
  type: Button
  text: "S+"
  action: spindle_override(+10%)
  manual_comment: "Увеличивает скорость шпинделя на 10%."

- id: mdi.spindle_minus
  type: Button
  text: "S-"
  action: spindle_override(-10%)
  manual_comment: "Уменьшает скорость шпинделя на 10%."

- id: mdi.sgl_step
  type: Button
  text: "SGL Step"
  action: run_single_line
  state_after_click: locked
  manual_comment: "Запускает по одной строке; повторный клик запускает следующую. В режиме выполнения доступны только Pause, Cont., End, F+, F-, S+, S-."

- id: mdi.sgl_blk
  type: Button
  text: "SGL BLK"
  action: run_block_once
  state_after_click: locked
  manual_comment: "Запускает весь блок один раз. Во время выполнения экран блокируется."

- id: mdi.cycle
  type: Button
  text: "Cycle"
  action: run_infinite_cycle
  state_after_click: locked
  manual_comment: "Запускает бесконечный цикл программы. Во время выполнения экран блокируется."

- id: mdi.fxd_count
  type: Button
  text: "FXD Count"
  action: run_fixed_count
  state_after_click: locked
  manual_comment: "Запускает программу заданное число раз. Во время выполнения экран блокируется."

- id: mdi.pause
  type: Button
  text: "Pause"
  action: pause
  manual_comment: "Приостанавливает выполнение."

- id: mdi.cont
  type: Button
  text: "Cont."
  action: continue
  manual_comment: "Возобновляет выполнение."

- id: mdi.end
  type: Button
  text: "End"
  action: end_and_unlock
  manual_comment: "Останавливает выполнение и снимает блокировку."

---

# 6. MIXED WIZARDS

**Источник:** страницы 28–31.

```yaml
SCREEN:
  id: mixed_wizards
  rows: 10
  template: wizard_editor
```

``` yaml
wizard_area:
  x: 0
  y: 0
  w: 55
  h: 8

parameter_area:
  x: 55
  y: 0
  w: 45
  h: 8

bottom_nav:
  y: 9
  h: 1
```

### Списки выбора

``` yaml
wizard_types:
  - Rect Pocket
  - RoundRect Pocket
  - Circular Pocket
  - Zigzag Surfacing
  - Cut Rectangle
  - Cut Circle
  - Cut RoundRect
  - Slotting
  - Matrix Drilling
  - Index Drilling
  - Positioning Drilling
  - Positioning Tapping

machining_modes:
  - Loop Inward Climb Milling
  - Loop Inward Conv. Milling
  - Loop Outward Climb Milling
  - Loop Outward Conv. Milling
  - Climb Milling Internal
  - Climb Milling External
  - Conv. Milling Internal
  - Conv. Milling External
  - Climb Milling
  - Conv. Milling
  - X-axis Milling
  - Y-axis Milling

distribution_modes:
  - Fixed Number Distribution
  - Fixed Angle Increment
```

### Параметры

``` yaml
- id: wizard.center_x
  type: Input
  text: "Center X"
  manual_comment: "X-координата центра обработки в рабочей системе."

- id: wizard.center_y
  type: Input
  text: "Center Y"
  manual_comment: "Y-координата центра обработки."

- id: wizard.z_start
  type: Input
  text: "Z Start"
  manual_comment: "Начальная Z-координата обработки."

- id: wizard.cutting_depth
  type: Input
  text: "Cutting Depth"
  manual_comment: "Полная глубина обработки; должна быть больше нуля."

- id: wizard.down_step
  type: Input
  text: "Down Step"
  manual_comment: "Глубина резания за проход по Z; должна быть больше нуля."

- id: wizard.line_spacing
  type: Input
  text: "Line Spacing"
  manual_comment: "Расстояние между соседними траекториями; для pocket/surfacing должно быть меньше диаметра инструмента."

- id: wizard.tool_r
  type: Input
  text: "Tool R"
  manual_comment: "Радиус инструмента."

- id: wizard.lead_in_r
  type: Input
  text: "Lead-in R"
  manual_comment: "Радиус спирального захода для circular pocket; должен быть положительным и меньше Tool R."

- id: wizard.machining_r
  type: Input
  text: "Machining R"
  manual_comment: "Радиус обрабатываемой окружности."

- id: wizard.length_x
  type: Input
  text: "Length X"
  manual_comment: "Длина по X для прямоугольных операций."

- id: wizard.width_y
  type: Input
  text: "Width Y"
  manual_comment: "Ширина по Y для прямоугольных операций."

- id: wizard.retract_mode
  type: Select
  text: "Retract Mode"
  options: [Rapid Retract, Retract at Center, Retract at Outside]
  manual_comment: "Режим отвода инструмента при обработке окружности."

- id: wizard.start_angle
  type: Input
  text: "Start Angle"
  manual_comment: "Начальный угол индексной обработки, отсчитывается от +Y."

- id: wizard.end_angle
  type: Input
  text: "End Angle"
  manual_comment: "Конечный угол; должен быть больше Start Angle."

- id: wizard.number_holes
  type: Input
  text: "Number of Holes"
  manual_comment: "Число отверстий для Fixed Number Distribution либо число шагов для Fixed Angle Increment."

- id: wizard.increment_angle
  type: Input
  text: "Increment Angle"
  manual_comment: "Добавка угла для каждой операции при Fixed Angle Increment."

- id: wizard.feed
  type: Input
  text: "Feed"
  manual_comment: "Скорость подачи обработки."

- id: wizard.rpm
  type: Input
  text: "RPM"
  manual_comment: "Скорость шпинделя."

### Управление списком

```yaml
- id: wizard.add_replace
  type: Button
  text: "Add/Replace"
  manual_comment: "Заменяет выбранный элемент либо добавляет новый процесс, если ничего не выбрано."

- id: wizard.delete
  type: Button
  text: "Delete"
  manual_comment: "Удаляет выбранный элемент; если ничего не выбрано — удаляет всё."

- id: wizard.send
  type: Button
  text: "Send"
  manual_comment: "Отправляет код и запускает выполнение."

- id: wizard.pause
  type: Button
  text: "Pause"
  manual_comment: "Приостанавливает выполнение."

- id: wizard.cont
  type: Button
  text: "Cont."
  manual_comment: "Возобновляет выполнение."

- id: wizard.end
  type: Button
  text: "End"
  manual_comment: "Завершает выполнение."

- id: wizard.copy
  type: Button
  text: "Copy"
  action: copy_to_mdi
  manual_comment: "Копирует сформированный код в MDI."

- id: wizard.back
  type: Button
  text: "Back"
  action: back
  manual_comment: "Возвращает на предыдущую страницу."
```

------------------------------------------------------------------------

# 7. SETTING --- общая архитектура

**Источник:** страницы 32--80.

Настройки разделены на страницы:

``` yaml
settings_pages:
  - main
  - tool_change
  - rtcp
  - debugging_rtcp
  - io_485
  - modbus_485
  - sta
  - screen
  - startup
  - user_defined
  - tool_table_1
  - tool_table_2
```

Доступ к настройкам защищён кодом `753951`; руководство указывает, что
изменить код нельзя.

------------------------------------------------------------------------

# 8. SETTING / Main

**Источник:** страницы 37--44.

``` yaml
SCREEN:
  id: setting.main
  rows: 10
  template: settings_form
```

### Таблица осей

``` yaml
axis_table:
  x: 0
  y: 0
  w: 74
  h: 6
  columns:
    - Pulse Equivalent
    - Max SPD
    - Acceleration
    - Max Travel
  rows: [X,Y,Z,A,B,C]
```

``` yaml
right_panel:
  x: 74
  y: 0
  w: 26
  h: 6
  fields:
    - Motor DIR
    - Status
    - Home DIR
    - Home SPD
    - Crawl SPD
    - RPM UL
    - RPM LL
```

### Параметры

``` yaml
- id: setting.pulse_equivalent
  type: Input
  scope: [X,Y,Z,A,B,C]
  manual_comment: "Соотношение моторных импульсов и перемещения/угла. После изменения требуется Apply и перезапуск."

- id: setting.max_spd
  type: Input
  scope: [X,Y,Z,A,B,C]
  manual_comment: "Максимальная скорость оси в мм/мин."

- id: setting.acceleration
  type: Input
  scope: [X,Y,Z,A,B,C]
  manual_comment: "Ускорение оси."

- id: setting.max_travel
  type: Input
  scope: [X,Y,Z,A,B,C]
  manual_comment: "Максимальный ход для soft limit и поиска home. Ноль отключает soft limit данной оси."

- id: setting.motor_dir
  type: Input
  manual_comment: "Направление движения мотора."

- id: setting.status
  type: Display
  manual_comment: "Не изменять."

- id: setting.node_deviation
  type: Input
  manual_comment: "Точность круговой интерполяции. Руководство рекомендует не менять."

- id: setting.home_dir
  type: Input
  manual_comment: "Направление Home."

- id: setting.crawl_spd
  type: Input
  manual_comment: "Скорость второй стадии Home."

- id: setting.spindle_ul
  type: Input
  manual_comment: "Верхний предел скорости шпинделя."

- id: setting.spindle_ll
  type: Input
  manual_comment: "Нижний предел скорости шпинделя."

- id: setting.home_spd
  type: Input
  manual_comment: "Скорость первой стадии Home."

- id: setting.pull_off
  type: Input
  manual_comment: "Отход после обнаружения Home switch; ноль недопустим."

- id: setting.switch_debounce
  type: Input
  manual_comment: "Время подавления дребезга Home switch; руководство отмечает, что обычно достаточно 0."

- id: setting.pulse_rev
  type: Input
  manual_comment: "Реверс импульса. При правильной проводке и отсутствии движения руководство предлагает попробовать 63."

### Переключатели

```yaml
- id: setting.en_dir
  type: Toggle
  text: "EN FWD / EN REV"
  manual_comment: "Разрешает forward/reverse enable; применимо к контроллерам с enable interface."

- id: setting.limit_dir
  type: Toggle
  text: "Limit FWD / Limit REV"
  manual_comment: "Направление сигнала limit switch."

- id: setting.home
  type: Toggle
  text: "Home ON / Home OFF"
  manual_comment: "Включает Home. При включении после перезапуска возникает alarm потери позиции; Home автоматически отключает hard limit."

- id: setting.mode
  type: Toggle
  text: "CNC Mode / Laser Mode"
  manual_comment: "Переключает CNC/Laser; после изменения требуется power cycle."

- id: setting.soft_limit
  type: Toggle
  text: "SPL On / SPL Off"
  manual_comment: "Soft limit. Требует Max Travel и успешно выполненного Home; после включения нужен перезапуск."

- id: setting.hard_limit
  type: Toggle
  text: "HPL On / HPL Off"
  manual_comment: "Hard limit. Автоматически отключается при включённом Home; руководство не рекомендует использовать."

- id: setting.units
  type: Toggle
  text: "MM Mode / Inch Mode"
  manual_comment: "Переключает миллиметры/дюймы."

- id: setting.probe_dir
  type: Toggle
  text: "Probe FWD / Probe REV"
  manual_comment: "Переключает направление сигнала probe."

- id: setting.bluetooth
  type: Button
  text: "BLUT"
  action: bluetooth_mode
  manual_comment: "Включает Bluetooth; система перезапускается, после чего требуется заново найти origin."

- id: setting.wifi
  type: Button
  text: "WIFI"
  action: wifi_ap
  manual_comment: "Включает WiFi AP mode; после перезапуска требуется заново найти origin."

- id: setting.none
  type: Button
  text: "NONE"
  action: disable_wireless
  manual_comment: "Отключает Bluetooth/WiFi; после перезапуска требуется заново найти origin."

- id: setting.read
  type: Button
  text: "Read"
  action: read_settings
  manual_comment: "Читает настройки; используется для проверки применённых параметров."

- id: setting.apply
  type: Button
  text: "Apply"
  action: apply_settings
  manual_comment: "После заполнения параметров руководство требует нажать Apply дважды."

- id: setting.screen_set
  type: Button
  text: "Screen Set"
  action: open(screen_settings)
  manual_comment: "Открывает настройки экрана."
```

------------------------------------------------------------------------

# 9. SETTING / Tool Change

**Источник:** страницы 45--55.

Шаблон:

``` yaml
SCREEN:
  id: setting.tool_change
  rows: 10
  template: settings_form_4_columns
```

### Параметры

``` yaml
- id: tool.pulse_width
  type: Display
  manual_comment: "Длительность импульса в микросекундах."

- id: tool.en_time
  type: Input
  manual_comment: "Время удержания enable-сигнала."

- id: tool.node_deviation
  type: Input
  manual_comment: "Раннее замедление на поворотах; default 0.01, менять не рекомендуется."

- id: tool.arc_tolerance
  type: Input
  manual_comment: "Точность интерполяции дуг; default 0.002, менять не рекомендуется."

- id: tool.setter_x
  type: Input
  manual_comment: "X машинная координата Tool Setter."

- id: tool.setter_y
  type: Input
  manual_comment: "Y машинная координата Tool Setter."

- id: tool.setter_z
  type: Input
  manual_comment: "Не используется."

- id: tool.setter_a
  type: Input
  manual_comment: "При ненулевом значении перед сменой инструмента соответствующая ось выравнивается по заданной машинной координате."

- id: tool.setter_b
  type: Input
  manual_comment: "Аналогично Tool Setter A."

- id: tool.setter_c
  type: Input
  manual_comment: "Аналогично Tool Setter A."

- id: tool.change_mode
  type: Input
  manual_comment: "Режим автоматической/ручной смены инструмента, значения 0–8."

- id: tool.turret_axis
  type: Input
  manual_comment: "Ось турели: 4=A, 5=B, 6=C; другие значения отключают turret axis."

- id: tool.active
  type: Input
  manual_comment: "Текущий номер инструмента. После успешной смены обновляется автоматически и сохраняется при power outage."

- id: tool.max
  type: Input
  manual_comment: "Количество инструментов в таблице, 1–20."

- id: tool.xy_spacing
  type: Input
  manual_comment: "Параметр L для G73/81/82/83: при нулевом spacing повторение происходит в одной позиции, при ненулевом — с приращением."

- id: tool.drill_d
  type: Input
  manual_comment: "Retract distance для G73; 0 использует default 0.4."

- id: tool.ip
  type: Display
  manual_comment: "IP-адрес для клиентского доступа к управлению и wireless file transfer."

- id: tool.mode
  type: Display
  manual_comment: "Текущий WiFi mode."
```

### Кнопки

``` yaml
- id: tool.wifi_sta
  type: Button
  text: "WIFI/STA"
  manual_comment: "Включает WiFi STA; система перезапускается."

- id: tool.sta_set
  type: Button
  text: "STA SET"
  manual_comment: "Открывает STA Settings."

- id: tool.wifi
  type: Button
  text: "WIFI"
  manual_comment: "Включает WiFi AP."

- id: tool.none
  type: Button
  text: "NONE"
  manual_comment: "Отключает wireless modes."

- id: tool.table1
  type: Button
  text: "Tooltable #1"
  manual_comment: "Открывает инструменты 1–10."

- id: tool.table2
  type: Button
  text: "Tooltable #2"
  manual_comment: "Открывает инструменты 11–20."

- id: tool.read
  type: Button
  text: "Read"
  manual_comment: "Читает настройки."

- id: tool.apply
  type: Button
  text: "Apply"
  manual_comment: "Применяет настройки; руководство требует два нажатия."
```

------------------------------------------------------------------------

# 10. TOOL TABLE

**Источник:** страницы 49--50 и далее.

Это полноэкранная таблица без нижней навигации.

``` yaml
SCREEN:
  id: tool_table
  rows: 10
  template: edit_table

table:
  x: 0
  y: 0
  w: 100
  h: 9
  rows: 10
  columns:
    - T#
    - Safe X
    - Safe Y
    - Safe Z
    - ToolIndex Position
    - ToolChange Clearance
    - Safe Pos
    - Tool Pos X
    - Tool Pos Y
    - Tool Un-clamp Z
    - Tool L

actions:
  y: 9
  h: 1
  items: [Apply, Read, Back]
```

`Tool Table #1` содержит 1--10; `Tool Table #2` --- 11--20.

------------------------------------------------------------------------

# 11. RTCP CONFIGURATION

**Источник:** страницы 56--61.

``` yaml
SCREEN:
  id: setting.rtcp
  rows: 10
  template: settings_form
```

``` yaml
- id: rtcp.tapping_axis
  type: Input
  manual_comment: "Ось для mixed wizard tapping: 4=A, 5=B, 6=C."

- id: rtcp.rtp_mode
  type: Input
  manual_comment: "Режим RTP/RTCP; G43.4 H1 используется для активации tool length compensation/RTCP для T1."

- id: rtcp.rtcp_digit
  type: Input
  manual_comment: "Неиспользуемый параметр."

- id: rtcp.pendulum_l
  type: Input
  manual_comment: "Длина pendulum для A/B swivel-head. Может быть измерена вручную; после Apply и First TLM используется для последующей автоматической компенсации."

- id: rtcp.a_ecc
  type: Input
  manual_comment: "Горизонтальное расстояние от pivot A-axis до tool tip."

- id: rtcp.b_ecc
  type: Input
  manual_comment: "Горизонтальное расстояние от pivot B-axis до tool tip."

- id: rtcp.c_ecc
  type: Input
  manual_comment: "Горизонтальное расстояние от pivot C-axis до tool tip."

- id: rtcp.x_cc
  type: Input
  manual_comment: "Калибровочная X-координата."

- id: rtcp.y_cc
  type: Input
  manual_comment: "Калибровочная Y-координата."

- id: rtcp.z_cc
  type: Input
  manual_comment: "Калибровочная Z-координата."

- id: rtcp.backlash
  type: Input
  scope: [X,Y,Z,A,B,C]
  manual_comment: "Компенсация люфта; неотрицательное значение, 0 отключает компенсацию оси."

- id: rtcp.tool_touch_off
  type: Input
  manual_comment: "0 — touch-off detection игнорируется; 1 — проверяется clamp cylinder; 2 — также проверяется retract cylinder. При неуспехе в течение 5 секунд система останавливается."

- id: rtcp.485_station
  type: Input
  manual_comment: "Номер станции 485; руководство указывает Set 1."

- id: rtcp.485_band
  type: Input
  manual_comment: "38400 для 16 каналов; 9600 для 8/4 каналов."

- id: rtcp.485_channels
  type: Input
  manual_comment: "Допустимые значения зависят от модуля: 4/18 для 4 каналов, 8/18 для 8 каналов, 16/20 для 16 каналов."

- id: rtcp.ip
  type: Display
  manual_comment: "IP для доступа клиентом к управлению и передаче файлов."

- id: rtcp.mode
  type: Display
  manual_comment: "Текущий WiFi mode."

- id: rtcp.wifi_ap
  type: Button
  text: "WIFI/AP"
  manual_comment: "Включает AP mode и перезапускает систему."

- id: rtcp.wifi_sta
  type: Button
  text: "WIFI/STA"
  manual_comment: "Включает STA mode и перезапускает систему."

- id: rtcp.sta_set
  type: Button
  text: "STA SET"
  manual_comment: "Открывает STA Settings."

- id: rtcp.none
  type: Button
  text: "NONE"
  manual_comment: "Отключает Bluetooth/WiFi."

- id: rtcp.startup
  type: Button
  text: "Startup-Config"
  manual_comment: "Открывает Startup Configuration."

- id: rtcp.user_defined
  type: Button
  text: "User-Defineds"
  manual_comment: "Открывает User-Defined Input Buttons Configuration."

- id: rtcp.read
  type: Button
  text: "Read"
  manual_comment: "Читает настройки."

- id: rtcp.apply
  type: Button
  text: "Apply"
  manual_comment: "Применение; требуется два нажатия."
```

### RTP Mode

Значения 4--19:

``` yaml
rtp_modes:
  4: "XY plane rotation around X by A angle"
  5: "XY plane rotation around Y by B angle"
  6: "XY plane rotation around Z by C angle"
  7: "A-axis swivel head RTCP"
  8: "B-axis swivel head RTCP"
  9: "C-axis swivel head RTCP"
  10: "C-axis rotary table RTCP"
  11: "B-axis swivel head + C-axis rotary table"
  12: "A-axis rotary table"
  13: "B-axis swivel head + A-axis rotary table"
  14: "B-axis rotary table"
  15: "A-C tilting table"
  16: "B-C tilting table"
  17: "A-axis swivel head + C-axis rotary table"
  18: "C&B dual swivel heads"
  19: "C&A dual swivel heads"
```

------------------------------------------------------------------------

# 12. 485 IO EXTENSION / MODBUS

**Источник:** страницы 70--73.

Это специализированные формы настроек.

## 12.1. 485 IO

``` yaml
SCREEN:
  id: setting.io_485
  rows: 10
  template: settings_form
```

Основные поля:

``` yaml
- 485 Station No.
- 485 Band
- 485 Channels
- IP Address
- Mode
```

Кнопки wireless/settings повторяют RTCP/Tool Change.

## 12.2. Modbus

``` yaml
SCREEN:
  id: setting.modbus
  rows: 10
  template: settings_form
```

Группы:

``` yaml
position_fields:
  - X/Y/Z/A/B/C Position
  - X/Y/Z/A/B/C Station
  - X/Y/Z/A/B/C Register

function_fields:
  - Function Code
  - Registers
  - Pulse/revolution parameters

electrical_conversion:
  - X/Y/Z/A/B/C Pulse
  - X/Y/Z/A/B/C Gear
```

------------------------------------------------------------------------

# 13. STA SETTINGS

**Источник:** страницы 74--75.

``` yaml
SCREEN:
  id: setting.sta
  rows: 10
  template: two_panel
```

``` yaml
network_list:
  x: 0
  y: 0
  w: 55
  h: 8
  type: List

connection_panel:
  x: 55
  y: 0
  w: 45
  h: 8
```

``` yaml
- id: sta.active_ssid
  type: Display
  manual_comment: "Текущее имя WiFi."

- id: sta.connected_ip
  type: Display
  manual_comment: "Текущий IP."

- id: sta.current_mode
  type: Display
  manual_comment: "Текущий режим."

- id: sta.new_line
  type: Input
  manual_comment: "Номер строки сети для подключения."

- id: sta.new_password
  type: Input
  manual_comment: "Пароль выбранной сети."

- id: sta.refresh
  type: Button
  manual_comment: "Обновляет список сетей."

- id: sta.disconnect
  type: Button
  manual_comment: "Отключает текущее соединение."

- id: sta.search
  type: Button
  manual_comment: "Ищет доступные WiFi сети."

- id: sta.connect
  type: Button
  manual_comment: "Подключает выбранную сеть; после подключения контроллер перезапускается."
```

------------------------------------------------------------------------

# 14. SCREEN SETTINGS

**Источник:** страницы 76.

``` yaml
SCREEN:
  id: setting.screen
  rows: 10
  template: settings_form
```

``` yaml
- id: screen.brightness
  type: Slider
  text: "Screen Brightness"
  manual_comment: "Регулировка яркости экрана."

- id: screen.power_on_by_pressing
  type: Button
  manual_comment: "Настройка включения/пробуждения нажатием."

- id: screen.press_after_powering_on
  type: Input
  manual_comment: "Параметр задержки/условия после включения."

- id: screen.select_switch
  type: Button
  manual_comment: "Выбор/переключение."

- id: screen.query
  type: Button
  manual_comment: "Запрос/проверка."

- id: screen.connect
  type: Button
  manual_comment: "Подключение."

- id: screen.baud
  type: Input
  text: "115200"
  manual_comment: "Параметр скорости соединения."

- id: screen.setting
  type: Button
  manual_comment: "Переход к настройкам."

- id: screen.touch_calibration
  type: Button
  manual_comment: "Калибровка touch."

- id: screen.reset
  type: Button
  manual_comment: "Сброс экрана в исходное состояние."
```

------------------------------------------------------------------------

# 15. STARTUP CONFIGURATION

**Источник:** страницы 77--78.

``` yaml
SCREEN:
  id: setting.startup
  rows: 10
  template: fixed_command_table

startup_commands:
  count: 8
  fields:
    - line: 1
    - line: 2
    - line: 3
    - line: 4
    - line: 5
    - line: 6
    - line: 7
    - line: 8

spindle_on_delay:
  type: Input
  unit: seconds

spindle_off_delay:
  type: Input
  unit: seconds

actions: [Apply, Read, Back]
```

Руководство отдельно описывает примеры startup-команд: автоматический
Home осей, перемещение в G54/G53 и т.п.

------------------------------------------------------------------------

# 16. USER-DEFINED INPUT BUTTONS

**Источник:** страницы 79--80.

``` yaml
SCREEN:
  id: setting.user_defined
  rows: 10
  template: fixed_command_table

buttons:
  count: 8
  fields:
    - line: 1
    - line: 2
    - line: 3
    - line: 4
    - line: 5
    - line: 6
    - line: 7
    - line: 8

spindle_on_delay:
  type: Input

spindle_off_delay:
  type: Input

actions: [Apply, Read, Back]
```

Особенность: это пользовательские команды, связанные с дополнительными
кнопками ввода.

------------------------------------------------------------------------

# 17. RUN

**Источник:** страницы 81--82.

``` yaml
SCREEN:
  id: run
  rows: 10
  template: icon_menu
```

``` yaml
icon_grid:
  columns: 5
  rows: 2-3
```

Элементы:

``` yaml
- {id: run.sd,             text: "SD Run",          action: open(sd_run)}
- {id: run.usb,            text: "USB Run",         action: open(usb_run)}
- {id: run.drilling,       text: "Drilling",        action: open(drilling)}
- {id: run.tapping,        text: "Tapping",         action: open(tapping)}
- {id: run.cut_circle,     text: "Cut Circle",      action: open(cut_circle)}
- {id: run.cut_rectangle,  text: "Cut Rectangle",   action: open(cut_rectangle)}
- {id: run.rect_pocket,    text: "Rect Pocket",     action: open(rect_pocket)}
- {id: run.circular_pocket, text:"Circular Pocket",  action: open(circular_pocket)}
- {id: run.cut_roundrect,  text: "Cut RoundRect",   action: open(cut_roundrect)}
- {id: run.surfacing,      text: "Surfacing",       action: open(surfacing)}
- {id: run.io_monitor,     text: "IO Monitor",      action: open(io_monitor)}
- {id: run.slotting,       text: "Slotting",        action: open(slotting)}
- {id: run.mixed_wizards,  text: "Mixed Wizards",   action: open(mixed_wizards)}
- {id: run.jog,            text: "JOG",              action: open(jog)}
- {id: run.mdi,            text: "MDI",              action: open(mdi)}
- {id: run.setting,        text: "SETTING",          action: open(setting.main)}
- {id: run.run,            text: "RUN",              action: open(run)}
```

`SD Run` рекомендуется руководством как основной способ выполнения
файлов с SD.

------------------------------------------------------------------------

# 18. ERROR REPORT

**Источник:** страница 83.

``` yaml
SCREEN:
  id: error_report
  rows: 10
  template: text_viewer
```

``` yaml
- id: error.back
  type: Button
  text: "Back"
  manual_comment: "Возврат на предыдущую страницу."

- id: error.err_pd
  type: Button
  text: "ERR PD"
  manual_comment: "Следующая страница ошибок."

- id: error.err_pu
  type: Button
  text: "ERR PU"
  manual_comment: "Предыдущая страница ошибок."

- id: error.alm_pd
  type: Button
  text: "ALM PD"
  manual_comment: "Следующая страница alarm."

- id: error.alm_pu
  type: Button
  text: "ALM PU"
  manual_comment: "Предыдущая страница alarm."
```

------------------------------------------------------------------------

# 19. SD RUN

**Источник:** страницы 84--87.

Две страницы одного экрана.

## 19.1. Page 1

``` yaml
SCREEN:
  id: sd_run.page1
  rows: 10
  template: file_browser
```

``` yaml
- id: sd.files
  type: List
  manual_comment: "Показывает файлы SD; имя файла выбирается кликом."

- id: sd.run
  type: Button
  text: "Run"
  manual_comment: "Запускает выбранный файл и переводит на Page 2; экран блокируется."

- id: sd.delete
  type: Button
  text: "Delete"
  manual_comment: "Удаляет выбранный файл."

- id: sd.spindle
  type: Toggle
  text: "Spindle Off / On"
  manual_comment: "Показывает и переключает состояние шпинделя."

- id: sd.pause
  type: Toggle
  text: "Pause / Cont."
  manual_comment: "Переключает паузу/продолжение. Во время Pause не следует использовать другие операции кроме Cont. и End."

- id: sd.spindle_speed
  type: ClickableDisplay
  manual_comment: "Клик задаёт скорость шпинделя."

- id: sd.coolant
  type: Toggle
  text: "CLNT Off / On"
  manual_comment: "Показывает и переключает coolant."

- id: sd.refresh
  type: Button
  text: "Refresh"
  manual_comment: "Обновляет список SD; также извлекает номер последней выполненной строки для power-loss recovery."

- id: sd.next_line
  type: Button
  text: "Set Next Line"
  manual_comment: "Задаёт breakpoint для восстановления после power failure."

- id: sd.cancel_next_line
  type: Button
  text: "Cancel Next Line"
  manual_comment: "Отменяет установленный breakpoint; рекомендуется нажать перед повторной настройкой."
```

## 19.2. Page 2 --- выполнение

``` yaml
SCREEN:
  id: sd_run.page2
  rows: 10
  template: run_status
```

``` yaml
- id: sd.tool_change
  type: Button
  text: "Tool Change"
  manual_comment: "Смена на номер инструмента из левого поля."

- id: sd.error
  type: Display
  text: "Error"
  manual_comment: "Текущая ошибка; NONE означает отсутствие ошибки."

- id: sd.status
  type: Display
  text: "Status"
  manual_comment: "Текущее состояние станка."

- id: sd.progress
  type: Display
  text: "Progress"
  manual_comment: "Текущий прогресс операции."

- id: sd.ongoing
  type: Display
  text: "Ongoing"
  manual_comment: "Имя выполняемого файла."

- id: sd.feed_buttons
  type: Button
  text: "F100/F+10/F+1/F-1/F-10"
  manual_comment: "Изменение feed override в реальном времени."

- id: sd.spindle_buttons
  type: Button
  text: "S100/S+1/S-1/S+10/S-10"
  manual_comment: "Изменение spindle override в реальном времени."

- id: sd.rapid_100
  type: Button
  text: "G0/1"
  manual_comment: "Rapid feedrate 100%."

- id: sd.rapid_50
  type: Button
  text: "G0/2"
  manual_comment: "Rapid feedrate 50%."

- id: sd.rapid_25
  type: Button
  text: "G0/4"
  manual_comment: "Rapid feedrate 25%."
```

------------------------------------------------------------------------

# 20. LIST MACHINING

**Источник:** страницы 88--89.

``` yaml
SCREEN:
  id: list_machining
  rows: 10
  template: edit_table
```

``` yaml
table:
  rows: 12
  columns:
    - Center X
    - Center Y
    - Z Start
    - Z End
    - Circle R / Length X
    - Width Y
    - Fillet / A
```

`SGL-point Milling` на wizard-странице переключается в
`Multi-point Milling`; затем Start Line/End Line задают диапазон строк
этой таблицы.

------------------------------------------------------------------------

# 21. ОБЩИЙ ШАБЛОН CAM OPERATION

Экраны Drilling, Tapping, Cut Circle, Cut Rectangle, Rectangular Pocket,
Circular Pocket, Cut RoundRect, Surfacing и Slotting построены по одному
принципу.

``` yaml
CAM_OPERATION:
  rows: 10

  header:
    y: 0
    h: 1

  left_parameters:
    x: 0
    y: 1
    w: 26
    h: 7

  middle_parameters:
    x: 26
    y: 1
    w: 25
    h: 7

  operation_parameters:
    x: 51
    y: 1
    w: 24
    h: 7

  action_panel:
    x: 75
    y: 1
    w: 25
    h: 7

  bottom_nav:
    y: 9
    h: 1
```

Общие поля:

``` yaml
- Safe Z
- Z Start
- Z End
- Down Step
- Start Line
- End Line
- Feedrate
- Spindle RPM
- Spindle Delay
- Tool R
- Center X
- Center Y
```

Общие команды:

``` yaml
- Save
- Read
- Feedrate +10%
- Feedrate -10%
- Spindle RPM +10%
- Spindle RPM -10%
- Centering
- Pause
- Cont.
```

`Save` и `Read` работают с параметрами, выделенными красным цветом на
изображении; руководство описывает сохранение/чтение параметров через
системную память.

------------------------------------------------------------------------

# 22. DRILLING

**Источник:** страницы 90--92.

Дополнительные параметры:

``` yaml
- A-Axis Index
- Number of Copies in X
- Number of Copies in Y
- X Spacing
- Y Spacing
```

Режимы:

``` yaml
- SGL-point Drilling
- Matrix Drilling
- List Drilling
```

``` yaml
- id: drilling.retract
  type: Toggle
  text: "Retract to Start Plane / Retract D Distance"
  manual_comment: "Переключает способ возврата при peck drilling."

- id: drilling.centering
  type: Button
  text: "Centering"
  manual_comment: "Быстро перемещает оси в центральную позицию над заданной точкой."

- id: drilling.deep
  type: Button
  text: "Deep-Hole Drilling"
  state_after_click: locked
  manual_comment: "Однопроходное глубокое сверление; после завершения автоматический возврат на безопасную высоту."

- id: drilling.peck
  type: Button
  text: "Peck Drilling"
  state_after_click: locked
  manual_comment: "Циклическое сверление с отводом для удаления стружки; шаг задаётся Down Step."

- id: drilling.pause
  type: Toggle
  text: "Pause / Cont."
  manual_comment: "Пауза/продолжение."
```

------------------------------------------------------------------------

# 23. TAPPING

**Источник:** страницы 93--96.

Дополнительные параметры:

``` yaml
- Pitch
- A-Axis Index
- Servo Spindle
```

Режимы:

``` yaml
- SGL-point Tapping
- Multi-point Tapping
```

``` yaml
- id: tapping.centering
  type: Button
  text: "Centering"
  manual_comment: "Быстро перемещает оси в центр заданной точки."

- id: tapping.query
  type: Button
  text: "Query"
  manual_comment: "Открывает страницу запроса стандарта шага резьбы."

- id: tapping.straight
  type: Button
  text: "Straight Tapping"
  state_after_click: locked
  manual_comment: "Нарезание резьбы до дна одним проходом; затем автоматический возврат."

- id: tapping.back_forth
  type: Button
  text: "Back and Forth"
  state_after_click: locked
  manual_comment: "Нарезание с возвратами для удаления стружки; шаг возврата определяется Down Step."

- id: tapping.pause
  type: Toggle
  text: "Pause / Cont."
  manual_comment: "Пауза/продолжение."
```

------------------------------------------------------------------------

# 24. CUT CIRCLE

**Источник:** страницы 97--99.

Поля:

``` yaml
- Safe Z
- Z Start
- Z End
- Down Step
- Tool R
- Machining R
- Start Line
- End Line
- Feedrate
- Spindle RPM
- Spindle Delay
- Center X
- Center Y
```

Режимы:

``` yaml
- SGL-point Milling
- Multi-point Milling
- Spiral External Milling
- Spiral Internal Milling
```

``` yaml
- id: cut_circle.threading
  type: Toggle
  text: "Cut Circle / Threading"
  manual_comment: "Переключает обычную обработку окружности и threading function."

- id: cut_circle.centering
  type: Button
  text: "Centering"
  manual_comment: "Быстро перемещает оси к центру."

- id: cut_circle.external
  type: Button
  text: "Spiral External Milling"
  state_after_click: locked
  manual_comment: "Наружное круговое контурное фрезерование; экран блокируется."

- id: cut_circle.internal
  type: Button
  text: "Spiral Internal Milling"
  state_after_click: locked
  manual_comment: "Внутреннее круговое фрезерование с автоматической компенсацией инструмента; экран блокируется."

- id: cut_circle.pause
  type: Toggle
  text: "Pause / Cont."
  manual_comment: "Пауза/продолжение."
```

------------------------------------------------------------------------

# 25. CUT RECTANGLE

**Источник:** страницы 100--102.

Поля:

``` yaml
- Safe Z
- Z Start
- Z End
- Down Step
- Start Line
- End Line
- Tool R
- Feedrate
- Spindle RPM
- Spindle Delay
- Center X
- Center Y
- Length X
- Width Y
```

Команды:

``` yaml
- Save
- Read
- Feedrate +10%
- Feedrate -10%
- Spindle RPM +10%
- Spindle RPM -10%
- SGL-point Milling
- Multi-point Milling
- Centering
- Spiral External Milling
- Spiral Internal Milling
- Pause / Cont.
```

Комментарии по смыслу совпадают с Cut Circle, но геометрия относится к
прямоугольнику.

------------------------------------------------------------------------

# 26. RECTANGULAR POCKET

**Источник:** страницы 103--105.

Поля:

``` yaml
- Safe Z
- Z Start
- Z End
- Down Step
- Line Spacing
- Start Line
- End Line
- Corner R
- Tool R
- Feedrate
- Spindle RPM
- Spindle Delay
- Center X
- Center Y
- Length X
- Width Y
```

Режимы:

``` yaml
- SGL-pos Milling
- Multi-pos Milling
- Centering
- Inward Climb Milling
- Inward Conv. Milling
- Pause / Cont.
```

`Line Spacing` должен быть положительным и меньше диаметра инструмента.

------------------------------------------------------------------------

# 27. CIRCULAR POCKET

**Источник:** страницы 106--108.

Поля:

``` yaml
- Safe Z
- Z Start
- Z End
- Down Step
- Lead-in R
- Line Spacing
- Start Line
- End Line
- Tool R
- Machining R
- Feedrate
- Spindle RPM
- Spindle Delay
- Center X
- Center Y
```

Команды:

``` yaml
- SGL-pos Milling
- Multi-pos Milling
- Centering
- Inward/Climb mode
- Pause / Cont.
```

------------------------------------------------------------------------

# 28. CUT ROUNDRECT

**Источник:** страницы 109--111.

Поля:

``` yaml
- Safe Z
- Z Start
- Z End
- Down Step
- Start Line
- End Line
- Corner R
- Tool R
- Feedrate
- Spindle RPM
- Spindle Delay
- Center X
- Center Y
- Length X
- Width Y
```

Команды:

``` yaml
- Save
- Read
- Feedrate +10%
- Feedrate -10%
- Spindle RPM +10%
- Spindle RPM -10%
- SGL-point Milling
- Multi-point Milling
- Centering
- Spiral External Milling
- Spiral Internal Milling
- Pause / Cont.
```

------------------------------------------------------------------------

# 29. SURFACING

**Источник:** страницы 112--114.

Поля:

``` yaml
- Safe Z
- Z Start
- Z End
- Down Step
- Line Spacing
- Start Line
- End Line
- Spindle RPM
- Feedrate
- Tool R
- Spindle Delay
- Center X
- Center Y
- Length X
- Width Y
```

Переключатели:

``` yaml
- Ramp Down / Straight Down
- SGL-pos Milling / Multi-pos Milling
- X-axis Milling / Y-axis Milling
```

``` yaml
- id: surfacing.start
  type: Button
  text: "Start Run"
  state_after_click: locked
  manual_comment: "Запускает обработку; экран блокируется, End разблокирует/останавливает."

- id: surfacing.centering
  type: Button
  text: "Centering"
  manual_comment: "Быстро перемещает оси к центру."

- id: surfacing.pause
  type: Toggle
  text: "Pause / Cont."
  manual_comment: "Пауза/продолжение."
```

------------------------------------------------------------------------

# 30. I/O MONITOR

**Источник:** страница 115.

``` yaml
SCREEN:
  id: io_monitor
  rows: 10
  template: io_matrix
```

``` yaml
inputs:
  type: DisplayMatrix
  interactive: false

outputs:
  type: ButtonMatrix
  interactive: true
```

Состояние:

``` yaml
active:
  visual: green
```

`manual_comment`:

> Если текущий I/O находится во входном/выходном состоянии, он
> отображается зелёным. Нажатие соответствующей output-кнопки выдаёт
> сигнал.

------------------------------------------------------------------------

# 31. SLOTTING

**Источник:** страницы 116--118.

Поля:

``` yaml
- Safe Z
- Z Start
- Z End
- Down Step
- Start Line
- End Line
- Spindle RPM
- Feedrate
- Tool R
- Spindle Delay
- Center X
- Center Y
- Slot Length
```

Переключатели:

``` yaml
- Ramp Down / Straight Down
- SGL-pos Milling / Multi-pos Milling
- X-axis Milling / Y-axis Milling
```

``` yaml
- id: slotting.centering
  type: Button
  text: "Centering"
  manual_comment: "Быстро перемещает оси в центр заданной точки."

- id: slotting.start
  type: Button
  text: "Start Run"
  state_after_click: locked
  manual_comment: "Запускает обработку; экран блокируется, End разблокирует."

- id: slotting.pause
  type: Toggle
  text: "Pause / Cont."
  manual_comment: "Пауза/продолжение."
```

------------------------------------------------------------------------

# 32. Глобальный механизм блокировки

По руководству блокировка используется в нескольких классах операций.

``` yaml
LOCKED_OPERATION:
  enter:
    - нажата операция автоматического движения/обработки

  disabled:
    - остальные элементы экрана

  allowed:
    - End
    - Pause
    - Cont.
    - F+
    - F-
    - S+
    - S-

  exit:
    - operation_complete
    - End
```

Для Home/Probe некоторые экраны используют клик по координатной области
как отмену/разблокировку.

------------------------------------------------------------------------

# 33. Глобальная интерактивность Status Box / Coordinate Display

Руководство задаёт особое поведение:

``` yaml
status_box:
  if: "Alarm"
  click: "force clear alarm"

  if: "Dwell"
  click: "resume operation"

  otherwise:
  click: "cancel current action"
```

Для координатной области:

``` yaml
coordinate_display.click:
  cancel:
    - step motion
    - positioning mode
    - некоторые locked probing/RTCP operations
```

------------------------------------------------------------------------

# 34. Сводка экранных шаблонов

``` yaml
templates:

  coordinate:
    screens: [JOG]

  coordinate_plus_operation:
    screens: [Probe]

  coordinate_selection:
    screens: [Offsets]

  editor:
    screens: [MDI]

  wizard_editor:
    screens: [Mixed Wizards]

  settings_form:
    screens:
      - Setting Main
      - Tool Change
      - RTCP
      - 485
      - Modbus
      - Screen
      - STA

  fixed_command_table:
    screens:
      - Startup Configuration
      - User Defined

  edit_table:
    screens:
      - Tool Table
      - List Machining

  icon_menu:
    screens: [RUN]

  text_viewer:
    screens: [Error Report]

  file_browser:
    screens: [SD Run Page 1]

  run_status:
    screens: [SD Run Page 2]

  cam_operation:
    screens:
      - Drilling
      - Tapping
      - Cut Circle
      - Cut Rectangle
      - Rectangular Pocket
      - Circular Pocket
      - Cut RoundRect
      - Surfacing
      - Slotting

  io_matrix:
    screens: [I/O Monitor]
```

------------------------------------------------------------------------

# 35. Карта переходов

``` yaml
navigation:

  JOG:
    Probe: probe
    Offsets: offsets
    JOG: jog
    MDI: mdi
    SETTING: setting.main
    RUN: run

  Probe:
    JOG: jog
    MDI: mdi
    SETTING: setting.main
    RUN: run

  Offsets:
    JOG: jog
    MDI: mdi
    SETTING: setting.main
    RUN: run

  MDI:
    Select: sd_run.page1
    Mixed Wizards: mixed_wizards
    JOG: jog
    SETTING: setting.main
    RUN: run

  Mixed Wizards:
    Back: previous
    MDI: mdi
    RUN: run

  Setting:
    NEXT: next_settings_page
    Screen Set: setting.screen
    STA SET: setting.sta
    Startup-Config: setting.startup
    User-Defineds: setting.user_defined
    Tooltable #1: tool_table_1
    Tooltable #2: tool_table_2

  RUN:
    SD Run: sd_run.page1
    Drilling: drilling
    Tapping: tapping
    Cut Circle: cut_circle
    Cut Rectangle: cut_rectangle
    Rect Pocket: rectangular_pocket
    Circular Pocket: circular_pocket
    Cut RoundRect: cut_roundrect
    Surfacing: surfacing
    IO Monitor: io_monitor
    Slotting: slotting
    Mixed Wizards: mixed_wizards
    JOG: jog
    MDI: mdi
    SETTING: setting.main
```

------------------------------------------------------------------------

# 36. Веб-интерфейс ESP3D

**Источник:** страницы 150--152.

Это отдельный класс UI и не подчиняется сенсорной сетке 10 строк.

``` yaml
SCREEN_CLASS:
  WEB
  geometry:
    responsive: true
    x: percent
    y: document_flow
```

Веб-интерфейс содержит:

``` yaml
top_navigation:
  - Dashboard
  - POWSEND
  - ESP3D

main:
  left:
    - graphical jog control
    - axis/home controls
    - step selector
    - coordinate displays

  right:
    - machine status
    - override controls
    - spindle
    - coolant
    - probing
```

На отдельных страницах присутствуют:

``` yaml
web_functions:
  - SD file listing
  - refresh
  - delete
  - download
  - upload
  - controller parameter configuration
  - firmware upgrade
```

Руководство отдельно предупреждает, что одновременное управление машиной
несколькими устройствами не поддерживается.

------------------------------------------------------------------------

# 37. Что является реконструкцией, а что источником

``` yaml
source_levels:

  manual:
    значение/поведение явно описано текстом руководства

  screenshot:
    элемент или его расположение видно на изображении

  inferred:
    поведение/геометрия восстановлены по сочетанию изображения и текста

  unknown:
    в руководстве недостаточно информации
```

Для следующего этапа реверса рекомендуется не переводить `inferred` в
`manual`, пока это не подтверждено экспериментом.

------------------------------------------------------------------------

# 38. Главный вывод по архитектуре UI

Интерфейс POCENDER не является набором независимых экранов. Он построен
вокруг нескольких повторяющихся шаблонов:

``` text
Coordinate
Editor
Wizard
Settings Form
Fixed Table
Icon Menu
File Browser
Run Status
CAM Operation
I/O Matrix
```

При реализации достаточно сначала воспроизвести эти шаблоны, а затем
задавать конкретные наборы элементов и `manual_comment` для каждого
экрана.

------------------------------------------------------------------------

# 39. Поведение кнопок при удержании

Этот раздел задаёт **реализуемое псевдокодом поведение**, которое можно
непосредственно перенести, например, в JavaScript/TypeScript, C#,
Python, C++ или другой язык.

Важно: это **не утверждение о внутренней реализации оригинального
POCENDER**. Руководство явно описывает удержание для кнопок Jog
(непрерывное движение), а для кнопок изменения числовых параметров
алгоритм удержания не описан. Ниже задано унифицированное поведение для
реализации интерфейса.

## 39.1. Модель события кнопки

Для любой кнопки, изменяющей значение текстового числового поля:

``` yaml
HoldButton:
  target: Input
  delta: +1
  repeat:
    enabled: true
    initial_delay_ms: 400
    interval_ms: 120
  acceleration:
    enabled: true
    after_ms: 2000
    minimum_interval_ms: 40
```

Кнопка имеет три события:

``` text
PRESS
RELEASE
CANCEL
```

`CANCEL` нужен для случая, когда палец/курсор покинул кнопку или
интерфейс потерял фокус.

## 39.2. Основной псевдокод

``` text
state:
    held_button = NONE
    hold_timer = NONE
    repeat_timer = NONE
    hold_start_time = 0


function on_button_press(button):

    if button.disabled:
        return

    held_button = button
    hold_start_time = NOW()

    # Первое изменение происходит сразу.
    change_target_value(button, button.delta)

    # Через небольшую задержку начинается повтор.
    hold_timer = schedule(
        delay = button.initial_delay_ms,
        callback = start_repeat
    )


function start_repeat():

    if held_button == NONE:
        return

    repeat_timer = schedule(
        delay = current_interval(held_button),
        callback = repeat_change
    )


function repeat_change():

    if held_button == NONE:
        return

    button = held_button

    change_target_value(button, button.delta)

    repeat_timer = schedule(
        delay = current_interval(button),
        callback = repeat_change
    )


function on_button_release(button):

    if held_button != button:
        return

    cancel(hold_timer)
    cancel(repeat_timer)

    hold_timer = NONE
    repeat_timer = NONE
    held_button = NONE


function on_button_cancel(button):

    on_button_release(button)
```

## 39.3. Изменение значения текстового поля

Для числового `Input` значение сначала преобразуется в число.

``` text
function change_target_value(button, delta):

    input = button.target

    value = parse_number(input.text)

    if value is INVALID:
        value = input.default_value

    new_value = value + delta

    new_value = clamp(
        new_value,
        input.minimum,
        input.maximum
    )

    new_value = round_to_step(
        new_value,
        input.step
    )

    input.text = format_number(
        new_value,
        input.decimals
    )

    notify_value_changed(input)
```

Таким образом, одна и та же кнопка может работать с любым числовым
полем:

``` yaml
- id: feed_plus
  type: HoldButton
  text: "+"
  target: feed
  delta: +10

- id: feed_minus
  type: HoldButton
  text: "-"
  target: feed
  delta: -10

- id: spindle_plus
  type: HoldButton
  text: "+"
  target: spindle
  delta: +10

- id: spindle_minus
  type: HoldButton
  text: "-"
  target: spindle
  delta: -10
```

## 39.4. Интервалы повторения и ускорение

Функция `current_interval()` может быть простой, без ускорения:

``` text
function current_interval(button):

    return button.interval_ms
```

Или с ускорением при длительном удержании:

``` text
function current_interval(button):

    elapsed = NOW() - hold_start_time

    if button.acceleration.enabled == false:
        return button.interval_ms

    if elapsed < 2000:
        return button.interval_ms

    if elapsed < 4000:
        return button.interval_ms * 0.75

    if elapsed < 6000:
        return button.interval_ms * 0.50

    if elapsed < 8000:
        return button.interval_ms * 0.33

    return button.acceleration.minimum_interval_ms
```

Для станочного интерфейса ускорение следует применять осторожно:
изменение параметров с большим шагом может слишком быстро привести к
нежелательному значению.

## 39.5. Вариант без ускорения --- предпочтительный базовый вариант

Для первоначальной реализации рекомендуется:

``` yaml
hold:
  first_change: immediate
  initial_delay_ms: 400
  repeat_interval_ms: 120
  acceleration: false
```

Псевдокод становится:

``` text
on PRESS:
    change(value, delta)
    wait 400 ms
    while button_is_held:
        change(value, delta)
        wait 120 ms

on RELEASE:
    stop
```

Такое поведение проще проверить и одинаково работает для сенсорного
экрана, мыши и аппаратной кнопки.

## 39.6. Нажатие и удержание должны использовать одну функцию изменения

Не следует реализовывать короткое нажатие и удержание двумя различными
алгоритмами изменения значения.

Правильно:

``` text
PRESS
  -> change_target_value(+delta)
  -> start repeat

REPEAT
  -> change_target_value(+delta)
  -> repeat

RELEASE
  -> stop repeat
```

Это гарантирует одинаковую обработку:

-   минимального значения;
-   максимального значения;
-   шага;
-   количества знаков после запятой;
-   некорректного содержимого поля.

## 39.7. Границы значения

Если достигнута граница:

``` text
if value >= maximum and delta > 0:
    value = maximum
```

Повторное удержание после достижения `maximum` ничего не меняет.

Аналогично:

``` text
if value <= minimum and delta < 0:
    value = minimum
```

При этом таймер можно оставить активным либо остановить. Для UI
рекомендуется остановить его, чтобы не выполнять бесполезные операции:

``` text
if value == maximum and delta > 0:
    stop_repeat()

if value == minimum and delta < 0:
    stop_repeat()
```

## 39.8. Поле с разными шагами

Некоторые параметры могут иметь шаг `0.01`, `0.1`, `1`, `10` и т.д.

Поэтому кнопка не должна предполагать, что значение всегда целое:

``` yaml
Input:
  value: 1.25
  step: 0.01
  minimum: 0
  maximum: 100
  decimals: 2
```

Пример:

``` text
+ button:
    delta = +0.01

- button:
    delta = -0.01
```

После изменения:

``` text
1.25
1.26
1.27
1.28
...
```

## 39.9. Динамический шаг

Если необходимо менять значение одновременно с масштабом изменения,
кнопка может использовать функцию `get_delta()`:

``` text
function get_delta(button):

    if button.delta_mode == FIXED:
        return button.delta

    if button.delta_mode == INPUT_STEP:
        return button.target.step

    if button.delta_mode == GEAR:
        return button.target.step * current_gear

    return button.delta
```

Тогда одна кнопка может быть описана:

``` yaml
- id: jog_parameter_plus
  type: HoldButton
  text: "+"
  target: jog_parameter
  delta_mode: INPUT_STEP
```

## 39.10. Повторное нажатие другой кнопки

Если одновременно нажаты `+` и `-` одного поля, должна быть определена
политика. Для сенсорного интерфейса рекомендуется:

``` text
if new_button is opposite_direction_of(held_button):

    cancel_repeat(held_button)

    held_button = new_button
    start_new_hold(new_button)
```

То есть последнее нажатие получает управление.

## 39.11. Потеря фокуса

Обязательно прекращать удержание при потере окна/экрана:

``` text
on APPLICATION_DEACTIVATED:
    cancel_all_holds()

on SCREEN_CHANGED:
    cancel_all_holds()

on MODAL_DIALOG_OPENED:
    cancel_all_holds()
```

Это особенно важно для станочного интерфейса: кнопка не должна
продолжать изменять параметр после перехода на другой экран.

## 39.12. Связь с элементом `Input`

Для формализованного описания рекомендуется расширить `Input`:

``` yaml
- id: drilling.feedrate
  type: Input
  text: "Feedrate"
  value: 100
  min: 0
  max: 10000
  step: 1
  decimals: 0
  action: edit
  manual_comment: "Скорость подачи."

  hold:
    plus:
      enabled: true
      delta: +1
    minus:
      enabled: true
      delta: -1
    initial_delay_ms: 400
    interval_ms: 120
    acceleration: false
```

Если отдельные кнопки на экране отсутствуют, это свойство можно считать
логическим поведением виртуальных кнопок редактора.

## 39.13. Специальный случай: кнопки F+/F-, S+/S-

В руководстве для MDI и SD Run указаны кнопки:

``` yaml
F+/-:
  manual_comment: "Feed ±10%."

S+/-:
  manual_comment: "Spindle ±10%."
```

Их лучше моделировать не как изменение текста в поле на `10`, а как
изменение **override**:

``` text
function change_feed_override(direction):

    override = get_feed_override()

    override = override + direction * 10

    override = clamp(override, 0, 100)

    set_feed_override(override)
```

При удержании:

``` text
PRESS F+:
    change_feed_override(+1)
    start_repeat()

HOLD:
    change_feed_override(+1)
    repeat()

RELEASE:
    stop_repeat()
```

Аналогично для `F-`, `S+`, `S-`.

Это сохраняет различие между:

``` text
изменением параметра поля
```

и

``` text
изменением текущего override во время выполнения.
```

## 39.14. Специальный случай: Jog

Для Jog руководство явно задаёт другое семантическое правило:

``` text
SHORT PRESS:
    выполнить один шаг

HOLD:
    непрерывное движение
```

Поэтому для Jog нельзя автоматически использовать алгоритм изменения
текстового поля:

``` yaml
jog_positive:
  type: HoldButton
  short_press: single_jog_step
  hold: continuous_motion
```

Псевдокод:

``` text
on PRESS(axis_positive):

    start_time = NOW()
    jog_step(axis, direction)

    start_hold_detection()


on HOLD(axis_positive):

    start_continuous_jog(axis, direction)


on RELEASE(axis_positive):

    stop_continuous_jog(axis)
```

Здесь `jog_step()` и `continuous_jog()` являются разными операциями.

------------------------------------------------------------------------

# 40. Унифицированная модель интерактивного элемента

После добавления поведения удержания элемент управления может быть
описан следующим образом:

``` yaml
Control:
  id: unique_id
  type: Button | Input | HoldButton | Toggle | Select
  text: "..."
  rect: {x: 0, y: 0, w: 10, h: 1}

  state:
    normal: true
    disabled: false
    locked: false

  action:
    type: edit | command | navigation | toggle
    target: null
    delta: null

  hold:
    enabled: false
    initial_delay_ms: 400
    interval_ms: 120
    acceleration: false

  manual_comment: "..."
  source: "p. NN"
```

Для `manual_comment` сохраняется исходное правило:

``` text
manual_comment
    = только описание, поддерживаемое руководством

hold algorithm
    = алгоритм реализации интерфейса
```

То есть эти два свойства не смешиваются.

------------------------------------------------------------------------

# 41. Пример полного элемента

``` yaml
- id: drilling.feedrate
  type: Input
  text: "Feedrate"
  rect: {x: 50, y: 2, w: 20, h: 1}

  value:
    current: 100
    minimum: 0
    maximum: 10000
    step: 1
    decimals: 0

  buttons:
    plus:
      type: HoldButton
      action: increment
      delta: +1

    minus:
      type: HoldButton
      action: decrement
      delta: -1

  hold:
    enabled: true
    initial_delay_ms: 400
    interval_ms: 120
    acceleration: false

  manual_comment: "Скорость подачи."
  source: "p. 90"
```

Таким образом, описание содержит одновременно:

1.  геометрию;
2.  отображаемое значение;
3.  допустимый диапазон;
4.  шаг изменения;
5.  короткое нажатие;
6.  удержание;
7.  комментарий из руководства;
8.  источник.
