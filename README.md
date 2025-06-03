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
