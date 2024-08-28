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

