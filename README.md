# Self-Guided Content: Introduction to Streamlit for Data Applications



## Table of Contents

- [Overview](#overview)
- [Objectives](#objectives)
- [Business Case & Dataset](#business-case--dataset)
- [Project Structure](#project-structure)
- [Backend](#backend)
  - [1. Load Data](#1-load-data)
  - [2. Summary Statistics](#2-summary-statistics)
  - [3. Visualizations](#3-visualizations)
- [Frontend (Streamlit)](#frontend-streamlit)
  - [4. Installation & Setup](#4-installation--setup)
  - [5. Running the App](#5-running-the-app)
  - [6. Building Blocks](#6-building-blocks)
  - [7. Interactivity & Widgets](#7-interactivity--widgets)
  - [8. Layout & Containers](#8-layout--containers)
  - [9. Caching](#9-caching)
- [Deployment](#deployment)
  - [Streamlit Sharing](#streamlit-sharing)
- [Bonus Activity](#bonus-activity)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

Streamlit is an open-source Python framework that makes it easy to build and share custom web apps for machine learning and data science. In minutes, you can create, deploy, and iterate on powerful data applications without requiring extensive front‑end experience.

This lesson guides you through:

- Setting up the project
- Building backend data-processing functions
- Creating an interactive Streamlit frontend
- Deploying your app for others to use

## Objectives

By the end of this lesson, you will be able to:

1. Understand Streamlit’s role in data app development.
2. Install and configure Streamlit.
3. Write backend functions to load, summarize, and visualize data.
4. Build a Streamlit frontend with widgets, tables, and charts.
5. Deploy your Streamlit app online.

## Business Case & Dataset

We’ll work with the **supermarket\_sales.csv** dataset (1,000 rows × 17 columns). It contains:

| Column                  | Description                                 |
| ----------------------- | ------------------------------------------- |
| Invoice ID              | Unique invoice number                       |
| Branch                  | Supermarket branch (A, B, C)                |
| City                    | Location of branch                          |
| Customer type           | Member or Normal                            |
| Gender                  | Customer gender                             |
| Product line            | Item category                               |
| Unit price              | Price per unit                              |
| Quantity                | Number of units sold                        |
| Tax 5%                  | Tax amount                                  |
| Total                   | Total sale (including tax)                  |
| Date                    | Purchase date                               |
| Time                    | Purchase time                               |
| Payment                 | Payment method (Cash, Credit card, Ewallet) |
| COGS                    | Cost of goods sold                          |
| Gross margin percentage | Margin %                                    |
| Gross income            | Income after cost                           |
| Rating                  | Customer rating (scale 1–10)                |

**Key Features**

- Filterable, sortable sales table
- Summary statistics: total revenue, avg. rating, transactions
- Time series visualizations of sales trends
- Interactive rating filter

## Project Structure

```
streamlit-sales/         # Root directory
├── backend.py           # Data-loading, summary, and plotting functions
├── app.py               # Streamlit frontend script
├── requirements.txt     # Python dependencies
├── supermarket_sales.csv# Data file (or link in code)
└── README.md            # This documentation
```

---

## Backend

All data logic resides in **backend.py**, keeping concerns separate from the UI.

### 1. Load Data

```python
import pandas as pd

def load_data():
    data = pd.read_csv('supermarket_sales.csv')
    data['Date'] = pd.to_datetime(data['Date'])
    return data
```

*Loads the CSV and ensures the **`Date`** column is datetime for plotting/filtering.*

### 2. Summary Statistics

```python
def get_summary(data):
    summary = pd.DataFrame({
        'Total Sales': [data['Total'].sum()],
        'Average Rating': [data['Rating'].mean()],
        'Total Transactions': [data['Invoice ID'].nunique()]
    })
    return summary
```

*Calculates key KPIs for display in the dashboard.*

### 3. Visualizations

```python
import matplotlib.pyplot as plt

def plot_sales_over_time(data):
    data['Date'] = pd.to_datetime(data['Date'])
    series = data.groupby(data['Date'].dt.date)['Total'].sum()
    plt.figure(figsize=(10, 5))
    plt.plot(series.index, series.values)
    plt.title('Sales Over Time')
    plt.xlabel('Date')
    plt.ylabel('Total Sales')
    plt.xticks(rotation=45)
    plt.tight_layout()
    return plt
```

*Generates a time-series plot of daily total sales.*

---

## Frontend (Streamlit)

### 4. Installation & Setup

1. **Create a virtual environment**:
   ```bash
   python3 -m venv streamlit_env
   source streamlit_env/bin/activate  # macOS/Linux
   streamlit_env\Scripts\activate     # Windows
   ```
2. **Install requirements**:
   ```bash
   pip install -r requirements.txt
   ```
3. **Verify Streamlit**:
   ```bash
   streamlit hello
   ```

### 5. Running the App

```bash
streamlit run app.py
```

Visit `http://localhost:8501` in your browser. The app reloads on code changes.

### 6. Building Blocks

In **app.py**, import Streamlit and backend functions:

```python
import streamlit as st
from backend import load_data, get_summary, plot_sales_over_time
```

- **st.title**, **st.write**, **st.table**, **st.dataframe** for text, tables, and data.
- **st.pyplot** to render Matplotlib figures.

### 7. Interactivity & Widgets

Add a sidebar slider to filter by `Rating`:

```python
def main():
    st.sidebar.header('Controls')
    min_rating = st.sidebar.slider('Minimum Rating', 0, 10, 5)
    data = load_data()
    filtered = data[data['Rating'] >= min_rating]

    st.write('### Summary Statistics')
    st.table(get_summary(filtered))
    st.write('### Raw Data')
    st.dataframe(filtered)
    st.write('### Sales Over Time')
    plt = plot_sales_over_time(filtered)
    st.pyplot(plt)

if __name__ == '__main__':
    main()
```

### 8. Layout & Containers

(Optionally) use **st.columns**, **st.expander**, and **st.container** to structure your app.

### 9. Caching

Speed up data loading with caching:

```python
@st.cache
def load_data():
    ...
```

---

## Deployment

### Streamlit Sharing

1. **Create **``:
   ```bash
   pip freeze > requirements.txt
   ```
2. **Push to GitHub**:
   ```bash
   ```

git init && git add . && git commit -m "Initial commit" git remote add origin [https://github.com/yourusername/streamlit-sales.git](https://github.com/yourusername/streamlit-sales.git) git push -u origin main

```
3. **Deploy on Streamlit Sharing**:
- Go to [share.streamlit.io](https://share.streamlit.io)
- Connect your GitHub repo
- Specify `app.py` path and branch
- Click **Deploy**

Your app will be live at the provided URL.

---

## Bonus Activity
**Enhancing Interactivity with Additional Widgets**

In **app_bonus.py**, add:
- **Multiselect** for `Product line` and `City`
- **Radio buttons** for `Gender` and `Customer type`

Ensure all filters update summary, data, and plots dynamically.

---

## Contributing
Feel free to open issues or pull requests. For major changes, please open an issue first to discuss what you’d like to change.

## License
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

```
