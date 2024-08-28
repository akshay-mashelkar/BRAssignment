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

Given the context of a 20MB file with 1 million records, let's evaluate the optimal approach:

**1. Python/Pandas with Great Expectations (GE) Library**

**Pros**
- Memory Efficiency: Pandas can handle the 20MB file efficiently within the memory of most modern systems.
- Ease of Use: Python/Pandas with GE provides a straightforward and user-friendly approach, with a rich set of built-in validations and easy customization.
- Integration: Great Expectations integrates well with Pandas, making it easy to set up and maintain.

**Cons:**
- Single-Node Processing: Pandas operates on a single node, so if the dataset were larger, it could become a bottleneck. However, for the given dataset size, this is not an issue.

**2. PySpark with Great Expectations (GE) Library**

**Pros:**
- Scalability: PySpark can handle much larger datasets due to its distributed nature, making it future-proof if data size increases significantly.
- Parallel Processing: It can distribute the workload across multiple cores or nodes, potentially reducing execution time on very large datasets.

**Cons:**
- Overhead: For a 20MB file, the overhead of setting up and managing a Spark cluster is unnecessary and could slow down processing.
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

## Conclusion on Most Optimal Approach
Python/Pandas with Great Expectations (GE) Library is the most optimal approach for a 20MB file with 1 million records.

