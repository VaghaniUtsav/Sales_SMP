Previous Week Clicks =
CALCULATE (
    [Total Clicks],
    FILTER (
        ALL ( 'DateTable' ),
        'DateTable'[WeekNumber] = WEEKNUM ( TODAY(), 2 ) - 1 &&
        'DateTable'[Year] = YEAR ( TODAY() - 7 ) // Handles year change
    )
)
