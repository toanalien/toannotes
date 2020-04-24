# Quản lý rủi ro với Kelly Criterion

## 1. Dataset

https://www.investing.com/etfs/spdr-s-p-500-historical-data

## 2. Công thức

```
K% = W - (1-W)/R

với:
- K% là % Kelly
- W là xác suất thắng
- R tỉ lệ thắng/thua
```

## 3. Áp dụng trong đầu tư

Từ một tìm kiếm nhanh trên Google, bạn sẽ tìm thấy rất nhiều người trích dẫn trích dẫn từ các nhà đầu tư giá trị áp dụng KC, chẳng hạn như:

- Tôi không thể tham gia vào 50 hoặc 75 thứ. Đó là cách đầu tư của Noah’s Ark - bạn kết thúc với một sở thú theo cách đó. Tôi thích đặt số tiền có ý nghĩa vào một vài điều. - Warren Buffett
- Những người khôn ngoan đặt cược rất nhiều khi thế giới cho họ cơ hội đó. Họ đặt cược lớn khi họ có tỷ lệ cược. Và thời gian còn lại, họ không. Nó chỉ đơn giản như vậy. - Charlie Munger

## 4. Ví dụ về gian lận đồng xu

Khi bạn đặt cược với một đồng xu lật theo ý bạn được 55% (một đồng xu công bằng sẽ có cơ hội thắng 50%). Ví dụ của chúng tôi, hãy giả sử bạn giả định bạn thắng $ 1 hoặc mất $ 1 cho kết quả đầu hoặc đuôi có liên quan đến mức độ rủi ro. Nói cách khác, chúng tôi đang giả định tỷ lệ hoàn trả 1 đến 1 so với KC hoặc số tiền rủi ro cho mỗi lần đặt cược.

```python
# Import libraries
import math
import time
import numpy as np
import pandas as pd
import datetime as dt
import cufflinks as cf
from pylab import plt

np.random.seed(1) # For reproducibility
plt.style.use('seaborn') # I think this looks pretty

%matplotlib inline # To get out plots

# Coin flip variable set up
p = 0.55 # Fixes the probability for heads.
f = p - (1-p) # Calculates the optimal fraction according to the Kelly criterion.
f

# f = 0.10
```

Trên đây là kích thước đặt cược Kelly Criterion tối ưu (f). Điều này có nghĩa là để hoàn trả 1 đến 1 và cơ hội được ưu tiên 55% để giành chiến thắng, chúng ta nên mạo hiểm 10% tổng số vốn của mình để tối đa hóa lợi nhuận.

```python
# Chuẩn bị mô phỏng của chúng tôi về các đồng xu lật với các biến
I = 50 # Số lượng loạt được mô phỏng.
n = 100 # Số lượng thử nghiệm trên mỗi loạt.

def run_simulation(f):
    c = np.zeros((n, I)) # Khởi tạo một đối tượng ndarray để lưu trữ kết quả mô phỏng.
    c[0] = 100 # Khởi tạo vốn khởi đầu với 100.
    for i in range(I): # Vòng lặp bên ngoài cho các mô phỏng loạt.
        for t in range(1,n): # Vòng lặp bên trong cho chính chuỗi.
            o = np.random.binomial(1, p) # Mô phỏng việc tung đồng xu.
            if o > 0: # If 1, i.e., heads …
                c[t, i] = (1+f) * c[t-1,i] #… thêm phần thắng vào quỹ.
            else: # If 0, i.e., tails …
                c[t, i] = (1-f) * c[t-1,i] #… trừ phần thua vào quỹ.
    return c

c_1 = run_simulation(f) # Chạy mô phỏng
c_1.round(2) # Xem mô phỏng
```

Biểu diễn lên biểu đồ

```python
plt.figure(figsize=(10,6))
plt.plot(c_1, 'b', lw=0.5) # Plots all 50 series.
plt.plot(c_1.mean(axis=1), 'r', lw=2.5); # Plots the average over all 50 series.
plt.title('50 Simulations of Rigged Coin Flips');
plt.xlabel('Number of trials');
plt.ylabel('$ Amount Won/Lost');
```

![](https://miro.medium.com/max/1230/0*qApwLlwE0FLbW-IO)

Bên cạnh việc đặt cược với 10% vốn của chúng tôi mọi lúc, điều gì sẽ xảy ra với các kích thước đặt cược khác / các giá trị KC khác nhau?

```python
c_2 = run_simulation(0.05) #Simulation with f = 0.05.
c_3 = run_simulation(0.25) #Simulation with f = 0.25.
c_4 = run_simulation(0.5) #Simulation with f = 0.5.
plt.figure(figsize=(10, 6))
plt.plot(c_1.mean(axis=1), 'r', label='$f^*=0.1$')
plt.plot(c_2.mean(axis=1), 'b', label='$f=0.05$')
plt.plot(c_3.mean(axis=1), 'y', label='$f=0.25$')
plt.plot(c_4.mean(axis=1), 'm', label='$f=0.5$')
plt.legend(loc=0);
plt.title('Varied KC Simulations of Rigged Coin Flips');
plt.xlabel('Number of trials');
plt.ylabel('$ Amount Won/Lost');
```

![](https://miro.medium.com/max/1230/0*qApwLlwE0FLbW-IO)

## 5. Kelly to SPY

Bây giờ chúng ta hãy ra khỏi ví dụ đơn giản và đẩy nó vào thế giới thực. Chúng tôi sẽ áp dụng KC cho chỉ số chứng khoán S & P 500. Để áp dụng những thay đổi này, công thức KC của chúng tôi sẽ như thế này [5]:

```
f* = (µ-r)/σ²

với:
- Mu ( µ) = lợi nhuận trung bình của SPY
- r = tỉ phi rủi ro
- sigma² (σ²) = phương sai SPY
```

```python
# Loading SPY data
data = pd.read_csv('SPY Historical Data.csv', index_col=0, parse_dates=True)
# Light Feature Engineering on Returns
data['Change %'] = data['Change %'].map(lambda x: x.rstrip('%')).astype(float) / 100
data.dropna(inplace=True)
data.tail()
```

Chỉ cần kiểm tra dữ liệu để xem kỹ thuật tính năng của chúng tôi có hoạt động không. Dữ liệu từ Investing.com thường có ký hiệu% mà Python có xu hướng chọn làm đối tượng chứ không phải số.

```python
mu = data['Change %'].mean() * 252 # Calculates the annualized return.
sigma = (data['Change %'].std() * 252 ** 0.5)  # Calculates the annualized volatility.
r = 0.0179 #1 year treasury rate
f = (mu - r) / sigma ** 2 # Calculates the optimal Kelly fraction to be invested in the strategy.
f

# f = 4.2
```

Từ tính toán của chúng tôi, giá trị KC là 4.2, nghĩa là chúng ta nên đặt cược 4.2x cho mỗi $ 1 được đầu tư vào SPY. Đối với những người bạn thoải mái hơn với tài chính, điều đó có nghĩa là chúng ta nên tận dụng giao dịch của mình thêm 4.2 lần để tối đa hóa lợi nhuận kỳ vọng. Theo trực giác, nghe có vẻ cao đối với tôi, nhưng hãy để Lôi chạy một vài mô phỏng như trước đây.

```python
equs = [] # preallocating space for our simulations
def kelly_strategy(f):
    global equs
    equ = 'equity_{:.2f}'.format(f)
    equs.append(equ)
    cap = 'capital_{:.2f}'.format(f)
    data[equ] = 1  #Generates a new column for equity and sets the initial value to 1.
    data[cap] = data[equ] * f  #Generates a new column for capital and sets the initial value to 1·f∗.
    for i, t in enumerate(data.index[1:]):
        t_1 = data.index[i]  #Picks the right DatetimeIndex value for the previous values.
        data.loc[t, cap] = data[cap].loc[t_1] * math.exp(data['Change %'].loc[t])
        data.loc[t, equ] = data[cap].loc[t] - data[cap].loc[t_1] + data[equ].loc[t_1]
        data.loc[t, cap] = data[equ].loc[t] * f 

kelly_strategy(f * 0.5) # Values for 1/2 KC
kelly_strategy(f * 0.66) # Values for 2/3 KC
kelly_strategy(f) # Values for optimal KC
ax = data['Change %'].cumsum().apply(np.exp).plot(legend=True,figsize=(10, 6))         
data[equs].plot(ax=ax, legend=True);
plt.title('Varied KC Values on SPY, Starting from $1');
plt.xlabel('Years');
plt.ylabel('$ Return')
```

![](https://miro.medium.com/max/1208/0*eAR4eeGrOq6GFJoK)

Giống như trong ví dụ lật đồng xu của chúng tôi, các giá trị cao hơn cho KC dẫn đến sự biến động lớn hơn. Số tiền tối ưu là 4.2 đã mất hơn 50% giá trị vào khoảng đầu năm 2019. Điều đó khá đáng sợ đối với hầu hết mọi người, đó là lý do tại sao các học viên áp dụng KC thường áp dụng một nửa KC (vốn_2.10 từ lô ngoài). Phần kết luận

## 6. Kết luận

Chúng tôi đã đi qua một lịch sử ngắn gọn về Tiêu chí Kelly (KC) với cách các nhà đầu tư áp dụng công thức này. Sau đó, chúng tôi tối đa hóa lợi nhuận của mình với một ví dụ lật đồng xu đơn giản. Cuối cùng, chúng tôi đã áp dụng những gì chúng tôi đã học được từ ví dụ lật đồng xu vào chỉ số chứng khoán S & P 500. Từ mô phỏng nhỏ của chúng tôi, chúng tôi đã học được rằng, mặc dù KC có thể đề xuất giá trị cao, đôi khi chúng tôi sẽ lấy giá trị KC giảm để tránh biến động không mong muốn. Không có nhiều người có thể chịu đựng được 50% số tiền rút ra (số tiền thua lỗ lớn nhất) và don sắt khiến tôi bắt đầu căng thẳng về tinh thần khi trải qua một lần! Vì vậy, hãy đưa ra giải pháp chiến lược, hãy yên tâm hơn bằng cách hạ thấp hơn KC đầy đủ. Tuyên bố miễn trừ trách nhiệm: Tất cả những điều được nêu trong bài viết này là của riêng tôi chứ không phải của bất kỳ nhà tuyển dụng nào. Đầu tư mang rủi ro nghiêm trọng và tham khảo ý kiến ​​cố vấn đầu tư của bạn trước khi thực hiện bất kỳ hành động đầu tư nào.

ref: https://towardsdatascience.com/python-risk-management-kelly-criterion-526e8fb6d6fd
