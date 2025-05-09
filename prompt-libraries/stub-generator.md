# Function Stub Generator Prompt Library

## Purpose

This prompt template helps generate well-structured function stubs with clear interfaces, documentation, and TODO comments. It's particularly useful in Stub-Driven Development (SDD) workflows where interfaces need to be defined before implementation.

## Base Prompt Template

```
Generate a function stub for a {language} function that {purpose}.

The function should:
- Accept inputs: {input_description}
- Return: {output_description}
- Handle these edge cases: {edge_cases}

Please include:
- Function signature with typed parameters (where applicable)
- Docstring/comments explaining purpose and parameters
- Return type annotation (where applicable)
- TODO comments indicating implementation steps
- Basic error handling structure
- Any necessary imports
```

## Example: Python Data Processing Function

```
Generate a function stub for a Python function that processes a CSV file containing user activity data and returns aggregated statistics.

The function should:
- Accept inputs: file_path (string), metrics_to_calculate (list of strings), date_range (optional tuple of start_date, end_date)
- Return: A dictionary mapping metric names to their calculated values
- Handle these edge cases: file not found, invalid CSV format, empty file, date range outside available data

Please include:
- Function signature with typed parameters
- Docstring explaining purpose and parameters
- Return type annotation
- TODO comments indicating implementation steps
- Basic error handling structure
- Any necessary imports
```

## Example Output (Python)

```python
import csv
from datetime import datetime
from typing import Dict, List, Optional, Tuple, Any
import os.path

def process_user_activity(
    file_path: str,
    metrics_to_calculate: List[str],
    date_range: Optional[Tuple[datetime, datetime]] = None
) -> Dict[str, Any]:
    """
    Process a CSV file containing user activity data and return aggregated statistics.
    
    Args:
        file_path: Path to the CSV file containing user activity data
        metrics_to_calculate: List of metric names to calculate (e.g., ['total_logins', 'avg_session_time'])
        date_range: Optional tuple of (start_date, end_date) to filter data by date
        
    Returns:
        Dictionary mapping metric names to their calculated values
        
    Raises:
        FileNotFoundError: If the specified file does not exist
        ValueError: If the CSV format is invalid or if requested metrics are not supported
    """
    # Initialize results dictionary
    results = {}
    
    # TODO: Validate inputs
    if not os.path.exists(file_path):
        raise FileNotFoundError(f"The file {file_path} does not exist")
    
    # TODO: Validate requested metrics against supported metrics
    supported_metrics = ['total_logins', 'avg_session_time', 'unique_users']
    for metric in metrics_to_calculate:
        if metric not in supported_metrics:
            raise ValueError(f"Unsupported metric: {metric}. Supported metrics: {supported_metrics}")
    
    try:
        # TODO: Read and parse CSV file
        # TODO: Filter data by date range if provided
        # TODO: Calculate each requested metric
        # TODO: Add results to the results dictionary
        pass
    except Exception as e:
        # TODO: Add specific exception handling for CSV parsing errors
        raise ValueError(f"Error processing CSV file: {str(e)}")
    
    return results
```

## Best Practices

1. **Be Specific About Inputs and Outputs**: Clearly define parameter types, formats, and return values.
2. **Include Edge Cases**: Explicitly mention edge cases to ensure they're handled in the stub.
3. **Use Type Annotations**: Where the language supports it, include type information for better clarity.
4. **Structured TODOs**: Break down the implementation into logical steps with TODO comments.
5. **Error Handling**: Include basic error handling structure even in stubs.
6. **Documentation**: Always request comprehensive documentation for the function's purpose and parameters.
