# VBA-Challenge

# Background : I will be using VBA scripting in Excel to analyze stock market data.

# Solution : In order to complete my assignment, I referenced my instructor's repo to make sure my work was accurate "https://git.bootcampcontent.com/Northwestern-University/NU-VIRT-DATA-PT-06-2024-U-LOLC/-/tree/main/Homework/02-VBA-Scripting?ref_type=heads".

# I produced a script that would loop through the stocks per quarter and yield the ticker symbol, quarterly change of price, percentage of quarterly change of price, and the total stock volume.

# To create further use for the script, I added a feature to return the stock with greatest percent increase / decrease, and greatest total volume. The VBA script is also able to run on every worksheet(quarter) at once. \

# Conditional formatting was also used to accentuate postive change in green and negative change in red. 
Sub stock_market_data_analysis():

    ' Setting the dimensions
    Dim total As Double
    Dim i As Long
    Dim change As Double
    Dim j As Integer
    Dim start As Long
    Dim rowCount As Long
    Dim percentChange As Double
    Dim days As Integer
    Dim dailyChange As Double
    Dim averageChange As Double

    ' Setting appropriate row titles
    Range("I1").Value = "Ticker"
    Range("J1").Value = "Quarterly Change"
    Range("K1").Value = "Percent Change"
    Range("L1").Value = "Total Stock Volume"
    Range("P1").Value = "Ticker"
    Range("Q1").Value = "Value"
    Range("O2").Value = "Greatest % Increase"
    Range("O3").Value = "Greatest % Decrease"
    Range("O4").Value = "Greatest Total Volume"

    ' Setting initial values and variables
    j = 0
    total = 0
    change = 0
    start = 2

    ' get the row number of the last row with data
    rowCount = Cells(Rows.Count, "A").End(xlUp).Row

    For i = 2 To rowCount

        ' If ticker changes then print results
        If Cells(i + 1, 1).Value <> Cells(i, 1).Value Then

            ' Store results in respective variables
            total = total + Cells(i, 7).Value

            ' Take care of zero total volume
            If total = 0 Then
                ' print the results
                Range("I" & 2 + j).Value = Cells(i, 1).Value
                Range("J" & 2 + j).Value = 0
                Range("K" & 2 + j).Value = "%" & 0
                Range("L" & 2 + j).Value = 0

            Else
                ' Find First non zero starting value
                If Cells(start, 3) = 0 Then
                    For find_value = start To i
                        If Cells(find_value, 3).Value <> 0 Then
                            start = find_value
                            Exit For
                        End If
                     Next find_value
                End If

                ' Calculate the percent Change
                change = (Cells(i, 6) - Cells(start, 3))
                percentChange = change / Cells(start, 3)

                ' start of the next stock ticker
                start = i + 1

                ' print the data results found
                Range("I" & 2 + j).Value = Cells(i, 1).Value
                Range("J" & 2 + j).Value = change
                Range("J" & 2 + j).NumberFormat = "0.00"
                Range("K" & 2 + j).Value = percentChange
                Range("K" & 2 + j).NumberFormat = "0.00%"
                Range("L" & 2 + j).Value = total

                ' conditional formatting -color green is positive and red is negative
                Select Case change
                    Case Is > 0
                        Range("J" & 2 + j).Interior.ColorIndex = 4
                    Case Is < 0
                        Range("J" & 2 + j).Interior.ColorIndex = 3
                    Case Else
                        Range("J" & 2 + j).Interior.ColorIndex = 0
                End Select

            End If

            ' reset variables for new stock ticker
            total = 0
            change = 0
            j = j + 1
            days = 0

        ' If ticker remains the same add results
        Else
            total = total + Cells(i, 7).Value

        End If

    Next i

    ' take the max and min and place them in a separate part in the worksheet
    Range("Q2") = "%" & WorksheetFunction.Max(Range("K2:K" & rowCount)) * 100
    Range("Q3") = "%" & WorksheetFunction.Min(Range("K2:K" & rowCount)) * 100
    Range("Q4") = WorksheetFunction.Max(Range("L2:L" & rowCount))

    ' return one less because header row is not a factor
    increase_number = WorksheetFunction.Match(WorksheetFunction.Max(Range("K2:K" & rowCount)), Range("K2:K" & rowCount), 0)
    decrease_number = WorksheetFunction.Match(WorksheetFunction.Min(Range("K2:K" & rowCount)), Range("K2:K" & rowCount), 0)
    volume_number = WorksheetFunction.Match(WorksheetFunction.Max(Range("L2:L" & rowCount)), Range("L2:L" & rowCount), 0)

    ' final ticker symbol for greatest % of increase and decrease, total, and average
    Range("P2") = Cells(increase_number + 1, 9)
    Range("P3") = Cells(decrease_number + 1, 9)
    Range("P4") = Cells(volume_number + 1, 9)

End Sub

