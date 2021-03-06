## [[UCI] Air Quality Data Set](https://archive.ics.uci.edu/ml/datasets/Air+Quality)
> 資料來源：[A Multivariate Time Series Guide to Forecasting and Modeling (with Python codes)](https://www.analyticsvidhya.com/blog/2018/09/multivariate-time-series-guide-forecasting-modeling-python-codes/)

<br>

### DataFrame.head(10)
![](https://i.imgur.com/VFIcVey.png)
| Date      | Time     | CO(GT) | PT08.S1(CO) | NMHC(GT) | C6H6(GT) | PT08.S2(NMHC) | NOx(GT) | PT08.S3(NOx) | NO2(GT) | PT08.S4(NO2) | PT08.S5(O3) | T    | RH   | AH     |
|-----------|----------|--------|-------------|----------|----------|---------------|---------|--------------|---------|--------------|-------------|------|------|--------|
| 3/10/2004 | 18:00:00 | 2.6    | 1360        | 150      | 11.9     | 1046          | 166     | 1056         | 113     | 1692         | 1268        | 13.6 | 48.9 | 0.7578 |
| 3/10/2004 | 19:00:00 | 2      | 1292        | 112      | 9.4      | 955           | 103     | 1174         | 92      | 1559         | 972         | 13.3 | 47.7 | 0.7255 |
| 3/10/2004 | 20:00:00 | 2.2    | 1402        | 88       | 9.0      | 939           | 131     | 1140         | 114     | 1555         | 1074        | 11.9 | 54.0 | 0.7502 |
| 3/10/2004 | 21:00:00 | 2.2    | 1376        | 80       | 9.2      | 948           | 172     | 1092         | 122     | 1584         | 1203        | 11.0 | 60.0 | 0.7867 |
| 3/10/2004 | 22:00:00 | 1.6    | 1272        | 51       | 6.5      | 836           | 131     | 1205         | 116     | 1490         | 1110        | 11.2 | 59.6 | 0.7888 |
| 3/10/2004 | 23:00:00 | 1.2    | 1197        | 38       | 4.7      | 750           | 89      | 1337         | 96      | 1393         | 949         | 11.2 | 59.2 | 0.7848 |
| 3/11/2004 | 0:00:00  | 1.2    | 1185        | 31       | 3.6      | 690           | 62      | 1462         | 77      | 1333         | 733         | 11.3 | 56.8 | 0.7603 |
| 3/11/2004 | 1:00:00  | 1      | 1136        | 31       | 3.3      | 672           | 62      | 1453         | 76      | 1333         | 730         | 10.7 | 60.0 | 0.7702 |
| 3/11/2004 | 2:00:00  | 0.9    | 1094        | 24       | 2.3      | 609           | 45      | 1579         | 60      | 1276         | 620         | 10.7 | 59.7 | 0.7648 |

9357 rows × 14 columns

<br>

### [A Multivariate Time Series Guide to Forecasting and Modeling (with Python codes)](https://www.analyticsvidhya.com/blog/2018/09/multivariate-time-series-guide-forecasting-modeling-python-codes/)
Metrics:
| Columns       | rmse       |
| ------------- | ----------:|
| CO(GT)        |   1.420 |
| PT08.S1(CO)   | 303.391 |
| NMHC(GT)      | 204.066 |
| C6H6(GT)      |  28.153 |
| PT08.S2(NMHC) |   6.538 |
| NOx(GT)       | 265.049 |
| PT08.S3(NOx)  | 250.767 |
| NO2(GT)       | 238.926 |
| PT08.S4(NO2)  | 247.506 |
| PT08.S5(O3)   | 392.313 |
| T             | 383.134 |
| RH            | 506.588 |
| AH            |   8.140 |

