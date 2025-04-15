## 🧠 `redcap_missing_data()`

Logic-aware missingness checker for REDCap projects. It pulls metadata and records from REDCap, processes branching logic and checkboxes, and classifies missingness according to predefined rules.

---

### 🧠 Purpose

`redcap_missing_data()` is designed to automate the process of assessing missing data in REDCap projects. It uses the REDCap API to pull project metadata and records, processes special field types (e.g., checkboxes, branching logic), and applies custom logic to classify missing data. The result is a comprehensive report that categorizes each field's data status as "No Missing Data", "Missing Data", or "Needs Investigation". This function simplifies the process of identifying missing data, especially for projects with complex data structures like multi-arm studies or those using extensive branching logic.

---

### 📥 Arguments

| Argument      | Type                | Default | Description                                                                                                                                                 |
|---------------|---------------------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `project_id`  | `numeric`           | `NULL`  | The REDCap project ID. This is required to make API calls to the specific REDCap project from which you want to retrieve data.                                |
| `token`       | `character`         | `NULL`  | Your REDCap API token. It is needed for authentication when making API calls. Tokens can be obtained from the REDCap project interface.                      |
| `url`         | `character`         | `NULL`  | The URL to the REDCap API endpoint. This is the base URL used for making API calls, e.g., `"https://redcap.example.com/api/"`.                              |
| `events`      | `character vector`  | `NULL`  | A vector of event names. The function will extract data only for the specified events (e.g., `"event_1", "event_2"`). Ensure that these match the event names in your REDCap project. |
| `fields`      | `character vector`  | `NULL`  | A vector of field names. These are the variables from your REDCap project that you wish to analyze for missingness.                                           |
| `forms`       | `character vector`  | `NULL`  | A vector of form names. These are the forms from which you wish to extract data (e.g., `"contact_form", "survey_form"`).                                     |
| `check_miss`  | `logical`           | `TRUE`  | If set to `TRUE`, the function will evaluate whether each field is missing based on branching logic and whether all necessary values in checkbox fields are filled out. |

---

### ⚙️ Behavior

- **API Data Retrieval**: The function retrieves both records and metadata from your REDCap project using the provided API token, URL, and project ID.
- **Checkbox Handling**: Checkbox fields are treated as multiple binary variables (1 = checked, 0 = unchecked), which are expanded automatically for missingness checks.
- **Branching Logic Transformation**: The function parses and transforms branching logic (conditional visibility of fields) into R-evaluable expressions. This allows it to correctly assess whether fields should be considered "missing" based on the participant's data.
- **Missingness Assessment**: The function applies predefined rules to classify missing data. Fields are evaluated as:
  - "No Missing Data": No missing values or all required conditions are met.
  - "Missing Data": Data is missing or required values are not entered.
  - "Needs Investigation": A flag for fields that might need a closer look, typically due to complex logic or inconsistent data.

---

### 📦 Example

```r
# Call the function with the necessary parameters
redcap_missing_data(
  project_id = 1234,  # REDCap project ID
  token = "YOUR_REDCAP_API_TOKEN",  # Your REDCap API token
  url = "https://redcap.example.com/api/",  # API endpoint URL
  events = c("event_1", "event_2"),  # Events to analyze
  fields = c("age", "dob", "blood_pressure"),  # Fields to check for missing data
  forms = c("contact_form", "visit_form"),  # Forms to extract data from
  check_miss = TRUE  # Check for missingness
)

This will return a dataframe with the following columns:

- `field`: The field name.
- `missing_status`: The classification of missingness for each field ("No Missing Data", "Missing Data", "Needs Investigation").
- `notes`: Any additional notes or reasons why a field may need investigation.

### 📝 Output Dataframes
The function generates several key summary dataframes that help track and report missing data across fields and forms:

#### Data Check Summary:
This dataframe provides an overview of the fields' missingness status, categorizing the data into "Hidden Fields", "Missing Data", and "No Missing Data".

#### QA Errors:
This dataframe filters the fields that have "Missing Data" and is useful for identifying specific variables that require attention.

#### Summary of Missing Data by Field:
This summary shows the frequency of missing data for each field in the specified form. It includes a total count of missing data for each variable.


#### 🚨 Notes
- **Package Dependencies**: The function requires the `httr` package for making API calls and `dplyr` for data manipulation. Ensure both packages are installed:

```r
install.packages("httr")
install.packages("dplyr")

- **Branching Logic**: Ensure that your REDCap project has valid and active branching logic. The function uses this logic to determine whether fields should be considered missing or not.
- **Checkbox Fields**: When check_miss = TRUE, the function will check whether all checkbox options are selected. If any option is missing, the field will be marked as "Missing Data."
- **Large Projects**: For large projects, especially those with many records and fields, processing might take some time. You may want to run the function on a subset of the data or optimize your API requests by limiting the number of fields and events.
- **Global Variables**: Be aware that the function modifies global variables like formData when building API parameters. Make sure these are initialized before running the function to avoid conflicts.



#### 🛠️ Advanced Use
You can modify the behavior of the function by adjusting its internal logic for handling branching conditions, checkbox expansions, and data classification rules. For more complex projects, consider reviewing the function's source code to understand how it's processing the REDCap data and adapt it to your specific use case.
