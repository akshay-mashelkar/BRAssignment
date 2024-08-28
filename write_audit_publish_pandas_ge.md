
# Write-Audit-Publish Pattern with Python/Pandas and Great Expectations

This document outlines a "Write-Audit-Publish" pattern for data quality management using Python, Pandas, and the Great Expectations (GE) library. The approach ensures that data is validated before being published, adhering to robust data quality management practices.

## **1. Write: Load and Preprocess Data**

The `load_and_preprocess_data` function loads the data using Pandas and performs necessary preprocessing, such as trimming whitespace. The preprocessed data is optionally saved to an intermediate file.

```python
import pandas as pd

# Paths for data and outputs
input_file_path = "employee_data.csv"
intermediate_file_path = "intermediate_data.csv"

def load_and_preprocess_data(input_file):
    # Load data using Pandas
    df = pd.read_csv(input_file)
    
    # Example preprocessing: trimming whitespace, etc.
    df = df.apply(lambda x: x.str.strip() if x.dtype == "object" else x)
    
    # Save intermediate data (optional step)
    df.to_csv(intermediate_file_path, index=False)
    
    return df
```

## **2. Audit: Perform Data Quality Checks**

The `audit_data` function uses Great Expectations to perform a series of data quality checks:
- **Special Characters Check**: Ensures no special characters (except commas in the `ADDRESS` column).
- **Country and State Check**: Validates that the `COUNTRY` column contains only 'India' and the `STATE` column contains only 'Maharashtra', 'Haryana', or 'Karnataka'.
- **Not Null/Blank Check**: Ensures that no columns contain null or blank values.

The results are stored in an `alerts` dictionary and saved as a JSON file.

```python
import great_expectations as ge
import json

# Path for audit report output
audit_report_path = "audit_report.json"

def audit_data(df):
    # Convert Pandas DataFrame to Great Expectations DataFrame
    ge_df = ge.from_pandas(df)
    
    # Initialize alerts dictionary
    alerts = {"special_characters_check": [], "country_check": [], "state_check": [], "not_null_check": []}

    # Custom special character checks for columns (excluding ADDRESS which allows commas)
    for column in df.columns:
        if column != 'ADDRESS':
            result = ge_df.expect_column_values_to_match_regex(column, r"^[a-zA-Z0-9\s]*$")
            if not result['success']:
                for idx in result['result'].get('unexpected_index_list', []):
                    alerts["special_characters_check"].append({"row_number": idx, "COLUMN": column})

    # Special case for ADDRESS column
    result = ge_df.expect_column_values_to_match_regex('ADDRESS', r"^[a-zA-Z0-9\s,]*$")
    if not result['success']:
        for idx in result['result'].get('unexpected_index_list', []):
            alerts["special_characters_check"].append({"row_number": idx, "COLUMN": 'ADDRESS'})
    
    # COUNTRY column should contain only 'India'
    result = ge_df.expect_column_values_to_be_in_set("COUNTRY", ["India"])
    if not result['success']:
        for idx in result['result'].get('unexpected_index_list', []):
            alerts["country_check"].append({"row_number": idx, "COLUMN": "COUNTRY"})
    
    # STATE column should contain only specific states
    result = ge_df.expect_column_values_to_be_in_set("STATE", ["Maharashtra", "Haryana", "Karnataka"])
    if not result['success']:
        for idx in result['result'].get('unexpected_index_list', []):
            alerts["state_check"].append({"row_number": idx, "COLUMN": "STATE"})
    
    # Check for not null/blank values across all columns
    for column in df.columns:
        result = ge_df.expect_column_values_to_not_be_null(column)
        if not result['success']:
            for idx in result['result'].get('unexpected_index_list', []):
                alerts["not_null_check"].append({"row_number": idx, "COLUMN": column})
        
        result = ge_df.expect_column_values_to_not_match_regex(column, r"^\s*$")
        if not result['success']:
            for idx in result['result'].get('unexpected_index_list', []):
                alerts["not_null_check"].append({"row_number": idx, "COLUMN": column})
    
    # Save audit results to a JSON file
    with open(audit_report_path, 'w') as json_file:
        json.dump(alerts, json_file, indent=4)
    
    return alerts
```

## **3. Publish: Save Data if it Passes Audit**

The `publish_data` function checks the audit results. If critical issues (e.g., null values, incorrect states or countries) are found, the data is not published, and the user is alerted. If no critical issues are found, the validated data is saved.

```python
validated_file_path = "validated_data.csv"

def publish_data(df, audit_results):
    # Determine if any critical audit checks failed
    critical_issues = len(audit_results["not_null_check"]) > 0 or len(audit_results["country_check"]) > 0 or len(audit_results["state_check"]) > 0
    
    if not critical_issues:
        # Save the validated data
        df.to_csv(validated_file_path, index=False)
        print(f"Data successfully validated and saved to {validated_file_path}")
    else:
        print("Data validation failed. Check the audit report for details.")
        print(f"Audit report saved to {audit_report_path}")
```

## **4. Main Execution**

The `main` function orchestrates the "Write-Audit-Publish" workflow.

```python
def main():
    # Step 1: Write
    df = load_and_preprocess_data(input_file_path)
    
    # Step 2: Audit
    audit_results = audit_data(df)
    
    # Step 3: Publish
    publish_data(df, audit_results)

if __name__ == "__main__":
    main()
```

## **Considerations:**

- **Audit Failure Handling**: The `publish_data` function prevents the publication of data if critical validation checks fail. Customize the criteria for what constitutes a "critical" issue as needed.
- **Intermediate File**: Storing an intermediate file after preprocessing is optional but useful for debugging.
- **Audit Report**: The audit report provides a detailed account of any issues found, which can be crucial for debugging and data quality assurance.
