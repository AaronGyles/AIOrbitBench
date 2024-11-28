# Time Series Analysis and Scoring for AI and Reference Solution Comparisons
### By: Aaron Gyles
This code provides functions for processing and comparing time series data, particularly in the context of evaluating AI-generated solutions against reference solutions.

This code is primarily used for orbital dynamic problems using the Python package REBOUND.

## Installation

This code requires the following Python packages:

* **Essential Packages:**
    * numpy
    * pandas
    * scikit-learn 
    
    **Note:** If you have Jupyter Notebook installed, these packages are likely already included.

* **For Running the Examples:**
    * **REBOUND:**  A package for N-body simulations. Install using `pip install rebound`.
    * **matplotlib.pyplot:** For plotting and visualization.  Usually included with Jupyter Notebook. If not, install with `pip install matplotlib`.
    * **google.generativeai:** For accessing Google's generative AI models. Install with `pip install google-generativeai`. You'll need to obtain an API key from Google Cloud Platform and set it as an environment variable.


## Functions

### `time_series(values1, time1, values2, time2, scaling=True, tscaling=True)`

This function takes two sets of time series data (values and corresponding time arrays) and performs scaling and normalization.

**Parameters:**

* `values1`: Dictionary of orbital values for the first dataset.
* `time1`: An array of time values for the first dataset.
* `values2`: Dictionary of orbital values for the second dataset.
* `time2`: An array of time values for the second dataset.
* `scaling`: Boolean value indicating whether to apply standardization scaling to the orbital values (default: True).
* `tscaling`: Boolean value indicating whether to apply normalization scaling to the time arrays (default: True).

**Returns:**

* A tuple of two pandas DataFrames (`df1`, `df2`) containing the scaled and normalized data.

**Functionality:**

1. Creates pandas DataFrames from the input values and time arrays.
2. If `scaling` is True, standardizes the orbital values using `sklearn.preprocessing.scale`. This ensures that both datasets have the same mean and scaling for comparison.
3. If `tscaling` is True, normalizes the time arrays using `sklearn.preprocessing.MinMaxScaler`. The scaler is fit on the DataFrame with the largest time value to ensure consistent scaling.

### `interpolate_data(ref_df, ai_df)`

This function interpolates the data in the AI DataFrame (`ai_df`) to match the time points in the reference DataFrame (`ref_df`).

**Parameters:**

* `ref_df`: DataFrame containing the reference solution values.
* `ai_df`: DataFrame containing the AI-generated solution values.

**Returns:**

* A tuple of two pandas DataFrames (`ref_df`, `ai_df_interpolated`) with the interpolated data.

**Functionality:**

1. Sets the 'Time' column as the index for both DataFrames.
2. Sorts the DataFrames by 'Time'.
3. Performs forward filling interpolation using `pd.merge_asof` to align the AI data with the reference time points.
4. Returns the original reference DataFrame and the interpolated AI DataFrame.

### `compute_score(ref_df, ai_df, tot_rmse=False)`

This function calculates the Root Mean Squared Error (RMSE) between the reference and AI DataFrames.

**Parameters:**

* `ref_df`: DataFrame containing the reference solution values.
* `ai_df`: DataFrame containing the AI-generated solution values.
* `tot_rmse`: Boolean value indicating whether to calculate the total RMSE across all columns (default: False).

**Returns:**

* If `tot_rmse` is False, returns a pandas Series containing the RMSE values for each orbital element column.
* If `tot_rmse` is True, returns a single floating-point number representing the total RMSE.

**Functionality:**

1. Excludes the 'Time' column from the calculation.
2. Calculates the squared errors between corresponding columns in the two DataFrames.
3. Computes the RMSE for each column.
4. If `tot_rmse` is True, calculates the total RMSE by taking the square root of the mean of the individual RMSE values.

## Usage

This code can be used to analyze and compare time series data from different sources, especially when evaluating the performance of AI models against known solutions. The `time_series` function ensures consistent scaling and normalization, while the `interpolate_data` function aligns the data for accurate comparison. The `compute_score` function provides a quantitative measure of the difference between the two datasets.

**Example:**

```python
# Assuming you have values1, time1, values2, time2 defined

# Scale and normalize the data
df1, df2 = time_series(values1, time1, values2, time2)

# Interpolate the AI data
ref_df, ai_df_interpolated = interpolate_data(df1, df2)

# Calculate the RMSE score
rmse_values = compute_score(ref_df, ai_df_interpolated)

# Print the RMSE values
print(rmse_values)