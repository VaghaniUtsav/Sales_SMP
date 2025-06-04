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
