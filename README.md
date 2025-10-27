# XdevGDI: Нативный GDI / C# Overlay Фреймворк для Windows 💻

<p align="center">
  <img src="https://img.shields.io/badge/Language-C%23%20%7C%20C%2B%2B-blueviolet" alt="Languages">
  <img src="https://img.shields.io/badge/.NET%20Framework-4.7.2%2B-blue" alt=".NET Framework">
  <img src="https://img.shields.io/badge/Status-Active-brightgreen" alt="Status">
</p>

## Описание Проекта

Библиотека **XdevGDI** — это высокопроизводительный фреймворк для C# (.NET Framework), предназначенный для создания динамических GDI-эффектов и наложений (Overlay) прямо на рабочем столе Windows. Проект использует гибридный подход:
* **Нативный код C++ (DLL):** Для высокоскоростных, полноэкранных, сложных GDI-эффектов (например, инверсия, рябь, туннель).
* **C# GDI+ Overlay (Thread):** Для отрисовки пользовательских элементов (текст, иконки, Bitmap) в отдельном потоке.

---

## ⚙️ Требования и Поддерживаемые Фреймворки

### Поддерживаемые Фреймворки
| Фреймворк | Версия | Примечание |
| :--- | :--- | :--- |
| **.NET Framework** | **4.7.2** | Минимальная и целевая версия (`TestLibNET472`). |
| **.NET Framework** | **4.8** | Рекомендовано для лучшей совместимости с WinForms. |
| **ОС** | **Windows 7 / 8 / 10 / 11** | Только 64-битные системы (x64). |

### Требования к Среде (Рантайм)
* **Visual C++ Redistributable (x64):** Для корректной работы нативной DLL (`XdevGDINative.dll`) необходимо наличие **VC 2015-2022 Redistributable**.

---

## 📦 Установка и Интеграция

Для работы требуются два файла: **`XdevGDI.dll`** (C# Core) и **`XdevGDINative.dll`** (Нативный C++ код).

1.  **Добавьте Ссылку:** Включите **`XdevGDI.dll`** в качестве ссылки в ваш .NET Framework проект.
2.  **Размещение DLL:** Скопируйте файл **`XdevGDINative.dll`** в папку, где находится исполняемый файл (`.exe`) вашего приложения (обычно это `bin/Debug` или `bin/Release`).

---

## 🔑 Инициализация Ядра (Обязательно)

Перед использованием любых функций необходимо инициализировать ядро, передав **валидный лицензионный ключ** (`uint`).

> **ВАЖНО:** Использование невалидного ключа активирует водяной знак (watermark) через `MessageBox`.

```csharp
using XdevGDI;

// Замените на ваш актуальный лицензионный ключ!
private const uint YOUR_LICENSE_KEY = 0x00000000; 

public void ApplicationStart()
{
    // Запускает ядро, проверяет лицензию и стартует потоки отрисовки.
    GdiCore.Start(YOUR_LICENSE_KEY);
}

public void ApplicationStop()
{
    // Обязательная очистка и остановка всех потоков при завершении работы!
    GdiCore.EmergencyStop();
}
```
## 📋 Полный Справочник Методов (Методы **`GdiCore`**)

### 1. Общие Методы Управления

| Метод | Сигнатура | Описание |
| :--- | :--- | :--- |
| **`Start`** | **`Start(uint key)`** | Запускает GDI Core, проверяет лицензию и инициализирует все фоновые потоки. Должен быть вызван первым. |
| **`EmergencyStop`** | **`EmergencyStop()`** | Обязательная полная остановка всех потоков, очистка экрана от всех эффектов и высвобождение ресурсов. |
| **`IsLicensed`** | **`IsLicensed`** | Обязательная полная остановка всех потоков, очистка экрана от всех эффектов и высвобождение ресурсов. |
| **`ClearNativeEffect`** | **`ClearNativeEffect()`** | Сбрасывает текущий активный нативный C++ эффект (оверлей C# GDI+ не затрагивается). |

### 2. Нативные Эффекты GDI (C++)
Эти методы активируют полноэкранные графические преобразования, реализованные в **`XdevGDINative.dll`**.
| Метод | Сигнатура | Описание |
| :--- | :--- | :--- |
| **`SetZoomEffect`** | **`SetZoomEffect(float factor)`** | Применяет эффект масштабирования всего экрана. Фактор > 1.0 — увеличение; < 1.0 — уменьшение. |
| **`SetRippleEffect`** | **`SetRippleEffect(float strength)`** | Активирует эффект волновой ряби/искажения на экране. |
| **`SetTunnelEffect`** | **`SetTunnelEffect(float rate)`** | Создает эффект туннельного смещения (визуальное движение к центру/от центра). |
| **`SetMandelaEffect`** | **`SetMandelaEffect(float rate)`** | Применяет сложный эффект "мандалы" (узора) к изображению экрана. |
| **`SetInvertEffect`** | **`SetInvertEffect()`** | Применяет эффект инверсии цвета (негатив). |
| **`SetDrawCirclesEffect`** | **`SetDrawCirclesEffect()`** | Активирует эффект непрерывного рисования GDI-кругов/примитивов. |

### 3. Методы C# Overlay и Системных Иконок
Эти методы управляют отрисовкой в отдельном GDI+ потоке (Overlay).
| Метод | Сигнатура | Описание |
| :--- | :--- | :--- |
| **`SetTextEffect`** | **`SetTextEffect(string Text, string FontName, int Size, FontStyle Style, Color Color, Color BackColor, TextPosition Position`** | Устанавливает/обновляет текстовый оверлей с указанными параметрами. Передайте **`null`** для **`Text`**, чтобы очистить.. |
| **`SetSystemIconEffectDraw`** | **`SetSystemIconEffectDraw(SystemIconType type, IconRenderMode mode, int count, int delay))`** | Рендерит системные иконки Windows с заданным типом, режимом и количеством.. |

## 🧪 Подробные Примеры Кода
### 1. Пример
``` csharp
using XdevGDI;
using System.Drawing; // Для Color и FontStyle
using System.Threading;

public void DemoEffects()
{
    // 1. Активация нативного эффекта (Zoom)
    GdiCore.SetZoomEffect(1.001f); 
    Console.WriteLine("Zoom Effect Active...");
    Thread.Sleep(3000);

    // 2. Добавление C# Overlay (Текст)
    GdiCore.SetTextEffect(
        "XdevGDI TEST", 
        "Arial Black", 
        90, 
        FontStyle.Bold, 
        Color.Lime, 
        Color.FromArgb(128, Color.DarkBlue), 
        TextPosition.TopCenter 
    );
    Console.WriteLine("Text Overlay Active...");
    Thread.Sleep(3000);

    // 3. Смена нативного эффекта и очистка текста
    GdiCore.SetInvertEffect(); 
    GdiCore.SetTextEffect(null, null, 0, 0, Color.Empty, Color.Empty, TextPosition.Center);
    Console.WriteLine("Invert Effect Active, Text Cleared...");
    Thread.Sleep(3000);

    // 4. Демонстрация системных иконок
    GdiCore.SetSystemIconEffectDraw(
        SystemIconType.IconError, 
        IconRenderMode.ModeFollowCursor, 
        1, 
        0
    );
    Console.WriteLine("Icon Following Cursor Active...");
    Thread.Sleep(3000);
    
    // 5. Полная остановка
    GdiCore.EmergencyStop();
    Console.WriteLine("GdiCore Stopped.");
}
```
### 2. Полный пример кода + Windows Forms

<img width="534" height="210" alt="image" src="https://github.com/user-attachments/assets/c8bf4be8-6a3a-4575-8604-754b0494782a" />

**`Form1.Designer.cs`**

``` csharp
namespace TestLibNET472
{
    partial class Form1
    {
        /// <summary>
        /// Обязательная переменная конструктора.
        /// </summary>
        private System.ComponentModel.IContainer components = null;

        /// <summary>
        /// Освободить все используемые ресурсы.
        /// </summary>
        /// <param name="disposing">истинно, если управляемый ресурс должен быть удален; иначе ложно.</param>
        protected override void Dispose(bool disposing)
        {
            if (disposing && (components != null))
            {
                components.Dispose();
            }
            base.Dispose(disposing);
        }

        #region Код, автоматически созданный конструктором форм Windows

        /// <summary>
        /// Требуемый метод для поддержки конструктора — не изменяйте 
        /// содержимое этого метода с помощью редактора кода.
        /// </summary>
        private void InitializeComponent()
        {
            this.button1 = new System.Windows.Forms.Button();
            this.button2 = new System.Windows.Forms.Button();
            this.statusStrip1 = new System.Windows.Forms.StatusStrip();
            this.labelStatus = new System.Windows.Forms.ToolStripStatusLabel();
            this.label1 = new System.Windows.Forms.Label();
            this.statusStrip1.SuspendLayout();
            this.SuspendLayout();
            // 
            // button1
            // 
            this.button1.Location = new System.Drawing.Point(18, 31);
            this.button1.Name = "button1";
            this.button1.Size = new System.Drawing.Size(75, 23);
            this.button1.TabIndex = 0;
            this.button1.Text = "button1";
            this.button1.UseVisualStyleBackColor = true;
            this.button1.Click += new System.EventHandler(this.button1_Click);
            // 
            // button2
            // 
            this.button2.Location = new System.Drawing.Point(99, 31);
            this.button2.Name = "button2";
            this.button2.Size = new System.Drawing.Size(75, 23);
            this.button2.TabIndex = 1;
            this.button2.Text = "button2";
            this.button2.UseVisualStyleBackColor = true;
            // 
            // statusStrip1
            // 
            this.statusStrip1.Items.AddRange(new System.Windows.Forms.ToolStripItem[] {
            this.labelStatus});
            this.statusStrip1.Location = new System.Drawing.Point(0, 149);
            this.statusStrip1.Name = "statusStrip1";
            this.statusStrip1.Size = new System.Drawing.Size(519, 22);
            this.statusStrip1.TabIndex = 2;
            this.statusStrip1.Text = "statusStrip1";
            // 
            // labelStatus
            // 
            this.labelStatus.Name = "labelStatus";
            this.labelStatus.Size = new System.Drawing.Size(64, 17);
            this.labelStatus.Text = "labelStatus";
            // 
            // label1
            // 
            this.label1.AutoSize = true;
            this.label1.Font = new System.Drawing.Font("Microsoft Sans Serif", 15.75F, System.Drawing.FontStyle.Bold, System.Drawing.GraphicsUnit.Point, ((byte)(204)));
            this.label1.Location = new System.Drawing.Point(13, 3);
            this.label1.Name = "label1";
            this.label1.Size = new System.Drawing.Size(104, 25);
            this.label1.TabIndex = 3;
            this.label1.Text = "XdevGDI";
            // 
            // Form1
            // 
            this.AutoScaleDimensions = new System.Drawing.SizeF(6F, 13F);
            this.AutoScaleMode = System.Windows.Forms.AutoScaleMode.Font;
            this.ClientSize = new System.Drawing.Size(519, 171);
            this.Controls.Add(this.label1);
            this.Controls.Add(this.statusStrip1);
            this.Controls.Add(this.button2);
            this.Controls.Add(this.button1);
            this.Font = new System.Drawing.Font("Microsoft Sans Serif", 8.25F, System.Drawing.FontStyle.Regular, System.Drawing.GraphicsUnit.Point, ((byte)(204)));
            this.Name = "Form1";
            this.Text = "Хачапури dev Lib Test Application .NET Framework 4.7.2";
            this.statusStrip1.ResumeLayout(false);
            this.statusStrip1.PerformLayout();
            this.ResumeLayout(false);
            this.PerformLayout();

        }

        #endregion

        private System.Windows.Forms.Button button1;
        private System.Windows.Forms.Button button2;
        private System.Windows.Forms.StatusStrip statusStrip1;
        private System.Windows.Forms.ToolStripStatusLabel labelStatus;
        private System.Windows.Forms.Label label1;
    }
}
```

**`Form1.cs`**

```csharp
using System;
using System.Drawing;
using System.Threading;
using System.Windows.Forms;
using XdevGDI;

namespace TestLibNET472
{
    public partial class Form1 : Form
    {
        private const uint CORRECT_KEY = 0x00000000;
        public Form1()
        {
            InitializeComponent();
            this.FormClosing += Form1_FormClosing;

            // Предполагаем наличие элементов button1, button2 и labelStatus
            button1.Text = "ЗАПУСТИТЬ ВСЕ 10 GDI ЭФФЕКТОВ";
            button2.Text = "ОСТАНОВИТЬ";

            button2.Enabled = false;
            SetStatus("Готов к запуску.");
        }

        private void button1_Click(object sender, EventArgs e)
        {
            button1.Enabled = false;
            button2.Enabled = true;

            // 1. Запуск ядра
            GdiCore.Start(CORRECT_KEY);

            // 2. Запускаем последовательность эффектов в ОТДЕЛЬНОМ ПОТОКЕ
            Thread effectThread = new Thread(RunAllEffectsSequence)
            {
                IsBackground = true,
                Name = "XdevGDI_TestSequence"
            };
            effectThread.Start();
        }

        private void button2_Click(object sender, EventArgs e)
        {
            GdiCore.EmergencyStop();
        }

        private void Form1_FormClosing(object sender, FormClosingEventArgs e)
        {

            GdiCore.EmergencyStop();
        }

        #region XdevGDI
        // --- ГЛАВНАЯ ПОСЛЕДОВАТЕЛЬНОСТЬ ЭФФЕКТОВ (В ОТДЕЛЬНОМ ПОТОКЕ) ---
        private void RunAllEffectsSequence()
        {
            try
            {

                // Длительность каждого шага
                const int STEP_DELAY_MS = 3000;
                int step = 0;

                // === I. ТЕСТ НАТИВНЫХ ЭФФЕКТОВ (C++ DLL) ===

                // 1. Zoom Effect
                step++;
                SetStatus($"{step}/13: Zoom Effect (Сжатие 0.98f)...");
                GdiCore.SetZoomEffect(0.98f);
                Thread.Sleep(STEP_DELAY_MS);

                // 2. Invert Effect
                step++;
                SetStatus($"{step}/13: Invert Effect (Инверсия)...");
                GdiCore.SetInvertEffect();
                Thread.Sleep(STEP_DELAY_MS);

                // 3. Ripple Effect
                step++;
                SetStatus($"{step}/13: Ripple Effect (Рябь 0.1f)...");
                GdiCore.SetRippleEffect(0.1f);
                Thread.Sleep(STEP_DELAY_MS);

                // 4. Wave Effect
                step++;
                SetStatus($"{step}/13: Wave Effect (Волна 5.0f)...");
                GdiCore.SetWaveEffect(5.0f);
                Thread.Sleep(STEP_DELAY_MS);

                // 5. Hue Shift Effect
                step++;
                SetStatus($"{step}/13: Hue Shift Effect (Сдвиг цвета 0.5f)...");
                GdiCore.SetHueShiftEffect(0.5f);
                Thread.Sleep(STEP_DELAY_MS);

                // 6. Draw Circles Effect
                step++;
                SetStatus($"{step}/13: Draw Circles Effect (Круги)...");
                GdiCore.SetDrawCirclesEffect();
                Thread.Sleep(STEP_DELAY_MS);

                // 7. Color Cycle Effect
                step++;
                SetStatus($"{step}/13: Color Cycle Effect (Цикл 1.5f)...");
                GdiCore.SetColorCycleEffect(1.5f);
                Thread.Sleep(STEP_DELAY_MS);

                // 8. Mandela Effect
                step++;
                SetStatus($"{step}/13: Mandela Effect (15.0f)...");
                GdiCore.SetMandelaEffect(15.0f);
                Thread.Sleep(STEP_DELAY_MS);

                // 9. Tunnel Effect
                step++;
                SetStatus($"{step}/13: Tunnel Effect (Смещение 1.0f)...");
                GdiCore.SetTunnelEffect(1.0f);
                Thread.Sleep(STEP_DELAY_MS);

                GdiCore.ClearNativeEffect(); // Сброс GDI-эффекта


                // === II. ТЕСТ НАТИВНЫХ ИКОНОК (C++ DLL) ===

                // 10. Иконка "Error" (Следование)
                step++;
                SetStatus($"{step}/13: Иконка 'Error' (Следование за курсором)...");
                // ИСПРАВЛЕНО имя метода
                GdiCore.SetSystemIconEffectDraw(SystemIconType.IconError, IconRenderMode.ModeFollowCursor, 1, 0);
                Thread.Sleep(STEP_DELAY_MS);

                // 11. Иконка "Question" (Рандом, 20 штук)
                step++;
                SetStatus($"{step}/13: Иконка 'Question' (20 рандомных)...");
                // ИСПРАВЛЕНО имя метода
                GdiCore.SetSystemIconEffectDraw(SystemIconType.IconQuestion, IconRenderMode.ModeRandomPosition, 20, 0);
                Thread.Sleep(STEP_DELAY_MS);

                // Очистка иконок (вызов с SystemIconType.IconNone или ClearNativeEffect)
                GdiCore.SetSystemIconEffectDraw(SystemIconType.IconNone, IconRenderMode.ModeFollowCursor);


                // === III. ТЕСТ C# OVERLAY (GDI+) ===

                // 12. C# Icon Overlay (Random)
                step++;
                SetStatus($"{step}/13: C# Icon Overlay (Рандомный квадрат)...");
                // Создаем простой Bitmap для C# Overlay
                using (Bitmap customIcon = new Bitmap(48, 48))
                using (Graphics g = Graphics.FromImage(customIcon))
                {
                    g.FillRectangle(Brushes.Magenta, 0, 0, 48, 48);
                    g.DrawString("C#", new Font("Arial", 20, FontStyle.Bold), Brushes.White, 5, 10);
                    // SetIconEffect использует C# Overlay Thread
                    GdiCore.SetIconEffect(IconFollowMode.ModeRandomPosition, customIcon);
                }
                Thread.Sleep(STEP_DELAY_MS);
                GdiCore.SetIconEffect(IconFollowMode.IconNone, null); // Очистка иконки

                // 13. Text Overlay (C# GDI+)
                step++;
                SetStatus($"{step}/13: Text Overlay (ТЕСТ УСПЕШЕН)...");
                // SetTextEffect использует C# Overlay Thread
                GdiCore.SetTextEffect("ТЕСТ УСПЕШЕН", "Arial", 64, FontStyle.Bold, Color.Yellow, Color.Red, TextPosition.Center);
                Thread.Sleep(STEP_DELAY_MS);
                // Очистка текста
                GdiCore.SetTextEffect(null, null, 0, 0, Color.Empty, Color.Empty, TextPosition.Center);

                SetStatus("Все эффекты протестированы.");
            }
            catch (Exception ex)
            {
                SetStatus($"КРИТИЧЕСКАЯ ОШИБКА: {ex.Message}");
            }
            finally
            {
                // Обязательная очистка и восстановление UI в главном потоке
                this.Invoke((MethodInvoker)delegate
                {
                    GdiCore.EmergencyStop();
                    button1.Enabled = true;
                    button2.Enabled = false;
                    SetStatus("Остановлено. Готово к повторному запуску.");
                });
            }
        }

        #endregion

        private void SetStatus(string status)
        {
            if (this.InvokeRequired)
            {
                this.Invoke((MethodInvoker)delegate
                {
                    this.labelStatus.Text = status;
                });
            }
            else
            {
                this.labelStatus.Text = status;
            }
        }
    }
}
```

## 🧩 Справочник Перечислений (Enums)
### Enum: `TextPosition`
Определяет стандартные позиции на экране для размещения текстового оверлея.

| Член (Member) | Описание |
| :--- | :--- |
| **`Center`** | Центр экрана. |
| **`TopLeft`** | Верхний левый угол. |
| **`TopCenter`** | Верхний центр. |
| **`TopRight`** | Верхний правый угол. |
| **`BottomLeft`** | Нижний левый угол. |
| **`BottomCenter`** | Нижний центр. |
| **`BottomRight`** | Нижний правый угол. |

### Enum: `SystemIconType`
Типы стандартных системных иконок Windows для отрисовки.

| Член (Member) | Описание |
| :--- | :--- |
| **`IconError`** | Красный значок ошибки. |
| **`IconWarning`** | Желтый значок предупреждения. |
| **`IconQuestion`** | Синий значок вопроса. |
| **`IconInformationt`** | Синий значок информации. |

### Enum: `IconRenderMode`
Типы стандартных системных иконок Windows для отрисовки.

| Член (Member) | Описание |
| :--- | :--- |
| **`ModeRandomPosition`** | Иконки появляются в случайных местах экрана. |
| **`ModeFollowCursor`** | Одна или несколько иконок следуют за курсором. |
| **`ModeStatic`** | Иконки остаются на месте после появления. |

## 📞 Bug report
Если вы обнаружили ошибку, неточность в документации или хотите предложить новую функцию, пожалуйста, используйте GitHub Issues:
- Сообщить о баге/ошибке: [Создать Issue на GitHub](https://github.com/xachapyri-dev/XdevGDI/issues)

## ⚠️ Предупреждение и отказ от ответственности

**ВНИМАНИЕ!**

Поскольку библиотека XdevGDI использует непосредственное взаимодействие с графическим интерфейсом Windows (GDI) и может быстро изменять содержимое экрана, пожалуйста, ознакомьтесь со следующими предупреждениями:

* **Риск для здоровья (Эпилепсия):** Некоторые графические эффекты, включая быстрые изменения цвета, инверсию или мерцание, могут вызвать эпилептические припадки у людей с фоточувствительной эпилепсией. **Используйте библиотеку на свой страх и риск.**
* **Системный Риск:** Любые эксперименты с GDI-эффектами могут потенциально привести к сбоям в работе операционной системы, временным зависаниям или ошибкам отрисовки.
* **Отказ от Ответственности:** Скачивая и используя эту программу, **вы берёте на себя все риски**. Автор не несёт ответственности за возможный ущерб вашей системе, потерю данных, повреждение оборудования или вред здоровью, включая, но не ограничиваясь, риском эпилептических припадков.
* **Цель Использования:** Приложение и библиотека предоставляются **исключительно в ознакомительных или образовательных целях**. Вы несёте полную ответственность за использование приложения на своём устройстве и за соблюдение действующих правил и законов.
