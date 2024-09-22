# Home_Sales 
Module 22

# Home Sales Analysis Project

## Overview
This project analyzes home sales data SparkSQL to determine key metrics about home sales data. Then you'll use Spark to create temporary views, partition the data, cache and uncache a temporary table, and verify that the table has been uncached.

# Home Sales Project

## 1. Spark DataFrame Creation
A Spark DataFrame was generated by reading the provided dataset (`home_sales_revised.csv`).
- **How**: The data was loaded into the DataFrame using the `spark.read.csv` function, with appropriate options to handle headers and infer schema.
- **Why**: This step ensures that the data is structured properly for analysis and querying.
- **Best Option**: Using `spark.read.csv` allows for efficient data loading, especially for large datasets.

## 2. Temporary Table Creation
A temporary table was created from the Spark DataFrame using SparkSQL.
- **How**: The DataFrame was registered as a temporary table using the `createOrReplaceTempView` method, allowing SQL queries to be run directly on the table.
- **Why**: Temporary tables facilitate the use of SQL queries, providing a familiar interface for data analysis.
- **Best Option**: This method maintains the ability to manipulate the DataFrame while allowing SQL queries, enhancing flexibility.

## 3. Average Price of Four-Bedroom Houses per Year
A query was written to calculate the average price of houses with four bedrooms for each year. The results were rounded to two decimal places, providing insights into trends in four-bedroom house prices over time.
- **How**: An SQL query was run using SparkSQL’s `SELECT` statement, where the `AVG(price)` function was used for the average, and the results were grouped by year. The results were rounded with `ROUND(AVG(price), 2)`.
- **Why**: This analysis helps identify price trends in the housing market for four-bedroom homes.
- **Best Option**: Using `ROUND` ensures that the price data is presented in a user-friendly format.

## 4. Average Price for 3-Bedroom, 3-Bathroom Homes per Year
A query was created to find the average price of homes with three bedrooms and three bathrooms, grouped by the year the home was built. The output was rounded to two decimal places.
- **How**: The query used `WHERE` clauses to filter for homes with three bedrooms and three bathrooms, grouping results by the year built using `GROUP BY`, and applying `ROUND` for price precision.
- **Why**: This allows for understanding how the average price of these specific homes has changed over time.
- **Best Option**: Filtering with `WHERE` clauses ensures that only relevant data is considered in the analysis.

## 5. Average Price for 3-Bedroom, 3-Bathroom, Two-Floor Homes over 2,000 Sq Ft per Year
A query was used to calculate the average price of homes that met the following criteria: three bedrooms, three bathrooms, two floors, and a size greater than or equal to 2,000 square feet. The results were grouped by the year the home was built and rounded to two decimal places.
- **How**: The query included multiple `WHERE` conditions to filter for homes with three bedrooms, three bathrooms, two floors, and at least 2,000 square feet. The query then used `AVG(price)` and `GROUP BY` to compute the averages for each year.
- **Why**: This analysis helps to assess the market for larger family homes that meet specific criteria.
- **Best Option**: Including multiple conditions in `WHERE` clauses ensures precise targeting of the desired home characteristics.

## 6. Average Price of Homes per "View" Rating
A query was written to return the average home price per "view" rating, but only for homes where the average price was greater than or equal to $350,000. The query included the run time for performance tracking and was rounded to two decimal places.
- **How**: The `HAVING` clause was used to filter view ratings with average home prices greater than $350,000. The query also tracked run time using Python’s `time` library to measure and print execution time.
- **Why**: Understanding how view ratings correlate with price can inform buyers about market preferences.
- **Best Option**: Using `HAVING` allows for post-aggregation filtering, providing a more meaningful result set.

### Measuring Query Execution Time

    # Measure the start time
    start_time = time.time()

    # Execute the query
    spark.sql("""
        SELECT
            view,
            ROUND(AVG(price), 2) AS avg_price
        FROM
            home_sales
        GROUP BY
            view
        HAVING
            AVG(price) >= 350000
        ORDER BY
            view DESC
        """).show()

    # Measure and print the end time
    end_time = time.time()
    print(f"--- {end_time - start_time} seconds ---")


1. **Using `time.time()`**  
   - **Function**: The `time.time()` function returns the current time in seconds since the epoch (January 1, 1970). It provides a simple way to measure elapsed time.
   - **Granularity**: This method offers sufficient precision for measuring the duration of tasks that are expected to run in a relatively short period, such as SQL queries.

2. **Measuring Start Time**  
   The `start_time` variable captures the time just before executing the SQL query. This serves as a reference point to determine when the execution begins.

3. **Executing the Query**  
   The SQL query is executed within the `spark.sql()` method. The execution time of this code block is what we want to measure.

4. **Measuring End Time**  
   The `end_time` variable captures the time immediately after the query execution is complete. This indicates when the execution has finished.

5. **Calculating Execution Duration**  
   The difference between `end_time` and `start_time` gives the total time taken to execute the SQL query. This result is printed in seconds, providing a clear indication of performance.

### Benefits of Timing Method
- **Simplicity**: It's straightforward to implement and requires minimal code changes.
- **Immediate Feedback**: By measuring the execution time directly around the query, you get immediate insights into how long the operation took.
- **Useful for Performance Monitoring**: This method helps in monitoring query performance over time, allowing for comparisons of efficiency when optimizing queries or working with different datasets.

The output will be in seconds and indicates how long the query took to execute, allowing for performance analysis and optimization efforts.

## 7. Caching the Temporary home_sales Table
The `home_sales` temporary table was cached to optimize performance for repeated queries. The cache was validated to ensure that the table was properly cached.
- **How**: The table was cached using Spark’s `cache()` method, and validation was done using `is_cached` to confirm the cache status of the table.
- **Why**: Caching reduces the time required for subsequent queries by storing the data in memory.
- **Best Option**: Using `cache()` is effective for speeding up repeated queries on the same dataset.

## 8. Running the Query from Step 6 on the Cached Table
The query from step 6 (average price per "view" rating) was run again using the cached data, and the run time was measured. This allowed a comparison of the performance between cached and uncached data.
- **How**: After caching the table, the same query from step 6 was rerun to observe performance improvements. The run time was again measured using `time.time()` to compare with the uncached execution.
- **Why**: Comparing execution times helps quantify the performance benefits of caching.
- **Best Option**: Re-running the query on cached data provides a clear demonstration of caching effectiveness.

## 9. Partitioning the Home Sales Dataset by date_built Field
The home sales data was partitioned by the `date_built` field, and the formatted data was stored in parquet format. Partitioning helped improve query performance by organizing the data for efficient retrieval.
- **How**: The dataset was written to parquet format using the `write.partitionBy` method, where the `date_built` field was specified as the partitioning key.
- **Why**: Partitioning allows Spark to read only the relevant data, thus speeding up query performance.
- **Best Option**: Using parquet format optimizes storage and performance due to its efficient columnar storage.

## 10. Creating a Temporary Table from the Parquet Data
A new temporary table was created from the partitioned parquet data. This allowed the system to leverage the performance improvements gained from partitioning.
- **How**: The parquet file was read back into a DataFrame using `spark.read.parquet` and then registered as a new temporary table using `createOrReplaceTempView`.
- **Why**: This step ensures that the performance gains from partitioning are available for further queries.
- **Best Option**: Temporary tables from parquet data maintain the flexibility of SQL querying while benefiting from optimized data storage.

## 11. Running the Query from Step 6 on the Parquet Table
The query from step 6 (average price per "view" rating) was run again on the parquet data, and the run time was recorded. The run time was compared with previous executions to evaluate the impact of partitioning on performance.
- **How**: After loading the parquet data into a new table, the same query from step 6 was executed, and the time taken was recorded using `time.time()` to compare with earlier runs.
- **Why**: This evaluation helps demonstrate the performance improvements resulting from data partitioning.
- **Best Option**: Comparing execution times across different data formats highlights the advantages of using optimized storage.

## 12. Uncaching the home_sales Temporary Table
The cached `home_sales` table was uncached to free up memory. The uncaching operation was verified to ensure that the table was no longer cached, and Spark could continue without relying on cached data.
- **How**: The `uncacheTable()` method was used to uncache the `home_sales` table, followed by a check using `is_cached` to ensure the table was successfully uncached.
- **Why**: Uncaching frees up memory resources for other processes, ensuring efficient use of Spark's resources.
- **Best Option**: Properly managing cache helps maintain optimal performance in larger data workflows.

## References

1. **ChatGPT**: OpenAI's ChatGPT language model (https://chat.openai.com/)
2. **Xpert Learning Assistant**: https://bootcampspot.instructure.com/courses/6446/external_tools/313
3. **Alexander Booth** Professor DATA-PT-EAST-APRIL-041524 Module 22 classroom and Bootcamp SparkSQL activities resources. 
4. **Apache Spark Documentation**: [Apache Spark Official Documentation](https://spark.apache.org/docs/latest/)
   - Comprehensive guide to all features and functions of Apache Spark.

5. **SparkSQL Guide**: [Spark SQL, DataFrames and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html)
   - Detailed information on using SparkSQL and working with DataFrames.

6. **Parquet File Format**: [Apache Parquet](https://parquet.apache.org/)
   - Official documentation on the Parquet file format, including benefits and usage.

7. **Python Time Library**: [Python Time Module](https://docs.python.org/3/library/time.html)
   - Overview of the `time` module in Python for measuring execution time.

8. **Caching in Spark**: [Caching and Persisting RDDs](https://spark.
