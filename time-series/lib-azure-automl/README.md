
## Endpoint

```python=
import pandas
import numpy

def get_data_in_dict(dataframe: pandas.DataFrame, row_idx: int, target_column_name: str):
    '''
    return a record without the target column
    '''
    data_in_dict = {}
    
    columns = list(dataframe.columns)
    columns.remove(target_column_name)
    
    for column in columns:
        value = df.loc[row_idx][column]
        
        if type(value) == np.int64:
            value = int(value)

        data_in_dict[column] = value
    return data_in_dict


# Azure Endpoint
import urllib.request
import json
import os
import ssl

def allowSelfSignedHttps(allowed):
    # bypass the server certificate verification on client side
    if allowed and not os.environ.get('PYTHONHTTPSVERIFY', '') and getattr(ssl, '_create_unverified_context', None):
        ssl._create_default_https_context = ssl._create_unverified_context

allowSelfSignedHttps(True) # this line is needed if you use self-signed certificate in your scoring service.

def predict(url, data_in_dict) -> dict:
    # Request data goes here
    data = {"data": [ data_in_dict ]}
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
        
url = 'http://bba9f75b-e423-4efc-bc06-5d70af8b33f6.southcentralus.azurecontainer.io/score'
data_in_dict = get_data_in_dict(df, 0, '#Passengers')
result = predict(url, data_in_dict)
print(data_in_dict)
print(result)
```

執行結果：
```
fetch the first row: {'Month': '1958-09-01'}
({'forecast': [429.5615711140687], 'prediction_interval': ['[390.47749011474076, 468.6456521133967]'], 'index': [{'Month': -357696000000}]}, 429.5615711140687)
```
