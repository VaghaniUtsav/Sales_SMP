Previous Week Clicks =
CALCULATE (
    [Total Clicks],
    FILTER (
        ALL ( 'DateTable' ),
        'DateTable'[WeekNumber] = WEEKNUM ( TODAY(), 2 ) - 1 &&
        'DateTable'[Year] = YEAR ( TODAY() - 7 ) // Handles year change
    )
)


Previous Week Clicks (Alt) =
CALCULATE(
    [Total Clicks],
    DATEADD('DateTable'[Date], -7, DAY)
) // This calculates for the 7 days prior to the current context
// To make it strictly "last calendar week", the above filter is better.
// For "7 days prior to today's week", this is more direct:


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
