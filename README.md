# Gamestop-stock-vs-Tesla

# Process
### import library
```
!pip install yfinance
!pip install bs4
!pip install nbformat
!pip install --upgrade plotly
```
```
import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
import plotly.graph_objects as go
from plotly.subplots import make_subplots
```
```
import plotly.io as pio
pio.renderers.default = "iframe"
```
```
import warnings
# Ignore all warnings
warnings.filterwarnings("ignore", category=FutureWarning)
```
### Define Graphing Function
```
def make_graph(stock_data, revenue_data, stock):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price", "Historical Revenue"), vertical_spacing = .3)
    stock_data_specific = stock_data[stock_data.Date <= '2021-06-14']
    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']
    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date, infer_datetime_format=True), y=stock_data_specific.Close.astype("float"), name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date, infer_datetime_format=True), y=revenue_data_specific.Revenue.astype("float"), name="Revenue"), row=2, col=1)
    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    fig.update_layout(showlegend=False,
    height=900,
    title=stock,
    xaxis_rangeslider_visible=True)
    fig.show()
    from IPython.display import display, HTML
    fig_html = fig.to_html()
    display(HTML(fig_html))
```
### extract Stock Data of Tesla by yfinance
```
Tesla = yf.Ticker('TSLA')
```
 Using the ticker object and the function history extract stock information and save it in a dataframe named tesla_data. Set the period parameter to "max" so we get information for the maximum amount of time.
```
tesla_data = Tesla.history(period='max')
```
![image](https://github.com/user-attachments/assets/52d289ff-9834-4a1d-bb96-dffb0140351f)


Reset the index using the reset_index(inplace=True) function on the tesla_data DataFrame and display the first five rows of the tesla_data dataframe using the head function. 
```
tesla_data.reset_index(inplace=True)
tesla_data.head()
```
<img width="545" alt="Screenshot 2025-03-03 093430" src="https://github.com/user-attachments/assets/93d8d411-d7c9-47d3-9eb6-95e6a9ab4fc8" />

### Use Webscraping to Extract Tesla Revenue Data
Use the requests library to download the webpage https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm Save the text of the response as a variable named html_data.
```
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm"
html_data = requests.get(url).text
```
Parse the html data using beautiful_soup using parser 
```
soup = BeautifulSoup(html_data, 'html.parser')
soup.find_all('table')
```
extract the table with Tesla Revenue and store it into a dataframe named tesla_revenue
```
tesla_revenue = pd.DataFrame(columns=['Date', 'Revenue'])
data_list =[]
for row in soup.find('tbody').find_all('tr'):
    col = row.find_all('td')
    date=col[0].text
    revenue =col[1].text
    data_list.append([date, revenue])
tesla_revenue = pd.DataFrame(data_list, columns=['Date', 'Revenue'])
tesla_revenue.head()
```
![image](https://github.com/user-attachments/assets/20f4d425-a0e9-452b-83c2-b076865d7cc9)

remove the comma and dollar sign from the Revenue column
```
tesla_revenue["Revenue"] = tesla_revenue['Revenue'].str.replace(r',|\$',"", regex=True)
```
remove an null or empty strings in the Revenue column.
```
tesla_revenue.dropna(inplace=True)

tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != ""]
```
display the last 5 row of the tesla_revenue dataframe using the tail function
```
tesla_revenue.tail()
```
<img width="74" alt="image" src="https://github.com/user-attachments/assets/315b4efc-2afa-4993-a678-2bcd685f808d" />

### extract Stock Data of Tesla by yfinance
```
Gamestop = yf.Ticker('GME')
gamestop_data = Gamestop.history(period = 'max')
```
![image](https://github.com/user-attachments/assets/8d799a42-1e9a-4fa5-808f-597ec876ae03)

Reset the index using the reset_index(inplace=True) function on the gme_data DataFrame and display the first five rows of the gme_data dataframe using the head function.
```
gamestop_data.reset_index(inplace=True)
gamestop_data.head()
```
![image](https://github.com/user-attachments/assets/576c1319-638a-4cb1-9d5c-a2ac7a5e633a)

### extract GME Revenue Data
download the webpage
```
URL = '  https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html'
html_data = requests.get(URL).text
```
```
soup = BeautifulSoup(html_data, 'html.parser')
soup.find_all('table')
```
extract the table with GameStop Revenue and store it into a dataframe named gme_revenue
```
gme_revenue = pd.DataFrame(columns = ['Date', 'Revenue'])
data_list = []
for row in soup.find('tbody').find_all('tr'):
    col = row.find_all('td')
    date = col[0].text
    revenue = col[1].text
    data_list.append([date, revenue])
gme_revenue = pd.DataFrame(data_list, columns = ['Date', 'Revenue'])
gme_revenue.head()
```
![image](https://github.com/user-attachments/assets/fabd0442-ee79-41d6-b76f-c99065ecc462)

remove the comma and dollar sign from the Revenue column
```
gme_revenue["Revenue"] = gme_revenue['Revenue'].str.replace(r',|\$',"", regex=True)
```
remove an null or empty strings in the Revenue column.
```
gme_revenue.dropna(inplace=True)

gme_revenue = gme_revenue[gme_revenue['Revenue'] != ""]
```
display the last 5 row of the tesla_revenue dataframe using the tail function
```
gme_revenue.tail()
```
![image](https://github.com/user-attachments/assets/e8ba25a7-2966-4f4f-9354-784e050afecc)

### Plot Tesla Stock Graph

```
import matplotlib.pyplot as plt
# Ensure Date columns are in datetime format
tesla_data['Date'] = pd.to_datetime(tesla_data['Date']).dt.tz_localize(None)
tesla_revenue['Date'] = pd.to_datetime(tesla_revenue['Date'], errors='coerce')
tesla_revenue.dropna(subset=['Date'], inplace=True)

def make_graph(tesla_data, tesla_revenue, title):
    # Filter data up to June 30, 2021
    tesla_stock_data = tesla_data[tesla_data['Date'] <= '2021-06-30']
    tesla_revenue_data = tesla_revenue[tesla_revenue['Date'] <= '2021-06-30']
    if tesla_stock_data.empty or tesla_revenue_data.empty:
        print("Không có dữ liệu để vẽ đồ thị.")
        return
        
    # Create figure
    plt.figure(figsize=(10, 6))
    
    # Plot stock price
    plt.subplot(2, 1, 1)
    plt.plot(tesla_stock_data['Date'], tesla_stock_data['Open'], label='Open price', color='blue')
    plt.title(f'{title} - Stock Price and Revenue')
    plt.ylabel('Stock Price (USD)')
    plt.legend()
    plt.grid(False)

    # Plot revenue
    plt.subplot(2, 1, 2)
    plt.bar(tesla_revenue_data['Date'].dt.year, tesla_revenue_data['Revenue'], label='Revenue', color='green')
    plt.xlabel('Year')
    plt.ylabel('Revenue (Million USD)')
    plt.legend()
    plt.grid(False)

    # Adjust layout and show plot
    plt.tight_layout()
    plt.show()

make_graph(tesla_data, tesla_revenue, 'Tesla')
```
![image](https://github.com/user-attachments/assets/158d0297-c99a-4045-9a57-0cbbbfcc914a)


### Plot Gamestop Stock Graph

```
import matplotlib.pyplot as plt
# Ensure Date columns are in datetime format
gamestop_data['Date'] = pd.to_datetime(gamestop_data['Date']).dt.tz_localize(None)
gme_revenue['Date'] = pd.to_datetime(gme_revenue['Date'], errors='coerce')
gme_revenue.dropna(subset=['Date'], inplace=True)

def make_graph(gamestop_data, gme_revenue, title):
    # Filter data up to June 30, 2021
    gme_stock_data = gamestop_data[gamestop_data['Date'] <= '2021-06-30']
    gme_revenue_data = gme_revenue[gme_revenue['Date'] <= '2021-06-30']
    if gme_stock_data.empty or gme_revenue_data.empty:
        print("Không có dữ liệu để vẽ đồ thị.")
        return
        
    # Create figure
    plt.figure(figsize=(10, 6))
    
    # Plot stock price
    plt.subplot(2, 1, 1)
    plt.plot(gme_stock_data['Date'], gme_stock_data['Open'], label='Open price', color='blue')
    plt.title(f'{title} - Stock Price and Revenue')
    plt.ylabel('Stock Price (USD)')
    plt.legend()
    plt.grid(False)

    # Plot revenue
    plt.subplot(2, 1, 2)
    plt.bar(gme_revenue_data['Date'].dt.year, gme_revenue_data['Revenue'], label='Revenue', color='green')
    plt.xlabel('Year')
    plt.ylabel('Revenue (Million USD)')
    plt.legend()
    plt.grid(False)

    # Adjust layout and show plot
    plt.tight_layout()
    plt.show()

make_graph(gamestop_data, gme_revenue, 'GameStop')
```
![image](https://github.com/user-attachments/assets/8adadd8c-586b-4525-b024-70016f3c0b5b)



