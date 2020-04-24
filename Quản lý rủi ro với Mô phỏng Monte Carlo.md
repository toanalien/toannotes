# Quản lý rủi ro với Mô phỏng Monte Carlo

Một cách phổ biến mô phỏng Monte Carlo được dạy là suy nghĩ về bất kỳ hình thức cơ hội nào, do đó hình ảnh của máy đánh bạc được lấy ví dụ minh hoạ. Tại bất kỳ thời điểm nào, **nhiều sự kiện** có thể xảy ra trong bước tiếp theo dựa trên **một hành động**. Bây giờ, tôi tin vào việc học các ví dụ đơn giản trước tiên để hiểu những cái phức tạp, Vì vậy, hãy nghĩ về việc ném một con xúc xắc sáu mặt ngẫu nhiên. Chúng ta đều biết rằng xúc xắc có 6 cơ hội có thể đánh số từ 1 đến 6. Nhưng để tìm ra bao nhiêu thử nghiệm, chúng ta sẽ phải thảy súc sắc vài lần và ghi lại kết quả. Với mô phỏng Monte Carlo, chúng tôi có thể thực hiện bao nhiêu thử nghiệm mà chúng tôi muốn trong mô phỏng.

Trong một định nghĩa kỹ thuật hơn, Monte Carlo được lấy từ phân phối xác suất để cung cấp một mô hình rủi ro đa biến hoặc trình bày nhiều điều gì xảy ra nếu các sự kiện. Công thức cơ bản cho Monte Carlo đạt được:

```
Today's Event = PriorEvent*e^(Drift+RandomComponent)
```

Về cơ bản, công thức được tạo thành từ 3 thành phần chính. Đầu tiên, là **dữ liệu theo thời gian của tài sản** mà chúng tôi đang xem xét. Trong bài viết của chúng tôi, nó sẽ là tiền điện tử Tezos và mã chứng khoán AMD. Phần tiếp theo là **drift**, đó là **hướng tài sản đã được di chuyển** trong quá khứ. Thành phần thứ ba là **một thành phần ngẫu nhiên** được lấy từ một bản phân phối. Trong trường hợp của chúng tôi, chúng tôi sẽ sử dụng phân phối bình thường để mô phỏng biến động tài sản. Biến động thường được xem là chuyển động điên rồ mà một cổ phiếu thực hiện trên cơ sở thông thường là loại có vẻ ngẫu nhiên.

## Nhà đầu tư / Thương nhân áp dụng Monte Carlo như thế nào

Như đã nêu trước đó, Monte Carlo là một cách tốt để vạch ra một vấn đề với nhiều kết quả có thể xảy ra. **Trong tài chính và đặc biệt là thị trường tài chính, một tài sản có thể đi đến nhiều mức giá khác nhau trong tương lai.** Bên cạnh việc định giá tài sản, mô phỏng Monte Carlo có thể được áp dụng trong các dự án chi tiết tài chính như dòng tiền.

## Monte Carlo trên một loại tiền điện tử - Tezos

![](https://miro.medium.com/max/1400/0*64CHIgab8kgIFtXi)

Trên ví dụ mô phỏng Tezos Monte Carlo, dữ liệu hàng ngày được lấy từ Investing.com https://www.investing.com/crypto/tezos/xtz-usd-historical-data?cid=1150186

```python
# Import Libraries
import numpy as np  
import pandas as pd  
import pandas_datareader as wb  
import matplotlib.pyplot as plt  
from scipy.stats import norm
%matplotlib inline

# Settings for Monte Carlo asset data, how long, and how many forecasts 
ticker = 'XTZ_USD' # ticker
t_intervals = 30 # time steps forecasted into future
iterations = 25 # amount of simulations

# Acquiring data
data = pd.read_csv('XTZ_USD Huobi Historical Data.csv',index_col=0,usecols=['Date', 'Price'])
data = data.rename(columns={"Price": ticker})

# Preparing log returns from data
log_returns = np.log(1 + data.pct_change())
# Plot of asset historical closing price
data.plot(figsize=(10, 6));
```

![](https://miro.medium.com/max/1210/0*w_XX_MmpZZkYwZlT)

Giá đóng cửa của Tezos sang USD từ tháng 8 năm 2019 đến tháng 1 năm 2020.

```python
# Plot of log returns
log_returns.plot(figsize = (10, 6))
```

![](https://miro.medium.com/max/1220/0*8ssWn4jtZvM-CWUj)

Log normal returns of Tezos to USD.

```python
# Setting up drift and random component in relation to asset data
u = log_returns.mean()
var = log_returns.var()
drift = u - (0.5 * var)
stdev = log_returns.std()
daily_returns = np.exp(drift.values + stdev.values * norm.ppf(np.random.rand(t_intervals, iterations)))

# Takes last data point as startpoint point for simulation
S0 = data.iloc[-1]
price_list = np.zeros_like(daily_returns)
price_list[0] = S0

# Applies Monte Carlo simulation in asset
for t in range(1, t_intervals):
    price_list[t] = price_list[t - 1] * daily_returns[t]

# Plot simulations
plt.figure(figsize=(10,6))
plt.plot(price_list);
```

![](https://miro.medium.com/max/1202/0*rNqlc2IpoRJ0uUZj)

25 mô phỏng Monte Carlo của Tezos nhận lại tới 30 ngày.

Một điều cần lưu ý là sự biến động lớn trong các mô phỏng này. Từ giá hôm nay 1,50, Tezos có thể có khả năng dao động từ 1,00 đến 2,75! Từ số lượng mô phỏng nhỏ này, tôi có thể thấy lý do tại sao một số người thích có cơ hội với Bitcoin và các loại tiền điện tử tương tự do tiềm năng tăng giá hơi được ưa chuộng.

## Monte Carlo trên AMD

Dataset: https://medium.com/gradient-growth/options-100k-challenge-6-months-to-6-figures-part-2-2cbc03a48864

Advanced Micro Devices Monte Carlo simulation

```python
# Import Libraries
import numpy as np  
import pandas as pd  
import pandas_datareader as wb  
import matplotlib.pyplot as plt  
from scipy.stats import norm
%matplotlib inline

# Settings for Monte Carlo asset data, how long, and how many forecasts 
ticker = 'AMD' # stock ticker
t_intervals = 30 # time steps forecasted into future
iterations = 25 # amount of simulations

# Acquiring data
data = pd.DataFrame()
data[ticker] = wb.DataReader(ticker, data_source='yahoo', start='2018-1-1')['Adj Close']

# Preparing log returns from data
log_returns = np.log(1 + data.pct_change())
# Plot of asset historical closing price
data.plot(figsize=(10, 6));
```

![](https://miro.medium.com/max/1184/0*ezsABGWh8ItKb_P2)

Closing prices of AMD from 2018 to 2020.

```python
# Plot of log returns
log_returns.plot(figsize = (10, 6))
```

![](https://miro.medium.com/max/1220/0*dJxhbo4SY-AJC_xt)

Log normal returns of AMD form 2018 to 2020.

Lợi nhuận của bản ghi trông khá tốt từ giá đóng cửa ở trên. Bây giờ để thiết lập drift và các thành phần ngẫu nhiên.

```python
# Setting up drift and random component in relatoin to asset data
u = log_returns.mean()
var = log_returns.var()
drift = u - (0.5 * var)
stdev = log_returns.std()
daily_returns = np.exp(drift.values + stdev.values * norm.ppf(np.random.rand(t_intervals, iterations)))

# Takes last data point as startpoint point for simulation
S0 = data.iloc[-1]
price_list = np.zeros_like(daily_returns)
price_list[0] = S0
# Applies Monte Carlo simulation in asset
for t in range(1, t_intervals):
    price_list[t] = price_list[t - 1] * daily_returns[t]

# Plot simulations
plt.figure(figsize=(10,6))
plt.plot(price_list);
```

![](https://miro.medium.com/max/1184/0*m_AvTbXb3T1LQgOF)

25 Monte Carlo simulations of AMD for the next 30 days

Có vẻ như xu hướng chung của AMD từ các mô phỏng Monte Carlo đang lên, đó là một dấu hiệu tốt do các vị trí tăng của tôi tại thời điểm thử thách!

## Kết luận

Mô phỏng Monte Carlo cho phép các nhà đầu tư và thương nhân chuyển đổi khả năng đầu tư thành quyết định. Ưu điểm của Monte Carlo là khả năng của nó trong việc xác định phạm vi các phẩm chất cho các sự kiện khác nhau có thể. Điều này cũng tương tự như nhược điểm đáng chú ý nhất của nó là đôi khi không đánh giá chính xác các sự kiện cực đoan. Ví dụ, nhiều mô phỏng Monte Carlo đã thất bại trong cuộc khủng hoảng gấu lớn. Vì vậy, mô hình này - như những người khác bị hạn chế bởi dữ liệu và cài đặt được áp dụng. Một giải pháp khả thi cho điểm yếu này là phân phối có lẽ không bình thường cho thành phần ngẫu nhiên được mô phỏng.

ref: https://towardsdatascience.com/python-risk-management-monte-carlo-simulations-7d41c891cb5

