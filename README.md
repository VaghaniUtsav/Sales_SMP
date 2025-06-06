Current Week Clicks (Dynamic) =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] ) // Gets the latest date in the current filter context (from slicer)
VAR MaxSelectedDateWeekNum = WEEKNUM ( MaxSelectedDate, 2 ) // Week starts on Monday
VAR MaxSelectedDateYear = YEAR ( MaxSelectedDate )
RETURN
    CALCULATE (
        [Total Clicks],
        FILTER (
            ALL ( 'DateTable' ), // Remove any existing filter on DateTable to apply new week/year filter
            'DateTable'[WeekNumber] = MaxSelectedDateWeekNum &&
            'DateTable'[Year] = MaxSelectedDateYear
        )
    )


Previous Week Clicks (Dynamic) =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] ) // Latest date in current filter context
VAR DateForPreviousWeek = MaxSelectedDate - 7 // Go back 7 days to land in the previous week
VAR PreviousWeekNum = WEEKNUM ( DateForPreviousWeek, 2 )
VAR PreviousWeekYear = YEAR ( DateForPreviousWeek )
RETURN
    CALCULATE (
        [Total Clicks],
        FILTER (
            ALL ( 'DateTable' ),
            'DateTable'[WeekNumber] = PreviousWeekNum &&
            'DateTable'[Year] = PreviousWeekYear
        )
    )


Selected Month Clicks (Dynamic) =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )
VAR TargetYear = YEAR ( MaxSelectedDate )
VAR TargetMonth = MONTH ( MaxSelectedDate )
RETURN
    CALCULATE (
        [Total Clicks],
        FILTER (
            ALL ( 'DateTable' ), // Use ALL to apply new month/year filter
            'DateTable'[Year] = TargetYear &&
            'DateTable'[MonthNumber] = TargetMonth
        )
    )


Previous Month Clicks (Dynamic) =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )
VAR DateInPreviousMonth = EDATE ( MaxSelectedDate, -1 )
VAR TargetYear = YEAR ( DateInPreviousMonth )
VAR TargetMonth = MONTH ( DateInPreviousMonth )
RETURN
    CALCULATE (
        [Total Clicks],
        FILTER (
            ALL ( 'DateTable' ),
            'DateTable'[Year] = TargetYear &&
            'DateTable'[MonthNumber] = TargetMonth
        )
    )


Selected Three Months Clicks =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )
RETURN
    CALCULATE (
        [Total Clicks],
        DATESINPERIOD (
            'DateTable'[Date],
            MaxSelectedDate,
            -3,
            MONTH
        )
    )

Previous Three Months Clicks =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )
VAR EndOfCurrentThreeMonthPeriod = MaxSelectedDate
VAR StartOfCurrentThreeMonthPeriod = EDATE(MaxSelectedDate, -3) // This is actually the start of the -2 month from max date, DATESINPERIOD includes current
                                                              // Let's adjust logic to be more precise for previous period

// First, define the END of the "current" 3-month period (which is MaxSelectedDate)
// Then define the START of the "current" 3-month period.
// The "previous" 3-month period ends the day before the "current" 3-month period starts.

VAR EndDateOfSelectedPeriod = MAX('DateTable'[Date])
// DATESINPERIOD includes the MaxSelectedDate, so its start is roughly MaxSelectedDate - 3 months + 1 day.
// To get the period *before* this:
VAR StartOfSelectedThreeMonthsApprox = EDATE(EndDateOfSelectedPeriod, -3) // This will be start of the month 2 months ago if Max is end of month
                                                                     // More accurately:
VAR EndOfPreviousThreeMonths = DATEADD(STARTOFMONTH(EDATE(EndDateOfSelectedPeriod, -2)), -1, DAY)
// This is complex. Let's use DATEADD on the DATESINPERIOD result.

RETURN
    CALCULATE (
        [Total Clicks],
        DATEADD(
            DATESINPERIOD (
                'DateTable'[Date],
                MaxSelectedDate,
                -3,
                MONTH
            ),
            -3,
            MONTH
        )
    )


Selected Year Clicks =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )
VAR TargetYear = YEAR ( MaxSelectedDate )
RETURN
    CALCULATE (
        [Total Clicks],
        FILTER (
            ALL ( 'DateTable' ),
            'DateTable'[Year] = TargetYear
        )
    )

Previous Year Clicks =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )
VAR TargetPreviousYear = YEAR ( MaxSelectedDate ) - 1
RETURN
    CALCULATE (
        [Total Clicks],
        FILTER (
            ALL ( 'DateTable' ),
            'DateTable'[Year] = TargetPreviousYear
        )
    )


The first argument to 'STARTOFMONTH' must specify a column.

Previous Three Months Clicks (Recommended) =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )
RETURN
    CALCULATE (
        [Total Clicks],
        DATEADD(
            DATESINPERIOD (
                'DateTable'[Date], // The date column from your Date Table
                MaxSelectedDate,   // The anchor date (latest date from slicer)
                -3,                // The number of intervals to go back
                MONTH              // The interval type
            ),
            -3,                    // Shift this resulting period back by 3
            MONTH                  // The interval type for the shift
        )
    )


Previous Three Months Clicks (Explicit) =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )

// Determine the 3-full-month period that MaxSelectedDate's month is the end of.
VAR EndOfSelectedMonthsWindow = ENDOFMONTH(MaxSelectedDate)
VAR StartOfSelectedMonthsWindow = STARTOFMONTH(EDATE(MaxSelectedDate, -2))

// The previous 3-full-month period ends the day before the "Selected Months Window" starts.
VAR EndDateOfPreviousThreeMonths = DATEADD(StartOfSelectedMonthsWindow, -1, DAY)

// The start of the previous 3-full-month period is 2 full months prior to the month of EndDateOfPreviousThreeMonths.
VAR StartDateOfPreviousThreeMonths = STARTOFMONTH(EDATE(EndDateOfPreviousThreeMonths, -2))

RETURN
    CALCULATE (
        [Total Clicks],
        DATESBETWEEN (
            'DateTable'[Date],
            StartDateOfPreviousThreeMonths,
            EndDateOfPreviousThreeMonths
        )
    )


Selected Day Clicks =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] ) // Gets the latest date from your slicer
RETURN
    CALCULATE (
        [Total Clicks],
        'DateTable'[Date] = MaxSelectedDate // Filter Total Clicks to where the date in DateTable matches MaxSelectedDate
    )

Previous Day Clicks =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )      // Gets the latest date from your slicer
VAR PreviousDate = MaxSelectedDate - 1             // Calculates the date for the day before
RETURN
    CALCULATE (
        [Total Clicks],
        'DateTable'[Date] = PreviousDate // Filter Total Clicks to the calculated previous date
    )


Selected Week Range Label =
VAR AnchorDate = MAX ( 'DateTable'[Date] )
// Assuming Monday is the start of the week (2). If Sunday, use 1.
VAR StartOfWeek = AnchorDate - WEEKDAY ( AnchorDate, 2 ) + 1
VAR EndOfWeek = StartOfWeek + 6
RETURN
    FORMAT ( StartOfWeek, "d mmm yyyy" ) & " - " & FORMAT ( EndOfWeek, "d mmm yyyy" )

Previous Week Range Label =
VAR AnchorDate = MAX ( 'DateTable'[Date] )
// Anchor in the previous week
VAR AnchorInPreviousWeek = AnchorDate - 7
VAR StartOfPreviousWeek = AnchorInPreviousWeek - WEEKDAY ( AnchorInPreviousWeek, 2 ) + 1
VAR EndOfPreviousWeek = StartOfPreviousWeek + 6
RETURN
    FORMAT ( StartOfPreviousWeek, "d mmm yyyy" ) & " - " & FORMAT ( EndOfPreviousWeek, "d mmm yyyy" )

Selected Day Label =
VAR SelectedDate = MAX ( 'DateTable'[Date] )
RETURN
    FORMAT ( SelectedDate, "d mmm yyyy" )

Previous Day Label =
VAR SelectedDate = MAX ( 'DateTable'[Date] )
VAR PreviousDate = SelectedDate - 1
RETURN
    FORMAT ( PreviousDate, "d mmm yyyy" )

Selected Month Range Label =
VAR AnchorDate = MAX ( 'DateTable'[Date] )
VAR StartOfMonth = STARTOFMONTH ( AnchorDate )
VAR EndOfMonth = ENDOFMONTH ( AnchorDate )
RETURN
    FORMAT ( StartOfMonth, "d mmm yyyy" ) & " - " & FORMAT ( EndOfMonth, "d mmm yyyy" )

Previous Month Range Label =
VAR AnchorDate = MAX ( 'DateTable'[Date] )
// Get the end of the previous month by going back one month from the anchor date
VAR EndOfPreviousMonth = ENDOFMONTH ( EDATE ( AnchorDate, -1 ) )
VAR StartOfPreviousMonth = STARTOFMONTH ( EndOfPreviousMonth )
RETURN
    FORMAT ( StartOfPreviousMonth, "d mmm yyyy" ) & " - " & FORMAT ( EndOfPreviousMonth, "d mmm yyyy" )

The first argument to 'ENDOFMONTH' must specify a column.
The syntax for 'StartOfMonth' is incorrect. (DAX(VAR AnchorDate = MAX ( 'Date Table'[Date] )VAR StartOfMonth = STARTOFMONTH ( AnchorDate )VAR EndOfMonth = ENDOFMONTH ( AnchorDate )RETURN FORMAT ( StartOfMonth, "d mmm yyyy" ) & " - " & FORMAT ( EndOfMonth, "d mmm yyyy" ))).


Selected Month Range Label =
VAR AnchorDate = MAX ( 'DateTable'[Date] )
// Correct way to get the start of the month from a single date variable
VAR StartOfMonth = DATE ( YEAR ( AnchorDate ), MONTH ( AnchorDate ), 1 )
// EOMONTH is designed to work with a start date and returns the end of the month
VAR EndOfMonth = EOMONTH ( AnchorDate, 0 )
RETURN
    FORMAT ( StartOfMonth, "d mmm testTakePhoto" ) & " - " & FORMAT ( EndOfMonth, "d mmm testTakePhoto" )


Selected 3 Calendar Months Label =
VAR AnchorDate = MAX ( 'DateTable'[Date] )
// Correctly get the start date of the 3-month window
VAR DateInStartMonth = EDATE ( AnchorDate, -2 )
VAR StartDate = DATE ( YEAR ( DateInStartMonth ), MONTH ( DateInStartMonth ), 1 )
// Correctly get the end date of the 3-month window
VAR EndDate = EOMONTH ( AnchorDate, 0 )
RETURN
    FORMAT ( StartDate, "d mmm gacchatiti" ) & " - " & FORMAT ( EndDate, "d mmmcourseSurvey" )


Previous Three Months Range Label =
VAR CurrentPeriodEndDate = MAX ( 'DateTable'[Date] )
// The previous period ends the day before the current rolling 3-month period starts
VAR PreviousPeriodEndDate = EDATE ( CurrentPeriodEndDate, -3 )
VAR PreviousPeriodStartDate = EDATE ( PreviousPeriodEndDate, -3 ) + 1
RETURN
    FORMAT ( PreviousPeriodStartDate, "d mmm yyyy" ) & " - " & FORMAT ( PreviousPeriodEndDate, "d mmm yyyy" )

The first argument to 'STARTOFMONTH' must specify a column.

Selected 3 Calendar Months Label =
VAR AnchorDate = MAX ( 'Date Table'[Date] )
// Correctly get the start date of the 3-month window
VAR DateInStartMonth = EDATE ( AnchorDate, -2 )
VAR StartDate = DATE ( YEAR ( DateInStartMonth ), MONTH ( DateInStartMonth ), 1 )
// Correctly get the end date of the 3-month window
VAR EndDate = EOMONTH ( AnchorDate, 0 )
RETURN
FORMAT ( StartDate, "d mmm yyyy" ) & " - " & FORMAT ( TODAY()-1, "dd mmm yyyy" )


this the DAX formula i have to show the date but i want to show date like if i select date from date slicer of current on going moths it shows me todays date but if selected date from past month it will shows me date according to that months last date please modify above DAX according to that

Selected 3 Calendar Months Label =
VAR AnchorDate = MAX ( 'Date Table'[Date] )
// Correctly get the start date of the 3-month window
VAR DateInStartMonth = EDATE ( AnchorDate, -2 )
VAR StartDate = DATE ( YEAR ( DateInStartMonth ), MONTH ( DateInStartMonth ), 1 )
// Determine the appropriate end date based on whether we're in current month or past month
VAR CurrentMonth = MONTH ( TODAY() )
VAR CurrentYear = YEAR ( TODAY() )
VAR AnchorMonth = MONTH ( AnchorDate )
VAR AnchorYear = YEAR ( AnchorDate )
VAR IsCurrentMonth = ( AnchorMonth = CurrentMonth && AnchorYear = CurrentYear )
// Use today's date if current month, otherwise use the last date of the anchor month
VAR EndDate = IF ( IsCurrentMonth, TODAY() - 1, EOMONTH ( AnchorDate, 0 ) )
RETURN
FORMAT ( StartDate, "d mmm yyyy" ) & " - " & FORMAT ( EndDate, "dd mmm yyyy" )

Previous 3 Calendar Months Label =
VAR AnchorDate = MAX ( 'Date Table'[Date] )
// Get the start date of the previous 3-month window (3 months before the selected period)
VAR DateInStartMonth = EDATE ( AnchorDate, -5 ) // Go back 5 months to get 3 months before current selection
VAR StartDate = DATE ( YEAR ( DateInStartMonth ), MONTH ( DateInStartMonth ), 1 )
// Calculate the end date for previous 3 months period
VAR PreviousPeriodEndMonth = EDATE ( AnchorDate, -3 ) // 3 months before anchor date
// Determine if the previous period's end month is the current month
VAR CurrentMonth = MONTH ( TODAY() )
VAR CurrentYear = YEAR ( TODAY() )
VAR PrevEndMonth = MONTH ( PreviousPeriodEndMonth )
VAR PrevEndYear = YEAR ( PreviousPeriodEndMonth )
VAR IsPrevEndCurrentMonth = ( PrevEndMonth = CurrentMonth && PrevEndYear = CurrentYear )
// Use today's date if previous period ends in current month, otherwise use end of that month
VAR EndDate = IF ( IsPrevEndCurrentMonth, TODAY() - 1, EOMONTH ( PreviousPeriodEndMonth, 0 ) )
RETURN
FORMAT ( StartDate, "d mmm yyyy" ) & " - " & FORMAT ( EndDate, "dd mmm yyyy" )


Current 1 Calendar Month Label =
VAR AnchorDate = MAX ( 'Date Table'[Date] )
// Get the start date of the selected month (1st day)
VAR StartDate = DATE ( YEAR ( AnchorDate ), MONTH ( AnchorDate ), 1 )
// Determine if the selected month is the current month
VAR CurrentMonth = MONTH ( TODAY() )
VAR CurrentYear = YEAR ( TODAY() )
VAR AnchorMonth = MONTH ( AnchorDate )
VAR AnchorYear = YEAR ( AnchorDate )
VAR IsCurrentMonth = ( AnchorMonth = CurrentMonth && AnchorYear = CurrentYear )
// Use today's date if selected month is current month, otherwise use end of selected month
VAR EndDate = IF ( IsCurrentMonth, TODAY() - 1, EOMONTH ( AnchorDate, 0 ) )
RETURN
FORMAT ( StartDate, "d mmm yyyy" ) & " - " & FORMAT ( EndDate, "dd mmm yyyy" )

Previous 1 Calendar Month Label =
VAR AnchorDate = MAX ( 'Date Table'[Date] )
// Get the previous month date
VAR PreviousMonthDate = EDATE ( AnchorDate, -1 )
// Get the start date of the previous month (1st day)
VAR StartDate = DATE ( YEAR ( PreviousMonthDate ), MONTH ( PreviousMonthDate ), 1 )
// Determine if the previous month is the current month
VAR CurrentMonth = MONTH ( TODAY() )
VAR CurrentYear = YEAR ( TODAY() )
VAR PrevMonth = MONTH ( PreviousMonthDate )
VAR PrevYear = YEAR ( PreviousMonthDate )
VAR IsPrevCurrentMonth = ( PrevMonth = CurrentMonth && PrevYear = CurrentYear )
// Use today's date if previous month is current month, otherwise use end of previous month
VAR EndDate = IF ( IsPrevCurrentMonth, TODAY() - 1, EOMONTH ( PreviousMonthDate, 0 ) )
RETURN
FORMAT ( StartDate, "d mmm yyyy" ) & " - " & FORMAT ( EndDate, "dd mmm yyyy" )
