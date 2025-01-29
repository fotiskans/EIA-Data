import streamlit as st
import requests
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime

# Streamlit App Title
st.title("EIA API Oil Data Dashboard")

# API Key and URL
api_key = "LaXrvxB1z6yeYWeFfuhS4fnZmyxus3yjXKY1Baz3"
url = f"https://api.eia.gov/v2/petroleum/move/wkly/data/?frequency=weekly&data[0]=value&facets[product][]=EPC0&facets[process][]=IM0&start=2019-01-01&sort[0][column]=period&sort[0][direction]=desc&offset=0&length=5000&api_key={api_key}"

# Fetch Data
@st.cache_data
def get_data():
    response = requests.get(url)
    if response.status_code == 200:
        data = response.json()["response"]["data"]
        return pd.DataFrame(data)
    else:
        st.error(f"Error fetching data: {response.status_code}")
        return pd.DataFrame()

# Load data
df = get_data()

# Filter and clean data
if not df.empty:
    df_exports = df.loc[:, ["period", "value"]]
    df_exports["period"] = pd.to_datetime(df_exports["period"])  # Convert to datetime
    df_exports = df_exports.sort_values(by="period")

    # Convert timestamps to Python datetime.date for Streamlit widgets
    min_date = df_exports["period"].min().date()
    max_date = df_exports["period"].max().date()
    
    selected_date_range = st.slider("Select Date Range:", 
                                    min_value=min_date, 
                                    max_value=max_date, 
                                    value=(min_date, max_date))
    
    df_filtered = df_exports[(df_exports["period"] >= pd.Timestamp(selected_date_range[0])) & 
                             (df_exports["period"] <= pd.Timestamp(selected_date_range[1]))]

    # Plot Data
    fig, ax = plt.subplots()
    ax.plot(df_filtered["period"], df_filtered["value"], marker="o", linestyle="-")
    ax.set_title("Weekly Oil Exports")
    ax.set_xlabel("Date")
    ax.set_ylabel("Exports (barrels)")
    ax.grid(True)

    # Display Plot
    st.pyplot(fig)
else:
    st.warning("No data available.")

# Run with: `streamlit run your_script.py`
