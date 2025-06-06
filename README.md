# 📊 Power BI Dynamic Time Intelligence Collection

<div align="center">

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-FF6F00?style=for-the-badge&logo=microsoft&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Production%20Ready-brightgreen?style=for-the-badge)

*A comprehensive collection of DAX measures for dynamic time intelligence in Power BI*

</div>

---

## 🚀 Overview

This repository contains a complete set of **production-ready DAX measures** for implementing dynamic time intelligence in Power BI reports. These measures automatically adapt to user selections and handle current period scenarios intelligently.

### ✨ Key Features

- 🎯 **Dynamic Period Calculation** - Automatically adapts based on slicer selections
- 📅 **Smart Current Period Handling** - Excludes incomplete current day data
- 🔄 **Period Comparison** - Compare current vs previous periods effortlessly
- 🏷️ **Dynamic Labels** - Automatically generated date range labels
- ⚡ **Performance Optimized** - Uses efficient DAX patterns
- 📱 **Mobile Friendly** - Works across all Power BI platforms

---

## 📋 Table of Contents

- [Installation](#-installation)
- [Prerequisites](#-prerequisites)
- [Available Measures](#-available-measures)
- [Usage Examples](#-usage-examples)
- [Configuration](#-configuration)
- [Best Practices](#-best-practices)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🛠 Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/powerbi-time-intelligence.git
   ```

2. **Copy the DAX measures** from the sections below into your Power BI model

3. **Ensure your Date Table** has the required columns (see [Prerequisites](#-prerequisites))

4. **Create the base measure** `[Total Clicks]` or replace with your metric name

---

## ✅ Prerequisites

Your Date Table must include these columns:

| Column | Type | Description |
|--------|------|-------------|
| `Date` | Date | Primary date column |
| `Year` | Integer | Year number (e.g., 2024) |
| `MonthNumber` | Integer | Month number (1-12) |
| `WeekNumber` | Integer | Week number of year |

> 💡 **Tip**: Use Power BI's built-in date table or create one using `CALENDAR()` function

---

## 📊 Available Measures

### 🎯 Current Period Measures

<details>
<summary><strong>Click to expand Current Period Measures</strong></summary>

#### 📅 Selected Day Clicks
```dax
Selected Day Clicks =
VAR MaxSelectedDate = MAX('DateTable'[Date])
RETURN
    CALCULATE(
        [Total Clicks],
        'DateTable'[Date] = MaxSelectedDate
    )
```

#### 📆 Current Week Clicks (Dynamic)
```dax
Current Week Clicks (Dynamic) =
VAR MaxSelectedDate = MAX('DateTable'[Date])
VAR MaxSelectedDateWeekNum = WEEKNUM(MaxSelectedDate, 2) // Monday start
VAR MaxSelectedDateYear = YEAR(MaxSelectedDate)
RETURN
    CALCULATE(
        [Total Clicks],
        FILTER(
            ALL('DateTable'),
            'DateTable'[WeekNumber] = MaxSelectedDateWeekNum &&
            'DateTable'[Year] = MaxSelectedDateYear
        )
    )
```

#### 🗓️ Selected Month Clicks (Dynamic)
```dax
Selected Month Clicks (Dynamic) =
VAR MaxSelectedDate = MAX('DateTable'[Date])
VAR TargetYear = YEAR(MaxSelectedDate)
VAR TargetMonth = MONTH(MaxSelectedDate)
RETURN
    CALCULATE(
        [Total Clicks],
        FILTER(
            ALL('DateTable'),
            'DateTable'[Year] = TargetYear &&
            'DateTable'[MonthNumber] = TargetMonth
        )
    )
```

#### 📋 Selected Three Months Clicks
```dax
Selected Three Months Clicks =
VAR MaxSelectedDate = MAX('DateTable'[Date])
RETURN
    CALCULATE(
        [Total Clicks],
        DATESINPERIOD(
            'DateTable'[Date],
            MaxSelectedDate,
            -3,
            MONTH
        )
    )
```

#### 🗂️ Selected Year Clicks
```dax
Selected Year Clicks =
VAR MaxSelectedDate = MAX('DateTable'[Date])
VAR TargetYear = YEAR(MaxSelectedDate)
RETURN
    CALCULATE(
        [Total Clicks],
        FILTER(
            ALL('DateTable'),
            'DateTable'[Year] = TargetYear
        )
    )
```

</details>

### ⏮️ Previous Period Measures

<details>
<summary><strong>Click to expand Previous Period Measures</strong></summary>

#### 📅 Previous Day Clicks
```dax
Previous Day Clicks =
VAR MaxSelectedDate = MAX('DateTable'[Date])
VAR PreviousDate = MaxSelectedDate - 1
RETURN
    CALCULATE(
        [Total Clicks],
        'DateTable'[Date] = PreviousDate
    )
```

#### 📆 Previous Week Clicks (Dynamic)
```dax
Previous Week Clicks (Dynamic) =
VAR MaxSelectedDate = MAX('DateTable'[Date])
VAR DateForPreviousWeek = MaxSelectedDate - 7
VAR PreviousWeekNum = WEEKNUM(DateForPreviousWeek, 2)
VAR PreviousWeekYear = YEAR(DateForPreviousWeek)
RETURN
    CALCULATE(
        [Total Clicks],
        FILTER(
            ALL('DateTable'),
            'DateTable'[WeekNumber] = PreviousWeekNum &&
            'DateTable'[Year] = PreviousWeekYear
        )
    )
```

#### 🗓️ Previous Month Clicks (Dynamic)
```dax
Previous Month Clicks (Dynamic) =
VAR MaxSelectedDate = MAX('DateTable'[Date])
VAR DateInPreviousMonth = EDATE(MaxSelectedDate, -1)
VAR TargetYear = YEAR(DateInPreviousMonth)
VAR TargetMonth = MONTH(DateInPreviousMonth)
RETURN
    CALCULATE(
        [Total Clicks],
        FILTER(
            ALL('DateTable'),
            'DateTable'[Year] = TargetYear &&
            'DateTable'[MonthNumber] = TargetMonth
        )
    )
```

#### 📋 Previous Three Months Clicks (Recommended)
```dax
Previous Three Months Clicks (Recommended) =
VAR MaxSelectedDate = MAX('DateTable'[Date])
RETURN
    CALCULATE(
        [Total Clicks],
        DATEADD(
            DATESINPERIOD(
                'DateTable'[Date],
                MaxSelectedDate,
                -3,
                MONTH
            ),
            -3,
            MONTH
        )
    )
```

#### 🗂️ Previous Year Clicks
```dax
Previous Year Clicks =
VAR MaxSelectedDate = MAX('DateTable'[Date])
VAR TargetPreviousYear = YEAR(MaxSelectedDate) - 1
RETURN
    CALCULATE(
        [Total Clicks],
        FILTER(
            ALL('DateTable'),
            'DateTable'[Year] = TargetPreviousYear
        )
    )
```

</details>

### 🏷️ Dynamic Label Measures

<details>
<summary><strong>Click to expand Label Measures</strong></summary>

#### 📅 Current Day Label
```dax
Current Day Label =
VAR AnchorDate = MAX('Date Table'[Date])
VAR IsToday = (AnchorDate = TODAY())
VAR DisplayDate = IF(IsToday, TODAY() - 1, AnchorDate)
RETURN
    FORMAT(DisplayDate, "dd mmm yyyy")
```

#### 📆 Current Week Label
```dax
Current Week Label =
VAR AnchorDate = MAX('Date Table'[Date])
VAR StartOfWeek = AnchorDate - WEEKDAY(AnchorDate, 2) + 1
VAR CurrentWeekStart = TODAY() - WEEKDAY(TODAY(), 2) + 1
VAR IsCurrentWeek = (StartOfWeek = CurrentWeekStart)
VAR EndDate = IF(IsCurrentWeek, TODAY() - 1, StartOfWeek + 6)
RETURN
    FORMAT(StartOfWeek, "d mmm yyyy") & " - " & FORMAT(EndDate, "dd mmm yyyy")
```

#### 🗓️ Current Month Label
```dax
Current 1 Calendar Month Label =
VAR AnchorDate = MAX('Date Table'[Date])
VAR StartDate = DATE(YEAR(AnchorDate), MONTH(AnchorDate), 1)
VAR CurrentMonth = MONTH(TODAY())
VAR CurrentYear = YEAR(TODAY())
VAR AnchorMonth = MONTH(AnchorDate)
VAR AnchorYear = YEAR(AnchorDate)
VAR IsCurrentMonth = (AnchorMonth = CurrentMonth && AnchorYear = CurrentYear)
VAR EndDate = IF(IsCurrentMonth, TODAY() - 1, EOMONTH(AnchorDate, 0))
RETURN
    FORMAT(StartDate, "d mmm yyyy") & " - " & FORMAT(EndDate, "dd mmm yyyy")
```

#### 📋 Selected 3 Calendar Months Label
```dax
Selected 3 Calendar Months Label =
VAR AnchorDate = MAX('Date Table'[Date])
VAR DateInStartMonth = EDATE(AnchorDate, -2)
VAR StartDate = DATE(YEAR(DateInStartMonth), MONTH(DateInStartMonth), 1)
VAR CurrentMonth = MONTH(TODAY())
VAR CurrentYear = YEAR(TODAY())
VAR AnchorMonth = MONTH(AnchorDate)
VAR AnchorYear = YEAR(AnchorDate)
VAR IsCurrentMonth = (AnchorMonth = CurrentMonth && AnchorYear = CurrentYear)
VAR EndDate = IF(IsCurrentMonth, TODAY() - 1, EOMONTH(AnchorDate, 0))
RETURN
    FORMAT(StartDate, "d mmm yyyy") & " - " & FORMAT(EndDate, "dd mmm yyyy")
```

#### 🗂️ Current Year Label
```dax
Current Year Label =
VAR AnchorDate = MAX('Date Table'[Date])
VAR StartDate = DATE(YEAR(AnchorDate), 1, 1)
VAR CurrentYear = YEAR(TODAY())
VAR AnchorYear = YEAR(AnchorDate)
VAR IsCurrentYear = (AnchorYear = CurrentYear)
VAR EndDate = IF(IsCurrentYear, TODAY() - 1, DATE(AnchorYear, 12, 31))
RETURN
    FORMAT(StartDate, "d mmm yyyy") & " - " & FORMAT(EndDate, "dd mmm yyyy")
```

</details>

---

## 💡 Usage Examples

### Basic Implementation

1. **Add to your report**: Copy the desired measures to your Power BI model
2. **Create cards/visuals**: Use the measures in cards, tables, or charts
3. **Add date slicer**: Connect a date slicer to enable dynamic functionality

### Advanced Usage

```dax
// Custom KPI with Growth Calculation
Week over Week Growth % = 
DIVIDE(
    [Current Week Clicks (Dynamic)] - [Previous Week Clicks (Dynamic)],
    [Previous Week Clicks (Dynamic)],
    0
) * 100
```

### Dashboard Example

| Visual Type | Measure | Label Measure |
|-------------|---------|---------------|
| Card | `[Current Week Clicks (Dynamic)]` | `[Current Week Label]` |
| Card | `[Previous Week Clicks (Dynamic)]` | `[Previous Week Label]` |
| Line Chart | Both measures for trend analysis | - |

---

## ⚙️ Configuration

### Customization Options

1. **Change base measure**: Replace `[Total Clicks]` with your metric
2. **Adjust week start**: Modify WEEKNUM parameter (1=Sunday, 2=Monday)
3. **Date formats**: Customize FORMAT functions for your locale
4. **Table names**: Update table references to match your model

### Performance Tips

- ✅ Use calculated columns for Year, Month, Week in your date table
- ✅ Mark your date table as a date table in Power BI
- ✅ Create relationships properly between fact and date tables
- ❌ Avoid complex calculations in visual-level filters

---

## 🎯 Best Practices

### 📋 Do's
- Always test measures with different date selections
- Use consistent naming conventions
- Document complex logic with comments
- Test performance with large datasets

### ❌ Don'ts
- Don't hardcode dates in measures
- Avoid nested CALCULATE functions where possible
- Don't ignore current period edge cases
- Don't skip testing with incomplete current periods

---

## 🤝 Contributing

We welcome contributions! Here's how you can help:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/AmazingFeature`)
3. **Commit** your changes (`git commit -m 'Add some AmazingFeature'`)
4. **Push** to the branch (`git push origin feature/AmazingFeature`)
5. **Open** a Pull Request

### Contribution Guidelines

- ✅ Follow existing code style
- ✅ Add comments for complex logic
- ✅ Test thoroughly before submitting
- ✅ Update documentation if needed

---

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- Power BI Community for inspiration and best practices
- DAX experts who shared knowledge and patterns
- Contributors who helped improve these measures

---

## 📞 Support

- 🐛 **Issues**: [Create an issue](../../issues)
- 💬 **Discussions**: [Join the discussion](../../discussions)
- ⭐ **Star this repo** if it helped you!

---

<div align="center">

**Made with ❤️ for the Power BI community**

![GitHub stars](https://img.shields.io/github/stars/yourusername/powerbi-time-intelligence?style=social)
![GitHub forks](https://img.shields.io/github/forks/yourusername/powerbi-time-intelligence?style=social)

</div>
