![Pizza_Sales](https://github.com/user-attachments/assets/89de50e0-fbae-4021-b37e-7d3834bb907b)

DAX Formula: Highest Orders

Time with Highest Orders = 
VAR SummaryTable =
    ADDCOLUMNS(
        VALUES(orders[Time of Day]),  // Get distinct values of Time of Day
        "Total Orders", [Total Orders by Time]  // Add the total orders by time measure
    )
VAR TopTimePeriod =
    TOPN(
        1,
        SummaryTable,
        [Total Orders by Time],  // Sort by total orders
        DESC
    )
RETURN
    FIRSTNONBLANK(
        TopTimePeriod,
        [Total Orders by Time]  // Return the first non-blank Time of Day with the highest orders
    )


Explanation:
VALUES(orders[Time of Day]): This function creates a unique list of the distinct values of Time of Day. It will return values like "Morning", "Afternoon", etc.
ADDCOLUMNS: Adds a new column to this list, which calculates the total orders for each time period using the measure [Total Orders by Time].
TOPN(1, SummaryTable, [Total Orders by Time], DESC): This function picks the time period with the highest total orders by sorting the SummaryTable in descending order.
FIRSTNONBLANK: Finally, we use FIRSTNONBLANK to return the Time of Day with the highest number of orders.

Key Changes:
The error you're seeing occurs because we were trying to use a column reference in a way that DAX doesn’t support. By first creating a summarized table (SummaryTable), and then passing that into the TOPN function, we can ensure that only scalar values are being returned at each step.

Test:
After applying the formula, add the Time with Highest Orders measure to a Card Visual or Table Visual to confirm that it is returning the correct "Time of Day" with the highest orders.

DAX Formula : - 

Time with Lowest Orders = 
VAR SummaryTable =
    ADDCOLUMNS(
        VALUES(orders[Time of Day]),  // Get distinct values of Time of Day
        "Total Orders", [Total Orders by Time]  // Add the total orders by time measure
    )
VAR BottomTimePeriod =
    TOPN(
        1, 
        SummaryTable, 
        [Total Orders by Time],  // Sort by Total Orders in ascending order (lowest first)
        ASC
    )
RETURN
    MAXX(BottomTimePeriod, orders[Time of Day])  // Return the Time of Day from the lowest orders


Explanation:
SummaryTable: This part remains the same. We create a summarized table where we have distinct Time of Day and corresponding Total Orders.
TOPN(1, SummaryTable, [Total Orders by Time], ASC): Here, we are sorting the data in ascending order (from lowest to highest) by Total Orders. This will give us the time period with the lowest orders.
MAXX(BottomTimePeriod, orders[Time of Day]): Since TOPN returns a table, MAXX is used to get the Time of Day from the top record (the one with the lowest orders).

Steps:
Implement this formula for the Time with Lowest Orders measure.
Add the measure to a Card visual or Table visual to display the time of day with the lowest number of orders.
This will now give you the time of day during which you receive the fewest orders, similar to how you retrieved the time with the highest orders.

If the highest orders and lowest orders are coming out to be the same, it could be due to one or more of the following reasons:

Possible Issues:
All Time Periods Have the Same Total Orders:

If every time period (e.g., Morning, Afternoon, etc.) has the same number of total orders, the "highest" and "lowest" orders will naturally be identical.
Incorrect Calculation of [Total Orders by Time]:

If the measure [Total Orders by Time] is not aggregating correctly or returning the same value for all time periods, the TOPN function for both highest and lowest will yield the same result.
Data Quality Issue:

Your data might not be sufficiently diverse (e.g., all time periods have exactly one order or the data is incomplete).
Summarization Issue:

The summarization in SummaryTable may not be grouping correctly by Time of Day.
Steps to Debug:
Verify [Total Orders by Time]: Check the measure [Total Orders by Time]. Place it in a table visual along with Time of Day to ensure that it is calculating the total orders correctly for each time period.

Example:

Total Orders by Time = COUNT(orders[order_id])

Check Distinct Time Periods: Create a simple table visual with orders[Time of Day] and [Total Orders by Time]. Verify if the total orders vary across time periods (Morning, Afternoon, Evening, Night).

Inspect the Raw Data: Look at the raw data in the orders table:

Do the Time values properly map to Time of Day?
Are there sufficient data points to distinguish between time periods?
Test Data Diversity: If your dataset is small or heavily skewed (e.g., most orders occur during a single time period), the results will not provide meaningful insights. Ensure that you have diverse and sufficient data.



Modified Measure for Better Clarity:
To debug further, you can create separate measures for Maximum Orders and Minimum Orders for clarity:

Maximum Orders:

Maximum Orders = 
MAXX(
    ADDCOLUMNS(
        VALUES(orders[Time of Day]),
        "Total Orders", [Total Orders by Time]
    ),
    [Total Orders]
)


Minimum Orders:

Minimum Orders = 
MINX(
    ADDCOLUMNS(
        VALUES(orders[Time of Day]),
        "Total Orders", [Total Orders by Time]
    ),
    [Total Orders]
)
These will return the numeric maximum and minimum values of the total orders, which you can compare directly.

Expected Outcome:
If the minimum and maximum orders are the same, your data lacks variability.
If they differ, the issue lies elsewhere (e.g., in your DAX formula or data processing).









