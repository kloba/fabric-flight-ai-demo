
## Flight Delay Prediction Lab Guide (Microsoft Fabric)

This guide provides step-by-step instructions on using Copilot and other AI tools within Microsoft Fabric to build an end-to-end flight delay prediction solution. Visitors can follow these instructions to learn how to effectively leverage AI capabilities in Microsoft Fabric.

### Repository: `fabric-flight-ai-demo`

---

## Step 1: Create Lakehouse
- Go to your workspace.
- Click **+ New > Lakehouse**.
- Name it following naming convention: `<prefix>-flightdelay-lakehouse` (e.g., `demo-flightdelay-lakehouse`) to ensure consistency and clarity.
- Click **Create**.

---

## Step 2: Upload CSV to Lakehouse
- Download the dataset from Kaggle: [Flight Delay and Cancellation Dataset (2019–2023)](https://www.kaggle.com/datasets/patrickzel/flight-delay-and-cancellation-dataset-2019-2023)
- Go to your Lakehouse > **Files** > Upload the CSV file.
- Name the file `flights_sample_3m.csv`.

---

## Step 3: Dataflow Gen 2 – Clean & Transform

### Create Dataflow:
- Go to workspace > **+ New > Dataflow Gen2**
- Name it: `dfg2-flightdelay-clean`

### Step-by-step using Copilot:

1. **Get data from Lakehouse file**:
```plaintext
Load the CSV file from Lakehouse named `flights_sample_3m.csv`
```

2. **Remove null ARR_DELAY rows**:
```plaintext
Remove all rows where the value in the column `ARR_DELAY` is null.
```

3. **Create column `IS_DELAYED`**:
```plaintext
Create a new column `IS_DELAYED`.
Set its value to 1 if `ARR_DELAY` > 15, otherwise 0.
```

4. **Create column `DEP_HOUR`**:
```plaintext
Create a column `DEP_HOUR` by extracting hour from `CRS_DEP_TIME` (e.g. 1530 → 15).
```

5. **Create column `FL_DAYOFWEEK`**:
```plaintext
Extract day of the week from `FL_DATE` and store in `FL_DAYOFWEEK`. Monday = 1.
```

6. **Create column `FL_MONTH`**:
```plaintext
Extract month from `FL_DATE` and store in `FL_MONTH`. Use Date.Month([FL_DATE]).
```

### Save Output:
- Destination: **Warehouse**

The Warehouse is chosen for storing processed data to facilitate efficient data retrieval and seamless integration with Power BI for analytics and reporting purposes.
- Table name: `whs-flightdelay-features`
- Click **Run** and **Save**

---

## Step 4: Data Agent (AI Skills)

### Create Agent:
- Go to workspace > **+ New > Data Agent**
- Name: `agent-flightdelay-qna`
- Add data source: the Lakehouse or Warehouse table `whs-flightdelay-features`
- Add instructions (optional) and save.

### Example Queries to Try:
- "What percentage of flights were delayed?"
- "Which hour of the day has the highest number of delays?"
- "How often do flights get delayed on each day of the week?"
- "Show me the top 5 airports with the most delays."
- "Which airlines have the highest average arrival delay?"
- "What is the monthly trend of delayed flights?"
- "Compare delay rates between weekdays and weekends."
- "Are longer flights more likely to be delayed?"
- "What is the most common reason for delays among delayed flights?"

---

## Step 5: Notebooks – AI Modeling

### Create Notebook:
- Go to **+ New > Notebook**
- Name it: `nb-flightdelay-model`

### Prompt 1 – Load and Prepare Data
```plaintext
Load the `whs-flightdelay-features` table into a Spark DataFrame.
Prepare the data for binary classification on `IS_DELAYED`.
Apply cleaning and encoding automatically.
```

### Prompt 2 – Train Model
```plaintext
Train a binary classification model to predict `IS_DELAYED`.
Split into train/test sets, fit the model, and evaluate its performance.
```

### Prompt 3 – Visualize Results
```plaintext
Show feature importance (bar chart), a confusion matrix (heatmap), and delay rate by `DEP_HOUR` (line chart).
```

### Prompt 4 – Predict New Flight
```plaintext
Create a DataFrame with a flight:
- DEP_HOUR = 18
- FL_DAYOFWEEK = 5
- FL_MONTH = 12
- AIRLINE_CODE = "UA"
- ORIGIN = "ORD"
- DEST = "LGA"
- DISTANCE = 733

Apply preprocessing, predict `IS_DELAYED`, and print:
- Class (0 or 1)
- Probability
- Message ("likely to be delayed" or not)
- Confidence %
```

---

## Step 6: Power BI – Dashboard

### Create Semantic Model:
- From the Warehouse table `whs-flightdelay-features`, create a semantic model
- Name it: `sm-flightdelay-prediction`

The semantic model provides an intuitive, business-friendly layer on top of your data, enabling powerful and interactive analytics within Power BI. It simplifies data structures, enhances query performance, and allows end-users to easily create insightful reports and dashboards.
- Use Direct Lake for optimal performance

### Auto-Create Report:
- In the Fabric workspace, locate the **default Power BI semantic model**
- Click on the **More menu (…)** next to the model
- Select **Create report**
- A blank Power BI canvas opens for editing the report using this semantic model
- Add visuals as needed (delay rate, airlines, airports, etc.)
- Click **Save**, and the report will be saved to the same workspace if you have write access
- If you don’t have permissions, the report will be saved to **My Workspace**

---

I will be happy to hear your feedback or answer any questions. You can contact me via LinkedIn: [aka.ms/taras](https://aka.ms/taras).
