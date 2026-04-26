# Data Preparation

Set up logging information


```python
#set up logging
import logging

logging.basicConfig(
    level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s',
    filename='data.log'
)
logger = logging.getLogger(__name__)
```

If you are running the following, please save your password to MongoDB as an environment variable in powershell under the name "MONGO_PASSWORD". Do NOT commit your password to anywhere public. Alternatively, skip this first step and just set the password variable to your password


```python
#obtain the password from your environment
try:
    import os
    #get password
    password = os.environ.get("MONGO_PASSWORD")
    logger.info("Got Password!")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```


```python
try:
    import subprocess
    import os
    #create a function that will utilize mongosh and connect to the Mongo Database. No pymongo. 
    def mongosh(query):
        #connect
        password = os.environ.get("MONGO_PASSWORD")
        uri = f"mongodb+srv://rveermo_db_user:{password}@pollution.q8lipjf.mongodb.net/airquality"
        command = f"mongosh '{uri}' --eval '{query}'"
        #run process
        result = subprocess.run(
            ['wsl', 'bash', '-c', command],
            capture_output=True,
            text=True
        )
        #print out the result
        print(result.stdout)
        print(result.stderr)
        logger.info("Query was successfully run")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```

The following code turns the flat csv into a list of json. 


```python
try:
    import pandas as pd
    #read in the flat csv
    df = pd.read_csv("../updated_pollution_dataset.csv")
    #turn into a list of json files
    df.to_json("pollution.json", orient="records")
    logger.info("Flat data was read in and converted properly")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```

The following uploads the data to MongoDB


```python
try:
    #process to upload data
    #data is taken as a json, and then inputted into MongoDB
    result = subprocess.run(
        ['wsl', 'mongoimport',
        f'--uri=mongodb+srv://rveermo_db_user:{password}@pollution.q8lipjf.mongodb.net/airquality',
        '--collection=pollution',
        '--file=pollution.json',
        '--jsonArray'],
        capture_output=True,
        text=True
    )
    print(result.stdout)
    print(result.stderr)
    logger.info("Data was inputted into MongoDB succesfully")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```

    
    2026-04-26T12:35:36.821-0400	connected to: mongodb+srv://[**REDACTED**]@pollution.q8lipjf.mongodb.net/airquality
    2026-04-26T12:35:39.822-0400	[##############..........] airquality.pollution	525KB/864KB (60.8%)
    2026-04-26T12:35:42.822-0400	[###################.....] airquality.pollution	699KB/864KB (80.9%)
    2026-04-26T12:35:45.822-0400	[########################] airquality.pollution	864KB/864KB (100.0%)
    2026-04-26T12:35:47.659-0400	[########################] airquality.pollution	864KB/864KB (100.0%)
    2026-04-26T12:35:47.661-0400	5000 document(s) imported successfully. 0 document(s) failed to import.
    
    

Functionality test



```python
try:
    #quick sanity check to ensure that all documents are present
    mongosh("db.pollution.countDocuments()")
    logger.info("Sanity has been checked")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```

    5000
    
    
    
