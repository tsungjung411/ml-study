
## Endpoint

```python
import pandas
import numpy
from sklearn.metrics import r2_score, mean_squared_error

# Azure Endpoint
import urllib.request
import json
import os
import ssl

class AzureEndpoint:
    def __init__(self, url: str, 
                 df: pandas.DataFrame, 
                 target_column_name: str,
                 metric_dict: dict={
                     'r2': r2_score, 
                     'rmse': lambda y_true, y_pred: 
                         mean_squared_error(y_true, y_pred, squared=False)
                 }):
        '''
        url: REST endpoint
        df: the dataset
        target_column_name: 
        '''
        self.url = url
        self.df = df
        self.target_column_name = target_column_name
        self.metric_dict = metric_dict
        
        self.allowSelfSignedHttps(True) # this line is needed if you use self-signed certificate in your scoring service.

    def get_input(self, row_idx: int) -> dict:
        '''
        return a record without the target column
        '''
        df = self.df
        target_column_name = self.target_column_name
        
        data_in_dict = {}
        columns = list(df.columns)
        columns.remove(target_column_name)

        for column in columns:
            value = df.loc[row_idx][column]

            if type(value) == numpy.int64:
                value = int(value)

            data_in_dict[column] = value
        return data_in_dict

    def allowSelfSignedHttps(self, allowed):
        # bypass the server certificate verification on client side
        if allowed and not os.environ.get('PYTHONHTTPSVERIFY', '') and getattr(ssl, '_create_unverified_context', None):
            ssl._create_default_https_context = ssl._create_unverified_context

    def predict(self, input_in_dict) -> dict:
        # Request data goes here
        url = self.url
        data = {"data": [ input_in_dict ]}
        body = str.encode(json.dumps(data))

        api_key = '' # Replace this with the API key for the web service
        headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

        req = urllib.request.Request(url, body, headers)

        try:
            response = urllib.request.urlopen(req)

            result = response.read()
            data_in_dict = json.loads(json.loads(result))
            y_pred = data_in_dict['forecast'][0]
            return data_in_dict, y_pred
        except urllib.error.HTTPError as error:
            print("The request failed with status code: " + str(error.code))

            # Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
            print(error.info())
            print(json.loads(error.read().decode("utf8", 'ignore')))
        
    def predict_all(self, debug:bool=False) -> dict:
        df = self.df
        target_column_name = self.target_column_name
        metric_dict = self.metric_dict
        
        y_true_list = df[target_column_name].values
        y_pred_list = []
        
        # predict for each row
        for row_idx in range(len(df)):
            input_in_dict = self.get_input(row_idx)
            _, y_pred = self.predict(input_in_dict)
            y_pred_list.append(y_pred)
            
            if debug:
                print('predict(#{}): {}'.format(row_idx, y_pred))
        
        if debug:
            print()
        
        metric_result = {}
        for name, func in metric_dict.items():
            if debug:
                print('measuring', name, ':')
                print(' - y_true_list:', y_true_list)
                print(' - y_pred_list:', y_pred_list)
                print()
            
            value = func(y_true_list, y_pred_list)
            metric_result[name] = value
        
        return y_pred_list, metric_result
       
```

測試：
```
url = 'http://bba9f75b-e423-4efc-bc06-5d70af8b33f6.southcentralus.azurecontainer.io/score'
endpoint = AzureEndpoint(url, df, '#Passengers')

data_in_dict = endpoint.get_input(0)
print('data_in_dict(#0):', data_in_dict)
print()

result = endpoint.predict(data_in_dict)
print('predict(#0):', result)
print()

result = endpoint.predict_all(debug=True)
print('predict_all:', result)
print()
```

執行結果：
```
data_in_dict(#0): {'Month': '1958-09-01'}

predict(#0): ({'forecast': [429.5615711140687], 'prediction_interval': ['[390.47749011474076, 468.6456521133967]'], 'index': [{'Month': -357696000000}]}, 429.5615711140687)

predict(#0): 429.5615711140687
predict(#1): 423.3771266696243
predict(#2): 419.9671266696243
predict(#3): 422.12490444740206
predict(#4): 421.94490444740205
predict(#5): 420.1549044474021
predict(#6): 424.8893488918465
predict(#7): 423.1382377807354
predict(#8): 424.36490444740207
predict(#9): 432.0115711140687
predict(#10): 438.56268222517986
predict(#11): 439.2804600029576
predict(#12): 429.5615711140687
predict(#13): 423.41157111406875
predict(#14): 420.21046000295763
predict(#15): 422.12490444740206
predict(#16): 422.43379333629093
predict(#17): 419.8693488918465
predict(#18): 424.2582377807354
predict(#19): 423.2293488918465
predict(#20): 424.4871266696243
predict(#21): 431.9493488918465
predict(#22): 438.76490444740205
predict(#23): 439.573793336291
predict(#24): 429.67490444740207
predict(#25): 423.25490444740205
predict(#26): 419.7682377807354
predict(#27): 422.1260155585132

measuring r2 :
 - y_true_list: [404 359 310 337 360 342 406 396 420 472 548 559 463 407 362 405 417 391
 419 461 472 535 622 606 508 461 390 432]
 - y_pred_list: [429.5615711140687, 423.3771266696243, 419.9671266696243, 422.12490444740206, 421.94490444740205, 420.1549044474021, 424.8893488918465, 423.1382377807354, 424.36490444740207, 432.0115711140687, 438.56268222517986, 439.2804600029576, 429.5615711140687, 423.41157111406875, 420.21046000295763, 422.12490444740206, 422.43379333629093, 419.8693488918465, 424.2582377807354, 423.2293488918465, 424.4871266696243, 431.9493488918465, 438.76490444740205, 439.573793336291, 429.67490444740207, 423.25490444740205, 419.7682377807354, 422.1260155585132]

measuring rmse :
 - y_true_list: [404 359 310 337 360 342 406 396 420 472 548 559 463 407 362 405 417 391
 419 461 472 535 622 606 508 461 390 432]
 - y_pred_list: [429.5615711140687, 423.3771266696243, 419.9671266696243, 422.12490444740206, 421.94490444740205, 420.1549044474021, 424.8893488918465, 423.1382377807354, 424.36490444740207, 432.0115711140687, 438.56268222517986, 439.2804600029576, 429.5615711140687, 423.41157111406875, 420.21046000295763, 422.12490444740206, 422.43379333629093, 419.8693488918465, 424.2582377807354, 423.2293488918465, 424.4871266696243, 431.9493488918465, 438.76490444740205, 439.573793336291, 429.67490444740207, 423.25490444740205, 419.7682377807354, 422.1260155585132]

predict_all: ([429.5615711140687, 423.3771266696243, 419.9671266696243, 422.12490444740206, 421.94490444740205, 420.1549044474021, 424.8893488918465, 423.1382377807354, 424.36490444740207, 432.0115711140687, 438.56268222517986, 439.2804600029576, 429.5615711140687, 423.41157111406875, 420.21046000295763, 422.12490444740206, 422.43379333629093, 419.8693488918465, 424.2582377807354, 423.2293488918465, 424.4871266696243, 431.9493488918465, 438.76490444740205, 439.573793336291, 429.67490444740207, 423.25490444740205, 419.7682377807354, 422.1260155585132], {'r2': 0.11299524319753551, 'rmse': 73.96480183186517})
```
