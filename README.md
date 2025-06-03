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

Selected Month Clicks =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )
VAR StartOfMonthOfMaxDate = STARTOFMONTH ( 'DateTable'[Date] ) // Context of MaxSelectedDate
VAR EndOfMonthOfMaxDate = ENDOFMONTH ( 'DateTable'[Date] )   // Context of MaxSelectedDate
RETURN
    CALCULATE (
        [Total Clicks],
        DATESBETWEEN(
            'DateTable'[Date],
            MINX( FILTER( ALLSELECTED('DateTable'), 'DateTable'[Date] <= MaxSelectedDate ), StartOfMonthOfMaxDate ),
            MAXX( FILTER( ALLSELECTED('DateTable'), 'DateTable'[Date] <= MaxSelectedDate ), EndOfMonthOfMaxDate )
        )
    )


Selected Month Clicks (Simpler) =
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

Previous Month Clicks (Alt) =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )
VAR StartOfSelectedMonthInContext = STARTOFMONTH('DateTable'[Date]) // This gets the start of month for MaxSelectedDate
RETURN
CALCULATE(
    [Total Clicks],
    PREVIOUSMONTH( StartOfSelectedMonthInContext ) // Needs a date column from the date table
)


Previous Month Clicks (Dynamic) =
VAR MaxSelectedDate = MAX ( 'DateTable'[Date] )
VAR FirstDayOfSelectedMonth = STARTOFMONTH( MaxSelectedDate )
VAR FirstDayOfPreviousMonth = EDATE( FirstDayOfSelectedMonth, -1 )
VAR LastDayOfPreviousMonth = ENDOFMONTH( FirstDayOfPreviousMonth )
RETURN
    CALCULATE (
        [Total Clicks],
        DATESBETWEEN(
            'DateTable'[Date],
            FirstDayOfPreviousMonth,
            LastDayOfPreviousMonth
        )
    )
