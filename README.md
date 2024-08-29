# Repository is created to Perform DQ Check Assignment on Sample Dataset

**Assignment**: Given a sample dataset of 1,000,000 dummy employee records with columns EMP_ID, FIRST_NAME, LAST_NAME, ADDRESS, CITY, STATE, COUNTRY, perform the following Quality Checks:

- Ensure columns do not contain any special characters, except for the ADDRESS column which can only have commas.
- Verify that the COUNTRY column contains only 'India'.
- Confirm that the STATE column includes only 'Maharashtra', 'Haryana', or 'Karnataka'.
- Perform Not Null/blank checks on all columns.
- Evaluate and determine the best solution/technique in terms of time complexity from the following options:
    - PySpark with Great Expectations Library using standard and custom GE rules.
    - Python/Pandas with Great Expectations Library using standard and custom GE rules.
    - PySpark without GE library.
    - Python/Pandas without GE library.
- The output of the QC alert should be in JSON format as follows:

 
```
{
    "special_characters_check": [
        {
            "row_number": 1,
            "COLUMN": "ADDRESS"
        },
        {
            "row_number": 2,
            "COLUMN": "STATE"
        }
        // Include other checks as necessary
    ],
    "not_null_check": [
        {
            "row_number": 1011,
            "COLUMN": "ADDRESS"
        },
        {
            "row_number": 29999,
            "COLUMN": "STATE"
        }
        // Include other checks as necessary
    ]
    // Include other checks as necessary
}

```

## Approach Evaluation 

Given the UseCase of a ~60MB input file with 1 million records, below is the evaluation summary:

**1. Python/Pandas with Great Expectations (GE) Library**

**Pros**
- Memory Efficiency: Pandas can handle the 60MB file efficiently within the memory of most modern systems on a standalone server.
- Ease of Use: Python/Pandas with GE provides a straightforward and user-friendly approach, with a rich set of built-in validations and easy customization.
- Integration: Great Expectations integrates well with Pandas, making it easy to set up and maintain.

**Cons:**
- Single-Node Processing: Pandas operates on a single node, so if the dataset were larger, it could become a bottleneck. However, for the given dataset size, this is not an issue.

**2. PySpark with Great Expectations (GE) Library**

**Pros:**
- Scalability: PySpark can handle much larger datasets due to its distributed nature, making it future-proof if data size increases significantly.
- Parallel Processing: It can distribute the workload across multiple cores or nodes, potentially reducing execution time on very large datasets.

**Cons:**
- Overhead: For a 60MB file, the overhead of setting up and managing a Spark cluster is unnecessary and could slow down processing.
- Complexity: More complex to set up and maintain compared to Pandas.

**3. Python/Pandas without GE Library**

**Pros:**
- Full Control: Directly using Pandas allows for customized and optimized code without the abstraction layer of GE, which could be slightly more efficient.
- Simplicity: Easier to debug and tailor for very specific needs.

**Cons:**
- Manual Validation: You'll need to manually code all validations, which could lead to more lines of code and potential bugs.
- Reusability: GE provides reusable validation logic; without it, the code may be less reusable.

**4. PySpark without GE Library**

**Pros:**
- Scalability: Like PySpark with GE, this approach is scalable for large datasets.
- Customization: Direct control over PySpark can lead to highly optimized and customized validation processes.

**Cons:**
- Unnecessary Complexity: Given the dataset size, the complexity and overhead of using PySpark are not justified.
- Manual Validation: Similar to Pandas without GE, you'll need to manually code all validations, which could be complex.


## Challenges Faced
1. **Performance and Scalability:**

- Pandas with Great Expectations: While Pandas is efficient for smaller datasets, handling 1 million records with extensive validation checks can strain memory and processing time. Great Expectations adds additional overhead as it involves converting Pandas DataFrames into Great Expectations DataFrames, which could lead to slower processing.
- PySpark Considerations: PySpark is designed for distributed data processing, making it a better fit for large datasets. However, setting up PySpark and integrating it with Great Expectations can be complex and may require additional infrastructure (e.g., a Spark cluster).

2. **Complexity of Integration:**

- Great Expectations: Integrating Great Expectations into the data pipeline introduces complexity, especially when writing custom rules. This could require more time for development and testing. Additionally, integrating Great Expectations with PySpark can be less straightforward than with Pandas due to differences in how the two frameworks handle data.
- Without Great Expectations: Writing custom validation logic directly in Pandas or PySpark reduces dependency on external libraries but increases the amount of code to maintain. Custom logic may also lack the built-in documentation, reporting, and reusability features that Great Expectations offers.

3. **Ease of Development and Maintenance:**

- Pandas: Working with Pandas is generally easier for data engineers and analysts due to its simpler API and extensive documentation. It allows for rapid prototyping and debugging. However, handling complex validation rules manually without Great Expectations could lead to repetitive and error-prone code.
- Great Expectations: Provides a structured and reusable way to define and apply data validation rules. It also automatically generates detailed reports, which can be useful for auditing and compliance purposes. However, there is a learning curve, especially for those unfamiliar with the library.

4. **Flexibility and Customization:**

- Great Expectations: Offers extensive flexibility with both built-in and custom expectations, making it easier to scale validation logic across different datasets and projects. However, creating complex custom rules might require deep knowledge of the library’s internals.
- Custom Pandas/PySpark Solutions: While custom solutions provide maximum flexibility, they require more effort to implement and maintain. Each new validation rule might need to be written from scratch, which could slow down development.


## Conclusion on Most Optimal Approach
**Python/Pandas with Great Expectations (GE) Library** is the most optimal approach for a given input file with 1 million records.
- Sufficient performance for the dataset size.
- Ease of use and a simpler setup compared to PySpark.
- Comprehensive validation capabilities with minimal custom code.
- Built-in reporting and auditing, which is valuable for data quality assurance.

